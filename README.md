# ansible

Starting Again...

First step : hum ssh key generate kregne aur usse check krenge ki humare pass store ho chuki hai ki nhi 

ssh-keygen -t ed25519 -C "Amul default"

fir hum usse check krenge ki ho gyi yaa nhi 

la -ls .ssh

fir hume aesa output dikhega 

admin@Docker:~$ la -ls .ssh
total 12
4 -rw------- 1 admin admin 444 Jun 13 11:09 id_ed25519         <<<< dekho key ho chuki hai genrate
4 -rw-r--r-- 1 admin admin  94 Jun 13 11:09 id_ed25519.pub
4 -rw-r--r-- 1 admin admin 666 Jun 13 11:32 known_hosts


ab hum yahi key jitne bhi server hai sb me copy kara denge taaki baar baar hume password naa dalna pade unse connect krne
ke liye 


Copy command : last me server ke ip change honge jitne bhi server hai sb ke ip dalo aur copy kro

"ssh-copy-id -i ~/.ssh/id_ed25519.pub 10.0.137.60"
"ssh-copy-id -i ~/.ssh/id_ed25519.pub 10.177.0.4"
"ssh-copy-id -i ~/.ssh/id_ed25519.pub 10.177.0.5"




server side :  <<< jo bhi humne copy kia hai ssh key woh copy ho chuki hai ya nhi 


admin@Ubuntu Server 3:/home$ cd admin/.ssh/
admin@Ubuntu Server 3:~/.ssh$ ls
authorized_keys
admin@Ubuntu Server 3:~/.ssh$ cat authorized_keys
output :
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJ14+NL9Hoa3enAscts5nSMwT5/H8So6TghNb5bhFvIV Amul default

server side in root : <<< sidha ye bhi path daal doge toh pata chal jaayega
root@Ubuntu Server 3:/# cat home/admin/.ssh/authorized_keys
output :
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJ14+NL9Hoa3enAscts5nSMwT5/H8So6TghNb5bhFvIV Amul default

---------------------------------------------------------------------------------------------------------------------------------------------------------

Me ansible ke liye wpis ssh key genrate krunga aur ussi keys se sirf apne server handle krunga toh me alaga se banakar wapis sb copy krunga aur fir 

"ssh -i ~/.ssh/ansible 10.177.0.5" <<< aese server ke ip's dal kr connect krunga aur password bhi nhi puchega aesa isliye jab mene key genrat kia toh 
no passpharse kia tha means mene empty chod krr enter kr dia mtlb koi password ki zarurt nhi

genrate aur kese copy krne hai woh same step hai isliye wese hi kro aur baki sb me server me check kro ki woh keys aa chuki hai ki nhi wo hume kese pata
chlega woh same step hai upr wala dekho janha hum authorie_keys check kie the usme pehle sirf ek key tha amul default wala abhi add ho chuka hoga ansible
wala.



Ye hume ansible side krna hai janha humare desktop hai ubuntu ka 


alias ssha='eval $(ssh-agent) && ssh-add'
ye jese hi command enter kroge aapko password pucheg ka admin ka dalna mene admin ka password admin dala hai kyun ki 
mene wese hi set kia fir aap wapis alias ssha command daaloge toh eval wla pura command shw hoga jesa fir 


sudo nano .bashrc <<<< ye open kro ubuntu host pr aur usme 
# ssh agent
alias ssha='eval $(ssh-agent) && ssh-add'

krke save kr do aur 

aur wapis check krna ki....

admin@Docker:/home$ alias ssha			<<< ye likhe ne se 
alias ssha='eval $(ssh-agent) && ssh-add'	<<< ye output aa rha hai ya nhi



# some more ls aliases
alias ll='ls -alF'
alias la='ls -A'
alais l='ls -CF


after create git repositary we ahed for ssh in setting then go to code in github using link git clone after that we create usernam aur user email

admin@Ansible_workStation:~$ git clone git@github.com:amul95/ansible.git
admin@Ansible_workStation:~$ cd ansible/
admin@Ansible_workStation:~/ansible$ ls
README.md
admin@Ansible_workStation:~/ansible$ git config --global user.name "Amul Raval" 
admin@Ansible_workStation:~/ansible$ git config --global user.email "ravalamul@gmail.com"
admin@Ansible_workStation:~/ansible$ cat ~/.gitconfig 
[user]
	name = Amul Raval
	email = ravalamul@gmail.com



git add README.md 
git commit -m "updated readme file"
git push origin main

nano README.md   >>>> me aama changes kryu kai pn lahyu
git add README.md >>>> add karai git ma  
git status      >>>> je pn chnages kryu ae green ma modified aavse 
git commit -m "updated readme file" >>> commit kryu
git push origin main >>> aa command push krta j tya git ma changes thai jase 


### me mara Ip address store krya inventory file ma 

nano inventory  >>> create kri and ema type krya server na ip's
git add inventory >> have git ma add kro
git commit -m "add inventory file"   >>> commit kro 
git push origin main >>> push kryu 

