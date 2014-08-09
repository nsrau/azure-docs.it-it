<properties  linkid="manage-windows-common-task-upload-vhd" urlDisplayName="Upload a VHD" pageTitle="Create and upload a Windows Server VHD to Azure" metaKeywords="Azure VHD, uploading VHD" description="Learn how to create and upload a virtual hard disk (VHD) in Azure that has the Windows Server operating system." metaCanonical="" services="virtual-machines" documentationCenter="" title="Creating and Uploading a Virtual Hard Disk that Contains the Windows Server Operating System" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

# Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Windows Server

Una macchina virtuale in Azure esegue il sistema operativo scelto al momento della creazione della macchina virtuale. Azure archivia il sistema operativo di una macchina virtuale in un disco rigido virtuale in formato VHD (file con estensione vhd). Il disco rigido virtuale di un sistema operativo che è stato preparato per la duplicazione è denominato immagine. In questo articolo viene illustrato come creare la propria immagine tramite il caricamento di un file VHD con un sistema operativo che è stato installato e generalizzato dall'utente. Per ulteriori informazioni su dischi e immagini in Azure, vedere [Gestione di dischi e immagini][1].

**Nota**: Quando si crea una macchina virtuale, è possibile personalizzare le impostazioni del sistema operativo per facilitare l'esecuzione dell'applicazione. La configurazione impostata viene archiviata nel disco per quella macchina virtuale. Per istruzioni, vedere [Come creare una macchina virtuale personalizzata](/en-us/manage/windows/how-to-guides/custom-create-a-vm/).

## Prerequisiti

In questo articolo si presuppone che l'utente disponga degli elementi seguenti:

**Un certificato di gestione**: l'utente deve avere creato un certificato di gestione per la sottoscrizione per cui si desidera caricare un file VHD e avere esportato il certificato in un file con estensione cer. Per ulteriori informazioni sulla creazione di certificati, vedere [Creare e caricare un certificato di gestione per Azure][2].

**Un sistema operativo Windows supportato archiviato in un file VHD**: l'utente deve avere installato un sistema operativo Windows Server supportato in un disco rigido virtuale. Sono disponibili vari strumenti per la creazione di file VHD. Per creare il file VHD e installare il sistema operativo, è possibile utilizzare soluzioni di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale][3].

**Importante**: il formato VHDX più recente non è supportato in Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet convert-vhd.

* **Supporto del sistema operativo Windows Server.** Questa attività richiede un file con estensione iso contenente il sistema operativo Windows Server. Sono supportate le versioni di Windows Server seguenti:
<P>
  <table  border="1" width="600">
  <tr  bgcolor="#E9E7E7">
    <th>SO</th>
    <th>SKU</th>
    <th>Service Pack</th>
    <th>Architettura</th>
  </tr>
  <tr>
    <td>Windows Server 2012</td>
    <td>Tutte le edizioni</td>
    <td>N/D</td>
    <td>x64</td>
  </tr>
  <tr>
    <td>Windows Server 2008 R2</td>
    <td>Tutte le edizioni</td>
    <td>SP1</td>
    <td>x64</td>
  </tr>
  </table>
</P>

* Cmdlet [Add-AzureVHD][4], incluso nel modulo Azure PowerShell. Per scaricare il modulo, vedere la pagina dei [download di Azure](/en-us/develop/downloads/).


Questa attività include i passaggi seguenti:

