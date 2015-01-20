<properties urlDisplayName="Upload a VHD" pageTitle="Creare e caricare un disco rigido virtuale con Windows Server in Azure" metaKeywords="Azure VHD, uploading VHD" description="Informazioni su come creare e caricare un disco rigido virtuale in Azure che contiene il sistema operativo Windows Server." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create and upload a Windows Server VHD to Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/23/2014" ms.author="kathydav" />


#Creare e caricare un disco rigido virtuale con Windows Server in Azure#

Questo articolo illustra come caricare un disco rigido virtuale (VHD, Virtual Hard Disk) con un sistema operativo in modo da poterlo usare come immagine in base alla quale creare macchine virtuali. Per altre informazioni su dischi e immagini in Microsoft Azure, vedere l'argomento relativo alla [gestione di dischi e immagini in Azure](http://msdn.microsoft.com/it-it/library/windowsazure/jj672979.aspx).

**Nota**: quando si crea una macchina virtuale, è possibile personalizzare le impostazioni del sistema operativo per facilitare l'esecuzione dell'applicazione. La configurazione impostata viene archiviata nel disco per quella macchina virtuale. Per istruzioni, vedere [Come creare una macchina virtuale personalizzata](http://www.windowsazure.com/it-it/documentation/articles/virtual-machines-windows-tutorial/).

##Prerequisiti##
In questo articolo si presuppone che l'utente disponga degli elementi seguenti:

**Una sottoscrizione di Azure**: se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere [Creare un account Azure](http://www.windowsazure.com/it-it/develop/php/tutorials/create-a-windows-azure-account/).  

**Microsoft Azure PowerShell**: il modulo Microsoft Azure PowerShell è installato. Per scaricare il modulo, vedere la pagina dei [download di Microsoft Azure](http://www.windowsazure.com/it-it/downloads/). In [questo articolo](http://www.windowsazure.com/it-it/documentation/articles/install-configure-powershell/) è disponibile un'esercitazione relativa all'installazione e alla configurazione di PowerShell con una sottoscrizione di Azure.

- Il cmdlet [Add-AzureVHD](http://msdn.microsoft.com/it-it/library/windowsazure/dn205185.aspx), incluso nel modulo Microsoft Azure PowerShell. Questo cmdlet verrà usato per caricare il VHD.

**Un sistema operativo Windows supportato archiviato in un file VHD**: l'utente deve avere installato un sistema operativo Windows Server supportato in un disco rigido virtuale. Sono disponibili vari strumenti per la creazione di file VHD. Per creare il file VHD e installare il sistema operativo, è possibile usare soluzioni di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](http://technet.microsoft.com/it-it/library/hh846766.aspx).

**Importante**: il formato VHDX non è supportato in Microsoft Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il [cmdlet Convert-VHD](http://technet.microsoft.com/it-it/library/hh848454.aspx). È possibile trovare un'esercitazione relativa alla procedura di conversione in [questo articolo](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).
 
**Supporto del sistema operativo Windows Server.** Questa attività richiede un file con estensione iso contenente il sistema operativo Windows Server. Sono supportate le versioni di Windows Server seguenti:
<P>
  <TABLE BORDER="1" WIDTH="600">
  <TR BGCOLOR="#E9E7E7">
    <TH>SO</TH>
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

## <a id="prepimage"> </a>Passaggio 1: Preparare l'immagine da caricare ##


Prima di poter caricare l'immagine in Azure, è necessario generalizzarla tramite il comando Sysprep. Per altre informazioni sull'uso di Sysprep, vedere [Introduzione all'uso di SysPrep](http://technet.microsoft.com/it-it/library/bb457073.aspx).

Nella macchina virtuale appena creata, completare la procedura seguente:

1. Accedere al sistema operativo.

2. Aprire una finestra del Prompt dei comandi come amministratore. Impostare la directory su **%windir%\system32\sysprep**, quindi eseguire `sysprep.exe`.

	![Open Command Prompt window](./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png)

3.	Verrà visualizzata la finestra di dialogo **Utilità preparazione sistema**.

	![Start Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  In **Utilità preparazione sistema** selezionare **Passare alla Configurazione guidata** e verificare che l'opzione Generalizza sia selezionata.

5.  In **Opzioni di arresto** del sistema selezionare **Arresta il sistema**.

6.  Fare clic su **OK**. 




## <a id="createstorage"> </a>Passaggio 2: Creare un account di archiviazione in Azure ##

L'account di archiviazione rappresenta il livello più elevato dello spazio dei nomi per l'accesso ai servizi di archiviazione di Azure ed è associato alla sottoscrizione di Azure. È necessario disporre di un account di archiviazione di Azure per caricare un file VHD in Azure che può essere usato per la creazione di una macchina virtuale. Per creare un account di archiviazione, è possibile usare il portale di gestione di Azure.

1. Accedere al portale di gestione di Azure.

2. Sulla barra dei comandi fare clic su **Nuovo**.

3. Fare clic su **Account di archiviazione** e quindi su **Creazione rapida**.

	![Quick create a storage account](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. Compilare i campi come indicato di seguito:

	
	
- In **URL** digitare un nome di sottodominio da usare nell'URL per l'account di archiviazione. La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo nome diventa il nome host all'interno dell'URL usato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione.
			
- Selezionare la **posizione o il gruppo di affinità** per l'account di archiviazione. Specificando un gruppo di affinità è possibile posizionare i servizi cloud nello stesso data center delle risorse di archiviazione.
		 
- Scegliere se si desidera usare la **replica geografica** per l'account di archiviazione. La replica geografica è attivata per impostazione predefinita. Se questa opzione è selezionata, i dati vengono replicati in una posizione secondaria, senza alcun costo aggiuntivo, per garantire il failover a una posizione secondaria qualora si verifichi un errore grave che non può essere gestito nella posizione primaria. La posizione secondaria viene assegnata automaticamente e non può essere modificata. Se determinati requisiti legali o criteri dell'organizzazione richiedono un controllo più rigido rispetto alla posizione dell'archiviazione basata su cloud, è possibile disattivare la replica geografica. Tenere presente che se si attiva la replica geografica in un momento successivo, verrà addebitato un corrispettivo di trasferimento dati una tantum per la replica dei dati esistenti nella posizione secondaria. I servizi di archiviazione senza replica geografica sono offerti a un prezzo scontato. Altri dettagli sulla gestione della replica geografica degli account di archiviazione sono disponibili nell'articolo [Come creare, gestire o eliminare un account di archiviazione](../storage-create-storage-account/#replication-options).

	![Enter storage account details](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)

5. Fare clic su **Crea account di archiviazione**.

	L'account verrà visualizzato in **Account di archiviazione**.

	![Storage account successfully created](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. Creare quindi un contenitore per i VHD caricati. Fare clic su **Nome account di archiviazione**, quindi su **Contenitori**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. Fare clic su **Crea contenitore**.

	![Storage account detail](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. Digitare un valore nel campo **Nome** per il contenitore e selezionare **Criteri di accesso**.

	![Container name](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

	> [WACOM.NOTE] Per impostazione predefinita, il contenitore è privato ed è accessibile solo al proprietario dell'account. Per consentire l'accesso in lettura pubblico ai BLOB nel contenitore, ma non alle proprietà e ai metadati del contenitore, usare l'opzione "BLOB pubblico". Per consentire l'accesso in lettura pubblico completo per contenitori e BLOB, usare l'opzione "Contenitore pubblico".

## <a id="PrepAzure"> </a>Passaggio 3: Preparare la connessione a Microsoft Azure ##

Prima di poter caricare un file VHD, è necessario stabilire una connessione sicura tra il computer e la sottoscrizione in Microsoft Azure. Per effettuare questa operazione è possibile usare il metodo basato su Microsoft Azure Active Directory o quello basato sul certificato.

<h3>Usare il metodo basato su Microsoft Azure AD</h3>

1. Aprire la console di Microsoft Azure PowerShell, come descritto in [Procedura: Installare Microsoft Azure PowerShell](#Install).

2. Digitare il comando seguente:  
	`Add-AzureAccount`
	
	Questo comando consente di aprire una finestra in cui è possibile eseguire l'accesso con l'account di lavoro e scuola.

	![PowerShell Window](./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png)

3. Le informazioni delle credenziali vengono autenticate e salvate in Microsoft Azure, quindi la finestra viene chiusa.

<h3>Usare il metodo basato sul certificato</h3> 

1. Aprire una finestra di Microsoft Azure PowerShell. 

2.	Tipo: 
	`Get-AzurePublishSettingsFile`.


3. Viene aperta una finestra del browser in cui viene chiesto di scaricare un file .publishsettings, contenente informazioni e un certificato per la sottoscrizione Microsoft Azure.

	![Browser download page](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. Salvare il file .publishsettings. 

4. Tipo: 
	`Import-AzurePublishSettingsFile <PathToFile>`

	Dove `<PathToFile>` è il percorso completo del file .publishsettings. 


	Per altre informazioni, vedere [Iniziare a usare i cmdlet di Microsoft Azure](http://msdn.microsoft.com/it-it/library/windowsazure/jj554332.aspx) 
	
	Per altre informazioni sull'installazione e la configurazione di PowerShell, vedere [Come installare e configurare Microsoft Azure PowerShell](http://www.windowsazure.com/it-it/documentation/articles/install-configure-powershell/) 


## <a id="upload"> </a>Passaggio 4: Caricare il file VHD ##

È possibile caricare il file VHD in qualsiasi posizione all'interno dell'archiviazione BLOB. Negli esempi di comandi seguenti, **BlobStorageURL** è l'URL per l'account di archiviazione creato nel passaggio 2 e **YourImagesFolder** è il contenitore all'interno dell'archiviazione BLOB in cui si desidera archiviare le immagini. **VHDName** è l'etichetta che identifica il disco rigido virtuale visualizzata nel portale di gestione. **PathToVHDFile** è il percorso completo e il nome del file VHD. 


1. Nella finestra di Microsoft Azure PowerShell usata nel passaggio precedente, digitare:

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`
	
	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png)

	Per altre informazioni sul cmdlet Add-AzureVhd, vedere [Add-AzureVhd](http://msdn.microsoft.com/it-it/library/dn495173.aspx).

##Aggiungere l'immagine all'elenco di immagini personalizzate ##
Dopo avere caricato il file VHD, aggiungerlo come immagine all'elenco di immagini personalizzate associate alla propria sottoscrizione.

1. Nel portale di gestione, in **Tutti gli elementi**, fare clic su **Macchine virtuali**.

2. In Macchine virtuali fare clic su **Immagini**.

3. Fare quindi clic su **Crea immagine**.

	![PowerShell Add-AzureVHD](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png)

4. In **Crea immagine da VHD** effettuare le seguenti operazioni:
 	
	- Specificare un **nome**
	- Specificare una **descrizione**.
	- Per specificare l'**URL del VHD**, fare clic sul pulsante della cartella per aprire la finestra di dialogo seguente.
 
	![Select VHD](./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png)
	- Selezionare l'account di archiviazione in cui è definito il VHD e fare clic su **Apri**. Viene nuovamente visualizzata la finestra **Crea immagine da VHD**.
	- Nella finestra **Crea immagine da VHD** selezionare la famiglia di sistemi operativi.
	- Selezionare la casella **Sysprep è stato eseguito sulla macchina virtuale associata a questo VHD** per confermare di avere generalizzato il sistema operativo nel passaggio 1 e quindi fare clic su **OK**. 

	![Add Image](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png)

5. **FACOLTATIVO:** è anche possibile usare il cmdlet Add-AzureVMImage di Azure PowerShell per aggiungere il VHD come immagine.

	Nella finestra di Microsoft Azure PowerShell digitare:

	`Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>`
	
	![PowerShell Add-AzureVMImage](./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png)

6. Una volta completati i passaggi precedenti, quando si seleziona la scheda **Immagini** la nuova immagine risulta inclusa nell'elenco. 


	![custom image](./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png)

	È possibile usare questa nuova immagine quando si crea una nuova macchina virtuale. Selezionare **Immagini personali** per visualizzare la nuova immagine. Per istruzioni generali, vedere [Creare una macchina virtuale che esegue Windows Server](http://www.windowsazure.com/it-it/documentation/articles/virtual-machines-windows-tutorial/).

	![create VM from custom image](./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png)

## Passaggi successivi ##
 

Dopo aver creato una macchina virtuale, provare a creare una macchina virtuale di SQL Server. Per istruzioni, vedere [Provisioning di una macchina virtuale di SQL Server in Microsoft Azure](http://www.windowsazure.com/it-it/documentation/articles/virtual-machines-provision-sql-server/). 

[Passaggio 1: Preparare l'immagine da caricare]: #prepimage
[Passaggio 2: Creare un account di archiviazione in Azure]: #createstorage
[Passaggio 3: Preparare la connessione ad Azure]: #prepAzure
[Passaggio 4: Caricare il file VHD]: #upload

<!--HONumber=35.2-->
