# Lab 2 Network Device Commands with Python
Template for Labs for Florida State College of Jacksonville Software Defined Networking

## :triangular_flag_on_post: Learning Goals
- Create a Python script using the netmiko library
- Utlize Netmiko to connect to a Cisco Always-On-Sandbox device
- Utilize Netmiko to run show commands on the device
- Parse the device output using ntc-templates
- Capture command output to display a formatted message to the terminal

## :globe_with_meridians: Overview:

As you continue to build on your Python skills you will learn that there is a python library for almost anything. Network Automation didn't appear out of nowhere it was a slow development process to the point we can utlize python to interact with devices. The history on the development includes several different python modules that eventually formed into the tools we use today just like in traditional networking. It started with the insecure `telnet` library which communicates it's limitations from the name. Everything you do using telnet is in **PLAIN TEXT!** This means if anyone captures that traffic over the wire they would be able to read it, including your username and password from logging in.

To address the security concern SSH was introduced encrypting the communication between the administrator and the device. In traditional networking you use terminal emulators like Putty or the Linux SSH client to connect to the device. In Python the `Paramiko` library was developed to handle the key exchange between the machine running the script and the network device. `Paramiko` although robust in handling SSH connections was rather complicated especially for network engineers many of whome have little experience writing code. To address this the `Netmiko` library was developed which simplifies coding in the parameters for the SSH connection by simply passing in the username, password, and device type.

In this lab you will utilize the netmiko library to connect to a network device hosted in the Cisco DevNet Always On Sandbox.

### Working with Python Libraries
Terms in Python can sometimes be up to the individual communicating. Python comes with standard built-in libraries such as `os`, `sys`, `math`, `datetime`, and `json`. However there are many community developed that can easily be downloaded, imported, and used to help us write cleaner code. Netmiko for example include paramiko so when you download netmiko it additionally has a dependency on paramiko therefore paramiko is also installed when we install netmiko. There a few methods to install a python library with the most common being the `pip` command line utility. Here is a simple example of using the command.

```bash

pip install netmiko

```

What do you do when you have a large project being worked on by a large amount of developers? The list of python packages and their dependencies can get *HUGE!* Not to mention what happens if a package is updated and breaks your code? To overcome this we use a `requirements.txt` file. This maintains a list of the required libraries as well as their versions. This makes management of the various packages quite easy. If you work on a project and add a library you can simply update the requirements file and include it in your next pull request. If you are working on a project and clone it for the first time you can install everything with a single `pip` command and pass in the file. Examples of adding a new requirement and installing via the file below.

```bash

#Adding a requirement
pip freeze >> requirements.txt

#Install all packages using the requirements.txt file
pip install -r requirements.txt

```

In our labs we are using the requirements.txt file which is loaded into your devcontainer upon creation and installed when the container is created. This can be viewed in the `devcontainer.json` file as the `postCreateCommand`.

### Reviewing the Docs, Methods, and Arguments
Anybody can cop/paste code including installing package and using the methods. How do you know from an example or tutorial what is required and what is optional? How do you know all the methods available to you with that package? From an interactive Python shell you have options on reviewing the information. This is great in a pinch or as a reference but utlimatly you should *READ THE DOCS* to get familiar and gain understanding wholistically. 

For references or review from the command line you have four options avialble to you the built in `dir()` function for reviewing the attributes of an object, mdoule, or package in a quick reference situation. The built in `help()` function to open the documentation for a module, class, or function which will pull the docstrings and descriptions. The `__doc__` syntax to pull the docstring of an object inline without the pager interface included in `help()`. Finally the `inspect` module which is the details including the source code, signatures, parameters names, and defaults which should be used when you want specifics not vague docstrings.

*`dir()`*
```python

import netmiko

dir(netmiko)

```

*`help()`*
```python

import netmiko

help(netmiko)

```

*`__doc__`*
```python

import netmiko

print(netmiko.ConnectHandler.__doc__)

```

*`inspect`*
```python

import inspect, netmiko

print(inspect.getdoc(netmiko.ConnectHandler))

```

### Authentication with 'getpass'
Although we are using Python to connect to the network device it is still using SSH under the hood. If you think about accessing a device using SSH the authentication rules still apply. This means in the most basic example you have to pass in a username and password. Hard coding a username and password into code is not a good practice as you risk pushing your changes to the repository therefore revealing your username and password to other contributors or bad actors if the repository is Public. Even removing your username and password if accidently pushed does not fix it as it will be available in the repository history logs. In short you should NEVER hard code a username or password into your script. NOTE: This authentication method does not play well if multifactor authentication is enabled on the network device. If using an MFA solution you would need to include the token from your MFA device and/or application. 

You could use the built in `input()` function to gather the username but `input()` displays what the user types to the terminal still posing a security risk to shoulder surfers or it being kept in your terminal logs. In order to obtain the password from the user we utilize the 'getpass' library which takes an input from the user without writing to the terminal. 

### Netmiko Device Setup
Netmiko was built specifically to interact with network devices which makes our lives as network professionals a lot easier. The main variables netmiko requires inlcude the 'device_type', the hostname as 'host' if the hostname is resolvable by DNS if not use the ip address. Finally a 'username' and 'password' for authentication. For a single device this is stored a python dictionary. 

```python

device = {
    "device_type": *<type>*,
    "host": *<hostname or ip address>*,
    "username": *<username>*,
    "password": *<password>*
}

```

There are a few things to note in this example. The `device_type` for example needs to be one of the supported device types included and shipped with netmiko. You can find the full list by viewing the `dir()` output in the terminal. To make the output more readable you can save the output as a variable which creates a python list and loop through the list.

```python

output = dir(netmiko)

for item in output:
    print(item)

```

