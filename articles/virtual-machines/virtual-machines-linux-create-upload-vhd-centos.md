<properties 
	pageTitle="Creare e caricare un VHD Linux basato su CentOS in Azure" 
	description="Come creare e caricare un disco rigido virtuale Azure (VHD) che contiene un sistema operativo Linux basato su CentOS." 
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

# Preparare una macchina virtuale basata su CentOS per Azure

- [Preparare una macchina virtuale CentOS 6.x per Azure](#centos6)
- [Preparare una macchina virtuale CentOS 7.0+ per Azure](#centos7)

##Prerequisiti##

In questo articolo si presuppone che l'utente abbia già installato un sistema operativo Linux CentOS (o un sistema derivato simile) in un disco rigido virtuale. Sono disponibili vari strumenti per creare file con estensione vhd, ad esempio una soluzione di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).


**Note sull'installazione di CentOS**

- Il formato VHDX più recente non è supportato in Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet convert-vhd.

- Durante l'installazione del sistema operativo Linux è consigliabile usare partizioni standard anziché LVM, che spesso è la scelta predefinita per numerose installazioni. In questo modo sarà possibile evitare conflitti di nome LVM con le VM clonate, in particolare se fosse necessario collegare un disco del sistema operativo a un'altra VM per la risoluzione dei problemi. Se si preferisce, su dischi di dati si può usare LVM o [RAID](virtual-machines-linux-configure-raid.md).

- NUMA non è supportato per VM di dimensioni maggiori a causa di un bug presente nelle versioni del kernel di Linux inferiori a 2.6.37. Questo problema incide principalmente sulle distribuzioni che usano il kernel upstream Red Hat 2.6.32. L'installazione manuale dell'agente Linux di Azure (waagent) disabiliterà automaticamente NUMA nella configurazione GRUB per il kernel Linux. Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.

- Non configurare una partizione swap nel disco del sistema operativo. L'agente Linux può essere configurato in modo da creare un file swap sul disco temporaneo delle risorse. Altre informazioni su questo argomento sono disponibili nei passaggi seguenti.

- Tutti i dischi rigidi virtuali devono avere dimensioni multiple di 1 MB.


## <a id="centos6"></a>CentOS 6.x ##

1. Nella console di gestione di Hyper-V selezionare la macchina virtuale.

2. Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.

3. Disinstallare NetworkManager attivando il seguente comando:

		# sudo rpm -e --nodeps NetworkManager

	**Nota:** se il pacchetto non è già installato, questo comando avrà esito negativo e verrà visualizzato un messaggio di errore. Si tratta di un comportamento previsto.

4.	Creare un file denominato **network** nella directory `/etc/sysconfig/` contenente il testo seguente:

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

5.	Creare un file denominato **ifcfg-eth0** nella directory `/etc/sysconfig/network-scripts/` contenente il testo seguente:

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
		PEERDNS=yes
		IPV6INIT=no

6.	Spostare o eliminare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti provocano problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V:

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/


7. Assicurarsi che il servizio di rete venga eseguito all'avvio eseguendo il comando seguente:

		# sudo chkconfig network on


8. **Solo CentOS 6.3**: installare i driver per Linux Integration Services

	**Importante: questo passaggio è valido solo per CentOS 6.3 e versioni precedenti.** In CentOS 6.4+ Linux Integration Services è *già disponibile nel kernel*.

	a) Scaricare il file ISO contenente i driver per Linux Integration Services dall'[Area download Microsoft](http://www.microsoft.com/download/details.aspx?id=41554).

	b) Nella console di gestione di Hyper-V, nel riquadro **Azioni** fare clic su **Impostazioni**.

	![Apertura impostazioni di Hyper-V](./media/virtual-machines-linux-create-upload-vhd-centos/settings.png)

	c) Nel riquadro **Hardware** fare clic su **Controller IDE 1**.

	![Aggiunta unità DVD con supporti di installazione](./media/virtual-machines-linux-create-upload-vhd-centos/installiso.png)

	d) Nella casella **Controller IDE** fare clic su **Unità DVD**, quindi fare clic su **Aggiungi**.

	e) Selezionare **File immagine**, passare a **Linux IC v3.2.iso** e quindi fare clic su **Apri**.

	f) Nella pagina **Impostazioni** fare clic su **OK**.

	g) Fare clic su **Connetti** per aprire la finestra della macchina virtuale.

	h) Nella finestra del prompt dei comandi digitare i comandi seguenti:

		# sudo mount /dev/cdrom /media
		# sudo /media/install.sh
		# sudo reboot

9. Installare il pacchetto python-pyasn1 eseguendo il comando seguente:

		# sudo yum install python-pyasn1

