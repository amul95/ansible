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

# README.md Pull frome Git

> me jese hi git clone use karunga mere github me jo meri banayi hui repository hogi woh mere local linux me store hogi toh mene kyaa kie pehle ji github me 
> Readme.md file create kia example ke liye taaki jab me local pr repository add kru toh pata chle ki kyaa readme file bhi aa gyi mere local pr agar aa gyi
> that means ki github se sahi se work kr rha hai ab me khud kuch changes krunga Readme file me aur woh changes automatic honge github pr 
	
nano README.md
		
	# Hello this is my first repository on GitHub
	
> fir me save krunga Readme wali file uske baad mene jo bhi changes kie muje woh mere github pr chahiye isliye me usse ab push krunga locak se github pr 
	
	git add README.md 

> jis file me humne changes kia us file ka name use krna hai git add commad ke aage

	git commit -m "updated readme file"

> fir hume commit krna hai uspr ki commit krna hai kuch uspr comment de do ki taaki dhyaan rhe 

	git push origin main

> aur last me hume git push krna hai taaki woh finally github pr pahuche 

# Create Inventory File 

> me ab inventory file create kr rha hoon jisme mere saare server ke IP address store krunga taaki me jab chahu tb use use kr sku 

nano inventory

	0.0.0.0 	# jo bhi server ip hai store krke uske samne comment kr do taaki sb clear rhe like this
	0.0.0.0 	# CentOS Server
	0.0.0.0 	# Ubuntu Server
	0.0.0.0		# CentOS server

> ab me save kr doonga aur fir usse hum git me push kr denge taaki woh store ho jaye apne github pr 
	
	git add inventory
	git commit -m "create inventor file"
	git push origin main
	
> Fir me us inventory file ko check krunga ki ping command se taaki jitne bhi uske ander IP hai un sb ko ek saath ping jaayega 		
	
	ansible all --key-file ~/.ssh/ansible -i inventory -m ping

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
	
	ansible all -m apt -a name=tmux --become --ask-become-pass 
	
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
	
# Targeting Different Servers
 - Humne abhi tak package install kie sare servers me chahe woh ubuntu ho ya cent os par humne jab bhi playbook run ki everytime sb server me woh run hua
 - ab mere pass total 4 servers hai as u know 2 centOS aur 2 ubuntu pr me playbook run kru aur woh sirf 2 server pr hi work kre aur baaki ke bache 2 server ko pata
 - pata bhi naa lge toh woh kese hoga aaie chlte hai 

> Step:1 - Update inventory file look like this 
	
	[web_servers]	# mee ynha specify kia ki ye ye dono server ke ip web server ke hai 
	10.177.0.3		
	10.177.0.6

	[ftp_servers]	# mee ynha specify kia ki ye ye dono server ke ip ftp server ke hai 
	10.177.0.5 
	10.177.0.4 

	[db_servers]	# mene ynha specify kia 1 ip woh db_servers ka hai 
	10.177.0.6
	
> Step:2 - Create Playbook for this operation
	
	find targeting_servers.yml file in my github repository

 - aap playbook dekhoge toh pata chalega ki mene hosts me jab pehle all hum ikha krte the toh ab mene ftp_server yaa db_server
 - specify kia hai toh uske baad jo bhi operations honge woh sirf un specific servers ke liye honge like mene agar hosts me 
 - web_servers use kia aur fir uske niche men koi bhi package install yaa uninstall ka playbook banaya toh woh sirf web_server wale
 - me ip store kie hai ussi me woh laagu hoga baki woh use nhi hoga
 
 # Learn_Tags 
 
 	https://notebook.zohopublic.in/public/notes/inx8106e505f6b4e44dc6bdb9b12292f28548

# Learning grep command

	cat /etc/ssh/sshd_config | wc -l  

-- text file me total kitni lines hai coding ki hui woh dikhayega yaa kitni lines me kuch na kuch likha hua hai

	cat /etc/ssh/sshd_config | grep Permit

