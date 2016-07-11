<properties
    pageTitle="Usare PowerShell per gestire le risorse del bus di servizio e di Hub eventi | Microsoft Azure"
    description="Uso di PowerShell per creare e gestire le risorse del bus di servizio e di Hub eventi"
    services="service-bus"
    documentationCenter=".NET"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="06/24/2016"
    ms.author="sethm"/>

# Usare PowerShell per gestire le risorse del bus di servizio e di Hub eventi

Microsoft Azure PowerShell è un ambiente di scripting che può essere usato per controllare e automatizzare la distribuzione e la gestione dei servizi di Azure. Questo articolo descrive come usare PowerShell per il provisioning e la gestione delle entità del bus di servizio come spazi dei nomi, code e hub eventi tramite una console locale di Azure PowerShell.

## Prerequisiti

Prima di iniziare, è necessario disporre di quanto segue:

- Una sottoscrizione di Azure. Azure è una piattaforma basata su sottoscrizione. Per altre informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][], [Offerte per i membri][] oppure [Account gratuito][].

- Un computer con Azure PowerShell. Per istruzioni, vedere [Come installare e configurare Azure PowerShell][].

- Conoscenza generale degli script di PowerShell, dei pacchetti NuGet e di .NET Framework.

## Includere un riferimento all'assembly .NET per il bus di servizio

Il numero di cmdlet PowerShell disponibili per la gestione del bus di servizio è limitato. Per eseguire il provisioning di entità non esposte tramite i cmdlet esistenti, è possibile usare il client .NET per il bus di servizio da PowerShell facendo riferimento al [pacchetto NuGet del bus di servizio].

Innanzitutto, assicurarsi che lo script sia in grado di individuare l'assembly **Microsoft.ServiceBus.dll**, che viene installato con il pacchetto NuGet. Per essere flessibile, lo script esegue i passaggi seguenti:

1. Determina il percorso da cui è stato richiamato.
2. Scorre il percorso finché trova una cartella denominata `packages`. Questa cartella viene creata durante l'installazione dei pacchetti NuGet.
3. Cerca in modo ricorsivo nella cartella `packages`, per trovare un assembly denominato **Microsoft.ServiceBus.dll**.
4. Fa quindi riferimento all'assembly in modo che i tipi siano disponibili per un uso successivo.

Di seguito viene illustrato in che modo questi passaggi vengono implementati in uno script di PowerShell:

```powershell

try
{
    # IMPORTANT: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Output "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Output "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error("Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script.")
}

```

## Provisioning di uno spazio dei nomi del bus di servizio

Quando si usano spazi dei nomi del bus di servizio, esistono due cmdlet che è possibile usare invece di .NET SDK: [Get-AzureSBNamespace][] e [New-AzureSBNamespace][].

Questo esempio crea alcune variabili locali nello script: `$Namespace` e `$Location`.

- `$Namespace` è il nome dello spazio dei nomi del bus di servizio che si intende usare.
- `$Location` identifica il data center in cui si eseguirà il provisioning dello spazio dei nomi.
- `$CurrentNamespace` archivia lo spazio dei nomi di riferimento che viene recuperato (o creato).

In uno script effettivo, `$Namespace` e `$Location` possono essere passati come parametri.

Questa parte dello script esegue le operazioni seguenti:

1. Tenta di recuperare uno spazio dei nomi del bus di servizio con il nome specificato.
2. Se lo spazio dei nomi viene trovato, viene segnalato ciò che viene trovato.
3. Se lo spazio dei nomi non viene trovato, viene creato lo spazio dei nomi e quindi viene recuperato lo spazio dei nomi appena creato.

	``` powershell

	$Namespace = "MyServiceBusNS"
	$Location = "West US"

	# Query to see if the namespace currently exists
	$CurrentNamespace = Get-AzureSBNamespace -Name $Namespace

	# Check if the namespace already exists or needs to be created
	if ($CurrentNamespace)
	{
	    Write-Output "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
	}
	else
	{
	    Write-Host "The [$Namespace] namespace does not exist."
	    Write-Output "Creating the [$Namespace] namespace in the [$Location] region..."
	    New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace false -NamespaceType Messaging
	    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
	    Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
	}
	```