10. Se si vuole usare i mirror OpenLogic ospitati nei data center di Azure, sostituire il file /etc/yum.repos.d/CentOS-Base.repo con i seguenti repository. Verrà aggiunto anche il repository **[openlogic]** che include i pacchetti per l'agente Linux di Azure:

		[openlogic]
		name=CentOS-$releasever - openlogic packages for $basearch
		baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
		enabled=1
		gpgcheck=0
		
		[base]
		name=CentOS-$releasever - Base
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#released updates
		[updates]
		name=CentOS-$releasever - Updates
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#additional packages that may be useful
		[extras]
		name=CentOS-$releasever - Extras
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#additional packages that extend functionality of existing packages
		[centosplus]
		name=CentOS-$releasever - Plus
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6
		
		#contrib - packages by Centos Users
		[contrib]
		name=CentOS-$releasever - Contrib
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-6

	**Nota**: nella parte restante di questa guida si presuppone che l'utente usi almeno il repository [openlogic], che verrà usato per installare l'agente Linux di Azure illustrato di seguito.


11.	Aggiungere la seguente riga a /etc/yum.conf:

		http_caching=packages

	Solo in CentOS 6.3** aggiungere la riga seguente:**

		exclude=kernel*

12. Disabilitare il modulo yum "fastestmirror" modificando il file "/etc/yum/pluginconf.d/fastestmirror.conf" e in `[main]` digitare quanto segue:

		set enabled=0

13.	Eseguire il comando seguente per cancellare i metadati yum correnti:

		# yum clean all

14. **Solo in CentOS 6.3**, aggiornare il kernel usando il comando seguente:

		# sudo yum --disableexcludes=all install kernel

15.	Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A questo scopo, aprire "/boot/grub/menu.lst" in un editor di testo e verificare che il kernel predefinito includa i parametri seguenti:

		console=ttyS0 earlyprintk=ttyS0 rootdelay=300 numa=off

	In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Verrà disabilitato NUMA, a causa di un bug nella versione del kernel usata da CentOS 6.

	Inoltre, è consigliabile *rimuovere* i parametri seguenti:

		rhgb quiet crashkernel=auto

	L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale.

	L'opzione `crashkernel` può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB, il che potrebbe causare problemi con le macchine virtuali di dimensioni inferiori.


16.	Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questo è in genere il valore predefinito.

17. Installare l'agente Linux di Azure eseguendo il comando seguente:

		# sudo yum install WALinuxAgent

	Si noti che, installando il pacchetto WALinuxAgent, i pacchetti NetworkManager e NetworkManager-gnome verranno rimossi, se l'operazione non è già stata eseguita come descritto nel passaggio 2.

18.	Non creare l'area di swap sul disco del sistema operativo.

	L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco *temporaneo* e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

19.	Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

20. Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.


----------


## <a id="centos7"> </a>CentOS 7.0+ ##

**Modifiche in CentOS 7 (e sistemi derivati simili)**

La preparazione di una macchina virtuale CentOS 7 per Azure è molto simile a CentOS 6, tuttavia vi sono alcune importanti differenze da notare:

 - Il pacchetto NetworkManager e l'agente Linux di Azure non sono più in conflitto. Questo pacchetto viene installato per impostazione predefinita ed è consigliabile non rimuoverlo.
 - GRUB2 viene ora usato come bootloader predefinito, quindi la procedura per la modifica dei parametri kernel è cambiata (vedere di seguito).
 - XFS è ora il file system predefinito. Se si vuole, è ancora possibile usare il file system ext4.


**Procedura di configurazione**

1. Nella console di gestione di Hyper-V selezionare la macchina virtuale.

2. Fare clic su **Connetti** per aprire una finestra della console per la macchina virtuale.

3.	Creare un file denominato **network** nella directory `/etc/sysconfig/` contenente il testo seguente:

		NETWORKING=yes
		HOSTNAME=localhost.localdomain

4.	Creare un file denominato **ifcfg-eth0** nella directory `/etc/sysconfig/network-scripts/` contenente il testo seguente:

		DEVICE=eth0
		ONBOOT=yes
		BOOTPROTO=dhcp
		TYPE=Ethernet
		USERCTL=no
		PEERDNS=yes
		IPV6INIT=no

5.	Spostare o eliminare le regole udev per evitare la generazione di regole statiche per l'interfaccia Ethernet. Le regole seguenti provocano problemi quando si clona una macchina virtuale in Microsoft Azure o Hyper-V:

		# sudo mkdir -m 0700 /var/lib/waagent
		# sudo mv /lib/udev/rules.d/75-persistent-net-generator.rules /var/lib/waagent/ 2>/dev/null
		# sudo mv /etc/udev/rules.d/70-persistent-net.rules /var/lib/waagent/ 2>/dev/null

