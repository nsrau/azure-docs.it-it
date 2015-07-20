<properties
	pageTitle="Uso di Azure PowerShell con Archiviazione di Azure | Microsoft Azure"
	description="Imparare a utilizzare i cmdlet PowerShell di Azure per l'archiviazione di Azure per creare e gestire gli account di archiviazione; lavorare con BLOB, tabelle, code e file. configurare analisi archiviazione di query e creare firme di accesso condiviso."
	services="storage"
	documentationCenter="na"
	authors="tamram" 
	manager="adinah"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/27/2015"
	ms.author="selcint"/>

# Uso di Azure PowerShell con Archiviazione di Azure

## Panoramica

In questa guida verrà illustrato come usare i [Cmdlets per la gestione del servizio di Azure per l’Archiviazione](https://msdn.microsoft.com/library/azure/dn806401.aspx) per eseguire una serie di attività di sviluppo e amministrazione con l’Archiviazione di Azure.

Azure PowerShell è un modulo che offre i cmdlet per gestire Azure tramite Windows PowerShell. Corrisponde a una shell della riga di comando basata su attività e un linguaggio di scripting progettato appositamente per l'amministrazione del sistema. Con PowerShell è possibile controllare e automatizzare facilmente l'amministrazione dei servizi e delle applicazioni di Azure. Ad esempio, è possibile usare i cmdlet per eseguire le stesse attività eseguibili tramite il portale di gestione di Azure.

Nella guida si presuppone una certa esperienza nell'uso di [Archiviazione di Azure](http://azure.microsoft.com/documentation/services/storage/) e di [Windows PowerShell](http://technet.microsoft.com/library/bb978526.aspx). La guida fornisce diversi script che mostrano come usare PowerShell con Archiviazione di Azure. Prima di eseguire gli script, è necessario aggiornarne le variabili in base alla configurazione.

La prima sezione di questa guida fornisce una panoramica di Archiviazione di Azure e PowerShell. Per informazioni dettagliate e istruzioni, iniziare da [Prerequisiti per l'uso di Azure PowerShell con Archiviazione di Azure](#prerequisites-for-using-azure-powershell-with-azure-storage).


## Introduzione ad Archiviazione di Azure e PowerShell in 5 minuti

Questa sezione descrive come accedere ad Archiviazione di Azure tramite PowerShell in 5 minuti.

**Novità in Azure:** ottenere una sottoscrizione di Microsoft Azure e un account Microsoft associato alla sottoscrizione. Per ottenere una sottoscrizione Azure, vedere la [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/), le [opzioni di acquisto](http://azure.microsoft.com/pricing/purchase-options/) e le [offerte per i membri](http://azure.microsoft.com/pricing/member-offers/) (per i membri di MSDN, Microsoft Partner Network, BizSpark e altri programmi Microsoft).

Per altre informazioni sugli account [Azure, vedere Gestire account, sottoscrizioni e ruoli amministrativi](https://msdn.microsoft.com/library/azure/hh531793.aspx).

**Dopo aver creato una sottoscrizione e un account di Microsoft Azure:**

1.	Scaricare e installare [Azure PowerShell](http://go.microsoft.com/?linkid=9811175&clcid=0x409).
2.	Avviare Windows PowerShell Integrated Scripting Environment (ISE): Nel computer locale, passare al menù **Start**. Digitare**Strumenti di amministrazione** e fare clic per eseguirli. Nella finestra **Strumenti di amministrazione** fare clic con il pulsante destro del mouse su **Windows PowerShell ISE**, farle clic su **Esegui come amministratore**.
3.	In **Windows PowerShell ISE**, fare clic su **File** > **Nuovo** per creare un nuovo file di script.
4.	A questo punto viene fornito uno script semplice che mostra i comandi PowerShell di base per accedere ad Archiviazione di Azure. Lo script richiede innanzitutto le credenziali dell'account Azure per aggiungerlo all'ambiente PowerShell locale. Lo script quindi imposta la sottoscrizione predefinita di Azure e crea un nuovo account di archiviazione in Azure. Lo script crea un nuovo contenitore in questo nuovo account di archiviazione e carica un file di immagine esistente (BLOB) in tale contenitore. Dopo aver elencato tutti i BLOB nel contenitore, lo script crea una nuova directory di destinazione nel computer locale e scarica il file di immagine.
5.	Nella sezione di codice riportata di seguito, selezionare lo script tra **#begin** e **#end**. Premere CTRL+C per copiarlo negli Appunti.

    	#begin
    	# Update with the name of your subscription.
    	$SubscriptionName="YourSubscriptionName"

    	# Give a name to your new storage account. It must be lowercase!
    	$StorageAccountName="yourstorageaccountname"

    	# Choose "West US" as an example.
    	$Location = "West US"

    	# Give a name to your new container.
    	$ContainerName = "imagecontainer"

    	# Have an image file and a source directory in your local computer.
    	$ImageToUpload = "C:\Images\HelloWorld.png"

    	# A destination directory in your local computer.
    	$DestinationFolder = "C:\DownloadImages"

    	# Add your Azure account to the local PowerShell environment.
    	Add-AzureAccount

    	# Set a default Azure subscription.
    	Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

    	# Create a new storage account.
    	New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $Location

    	# Set a default storage account.
    	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

    	# Create a new container.
    	New-AzureStorageContainer -Name $ContainerName -Permission Off

    	# Upload a blob into a container.
    	Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

    	# List all blobs in a container.
    	Get-AzureStorageBlob -Container $ContainerName

    	# Download blobs from the container:
    	# Get a reference to a list of all blobs in a container.
    	$blobs = Get-AzureStorageBlob -Container $ContainerName

    	# Create the destination directory.
    	New-Item -Path $DestinationFolder -ItemType Directory -Force  

    	# Download blobs into the local destination directory.
    	$blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder
    	#end

5.	In **Windows PowerShell ISE**, premere CTRL + V per copiare lo script. Fare clic su **File** > **Salva**. Nella finestra di dialogo **Salva con nome** digitare il nome del file di script, ad esempio "mystoragescript". Fare clic su **Save**.

6.	A questo punto, è necessario aggiornare le variabili dello script in base alle impostazioni di configurazione. È necessario aggiornare la variabile **$SubscriptionName** con la propria sottoscrizione. È possibile mantenere le altre variabili come specificato nello script o aggiornarle secondo le proprie preferenze.

	- **$SubscriptionName:** è necessario aggiornare questa variabile con il proprio nome di sottoscrizione. Attenersi a uno dei tre modi seguenti per individuare il nome della sottoscrizione:

		a. In **Windows PowerShell ISE**, fare clic su **File** > **Nuovo** per creare un nuovo file di script. Copiare lo script seguente nel nuovo file di script e fare clic su **Debug** > **Esegui**. Lo script richiede innanzitutto le credenziali dell'account Azure per aggiungerlo all'ambiente PowerShell locale, quindi visualizza tutte le sottoscrizioni connesse alla sessione PowerShell locale. Prendere nota del nome della sottoscrizione da usare durante questa esercitazione:

    		Add-AzureAccount
       		Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName


		b. Attualmente, Azure supporta due portali: il [portale di gestione di Azure](https://manage.windowsazure.com/) e il [portale di anteprima di Azure](https://portal.azure.com/). Se si accede all’attuale [portale di gestione di Azure](https://portal.azure.com/), scorrere e fare clic su **Impostazioni** sul lato sinistro del portale. Fare clic su **Sottoscrizioni**. Copiare il nome della sottoscrizione da usare durante l'esecuzione degli script specificati in questa guida. Vedere la schermata seguente come esempio.

		![Portale di gestione di Azure][Image1]

		c. Se si accede al [portale di anteprima di Azure](https://portal.azure.com/), fare clic su **SFOGLIA** nel menu Hub a sinistra. Quindi, fare clic su **Tutto**, fare clic su **Sottoscrizioni**. Copiare il nome della sottoscrizione da usare durante l'esecuzione degli script specificati in questa guida. Vedere la schermata seguente come esempio.

		![Portale di anteprima di Azure][Image2]


	- **$StorageAccountName:** utilizzare il nome specificato nello script oppure immettere un nuovo nome per l'account di archiviazione. **Importante:** il nome dell'account di archiviazione deve essere univoco in Azure. Utilizzare caratteri minuscoli.

	- **$Location:** utilizzare "West US" specificato nello script oppure scegliere altre posizioni di Azure, ad esempio Stati Uniti orientali, Europa settentrionale e così via.

	- **$ContainerName:** usare il nome specificato nello script oppure immettere un nuovo nome per il contenitore.

	- **$ImageToUpload:** immettere il percorso di un'immagine nel computer locale, ad esempio "C:\\Images\\HelloWorld.png".

	- **$DestinationFolder:** immettere il percorso di una directory locale per archiviare i file scaricati da Archiviazione di Azure, ad esempio "C:\\DownloadImages".

7.	Dopo aver aggiornato le variabili dello script nel file "mystoragescript.ps1", fare clic su **File** > **Salva**. Quindi, fare clic su **Debug** > **Esegui** o premere **F5** per eseguire lo script.

Dopo l'esecuzione dello script è necessario disporre di una cartella di destinazione locale che includa il file di immagine scaricato. La schermata seguente mostra un output di esempio:

![Scaricare BLOB][Image3]


> [AZURE.NOTE]La sezione "Introduzione ad Archiviazione di Azure e PowerShell in 5 minuti" fornisce un'introduzione rapida su come usare Azure PowerShell con Archiviazione di Azure. Per informazioni dettagliate e istruzioni si consiglia di leggere le sezioni seguenti.

## Prerequisiti per l'uso di Azure PowerShell con Archiviazione di Azure
Sono necessari una sottoscrizione e un account di Azure per eseguire i cmdlet di PowerShell forniti in questa guida.

Azure PowerShell è un modulo che offre i cmdlet per gestire Azure tramite Windows PowerShell. Per informazioni sull'installazione e sulla configurazione di Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). Si consiglia di scaricare e installare oppure di aggiornare il modulo alla versione di Azure PowerShell più recente prima di usare questa guida.

È possibile eseguire i cmdlet nella console di Azure PowerShell, nella console standard di Windows PowerShell oppure in Windows PowerShell Integrated Scripting Environment (ISE). Ad esempio, per aprire una **console di Azure PowerShell** andare al menu Start, digitare Microsoft Azure PowerShell, fare clic con il pulsante destro del mouse e scegliere Esegui come amministratore. Per aprire **Windows PowerShell ISE**, andare al menu Start, digitare Strumenti di amministrazione e fare clic per eseguirli. Nella finestra Strumenti di amministrazione fare clic con il pulsante destro del mouse su Windows PowerShell ISE, quindi scegliere Esegui come amministratore.

## Come gestire gli account di archiviazione in Azure

### Come impostare una sottoscrizione di Azure predefinita
Per gestire Archiviazione di Azure con Azure PowerShell è necessario eseguire l'autenticazione dell'ambiente client con Azure usando l'autenticazione di Azure Active Directory o l'autenticazione basata su certificato. Per informazioni dettagliate, vedere il tutorial [Come installare e configurare Azure PowerShell](../powershell-install-configure.md). In questa guida viene usata l'autenticazione di Azure Active Directory.

1.	Nella console di Azure PowerShell o in Windows PowerShell ISE, digitare il comando seguente per aggiungere l'account Azure all'ambiente PowerShell locale:

    `Add-AzureAccount`

2.	Nella finestra di accesso a Microsoft Azure digitare l'indirizzo di posta elettronica e la password associati all'account. Le informazioni delle credenziali vengono autenticate e salvate in Azure, quindi la finestra viene chiusa.

3.	Eseguire questo comando per visualizzare gli account Azure nell'ambiente PowerShell locale e verificare che l'account sia presente:

	`Get-AzureAccount`

4.	Quindi, eseguire il cmdlet seguente per visualizzare tutte le sottoscrizioni connesse alla sessione PowerShell locale e verificare che la sottoscrizione sia presente:

	`Get-AzureSubscription | Format-Table SubscriptionName, IsDefault, IsCurrent, CurrentStorageAccountName`

5.	Per impostare la sottoscrizione di Azure predefinita, eseguire il cmdlet Select-AzureSubscription:

	    $SubscriptionName = 'Your subscription Name'
    	Select-AzureSubscription -SubscriptionName $SubscriptionName –Default

6.	Verificare il nome della sottoscrizione predefinita eseguendo il cmdlet Get-AzureSubscription:

	`Get-AzureSubscription -Default`

7.	Per visualizzare tutti i cmdlet di PowerShell disponibili per Archiviazione di Azure, eseguire:

	`Get-Command -Module Azure -Noun *Storage*`

### Come creare un nuovo account di archiviazione di Azure
Per usare Archiviazione di Azure, è necessario un account di archiviazione. Dopo aver configurato il computer per connettersi alla sottoscrizione, è possibile creare un nuovo account di archiviazione di Azure.

1.	Eseguire il cmdlet Get-AzureLocation per trovare tutte le posizioni dei data center disponibili:

    `Get-AzureLocation | format-Table -Property Name, AvailableServices, StorageAccountTypes`

2.	Eseguire il cmdlet New-AzureStorageAccount per creare un nuovo account di archiviazione. Nell'esempio seguente viene creato un nuovo account di archiviazione nel data center "West US":

    	$location = "West US"
	    $StorageAccountName = "yourstorageaccount"
	    New-AzureStorageAccount –StorageAccountName $StorageAccountName -Location $location

> [AZURE.IMPORTANT]Il nome per l'account di archiviazione è univoco in Azure e deve essere in minuscolo. Per le convenzioni di denominazione e le restrizioni, vedere [Informazioni sugli account di archiviazione di Azure](storage-create-storage-account.md) e [Assegnazione di nome e riferimento a contenitori, BLOB e metadati](http://msdn.microsoft.com/library/azure/dd135715.aspx).

### Come impostare un account di archiviazione di Azure predefinito
È possibile avere più account di archiviazione nella sottoscrizione. È possibile sceglierne uno e impostarlo come account di archiviazione predefinito per tutti i comandi di archiviazione nella stessa sessione PowerShell. Questo consente di eseguire i comandi di archiviazione di Azure PowerShell senza specificare in modo esplicito il contesto di archiviazione.

1.	Per impostare un account di archiviazione predefinito per la sottoscrizione, è possibile eseguire il cmdlet Set-AzureSubscription.

		$SubscriptionName = "Your subscription name"
     	$StorageAccountName = "yourstorageaccount"  
    	Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName

2.	Eseguire quindi il cmdlet Get-AzureSubscription per verificare che l'account di archiviazione sia associato all'account di sottoscrizione predefinito. Questo comando restituisce le proprietà della sottoscrizione corrente, incluso l'account di archiviazione corrente.

	    Get-AzureSubscription –Current

### Come elencare tutti gli account di archiviazione di Azure in una sottoscrizione
Ogni sottoscrizione di Azure può avere fino a 100 account di archiviazione. Per informazioni più aggiornate sui limiti, vedere [Sottoscrizione di Azure e limiti dei servizi, quote e vincoli](../azure-subscription-service-limits.md).

Eseguire il cmdlet seguente per trovare il nome e lo stato degli account di archiviazione nella sottoscrizione corrente:

    Get-AzureStorageAccount | Format-Table -Property StorageAccountName, Location, AccountType, StorageAccountStatus

### Come creare un contesto di archiviazione di Azure
Il contesto di archiviazione di Azure è un oggetto in PowerShell che permette di incapsulare le credenziali di archiviazione. Usando il contesto di archiviazione durante l'esecuzione dei cmdlet successivi, è possibile autenticare la richiesta senza specificare in modo esplicito l'account di archiviazione e la relativa chiave di accesso. È possibile creare un contesto di archiviazione in molti modi, ad esempio usando la chiave di accesso e il nome dell'account di archiviazione, il token di firma di accesso condiviso, la stringa di connessione o il valore anonimo. Per ulteriori informazioni, vedere [Nuovo AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx).

Usare uno dei seguenti tre metodi per creare un contesto di archiviazione:

- Eseguire il [Get AzureStorageKey](http://msdn.microsoft.com/library/azure/dn495235.aspx) cmdlet per individuare la chiave di accesso alle risorse di archiviazione primaria per l'account di archiviazione di Azure. Chiamare quindi il cmdlet [New AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) per creare un contesto di archiviazione:

    	$StorageAccountName = "yourstorageaccount"
    	$StorageAccountKey = Get-AzureStorageKey -StorageAccountName $StorageAccountName
    	$Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey.Primary


- Generare un token di firma di accesso condiviso per un contenitore di archiviazione di Azure e usarlo per creare un contesto di archiviazione:

    	$sasToken = New-AzureStorageContainerSASToken -Container abc -Permission rl
    	$Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -SasToken $sasToken

	Per ulteriori informazioni, vedere [New AzureStorageContainerSASToken](http://msdn.microsoft.com/library/azure/dn806416.aspx) e [Firme Accesso Condiviso, Parte 1: Comprensione del Modello SAS](storage-dotnet-shared-access-signature-part-1.md).

- In alcuni casi, è possibile specificare gli endpoint del servizio quando si crea un nuovo contesto di archiviazione. Ciò potrebbe essere necessario quando un nome di dominio personalizzato per l'account di archiviazione viene registrato con il servizio BLOB oppure si vuole usare una firma di accesso condiviso per l'accesso alle risorse di archiviazione. Impostare gli endpoint del servizio in una stringa di connessione e usarla per creare un nuovo contesto di archiviazione, come illustrato di seguito:

    	$ConnectionString = "DefaultEndpointsProtocol=http;BlobEndpoint=<blobEndpoint>;QueueEndpoint=<QueueEndpoint>;TableEndpoint=<TableEndpoint>;AccountName=<AccountName>;AccountKey=<AccountKey>"
    	$Ctx = New-AzureStorageContext -ConnectionString $ConnectionString

Per altre informazioni su come configurare una stringa di connessione di archiviazione, vedere [Configurazione delle stringhe di connessione](storage-configure-connection-string.md).

Dopo aver configurato il computer e compreso come gestire le sottoscrizioni e gli account di archiviazione di Azure PowerShell, passare alla sezione successiva per informazioni su come gestire i BLOB e gli snapshot BLOB di Azure.

## Come gestire i BLOB e gli snapshot BLOB di Azure
Archivio BLOB di Azure è un servizio per l'archiviazione di grandi quantità di dati non strutturati, ad esempio dati di testo o binari, a cui è possibile accedere da qualsiasi parte del mondo tramite HTTP o HTTPS. Questa sezione presuppone la conoscenza dei concetti relativi al servizio di archiviazione BLOB di Azure. Per informazioni dettagliate, vedere [Come usare l'archiviazione BLOB da .NET](storage-dotnet-how-to-use-blobs.md) e [Concetti relativi al servizio BLOB](http://msdn.microsoft.com/library/azure/dd179376.aspx).

### Come creare un contenitore
Ogni BLOB nell'archiviazione di Azure deve risiedere in un contenitore. È possibile creare un contenitore privato usando il cmdlet New-AzureStorageContainer:

    $StorageContainerName = "yourcontainername"
    New-AzureStorageContainer -Name $StorageContainerName -Permission Off

> [AZURE.NOTE]Esistono tre livelli di accesso in lettura anonimo: **Off**, **Blob**, e **contenitore**. Per impedire l'accesso anonimo ai BLOB, impostare il parametro di autorizzazione su **Disattivato**. Per impostazione predefinita, il nuovo contenitore è privato ed è accessibile solo al proprietario dell'account. Per consentire l'accesso in lettura pubblico anonimo alle risorse BLOB, ma non ai metadati del contenitore o all'elenco dei BLOB nel contenitore, impostare il parametro di autorizzazione su **BLOB**. Per consentire l'accesso in lettura pubblico completo alle risorse BLOB, ai metadati del contenitore e all'elenco dei BLOB nel contenitore, impostare il parametro di autorizzazione **su Contenitore**. Per ulteriori informazioni, vedere [gestione dell'accesso alle risorse di archiviazione Azure](storage-manage-access-to-resources.md).

### Come caricare un BLOB in un contenitore
In Archiviazione BLOB di Azure sono supportati BLOB in blocchi e BLOB di pagine. Per altre informazioni, vedere [Informazioni sui Blob in blocchi e sui Blob di pagine](http://msdn.microsoft.com/library/azure/ee691964.aspx).

Per caricare i BLOB in un contenitore, è possibile usare il cmdlet [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx). Per impostazione predefinita, questo comando carica i file locali in un BLOB in blocchi. Per specificare il tipo per il BLOB, è possibile usare il parametro - BlobType.

Nell'esempio seguente viene eseguito il cmdlet [Get-ChildItem](http://technet.microsoft.com/library/hh849800.aspx) per ottenere tutti i file nella cartella specificata, e poi passarli al cmdlet successivo usando l'operatore pipeline. Il cmdlet [Set-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806379.aspx) consente di caricare i file locali nel contenitore:

    Get-ChildItem –Path C:\Images* | Set-AzureStorageBlobContent -Container "yourcontainername"

### Come scaricare i BLOB da un contenitore
Nell'esempio seguente viene mostrato come scaricare i BLOB da un contenitore. Innanzitutto viene stabilita una connessione ad Archiviazione di Azure usando il contesto dell'account di archiviazione che include il nome dell'account di archiviazione e la relativa chiave di accesso primaria. Quindi, l'esempio recupera un riferimento BLOB usando il cmdlet [Get AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx). Nell'esempio viene usato il cmdlet [Get-AzureStorageBlobContent](http://msdn.microsoft.com/library/azure/dn806418.aspx) per scaricare i BLOB nella cartella di destinazione locale.

    #Define the variables.
    $ContainerName = "yourcontainername"
    $DestinationFolder = "C:\DownloadImages"

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #List all blobs in a container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Download blobs from a container.
    New-Item -Path $DestinationFolder -ItemType Directory -Force
    $blobs | Get-AzureStorageBlobContent -Destination $DestinationFolder -Context $Ctx

### Come copiare i BLOB da un contenitore di archiviazione a un altro
È possibile copiare i BLOB tra aree e account di archiviazione in modo asincrono. Nell'esempio seguente viene mostrato come copiare i BLOB da un contenitore di archiviazione a un altro in due diversi account di archiviazione. L'esempio imposta prima le variabili per gli account di archiviazione di origine e di destinazione, quindi crea un contesto di archiviazione per ogni account. Quindi, i BLOB vengono copiati dal contenitore di origine al contenitore di destinazione mediante il cmdlet [Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx). Nell'esempio si presuppone che i contenitori e gli account di archiviazione di origine e di destinazione esistano già.

    #Define the source storage account and context.
    $SourceStorageAccountName = "yoursourcestorageaccount"
    $SourceStorageAccountKey = "Storage key for yoursourcestorageaccount"
    $SrcContainerName = "yoursrccontainername"
    $SourceContext = New-AzureStorageContext -StorageAccountName $SourceStorageAccountName -StorageAccountKey $SourceStorageAccountKey

    #Define the destination storage account and context.
    $DestStorageAccountName = "yourdeststorageaccount"
    $DestStorageAccountKey = "Storage key for yourdeststorageaccount"
    $DestContainerName = "destcontainername"
    $DestContext = New-AzureStorageContext -StorageAccountName $DestStorageAccountName -StorageAccountKey $DestStorageAccountKey

    #Get a reference to blobs in the source container.
    $blobs = Get-AzureStorageBlob -Container $SrcContainerName -Context $SourceContext

    #Copy blobs from one container to another.
    $blobs| Start-AzureStorageBlobCopy -DestContainer $DestContainerName -DestContext $DestContext

In questo esempio viene eseguita una copia asincrona. È possibile monitorare lo stato di ogni copia eseguendo il cmdlet [Get-AzureStorageBlobCopyState](http://msdn.microsoft.com/library/azure/dn806406.aspx).

### Come copiare i BLOB da una posizione secondaria
È possibile copiare i BLOB da una posizione secondaria di un account abilitato RA-GRS.

    #define secondary storage context using a connection string constructed from secondary endpoints. 
    $SrcContext = New-AzureStorageContext -ConnectionString "DefaultEndpointsProtocol=https;AccountName=***;AccountKey=***;BlobEndpoint=http://***-secondary.blob.core.windows.net;FileEndpoint=http://***-secondary.file.core.windows.net;QueueEndpoint=http://***-secondary.queue.core.windows.net; TableEndpoint=http://***-secondary.table.core.windows.net;"
    Start-AzureStorageBlobCopy –Container *** -Blob *** -Context $SrcContext –DestContainer *** -DestBlob *** -DestContext $DestContext

### Come eliminare un BLOB
Per eliminare un BLOB, ottenere prima un riferimento al BLOB su cui chiamare il cmdlet Remove-AzureStorageBlob. Nell'esempio seguente vengono eliminati tutti i BLOB in un contenitore specificato. L'esempio imposta prima le variabili per un account di archiviazione, quindi crea un contesto di archiviazione. Quindi, viene recuperato un riferimento BLOB usando il cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) e viene eseguito il cmdlet [Remove-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806399.aspx) per rimuovere i BLOB da un contenitore in Archiviazione di Azure.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "containername"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to all the blobs in the container.
    $blobs = Get-AzureStorageBlob -Container $ContainerName -Context $Ctx

    #Delete blobs in a specified container.
    $blobs| Remove-AzureStorageBlob

## Come gestire gli snapshot BLOB di Azure
Azure consente di creare uno snapshot di un BLOB. Uno snapshot è una versione di sola lettura di un BLOB eseguito in un determinato momento. Una volta creato uno snapshot, è possibile leggerlo, copiarlo o eliminarlo, ma non modificarlo. Gli snapshot consentono di eseguire il backup di un BLOB così com'è in un determinato momento. Per ulteriori informazioni, vedere [Creazione di uno Snapshot di un BLOB](http://msdn.microsoft.com/library/azure/hh488361.aspx).

### Come creare uno snapshot BLOB
Per creare uno snapshot di un BLOB, ottenere prima un riferimento al BLOB su cui chiamare il `ICloudBlob.CreateSnapshot` metodo su di esso. L'esempio seguente imposta prima le variabili per un account di archiviazione, quindi crea un contesto di archiviazione. Quindi, viene recuperato un riferimento BLOB usando il cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) e viene eseguito il metodo [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) per creare uno snapshot.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $ContainerName = "yourcontainername"
    $BlobName = "yourblobname"
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $ContainerName -Blob $BlobName

    #Create a snapshot of the blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

### Come elencare gli snapshot di un BLOB
Non ci sono limiti agli snapshot creati per un BLOB. È possibile elencare gli snapshot associati al BLOB per tenere traccia degli snapshot correnti. Nell'esempio seguente viene usato un BLOB predefinito e chiamato il cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) per elencare gli snapshot del BLOB.

    #Define the blob name.
    $BlobName = "yourblobname"

    #List the snapshots of a blob.
    Get-AzureStorageBlob –Context $Ctx -Prefix $BlobName -Container $ContainerName  | Where-Object  { $_.ICloudBlob.IsSnapshot -and $_.Name -eq $BlobName }

### Come copiare uno snapshot di un BLOB
È possibile copiare uno snapshot di un BLOB per ripristinare lo snapshot. Per informazioni dettagliate e le restrizioni, vedere [Creazione di uno Snapshot di un BLOB](http://msdn.microsoft.com/library/azure/hh488361.aspx). L'esempio seguente imposta prima le variabili per un account di archiviazione, quindi crea un contesto di archiviazione. L'esempio definisce poi le variabili per il nome BLOB e il contenitore. Nell'esempio viene recuperato un riferimento BLOB usando il cmdlet [Get-AzureStorageBlob](http://msdn.microsoft.com/library/azure/dn806392.aspx) e viene eseguito il metodo [ICloudBlob.CreateSnapshot](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.icloudblob.aspx) per creare uno snapshot. Poi viene eseguito il cmdlet[Start-AzureStorageBlobCopy](http://msdn.microsoft.com/library/azure/dn806394.aspx) per copiare lo snapshot di un BLOB usando l'oggetto ICloudBlob per il BLOB di origine. Assicurarsi di aggiornare le variabili in base alla configurazione prima di eseguire l'esempio. Nell'esempio seguente si presuppone che i contenitori di origine e di destinazione e il BLOB di origine siano già presenti.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Define the variables.
    $SrcContainerName = "yoursourcecontainername"
    $DestContainerName = "yourdestcontainername"
    $SrcBlobName = "yourblobname"
    $DestBlobName = "CopyBlobName"

    #Get a reference to a blob.
    $blob = Get-AzureStorageBlob -Context $Ctx -Container $SrcContainerName -Blob $SrcBlobName

    #Create a snapshot of a blob.
    $snap = $blob.ICloudBlob.CreateSnapshot()

    #Copy the snapshot to another container.
    Start-AzureStorageBlobCopy –Context $Ctx -ICloudBlob $snap -DestBlob $DestBlobName -DestContainer $DestContainerName

Ora che si è appreso come gestire i BLOB e gli snapshot BLOB con Azure PowerShell, passare alla sezione successiva per informazioni su come gestire tabelle, code e file.

## Come gestire le tabelle e le entità di tabella di Azure
Il servizio di archiviazione tabelle di Azure è un archivio dati NoSQL, che è possibile usare per archiviare ed eseguire query su grandi set di dati strutturati non relazionali. I componenti principali del servizio sono tabelle, entità e proprietà. una tabella è una raccolta di entità. Un'entità è un set di proprietà. Ogni entità può avere fino a 252 proprietà, che corrispondono tutte a coppie nome-valore. Questa sezione presuppone la conoscenza dei concetti relativi al servizio di archiviazione tabelle di Azure. Per informazioni dettagliate, vedere [Informazioni sul modello di dati del servizio tabelle](http://msdn.microsoft.com/library/azure/dd179338.aspx) e [Come usare l'archiviazione tabelle da .NET](storage-dotnet-how-to-use-tables.md).

Nelle seguenti sottosezioni verrà spiegato come gestire il servizio di archiviazione tabelle di Azure usando Azure PowerShell. Gli scenari presentati includono **la creazione**, **l’eliminazione**, e **il recupero** **delle tabelle**, nonché **l’aggiunta**, **l'esecuzione di query**, e **l'eliminazione delle entità di tabella**.

### Come creare una tabella
Ogni tabella deve risiedere in un account di archiviazione di Azure. L'esempio seguente mostra come creare una tabella in Archiviazione di Azure. Innanzitutto viene stabilita una connessione ad Archiviazione di Azure usando il contesto dell'account di archiviazione che include il nome dell'account di archiviazione e la relativa chiave di accesso. Viene quindi usato il cmdlet [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) per creare una tabella in Archiviazione di Azure.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Create a new table.
    $tabName = "yourtablename"
    New-AzureStorageTable –Name $tabName –Context $Ctx

### Come recuperare una tabella
È possibile eseguire query e recuperare una o tutte le tabelle di un account di archiviazione. L'esempio seguente mostra come recuperare una determinata tabella usando il cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx).

    #Retrieve a table.
    $tabName = "yourtablename"
    Get-AzureStorageTable –Name $tabName –Context $Ctx

Se si chiama il cmdlet Get-AzureStorageTable senza parametri, si ottengono tutte le tabelle di archiviazione per un account di archiviazione.

### Come eliminare una tabella
È possibile eliminare una tabella da un account di archiviazione usando il cmdlet [Remove-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806393.aspx).

    #Delete a table.
    $tabName = "yourtablename"
    Remove-AzureStorageTable –Name $tabName –Context $Ctx

### Come gestire le entità di tabella
Attualmente, Azure PowerShell non fornisce i cmdlet per gestire direttamente le entità di tabella. Per eseguire operazioni sulle entità di tabella, è possibile usare le classi fornite nella [libreria client di archiviazione di Azure per .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).

#### Come aggiungere le entità di tabella
Per aggiungere un'entità a una tabella, creare prima un oggetto che definisca le proprietà dell'entità. Un'entità può contenere fino a 255 proprietà, incluse 3 proprietà di sistema: **PartitionKey**, **RowKey**, e **Timestamp**. L'utente è responsabile dell'inserimento e dell'aggiornamento dei valori di **PartitionKey** e **RowKey**. Il server gestisce il valore **Timestamp**, che non può essere modificato. Insieme **PartitionKey** e **RowKey** identificano in modo univoco tutte le entità di una tabella.

-	**PartitionKey**: determina la partizione in cui è archiviata l'entità.
-	**RowKey**: identifica in modo univoco l'entità all'interno della partizione.

È possibile definire fino a 252 proprietà personalizzate per un'entità. Per ulteriori informazioni, vedere [Informazioni sul modello di dati del servizio tabelle](http://msdn.microsoft.com/library/azure/dd179338.aspx).

Nell'esempio seguente viene mostrato come aggiungere le entità a una tabella. L'esempio mostra come recuperare la tabella employee e come aggiungere varie entità. Innanzitutto viene stabilita una connessione ad Archiviazione di Azure usando il contesto dell'account di archiviazione che include il nome dell'account di archiviazione e la relativa chiave di accesso. Successivamente viene recuperata la tabella specificata usando il cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx). Se la tabella non esiste, il cmdlet [New-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806417.aspx) viene usato per creare una tabella in Archiviazione di Azure. L'esempio definisce quindi una funzione personalizzata Add-Entity per aggiungere entità alla tabella specificando la partizione di ciascuna entità e la chiave di riga. La funzione Add-Entity chiama il cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) nella classe [Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.dynamictableentity.aspx) classe per creare un oggetto entità. Quindi, viene chiamato il metodo [Microsoft.WindowsAzure.Storage.Table.TableOperation.Insert](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.insert.aspx) nell'oggetto entità per aggiungerlo a una tabella.

    #Function Add-Entity: Adds an employee entity to a table.
    function Add-Entity()
    {
    param(
       $table,
       [String]$partitionKey,
       [String]$rowKey,
       [String]$name,
       [Int]$id
    )

      $entity = New-Object Microsoft.WindowsAzure.Storage.Table.DynamicTableEntity $partitionKey, $rowKey
      $entity.Properties.Add("Name", $name)
      $entity.Properties.Add("ID", $id)

      $result = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Insert($entity))
    }

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext $StorageAccountName -StorageAccountKey $StorageAccountKey
    $TableName = "Employees"

    #Retrieve the table if it already exists.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx -ErrorAction Ignore

    #Create a new table if it does not exist.
    if ($table -eq $null)
    {
       $table = New-AzureStorageTable –Name $TableName -Context $Ctx
    }

    #Add multiple entities to a table.
    Add-Entity $table "Partition1" "Row1" "Chris" 1
    Add-Entity $table "Partition1" "Row2" "Jessie" 2
    Add-Entity $table "Partition2" "Row1" "Christine" 3
    Add-Entity $table "Partition2" "Row2" "Steven" 4


#### Come eseguire query sulle entità di tabella
Per eseguire query su una tabella, usare la classe [Microsoft.WindowsAzure.Storage.Table.TableQuery](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tablequery.aspx) Il seguente esempio presuppone che lo script indicato nella sezione Come aggiungere le entità di questa guida sia stato già eseguito. Innanzitutto viene stabilita una connessione ad Archiviazione di Azure usando il contesto di archiviazione che include il nome dell'account di archiviazione e la relativa chiave di accesso. Successivamente, si tenta di recuperare la tabella "Employees" creata in precedenza usando il cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx). La chiamata del cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) nella classe Microsoft.WindowsAzure.Storage.Table.TableQuery crea un nuovo oggetto query. Nell'esempio vengono cercate le entità con il valore 1 nella colonna 'ID', come specificato in un filtro di stringa. Per informazioni dettagliate, vedere [Tabelle di query ed entità](http://msdn.microsoft.com/library/azure/dd894031.aspx). Quando si esegue questa query, vengono restituite tutte le entità che soddisfano i criteri di filtro.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    $TableName = "Employees"

    #Get a reference to a table.
    $table = Get-AzureStorageTable –Name $TableName -Context $Ctx

    #Create a table query.
    $query = New-Object Microsoft.WindowsAzure.Storage.Table.TableQuery

    #Define columns to select.
    $list = New-Object System.Collections.Generic.List[string]
    $list.Add("RowKey")
    $list.Add("ID")
    $list.Add("Name")

    #Set query details.
    $query.FilterString = "ID gt 0"
    $query.SelectColumns = $list
    $query.TakeCount = 20

    #Execute the query.
    $entities = $table.CloudTable.ExecuteQuery($query)

    #Display entity properties with the table format.
    $entities  | Format-Table PartitionKey, RowKey, @{ Label = "Name"; Expression={$_.Properties["Name"].StringValue}}, @{ Label = "ID"; Expression={$_.Properties[“ID”].Int32Value}} -AutoSize

#### Come eliminare le entità di tabella
È possibile eliminare un'entità usando le relative chiavi di riga e di partizione. Il seguente esempio presuppone che lo script indicato nella sezione Come aggiungere le entità di questa guida sia stato già eseguito. Innanzitutto viene stabilita una connessione ad Archiviazione di Azure usando il contesto di archiviazione che include il nome dell'account di archiviazione e la relativa chiave di accesso. Successivamente, si tenta di recuperare la tabella "Employees" creata in precedenza usando il cmdlet [Get-AzureStorageTable](http://msdn.microsoft.com/library/azure/dn806411.aspx) Se la tabella esiste, viene chiamato il metodo [Microsoft.WindowsAzure.Storage.Table.TableOperation.Retrieve](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.retrieve.aspx) per recuperare un'entità in base ai relativi valori di chiave di riga e di partizione. Quindi l'entità viene passata al metodo [Microsoft.WindowsAzure.Storage.Table.TableOperation.Delete](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.table.tableoperation.delete.aspx) per l'eliminazione.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Retrieve the table.
    $TableName = "Employees"
    $table = Get-AzureStorageTable -Name $TableName -Context $Ctx -ErrorAction Ignore

    #If the table exists, start deleting its entities.
    if ($table -ne $null)
    {
       #Together the PartitionKey and RowKey uniquely identify every  
       #entity within a table.
       $tableResult = $table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Retrieve(“Partition2”, "Row1"))
       $entity = $tableResult.Result;
    if ($entity -ne $null)
    {
       #Delete the entity.$table.CloudTable.Execute([Microsoft.WindowsAzure.Storage.Table.TableOperation]::Delete($entity))
    }
    }

## Come gestire le code e i messaggi della coda di Azure
Il servizio di archiviazione di accodamento di Azure consente di archiviare grandi quantità di messaggi ai quali è possibile accedere da qualsiasi parte del mondo mediante chiamate autenticate tramite HTTP o HTTPS. Questa sezione presuppone la conoscenza dei concetti relativi al servizio di archiviazione di accodamento di Azure. Per altre informazioni, vedere [Come utilizzare l'archiviazione di accodamento da .NET](storage-dotnet-how-to-use-queues.md).

Questa sezione spiega come gestire il servizio di archiviazione di accodamento di Azure usando Azure PowerShell. Gli scenari presentati includono **l’inserimento** e **l’eliminazione** dei messaggi in coda, nonché **la creazione**, **l’eliminazione**, e **il recupero di code**.

### Come creare una coda
Innanzitutto viene stabilita una connessione ad Archiviazione di Azure usando il contesto dell'account di archiviazione che include il nome dell'account di archiviazione e la relativa chiave di accesso. Successivamente, viene chiamato il cmdlet [New-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806382.aspx) per creare una coda denominata 'queuename'.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    $QueueName = "queuename"
    $Queue = New-AzureStorageQueue –Name $QueueName -Context $Ctx

Per informazioni sulle convenzioni di denominazione per il servizio di accodamento di Azure, vedere [Denominazione di code e metadati](http://msdn.microsoft.com/library/azure/dd179349.aspx).

### Come recuperare una coda
È possibile eseguire query e recuperare una coda specifica o un elenco di tutte le code di un account di archiviazione. L'esempio seguente mostra come recuperare una determinata coda usando il cmdlet [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx).

    #Retrieve a queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue –Name $QueueName –Context $Ctx

Se si chiama il cmdlet [Get-AzureStorageQueue](http://msdn.microsoft.com/library/azure/dn806377.aspx) senza parametri, si ottiene un elenco di tutte le code.

### Come eliminare una coda
Per eliminare una coda e tutti i messaggi che contiene, chiamare il metodo Remove-AzureStorageQueue. L'esempio seguente mostra come eliminare una determinata coda usando il cmdlet Remove-AzureStorageQueue.

    #Delete a queue.
    $QueueName = "yourqueuename"
    Remove-AzureStorageQueue –Name $QueueName –Context $Ctx

### Come gestire i messaggi della coda
Attualmente, Azure PowerShell non fornisce i cmdlet per gestire direttamente i messaggi della coda. Per eseguire operazioni sui messaggi della coda, è possibile usare le classi fornite [Libreria client di archiviazione di Azure per .NET](http://msdn.microsoft.com/library/azure/wa_storage_30_reference_home.aspx).

#### Come inserire un messaggio in una coda
Per inserire un messaggio in una coda esistente, creare innanzitutto una nuova istanza della classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx). Quindi, chiamare il metodo [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx). È possibile creare un oggetto CloudQueueMessage da una stringa in formato UTF-8 o da una matrice di byte.

Nell'esempio seguente viene mostrato come aggiungere un messaggio a una coda. Innanzitutto viene stabilita una connessione ad Archiviazione di Azure usando il contesto dell'account di archiviazione che include il nome dell'account di archiviazione e la relativa chiave di accesso. Successivamente viene recuperata la coda specificata usando il cmdlet [Get-AzureStorageQueue](https://msdn.microsoft.com/library/azure/dn806377.aspx). Se la coda esiste, il cmdlet [New-Object](http://technet.microsoft.com/library/hh849885.aspx) viene usato per creare un'istanza della classe [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx). Quindi, viene chiamato il metodo [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx) nell'oggetto messaggio per aggiungerlo a una coda. Di seguito è riportato il codice che consente di creare una coda e di inserire il messaggio 'MessageInfo':

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    #If the queue exists, add a new message.
    if ($Queue -ne $null)
    {
       # Create a new message using a constructor of the CloudQueueMessage class.
       $QueueMessage = New-Object "Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage" "MessageInfo"

       #Add a new message to the queue.
       $Queue.CloudQueue.AddMessage($QueueMessage)
    }


#### Come rimuovere il messaggio successivo dalla coda
Il codice consente di rimuovere un messaggio da una coda in due passaggi. Chiamando il metodo [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx), si ottiene il messaggio successivo in una coda. Un messaggio restituito da **GetMessage** diventa invisibile a qualsiasi altro codice che legge i messaggi dalla stessa coda. Per completare la rimozione del messaggio dalla coda, è necessario chiamare anche il metodo [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx). Questo processo in due passaggi di rimozione di un messaggio assicura che, qualora l'elaborazione di un messaggio non riesca a causa di errori hardware o software, un'altra istanza del codice sia in grado di ottenere lo stesso messaggio e di riprovare. Il codice chiama **DeleteMessage** immediatamente dopo l'elaborazione del messaggio.

    #Define the storage account and context.
    $StorageAccountName = "yourstorageaccount"
    $StorageAccountKey = "Storage key for yourstorageaccount ends with =="
    $Ctx = New-AzureStorageContext –StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

    #Retrieve the queue.
    $QueueName = "queuename"
    $Queue = Get-AzureStorageQueue -Name $QueueName -Context $ctx

    $InvisibleTimeout = [System.TimeSpan]::FromSeconds(10)

    #Get the message object from the queue.
    $QueueMessage = $Queue.CloudQueue.GetMessage($InvisibleTimeout)
    #Delete the message.
    $Queue.CloudQueue.DeleteMessage($QueueMessage)

## Come gestire condivisioni file e file di Azure
L'archiviazione file di Azure offre un'archiviazione condivisa per le applicazioni che usano il protocollo SMB 2.1 standard. Le macchine virtuali e i servizi cloud di Microsoft Azure possono condividere dati file tra componenti delle applicazioni tramite le condivisioni montate e le applicazioni locali possono accedere ai dati file in una condivisione tramite l'API dell'archiviazione file o Azure PowerShell.

Per informazioni dettagliate sull'archiviazione file di Azure, vedere [Come usare l'archiviazione file di Azure](storage-dotnet-how-to-use-files.md) e [API REST del servizio File](http://msdn.microsoft.com/library/azure/dn167006.aspx).

## Come impostare ed eseguire query di Analisi archiviazione
È possibile utilizzare [Analisi archiviazione di Azure](storage-analytics.md) per raccogliere le metriche per gli account di archiviazione di Azure e per registrare i dati sulle richieste inviate all'account di archiviazione. È possibile usare le metriche di archiviazione per monitorare l'integrità di un account di archiviazione e Registrazione archiviazione per diagnosticare e risolvere i problemi relativi al proprio account di archiviazione. Per impostazione predefinita, le Metriche di archiviazione non è abilitato per i servizi di archiviazione. È possibile abilitare il monitoraggio usando il portale di gestione di Azure o Windows PowerShell. In alternativa, è possibile abilitarlo a livello di codice tramite un'API di archiviazione. La Registrazione archiviazione viene eseguita sul lato server e consente all'utente di registrare i dettagli delle richieste, riuscite e non riuscite, nel proprio account di archiviazione. Questi log consentono di visualizzare i dettagli delle operazioni di lettura, scrittura ed eliminazione a fronte delle proprie tabelle, code e BLOB, nonché i motivi per cui le richieste non sono riuscite.

Per informazioni su come abilitare e visualizzare i dati di Metriche di archiviazione con PowerShell, vedere [Come abilitare Metriche di archiviazione usando PowerShell](http://msdn.microsoft.com/library/azure/dn782843.aspx#HowtoenableStorageMetricsusingPowerShell).

Per informazioni su come abilitare e recuperare i dati di registrazione di archiviazione con PowerShell, vedere [Come abilitare la registrazione di archiviazione con PowerShell](http://msdn.microsoft.com/library/azure/dn782840.aspx#HowtoenableStorageLoggingusingPowerShell) e [Trovare i dati del log di registrazione di archiviazione](http://msdn.microsoft.com/library/azure/dn782840.aspx#FindingyourStorageLogginglogdata). Per informazioni dettagliate sull'uso di Metriche di archiviazione e Registrazione archiviazione per la risoluzione dei problemi di archiviazione, vedere [Monitoraggio, diagnosi e risoluzione dei problemi del servizio di archiviazione di Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## Come gestire la firma di accesso condiviso (SAS) e criteri di accesso archiviati
Le firme di accesso condiviso costituiscono una parte essenziale del modello di sicurezza di qualsiasi applicazione che usa il servizio di archiviazione di Azure. Le firme di accesso condiviso sono utili per offrire autorizzazioni limitate all'account di archiviazione ai client ai quali non si desidera fornire la chiave dell'account. Per impostazione predefinita, solo il proprietario dell'account di archiviazione può accedere a BLOB, tabelle e code all'interno dell'account. Se il servizio o l'applicazione deve rendere disponibili queste risorse ad altri client senza condividere la chiave di accesso, sono disponibili tre opzioni:

- Impostare le autorizzazioni di un contenitore per consentire l'accesso in lettura anonimo al contenitore e ai relativi BLOB. Questa operazione non è consentita per le tabelle o le code.
- Usare una firma di accesso condiviso che concede diritti di accesso limitati a contenitori, BLOB, code e tabelle per un intervallo di tempo specifico.
- Usare criteri di accesso archiviati per ottenere un livello di controllo aggiuntivo sulle firme di accesso condiviso per un contenitore o i relativi BLOB, per una coda o per una tabella. I criteri di accesso archiviati consentono di modificare l'ora di inizio, la scadenza o le autorizzazioni per una firma o di revocare la firma dopo che è stata emessa.

Una firma di accesso condiviso può assumere una delle due forme seguenti:

- **SAS Ad hoc **quando si crea una firma di accesso condiviso ad hoc, l'ora di inizio, la scadenza e le autorizzazioni vengono tutte specificate nell'URI corrispondente. Questo tipo di firma di accesso condiviso può essere creato per un contenitore, un BLOB, una tabella e una coda e non è revocabile.
- **SAS con politica di accesso archiviazione:**i criteri di accesso archiviati vengono definiti per un contenitore di risorse, ovvero un contenitore BLOB, una tabella o una coda, e possono essere usati per gestire i vincoli per una o più firme di accesso condiviso. Quando si associa una firma di accesso condiviso a criteri di accesso archiviati, la firma eredita i vincoli, ovvero ora di inizio, scadenza e autorizzazioni, definiti per i criteri di accesso archiviati. Questo tipo di firma di accesso condiviso è revocabile.

Per ulteriori informazioni, vedere [il tutorial per le firme di accesso condiviso](storage-dotnet-shared-access-signature-part-1.md) e la [Gestione dell'accesso alle risorse di archiviazione Azure](storage-manage-access-to-resources.md).

Nelle sezioni successive verrà illustrato come creare un token di firma di accesso condiviso e criteri di accesso archiviati per le tabelle di Azure. Azure PowerShell fornisce cmdlet simili per contenitori, BLOB e code. Per eseguire gli script in questa sezione, scaricare [Azure PowerShell versione 0.8.14](http://go.microsoft.com/?linkid=9811175&clcid=0x409) o successiva.

### Come creare criteri in base al token di firma di accesso condiviso
Usare il cmdlet New-AzureStorageTableStoredAccessPolicy per creare nuovi criteri di accesso archiviati. Chiamare quindi il nuovo cmdlet [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) per creare un nuovo token di firma di accesso condiviso basato sui criteri per una tabella di archiviazione Azure.

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx
    New-AzureStorageTableSASToken -Name $tableName -Policy $policy -Context $Ctx

### Come creare un token di firma di accesso condiviso ad hoc (non revocabile)
Usare il cmdlet [New-AzureStorageTableSASToken](http://msdn.microsoft.com/library/azure/dn806400.aspx) per creare un nuovo token di firma di accesso condiviso ad hoc (non revocabile) per una tabella di archiviazione di Azure:

    New-AzureStorageTableSASToken -Name $tableName -Permission "rqud" -StartTime "2015-01-01" -ExpiryTime "2015-02-01" -Context $Ctx

### Come creare criteri di accesso archiviati
Usare il cmdlet New-AzureStorageTableStoredAccessPolicy per creare nuovi criteri di accesso archiviati per una tabella di archiviazione di Azure:

    $policy = "policy1"
    New-AzureStorageTableStoredAccessPolicy -Name $tableName -Policy $policy -Permission "rd" -StartTime "2015-01-01" -ExpiryTime "2016-01-01" -Context $Ctx

### Come aggiornare criteri di accesso archiviati
Usare il cmdlet Set-AzureStorageTableStoredAccessPolicy per aggiornare criteri di accesso archiviati esistenti per una tabella di archiviazione di Azure:

    Set-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Permission "rd" -NoExpiryTime -NoStartTime -Context $Ctx

### Come eliminare criteri di accesso archiviati
Utilizzare il cmdlet Remove-AzureStorageTableStoredAccessPolicy per eliminare criteri di accesso archiviati in una tabella di archiviazione di Azure:

    Remove-AzureStorageTableStoredAccessPolicy -Policy $policy -Table $tableName -Context $Ctx


## Come usare Archiviazione di Azure per il governo degli Stati Uniti e Azure Cina
Un ambiente Azure è una distribuzione indipendente di Microsoft Azure, ad esempio [Azure Government per il governo degli Stati Unit](http://azure.microsoft.com/features/gov/), [AzureCloud per Azure globale](https://manage.windowsazure.com), e [AzureChinaCloud per Azure gestito da 21Vianet in Cina](http://www.windowsazure.cn/). È possibile distribuire nuovi ambienti Azure per il governo degli Stati Uniti e Azure Cina.

Per usare Archiviazione di Azure con AzureChinaCloud, è necessario creare un contesto di archiviazione associato ad AzureChinaCloud. Seguire questi passaggi per iniziare:

1.	Eseguire il cmdlet [Get-AzureEnvironment](https://msdn.microsoft.com/library/azure/dn790368.aspx) per visualizzare gli ambienti Azure disponibili:

    `Get-AzureEnvironment`

2.	Aggiungere un account Azure Cina a Windows PowerShell:

    `Add-AzureAccount –Environment AzureChinaCloud`

3.	Creare un contesto di archiviazione per un account AzureChinaCloud:

    	$Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment AzureChinaCloud

Per utilizzare l'archiviazione di Azure con [il governo degli Stati Uniti](http://azure.microsoft.com/features/gov/), è necessario definire un nuovo ambiente e creare un nuovo contesto di archiviazione con questo ambiente:

1. Chiamare il cmdlet [Add-AzureEnvironment](http://msdn.microsoft.com/library/azure/dn790364.aspx) per creare un nuovo ambiente Azure per il data center privato.

    	Add-AzureEnvironment -Name $EnvironmentName -PublishSettingsFileUrl $publishSettingsFileUrl -ServiceEndpoint $serviceEndpoint -ManagementPortalUrl $managementPortalUrl -StorageEndpoint $storageEndpoint -ActiveDirectoryEndpoint $activeDirectoryEndpoint -ResourceManagerEndpoint $resourceManagerEndpoint -GalleryEndpoint $galleryEndpoint -ActiveDirectoryServiceEndpointResourceId $activeDirectoryServiceEndpointResourceId -GraphEndpoint $graphEndpoint -SubscriptionDataFile $subscriptionDataFile

2. Eseguire il cmdlet [New-AzureStorageContext](http://msdn.microsoft.com/library/azure/dn806380.aspx) per creare un nuovo contesto di archiviazione per questo nuovo ambiente, come illustrato di seguito.

	    $Ctx = New-AzureStorageContext -StorageAccountName $AccountName -StorageAccountKey $AccountKey> -Environment $EnvironmentName

Per altre informazioni, vedere:

- [Guida per gli sviluppatori di Microsoft Azure Government](../azure-government-developer-guide.md).
- [Note degli sviluppatori per le applicazioni di Azure in Cina](https://msdn.microsoft.com/library/azure/dn578439.aspx)

## Passaggi successivi
In questa guida è stato appreso come gestire Archiviazione di Azure con Azure PowerShell. Per altre informazioni, vedere gli articoli e le risorse correlati seguenti:

- [Documentazione di Archiviazione di Azure](http://azure.microsoft.com/documentation/services/storage/)
- [Riferimento MSDN ad Archiviazione di Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- [Cmdlet di PowerShell per Archiviazione di Azure](http://msdn.microsoft.com/library/azure/dn806401.aspx)
- [Riferimenti Windows PowerShell](https://msdn.microsoft.com/library/ms714469.aspx)

[Image1]: ./media/storage-powershell-guide-full/Subscription_currentportal.png
[Image2]: ./media/storage-powershell-guide-full/Subscription_Previewportal.png
[Image3]: ./media/storage-powershell-guide-full/Blobdownload.png


[Getting started with Azure Storage and PowerShell in 5 minutes]: #getstart
[Prerequisites for using Azure PowerShell with Azure Storage]: #pre
[How to manage storage accounts in Azure]: #manageaccount
[How to set a default Azure subscription]: #setdefsub
[How to create a new Azure storage account]: #createaccount
[How to set a default Azure storage account]: #defaultaccount
[How to list all Azure storage accounts in a subscription]: #listaccounts
[How to create an Azure storage context]: #createctx
[How to manage Azure blobs and blob snapshots]: #manageblobs
[How to create a container]: #container
[How to upload a blob into a container]: #uploadblob
[How to download blobs from a container]: #downblob
[How to copy blobs from one storage container to another]: #copyblob
[How to delete a blob]: #deleteblob
[How to manage Azure blob snapshots]: #manageshots
[How to create a blob snapshot]: #createshot
[How to list snapshots of a blob]: #listshot
[How to copy a snapshot of a blob]: #copyshot
[How to manage Azure tables and table entities]: #managetables
[How to create a table]: #createtable
[How to retrieve a table]: #gettable
[How to delete a table]: #remtable
[How to manage table entities]: #mngentity
[How to add table entities]: #addentity
[How to query table entities]: #queryentity
[How to delete table entities]: #deleteentity
[How to manage Azure queues and queue messages]: #managequeues
[How to create a queue]: #createqueue
[How to retrieve a queue]: #getqueue
[How to delete a queue]: #remqueue
[How to manage queue messages]: #mngqueuemsg
[How to insert a message into a queue]: #addqueuemsg
[How to de-queue at the next message]: #dequeuemsg
[How to manage Azure file shares and files]: #files
[How to set and query storage analytics]: #stganalytics
[How to manage Shared Access Signature (SAS) and Stored Access Policy]: #sas
[How to use Azure Storage for U.S. government and Azure China]: #gov
[Next Steps]: #next
 

<!---HONumber=July15_HO2-->