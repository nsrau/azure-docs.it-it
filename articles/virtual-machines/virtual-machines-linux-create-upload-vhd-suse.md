<properties
	pageTitle="Creazione e caricamento di un VHD SUSE Linux in Azure"
	description="Informazioni su come creare e caricare un disco rigido virtuale (VHD) di Azure che contiene un sistema operativo SUSE Linux."
	services="virtual-machines"
	documentationCenter=""
	authors="szarkos"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager,azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/15/2015"
	ms.author="szarkos"/>

# Preparare una macchina virtuale SLES od openSUSE per Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]

- [Preparare una macchina virtuale SLES 11 SP3 per Azure](#sles11)
- [Preparare una macchina virtuale openSUSE 13.1+ per Azure](#osuse)

##Prerequisiti##

In questo articolo si presuppone che l'utente abbia già installato un sistema operativo Linux SUSE od openSUSE in un disco rigido virtuale. Sono disponibili vari strumenti per creare file con estensione vhd, ad esempio una soluzione di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).


**Note di installazione di SLES/openSUSE**

 - [SUSE Studio](http://www.susestudio.com) consente di creare e gestire facilmente le immagini SLES/openSUSE per Azure e Hyper-V. Questo è l'approccio consigliato per personalizzare le proprie immagini SUSE e openSUSE. Le seguenti immagini ufficiali nella SUSE Studio Gallery possono essere scaricate o clonate nella propria installazione di SUSE Studio:

  - [SLES 11 SP3 per Azure nella raccolta di SUSE Studio](http://susestudio.com/a/02kbT4/sles-11-sp3-for-windows-azure)
  - [openSUSE 13.1 per Azure nella raccolta di SUSE Studio](https://susestudio.com/a/02kbT4/opensuse-13-1-for-windows-azure)

- Il formato VHDX più recente non è supportato in Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet convert-vhd.

- Durante l'installazione del sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, che spesso è la scelta predefinita per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le VM clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra VM per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare LVM o [RAID](virtual-machines-linux-configure-raid.md).

- Non configurare una partizione swap nel disco del sistema operativo. L'agente Linux può essere configurato in modo da creare un file swap sul disco temporaneo delle risorse. Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.

- Tutti i dischi rigidi virtuali devono avere dimensioni multiple di 1 MB.


## <a id="sles11"> </a>Preparare SUSE Linux Enterprise Server 11 SP3 ##

1. Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.

2. Fare clic su **Connect** per aprire la finestra della macchina virtuale.

3. Registrare il sistema SUSE Linux Enterprise per scaricare gli aggiornamenti e installare i pacchetti.

4. Aggiornare il sistema con tutte le patch più recenti:

		# sudo zypper update

5. Installare l'agente Linux di Azure dal repository SLES:

		# sudo zypper install WALinuxAgent

6. Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire "/boot/grub/menu.lst" in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	In questo modo si garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug.

7.	Si consiglia di modificare il file "/etc/sysconfig/network/dhcp" e modificare il parametro `DHCLIENT_SET_HOSTNAME` come segue:

		DHCLIENT_SET_HOSTNAME="no"

8.	In "/etc/sudoers" rimuovere o impostare come commento le righe seguenti, se esistenti:

		Defaults targetpw   # ask for the password of the target user i.e. root
		ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.	Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questo è in genere il valore predefinito.

10.	Non creare l'area di swap sul disco del sistema operativo.

	L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco *temporaneo* e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11.	Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

12. Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.


----------

## <a id="osuse"> </a> Preparare openSUSE 13.1+ ##

1. Nel riquadro centrale della console di gestione di Hyper-V selezionare la macchina virtuale.

2. Fare clic su **Connetti** per aprire la finestra della macchina virtuale.

3. Nella shell eseguire il comando '`zypper lr`'. Se questo comando restituisce un output simile al seguente (si noti che i numeri di versione possono variare):

		# | Alias                 | Name                  | Enabled | Refresh
		--+-----------------------+-----------------------+---------+--------
		1 | Cloud:Tools_13.1      | Cloud:Tools_13.1      | Yes     | Yes
		2 | openSUSE_13.1_OSS     | openSUSE_13.1_OSS     | Yes     | Yes
		3 | openSUSE_13.1_Updates | openSUSE_13.1_Updates | Yes     | Yes

	gli archivi sono configurati correttamente e non sono necessarie correzioni.

	Se il comando restituisce un messaggio simile a "Nessun archivio definito..." usare i comandi seguenti per aggiungere gli archivi:

		# sudo zypper ar -f http://download.opensuse.org/repositories/Cloud:Tools/openSUSE_13.1 Cloud:Tools_13.1
		# sudo zypper ar -f http://download.opensuse.org/distribution/13.1/repo/oss openSUSE_13.1_OSS
		# sudo zypper ar -f http://download.opensuse.org/update/13.1 openSUSE_13.1_Updates

	Sarà quindi possibile verificare che i repository siano stati aggiunti eseguendo nuovamente il comando '`zypper lr`' . Se uno degli archivi di aggiornamento pertinenti non è abilitato, abilitarlo con il comando seguente:

		# sudo zypper mr -e [NUMBER OF REPOSITORY]


4. Aggiornare il kernel all'ultima versione disponibile:

		# sudo zypper up kernel-default

	Oppure per aggiornare il sistema con tutte le patch più recenti:

		# sudo zypper update

5.	Installare l'agente Linux di Azure

		# sudo zypper install WALinuxAgent

6.	Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire "/boot/grub/menu.lst" in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300

	In questo modo si garantisce che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Rimuovere inoltre i messaggi seguenti dalla riga di avvio del kernel, se presenti:

		libata.atapi_enabled=0 reserve=0x1f0,0x8

7.	Si consiglia di modificare il file "/etc/sysconfig/network/dhcp" e modificare il parametro `DHCLIENT_SET_HOSTNAME` come segue:

		DHCLIENT_SET_HOSTNAME="no"

8.	**Importante**: in "/etc/sudoers" rimuovere o impostare come commento le righe seguenti, se esistenti:

		Defaults targetpw   # ask for the password of the target user i.e. root
		ALL    ALL=(ALL) ALL   # WARNING! Only use this together with 'Defaults targetpw'!

9.	Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questo è in genere il valore predefinito.

10.	Non creare l'area di swap sul disco del sistema operativo.

	L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco *temporaneo* e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

11.	Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

12. Verificare che l'agente Linux di Azure venga eseguito all'avvio:

		# sudo systemctl enable waagent.service

13. Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.

<!---HONumber=Sept15_HO4-->