-- is txt file me jo kahin Permit likha hua hoga woh saara kuch output me aayega


	 cat /etc/ssh/sshd_config | grep Port 
-- is txt file me jo kahin Port likha hua hoga woh saara kuch output me aayega


	grep Port /etc/ssh/sshd_config 

-- isse bhi same output milega par aapko agar aese yaad rhe toh aese hi sahi aap likh skte ho 


Example : Ab mene ek text file create ki hai jisme kuch aesa likha hai mene
          taki aur asani ho grep command ko smjne me toh shuru krte hai.
	  
	┌─[amul@Hacker]─[~/Desktop]
	└──╼ $cat characters.txt 

	Name:Akashy Kumar  Occupation:Bollywood Actor   Movie:Sooryavanshi
	Name:Batman        Occupation:Hollywood Actor   Movie:Dark Knight
	Name:Tony Stark    Occupation:Hollywood Actor   Movie:Iron Man
	Name:Yash          Occupation:South Actor       Movie:Kgf
	Name:Donatello     Occupation:Court Mage        Species:Mutant Turtle
	Name:Kefka         Occupation:Cute Pink Puff    Species:Human
	Name:Kirby         Occupation:Ninja             Species:Kirby
	Name:Leonardo      Occupation:Ninja             Species:Mutant Turtle
	Name:Michelangelo  Occupation:Ninja             Species:Mutant Turtle
	Name:Raphael       Occupation:Super Villian     Species:Mutant Turtle
	Name:Riddler       Occupation:Bounty Hunger     Species:Human
	Name:Samus         Occupation:Hero              Species:Human
	Name:Terra         Occupation:Hero              Species:Esper

Grep command ko kese hum advanced tarike se use kr skte hai dekhte hai...

	┌─[amul@Hacker]─[~/Desktop]
   	└──╼ $grep Movie characters.txt 
	
	Name:Akashy Kumar  Occupation:Bollywood Actor   Movie:Sooryavanshi
	Name:Batman        Occupation:Hollywood Actor   Movie:Dark Knight
	Name:Tony Stark    Occupation:Hollywood Actor   Movie:Iron Man
	Name:Yash          Occupation:South Actor       Movie:Kgf
	
	-- kyun ki isme dekho aap Movie likha hua isliye woh sari lines 
	   aa gyi jo bhi movie likha hua hai woh 

Mene " -v " mention karunga toh wo us keyword ke inculde na karke baaki saara 
kuch output dega...

	┌─[amul@Hacker]─[~/Desktop]
	└──╼ $grep -v Movie characters.txt 
	
	Name:Donatello     Occupation:Court Mage        Species:Mutant Turtle
	Name:Kefka         Occupation:Cute Pink Puff    Species:Human
	Name:Kirby         Occupation:Ninja             Species:Kirby
	Name:Leonardo      Occupation:Ninja             Species:Mutant Turtle
	Name:Michelangelo  Occupation:Ninja             Species:Mutant Turtle
	Name:Raphael       Occupation:Super Villian     Species:Mutant Turtle
	Name:Riddler       Occupation:Bounty Hunger     Species:Human
	Name:Samus         Occupation:Hero              Species:Human
	Name:Terra         Occupation:Hero              Species:Esper

	-- kyun ki humne -v mention kia hai toh Movie word wali jo lines thi 		usse chodkr saari lines hume show hogi aap dekh skt ho Movie word 	     aur woh puri lines aapko nhi show ho rhi hogi output me 

Mene " -n " mention kia n means number hum output me line numbers bhi show honge...as u see below 

	┌─[amul@Hacker]─[~/Desktop]
	└──╼ $grep -n Movie characters.txt 
	3:Name:Akashy Kumar  Occupation:Bollywood Actor   Movie:Sooryavanshi
	4:Name:Batman        Occupation:Hollywood Actor   Movie:Dark Knight
	5:Name:Tony Stark    Occupation:Hollywood Actor   Movie:Iron Man
	6:Name:Yash          Occupation:South Actor       Movie:Kgf

