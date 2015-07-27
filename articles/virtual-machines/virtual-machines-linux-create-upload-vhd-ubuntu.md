<properties 
	pageTitle="Creazione e caricamento di un VHD Ubuntu Linux in Azure" 
	description="Informazioni su come creare e caricare un disco rigido virtuale (VHD) di Azure che contiene un sistema operativo Ubuntu Linux." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="szarkos" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/15/2015" 
	ms.author="szarkos"/>


# Preparare una macchina virtuale Ubuntu per Azure

##Prerequisiti##

In questo articolo si presuppone che l'utente abbia già installato un sistema operativo Ubuntu Linux in un disco rigido virtuale. Sono disponibili vari strumenti per creare file con estensione vhd, ad esempio una soluzione di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).

**Note sull'installazione di Ubuntu**

- Il formato VHDX più recente non è supportato in Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet convert-vhd.

- Durante l'installazione del sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, che spesso è la scelta predefinita per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le VM clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra VM per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare LVM o [RAID](virtual-machines-linux-configure-raid.md).

- Non configurare una partizione swap nel disco del sistema operativo. L'agente Linux può essere configurato in modo da creare un file swap sul disco temporaneo delle risorse. Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.

- Tutti i dischi rigidi virtuali devono avere dimensioni multiple di 1 MB.


## <a id="ubuntu"> </a>Ubuntu 12.04+ ##

1. Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.

2. Fare clic su **Connect** per aprire la finestra della macchina virtuale.

3.	Sostituire gli archivi correnti nell'immagine in modo da usare quelli Azure di Ubuntu. La procedura varia leggermente in base alla versione di Ubuntu.

	Prima di modificare /etc/apt/sources.list, è consigliabile eseguire un backup.

		# sudo cp /etc/apt/sources.list /etc/apt/sources.list.bak

	Ubuntu 12.04:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-add-repository 'http://archive.canonical.com/ubuntu precise-backports main'
		# sudo apt-get update

	Ubuntu 12.10:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-add-repository 'http://archive.canonical.com/ubuntu quantal-backports main'
		# sudo apt-get update

	Ubuntu 14.04+:

		# sudo sed -i "s/[a-z][a-z].archive.ubuntu.com/azure.archive.ubuntu.com/g" /etc/apt/sources.list
		# sudo apt-get update

4. Aggiornare il sistema operativo al kernel più recente eseguendo i comandi seguenti:

	Ubuntu 12.04:

		# sudo apt-get update
		# sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-precise-virtual
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot

	Ubuntu 12.10:

		# sudo apt-get update
		# sudo apt-get install hv-kvp-daemon-init linux-backports-modules-hv-quantal-virtual
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot
	
	Ubuntu 14.04+:

		# sudo apt-get update
		# sudo apt-get install hv-kvp-daemon-init
		(recommended) sudo apt-get dist-upgrade

		# sudo reboot

5.	(Facoltativo) Se il sistema Ubuntu rileva un errore e viene riavviato, attenderà al prompt di avvio grub l'input utente che impedisce l'avvio corretto del sistema. Per evitare questa situazione, completare i passaggi seguenti:

	a) Aprire il file /etc/grub.d/00_header.

	b) Nella funzione **make_timeout()** cercare **if ["\\${recordfail}" = 1 ]; then**

	c) Modificare l'istruzione sotto questa riga in **set timeout=5**.

	d) Eseguire 'sudo update-grub'.

6. Modificare la riga di avvio del kernel per Grub in modo da includere ulteriori parametri del kernel per Azure. A questo scopo, aprire "/etc/default/grub" in un editor di testo, individuare la variabile `GRUB_CMDLINE_LINUX_DEFAULT` (o aggiungerla, se necessario) e modificarla per includere i parametri seguenti:

		GRUB_CMDLINE_LINUX_DEFAULT="console=tty1 console=ttyS0 earlyprintk=ttyS0 rootdelay=300"

	Salvare e chiudere il file e quindi eseguire '`sudo update-grub`'. In questo modo si garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto tecnico di Azure nella risoluzione dei problemi di debug.

8.	Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questo è in genere il valore predefinito.

9.	Installare l'agente Linux di Azure:

		# sudo apt-get update
		# sudo apt-get install walinuxagent

	Si noti che installando il pacchetto `walinuxagent`, i pacchetti `NetworkManager` e `NetworkManager-gnome` eventualmente installati verranno rimossi.

10.	Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

11. Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.


 

<!---HONumber=July15_HO3-->