Per il provisioning di altre entità del bus di servizio, viene creata un'istanza dell'oggetto `NamespaceManager` dall'SDK. È possibile usare il cmdlet [Get-AzureSBAuthorizationRule][] per recuperare una regola di autorizzazione usata per fornire una stringa di connessione. Questo esempio archivia un riferimento all'istanza `NamespaceManager` nella variabile `$NamespaceManager`. Lo script in seguito usa `$NamespaceManager` per il provisioning di altre entità.

	``` powershell
	$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
	# Create the NamespaceManager object to create the Event Hub
	Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
	$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
	Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
	```

## Provisioning di altre entità del bus di servizio

Per il provisioning di altre entità, ad esempio, code, argomenti e hub eventi, è possibile usare l'[API .NET per il bus di servizio][]. Alla fine di questo articolo sono presenti riferimenti ad esempi più dettagliati, incluse altre entità.

### Creare un hub eventi

Questa parte dello script crea altre quattro variabili locali che vengono usate per creare istanze dell'oggetto `EventHubDescription`. Lo script effettua le operazioni seguenti:

1. Usando l'oggetto `NamespaceManager`, viene verificata innanzitutto l'esistenza dell'hub eventi identificato da `$Path`.
2. Se non esiste, viene creato un oggetto `EventHubDescription` che viene passato al metodo `CreateEventHubIfNotExists` della classe `NamespaceManager`.
3. Dopo aver determinato la disponibilità dell'hub eventi, viene creato un gruppo di clienti tramite `ConsumerGroupDescription` e `NamespaceManager`.

	``` powershell

	$Path  = "MyEventHub"
	$PartitionCount = 12
	$MessageRetentionInDays = 7
	$UserMetadata = $null
	$ConsumerGroupName = "MyConsumerGroup"

	# Check if the Event Hub already exists
	if ($NamespaceManager.EventHubExists($Path))
	{
	    Write-Output "The [$Path] event hub already exists in the [$Namespace] namespace."  
	}
	else
	{
	    Write-Output "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
	    $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
	    $EventHubDescription.PartitionCount = $PartitionCount
	    $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
	    $EventHubDescription.UserMetadata = $UserMetadata
	    $EventHubDescription.Path = $Path
	    $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription);
	    Write-Output "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
	}

	# Create the consumer group if it doesn't exist
	Write-Output "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
	$ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
	$ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
	$NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription);
	Write-Output "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
	```

### Creare una coda

Per creare una coda o un argomento, eseguire una verifica dello spazio dei nomi come nella sezione precedente.

	if ($NamespaceManager.QueueExists($Path))
	{
	    Write-Output "The [$Path] queue already exists in the [$Namespace] namespace."
	}
	else
	{
	    Write-Output "Creating the [$Path] queue in the [$Namespace] namespace..."
	    $QueueDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.QueueDescription -ArgumentList $Path
	    if ($AutoDeleteOnIdle -ge 5)
	    {
	        $QueueDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
	    }
	    if ($DefaultMessageTimeToLive -gt 0)
	    {
	        $QueueDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
	    }
	    if ($DuplicateDetectionHistoryTimeWindow -gt 0)
	    {
	        $QueueDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
	    }
	    $QueueDescription.EnableBatchedOperations = $EnableBatchedOperations
	    $QueueDescription.EnableDeadLetteringOnMessageExpiration = $EnableDeadLetteringOnMessageExpiration
	    $QueueDescription.EnableExpress = $EnableExpress
	    $QueueDescription.EnablePartitioning = $EnablePartitioning
	    $QueueDescription.ForwardDeadLetteredMessagesTo = $ForwardDeadLetteredMessagesTo
	    $QueueDescription.ForwardTo = $ForwardTo
	    $QueueDescription.IsAnonymousAccessible = $IsAnonymousAccessible
	    if ($LockDuration -gt 0)
	    {
	        $QueueDescription.LockDuration = [System.TimeSpan]::FromSeconds($LockDuration)
	    }
	    $QueueDescription.MaxDeliveryCount = $MaxDeliveryCount
	    $QueueDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
	    $QueueDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
	    $QueueDescription.RequiresSession = $RequiresSession
	    if ($EnablePartitioning)
	    {
	        $QueueDescription.SupportOrdering = $False
	    }
	    else
	    {
	        $QueueDescription.SupportOrdering = $SupportOrdering
	    }
	    $QueueDescription.UserMetadata = $UserMetadata
	    $NamespaceManager.CreateQueue($QueueDescription);
	}