Explicitly setting the device type informs netmiko what SSH parameters to send to paramiko which is the benefit of using netmiko. 

As stated the hostname of the device must be resolvable by the system executing the scripts DNS server. If not it has no IP to form the connection. If the IP is not resolvable you can enter the IP as a string value ex. `10.0.0.10`.

As stated you should *NEVER* hard code usernames and passwords in your script. The risk of uploading to the repository is very high and difficult to clean up. To include the username and password for the device collect it dynamically at your scripts runtime by using the functions `input()` and the package `getpass`. This information can be stored as a variable and the variable set as the value in the dictionary.

```python

import getpass

user = input("Enter your username: ")
pass = getpass.getpass("Enter your password: ")

```

### Creating the Connection
Although we have configured a dictionary to connect to our device that really doesn't do anything and hasn't even connected to the device yet. To connect we need to create a variable for the connection which is equal to the Netmiko ConnectHandler method passing in the device we created.  This creates our SSH connection. After executing this you can verify the SSH session on the device using the command `show ssh` on Cisco. To make sure the connection stays open you typically want to store the connection as a variable.

```python

device_connection = ConnectHandler(device)

```

### Sending Commands
With the connection setup we can now send commands to our device same as you would in an interactive terminal. We use the `send_command` method included in ConnectHandler to send the commands. As with almost everything in Python we don't just want to send the commands we want the output as well. To do this we create a variable with a value equal to our connections send_command method passing in the command we wish to send. We can then view the output by printing it to the terminal.

```python

output = device_connection.send_command('show ip interface breif)
print(output)

```

### TextFSM & NTC Templates
Once you print the output of the command you will very quickly notice a big problem... **THE OUTPUT IS NOT STRUCTURED AT ALL!**  This poses a big problem for a few different reasons.

1. From a human perspective you can't read it!
2. From a Python perspetive it is completely unusable as it is not formated in anyway such as `json`, `yaml`, `csv`, or `xml`

The command line output that you get when interacting with a network device beleive it or not is actually meant **FOR HUMANS**. Network automation however requires a different way of thinking. We don't script or automate to gather simple one off commands. We automate for consitency, effeciency, and to lower the human error factor. When we think about show commands and the output you may be asking yourself 'What would be the purpose of show commands` which is 100% valid. When it comes to show commands that is to gather data from the device. However many of these commands contain extra information or multiple commands need to be run to gain a wholistic view of whatever information you are attemtpting to retreive off of the device. For example you wouldn't want to run a show command and save the complete output to a file for review. That defeats the purpose of scripting and automation. Instead you would want to create a formatted string and include the exact information you are after. This is what enable automated reporting!

`TextFSM` is a Python library used to parse CLI output into structured data so we can make use of it in Python. `ntc-templates` is a Python library sponsored by [Network to Code](https://networktocode.com/) that include pre-built templates for the most common network vendor devices. 

There are multiple ways to utilize these pre-built templates for this course we will keep it simple and import the `parse_output()` method directly from `ntc_templates.parse`. These templates don't magically know what device and command you entered magically though. We have to specify. Luckily the templates were designed for integration and use with netmiko specifically. The method in it's most basic form requires us to pass in key/value parameters of `platform`, `command`, and `data` where platform is equal to our device type, command is equal to the command we sent with `send_command()`, and `data` is the output from running the command.

```python

from ntc_templates.parse import parse_output

parsed_interfaces = parse_output(platform='cisco', command='show ip interface brief', data=output)

```

You can now work with the data the same you would any other python list or dictionary. To review your data in it's new form you can print it to the terminal or for better readability import the Pretty Print library named `pprint` and print it to the terminal. You will see it is much easier now.

```python

from pprint import pprint

pprint(parsed_interfaces)

```

### Text with Dynamic Content
With the specific information we desired extracted we can now use it for follow on actions such as running another command or creating a report. To create a report a template should be authored and utlized for consistency. For these labs we will use a simple docstring declared in our script as a string variable. For any information you would like dynamically added to the string the syntax looks as `f"this is text. This is using a {variable}"` within the text. You can then save this a a text file, html document, markdown, or whatever reporting system is in place at your organization. Considering there are multiple python librarie for working with a large aaray of documents such as Excel files, Word documents, and pdf files we are able to adapt no matter what the system is.

```python

interface_name = "GigabitEthernet0/1"
interface_ip = "10.10.0.1"

report =f"Interface {interface_named} ip address is {interface_ip}"

print(report)
```

---

## :card_file_box: File Structure:

'''bash
Lab-2-Network-Device-Commands-with-Python/
├── README.md
├── LICENSE
├── requirements.txt
├── .gitignore
├── .devcontainer/
|   └── .devcontainer.json
├── .github/
|   └── workflows
|       └── autograding.yml
├── src/
|   ├── __init__.py
|   └── main.py
├── data/

---

## Components


### 1. **devcontainer.json**
This includes the configuration for your development container. Do not make any changes to this file or you risk breaking your dev container. If you do end up breaking it you can simply do a `git fetch` to return your repository to the default from the main repository.

### 2. **autograding.yml**
These are the tests set for running your grade. This uses `github actions` as the grader which is heavily used in CI/CD pipelines. **DO NOT ALTER THIS FILE! IF IT IS ALTERED IN ANY WAY YOU WILL RECEIVE 0 POINTS FOR THE ASSIGNMENT AND REPORTED FOR ACADEMIC DISHONESTY.**

### 3. **__init__.py**
text

### 3. **main.py**
text

## :memo: Instructions
1. text
2. text
3. text

## :page_facing_up: Logging
text

## :green_checkmark: Grading Breakdown
- x pts: 
- x pts:
- x pts: