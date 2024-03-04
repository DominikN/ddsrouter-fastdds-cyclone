# ddsrouter-fastdds-cyclone

Connecting Cyclone DDS and Fast DDS powered ROS 2 nodes over DDS Router:

## Quick Start

```
docker compose up
```

## Notes

### FastDDS <> FastDDS

This setup works fine:

```yaml
services:
  
  talker:
    build: .
    environment:
      - ROS_DOMAIN_ID=1
    command: ros2 run demo_nodes_cpp talker

  ros2router:
    image: husarnet/ros2router:1.9.0 # we have different a versioning and we're using DDS Router v2.1.0 inisde this docker image
    volumes:
      - ./config.yaml:/config.yaml
    environment:
      - PARTICIPANTS= # by keeping this env empty, we're bypassing auto-configuring the DDS Router, and we're using the config.yaml file
    command: ddsrouter -c /config.yaml

  listener:
    build: .
    environment:
      - ROS_DOMAIN_ID=2
    command: ros2 run demo_nodes_cpp listener
```

### FastDDS <> Cyclone

This setup fails (no talker-listener communication):

```yaml
services:
  
  talker:
    build: .
    environment:
      - ROS_DOMAIN_ID=1
    command: ros2 run demo_nodes_cpp talker

  ros2router:
    image: husarnet/ros2router:1.9.0 # we have different a versioning and we're using DDS Router v2.1.0 inisde this docker image
    volumes:
      - ./config.yaml:/config.yaml
    environment:
      - PARTICIPANTS= # by keeping this env empty, we're bypassing auto-configuring the DDS Router, and we're using the config.yaml file
    command: ddsrouter -c /config.yaml

  listener:
    build: .
    environment:
      - RMW_IMPLEMENTATION=rmw_cyclonedds_cpp 
      - ROS_DOMAIN_ID=2
    command: ros2 run demo_nodes_cpp listener
```