### Creare un argomento

	if ($NamespaceManager.TopicExists($Path))
	{
	    Write-Output "The [$Path] topic already exists in the [$Namespace] namespace."
	}
	else
	{
	    Write-Output "Creating the [$Path] topic in the [$Namespace] namespace..."
	    $TopicDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.TopicDescription -ArgumentList $Path
	    if ($AutoDeleteOnIdle -ge 5)
	    {
	        $TopicDescription.AutoDeleteOnIdle = [System.TimeSpan]::FromMinutes($AutoDeleteOnIdle)
	    }
	    if ($DefaultMessageTimeToLive -gt 0)
	    {
	        $TopicDescription.DefaultMessageTimeToLive = [System.TimeSpan]::FromMinutes($DefaultMessageTimeToLive)
	    }
	    if ($DuplicateDetectionHistoryTimeWindow -gt 0)
	    {
	        $TopicDescription.DuplicateDetectionHistoryTimeWindow = [System.TimeSpan]::FromMinutes($DuplicateDetectionHistoryTimeWindow)
	    }
	    $TopicDescription.EnableBatchedOperations = $EnableBatchedOperations
	    $TopicDescription.EnableExpress = $EnableExpress
	    $TopicDescription.EnableFilteringMessagesBeforePublishing = $EnableFilteringMessagesBeforePublishing
	    $TopicDescription.EnablePartitioning = $EnablePartitioning
	    $TopicDescription.IsAnonymousAccessible = $IsAnonymousAccessible
	    $TopicDescription.MaxSizeInMegabytes = $MaxSizeInMegabytes
	    $TopicDescription.RequiresDuplicateDetection = $RequiresDuplicateDetection
	    if ($EnablePartitioning)
	    {
	        $TopicDescription.SupportOrdering = $False
	    }
	    else
	    {
	        $TopicDescription.SupportOrdering = $SupportOrdering
	    }
	    $TopicDescription.UserMetadata = $UserMetadata
	    $NamespaceManager.CreateTopic($TopicDescription);
	}

## Passaggi successivi

Questo articolo ha fornito una descrizione di base per il provisioning delle entità del bus di servizio tramite PowerShell. Anche se è disponibile un numero limitato di cmdlet di PowerShell per la gestione delle entità di messaggistica del bus di servizio, facendo riferimento all'assembly Microsoft.ServiceBus.dll, praticamente qualsiasi operazione che è possibile eseguire usando le librerie client di .NET può essere eseguita in uno script di PowerShell.

Esempi più dettagliati sono disponibili nei post dei blog seguenti:

- [Come creare code, argomenti e sottoscrizioni del bus di servizio tramite uno script di PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Come creare uno spazio dei nomi del bus di servizio e un hub eventi tramite uno script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Sono disponibili per il download anche alcuni script predefiniti:

- [Script PowerShell del bus di servizio](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[Opzioni di acquisto]: http://azure.microsoft.com/pricing/purchase-options/
[Offerte per i membri]: http://azure.microsoft.com/pricing/member-offers/
[Account gratuito]: http://azure.microsoft.com/pricing/free-trial/
[pacchetto NuGet del bus di servizio]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[API .NET per il bus di servizio]: https://msdn.microsoft.com/it-IT/library/azure/mt419900.aspx
[Come installare e configurare Azure PowerShell]: ../powershell-install-configure.md

<!---HONumber=AcomDC_0629_2016-->