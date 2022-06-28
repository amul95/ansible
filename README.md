# Generate SSH-Key...

	-- ssh-keygen -t ed25519 -C "Amul default"
	
> isme mene passwd set kia agr me chahu oth blank chorr skta hoon jise ye passwd protected key nhi hoga.
> store hui key is path par milegi : /root/.ssh/ me id_25519 ke naam se hoga 

	-- ssh-keygen -t ed25519 -C "Ansible"
	
> isme mene passwd nhi set kiaa kyun ki jab bhi server se hum connect honge hum key ke through toh
  direct connect honge hume baar baar passwd nhi dena padega

# Copy SSH-Key's to all Servers
> humne jo bhi key create ki hai woh hum humare sare server ko copy karayenge aur check krenge ki woh wnha copy ho chuki hai yaa nhi 

	ssh-copy-id -i root/.ssh/id_ed25519.pub 0.0.0.0



# Connect Server's through ansible key
 	
	ssh -i ~/.ssh/ansible 10.177.0.5

# alias ssha

> hume .bashrc me jaakr niche wala command type krke save krna hai aur check krna hai

nano .bashrc  

	# ssh agent
	  alias ssha='eval $(ssh-agent) && ssh-add'
	  
> Fir hume check krenge ki woh type kia hua woh sahi se work kr rha hai ki nhi  

**Hume alias ssha type krna hai** 	
> toh ye output milega...

	alias ssha='eval $(ssh-agent) && ssh-add'
         
**fir hume sirf ssha type krna hai toh aesa output aayega**

	[root@Ansible_WS ansible]# ssha
	Agent pid 3382
	Enter passphrase for /root/.ssh/id_ed25519: 
	Identity added: /root/.ssh/id_ed25519 (CentOS)

# Git Clone
> ab hume github use krna hai toh uske pehle github me account banao aur fir usme ek repository banao uske bad hume github ke settings me jana hai aur ssh keys me jaakr jo bhi humne ssh key generate ki hai ansible wale host pr usse copy krke ynha save kr denge fir hum dekhenge ki jo b humne repository create kia hai usme code krke ek box hoga use click kroge toh jo link hoga usse copy krna hai fir usse humare terminal likhna hai 

	 git clone git@github.com:amul95/ansible.git


	admin@Ansible_workStation:~/ansible$ git config --global user.name "Amul Raval" 
	admin@Ansible_workStation:~/ansible$ git config --global user.email "ravalamul@gmail.com"
	admin@Ansible_workStation:~/ansible$ cat ~/.gitconfig 
	[user]
	name = Amul Raval
	email = ravalamul@gmail.com

> Fir ab ek ke bad ek file add krenge aur dekhenge ki woh github me kese changes hote hai 

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

# PHP

> ab hum PHP install krenge uske liye bhi mene jo playbook create kia tha usme mene bas command add kr dia yum php bas 
> install ho jaane ke baad hum jis bhi servers humne PHP install kr dia toh wnha hume jaakr ye command daalna 
	
server me jaakr terminal me hume ek file create krni hai 

save krne ka location hai : cd /var/www/html/ me jakar nano info.php krke text-editor me 
	<?php
	phpinfo();
	?>
ye wala likh kar save kr dena hai bas fir hume ansible wale host pr jaakr sidha browser me http://serverip/info,php enter krna hai 

tb hume php ka page dekhenge...
	
	