Ab me mention karunga " -c " usse hume us perticular keyword kitni line me aata hai uska COUNT milega...

	┌─[✗]─[amul@Hacker]─[~/Desktop]
	└──╼ $grep -c Movie characters.txt 
	4
	
Ab me mention karunga "-i" usse yhi hoga maan lo me jo search kr rha hoon woh mne small me likha but same woh chiz agar capital me hui toh agar -i use nhi krunga toh woh miss ho jayega isliye me chahta hoon ki muje woh dikhe 

	
	-- mene sirf akshay likha toh kuch bhi find nhi hua balki ander file            me akshay kumar tha 
	
	┌─[amul@Hacker]─[~/Desktop]
	└──╼ $grep akshay characters.txt 
	
	Par jese hi mene -i use kia toh usne toh dekho hume output mil gya
	
	┌─[amul@Hacker]─[~/Desktop]
	└──╼ $grep -i akshay characters.txt 
	Name:Akshay Kumar  Occupation:Bollywood Actor   Movie:Sooryavanshi


#  The echo command

	
	┌─[amul@Hacker]─[~]
	└──╼ $echo "Amul Raval"
	Amul Raval
	# jo hum likhenge woh print hoga
	
	┌─[amul@Hacker]─[~]
	└──╼ $echo -e "Amul Raval\c"
	# "-c" aur uske saath "\c" use krunga
	
	Amul Raval┌─[amul@Hacker]─[~]
	# upr dekh skte ho aesa ho jayega bash style humara
	
	┌─[amul@Hacker]─[~]
	└──╼ $echo -e "Amul \nRaval"
	Amul 
	Raval
	# me agar "\n" use krunga toh next line me bhi add hoga
	
	┌─[amul@Hacker]─[~]
	└──╼ $abc="bhai bhai che"
	# variable bana do aur usme store kr do jo print karvana hai
	
	┌─[amul@Hacker]─[~]
	└──╼ $echo $abc
	bhai bhai che
	# ab echo use kro "$" symbol use krke variable name likho
	
# Bash History 

-- hm history sirf ye dekhne ke liye krte hai ki humne recent me kyaa kya command dia hai woh dekhne ke liye aur haan ap jab history command use kroge toh aapko pata chalgea ki command kon kon se hai aur command ke aage line numbers diye honge me yna line numbers isliye define kr rha hoon taaki apko same command wapis dena hai toh aap copy paste kr lo history me se yaa fir line number 35 hai toh aap " !35 " likh denge aur enter kr loge toh bhi woh hi command exceute hoga jo us line number pr hai...

# Swap Memory 

--> Unused RAM is Wasted RAM 

What is SWAP-Memory : thoda tricky hai but hum aasani se smj lenge ki ye 
swap hai kyaa chlo maan lo mere laptop me 4Gb ram installed hai ab me mere 
laptop me kuch bhi kaam krunga toh ram consum hoga woh apne cache me sb store
krega pr mera laptop kaa kaam itna badh jaaye ki muje 4 gb ram bhi kum padegi 
ab maan lo meri ram ki kuch capacity hai uske above me apan sb kaam krunga 
process run krunga us pr load padega ek time aaywga ki ram out of controll ho jayeegi aur sara laptop mera crash yaa freezze ab jab tm me sb close krunga uske baad ram free hoga then sahi se work krega pr ye toh mera single laptoop hai but server me aesa hua toh kyaa karenge sb bandh company thode smy ke liye ruk jaayegi isliye swap ne janm lia ki jab humne jo install ki hai ram 4 gb yaa 8 gb jo size ki agar woh full ho jati hai toh uska ek emergenccy option hi swap memmory jo humare hard disk ke storage ke through hum use krte hai pr jo perfomance hume ram se mileta ha cache memory se thik wesa hume swap memory se nhi milega isliye woh end moment pr hume bacha le isliye hume create krke rkh dena hota hai


	sudo nano /etc/sysctl.conf
	# ynha last me jao wnha vm.swappiness lika hoga uske samne aap
	   1-100 tk digit daal do 4GB ram hai toh mene 10 dala ha uska mtlb 
	   ye hua ki jab mer 4gb ram 80% use hogi tbb woh swap kr legi meri              storage wali ram ko 


