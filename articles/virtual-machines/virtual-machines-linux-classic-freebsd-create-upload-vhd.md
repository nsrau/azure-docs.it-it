<properties
   pageTitle="Creare e caricare un'immagine della macchina virtuale FreeBSD | Microsoft Azure"
   description="Informazioni su come creare e caricare un disco rigido virtuale (VHD) che contiene il sistema operativo FreeBSD per creare una macchina virtuale di Azure."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/29/2016"
   ms.author="kyliel"/>

# Creare e caricare un disco rigido virtuale con FreeBSD in Azure

Questo articolo descrive come creare e caricare un disco rigido virtuale (VHD) che contiene il sistema operativo FreeBSD. Dopo il caricamento, è possibile usarlo come immagine personalizzata per creare una macchina virtuale in Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


## Prerequisiti
In questo articolo si presuppone che l'utente disponga degli elementi seguenti:

- **Una sottoscrizione Azure**: se non è già disponibile un account, è possibile crearne uno in pochi minuti. Se si ha una sottoscrizione MSDN, vedere [Crediti mensili di Azure per sottoscrittori di Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/). Altrimenti, vedere [Crea subito il tuo account Azure gratuito](https://azure.microsoft.com/pricing/free-trial/).

- **Strumenti di Azure PowerShell**: è necessario che il modulo Microsoft Azure PowerShell sia installato e configurato per usare la sottoscrizione di Azure. Per scaricare il modulo, vedere la pagina dei [download di Azure](https://azure.microsoft.com/downloads/). Un'esercitazione per installare e configurare in modulo è disponibile qui. Per caricare il disco rigido virtuale usare il cmdlet nella pagina [Download di Azure](https://azure.microsoft.com/downloads/).

- **Sistema operativo FreeBSD installato in un file VHD**: è necessario installare un sistema operativo FreeBSD supportato in un disco rigido virtuale. Sono disponibili vari strumenti per la creazione di file VHD. Ad esempio, per creare il file VHD e installare il sistema operativo, è possibile usare soluzioni di virtualizzazione come Hyper-V. Per istruzioni, vedere su come installare e usare Hyper-V, vedere [Installare Hyper-V e creare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] Il formato VHDX più recente non è supportato in Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet [convert-vhd](https://technet.microsoft.com/library/hh848454.aspx). È disponibile anche un'[esercitazione in MSDN sull'uso di FreeBSD con Hyper-V](http://blogs.msdn.com/b/kylie/archive/2014/12/25/running-freebsd-on-hyper-v.aspx).

Questa attività include i cinque passaggi illustrati di seguito.

## Passaggio 1: preparare l'immagine da caricare

Dalla macchina virtuale in cui è stato installato il sistema operativo FreeBSD, completare le procedure seguenti:

1. Abilitare DHCP.

		# echo 'ifconfig_hn0="SYNCDHCP"' >> /etc/rc.conf
		# service netif restart

2. Abilitare SSH.

    SSH è abilitato per impostazione predefinita dopo l'installazione dal disco. Se non lo è per qualche motivo o se si usa direttamente un VHD FreeBSD, digitare quanto segue:

		# echo 'sshd_enable="YES"' >> /etc/rc.conf
		# ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key
		# ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key
		# service sshd restart

3. Configurare la console seriale.

		# echo 'console="comconsole vidconsole"' >> /boot/loader.conf
		# echo 'comconsole_speed="115200"' >> /boot/loader.conf

4. Installare sudo.

    L'account radice è disabilitato in Azure. Ciò significa che per eseguire comandi con privilegi elevati da un account utente senza privilegi è necessario usare sudo.

		# pkg install sudo
;
5. Prerequisiti per l'agente di Azure.

		# pkg install python27  
		# pkg install Py27-setuptools27   
		# ln -s /usr/local/bin/python2.7 /usr/bin/python   
		# pkg install git

6. Installare l'agente di Azure.

    L'ultima versione dell'agente di Azure è sempre disponibile in [github](https://github.com/Azure/WALinuxAgent/releases). La versione 2.0.10 + supporta ufficialmente FreeBSD 10 e 10.1 e la versione 2.1.4 supporta ufficialmente FreeBSD 10.2 e versioni successive.

		# git clone https://github.com/Azure/WALinuxAgent.git  
		# cd WALinuxAgent  
		# git tag  
		…
		WALinuxAgent-2.0.16
		…
		v2.1.4
		v2.1.4.rc0
		v2.1.4.rc1

    Per la versione 2.0 verrà usata come esempio la versione 2.0.16:

		# git checkout WALinuxAgent-2.0.16
		# python setup.py install  
		# ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  

    Per la versione 2.1 verrà usata come esempio la versione 2.1.4:

		# git checkout v2.1.4
		# python setup.py install  
		# ln -sf /usr/local/sbin/waagent /usr/sbin/waagent  
		# ln -sf /usr/local/sbin/waagent2.0 /usr/sbin/waagent2.0

    >[AZURE.IMPORTANT] Dopo aver installato l'agente di Azure, è consigliabile verificare che sia in esecuzione:

		# waagent -version
		WALinuxAgent-2.1.4 running on freebsd 10.3
		Python: 2.7.11
		# service –e | grep waagent
		/etc/rc.d/waagent
		# cat /var/log/waagent.log

7. Eseguire il deprovisioning del sistema.

    Eseguire il deprovisioning del sistema per pulire il sistema e renderlo idoneo per un nuovo provisioning. Il comando seguente elimina anche l'ultimo account utente di cui è stato effettuato il provisioning e i dati associati:

		# echo "y" |  /usr/local/sbin/waagent -deprovision+user  
		# echo  'waagent_enable="YES"' >> /etc/rc.conf

    Ora è possibile arrestare la macchina virtuale.

## Passaggio 2: creare un account di archiviazione in Microsoft Azure ##

È necessario avere un account di archiviazione di Azure per caricare un file VHD da usare per la creazione di una macchina virtuale. Per creare un account di archiviazione, è possibile utilizzare il portale classico di Microsoft Azure.

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com).

2. Sulla barra dei comandi selezionare **Nuovo**.

3. Fare clic su **Servizi dati** > **Archiviazione** > **Creazione rapida**.

	![Creazione rapida di un account di archiviazione](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-quick-create.png)

4. Compilare i campi come indicato di seguito:

	- In **URL** digitare il nome di un sottodominio da usare nell'URL dell'account di archiviazione. La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo nome diventa il nome host all'interno dell'URL utilizzato per fare riferimento alle risorse di archiviazione BLOB di Azure, archiviazione code di Azure o archiviazione tabelle di Azure per la sottoscrizione.

	- Nel menu a discesa **Località/gruppo di affinità** scegliere la **località o il gruppo di affinità** per l'account di archiviazione. Un gruppo di affinità consente di inserire i servizi cloud e le risorse di archiviazione nello stesso data center.

	- Nel campo **Replica** decidere se usare la replica **Con ridondanza geografica** per l'account di archiviazione. La replica geografica è attivata per impostazione predefinita. Se questa opzione è selezionata, i dati vengono replicati in una posizione secondaria, senza alcun costo aggiuntivo, per garantire il failover a tale posizione qualora si verifichi un errore grave nella posizione primaria. La località secondaria viene assegnata automaticamente e non può essere modificata. Se è necessario un maggiore controllo sulla posizione dell'archiviazione basata sul cloud a causa di requisiti legali o criteri dell'organizzazione, è possibile disattivare la replica geografica. Tenere presente che se si attiva la replica geografica in un momento successivo, verrà addebitato un corrispettivo di trasferimento dati una tantum per la replica dei dati esistenti nella posizione secondaria. I servizi di archiviazione senza replica geografica sono offerti a un prezzo scontato. Altri dettagli sulla gestione della replica geografica degli account di archiviazione sono disponibili nell'articolo [Creare, gestire o eliminare un account di archiviazione](../storage-create-storage-account/#replication-options).

	![Immissione dei dettagli dell'account di archiviazione](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storage-create-account.png)


5. Fai clic su **Crea account di archiviazione**. L'account verrà visualizzato in **Archiviazione**.

	![Creazione dell'account di archiviazione completata](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Storagenewaccount.png)

6. Creare quindi un contenitore per i VHD caricati. Selezionare il nome dell'account di archiviazione e quindi **Contenitori**.

	![Dettaglio dell'account di archiviazione](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_detail.png)

7. Selezionare **Crea contenitore**.

	![Dettaglio dell'account di archiviazione](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_container.png)

8. Nel campo **Nome** digitare un nome per il contenitore. Quindi nel menu a discesa **Accesso** selezionare il tipo di criteri di accesso desiderato.

	![Nome del contenitore](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/storageaccount_containervalues.png)

    > [AZURE.NOTE] Per impostazione predefinita, il contenitore è privato ed è accessibile solo al proprietario dell'account. Per consentire l'accesso pubblico in lettura ai BLOB nel contenitore, ma non alle proprietà e ai metadati del contenitore, usare l'opzione **BLOB pubblico**. Per consentire l'accesso in lettura pubblico completo per contenitori e BLOB, usare l'opzione **Contenitore pubblico**.

## Passaggio 3: preparare la connessione a Microsoft Azure

Prima di poter caricare un file VHD, è necessario stabilire una connessione sicura tra il computer e la sottoscrizione di Azure. Per eseguire questa operazione è possibile usare il metodo basato su Azure Active Directory (Azure AD) o sul certificato.

### Usare il metodo Azure AD per caricare un file VHD

1. Aprire la console di Azure PowerShell.

2. Digitare il comando seguente:`Add-AzureAccount`

	Questo comando consente di aprire una finestra in cui è possibile eseguire l'accesso con l'account aziendale o dell'istituto di istruzione.

	![Finestra di PowerShell](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/add_azureaccount.png)

3. Le informazioni delle credenziali vengono autenticate e salvate in Azure, quindi la finestra viene chiusa.

### Usare il metodo basato sul certificato per caricare un file VHD

1. Aprire la console di Azure PowerShell.

2. Digitare: `Get-AzurePublishSettingsFile`.

3. Viene aperta una finestra del browser in cui viene chiesto di scaricare un file con estensione publishsettings, che contiene informazioni e un certificato per la sottoscrizione di Azure.

	![Pagina di download del browser](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/Browser_download_GetPublishSettingsFile.png)

3. Salvare il file con estensione publishsettings.

4. Digitare `Import-AzurePublishSettingsFile <PathToFile>`, dove `<PathToFile>` è il percorso completo del file con estensione publishsettings.

   Per altre informazioni, vedere [Iniziare a usare i cmdlet di Azure](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx).

   Per altre informazioni sull'installazione e la configurazione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md).

## Passaggio 4: caricare il file VHD

È possibile caricare il file VHD in qualsiasi posizione all'interno dell'archivio BLOB. Di seguito sono riportati alcuni termini che saranno quando si carica il file:
-  **BlobStorageURL** è l'URL dell'account di archiviazione creato nel passaggio 2.
-  **YourImagesFolder** è il contenitore all'interno dell'archivio BLOB in cui si vogliono archiviare le immagini.
- **VHDName** è l'etichetta che identifica il disco rigido virtuale visualizzata nel portale di Azure classico.
- **PathToVHDFile** è il percorso completo e il nome del file VHD.


Nella finestra di Azure PowerShell usata nel passaggio precedente, digitare:

		Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>

## Passaggio 5: Creare una macchina virtuale con il file VHD caricato
Dopo avere caricato il file VHD, è possibile aggiungerlo come immagine all'elenco di immagini personalizzate associate alla propria sottoscrizione e creare una macchina virtuale con questa immagine personalizzata.

1. Nella finestra di Azure PowerShell usata nel passaggio precedente, digitare:

		Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>

    > [AZURE.NOTE]Usare Linux come tipo di sistema operativo. La versione corrente di Azure PowerShell accetta solo "Linux" o "Windows" come parametro.

2. Una volta completati i passaggi precedenti, quando si seleziona la scheda **Immagini** nel portale di Azure classico, la nuova immagine risulta inclusa nell'elenco.

    ![Scegli un'immagine](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/addfreebsdimage.png)

3. Creare una macchina virtuale dalla raccolta. La nuova immagine è ora disponibile in **Immagini personali**.
4. Selezionare la nuova immagine. Seguire quindi le istruzioni visualizzate per configurare nome host, password/chiave SSH e così via.

	![Immagine personalizzata](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/createfreebsdimageinazure.png)

4. Dopo avere completato il provisioning, la macchina virtuale FreeBSD in esecuzione sarà visibile in Azure.

	![immagine di FreeBSD in Azure](./media/virtual-machines-linux-classic-freebsd-create-upload-vhd/freebsdimageinazure.png)

<!---HONumber=AcomDC_0831_2016-->