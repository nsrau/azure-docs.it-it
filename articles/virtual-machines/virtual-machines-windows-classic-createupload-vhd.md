<properties
	pageTitle="Creazione e caricamento di un disco rigido virtuale con Windows Server usando Powershell | Microsoft Azure"
	description="Viene illustrato come creare e caricare un disco rigido virtuale (VHD) basato su Server Windows utilizzando il modello di distribuzione classica e Azure Powershell."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor="tysonn"
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="cynthn"/>

# Creazione e caricamento di un disco rigido virtuale con Windows Server in Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.


Questo articolo illustra come caricare un disco rigido virtuale (VHD, Virtual Hard Disk) con un sistema operativo in modo da poterlo usare come immagine in base alla quale creare macchine virtuali. Per informazioni dettagliate sui dischi e sui dischi rigidi virtuali in Microsoft Azure, vedere [Informazioni sui dischi e sui dischi rigidi virtuali per le macchine virtuali](virtual-machines-linux-about-disks-vhds.md).



## Prerequisiti

Questo articolo presuppone che l'utente abbia:

1. **Una sottoscrizione di Microsoft Azure**: se non già disponibile è possibile [aprire un account Microsoft Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F). Si riceveranno dei crediti da usare per provare i servizi di Microsoft Azure a pagamento e, anche dopo avere esaurito i crediti, sarà possibile mantenere comunque l'account per usare i servizi di Microsoft Azure gratuiti, ad esempio Siti Web. Verranno applicati addebiti alla carta di credito solo se l'utente modifica le impostazioni e richiede esplicitamente l'addebito. È possibile inoltre [attivare i benefici della sottoscrizione MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): con la sottoscrizione MSDN ogni mese si accumulano crediti che è possibile usare per i servizi di Microsoft Azure a pagamento.

