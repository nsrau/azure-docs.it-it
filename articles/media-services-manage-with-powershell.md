<properties 
	pageTitle="Gestire gli account di Servizi multimediali di Azure con PowerShell" 
	description="Informazioni su come gestire gli account di Servizi multimediali di Azure con i cmdlet di PowerShell." 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/14/2015" 
	ms.author="juliako"/>


# Gestire gli account di Servizi multimediali di Azure con PowerShell

## Informazioni generali 

Questi articoli illustrano come usare i cmdlet di PowerShell per gestire gli account di Servizi multimediali di Azure.

>[AZURE.NOTE]
> Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">versione di valutazione gratuita di Azure</a>.

## Installare i cmdlet di PowerShell di Microsoft Azure

Per installare i cmdlet di PowerShell di Azure più recenti, vedere la pagina relativa all'[installazione e configurazione di Azure PowerShell](powershell-install-configure.md)

## Selezionare la sottoscrizione Azure

Dopo aver installato e configurato i cmdlet di PowerShell, è necessario specificare quale sottoscrizione si desidera usare. 

Per ottenere un elenco delle sottoscrizioni disponibili, eseguire il seguente cmdlet:

	PS C:> Get-AzureSubscription

Selezionare quindi una sottoscrizione eseguendo il seguente comando:

	PS C:> Select-AzureSubscription "TestSubscription"

 
## Ottenere il nome dell'account di archiviazione

Servizi multimediali di Azure usa Archiviazione di Azure per archiviare il contenuto multimediale. Quando si crea un nuovo account di Servizi multimediali, è necessario associarlo a un account di archiviazione. L'account di archiviazione deve appartenere alla stessa sottoscrizione che si intende usare per l'account di Servizi multimediali. 

In questo esempio viene usato un account di archiviazione esistente. Il cmdlet [Get-AzureStorageAccount](https://msdn.microsoft.com/library/azure/dn495134.aspx) ottiene gli account di archiviazione nella sottoscrizione corrente. Ottenere il nome (StorageAccountName) dell'account di archiviazione al quale si desidera associare l'account di Servizi multimediali.

	StorageAccountDescription :
	AffinityGroup             :
	Location                  : East US
	GeoReplicationEnabled     : True
	GeoPrimaryLocation        : East US
	GeoSecondaryLocation      : West US
	Label                     : storagetest001
	StorageAccountStatus      : Created
	StatusOfPrimary           : Available
	StatusOfSecondary         : Available
	Endpoints                 : {https://storagetest001.blob.core.windows.net/,
	                            https://storagetest001.queue.core.windows.net/,
	                            https://storagetest001.table.core.windows.net/}
	AccountType               : Standard_GRS
	StorageAccountName        : storatetest001
	OperationDescription      : Get-AzureStorageAccount
	OperationId               : e919dd56-7691-96db-8b3c-2ceee891ae5d
	OperationStatus           : Succeeded

## Creare un nuovo account di Servizi multimediali

Per creare un nuovo account di Servizi multimediali di Azure, usare il cmdlet [New-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495286.aspx) che fornisce il nome dell'account, il percorso del data center in cui verrà creato e il nome dell'account di archiviazione. 


	PS C:> New-AzureMediaServicesAccount -Name "amstestaccount001" -StorageAccountName "storagetest001" -Location "East US"

## Ottenere gli account di Servizi multimediali

Una volta creato uno o più account di Servizi multimediali, è possibile elencare le informazioni tramite il cmdlet [Get-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495286.aspx)

	
	PS C:> Get-AzureMediaServicesAccount
	
	AccountId		Name				State
	---------       ----       			 -----
	xxxxxxxxxx      amstestaccount001   Active

Fornendo il parametro Name si otterranno informazioni più dettagliate, incluse le chiavi dell'account.

	PS C:> Get-AzureMediaServicesAccount -Name amstestaccount001

## Rigenerare le chiavi di accesso a Servizi multimediali

Per aggiornare la chiave di accesso primaria o secondaria a Servizi multimediali, usare il cmdlet [New-AzureMediaServicesKey](https://msdn.microsoft.com/library/azure/dn495215.aspx). 
È necessario fornire il nome dell'account e specificare la chiave che si desidera rigenerare (primaria o secondaria). 

Se non si desidera che PowerShell ponga domande di conferma, specificare un'opzione -Force.

	PS C:> New-AzureMediaServicesKey -Name "amstestaccount001" -KeyType "Primary" -Force

## Rimuovere l'account di Servizi multimediali

Per eliminare l'account di Servizi multimediali di Azure, usare il cmdlet [Remove-AzureMediaServicesAccount](https://msdn.microsoft.com/library/azure/dn495220.aspx).

	PS C:> Remove-AzureMediaServicesAccount -Name "amstestaccount001" -Force



<!--HONumber=52-->