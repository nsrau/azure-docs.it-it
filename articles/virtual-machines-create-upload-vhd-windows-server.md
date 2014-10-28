<properties linkid="manage-windows-common-task-upload-vhd" urlDisplayName="Upload a VHD" pageTitle="Create and upload a Windows Server VHD to Azure" metaKeywords="Azure VHD, uploading VHD" description="Learn to create and upload a virtual hard disk (VHD) in Azure that has the Windows Server operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create and upload a Windows Server VHD to Azure" authors="kathydav" solutions="" manager="timlt" editor="tysonn" />

<tags ms.service="virtual-machines" ms.workload="infrastructure-services" ms.tgt_pltfrm="vm-windows" ms.devlang="na" ms.topic="article" ms.date="09/23/2014" ms.author="kathydav"></tags>

# Creazione e caricamento di un disco rigido virtuale con Windows Server in Azure

Questo articolo illustra come caricare un disco rigido virtuale (VHD, Virtual Hard Disk) con un sistema operativo in modo da poterlo usare come immagine in base alla quale creare macchine virtuali. Per altre informazioni su dischi e immagini in Microsoft Azure, vedere l'argomento relativo alla [gestione di dischi e immagini in Azure][gestione di dischi e immagini in Azure].

**Nota**: quando si crea una macchina virtuale, è possibile personalizzare le impostazioni del sistema operativo per facilitare l'esecuzione dell'applicazione. La configurazione impostata viene archiviata nel disco per quella macchina virtuale. Per istruzioni, vedere [Come creare una macchina virtuale personalizzata][Come creare una macchina virtuale personalizzata].

## Prerequisiti

In questo articolo si presuppone che l'utente disponga degli elementi seguenti:

**Una sottoscrizione Azure**: se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni, vedere [Creazione di un account Azure][Creazione di un account Azure].

**Microsoft Azure PowerShell**: se il modulo Microsoft Azure PowerShell è installato. Per scaricare il modulo, vedere la pagina dei [download di Microsoft Azure][download di Microsoft Azure]. In [questo articolo][questo articolo] è disponibile un'esercitazione relativa all'installazione e alla configurazione di PowerShell con una sottoscrizione Azure.

-   Il cmdlet [Add-AzureVHD][Add-AzureVHD], incluso nel modulo Microsoft Azure PowerShell. Questo cmdlet verrà usato per caricare il VHD.

**Un sistema operativo Windows supportato archiviato in un file VHD**: l'utente deve avere installato un sistema operativo Windows Server supportato in un disco rigido virtuale. Sono disponibili vari strumenti per la creazione di file VHD. Per creare il file VHD e installare il sistema operativo, è possibile utilizzare soluzioni di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale][Installare il ruolo Hyper-V e configurare una macchina virtuale].

**Importante**: il formato VHDX non è supportato in Microsoft Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il [cmdlet Convert-VHD][cmdlet Convert-VHD]. È possibile trovare un'esercitazione relativa alla procedura di conversione in [questo articolo][1].

**Supporto del sistema operativo Windows Server.** Questa attività richiede un file con estensione iso contenente il sistema operativo Windows Server. Sono supportate le versioni di Windows Server seguenti:

<table border="1" width="600">

<tr bgcolor="#E9E7E7">

<th>
SO

</th>

<th>
SKU

</th>

<th>
Service Pack

</th>

<th>
Architettura

</th>

</tr>

<tr>

<td>
Windows Server 2012 R2

</td>

<td>
Tutte le edizioni

</td>

<td>
N/D

</td>

<td>
x64

</td>

</tr>

<tr>

<td>
Windows Server 2012

</td>

<td>
Tutte le edizioni

</td>

<td>
N/D

</td>

<td>
x64

</td>

</tr>

<tr>

<td>
Windows Server 2008 R2

</td>

<td>
Tutte le edizioni

</td>

<td>
SP1

</td>

<td>
x64

</td>

</tr>

</table>

</p>
</p>
Questa attività include i passaggi seguenti:

-   [Passaggio 1: Preparare l'immagine da caricare][Passaggio 1: Preparare l'immagine da caricare]
-   [Passaggio 2: Creare un account di archiviazione in Azure][Passaggio 2: Creare un account di archiviazione in Azure]
-   [Passaggio 3: Preparare la connessione ad Azure][Passaggio 3: Preparare la connessione ad Azure]
-   [Passaggio 4: Caricare il file VHD][Passaggio 4: Caricare il file VHD]