2. **Microsoft Azure PowerShell**: è necessario che il modulo di Microsoft Azure PowerShell sia installato e configurato per l’uso della sottoscrizione. Per scaricare il modulo, vedere la pagina dei [download di Microsoft Azure](https://azure.microsoft.com/downloads/). Un'esercitazione per installare e configurare in modulo è disponibile [qui](../powershell-install-configure.md). Per caricare il VHD, usare il cmdlet [Add-AzureVHD](http://msdn.microsoft.com/library/azure/dn495173.aspx) di Azure.

3. **Una versione supportata del sistema operativo Windows archiviata in un file con estensione vhd e collegata a una macchina virtuale**: sono disponibili più strumenti per la creazione di file con estensione vhd. Ad esempio, è possibile usare Hyper-V per creare una macchina virtuale e installare il sistema operativo. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx). Per informazioni dettagliate sui sistemi operativi, vedere [Supporto di software server Microsoft per le macchine virtuali di Microsoft Azure](http://go.microsoft.com/fwlink/p/?LinkId=393550).

> [AZURE.IMPORTANT] il formato VHDX non è supportato in Microsoft Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il [cmdlet Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). Per informazioni dettagliate, vedere questo [post di blog](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).

## Passaggio 1: Preparare il disco rigido virtuale 

Prima di caricare il disco rigido virtuale in Azure, è necessario generalizzarlo usando lo strumento Sysprep, che prepara il disco rigido virtuale in modo che possa essere usato come immagine. Per ulteriori informazioni su Sysprep, vedere [Introduzione: Introduzione](http://technet.microsoft.com/library/bb457073.aspx).

Dalla macchina virtuale su cui è stato installato il sistema operativo, completare la seguente procedura:

1. Accedere al sistema operativo.

2. Aprire una finestra del prompt dei comandi come amministratore. Impostare la directory su **%windir%\\system32\\sysprep**, quindi eseguire `sysprep.exe`.

	![Apertura della finestra del Prompt dei comandi](./media/virtual-machines-windows-classic-createupload-vhd/sysprep_commandprompt.png)

3.	Verrà visualizzata la finestra di dialogo **Utilità preparazione sistema**.

	![Avvio di Sysprep](./media/virtual-machines-windows-classic-createupload-vhd/sysprepgeneral.png)

4.  In **Utilità preparazione sistema** selezionare **Passare alla Configurazione guidata** e assicurarsi che l'opzione **Generalizza** sia selezionata.

5.  In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.

6.  Fare clic su **OK**.

## Passaggio 2: Creare oppure ottenere informazioni dall'account di archiviazione di Azure

È necessario un account di archiviazione di Azure in cui caricare il file con estensione vhd. Questo passaggio illustra come creare un account o come ottenere le informazioni necessarie da un account esistente.

### Opzione 1: Creare un account di archiviazione

1. Accedere al [portale di Azure classico](https://manage.windowsazure.com).

2. Sulla barra dei comandi fare clic su **New**.

3. Fare clic su **Servizi dati** > **Archiviazione** > **Creazione rapida**.

	![Creazione rapida di un account di archiviazione](./media/virtual-machines-windows-classic-createupload-vhd/Storage-quick-create.png)

4. Compilare i campi come indicato di seguito:

 - In **URL** digitare un nome di sottodominio da usare nell'URL per l'account di archiviazione. La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo nome diventa il nome host dell'URL usato per accedere alle risorse di tipo BLOB, code o tabelle per la sottoscrizione.
 - Selezionare la **posizione o il gruppo di affinità** per l'account di archiviazione. Un gruppo di affinità consente di collocare i servizi cloud e l'archiviazione nello stesso data center.
 - Scegliere se si desidera usare la **replica geografica** per l'account di archiviazione. La replica geografica è attivata per impostazione predefinita. Se questa opzione è selezionata, i dati vengono replicati in una posizione secondaria, senza alcun costo aggiuntivo, per garantire il failover a tale posizione qualora si verifichi un errore grave nella posizione primaria. La posizione secondaria viene assegnata automaticamente e non può essere modificata. Se è necessario un maggiore controllo sulla posizione dell'archiviazione basata sul cloud a causa di requisiti legali o criteri dell'organizzazione, è possibile disattivare la replica geografica. Se si attiva la replica geografica in un momento successivo, tuttavia, verrà addebitato un corrispettivo di trasferimento dati una tantum per la replica dei dati esistenti nella posizione secondaria. I servizi di archiviazione senza replica geografica sono offerti a un prezzo scontato. Per altre informazioni, vedere [Creare, gestire o eliminare un account di archiviazione](../storage-create-storage-account/#replication-options).

      ![Immissione dei dettagli dell'account di archiviazione](./media/virtual-machines-windows-classic-createupload-vhd/Storage-create-account.png)

5. Fare clic su **Create Storage Account**. L'account verrà visualizzato in **Archiviazione**.

	![Creazione dell'account di archiviazione completata](./media/virtual-machines-windows-classic-createupload-vhd/Storagenewaccount.png)

6. Creare quindi un contenitore per i VHD caricati. Fare clic sul nome account di archiviazione, quindi su **Contenitori**.

	![Dettaglio dell'account di archiviazione](./media/virtual-machines-windows-classic-createupload-vhd/storageaccount_detail.png)

7. Fare clic su **Crea contenitore**.

	![Dettaglio dell'account di archiviazione](./media/virtual-machines-windows-classic-createupload-vhd/storageaccount_container.png)

8. Digitare un valore nel campo **Nome** per il contenitore e selezionare i **Criteri di accesso**.

	![Nome del contenitore](./media/virtual-machines-windows-classic-createupload-vhd/storageaccount_containervalues.png)

	> [AZURE.NOTE] Per impostazione predefinita, il contenitore è privato ed è accessibile solo al proprietario dell'account. Per consentire l'accesso in lettura pubblico ai BLOB nel contenitore, ma non alle proprietà e ai metadati del contenitore, usare l'opzione **BLOB pubblico**. Per consentire l'accesso in lettura pubblico completo per contenitori e BLOB, usare l'opzione **Contenitore pubblico**.

### Opzione 2: Ottenere le informazioni sull'account di archiviazione

1.	Accedere al [portale di Azure classico](https://manage.windowsazure.com).

2.	Nel riquadro di spostamento fare clic su **Archiviazione**.

3.	Fare clic sul nome dell'account di archiviazione, quindi su **Dashboard**.

4.	In **Servizi** nel dashboard passare il mouse sull'URL dei BLOB, fare clic sull'icona relativa agli Appunti per copiare l'URL, quindi copiare e incollare l'URL. Verrà usato quando si crea il comando per il caricamento del disco rigido virtuale.

## Passaggio 3: Connettersi alla sottoscrizione da Azure PowerShell

Prima di poter caricare un file VHD, è necessario stabilire una connessione sicura tra il computer e la sottoscrizione in Azure. Per effettuare questa operazione è possibile usare il metodo basato su Microsoft Azure Active Directory o quello basato sul certificato.

> [AZURE.TIP] Per informazioni introduttive su Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). Per altre informazioni, vedere [Iniziare a usare i cmdlet di Microsoft Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

### Opzione 1: Usare Microsoft Azure AD

1. Aprire la console di Azure PowerShell.

2. Digitare: `Add-AzureAccount`

3.	Nella finestra di accesso immettere il nome utente e la password dell'account dell'azienda o dell'istituto di istruzione.

4. Le informazioni delle credenziali vengono autenticate e salvate in Azure, quindi la finestra viene chiusa.

### Opzione 2: Usare un certificato

1. Aprire la console di Azure PowerShell.

2.	Digitare: `Get-AzurePublishSettingsFile`.

3. Viene aperta una finestra del browser in cui viene chiesto di scaricare un file con estensione publishsettings, contenente informazioni e un certificato per la sottoscrizione Microsoft Azure.

	![Pagina di download del browser](./media/virtual-machines-windows-classic-createupload-vhd/Browser_download_GetPublishSettingsFile.png)

3. Salvare il file con estensione publishsettings.

4. Digitare: `Import-AzurePublishSettingsFile <PathToFile>`

	Dove `<PathToFile>` è il percorso completo del file .publishsettings.

## Passaggio 4: caricare il file VHD

È possibile caricare il file VHD in qualsiasi posizione all'interno dell'archiviazione BLOB.

1. Nella finestra di Azure PowerShell usata nel passaggio precedente, digitare un comando analogo al seguente:

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`

	Dove:
	- **BlobStorageURL** è l'URL dell'account di archiviazione.
	- **YourImagesFolder** è il contenitore all'interno dell'archivio BLOB in cui si vuole archiviare le immagini.
	- **VHDName** è il nome da visualizzare nel portale di Azure classico per identificare il disco rigido virtuale.
	- **PathToVHDFile** è il percorso completo e il nome del file VHD.

	![Add-AzureVHD di PowerShell](./media/virtual-machines-windows-classic-createupload-vhd/powershell_upload_vhd.png)

Per altre informazioni sul cmdlet Add-AzureVhd, vedere [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx).

## Passaggio 5: Aggiungere l'immagine all'elenco di immagini personalizzate

> [AZURE.TIP] Per usare Azure PowerShell invece del portale di Azure classico per aggiungere l'immagine, usare il cmdlet **Add-AzureVMImage**. Ad esempio:

>	`Add-AzureVMImage -ImageName <ImageName> -MediaLocation <VHDLocation> -OS <OSType>`

1. Nel portale di Azure classico, in **Tutti gli elementi**, fare clic su **Macchine virtuali**.

2. In Macchine virtuali fare clic su **Immagini**.

3. Fare clic su **Crea immagine**.

	![Add-AzureVHD di PowerShell](./media/virtual-machines-windows-classic-createupload-vhd/Create_Image.png)

4. Nella finestra **Crea immagine da VHD**:

	- Specificare il **nome**.

	- Specificare la **descrizione**.

	- In **URL VHD** fare clic sull'icona della cartella per aprire la finestra **Sfoglia archiviazione cloud**. Individuare il file con estensione vhd, quindi fare clic su **Apri**.

    ![Selezione del VHD](./media/virtual-machines-windows-classic-createupload-vhd/Select_VHD.png)

5.	Nella finestra **Crea immagine da VHD** selezionare il sistema operativo in **Famiglia di sistemi operativi**. Selezionare **Sysprep è stato eseguito sulla macchina virtuale associata a questo VHD** per verificare che il sistema operativo sia stato generalizzato, quindi fare clic su **OK**.

    ![Aggiunta di un'immagine](./media/virtual-machines-windows-classic-createupload-vhd/Create_Image_From_VHD.png)

6. Una volta completati i passaggi precedenti, quando si seleziona la scheda **Immagini** la nuova immagine risulta inclusa nell'elenco.

	![immagine personalizzata](./media/virtual-machines-windows-classic-createupload-vhd/vm_custom_image.png)

	La nuova immagine è ora disponibile in **Immagini personali** quando si crea una macchina virtuale. Per istruzioni, vedere l'articolo relativo alla [creazione di una macchina virtuale personalizzata](virtual-machines-windows-classic-createportal.md).

	![creazione di una macchina virtuale in base a un'immagine personalizzata](./media/virtual-machines-windows-classic-createupload-vhd/create_vm_custom_image.png)

	> [AZURE.TIP] Se si verifica un errore quando si tenta di creare una macchina virtuale e viene visualizzato il messaggio "Le dimensioni virtuali del VHD https://XXXXX.. pari a YYYY byte non sono supportate. La dimensione deve essere un numero intero (espresso in MB)", significa che il disco rigido virtuale non corrisponde a un numero intero di MB. Deve essere un VHD di dimensioni fisse. Provare a usare il cmdlet PowerShell **Add-AzureVMImage** anziché il portale di Azure classico per aggiungere l'immagine (vedere il passaggio 5 precedente). I cmdlet di Azure garantiscono che il VHD soddisfi i requisiti di Azure.



[Step 1: Prepare the image to be uploaded]: #prepimage
[Step 2: Create a storage account in Azure]: #createstorage
[Step 3: Prepare the connection to Azure]: #prepAzure
[Step 4: Upload the .vhd file]: #upload

<!---HONumber=AcomDC_0323_2016-->