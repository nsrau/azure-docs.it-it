<properties 
	pageTitle="Creare e caricare un disco rigido virtuale con Windows Server in Azure" 
	description="Informazioni su come creare e caricare un disco rigido virtuale in Azure che contiene il sistema operativo Windows Server." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-windows" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/19/2015" 
	ms.author="kathydav"/>


#Creare e caricare un disco rigido virtuale con Windows Server in Azure#

Questo articolo illustra come caricare un disco rigido virtuale (VHD, Virtual Hard Disk) con un sistema operativo in modo da poterlo usare come immagine in base alla quale creare macchine virtuali. Per altre informazioni su dischi e immagini in Microsoft Azure, vedere l'argomento relativo alla [gestione di dischi e immagini in Azure](http://msdn.microsoft.com/library/windowsazure/jj672979.aspx).

> [AZURE.NOTE] Quando si crea una macchina virtuale basata su un'immagine, è possibile personalizzare le impostazioni del sistema operativo come appropriato per le applicazioni che si intende eseguire sulla macchina virtuale. Questa configurazione viene salvata per la macchina virtuale e non influisce sull'immagine. Per istruzioni, vedere [Come creare una macchina virtuale personalizzata](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/).

##Prerequisiti##
In questo articolo si presuppone che l'utente disponga degli elementi seguenti:

1. **Una sottoscrizione di Azure**: se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni, vedere [Creare un account Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/).  

