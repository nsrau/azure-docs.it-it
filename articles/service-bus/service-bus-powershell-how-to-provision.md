<properties
	pageTitle="Gestire il bus di servizio con PowerShell"
	description="Gestire il bus di servizio con gli script PowerShell invece di .NET"
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2015"
	ms.author="sethm"/>

# Gestire il bus di servizio con PowerShell

## Panoramica

Microsoft Azure PowerShell è un ambiente di scripting che può essere utilizzato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. In questo articolo, si apprenderà come utilizzare PowerShell per il provisioning e la gestione delle entità bus di servizio come spazi dei nomi, code e hub evento tramite una console locale Azure PowerShell.

## Prerequisiti

Per eseguire le procedure descritte nell'articolo è necessario:

- Una sottoscrizione di Azure. Azure è una piattaforma basata su sottoscrizione. Per altre
informazioni su come ottenere una sottoscrizione, vedere [Opzioni di acquisto][azure-purchase-options],
[Offerte per i membri][azure-member-offers] o [Versione di prova gratuita][azure-free-trial].

- Un computer con Azure PowerShell. Per istruzioni, vedere [Installare e configurare Azure PowerShell][powershell-install-configure].

- Conoscenza generale degli script PowerShell, dei pacchetti NuGet e di .NET Framework.

## Inclusione di un riferimento all'assembly .NET per il bus di servizio

Il numero di cmdlet PowerShell disponibili per la gestione del bus di servizio è limitato. Per il provisioning
delle entità non esposte tramite i cmdlet esistenti, verrà utilizzato il client .NET per
il bus di servizio tramite il [pacchetto NuGet del bus di servizio][].

Innanzitutto, è opportuno assicurarsi che lo script sia in grado di individuare l'assembly **Microsoft.ServiceBus.dll**, che viene installato con il pacchetto NuGet. Per essere flessibile, lo script eseguirà i seguenti passaggi:

1. Lo script determina il percorso in cui è stato chiamato.
2. Scorre il percorso finché trova una cartella denominata `pacchetti`. Si tratta della cartella creata al momento dell'installazione dei pacchetti NuGet.
3. Viene ricercato in modo ricorsivo nella cartella `pacchetti`, per trovare un assembly denominato **Microsoft.ServiceBus.dll**.
4. Viene quindi fatto riferimento all'assembly in modo che i tipi siano disponibili per un utilizzo successivo.

Di seguito viene illustrato in che modo questi passaggi vengono implementati in uno script PowerShell:

```powershell

try
{
 # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
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

Quando si lavora con gli spazi dei nomi del bus di servizio, è possibile utilizzare due cmdlet invece dell'SDK .NET. In modo specifico, è possibile utilizzare [Get-AzureSBNamespace][] e [New-AzureSBNamespace][].

Nel nostro esempio verranno create delle variabili locali nello script, `$Namespace` e `$Location`.
- `$Namespace` è il nome dello spazio dei nomi del bus di servizio con cui si desidera lavorare.
- `$Location` identifica il data center in cui si eseguirà il provisioning dello spazio dei nomi.
- `$CurrentNamespace` archivia lo spazio dei nomi di riferimento che viene recuperato (o creato).

In uno script effettivo, `$Namespace` e `$Location` possono essere passati come parametri.

Questa parte dello script tenterà di

1. recuperare lo spazio dei nomi di un bus di servizio con il nome fornito.
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

Per il provisioning di altre entità del servizio bus, viene creata un'istanza dell'oggetto `NamespaceManager` dall'SDK.
È possibile utilizzare il cmdlet [Get-AzureSBAuthorizationRule][] per recuperare una regola di autorizzazione utilizzata per fornire una stringa di connessione. Verrà archiviato un riferimento all'istanza `NamespaceManager` nella variabile `$NamespaceManager`. `$NamespaceManager` verrà utilizzato in seguito nello script per il provisioning di altre entità.

``` powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Output "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Output "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## Provisioning di altre entità del bus di servizio

Per il provisioning di altre entità, ad esempio, code, argomenti e hub evento, è possibile utilizzare l'API [.NET per il bus di servizio][dotnet-servicebus-api]. In questo articolo verranno illustrati unicamente gli hub evento, anche se i passaggi per le altre entità sono simili. Inoltre, al termine di questo articolo si fa riferimento a esempi più dettagliati, incluse altre entità.

In questa parte dello script, verranno create altre quattro variabili locali. Tali variabili verranno utilizzate per creare istanze di `EventHubDescription`. Verranno effettuate le seguenti operazioni:

1. Utilizzando l'oggetto `NamespaceManager`, verrà verificata innanzitutto l'esistenza dell'hub evento `$Path`.
2. Se non esiste, verrà creato un  `EventHubDescription` che verrà passato al metodo  `CreateEventHubIfNotExists` della classe  `NamespaceManager`.
3. Quando si è certi che l'hub evento è disponibile, verrà creato un gruppo di consumatori tramite  `ConsumerGroupDescription` e `NamespaceManager`.

``` powershell

$Path = "MyEventHub"
$PartitionCount = 12
$MessageRetentionInDays = 7
$UserMetadata = $null
$ConsumerGroupName = "MyConsumerGroup"

# Check if the event hub already exists
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

## Passaggi successivi

In questo articolo viene fornita una descrizione di base per il provisioning delle entità del bus di servizio tramite PowerShell. Tutte le operazioni che è possibile eseguire mediante le librerie client .NET, possono essere eseguite anche in uno script PowerShell.

Esempi più dettagliati sono disponibili sui seguenti post di blog:

- [Come creare code, argomenti e sottoscrizioni del bus di servizio tramite uno script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
- [Come creare uno spazio dei nomi del bus di servizio e un hub evento tramite uno script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Sono disponibili per il download anche alcuni script predefiniti:
- [Script PowerShell del bus di servizio](https://code.msdn.microsoft.com/windowsazure/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[powershell-install-configure]: ../install-configure-powershell/
[pacchetto NuGet del bus di servizio]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495122.aspx
[New-AzureSBNamespace]: https://msdn.microsoft.com/library/azure/dn495165.aspx
[Get-AzureSBAuthorizationRule]: https://msdn.microsoft.com/library/azure/dn495113.aspx
[dotnet-servicebus-api]: https://msdn.microsoft.com/library/microsoft.servicebus.aspx

<!--HONumber=47-->
 