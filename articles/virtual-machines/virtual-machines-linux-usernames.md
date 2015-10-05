<properties 
	pageTitle="Selezione dei nomi utente per Linux | Microsoft Azure" 
	description="Informazioni su come selezionare i nomi utente per una macchina virtuale Linux in Azure." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor=""
	tags="azure-service-management,azure-resource-manager" />

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2015" 
	ms.author="szark"/>



#Selezione dei nomi utente per Linux su Azure#

Durante il provisioning di una macchina virtuale Linux in Azure è necessario specificare il nome di un utente non radice, che in un secondo momento è possibile utilizzare per accedere alla macchina virtuale. È possibile scegliere il nome del nuovo utente o se il provisioning avviene tramite il portale di gestione è possibile accettare il nome predefinito "azureuser".

Nella maggior parte dei casi questo nuovo utente non esiste nell'immagine di base e viene creato durante il processo di provisioning. Se l'utente esiste nell'immagine della VM di base, l'agente Linux di Azure configura la password (e/o la chiave SSH) per tale utente in base alle informazioni specificate durante la creazione della VM.

**Tuttavia**, Linux definisce un set di nomi utente che non devono essere usati. Se si prova a eseguire il provisioning di una macchia virtuale Linux usando un utente di sistema esistente, definito come utente con un valore UID compreso tra 0 e 99, il processo avrà **esito negativo**. Un esempio tipico è rappresentato dall'utente `root`, il cui valore UID è 0.

 - Vedere anche: [Base standard di Linux - Intervalli ID utente](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

Di seguito sono riportati i nomi utente da non usare quando si esegue il provisioning di una macchina virtuale Linux. Si consiglia di **non usare questi nomi utente** per evitare che il processo di provisioning della macchina virtuale abbia esito negativo.


## openSUSE
- abrt
- adm
- audio
- bin
- bin
- cdrom
- cgred
- daemon
- dbus
- dialout
- dip
- disk
- floppy
- ftp
- games
- gopher
- haldaemon
- halt
- kmem
- lock
- lp
- mail
- man
- mem
- nfsnobody
- nobody
- ntp
- operator
- oprofile
- postdrop
- postfix
- qpidd
- root
- rpc
- rpcuser
- saslauth
- shutdown
- slocate
- sshd
- stapdev
- stapusr
- sync
- sys
- tape
- test
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel


## SLES
- audio
- bin
- cdrom
- console
- daemon
- dialout
- disk
- floppy
- ftp
- ftp
- games
- haldaemon
- kmem
- lp
- lp
- mail
- maildrop
- man
- messagebus
- modem
- news
- news
- nobody
- nogroup
- polkituser
- postfix
- public
- root
- shadow
- sshd
- sys
- test
- trusted
- tty
- users
- utmp
- uucp
- uuidd
- video
- wheel
- www
- wwwrun
- xok

 
## CentOS
- abrt
- adm
- audio
- bin
- cdrom
- cgred
- daemon
- dbus
- dialout
- dip
- disk
- floppy
- ftp
- ftp
- games
- gopher
- haldaemon
- halt
- kmem
- lock
- lp
- mail
- man
- mem
- nfsnobody
- nobody
- ntp
- operator
- oprofile
- postdrop
- postfix
- qpidd
- root
- rpc
- rpcuser
- saslauth
- shutdown
- slocate
- sshd
- stapdev
- stapusr
- sync
- sys
- tape
- test
- tcpdump
- tty
- users
- utempter
- utmp
- uucp
- vcsa
- video
- wheel


## Ubuntu
- adm
- admin
- audio
- backup
- bin
- cdrom
- crontab
- daemon
- dialout
- dip
- disk
- fax
- floppy
- fuse
- games
- gnats
- irc
- kmem
- landscape
- libuuid
- list
- lp
- mail
- man
- messagebus
- mlocate
- netdev
- news
- nobody
- nogroup
- operator
- plugdev
- proxy
- root
- sasl
- shadow
- src
- ssh
- sshd
- staff
- sudo
- sync
- sys
- syslog
- tape
- tty
- users
- utmp
- uucp
- video
- voice
- whoopsie
- www-data

 

<!---HONumber=Sept15_HO4-->