## <span id="prepimage"></span> </a>Passaggio 1: Preparare l'immagine da caricare

Prima di poter caricare l'immagine in Azure, è necessario generalizzarla tramite il comando Sysprep. Per ulteriori informazioni sull'utilizzo di Sysprep, vedere [Introduzione all'utilizzo di SysPrep][Introduzione all'utilizzo di SysPrep].

Nella macchina virtuale appena creata, completare la procedura seguente:

1.  Accedere al sistema operativo.

2.  Aprire una finestra del Prompt dei comandi come amministratore. Impostare la directory su **%windir%\\system32\\sysprep**, quindi eseguire `sysprep.exe`.

    ![Apertura della finestra del Prompt dei comandi][Apertura della finestra del Prompt dei comandi]

3.  Verrà visualizzata la finestra di dialogo **Utilità preparazione sistema**.

    ![Avvio di Sysprep][Avvio di Sysprep]

4.  In **Utilità preparazione sistema** selezionare **Passare alla Configurazione guidata** e verificare che l'opzione Generalizza sia selezionata.

5.  In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.

6.  Fare clic su **OK**.

## <span id="createstorage"></span> </a>Passaggio 2: Creare un account di archiviazione in Azure

L'account di archiviazione rappresenta il livello più elevato dello spazio dei nomi per l'accesso ai servizi di archiviazione di Azure ed è associato alla sottoscrizione di Azure. È necessario disporre di un account di archiviazione di Azure per caricare un file VHD in Azure che può essere utilizzato per la creazione di una macchina virtuale. Per creare un account di archiviazione, è possibile utilizzare il portale di gestione di Azure.

1.  Accedere al portale di gestione di Azure.

2.  Sulla barra dei comandi fare clic su **Nuovo**.

3.  Fare clic su **Account di archiviazione** e quindi su **Creazione rapida**.

    ![Creazione rapida di un account di archiviazione][Creazione rapida di un account di archiviazione]

4.  Compilare i campi come indicato di seguito:

-   In **URL** digitare un nome di sottodominio da utilizzare nell'URL per l'account di archiviazione. La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo nome diventa il nome host all'interno dell'URL utilizzato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione.

-   Selezionare la **posizione o il gruppo di affinità** per l'account di archiviazione. Specificando un gruppo di affinità è possibile posizionare i servizi cloud nello stesso data center delle risorse di archiviazione.

-   Scegliere se si desidera usare la **replica geografica** per l'account di archiviazione. La replica geografica è attivata per impostazione predefinita. Se questa opzione è selezionata, i dati vengono replicati in una posizione secondaria, senza alcun costo aggiuntivo, per garantire il failover a una posizione secondaria qualora si verifichi un errore grave che non può essere gestito nella posizione primaria. La posizione secondaria viene assegnata automaticamente e non può essere modificata. Se determinati requisiti legali o criteri dell'organizzazione richiedono un controllo più rigido rispetto alla posizione dell'archiviazione basata su cloud, è possibile disattivare la replica geografica. Tenere presente che se si attiva la replica geografica in un momento successivo, verrà addebitato un corrispettivo di trasferimento dati una tantum per la replica dei dati esistenti nella posizione secondaria. I servizi di archiviazione senza replica geografica sono offerti a un prezzo scontato. Altri dettagli sulla gestione della replica geografica degli account di archiviazione sono disponibili nell'articolo [Come gestire gli account di archiviazione][Come gestire gli account di archiviazione].

    ![Immissione dei dettagli dell'account di archiviazione][Immissione dei dettagli dell'account di archiviazione]

1.  Fare clic su **Crea account di archiviazione**.

    L'account verrà visualizzato in **Account di archiviazione**.

    ![Creazione dell'account di archiviazione completata][Creazione dell'account di archiviazione completata]

2.  Creare quindi un contenitore per i VHD caricati. Fare clic su **Nome account di archiviazione**, quindi su **Contenitori**.

    ![Dettaglio dell'account di archiviazione][Dettaglio dell'account di archiviazione]

