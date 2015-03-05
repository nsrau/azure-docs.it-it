<properties 
	pageTitle="Creazione e caricamento di un file VHD Linux in Azure" 
	description="Come creare e caricare un disco rigido virtuale Azure (VHD) che contiene il sistema operativo Linux." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="KBDAzure" 
	manager="timlt" 
	editor="tysonn"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="vm-linux" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/13/2015" 
	ms.author="kathydav, szarkos"/>

# Creazione e caricamento di un disco rigido virtuale che contiene il sistema operativo Linux

Questo articolo illustra come creare e caricare un disco rigido virtuale (VHD) in modo da usarlo come immagine per la creazione di macchine virtuali in Azure. L'articolo fornisce istruzioni su come preparare il sistema operativo in modo da usarlo per la creazione di più macchine virtuali basate sull'immagine specificata.  

> [AZURE.NOTE] Per completare i passaggi descritti in questo articolo, non è necessario essere esperti di macchine virtuali di Azure. È invece richiesto un account Azure. È possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni, vedere [Creare un account Azure](http://azure.microsoft.com/develop/php/tutorials/create-a-windows-azure-account/). 

Una macchina virtuale in Azure esegue il sistema operativo basato sull'immagine scelta al momento della creazione della macchina virtuale. Le immagini vengono archiviate in formato VHD, nei file vhd in un account di archiviazione. Per altre informazioni su dischi e immagini in Azure, vedere [Gestione di dischi e immagini](http://msdn.microsoft.com/library/windowsazure/jj672979.aspx).

Quando si crea una macchina virtuale, è possibile personalizzare le impostazioni del sistema operativo in modo che siano appropriate all'applicazione che si desidera eseguire. Per istruzioni, vedere [Come creare una macchina virtuale personalizzata](/it-it/manage/windows/how-to-guides/custom-create-a-vm/).

**Importante**: il contratto di servizio della piattaforma Azure si applica alle macchine virtuali che eseguono il sistema operativo Linux solo quando una distribuzione approvata viene usata con i dettagli di configurazione specificati in [questo articolo](http://support.microsoft.com/kb/2805216). Tutte le distribuzioni di Linux disponibili nella raccolta immagini di Azure sono distribuzioni approvate con la configurazione richiesta.


##Prerequisiti##
In questo articolo si presuppone che l'utente disponga degli elementi seguenti:

- **Un certificato di gestione**: l'utente deve avere creato un certificato di gestione per la sottoscrizione per cui si desidera caricare un file VHD e avere esportato il certificato in un file con estensione .cer. Per altre informazioni sulla creazione di certificati, vedere [Creare e caricare un certificato di gestione per Azure](http://msdn.microsoft.com/library/windowsazure/gg551722.aspx). 

- **Sistema operativo Linux installato in un file VHD**  : l'utente deve aver installato un sistema operativo Linux supportato in un disco rigido virtuale. Sono disponibili diversi strumenti per creare file VHD, ad esempio per creare il file VHD e installare il sistema operativo, è possibile usare una soluzione di virtualizzazione come Hyper-V. Per istruzioni, vedere [Installare il ruolo Hyper-V e configurare una macchina virtuale](http://technet.microsoft.com/library/hh846766.aspx). 

	**Importante**: il formato VHDX più recente non è supportato in Azure. È possibile convertire il disco in formato VHD tramite la console di gestione di Hyper-V o il cmdlet convert-vhd.

	Per un elenco delle distribuzioni approvate, vedere [Linux in Azure - Distribuzioni approvate](../linux-endorsed-distributions). In alternativa vedere la sezione [Informazioni per le distribuzioni non approvate](../virtual-machines-linux-create-upload-vhd-generic) al termine di questo articolo.

- **Strumento da riga di comando di Azure per Linux**: se si usa un sistema operativo Linux per creare l'immagine, usare gli [strumenti da riga di comando di Azure per Linux e Mac](http://go.microsoft.com/fwlink/?LinkID=253691&clcid=0x409) per caricare il file VHD.

- **Strumenti di Azure PowerShell**: per caricare il file VHD, è anche possibile usare il cmdlet `Add-AzureVhd`. Per scaricare i cmdlet di Azure Powershell, visitare la pagina dei [download di Azure](http://azure.microsoft.com/downloads/). Per informazioni di riferimento, vedere [Add-AzureVhd](http://msdn.microsoft.com/library/windowsazure/dn495173.aspx).


Questa attività include i passaggi seguenti:

- [Passaggio 1: Preparare l'immagine da caricare] []
- [Passaggio 2: Creare un account di archiviazione in Azure] []
- [Passaggio 3: Preparare la connessione ad Azure] []
- [Passaggio 4: Caricare l'immagine in Azure] []

## <a id="prepimage"> </a>Passaggio 1: Preparare l'immagine da caricare ##

Microsoft Azure supporta numerose distribuzioni di Linux (vedere la sezione sulle [distribuzioni approvate](../linux-endorsed-distributions)). Gli articoli seguenti forniscono le istruzioni per preparare le diverse distribuzioni Linux supportate in Azure:

- **[Distribuzioni basate su CentOS](../virtual-machines-linux-create-upload-vhd-centos)**
- **[Oracle Linux](../virtual-machines-linux-create-upload-vhd-oracle)**
- **[SLES e openSUSE](../virtual-machines-linux-create-upload-vhd-suse)**
- **[Ubuntu](../virtual-machines-linux-create-upload-vhd-ubuntu)**
- **[Altro - Distribuzioni non approvate](../virtual-machines-linux-create-upload-vhd-generic)**

Vedere inoltre le **[note sull'installazione di Linux](../virtual-machines-linux-create-upload-vhd-generic/#linuxinstall)** per ulteriori suggerimenti sulla preparazione di immagini Linux per Azure.

Una volta eseguite le procedure delle guide precedenti, si dovrebbe avere un file VHD pronto per essere caricato in Azure.


## <a id="createstorage"> </a>Passaggio 2: Creare un account di archiviazione in Azure ##

L'account di archiviazione rappresenta il livello più elevato dello spazio dei nomi per l'accesso ai servizi di archiviazione di Azure ed è associato alla sottoscrizione di Azure. È necessario disporre di un account di archiviazione di Azure per caricare un file VHD in Azure che può essere usato per la creazione di una macchina virtuale. Per creare un account di archiviazione, è possibile usare il portale di gestione di Azure.

1. Accedere al portale di gestione di Azure.

2. Sulla barra dei comandi fare clic su **Nuovo**.

	![Create storage account](./media/virtual-machines-linux-create-upload-vhd/create.png)

3. Fare clic su **Account di archiviazione** e quindi su **Creazione rapida**.

	![Quick create a storage account](./media/virtual-machines-linux-create-upload-vhd/storage-quick-create.png)

4. Compilare i campi come indicato di seguito:

	![Enter storage account details](./media/virtual-machines-linux-create-upload-vhd/storage-create-account.png)

- In **URL** digitare un nome di sottodominio da usare nell'URL per l'account di archiviazione. La voce può contenere da 3 a 24 lettere minuscole e numeri. Questo nome diventa il nome host all'interno dell'URL usato per fare riferimento a risorse BLOB, di accodamento o tabelle per la sottoscrizione.
	
- Selezionare la posizione o il gruppo di affinità per l'account di archiviazione. Specificando un gruppo di affinità è possibile posizionare i servizi cloud nello stesso data center delle risorse di archiviazione.
 
- Scegliere se si desidera usare la replica geografica per l'account di archiviazione. La replica geografica è attivata per impostazione predefinita. Se questa opzione è selezionata, i dati vengono replicati in una posizione secondaria, senza alcun costo aggiuntivo, per garantire il failover a una posizione secondaria qualora si verifichi un errore grave che non può essere gestito nella posizione primaria. La posizione secondaria viene assegnata automaticamente e non può essere modificata. Se determinati requisiti legali o criteri dell'organizzazione richiedono un controllo più rigido rispetto alla posizione dell'archiviazione basata sul cloud, è possibile disattivare la replica geografica. Si noti che se si attiva la replica geografica in un momento successivo, verrà addebitato un corrispettivo di trasferimento dati una tantum per la replica dei dati esistenti nella posizione secondaria. I servizi di archiviazione senza replica geografica sono offerti a un prezzo scontato.

5. Fare clic su **Crea account di archiviazione**.

	L'account verrà visualizzato in **Account di archiviazione**.

	![Storage account successfully created](./media/virtual-machines-linux-create-upload-vhd/Storagenewaccount.png)


## <a id="connect"> </a>Passaggio 3: Preparare la connessione ad Azure ##

Prima di poter caricare un file VHD, è necessario stabilire una connessione sicura tra il computer e la sottoscrizione in Azure. 

1. Aprire una finestra di Azure PowerShell.

2. Digitare: 

	`Get-AzurePublishSettingsFile`

	Questo comando apre una finestra del browser e scarica automaticamente un file .publishsettings che contiene un certificato e le informazioni relative alla sottoscrizione di Azure. 

3. Salvare il file .publishsettings. 

4. Digitare:

	`Import-AzurePublishSettingsFile <PathToFile>`

	Dove `<PathToFile>` è il percorso completo del file .publishsettings. 

	Per altre informazioni, vedere [Iniziare a usare i cmdlet di Azure](http://msdn.microsoft.com/library/windowsazure/jj554332.aspx) 


## <a id="upload"> </a>Passaggio 4: Caricare l'immagine in Azure ##

È possibile caricare il file VHD in qualsiasi posizione all'interno dell'archiviazione BLOB. Negli esempi di comandi seguenti, **BlobStorageURL** è l'URL per l'account di archiviazione creato nel passaggio 2 e **YourImagesFolder** è il contenitore all'interno dell'archiviazione BLOB in cui si desidera archiviare le immagini. **VHDName** è l'etichetta che identifica il disco rigido virtuale visualizzata nel portale di gestione. **PathToVHDFile** è il percorso completo e il nome del file VHD. 

Eseguire una delle operazioni seguenti:

- Nella finestra di Azure PowerShell usata nel passaggio precedente, digitare:

		`Add-AzureVhd -Destination <BlobStorageURL>/<YourImagesFolder>/<VHDName> -LocalFilePath <PathToVHDFile>`

	Per altre informazioni, vedere [Add-AzureVhd](http://msdn.microsoft.com/library/windowsazure/dn205185.aspx).

- Usare lo strumento da riga di comando per Linux per caricare l'immagine. È possibile caricare un'immagine usando il comando seguente:

		# azure vm image create <image-name> --location <location-of-the-data-center> --os Linux <source-path-to the vhd>



[Passaggio 1: Preparare l'immagine da caricare]: #prepimage
[Passaggio 2: Creare un account di archiviazione in Azure]: #createstorage
[Passaggio 3: Preparare la connessione ad Azure]: #connect
[Passaggio 4: Caricare l'immagine in Azure]: #upload


<!--HONumber=45--> 
