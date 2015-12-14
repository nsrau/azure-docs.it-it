<properties
	pageTitle="Preparare i file VHD Linux Debian | Microsoft Azure"
	description="Informazioni su come creare i file Debian VHD 7 e 8 per la distribuzione in Azure."
	services="virtual-machines"
	documentationCenter=""
	authors="SuperScottz"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="mingzhan"/>




#Preparare Debian VHD per Azure

##Prerequisiti
In questa sezione si presuppone che un sistema operativo Linux Debian sia già stato installato da un file .iso scaricato dal [sito Web di Debian](https://www.debian.org/distrib/) in un disco rigido virtuale. Sono disponibili vari strumenti per creare file con estensione .vhd; Hyper-V è solo un esempio. Per istruzioni sull’uso di Hyper-V, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](https://technet.microsoft.com/library/hh846766.aspx).


## Note sull'installazione

- Il formato VHDX più recente non è supportato in Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet **convert-vhd**.
- Durante l'installazione del sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, che spesso è la scelta predefinita per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le VM clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra VM per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare LVM o RAID.
- Non configurare una partizione swap nel disco del sistema operativo. L'agente Linux di Azure può essere configurato in modo da creare un file swap sul disco temporaneo delle risorse. Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.
- Tutti i dischi rigidi virtuali devono avere dimensioni multiple di 1 MB.


##Debian 7.x e 8. x

1. Nella console di gestione di Hyper-V selezionare la macchina virtuale.

2. Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.

3. Impostare come commento la riga per **deb cdrom** in `/etc/apt/source.list` se si configura la macchina virtuale rispetto a un file ISO.

4. Modificare il file `/etc/default/grub` e modificare il parametro **GRUB\_CMDLINE\_LINUX** come indicato di seguito per includere altri parametri del kernel per Azure.

        GRUB_CMDLINE_LINUX="console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

5. Ricompilare il grub ed eseguire:

        # sudo update-grub 

6. Installare i pacchetti di dipendenza per l’agente Linux di Azure:

        # apt-get install -y git parted

7.	Installare l'agente Linux di Azure da Github tramite [indicazioni](virtual-machines-linux-update-agent.md) e scegliere la versione 2.0.14:

			# wget https://raw.githubusercontent.com/Azure/WALinuxAgent/WALinuxAgent-2.0.14/waagent
			# chmod +x waagent
			# cp waagent /usr/sbin
			# /usr/sbin/waagent -install -verbose

8. Effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure ed eseguire:

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout
 
9. Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.

##Utilizzo di Credativ Script per creare Debian VHD

Sul sito Web Credativ che consente di compilare automaticamente il Debian VHD è disponibile uno script. È possibile scaricarlo da [qui](https://gitlab.credativ.com/de/azure-manage) e installarlo nella VM Linux. Per creare un Debian VHD (ad esempio Debian 7) eseguire:

        # azure_build_image --option release=wheezy --option image_prefix=lilidebian7 --option image_size_gb=30 SECTION

Se ci sono dei problemi per usare questo script, presentare semplicemente il problema a Credativ [qui](https://gitlab.credativ.com/groups/de/issues).

## Passaggi successivi

Ora si è pronti a usare il file con estensione vhd. di Debian per creare nuove macchine virtuali di Azure.

<!---HONumber=AcomDC_1203_2015-->