6. Assicurarsi che il servizio di rete venga eseguito all'avvio eseguendo il comando seguente:

		# sudo chkconfig network on

7. Installare il pacchetto python-pyasn1 eseguendo il comando seguente:

		# sudo yum install python-pyasn1

8. Se si vuole usare i mirror OpenLogic ospitati nei data center di Azure, sostituire il file /etc/yum.repos.d/CentOS-Base.repo con i seguenti repository. Verrà aggiunto anche il repository **[openlogic]** che include i pacchetti per l'agente Linux di Azure:

		[openlogic]
		name=CentOS-$releasever - openlogic packages for $basearch
		baseurl=http://olcentgbl.trafficmanager.net/openlogic/$releasever/openlogic/$basearch/
		enabled=1
		gpgcheck=0
		
		[base]
		name=CentOS-$releasever - Base
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/os/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		
		#released updates
		[updates]
		name=CentOS-$releasever - Updates
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/updates/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		
		#additional packages that may be useful
		[extras]
		name=CentOS-$releasever - Extras
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/extras/$basearch/
		gpgcheck=1
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		
		#additional packages that extend functionality of existing packages
		[centosplus]
		name=CentOS-$releasever - Plus
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/centosplus/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		
		#contrib - packages by Centos Users
		[contrib]
		name=CentOS-$releasever - Contrib
		baseurl=http://olcentgbl.trafficmanager.net/centos/$releasever/contrib/$basearch/
		gpgcheck=1
		enabled=0
		gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-CentOS-7
		


	**Nota**: nella parte restante di questa guida si presuppone che l'utente usi almeno il repository [openlogic], che verrà usato per installare l'agente Linux di Azure illustrato di seguito.

9.	Eseguire il comando seguente per cancellare i metadati yum correnti e installare eventuali aggiornamenti:

		# sudo yum clean all
		# sudo yum -y update

10.	Modificare la riga di avvio del kernel nella configurazione GRUB per includere ulteriori parametri del kernel per Azure. A tale scopo, aprire "/etc/default/grub" in un editor di testo e modificare il parametro `GRUB_CMDLINE_LINUX`, ad esempio:

		GRUB_CMDLINE_LINUX="rootdelay=300 console=ttyS0 earlyprintk=ttyS0"

	In questo modo si garantisce inoltre che tutti i messaggi della console vengano inviati alla prima porta seriale, agevolando così il supporto di Azure nella risoluzione dei problemi di debug. Inoltre, è consigliabile *rimuovere* i parametri seguenti:

		rhgb quiet crashkernel=auto

	L'avvio grafico e l'avvio silenzioso non sono utili in un ambiente cloud se tutti i log devono essere inviati alla porta seriale.

	L'opzione `crashkernel` può essere configurata, ma si tenga presente che questo parametro ridurrà la quantità di memoria disponibile nella macchina virtuale di almeno 128 MB, il che potrebbe causare problemi con le macchine virtuali di dimensioni inferiori.

11. Dopo aver terminato di modificare "/etc/default/grub" come sopra illustrato, eseguire il comando seguente per ricompilare la configurazione GRUB:

		# sudo grub2-mkconfig -o /boot/grub2/grub.cfg

12.	Verificare che il server SSH sia installato e configurato per l'esecuzione all'avvio. Questo è in genere il valore predefinito.

13. Installare l'agente Linux di Azure eseguendo il comando seguente:

		# sudo yum install WALinuxAgent

14.	Non creare l'area di swap sul disco del sistema operativo.

	L'agente Linux di Azure può configurare automaticamente l'area di swap utilizzando il disco risorse locale collegato alla VM dopo il provisioning in Azure. Si noti che il disco risorse locale è un disco *temporaneo* e potrebbe essere svuotato in seguito al deprovisioning della macchina virtuale. Dopo aver installato l'agente Linux di Azure come illustrato nel passaggio precedente, modificare i parametri seguenti in /etc/waagent.conf in modo appropriato:

		ResourceDisk.Format=y
		ResourceDisk.Filesystem=ext4
		ResourceDisk.MountPoint=/mnt/resource
		ResourceDisk.EnableSwap=y
		ResourceDisk.SwapSizeMB=2048    ## NOTE: set this to whatever you need it to be.

15.	Eseguire i comandi seguenti per effettuare il deprovisioning della macchina virtuale e prepararla per il provisioning in Azure:

		# sudo waagent -force -deprovision
		# export HISTSIZE=0
		# logout

16. Fare clic su **Azione -> Arresta** nella console di gestione di Hyper-V. Il file VHD Linux è ora pronto per il caricamento in Azure.


 

<!---HONumber=July15_HO4-->