2. **Microsoft Azure PowerShell** - è necessario che il modulo di Microsoft Azure PowerShell sia installato e configurato per usare la sottoscrizione. Per scaricare il modulo, vedere la pagina dei [download di Microsoft Azure](http://azure.microsoft.com/downloads/). Un'esercitazione per installare e configurare il moduloè disponibile [qui](http://azure.microsoft.com/documentation/articles/install-configure-powershell/). Si userà il cmdlet [Add-AzureVHD](http://msdn.microsoft.com/library/azure/dn495173.aspx) per caricare il disco rigido virtuale.

3. **Un sistema operativo Windows supportato archiviato in un file VHD**: l'utente deve avere installato un sistema operativo Windows Server supportato in un disco rigido virtuale. Sono disponibili vari strumenti per la creazione di file VHD. Per creare il file VHD e installare il sistema operativo, è possibile usare soluzioni di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE] il formato VHDX non è supportato in Microsoft Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il [cmdlet Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). È possibile trovare un'esercitazione relativa alla procedura di conversione in [questo articolo](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).
 
 Sono supportate le versioni di Windows Server seguenti:
<P>
  <TABLE BORDER="1" WIDTH="600">
  <TR BGCOLOR="#E9E7E7">
    <TH>OS</TH>
    <TH>SKU</TH>
    <TH>Service Pack</TH>
    <TH>Architettura</TH>
  </TR>
  <TR>
    <TD>Windows Server 2012 R2</TD>
    <TD>Tutte le edizioni</TD>
    <TD>N/D</TD>
    <TD>x64</TD>
  </TR>
  <TR>
    <TD>Windows Server 2012</TD>
    <TD>Tutte le edizioni</TD>
    <TD>N/D</TD>
    <TD>x64</TD>
  </TR>
  <TR>
    <TD>Windows Server 2008 R2</TD>
    <TD>Tutte le edizioni</TD>
    <TD>SP1</TD>
    <TD>x64</TD>
  </TR>
  </TABLE>
</P>


Questa attività include i passaggi seguenti:

- [Passaggio 1: Preparare l'immagine da caricare] []
- [Passaggio 2: Creare un account di archiviazione in Azure] []
- [Passaggio 3: Preparare la connessione ad Azure] []
- [Passaggio 4: Caricare il file VHD] []

## <a id="prepimage"> </a>Step 1: Preparare l'immagine da caricare ##

Prima di poter caricare l'immagine in Azure, è necessario generalizzarla tramite il comando Sysprep. Per altre informazioni sull'utilizzo di Sysprep, vedere [Introduzione all'uso di SysPrep](http://technet.microsoft.com/library/bb457073.aspx).

Dalla macchina virtuale su cui è stato installato il sistema operativo, completare la seguente procedura:

1. Accedere al sistema operativo.

2. Aprire una finestra del Prompt dei comandi come amministratore. Modificare la directory con **%windir%\system32\sysprep** e quindi eseguire `sysprep.exe`.

	![Open Command Prompt window](./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png)

3.	Verrà visualizzata la finestra di dialogo **Utilità preparazione sistema**.

	![Start Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  In Utilità preparazione sistema selezionare Passare alla Configurazione guidata e verificare che l'opzione Generalizza sia selezionata.

5.  In **Opzioni di arresto** del sistema selezionare **Arresta il sistema**.

6.  Fare clic su **OK**. 


## <a id="createstorage"> </a>Step 2: Creare un account di archiviazione in Azure ##

È necessario disporre di un account di archiviazione di Azure per caricare un file .vhd da usare in Azure per la creazione di una macchina virtuale. Per creare un account di archiviazione, è possibile usare il portale di gestione di Azure.

1. Accedere al portale di gestione di Azure.

2. Sulla barra dei comandi fare clic su **Nuovo**.

3. Fare clic su **Servizi dati** > **Archiviazione** > **Creazione rapida**.

	![Quick create a storage account](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. Compilare i campi come indicato di seguito:
	
	- In **URL** digitare un nome di sottodominio da usare nell'URL per l'account di archiviazione. La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo nome diventa il nome host all'interno dell'URL usato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione.
			
	- Selezionare la **posizione o il gruppo di affinità** per l'account di archiviazione. Un gruppo di affinità consente di collocare i servizi cloud e l'archiviazione nello stesso data center.
		 
	- Scegliere se si desidera usare la **replica geografica** per l'account di archiviazione. La replica geografica è attivata per impostazione predefinita. Se questa opzione è selezionata, i dati vengono replicati in una posizione secondaria, senza alcun costo aggiuntivo, per garantire il failover a tale posizione qualora si verifichi un errore grave nella posizione primaria. La posizione secondaria viene assegnata automaticamente e non può essere modificata. Se è necessario un maggiore controllo sulla posizione dell'archiviazione basata su cloud a causa di requisiti legali o criteri dell'organizzazione, è possibile disattivare la replica geografica. Tenere presente che se si attiva la replica geografica in un momento successivo, verrà addebitato un corrispettivo di trasferimento dati una tantum per la replica dei dati esistenti nella posizione secondaria. I servizi di archiviazione senza replica geografica sono offerti a un prezzo scontato. Altri dettagli sulla gestione della replica geografica degli account di archiviazione sono disponibili nell'articolo [Creare, gestire o eliminare un account di archiviazione](../storage-create-storage-account/#replication-options).

	![Enter storage account details](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)


5. Fare clic su **Crea account di archiviazione**. L'account verrà visualizzato in **Archiviazione**.

	![Storage account successfully created](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. Creare quindi un contenitore per i VHD caricati. Fare clic sul nome account di archiviazione, quindi su **Contenitori**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. Fare clic su **Crea contenitore**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. Digitare un **Nome** per il contenitore e selezionare il criterio di **Accesso**.

	![Container name](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

	> [AZURE.NOTE] Per impostazione predefinita, il contenitore è privato ed è accessibile solo al proprietario dell'account. Per consentire l'accesso in lettura pubblico ai BLOB nel contenitore, ma non alle proprietà e ai metadati del contenitore, usare l'opzione "BLOB pubblico". Per consentire l'accesso in lettura pubblico completo per contenitori e BLOB, usare l'opzione "Contenitore pubblico".

## <a id="PrepAzure"> </a>Step 3: Preparare la connessione a Microsoft Azure ##

Prima di poter caricare un file VHD, è necessario stabilire una connessione sicura tra il computer e la sottoscrizione in Azure. Per effettuare questa operazione è possibile usare il metodo basato su Microsoft Azure Active Directory o quello basato sul certificato.

<h3>Usare il metodo basato su Microsoft Azure AD</h3>

1. Aprire la console di Azure PowerShell come descritto in [Procedura: Install Microsoft Azure PowerShell](#Install).

2. Digitare il comando seguente:  
	`Add-AzureAccount`
	
	Questo comando consente di aprire una finestra in cui è possibile eseguire l'accesso con l'account di lavoro e scuola.

	![PowerShell Window](./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png)

3. Le informazioni delle credenziali vengono autenticate e salvate in Azure, quindi la finestra viene chiusa.

<h3>Usare il metodo basato sul certificato</h3> 

1. Aprire la console di Azure PowerShell. 

2.	Digitare: 
	`Get-AzurePublishSettingsFile`.


3. Viene aperta una finestra del browser in cui viene chiesto di scaricare un file .publishsettings, contenente informazioni e un certificato per la sottoscrizione di Microsoft Azure.

	![Browser download page](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. Salvare il file .publishsettings. 

4. Digitare: 
	`Import-AzurePublishSettingsFile <PathToFile>`

	Dove `<PathToFile>` è il percorso completo del file .publishsettings. 


	Per altre informazioni, vedere [Iniziare a usare i cmdlet di Microsoft Azure](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx). 
	
	Per altre informazioni sull'installazione e la configurazione di PowerShell, vedere [Come installare e configurare Microsoft Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/) 


## <a id="upload"> </a>Step 4: Caricare il file VHD ##

È possibile caricare il file VHD in qualsiasi posizione all'interno dell'archiviazione BLOB. Negli esempi di comandi seguenti, **BlobStorageURL** è l'URL per l'account di archiviazione creato nel passaggio 2 e **YourImagesFolder** è il contenitore all'interno dell'archiviazione BLOB in cui si desidera archiviare le immagini. **VHDName** è l'etichetta che identifica il disco rigido virtuale visualizzata nel portale di gestione. **PathToVHDFile** è il percorso completo e il nome del file VHD. 


1. Nella finestra di Azure PowerShell usata nel passaggio precedente, digitare:

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`
	
	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png)

	Per altre informazioni sul cmdlet Add-AzureVhd, vedere [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx).

##Passaggio 5: Aggiungere l'immagine all'elenco di immagini personalizzate ##
Dopo avere caricato il file VHD, aggiungerlo come immagine all'elenco di immagini personalizzate associate alla propria sottoscrizione.

1. Nel portale di gestione, in **Tutti gli elementi**, fare clic su **Macchine virtuali**.

2. In Macchine virtuali fare clic su **Immagini**.

3. Fare quindi clic su **Crea immagine**.

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png)

4. In **Crea immagine da VHD** effettuare le seguenti operazioni:
 	

	- Specificare un **nome**
	- Specificare una **descrizione**.
	- Per specificare l'**URL del VHD**, fare clic sul pulsante della cartella per aprire la seguente finestra di dialogo::
 
	![Select VHD](./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png)

	- Selezionare l'account di archiviazione in cui è definito il VHD e fare clic su **Apri**. Viene nuovamente visualizzata la finestra **Crea immagine da VHD**.
	- Nella finestra **Crea immagine da VHD** selezionare la famiglia di sistemi operativi.
	- Selezionare la casella **Sysprep è stato eseguito sulla macchina virtuale associata a questo VHD** per confermare di avere generalizzato il sistema operativo nel passaggio 1 e quindi fare clic su **OK**. 

	![Add Image](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png)

5. **FACOLTATIVO**: è possibile usare il cmdlet Add-AzureVMImage anziché il portale di gestione per aggiungere il VHD come immagine. Nella console di Azure PowerShell, digitare::

	`Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>`
	
	![PowerShell Add-AzureVMImage](./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png)

6. Una volta completati i passaggi precedenti, quando si seleziona la scheda **Immagini** la nuova immagine risulta inclusa nell'elenco. 


	![custom image](./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png)

	La nuova immagine è ora disponibile in **Immagini personali** quando si crea una macchina virtuale. Per istruzioni generali, vedere [Creare una macchina virtuale che esegue Windows Server](http://azure.microsoft.com/documentation/articles/virtual-machines-windows-tutorial/).

	![create VM from custom image](./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png)

	> [AZURE.TIP] Se si verifica un errore quando si tenta di creare una macchina virtuale, con un messaggio simile al seguente "Le dimensioni virtuali del VHD https://XXXXX... di YYYY byte non sono supportate. La dimensione deve essere un numero intero (espresso in MB)", significa che il disco rigido virtuale non corrisponde a un numero intero di MB. Deve essere un VHD di dimensioni fisse. Provare a usare il cmdlet Add-AzureVMImage PowerShell anziché il portale di gestione per aggiungere l'immagine (vedere il passaggio 5, sopra). I cmdlet di Azure garantiscono che il VHD soddisfi i requisiti di Azure.
	
## Passaggi successivi ##
 

Dopo aver creato una macchina virtuale, provare a creare una macchina virtuale di SQL Server. Per istruzioni, vedere [Provisioning di una macchina virtuale di SQL Server in Microsoft Azure](http://azure.microsoft.com/documentation/articles/virtual-machines-provision-sql-server/). 

[Passaggio 1: Preparare l'immagine da caricare]: #prepimage
[Passaggio 2: Creare un account di archiviazione in Azure]: #createstorage
[Passaggio 3: Preparare la connessione ad Azure]: #prepAzure
[Passaggio 4: Caricare il file VHD]: #upload




<!--HONumber=42-->