# WGET

-- me online se koi bhi software ya file download krta hoon par muje agar teminal se krna hai toh me wget command ka use krke kar skta hoon jese ki men wordpres ki site pr gya wnha software donwload krna tha muje toh me agar sidha woh link par copy krunga jisse woh download ho jayega par me us link par right click krunga toh toh woh download wali link copy kr loonga aur fir usse terminal se wget ke aage use kr loonga toh woh download ho jayega jese mere click krne se hota ab wget me aur bhi advanced hai woh ye hai ki me download ke saath saath pehle se name change kar doon fir woh mere laptop ussi name se download hoga ap niche dekh skte ho

Using -o command for rename downloaded file 

	┌──[amul@Hacker]─[~]
	└──╼ $ wget -o wp.zip https://wordpress.org/latest.zip
	┌─[amul@Hacker]─[~]
	└──╼ $ls
	ansible   Documents       latest.zip  Pictures  swap.txt   Test
	code.txt  Downloads       log.txt     Public    Sync       Videos
	Desktop   gnome-terminal  Music       snap      Templates  wp.zip
	
Using -P command for path 
--muje path bhi dena ho toh me de skta hoon ki muje kaha file download krni hai kis folder me 

	┌─[amul@Hacker]─[~]
	└──╼ $ wget -P /home/amul/Downloads https://wordpress.org/latest.zip
	
	--2022-07-23 18:19:38--  https://wordpress.org/latest.zip
	Resolving wordpress.org (wordpress.org)... 198.143.164.252
	Connecting to wordpress.org (wordpress.org)|198.143.164.252|:443... 	    connected.
	HTTP request sent, awaiting response... 200 OK
	Length: 22771633 (22M) [application/zip]
	Saving to: ‘/home/amul/Downloads/latest.zip’

	latest.zip           100%[=====================>]  21.72M  2.95MB/s    	 	in 9.9s    

	2022-07-23 18:19:49 (2.20 MB/s) - ‘/home/amul/Downloads/latest.zip’ 		saved [22771633/22771633]

	┌─[amul@Hacker]─[~]
	└──╼ $cd /home/amul/Downloads/
	┌─[amul@Hacker]─[~/Downloads]
	└──╼ $ls
	'acknowledgementSlip (1).pdf'
	'acknowledgementSlip (2).pdf'
	'acknowledgementSlip (3).pdf'
	'acknowledgementSlip (4).pdf'
	'acknowledgementSlip (5).pdf'
	 acknowledgementSlip.pdf
	'Ahmedabad to Mumbai.pdf'
	 Dracula.zip
	 EAadhaar_0000001052642120220710115839_17072022133453.pdf
	 EAadhaar_0000005497740920220718073552_2207202217246.pdf
	 google-chrome.tar.gz
	 gtk-master.zip
	
	 invoice-8a129cc381fc10a501820c263e4e7032.pdf
	
	 latest.zip
	
	'Mumbai to Ahmedabad.pdf'
	'parrot terminal theme'
	 wallpaper-master
	 wallpaper-master.zip
	'WhatsApp Image 2022-07-17 at 1.46.23 PM.jpeg'
	
-- Dekha aapne mene path dia ki user amul ke Download folder me ho aur fir wnha check kia toh " latest.zip " kar ke aapko wnha dikh rha hoga 




	

	