* [Passaggio 1: Preparare l'immagine da caricare](#prepimage)
* [Passaggio 2: Creare un account di archiviazione in Azure](#createstorage)
* [Passaggio 3: Preparare la connessione ad Azure](#prepAzure)
* [Passaggio 4: Caricare il file VHD](#upload)

## <a  id="prepimage"> </a>Passaggio 1: Preparare l'immagine da caricare

Prima di poter caricare l'immagine in Azure, è necessario generalizzarla tramite il comando Sysprep. Per ulteriori informazioni sull'utilizzo di Sysprep, vedere [Introduzione all'utilizzo di Sysprep][5].

Nella macchina virtuale appena creata, completare la procedura seguente:

1.  Accedere al sistema operativo.

2.  Aprire una finestra del Prompt dei comandi come amministratore. Modificare la directory con **%windir%\\system32\\sysprep** e quindi eseguire `sysprep.exe`.
    
    ![Apertura della finestra del Prompt dei comandi](./media/virtual-machines-create-upload-vhd-windows-server/sysprepcommand.png)

3.  Verrà visualizzata la finestra di dialogo **Utilità preparazione sistema**.
    
    ![Avvio di Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  In **Azione pulizia sistema** selezionare **Passare alla Configurazione guidata** e verificare che l'opzione **Generalizza** sia selezionata.

5.  In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.

6.  Fare clic su **OK**.


## <a  id="createstorage"> </a>Passaggio 2: Creare un account di archiviazione in Azure

L'account di archiviazione rappresenta il livello più elevato dello spazio dei nomi per l'accesso ai servizi di archiviazione di Azure ed è associato alla sottoscrizione di Azure. È necessario disporre di un account di archiviazione di Azure per caricare un file VHD in Azure che può essere utilizzato per la creazione di una macchina virtuale. Per creare un account di archiviazione, è possibile utilizzare il portale di gestione di Azure.

1.  Accedere al portale di gestione di Azure.

2.  Sulla barra dei comandi fare clic su **New**.

3.  Fare clic su **Storage Account** e quindi su **Quick Create**.
    
    ![Creazione rapida di un account di archiviazione](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4.  Compilare i campi come indicato di seguito:
    
    ![Immissione dei dettagli dell'account di archiviazione](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)

* In **URL** digitare un nome di sottodominio da utilizzare nell'URL per l'account di archiviazione. La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo nome diventa il nome host all'interno dell'URL utilizzato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione.

* Scegliere la posizione o il gruppo di affinità per l'account di archiviazione. Specificando un gruppo di affinità è possibile posizionare i servizi cloud nello stesso data center delle risorse di archiviazione.

* Scegliere se si desidera utilizzare la replica geografica per l'account di archiviazione. La replica geografica è attivata per impostazione predefinita. Se questa opzione è selezionata, i dati vengono replicati in una posizione secondaria, senza alcun costo aggiuntivo, per garantire il failover a una posizione secondaria qualora si verifichi un errore grave che non può essere gestito nella posizione primaria. La posizione secondaria viene assegnata automaticamente e non può essere modificata. Se determinati requisiti legali o criteri dell'organizzazione richiedono un controllo più rigido rispetto alla posizione dell'archiviazione basata su cloud, è possibile disattivare la replica geografica. Tenere presente che se si attiva la replica geografica in un momento successivo, verrà addebitato un corrispettivo di trasferimento dati una tantum per la replica dei dati esistenti nella posizione secondaria. I servizi di archiviazione senza replica geografica sono offerti a un prezzo scontato.

1.  Fare clic su **Create Storage Account**.
    
    L'account verrà visualizzato in **Storage Accounts**.
    
    ![Creazione dell'account di archiviazione completata](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

## <a id="PrepAzure"> </a>Passaggio 3: Preparare la connessione ad Azure

Prima di poter caricare un file VHD, è necessario stabilire una connessione sicura tra il computer e la sottoscrizione in Azure.

1.  Aprire una finestra di Azure PowerShell.

2.  Digitare:
    
    `Get-AzurePublishSettingsFile`
    
    Questo comando apre una finestra del browser e scarica automaticamente un file .publishsettings che contiene un certificato e le informazioni relative alla sottoscrizione di Azure.

3.  Salvare il file .publishsettings.

4.  Digitare:
    
    `Import-AzurePublishSettingsFile <PathToFile>`
    
    Dove `<PathToFile>` è il percorso completo del file .publishsettings.
    
    Per ulteriori informazioni, vedere [Iniziare a utilizzare i cmdlet di Azure][6].

## <a  id="upload"> </a>Passaggio 4: Caricare il file VHD

È possibile caricare il file VHD in qualsiasi posizione all'interno dell'archiviazione BLOB. Negli esempi di comandi seguenti, **BlobStorageURL** è l'URL per l'account di archiviazione creato nel passaggio 2 e **YourImagesFolder** è il contenitore all'interno dell'archiviazione BLOB in cui si desidera archiviare le immagini. **VHDName** è l'etichetta che identifica il disco rigido virtuale visualizzata nel portale di gestione. **PathToVHDFile** è il percorso completo e il nome del file VHD.

1.  Nella finestra di Azure PowerShell utilizzata nel passaggio precedente, digitare:
    
	`Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`
    
    Per ulteriori informazioni, vedere [Add-AzureVhd][4].

## Aggiungere l'immagine all'elenco di immagini personalizzate
Dopo avere caricato il file VHD, aggiungerlo come immagine all'elenco di immagini personalizzate associate alla propria sottoscrizione.

1.  Nel portale di gestione, in **All Items** fare clic su **Virtual Machines**.

2.  In Virtual Machines fare clic su **Images** e quindi su **Create**.

3.  In **Create an image from a VHD** specificare un nome e l'URL del file VHD caricato.

4.  Selezionare la casella **I have run Sysprep on the virtual machine associated with this VHD** per confermare di avere generalizzato il sistema operativo nel passaggio 2 e quindi fare clic su **OK**.


## Passaggi successivi
Quando l'immagine è disponibile nell'elenco, è possibile utilizzarla per creare macchine virtuali. Per istruzioni, vedere [Creazione di una macchina virtuale che esegue Windows Server](../virtual-machines-windows-tutorial/).

[1]: http://msdn.microsoft.com/it-it/library/windowsazure/jj672979.aspx
[2]: http://msdn.microsoft.com/it-it/library/windowsazure/gg551722.aspx
[3]: http://technet.microsoft.com/en-us/library/hh846766.aspx
[4]: http://msdn.microsoft.com/it-it/library/windowsazure/dn205185.aspx
[5]: http://technet.microsoft.com/en-us/library/bb457073.aspx
[6]: http://msdn.microsoft.com/it-it/library/windowsazure/jj554332.aspx