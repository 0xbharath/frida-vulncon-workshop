# Setup

You can find all the workshop content in the Git repo [https://github.com/0xbharath/frida-vulncon-workshop](https://github.com/0xbharath/frida-vulncon-workshop)

- `documentation` branch has this documentation content.
- `hands-on` branch has the hands-on content.

## Following along

### Browser based labs

Easiest way to follow along the workshop is to use the labs at [https://killercoda.com/0xbharath/scenario/Frida-Workshop-Vulncon](https://killercoda.com/0xbharath/scenario/Frida-Workshop-Vulncon)

### Manual setup using Docker

1. Clone the workshop repo [https://github.com/0xbharath/frida-vulncon-workshop](https://github.com/0xbharath/frida-vulncon-workshop)
2. Build the Docker container `docker build -t frida-workshop .`
3. Run the Docker container and get shell access `docker run -it --rm frida-workshop /bin/bash`
4. You can get additional shells on the Docker by running `docker exec <CONTAINER_ID> -it /bin/bash`

## Few things about the lab environment

1. You can work with `bash` shell in the labs but `tmux` is installed and preferred. Use `tmux` cheatsheet for common commands https://tmuxcheatsheet.com/
2. `~/frida-vulncon-workshop/exercises` directory contains all the hands-on exercises related files
3. There is a Python virtual environment in `~/frida-playground` which all the necessary packages. To use this virtual environment, use the following commands:

```
# To activate the environment

cd ~/frida-playground
source ~/.venv/bin/activate

# To install any additional python packages

uv pip install <package_name>
```

