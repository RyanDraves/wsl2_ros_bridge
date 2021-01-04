# WSL2 ROS Bridge
A quick-and-dirty package that allows for one to connect two rosbridge servers together, essentially merging two separate roscores for whatever topics you configure. This is essentially a naughty version of [actual ROS networking](http://wiki.ros.org/ROS/Tutorials/MultipleMachines), but this option allows for WSL2 (and Docker w/ the WSL2 backend) to get involved without needing to [forward 65000 ports](http://wiki.ros.org/ROS/NetworkSetup).

## Dependencies
Both the WSL2 instance and the remote machine should have:
- A ROS installation
- `sudo apt-get install -y ros-[version]-rosbridge-server`

WSL2 specifically needs:
- `python3 -m pip install roslibpy`

I used ROS Noetic so python3 is listed, but you shouldn't have any troubles switching to Python2 or another version of ROS1.

## Usage
First, create a CSV file to configure the topics you'd like to send. The example `connections.csv` serves as a template:
```
Topic,Msg Type,Direction
/chatter,std_msgs/String,b-to-a
```

Please include the header row as well. The options for "Direction" are either `a-to-b` or `b-to-a`. `a-to-b` means "host to remote", i.e. "WSL2 to remote machine"; vice versa for `b-to-a`.

From here you can run on the remote machine:
- `roslaunch rosbridge_server rosbridge_websocket.launch port:=[some_port]`

And afterwards on the WSL2:
- `roslaunch wsl2_ros_bridge connect.launch remote_host:=[some_address] remote_port:=[some_port] local_port:=[some_port] connections_file:=[path]`

The port arguments are optional and will default to `9090` if not specified.

## Known Issues
If anything has been configured incorrectly, or perhaps the remote rosbridge_websocket is not running, then a _lot_ of errors will print out. `roslibpy` is pretty spammy.

## Known Limitations
Have not added support for sending TFs yet. Feel free to open a PR.
