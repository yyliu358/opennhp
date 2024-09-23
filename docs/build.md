

# Network-infrastructure Hiding Protocol

## Chapter 1: Introduction
**NHP** is a zero-trust Network-infrastructure Hiding Protocol published by CSA(Cloud Security Alliance), comparing with SPA, a single-packet authorization protocol in zero-trust SDP (software-defined perimeter), it has better stealthiness, stronger performance and reliability, more flexible scalability, and good Cintron compatibility.

**OpenNHP** is an open source software project developed based on the NHP standard. Before getting started with the OpenNHP project, it is recommended to read the following article:

- [Zero Trust NHP: Making Security “Cost-Effective”.](https://gitcode.com/opennhp/nhpdocs/overview)
- [Zero-trust network stealth protocol NHP standard specification] (https://gitcode.com/opennhp/nhpdocs/blob/main/NHPSpec-V0.9.4.pdf)

For a detailed explanation of OpenNHP's code structure and technology, please read:
- [OpenNHP Design Document](./docs/whitepaper.md)

## Chapter 2: OpenNHP Compatibility
OpenNHP has good compatibility, especially the support of Sintronic ecosystem, the following cryptographic algorithms and hardware and software are compatible with OpenNHP.

### 2.1 Cryptographic Algorithms
Chinese cryptographic algorithms: SM2, SM3, SM4
International cryptographic algorithms: Curve25519, AES, SHA256.
### 2.2 Operating System
Operating System Compatibility:
Windows ✅
Apple MacOS ✅
Unix UOS ✅
KylinOS ✅
CLPOS ✅
Apple iOS ✅
Android ✅
### 2.3 CPU Instruction Set
CPU Type Compatibility
x86 ✅
ARM Huawei Kunpeng ✅
LoongArch ✅
SW Shenwei ✅
## Chapter 3: Compiling OpenNHP
**Tips:** Windows 10/11 can run Linux through the WSL subsystem, please see the WSL official document for details: https://learn.microsoft.com/zh-cn/windows/wsl/install

- **[Enable WSL Function]** 
On Win10, you need to enable WSL first to use WSL to install Linux, please see the following figure for the setup interface. WSL Settings on Win10

- **[Install Linux on WSL]** It is recommended to install Ubuntu Linux on WSL by running the following command through PowerShell:
```
wsl --update
wsl --install -d Ubuntu
```
If you encounter the following problem, refer to: https://blog.csdn.net/weixin_44293949/article/details/121863559
``` text
Unable to extract list distribution from 'https://raw.githubusercontent.com/microsoft/WSL/master/distributions/DistributionInfo.json'. Unable to resolve server name or address
Error code: Wsl/WININET_E_NAME_NOT_RESOLVED
```
-**[IP address of WSL environment]** Run the following commands to obtain the IP address in the Linux environment of WSL:

| Host | The command to view the IP address| 
|:--:|:--:|
|Linux host in WSL| `hostname -I \| awk '{print $1}'` |  
|WSL host Windows host| `ip route show \| grep -i default \| awk '{ print $3}'` | 
### 3.1 System Requirements
- 3.1.1 `Go` environment: **Go 1.18** or above. Download the installer: https://go.dev/dl/

- For Windows and macOS, install Go through the downloaded installer.
- Under Linux environment, you can install Go directly through the management tool: :
`sudo apt install golang` 
- After installation, run the command `go version` to check the Go version number.
- On Windows and macOS, install Go via the downloaded installer.
On Linux, you can install Go directly through the administration tool: sudo apt install golang, or you can install Go manually with the following commands:
```bash
   1. sudo apt-get update
   2. wget https://go.dev/dl/go1.21.0.linux-amd64.tar.gz
   3. sudo tar -xvf go1.21.0.linux-amd64.tar.gz
   4. sudo mv go /usr/local
   5. export GOROOT=/usr/local/go
   6. export GOPATH=$HOME/go
   7. export PATH=$GOPATH/bin:$GOROOT/bin:$PATH
   8. source ~/.profile
   ```
 - After successful installation, run the command go version to see the Go version number.
- `GCC` environment:

 - **Linux and macOS: GCC 8.0 or above:**

	- To view the GCC version: `gcc -v`
	- Install GCC: `sudo apt install build-essential`
 - **Windows:**

	1. Install mingw64. mingw64 can be downloaded via the package management tool for msys2. Installation of msys2 system requirements, download and installation tutorials can be found at: https://www.msys2.org/. install_msys2

	2. Install GCC. enter the command in the msys2 console:
	```bash
	pacman -S mingw-w64-ucrt-x86_64-gcc
	```
	3. Configure GCC. Add the GCC tool path to the *%PATH%*  environment variable on Windows. 

	For example, if mingw-w64-gcc is installed in `C:\Program Files\MSYS2\`, you need to run this command:
	```bash
	setx PATH “%PATH%;C:\Program Files\MSYS2\ucrt64\bin
	```
	After successful execution, open a new command line window and check the version number of gcc
	``` bash
	gcc --version
	``` 

- Tip: You can run Linux on Windows via the WSL subsystem, see the official WSL documentation at https://learn.microsoft.com/zh-cn/windows/wsl/install.

	-It is recommended to run the latest version of Ubuntu, v22, on WSL, and install it by running the following command from PowerShell on Windows:
	```bash
	wsl --install --distribution Ubuntu-22.04
	```
### 3.2 Build
1. Pull the code repository
	```bash
	git clone https://gitcode.com/opennhp/nhp.git
	```
2. Go environment setup
	```
	go env -w GOPROXY="https://goproxy.cn,direct”
	```
3. Compile the build

- Linux and macOS: run the script make in the root folder of the code
	`make`
- Windows: Run the BAT file build.bat from the root directory
	`build.bat`

## Chapter 4: Running OpenNHP
### 4.1 Description of OpenNHP components
According to the build steps in the previous chapter, the build result will be output to the *release* directory, which has three subdirectories containing the three core components of OpenNHP: *nhp-agent*, *nhp-server* and *nhp-ac*.

- **nhp-agent:** The module that initiates the knock request with the identity and device information of the data visitor, usually installed on the device at the user's end.
- **nhp-server:** The module, usually a server program, that processes and validates knock requests. Its functions include validating knock requests and interacting with external authorized service providers to implement authentication operations, as well as controlling NHP access control to perform door opening actions.
- **nhp-ac:**The access control enforcement module, usually a server program. This module enforces the default deny all security policy and ensures network invisibility of the protected resource, usually on the same host where the protected resource resides. It is responsible for opening access to authorized NHP agents or closing access to unauthorized NHP agents, and performing release actions for NHP agents based on parameters returned by the NHP server.

### 4.2 OpenNHP Development and Test Environment Setup

#### 4.2.1 Development and Test Environment: Windows/MacOS Development Host + Linux Virtual Machine

Assuming that the development host is Windows or macOS, a simple OpenNHP test environment can be set up by installing a virtual machine environment (e.g., VirtualBox) and creating two Linux virtual machines. When creating the VMs, please set the NIC option to `“Host-only Adapter”`(as shown below), so that the IP of the VMs and the IP of the development hosts are in the same network segment.

	![VirualBox Network](./docs/images/vbnetwork.png)

	**Tip: ** If you want the virtual machine to be able to access the Internet at the same time, you can add another “NAT” card: VirualBox Network.

At this point, the environment of the three major components of NHP is set up as follows:

- [nhp-server] Runs on a Linux virtual machine with IP address 192.168.56.101.
- [nhp-ac] running on Linux virtual host, IP address 192.168.56.102
- [nhp-agent] running on Windows/macOS development host, IP address 192.168.56.1

#### 4.2.2 Network Topology and Basic Information of Development and Test Environment
 ![OpenNHP-Dev-WSL](./docs/images/dev_wsl.png)

| server name | IP address | basic configuration  |
|:--:|:--:|:--:|
| NHP-Server | 192.168.56.101  | **public key:** WqJxe+Z4+wLen3VRgZx6YnbjvJFmptz99zkONCt/7gc=<br/>**private key:** eHdyRHKJy/YZJsResCt5XTAZgtcwvLpSXAiZ8DBc0V4= <br/> **Hostname:** localhost <br/> **ListenPort:** 62206 <br/> **aspId:** example |
| NHP-AC | 192.168.56.102  | **public key:** Fr5jzZDVpNh5m9AcBDMtHGmbCAczHyPegT8IxQ3XAzE=<br/>**private key:** +B0RLGbe+nknJBZ0Fjt7kCBWfSTUttbUqkGteLfIp30=<br/>**ACId:** testAC-1 <br/> protected resources **resId:** test |
| NHP-Agent | 192.168.56.1  | **public key:** WnJAolo88/q0x2VdLQYdmZNtKjwG2ocBd1Ozj41AKlo=<br/>**private key: ** +Jnee2lP6Kn47qzSaqwSmWxORsBkkCV6YHsRqXCegVo= <br/> **UserId:** agent-0 |
Tip: Since version 0.3.3, most of the fields in the configuration file of each component support dynamic update, please refer to the comments of each configuration file for details.

### 4.3 Configuration and Operation of NHP-Server

#### 4.3.1 System Requirements
- Linux server or Windows
#### **4.3.2 Running**
Copy the nhp-server directory under the release directory to the target machine. Configure the toml file in the etc directory (see the next section for detailed parameters) and run nhp-serverd run.

- Linux environment:
	```bash
	nohup . /nhp-serverd run 2>&1 &
	```
- Windows environment:
	```bash
	nhp-serverd.exe 
	```
*Optional:* disable UDP port exposure: run `iptables_default.sh`

#### 4.3.3 NHP-Server Server Configuration Files

- Basic configuration: config.toml
- Gateway peer list configuration: ac.toml
- Client peer list configuration: agent.toml
- http service configuration: http.toml
- Server plugin read configuration: resource.toml
- Source address association list: srcip.toml
- Server plugin resource configuration: resource.toml

### 4.4 Configuration and Operation of NHP-AC

#### 4.4.1 System Requirements
Linux server with kernel support for ipset, you can check the ipset support with the following command
```bash
lsmod | grep ip_set 
```
#### 4.4.2 Run
Copy the nhp-ac directory in the release directory to the target machine. Configure the toml file in the etc directory (see the next chapter for detailed parameters), and run iptables_default.sh to add firewall rules, at which time external connections will not be established. Run nhp-acd run again.

Note: nhp-acd and iptables_default.sh need to be run under root privileges.

- Linux environment:
```bash
su
Linux environment: su . /iptables_default.sh
nohup . /nhp-acd run 2>&1 &
```
If you want to revert the changes made to iptables by `iptables_default.sh`, you can run the following command to clear it:
```bash
iptables -F
```
#### 4.4.3 NHP-AC Access Control Configuration File

- Base configuration: config.toml
- Server peer list: server.toml

### 4.5 NHP-Agent Configuration and Operation

#### 4.5.1 System Requirements
- Platforms: Windows, Linux, macOS, Android, iOS.

#### 4.5.2 Run
Copy the nhp-agent directory under the release directory to the target machine. Configure the toml file in the etc directory (see the next chapter for detailed parameters) and run nhp-agentd run.

Linux:
```
nohup . /nhp-agentd run 2>&1 &
```
Windows:
```
nhp-agentd.exe run
```
#### 4.5.3 Configuration file of NHP-Agent
Basic configuration: [config.toml](agent/main/etc/config.toml)
Knock target configuration: [resource.toml](agent/main/etc/resource.toml)
Server peer list: [server.toml](agent/main/etc/server.toml)

#### 4.6 Testing NHP network stealth effect
To verify the NHP network stealth effect, you can test by pinging nhp-ac host *(IP: 192.168.56.102)* through nhp-agent host *(IP: 192.168.56.1)*.

|Test Case |Test Command| Test Purpose |Expected Result|
|:--:|:--:|:--:|:--:|
|nhp-agent is not running| `ping 192.168.56.102`| Test AC stealthy to Agent| ping failed|
|nhp-agent is running| `ping 192.168.56.102`|Test AC open to Agent| ping Successful|

## Chapter 5: Logging Instructions

### 5.1 Log File Locations
Log files are generated in each component's respective logs directory, with the date as the filename, and can be viewed with the tail command.

To view the logs of nhp-server
```bash
tail -f release/nhp-server/logs/server-2024-03-10.log
```

To view the logs of nhp-ac
```bash
tail -f release/nhp-ac/logs/ac-2024-03-10.log
```
To view nhp-agent's log
 ```bash
tail -f release/nhp-agent/logs/agent-2024-03-10.log
```
Log Levels:
- Error
- Critical
- Warning
- Info
- Debug

## Appendix A: FAQ

- **Q:** Compile error on Windows platform: `running gcc failed: exec: “gcc”: executable file not found in %PATH%` 
  **A:** The reason is that the `gcc` compilation tool is not installed. Please follow the steps in 3.1.3 above to install GCC.

- The log shows the error: `NHP-AC [Critical] received stale packet from 192.168.56.101:62206, drop packet` . 
   [Cause] The receiver has a requirement on the packet reception time, and the packet cannot be sent more than 10 minutes earlier than the receiver.
   [Fix] Time synchronization between two machines