3.  Fare clic su **Crea contenitore**.

    ![Dettaglio dell'account di archiviazione][2]

4.  Digitare un valore nel campo **Nome** per il contenitore e selezionare **Criteri di accesso**.

    ![Nome del contenitore][Nome del contenitore]

    > [WACOM.NOTE] Per impostazione predefinita, il contenitore è privato ed è accessibile solo al proprietario dell'account. Per consentire l'accesso in lettura pubblico ai BLOB nel contenitore, ma non alle proprietà e ai metadati del contenitore, usare l'opzione "BLOB pubblico". Per consentire l'accesso in lettura pubblico completo per contenitori e BLOB, usare l'opzione "Contenitore pubblico".

## <span id="PrepAzure"></span> </a>Passaggio 3: Preparare la connessione a Microsoft Azure

Prima di poter caricare un file VHD, è necessario stabilire una connessione sicura tra il computer e la sottoscrizione in Microsoft Azure. Per effettuare questa operazione è possibile usare il metodo basato su Microsoft Azure Active Directory o quello basato sul certificato.

### Usare il metodo basato su Microsoft Azure AD

1.  Aprire la console di Microsoft Azure PowerShell, come descritto in [Procedura: Installare Microsoft Azure PowerShell][Procedura: Installare Microsoft Azure PowerShell].

2.  Digitare il comando seguente:
    `Add-AzureAccount`

    Questo comando consente di aprire una finestra in cui è possibile eseguire l'accesso con l'account di lavoro e scuola.

    ![Finestra di PowerShell][Finestra di PowerShell]

3.  Le informazioni delle credenziali vengono autenticate e salvate in Microsoft Azure, quindi la finestra viene chiusa.

### Usare il metodo basato sul certificato

1.  Aprire una finestra di Microsoft Azure PowerShell.

2.  Digitare:
    `Get-AzurePublishSettingsFile`

3.  Viene aperta una finestra del browser in cui viene chiesto di scaricare un file .publishsettings, contenente informazioni e un certificato per la sottoscrizione Microsoft Azure.

    ![Pagina di download del browser][Pagina di download del browser]

4.  Salvare il file .publishsettings.

5.  Digitare:
    `Import-AzurePublishSettingsFile <PathToFile>`

    Dove `<PathToFile>` è il percorso completo del file .publishsettings.

    Per altre informazioni, vedere [Iniziare a usare i cmdlet di Microsoft Azure][Iniziare a usare i cmdlet di Microsoft Azure].

    Per altre informazioni sull'installazione e la configurazione di PowerShell, vedere [Come installare e configurare Microsoft Azure PowerShell][questo articolo]

## <span id="upload"></span> </a>Passaggio 4: Caricare il file VHD

È possibile caricare il file VHD in qualsiasi posizione all'interno dell'archiviazione BLOB. Negli esempi di comandi seguenti, **BlobStorageURL** è l'URL per l'account di archiviazione creato nel passaggio 2 e **YourImagesFolder** è il contenitore all'interno dell'archiviazione BLOB in cui si desidera archiviare le immagini. **VHDName** è l'etichetta che identifica il disco rigido virtuale visualizzata nel portale di gestione. **PathToVHDFile** è il percorso completo e il nome del file VHD.

1.  Nella finestra di Microsoft Azure PowerShell usata nel passaggio precedente, digitare:

    `Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`

    ![Add-AzureVHD di PowerShell][Add-AzureVHD di PowerShell]

    Per altre informazioni sul cmdlet Add-AzureVhd, vedere [Add-AzureVhd][Add-AzureVhd].

## Aggiungere l'immagine all'elenco di immagini personalizzate

Dopo avere caricato il file VHD, aggiungerlo come immagine all'elenco di immagini personalizzate associate alla propria sottoscrizione.

1.  Nel portale di gestione, in **Tutti gli elementi**, fare clic su **Macchine virtuali**.

2.  In Macchine virtuali fare clic su **Immagini**.

3.  Fare quindi clic su **Crea immagine**.

    ![Add-AzureVHD di PowerShell][3]

4.  In **Crea immagine da VHD** effettuare le seguenti operazioni:

    -   Specificare un **nome**.
    -   Specificare una **descrizione**.
    -   Per specificare l'**URL del VHD**, fare clic sul pulsante della cartella per avviare la finestra di dialogo seguente.

    ![Selezione del VHD][Selezione del VHD]

    -   Selezionare l'account di archiviazione in cui è definito il VHD e fare clic su **Apri**. Viene nuovamente visualizzata la finestra **Crea immagine da VHD**.
    -   Una volta visualizzata la finestra **Crea immagine da VHD**, selezionare la famiglia di sistemi operativi.
    -   Selezionare la casella **Sysprep eseguito sulla macchina virtuale associata a questo VHD** per confermare di avere generalizzato il sistema operativo nel passaggio 1 e quindi fare clic su **OK**.

    ![Aggiunta di un'immagine][Aggiunta di un'immagine]

5.  **FACOLTATIVO:** è anche possibile usare il cmdlet Add-AzureVMImage di Azure PowerShell per aggiungere il VHD come immagine.

    Nella finestra di Microsoft Azure PowerShell digitare:

    `Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>`

    ![PowerShell Add-AzureVMImage][PowerShell Add-AzureVMImage]

6.  Una volta completati i passaggi precedenti, quando si seleziona la scheda **Immagini** la nuova immagine risulta inclusa nell'elenco.

    ![immagine personalizzata][immagine personalizzata]

    È possibile usare questa nuova immagine quando si crea una nuova macchina virtuale. Selezionare **Immagini personali** per visualizzare la nuova immagine. Per istruzioni, vedere [Creazione di una macchina virtuale che esegue Windows Server][Come creare una macchina virtuale personalizzata].

    ![creazione di una macchina virtuale in base a un'immagine personalizzata][creazione di una macchina virtuale in base a un'immagine personalizzata]

## Passaggi successivi

Dopo aver creato una macchina virtuale, provare a creare una macchina virtuale di SQL Server. Per istruzioni, vedere [Provisioning di una macchina virtuale di SQL Server in Microsoft Azure][Provisioning di una macchina virtuale di SQL Server in Microsoft Azure].

  [gestione di dischi e immagini in Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/jj672979.aspx
  [Come creare una macchina virtuale personalizzata]: http://www.windowsazure.com/it-it/documentation/articles/virtual-machines-windows-tutorial/
  [Creazione di un account Azure]: http://www.windowsazure.com/it-it/develop/php/tutorials/create-a-windows-azure-account/
  [download di Microsoft Azure]: http://www.windowsazure.com/it-it/downloads/
  [questo articolo]: http://www.windowsazure.com/it-it/documentation/articles/install-configure-powershell/
  [Add-AzureVHD]: http://msdn.microsoft.com/it-it/library/windowsazure/dn205185.aspx
  [Installare il ruolo Hyper-V e configurare una macchina virtuale]: http://technet.microsoft.com/it-it/library/hh846766.aspx
  [cmdlet Convert-VHD]: http://technet.microsoft.com/it-it/library/hh848454.aspx
  [1]: http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx
  [Passaggio 1: Preparare l'immagine da caricare]: #prepimage
  [Passaggio 2: Creare un account di archiviazione in Azure]: #createstorage
  [Passaggio 3: Preparare la connessione ad Azure]: #prepAzure
  [Passaggio 4: Caricare il file VHD]: #upload
  [Introduzione all'utilizzo di SysPrep]: http://technet.microsoft.com/it-it/library/bb457073.aspx
  [Apertura della finestra del Prompt dei comandi]: ./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png
  [Avvio di Sysprep]: ./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png
  [Creazione rapida di un account di archiviazione]: ./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png
  [Come gestire gli account di archiviazione]: http://www.windowsazure.com/it-it/documentation/articles/storage-manage-storage-account/
  [Immissione dei dettagli dell'account di archiviazione]: ./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png
  [Creazione dell'account di archiviazione completata]: ./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png
  [Dettaglio dell'account di archiviazione]: ./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png
  [2]: ./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png
  [Nome del contenitore]: ./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png
  [Procedura: Installare Microsoft Azure PowerShell]: #Install
  [Finestra di PowerShell]: ./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png
  [Pagina di download del browser]: ./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png
  [Iniziare a usare i cmdlet di Microsoft Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/jj554332.aspx
  [Add-AzureVHD di PowerShell]: ./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png
  [Add-AzureVhd]: http://msdn.microsoft.com/it-it/library/dn495173.aspx
  [3]: ./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png
  [Selezione del VHD]: ./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png
  [Aggiunta di un'immagine]: ./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png
  [PowerShell Add-AzureVMImage]: ./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png
  [immagine personalizzata]: ./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png
  [creazione di una macchina virtuale in base a un'immagine personalizzata]: ./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png
  [Provisioning di una macchina virtuale di SQL Server in Microsoft Azure]: http://www.windowsazure.com/it-it/documentation/articles/virtual-machines-provision-sql-server/
