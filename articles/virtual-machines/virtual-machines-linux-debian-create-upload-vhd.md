<properties
	pageTitle="Preparare i file VHD Linux Debian | Microsoft Azure"
	description="Informazioni su come creare i file Debian VHD 7 e 8 per la distribuzione in Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor=""
    tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/25/2016"
	ms.author="szark"/>




# Preparare un disco rigido virtuale Debian per Azure

## Prerequisiti
In questa sezione si presuppone che un sistema operativo Linux Debian sia già stato installato da un file .iso scaricato dal [sito Web di Debian](https://www.debian.org/distrib/) in un disco rigido virtuale. Sono disponibili vari strumenti per creare file con estensione .vhd; Hyper-V è solo un esempio. Per istruzioni sull’uso di Hyper-V, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](https://technet.microsoft.com/library/hh846766.aspx).


## Note sull'installazione

- Il formato VHDX più recente non è supportato in Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet **convert-vhd**.
- Durante l'installazione del sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, che spesso è la scelta predefinita per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le VM clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra VM per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare LVM o RAID.
- Non configurare una partizione swap nel disco del sistema operativo. L'agente Linux di Azure può essere configurato in modo da creare un file swap sul disco temporaneo delle risorse. Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.
- Tutti i dischi rigidi virtuali devono avere dimensioni multiple di 1 MB.


## Utilizzare Azure-Manage per creare dischi rigidi virtuali Debian

Sono disponibili strumenti per la generazione di dischi rigidi virtuali Debian per Azure, ad esempio gli script [azure-manage](https://gitlab.credativ.com/de/azure-manage) di [credativ](http://www.credativ.com/). Questo è l'approccio consigliato rispetto alla creazione di un'immagine da zero. Ad esempio, per creare un disco rigido virtuale Debian 8, eseguire i comandi seguenti per scaricare azure-manage (e le dipendenze) ed eseguire lo script azure\_build\_image:

	# sudo apt-get update
	# sudo apt-get install git qemu-utils mbr kpartx debootstrap

	# sudo apt-get install python3-pip
	# sudo pip3 install azure-storage azure-servicemanagement-legacy pytest pyyaml
	# git clone https://gitlab.credativ.com/de/azure-manage.git
	# cd azure-manage
	# sudo pip3 install .

	# sudo azure_build_image --option release=jessie --option image_size_gb=30 --option image_prefix=debian-jessie-azure section


## Preparare manualmente un disco rigido virtuale Debian

1. Nella console di gestione di Hyper-V selezionare la macchina virtuale.

2. Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.

3. Impostare come commento la riga per **deb cdrom** in `/etc/apt/source.list` se si configura la macchina virtuale rispetto a un file ISO.

4. Modificare il file `/etc/default/grub` e modificare il parametro **GRUB\_CMDLINE\_LINUX** come indicato di seguito per includere altri parametri del kernel per Azure.

        GRUB_CMDLINE_LINUX="console=tty0 console=ttyS0,115200 earlyprintk=ttyS0,115200 rootdelay=30"

5. Ricompilare il grub ed eseguire:

        # sudo update-grub

6. Aggiungere i repository Azure di Debian al file /etc/apt/sources.list per Debian 6 o 7:

	**Debian 6.x "Wheezy"**

		deb http://debian-archive.trafficmanager.net/debian wheezy-backports main
		deb-src http://debian-archive.trafficmanager.net/debian wheezy-backports main
		deb http://debian-archive.trafficmanager.net/debian-azure wheezy main
		deb-src http://debian-archive.trafficmanager.net/debian-azure wheezy main


	**Debian 7.x "Jessie"**

		deb http://debian-archive.trafficmanager.net/debian jessie-backports main
		deb-src http://debian-archive.trafficmanager.net/debian jessie-backports main
		deb http://debian-archive.trafficmanager.net/debian-azure jessie main
		deb-src http://debian-archive.trafficmanager.net/debian-azure jessie main


7. Installare l'agente Linux di Azure:

		# sudo apt-get update
		# sudo apt-get install waagent

8. Per Debian 7, è necessario eseguire il kernel basato su 3.16 dal repository wheezy-backports. Creare innanzitutto un file denominato /etc/apt/preferences.d/linux.pref con il seguente contenuto:

		Package: linux-image-amd64 initramfs-tools
		Pin: release n=wheezy-backports
		Pin-Priority: 500

	Quindi eseguire "sudo apt-get install linux-image-amd64" per l'installazione del nuovo kernel.

8. Effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure ed eseguire:

        # sudo waagent –force -deprovision
        # export HISTSIZE=0
        # logout

9. Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.


## Passaggi successivi

È ora possibile usare il disco rigido virtuale Debian per creare nuove macchine virtuali in Azure. Se è la prima volta che si carica il file VHD in Azure, vedere i passaggi 2 e 3 nell'articolo relativo a [creazione e caricamento di un disco rigido virtuale contenente il sistema operativo Linux](virtual-machines-linux-classic-create-upload-vhd.md).

<!---HONumber=AcomDC_0330_2016-->