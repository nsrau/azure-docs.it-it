---
title: Selezione dei nomi utente per Linux | Microsoft Docs
description: Informazioni su come selezionare i nomi utente per una macchina virtuale Linux in Azure.
services: virtual-machines-linux
documentationcenter: ''
author: szarkos
manager: timlt
editor: ''
tags: azure-service-management,azure-resource-manager

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/13/2016
ms.author: szark

---
# Selezione dei nomi utente per Linux su Azure
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Durante il provisioning di una macchina virtuale Linux in Azure è necessario specificare il nome di un utente non radice, che in un secondo momento è possibile utilizzare per accedere alla macchina virtuale. È possibile scegliere il nome del nuovo utente o se il provisioning avviene tramite il portale di Azure classico è possibile accettare il nome predefinito "azureuser".

Nella maggior parte dei casi questo nuovo utente non esiste nell'immagine di base e viene creato durante il processo di provisioning. Se l'utente esiste nell'immagine della VM di base, l'agente Linux di Azure configura la password (e/o la chiave SSH) per tale utente in base alle informazioni specificate durante la creazione della VM.

**Tuttavia**, Linux definisce un set di nomi utente che non devono essere usati. Se si prova a eseguire il provisioning di una macchia virtuale Linux usando un utente di sistema esistente, definito come utente con un valore UID compreso tra 0 e 99, il processo avrà **esito negativo**. Un esempio tipico è rappresentato dall'utente `root`, il cui valore UID è 0.

* Vedere anche: [Base standard di Linux - Intervalli ID utente](http://refspecs.linuxfoundation.org/LSB_4.1.0/LSB-Core-generic/LSB-Core-generic/uidrange.html)

L'elenco seguente include utenti di sistema predefiniti comuni per CentOS e Ubuntu che non devono essere usati quando si effettua il provisioning di una macchina virtuale Linux in Azure. Questo elenco è solo un esempio. Vedere la documentazione specifica della distribuzione per assicurarsi che il nome utente scelto non sia in conflitto con un utente di sistema esistente.

## CentOS
* abrt
* adm
* audio
* bin
* cdrom
* cgred
* daemon
* dbus
* dialout
* dip
* disk
* floppy
* ftp
* ftp
* games
* gopher
* haldaemon
* halt
* kmem
* lock
* lp
* mail
* man
* mem
* nfsnobody
* nobody
* ntp
* operator
* oprofile
* postdrop
* postfix
* qpidd
* root
* rpc
* rpcuser
* saslauth
* shutdown
* slocate
* sshd
* stapdev
* stapusr
* sync
* sys
* tape
* test
* tcpdump
* tty
* users
* utempter
* utmp
* uucp
* vcsa
* video
* wheel

## Ubuntu
* adm
* admin
* audio
* backup
* bin
* cdrom
* crontab
* daemon
* dialout
* dip
* disk
* fax
* floppy
* fuse
* games
* gnats
* irc
* kmem
* landscape
* libuuid
* list
* lp
* mail
* man
* messagebus
* mlocate
* netdev
* news
* nobody
* nogroup
* operator
* plugdev
* proxy
* root
* sasl
* shadow
* src
* ssh
* sshd
* staff
* sudo
* sync
* sys
* syslog
* tape
* tty
* users
* utmp
* uucp
* video
* voice
* whoopsie
* www-data

<!---HONumber=AcomDC_0713_2016-->