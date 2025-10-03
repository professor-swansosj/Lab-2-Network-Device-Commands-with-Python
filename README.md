# Lab 2 — Network Device Commands with Python

**Course:** Software Defined Networking  
**Module:** Network Automation Fundamentals • **Lab #:** 2  
**Estimated Time:** 90-120 Minutes

## Repository structure

```text
Lab-2-Network-Device-Commands-with-Python
├── .devcontainer
│   ├── devcontainer.json
│   ├── health_check.py
│   ├── post_create.sh
│   └── post_start.sh
├── .gitignore
├── .markdownlint.json
├── .markdownlintignore
├── .pettierrc.yml
├── INSTRUCTIONS.backup.md
├── INSTRUCTIONS.md
├── LICENSE
├── README.backup.md
├── README.md
├── data
│   └── inventory.example.yml
├── lab.yml
├── prettierrc.yml
├── requirements.txt
└── src
    ├── __init__.py
    └── main.py
```


## Lab Topics

### Installing Python Libraries with pip
Terms in Python can sometimes be up to the individual communicating. Python comes with standard  built-in libraries such as `os`, `sys`, `math`, `datetime`, and `json`. However there are many  community developed that can easily be downloaded, imported, and used to help us write cleaner  code. Netmiko for example include paramiko so when you download netmiko it additionally has a  dependency on paramiko therefore paramiko is also installed when we install netmiko. There a few  methods to install a python library with the most common being the `pip` command line utility. which stands for 'Python Installer Package'.
You can find more information about pip here: https://pip.pypa.io/en/stable/ or by running `pip --help`. In the example below we use pip to install the netmiko library which is a multi-vendor library that simplifies and standardizes the process of connecting to network devices via SSH.


```bash
pip install netmiko

```

### Maintain the `requirements.txt` File
When working on a project it is common to have multiple dependencies. To make it easier to manage these dependencies we can use a `requirements.txt` file. This file is simply a text file that lists the libraries and their versions that are required for the project. This makes it easy for others to install the same dependencies by simply running a single command.
You can create a `requirements.txt` file manually or you can generate one using the `pip freeze` command. The `pip freeze` command lists all the installed packages in the current environment along with their versions. You can redirect this output to a `requirements.txt` file using the following command:


```bash
pip freeze > requirements.txt

```

### Install Dependencies from `requirements.txt`
To install the dependencies listed in a `requirements.txt` file, you can use the following command:


```bash
pip install -r requirements.txt

```

### Using Virtual Environments
When working on multiple Python projects, it is common to have different dependencies and versions of libraries for each project. To avoid conflicts between these dependencies, it is recommended to use virtual environments. A virtual environment is a self-contained directory that contains its own Python interpreter and libraries.
You can create a virtual environment using the `venv` module that comes with Python. To create a virtual environment, you can use the following command:


```bash
python -m venv myenv
source myenv/bin/activate
pip install -r requirements.txt

```

### Cleanup a Virtual Environment
To deactivate a virtual environment and return to the global Python environment, you can use the deactivate command.  This will restore your PATH and other environment variables to their original state. You can then delete the virtual  environment directory if you no longer need it. If you need it back again you can always recreate it.


```bash
deactivate

rm -rf myvenv

```

### Using the dir(), help(), and inspect Modules
When working with Python libraries, it is often helpful to explore the available classes, methods, and functions. The `dir()` function can be used to list the attributes of a module or class. The `help()` function can be used to display the documentation for a module, class, or function. The `inspect` module provides several useful functions to get information about live objects such as modules, classes, methods, functions, tracebacks, frame objects, and code objects.
You can use these functions to explore the Netmiko library and identify the correct device type and method to send commands to the device.


```python
import netmiko
help(netmiko)
dir(netmiko)

inspect.getmembers(netmiko)

```

### Getting Credentials Securely
When working with network devices, it is important to handle credentials securely. Hardcoding credentials in your scripts can lead to security vulnerabilities if the code is shared or stored in a public repository. Instead, you can use the `input()` function to prompt for the username and the `getpass` module to securely prompt for the password without echoing it to the terminal.


```python
import getpass

username = input("Enter your username: ")
password = getpass.getpass("Enter your password: ")

```

### Device Setup for Netmiko
To connect to a network device using Netmiko, you need to create a device dictionary that contains the necessary connection parameters. The device dictionary should include the device type, host (IP address or hostname), username, and password. You can also include optional parameters such as port, secret, and timeout.


```python
device = {
  "device_type": "cisco_ios",
  "host": "sandbox-iosxe-latest-1.cisco.com",
  "username": username,
  "password": password,
  "port": 22,
  "secret": password,
  "timeout": 10
}

```

### ConnectHandler
The `ConnectHandler` class in Netmiko is used to establish an SSH connection to a network device. You can create an instance of the `ConnectHandler` class by passing the device dictionary as an argument. Once the connection is established, you can use various methods to send commands to the device and retrieve the output.


```python
from netmiko import ConnectHandler

net_connect = ConnectHandler(**device)
output = net_connect.send_command("show ip interface brief")
print(output)

```

### Sending Commands
The `send_command()` method of the `ConnectHandler` class is used to send a single command to the network device and retrieve the output. You can pass the command as a string argument to the method. The method returns the output of the command as a string.


```python
from netmiko import ConnectHandler

net_connect = ConnectHandler(**device)
output = net_connect.send_command("show ip interface brief")
print(output)

```

### TextFSM and ntc-templates
TextFSM is a Python library that allows you to parse unstructured text into structured data using templates. The ntc-templates project provides a collection of pre-defined TextFSM templates for various network devices and commands. You can use these templates to parse the output of commands sent to network devices using Netmiko.
To use ntc-templates, you need to install the library and set the `NET_TEXTFSM` environment variable to point to the directory containing the templates. You can then use the `parse_output()` function from the `ntc_templates.parse` module to parse the command output using the appropriate template.


```python
from ntc_templates.parse import parse_output

template = "cisco_ios_show_ip_interface_brief.textfsm"
parsed_output = parse_output(template, output)
print(parsed_output)

```

### f-text Strings
f-strings, also known as formatted string literals, are a way to embed expressions inside string literals, using curly braces `{}`. They were introduced in Python 3.6 and provide a more readable and concise way to format strings.
To create an f-string, you simply prefix the string with the letter `f` or `F`. Inside the string, you can include any valid Python expression within curly braces, and the expression will be evaluated at runtime and inserted into the string.


```python
interface = "GigabitEthernet0/1"
ip_address = "192.168.1.1"
subnet_mask = "255.255.255.0"

config = f"""
interface {interface}
  ip address {ip_address} {subnet_mask}
"""
print(config)

```



## License
© 2025 Your Name — Classroom use.
