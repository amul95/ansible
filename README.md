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
> Hum alias ssha isliye use kr rhe hai kyun ki jab hum git se push n pull krenge toh baar baar hume authentication naa krna pade isliye
> ek baar command daalne se hmse passwd bhi jayega aur hme de dena fir woh automatic fetch kr lega 
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

		admin@Ansible_workStation:~/ git clone git@github.com:amul95/ansible.git
		admin@Ansible_workStation:~/ cd ansible
		admin@Ansible_workStation:~/ansible$ git config --global user.name "Amul Raval" 
		admin@Ansible_workStation:~/ansible$ git config --global user.email "ravalamul@gmail.com"
		admin@Ansible_workStation:~/ansible$ cat ~/.gitconfig 
		[user]
		name = Amul Raval
		email = ravalamul@gmail.com

> Fir ab ek ke bad ek file add krenge aur dekhenge ki woh github me kese changes hote hai 

# Git Push & Pull

> mann lo mene mere ansible host mtlb ubuntu me yaa centOS me kuch changes kia chahe ReadMe file ho yaa mene jo inventory file banaya
> toh mene usme jo bhi changes kie me chahra hoon woh mere github me wnha changes hokr save rhe taki muje jab bhi zarurat pde me wapis
> wnha pull kr ke files le sku aur mene jo apne local side se kuch changes kie woh me push krke github me save kr sku 

	git add README.md 
> jis file me humne changes kia us file ka name use krna hai git add commad ke aage
	git commit -m "updated readme file"
> fir hume commit krna hai uspr ki commit krna hai kuch uspr comment de do ki taaki dhyaan rhe 
	git push origin main
> fir ye last hai git push mtlb jo bhi mene changes kia woh wnha github ki website pr ho jaaye 
	git pull
> ye command jab hum github site pr kuch changes kre toh woh hum apne local site pr chahhiye toh hum ye krenge 

# Create inventory File 
> isme me ab apne servers ke ip daaluunga taaki sb ek sb ek jgh store ho aur me jab chahu in sb pr koi command chalana toh kr sku
	ansible all --key-file ~/.ssh/ansible -i inventory -m ping
> is comaand se hum apne saare server ke ip ko ping kr skte hai 

# Create Ansible Configuration File 

	nano ansible.cfg
	

	[defaults]
	inventory = inventory
	private_key_file = ~/.ssh/ansible

> fir hum sirf ansible all -m ping itne hi short command se ping kr paayenge 

	ansible all -m ping

	ansible all --list-host

> hume git push bhi krna padega taaki github pr save ho jaaye humara file

	git add ansible.cfg 
	git commit -m "new file added"
	git push origin main

all set 

# Gather informations From Servers

	ansible all -m  gather_facts
> ye all servers ke liye ek saath sb server ka info milega

	ansible all -m  gather_facts --limit <server ip address> 
> ye perticular server ka information gather krna chahte hai 

# How to push 'sudo apt-get update' 

	ansible all -m apt -a update_cache=true

> ye hua nornam apt-get update pr ye work nhi karega kyun ki uske liye hume sudo lagna pdega tb hi work krega pr hum normal us system >  
> sudo apt-get update likhene toh work kerega but hum ansible se kese krvayenge 

	ansible all -m apt -a update_cache=true --become --ask-become-pass 

> hm jab bhi user mode me hota hai aur hume agar kuch install krna hai toh sudo apt-get install <package name> toh hume password 
> puchega jo bhi root ka password hoga toh bas issliye become-pass ka mtlb woh hi hai 

# How install package from Ansible Host in UBUNTU 

> name ke aage mene package name dala ki ye install krna hai muje fir ye install ho jayega 

	ansible all -m apt -a name=vim-nox --become --ask-become-pass 

# How install package from Ansible Host in CentOS

> centOS me apt nhi chlega usme yum chalta hai toh bas woh change krna padega aur haan usme sudo do yaaa naa do woh frk nhi pdega

	ansible all -m yum -a name=tmux

# Creating Play-Books

> muje ab maan lo apne saaare servers pr apache server install krna hai toh me kya krunga ek tarika hai server pr jaakr baari bari se sb > command likhu yum install 'pkg name' yaa fir usse ansible host me likhu kuch is tarike se jo me run kru toh sb me ek sath install ho 
> jaaye yes aesa mumkin hai playbook ke zariae 

Step : 1 - Create nano file & save .yml extenstion

	nano install_apache.yml

Step : 2 - usme niche wali sari line copy kr do 

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
	
	


