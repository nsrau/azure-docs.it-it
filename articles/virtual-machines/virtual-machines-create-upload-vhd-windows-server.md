<properties 
	pageTitle="Creazione e caricamento di un disco rigido virtuale con Windows Server in Azure" 
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
	ms.date="04/29/2015" 
	ms.author="kathydav"/>


# Creazione e caricamento di un disco rigido virtuale con Windows Server in Azure#

Questo articolo illustra come caricare un disco rigido virtuale \(VHD, Virtual Hard Disk\) con un sistema operativo in modo da poterlo usare come immagine in base alla quale creare macchine virtuali. Per altre informazioni su dischi e immagini in Microsoft Azure, vedere l'argomento relativo alla [gestione di dischi e immagini in Azure](http://msdn.microsoft.com/library/azure/jj672979.aspx).

> [AZURE.NOTE]Quando si crea una macchina virtuale basata su un'immagine, è possibile personalizzare le impostazioni del sistema operativo come appropriato per le applicazioni che si intende eseguire sulla macchina virtuale. Questa configurazione viene salvata per la macchina virtuale e non influisce sull'immagine.

## Prerequisiti##

In questo articolo si presuppone che l'utente disponga degli elementi seguenti:

1. **Una sottoscrizione di Microsoft Azure**: se non già disponibile è possibile [aprire un account Microsoft Azure gratuitamente](/pricing/free-trial/?WT.mc_id=A261C142F). Si riceveranno dei crediti da usare per provare i servizi di Microsoft Azure a pagamento e, anche dopo avere esaurito i crediti, sarà possibile mantenere comunque l'account per usare i servizi di Microsoft Azure gratuiti, ad esempio Siti Web. La carta di credito non verrà mai addebitata, a meno l'utente non modifichi le impostazioni e che richieda esplicitamente di essere addebitato. È possibile inoltre [attivare i benefici della sottoscrizione MSDN](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): con la sottoscrizione MSDN ogni mese si accumulano crediti che è possibile utilizzare per i servizi di Microsoft Azure a pagamento.   