and check kryu me git ni site pr aavi gyu tu 	

ansible all --key-file ~/.ssh/ansible -i inventory -m ping

aa command lakhine ne aapda inventory file ma jetla pn server ip che ae badhne ping jase ane suceess lakheli aavse command dhyan thi jovo 




nano ansible.cfg

[defaults]
inventory = inventory
private_key_file = ~/.ssh/ansible



ctrl+O enter ctrl+X

ansible all -m ping
ansible all --list-host

git add ansible.cfg 
git commit -m "new file added"
git push origin main


all set 

ansible all -m  gather_facts

 
ansible all -m  gather_facts --limit server ip address 

* je pn me store krya che ae badha IP addreess step by step nakho 


-- Server static ip address 	

server_1
ifconfig eth1 10.0.137.60/24
route add default gw 10.0.137.1

server_2
ifconfig eth1 10.0.137.165/24
route add default gw 10.0.137.1

server_3
ifconfig eth1 10.0.137.25/24	
route add default gw 10.0.137.1



-- push apt-get update thorugh ubuntu desktop means ansible host 


ansible all -m apt -a update_cache=true
# ye hua nornam apt-get update pr ye work nhi karega kyun ki uske liye hume sudo lagna pdega tb hi work krega pr hum normal us system sudo apt-get update likhene toh work kerega but hum ansible se kese krvayenge 

ansible all -m apt -a update_cache=true --become --ask-become-pass

# sudo apt-get update ye hum kuch is tarh type krenge ansible me 

ab hum is command ke dep me jaate hai 


ansible all -m 
# iska mtlb ki mere ansible me folder me jo bhi files hai like ansible.cfg jisme ssh key ka path hai aur inventory me jo ip hai server ke woh bhi hai toh un sb ke saath muje kyaa krnna hai ping krna hai toh me ansible all -m ping aese likhunga toh jo bhi server ip hone un sb ko ping hoga 

apt
# ye hum use krte hai apt-get update yaa koi apt package install krne ke liye toh hum apt command ke zariae install krte hai toh hume hum baaki server me apt-get update karvana hai toh hum ansible all -m apt type krenge aur bdhte hai aage

-a update_cache=true

# fir -a use kia that menas = argument aur uske baad update_cache=true likha ynha update_cache means hua apt-get update jo by defult of hota hai means false toh hum ynha true krenge taaki ye command work ho skte aur ye sudo ka similar hai ansible me 

--become --ask-become-pass

# ab aata hai become aur become pass means password ki aap jo krna chahte hai servers pr usme aapke pass sudo yaa sudo ka password hai toh hume jese hi ye last me likhenge toh hmse sudo ka passwd pucha jayega fir humare ye command work krega aur jitne bhi server hai sb me sudo apt-get update ho jaayega 

*************************************************************************************

Ab me chahta hoon ki mene jese package install krta hoon like 
apt-get install <package name> toh ye kese krunga ansible host side se sb server pr ab woh sikhenge 


ansible all -m apt -a name=vim-nox --become --ask-become-pass 

Centos Linux 

isme hume apt ki jagh use krna hai yum keyword example deta hoon me jese ki hum apt-get update likhte hai ubuntu me toh isme hume ye likhna hai cent os me yum update toh start ho jayega toh ye is tarh se kaam krta hai 

aur haan isme hume become password dene ka zarrut nhi hota hai kyun ki sudo jesa kuch nhi aata hai cent os me 

ansible all -m yum -a name=tmux

jese hi ye command type krenge humare server ip wale me sb tmux install ho jayega 

ab badhte hai playbook ki aur 

-- Playbooks

muje ab maan lo apne saaare servers pr apache server install krna hai toh me kya krunga ek tarika hai server pr jaakr baari bari se sb command likhu yum install 'pkg name' yaa fir usse ansible host me likhu kuch is tarike se jo me run kru toh sb me ek sath install ho jaaye yes aesa mumkin hai playbook ke zariae 


ab step 1 sbse phle hum ansible host me humara jo ansible wala directory hai usme jayenge aur usme ab ek nano file create krenge 

step2 - nano install_apache.yml
toh ek text editor open hoga as u all know 

---
- hosts: all
  tasks:
  - name: install apache2 package
    yum:
      name: httpd 

itna likh kar hume Ctrl+O uske Ctrl+X bahar nikl jao 

ab host : all mtlb jo bhi server ip humne menion kie hai woh 
tasks: ab hume in sb host ke saath kya krvana hai toh woh hoga tasks

then hum name : denge kuch ki me ye install kr rha 
yum : isliye kyun ki hum centos me koi package install krva rhe 
name : jo bhi pakcage ka naam hai woh daalo 

toh hua ye ki 'yum install htppd' ye command hum sb me bari baari dalte woh ab ansible se hi sb me ek saath push hoga aur hume output milega task me ok krke ab bahdte hai aage 