2. **Microsoft Azure PowerShell**: è necessario che il modulo di Microsoft Azure PowerShell sia installato e configurato per l’uso della sottoscrizione. Per scaricare il modulo, vedere la pagina dei [download di Microsoft Azure](http://azure.microsoft.com/downloads/). Un'esercitazione per installare e configurare in modulo è disponibile [qui](../powershell-install-configure.md). Per caricare il VHD, usare il cmdlet [Add-AzureVHD](http://msdn.microsoft.com/library/azure/dn495173.aspx) di Azure.

3. **Un sistema operativo Windows supportato archiviato in un file VHD**: l'utente deve avere installato un sistema operativo Windows Server supportato in un disco rigido virtuale. Sono disponibili vari strumenti per la creazione di file VHD. Per creare una macchina virtuale e installare il sistema operativo, è possibile usare soluzioni di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx).

> [AZURE.NOTE]il formato VHDX non è supportato in Microsoft Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il [cmdlet Convert-VHD](http://technet.microsoft.com/library/hh848454.aspx). È possibile trovare un'esercitazione relativa alla procedura di conversione in [questo articolo](http://blogs.msdn.com/b/virtual_pc_guy/archive/2012/10/03/using-powershell-to-convert-a-vhd-to-a-vhdx.aspx).
 
 Sono supportate le seguenti versioni di Windows Server<P> <TABLE BORDER="1" WIDTH="600"> <TR BGCOLOR="#E9E7E7"> <TH>Sistema operativo</TH> <TH>SKU</TH> <TH>Service Pack</TH> <TH>Architettura</TH> </TR> <TR> <TD>Windows Server 2012 R2</TD> <TD>Tutte le edizioni</TD> <TD>N/D</TD> <TD>x64</TD> </TR> <TR> <TD>Windows Server 2012</TD> <TD>Tutte le edizioni</TD> <TD>N/D</TD> <TD>x64</TD> </TR> <TR> <TD>Windows Server 2008 R2</TD> <TD>Tutte le edizioni</TD> <TD>SP1</TD> <TD>x64</TD> </TR> </TABLE> </P>


Questa attività include i passaggi seguenti:

- [Passaggio 1: preparare l'immagine da caricare][]
- [Passaggio 2: creare un account di archiviazione in Microsoft Azure][]
- [Passaggio 3: preparare la connessione a Microsoft Azure][]
- [Passaggio 4: caricare il file VHD][]

## Passaggio 1: preparare l'immagine da caricare ##

Prima di poter caricare l'immagine in Azure, è necessario generalizzarla utilizzando il comando Sysprep. Per ulteriori informazioni su Sysprep, vedere [Come usare Sysprep: introduzione](http://technet.microsoft.com/library/bb457073.aspx).

Dalla macchina virtuale su cui è stato installato il sistema operativo, completare la seguente procedura:

1. Accedere al sistema operativo.

2. Aprire una finestra del Prompt dei comandi come amministratore. Impostare la directory su **%windir%\\system32\\sysprep**, quindi eseguire `sysprep.exe`.

	![Apertura della finestra del Prompt dei comandi](./media/virtual-machines-create-upload-vhd-windows-server/sysprep_commandprompt.png)

3.	Verrà visualizzata la finestra di dialogo **Utilità preparazione sistema**.

	![Avvio di Sysprep](./media/virtual-machines-create-upload-vhd-windows-server/sysprepgeneral.png)

4.  In **Utilità preparazione sistema** selezionare **Passare alla Configurazione guidata** e verificare che l'opzione Generalizza sia selezionata.

5.  In **Opzioni di arresto del sistema** selezionare **Arresta il sistema**.

6.  Fare clic su **OK**.


## Passaggio 2: creare un account di archiviazione in Microsoft Azure ##

È necessario disporre di un account di archiviazione di Azure per caricare un file .vhd da usare in Azure per la creazione di una macchina virtuale. Per creare un account di archiviazione, è possibile usare il portale di gestione di Azure.

1. Accedere al portale di gestione di Azure.

2. Sulla barra dei comandi fare clic su **Nuovo**.

3. Fare clic su **Servizi dati** \> **Archiviazione** \> **Creazione rapida**.

	![Creazione rapida di un account di archiviazione](./media/virtual-machines-create-upload-vhd-windows-server/Storage-quick-create.png)

4. Compilare i campi come indicato di seguito:
	
	- In **URL** digitare un nome di sottodominio da usare nell'URL per l'account di archiviazione. La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo nome diventa il nome host all'interno dell'URL utilizzato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione.
			
	- Selezionare la **posizione o il gruppo di affinità** per l'account di archiviazione. Un gruppo di affinità consente di collocare i servizi cloud e l'archiviazione nello stesso data center.
		 
	- Scegliere se si desidera usare la **replica geografica** per l'account di archiviazione. La replica geografica è attivata per impostazione predefinita. Se questa opzione è selezionata, i dati vengono replicati in una posizione secondaria, senza alcun costo aggiuntivo, per garantire il failover a tale posizione qualora si verifichi un errore grave nella posizione primaria. La posizione secondaria viene assegnata automaticamente e non può essere modificata. Se è necessario un maggiore controllo sulla posizione dell'archiviazione basata sul cloud a causa di requisiti legali o criteri dell'organizzazione, è possibile disattivare la replica geografica. Tenere presente che se si attiva la replica geografica in un momento successivo, verrà addebitato un corrispettivo di trasferimento dati una tantum per la replica dei dati esistenti nella posizione secondaria. I servizi di archiviazione senza replica geografica sono offerti a un prezzo scontato. Altri dettagli sulla gestione della replica geografica degli account di archiviazione sono disponibili nell'articolo: [Creare, gestire o eliminare un account di archiviazione](../storage-create-storage-account/#replication-options).

	![Immissione dei dettagli dell'account di archiviazione](./media/virtual-machines-create-upload-vhd-windows-server/Storage-create-account.png)


5. Fare clic su **Create Storage Account**. L'account verrà visualizzato in **Archiviazione**.

	![Creazione dell'account di archiviazione completata](./media/virtual-machines-create-upload-vhd-windows-server/Storagenewaccount.png)

6. Creare quindi un contenitore per i VHD caricati. Fare clic sul nome account di archiviazione, quindi su **Contenitori**.

	![Dettaglio dell'account di archiviazione](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_detail.png)

7. Fare clic su **Crea contenitore**.

	![Dettaglio dell'account di archiviazione](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_container.png)

8. Digitare un valore nel campo **Nome** per il contenitore e selezionare i **Criteri di accesso**.

	![Nome del contenitore](./media/virtual-machines-create-upload-vhd-windows-server/storageaccount_containervalues.png)

	> [AZURE.NOTE]Per impostazione predefinita, il contenitore è privato ed è accessibile solo al proprietario dell'account. Per consentire l'accesso in lettura pubblico ai BLOB nel contenitore, ma non alle proprietà e ai metadati del contenitore, usare l'opzione "BLOB pubblico". Per consentire l'accesso in lettura pubblico completo per contenitori e BLOB, usare l'opzione "Contenitore pubblico".

## Passaggio 3: preparare la connessione a Microsoft Azure ##

Prima di poter caricare un file VHD, è necessario stabilire una connessione sicura tra il computer e la sottoscrizione in Azure. Per effettuare questa operazione è possibile usare il metodo basato su Microsoft Azure Active Directory o quello basato sul certificato.

> [AZURE.TIP]Per informazioni introduttive su Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](../install-configure-powershell.md). Per altre informazioni, vedere [Iniziare a usare i cmdlet di Microsoft Azure](https://msdn.microsoft.com/library/azure/jj554332.aspx).

### Usare il metodo basato su Microsoft Azure AD

1. Aprire la console di Azure PowerShell.

2. Digitare: `Add-AzureAccount`
	
	Questo comando consente di aprire una finestra in cui è possibile eseguire l'accesso con l'account di lavoro e scuola.

	![Finestra di PowerShell](./media/virtual-machines-create-upload-vhd-windows-server/add_azureaccount.png)

3. Le informazioni delle credenziali vengono autenticate e salvate in Azure, quindi la finestra viene chiusa.

### Usare il metodo basato sul certificato 

1. Aprire la console di Azure PowerShell. 

2.	Digitare: `Get-AzurePublishSettingsFile`.

3. Viene aperta una finestra del browser in cui viene chiesto di scaricare un file .publishsettings, contenente informazioni e un certificato per la sottoscrizione Microsoft Azure.

	![Pagina di download del browser](./media/virtual-machines-create-upload-vhd-windows-server/Browser_download_GetPublishSettingsFile.png)

3. Salvare il file .publishsettings.

4. Digitare: `Import-AzurePublishSettingsFile <PathToFile>`

	Dove `<PathToFile>` è il percorso completo del file .publishsettings.

## Passaggio 4: caricare il file VHD

È possibile caricare il file VHD in qualsiasi posizione all'interno dell'archiviazione BLOB. Negli esempi di comandi seguenti, **BlobStorageURL** è l'URL per l'account di archiviazione creato nel passaggio 2 e **YourImagesFolder** è il contenitore all'interno dell'archiviazione BLOB in cui si desidera archiviare le immagini. **VHDName** è l'etichetta che identifica il disco rigido virtuale visualizzata nel portale di gestione. **PathToVHDFile** è il percorso completo e il nome del file VHD.


1. Nella finestra di Azure PowerShell utilizzata nel passaggio precedente, digitare:

	`Add-AzureVhd -Destination "<BlobStorageURL>/<YourImagesFolder>/<VHDName>.vhd" -LocalFilePath <PathToVHDFile>`
	
	![Add-AzureVHD di PowerShell](./media/virtual-machines-create-upload-vhd-windows-server/powershell_upload_vhd.png)

	Per altre informazioni sul cmdlet Add-AzureVhd, vedere [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx).

## Passaggio 5: aggiungere l'immagine all'elenco di immagini personalizzate ##

Dopo avere caricato il file VHD, aggiungerlo come immagine all'elenco di immagini personalizzate associate alla propria sottoscrizione.

1. Nel portale di gestione, in **Tutti gli elementi**, fare clic su **Macchine virtuali**.

2. In Macchine virtuali fare clic su **Immagini**.

3. Fare quindi clic su **Crea immagine**.

	![Add-AzureVHD di PowerShell](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image.png)

4. In **Crea immagine da VHD** effettuare le seguenti operazioni:
 	

	- Specificare un **nome**.
	- Specificare una **descrizione**.
	- Per specificare l'**URL del VHD**, fare clic sul pulsante della cartella per aprire la seguente finestra di dialogo:
 
	![Selezione del VHD](./media/virtual-machines-create-upload-vhd-windows-server/Select_VHD.png)

	- Selezionare l'account di archiviazione in cui è definito il VHD e fare clic su **Apri**. Viene nuovamente visualizzata la finestra **Crea immagine da VHD**.
	- Una volta visualizzata la finestra **Crea immagine da VHD**, selezionare la famiglia di sistemi operativi.
	- Selezionare la casella **Sysprep eseguito sulla macchina virtuale associata a questo VHD** per confermare di avere generalizzato il sistema operativo nel passaggio 1 e quindi fare clic su **OK**. 

	![Aggiunta di un'immagine](./media/virtual-machines-create-upload-vhd-windows-server/Create_Image_From_VHD.png)

5. **FACOLTATIVO:** è possibile usare il cmdlet Add-AzureVMImage invece del portale di gestione per aggiungere il proprio VHD come immagine. Nella console di Azure PowerShell, digitare:

	`Add-AzureVMImage -ImageName <Your Image's Name> -MediaLocation <location of the VHD> -OS <Type of the OS on the VHD>`
	
	![PowerShell Add-AzureVMImage](./media/virtual-machines-create-upload-vhd-windows-server/add_azureimage_powershell.png)

6. Una volta completati i passaggi precedenti, quando si seleziona la scheda **Immagini** la nuova immagine risulta inclusa nell'elenco.


	![immagine personalizzata](./media/virtual-machines-create-upload-vhd-windows-server/vm_custom_image.png)

	La nuova immagine è ora disponibile in **Immagini personali** quando si crea una macchina virtuale. Per istruzioni, vedere [Come creare una macchina virtuale personalizzata che esegue Windows](virtual-machines-windows-create-custom.md).

	![creazione di una macchina virtuale in base a un'immagine personalizzata](./media/virtual-machines-create-upload-vhd-windows-server/create_vm_custom_image.png)

	> [AZURE.TIP]Se si verifica un errore quando si tenta di creare una macchina virtuale e viene visualizzato il messaggio "Le dimensioni virtuali del VHD https://XXXXX.. pari a YYYY byte non sono supportate. La dimensione deve essere un numero intero \(espresso in MB\)", significa che il disco rigido virtuale non corrisponde a un numero intero di MB. Deve essere un VHD di dimensioni fisse. Provare a usare il cmdlet Add-AzureVMImage PowerShell anziché il portale di gestione per aggiungere l'immagine \(vedere il passaggio 5, sopra\). I cmdlet di Azure garantiscono che il VHD soddisfi i requisiti di Azure.
	
## Passaggi successivi ##
 

Dopo aver creato una macchina virtuale, provare a creare una macchina virtuale di SQL Server. Per istruzioni, vedere [Provisioning di una macchina virtuale di SQL Server in Microsoft Azure](virtual-machines-provision-sql-server.md).

[Passaggio 1: preparare l'immagine da caricare]: #prepimage
[Passaggio 2: creare un account di archiviazione in Microsoft Azure]: #createstorage
[Passaggio 3: preparare la connessione a Microsoft Azure]: #prepAzure
[Passaggio 4: caricare il file VHD]: #upload
 

<!---HONumber=58_postMigration-->