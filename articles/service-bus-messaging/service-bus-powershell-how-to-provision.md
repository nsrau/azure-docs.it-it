---
title: Gestire il bus di servizio di Azure con PowerShell | Documentazione Microsoft
description: Gestire il bus di servizio con gli script PowerShell
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: aff8e2ce-bc8b-489f-aca9-a18782be0375
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/12/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 61f31c8ad0463776937f366d145595f04cc42d2e
ms.openlocfilehash: 24dd8757942488aa8364cc6cf968cfc17299699f


---
# <a name="manage-service-bus-with-powershell"></a>Gestire il bus di servizio con PowerShell
## <a name="overview"></a>Panoramica
Microsoft Azure PowerShell è un ambiente di scripting che può essere utilizzato per controllare e automatizzare la distribuzione e la gestione dei carichi di lavoro in Azure. Questo articolo descrive come usare PowerShell per il provisioning e la gestione delle entità del bus di servizio come spazi dei nomi, code e hub eventi tramite una console locale di Azure PowerShell.

## <a name="prerequisites"></a>Prerequisiti
Prima di iniziare a leggere questo articolo, verificare di avere i prerequisiti seguenti:

* Una sottoscrizione di Azure. Azure è una piattaforma basata su sottoscrizione. Per altre informazioni su come ottenere una sottoscrizione, vedere le [opzioni di acquisto][Purchase Options], le [offerte per i membri][Member Offers] oppure la [versione di valutazione gratuita][Free Trial].
* Un computer con Azure PowerShell. Per istruzioni, vedere [Installare e configurare Azure PowerShell][Install and configure Azure PowerShell].
* Conoscenza generale degli script di PowerShell, dei pacchetti NuGet e di .NET Framework.

## <a name="including-a-reference-to-the-net-assembly-for-service-bus"></a>Inclusione di un riferimento all'assembly .NET per il bus di servizio
Il numero di cmdlet PowerShell disponibili per la gestione del bus di servizio è limitato. Per eseguire il provisioning di entità non esposte tramite i cmdlet esistenti, è possibile usare il client .NET per il bus di servizio nel [pacchetto NuGet del bus di servizio][Service Bus NuGet package].

Innanzitutto, assicurarsi che lo script sia in grado di individuare l'assembly **Microsoft.ServiceBus.dll** , che viene installato con il pacchetto NuGet. Per essere flessibile, lo script esegue i passaggi seguenti:

1. Determina il percorso in cui è stato richiamato.
2. Scorre il percorso finché trova una cartella denominata `packages`. Questa cartella viene creata durante l'installazione dei pacchetti NuGet.
3. Cerca in modo ricorsivo nella cartella `packages` , per trovare un assembly denominato **Microsoft.ServiceBus.dll**.
4. Fa quindi riferimento all'assembly in modo che i tipi siano disponibili per un uso successivo.

Di seguito viene illustrato in che modo questi passaggi vengono implementati in uno script di PowerShell:

```powershell
try
{
    # WARNING: Make sure to reference the latest version of Microsoft.ServiceBus.dll
    Write-Host "Adding the [Microsoft.ServiceBus.dll] assembly to the script..."
    $scriptPath = Split-Path (Get-Variable MyInvocation -Scope 0).Value.MyCommand.Path
    $packagesFolder = (Split-Path $scriptPath -Parent) + "\packages"
    $assembly = Get-ChildItem $packagesFolder -Include "Microsoft.ServiceBus.dll" -Recurse
    Add-Type -Path $assembly.FullName

    Write-Host "The [Microsoft.ServiceBus.dll] assembly has been successfully added to the script."
}

catch [System.Exception]
{
    Write-Error "Could not add the Microsoft.ServiceBus.dll assembly to the script. Make sure you build the solution before running the provisioning script."
}
```

## <a name="provision-a-service-bus-namespace"></a>Provisioning di uno spazio dei nomi del bus di servizio
Due cmdlet di PowerShell supportano le operazioni di spazio dei nomi del bus di servizio. Al posto delle API di .NET SDK, è possibile usare [Get-AzureSBNamespace][Get-AzureSBNamespace] e [New-AzureSBNamespace][New-AzureSBNamespace].

Questo esempio crea alcune variabili locali nello script: `$Namespace` e `$Location`.

* `$Namespace` è il nome dello spazio dei nomi del bus di servizio che si vuole usare.
* `$Location` identifica il data center in cui lo script eseguirà il provisioning dello spazio dei nomi.
* `$CurrentNamespace` archivia lo spazio dei nomi di riferimento che viene recuperato (o creato) dallo script.

In uno script effettivo, `$Namespace` e `$Location` possono essere passati come parametri.

Questa parte dello script esegue le operazioni seguenti:

1. Tenta di recuperare uno spazio dei nomi del bus di servizio con il nome specificato.
2. Se lo spazio dei nomi viene trovato, viene segnalato ciò che viene trovato.
3. Se lo spazio dei nomi non viene trovato, viene creato lo spazio dei nomi e quindi viene recuperato lo spazio dei nomi appena creato.
   
    ```powershell
    $Namespace = "MyServiceBusNS"
    $Location = "West US"
   
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace [$Namespace] already exists in the [$($CurrentNamespace.Region)] region."
    }
    else
    {
        Write-Host "The [$Namespace] namespace does not exist."
        Write-Host "Creating the [$Namespace] namespace in the [$Location] region..."
        New-AzureSBNamespace -Name $Namespace -Location $Location -CreateACSNamespace $false -NamespaceType Messaging
        $CurrentNamespace = Get-AzureSBNamespace -Name $Namespace
        Write-Host "The [$Namespace] namespace in the [$Location] region has been successfully created."
    }
    ```

Per il provisioning di altre entità del bus di servizio, creare un'istanza della classe [NamespaceManager][NamespaceManager] dall'SDK.
È possibile usare il cmdlet [Get-AzureSBAuthorizationRule][Get-AzureSBAuthorizationRule] per recuperare una regola di autorizzazione usata per specificare una stringa di connessione. Verrà archiviato un riferimento all'istanza di `NamespaceManager` nella variabile `$NamespaceManager`. `$NamespaceManager` verrà utilizzato in seguito nello script per il provisioning di altre entità.

```powershell
$sbr = Get-AzureSBAuthorizationRule -Namespace $Namespace
# Create the NamespaceManager object to create the event hub
Write-Host "Creating a NamespaceManager object for the [$Namespace] namespace..."
$NamespaceManager = [Microsoft.ServiceBus.NamespaceManager]::CreateFromConnectionString($sbr.ConnectionString);
Write-Host "NamespaceManager object for the [$Namespace] namespace has been successfully created."
```

## <a name="provisioning-other-service-bus-entities"></a>Provisioning di altre entità del bus di servizio
Per effettuare il provisioning di altre entità, ad esempio code, argomenti e Hub eventi, usare l'[API .NET per il bus di servizio][API .NET per il bus di servizio]. In questo articolo verranno illustrati unicamente gli hub eventi, anche se i passaggi per le altre entità sono simili. Inoltre, al termine di questo articolo si fa riferimento a esempi più dettagliati, incluse altre entità.

Questa parte dello script crea altre quattro variabili locali che vengono usate per creare istanze dell'oggetto `EventHubDescription`. Questo script esegue le operazioni seguenti:

1. Usando l'oggetto `NamespaceManager`, viene verificata innanzitutto l'esistenza dell'hub eventi identificato da `$Path`.
2. Se non esiste, viene creato un oggetto `EventHubDescription` che viene passato al metodo `CreateEventHubIfNotExists` della classe `NamespaceManager`.
3. Dopo aver determinato la disponibilità dell'hub eventi, viene creato un gruppo di clienti tramite `ConsumerGroupDescription` e `NamespaceManager`.
   
    ```powershell
    $Path  = "MyEventHub"
    $PartitionCount = 12
    $MessageRetentionInDays = 7
    $UserMetadata = $null
    $ConsumerGroupName = "MyConsumerGroup"
   
    # Check to see if the Event Hub already exists
    if ($NamespaceManager.EventHubExists($Path))
    {
        Write-Host "The [$Path] event hub already exists in the [$Namespace] namespace."  
    }
    else
    {
        Write-Host "Creating the [$Path] event hub in the [$Namespace] namespace: PartitionCount=[$PartitionCount] MessageRetentionInDays=[$MessageRetentionInDays]..."
        $EventHubDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.EventHubDescription -ArgumentList $Path
        $EventHubDescription.PartitionCount = $PartitionCount
        $EventHubDescription.MessageRetentionInDays = $MessageRetentionInDays
        $EventHubDescription.UserMetadata = $UserMetadata
        $EventHubDescription.Path = $Path
        $NamespaceManager.CreateEventHubIfNotExists($EventHubDescription)
        Write-Host "The [$Path] event hub in the [$Namespace] namespace has been successfully created."
    }
   
    # Create the consumer group if it doesn't exist
    Write-Host "Creating the consumer group [$ConsumerGroupName] for the [$Path] event hub..."
    $ConsumerGroupDescription = New-Object -TypeName Microsoft.ServiceBus.Messaging.ConsumerGroupDescription -ArgumentList $Path, $ConsumerGroupName
    $ConsumerGroupDescription.UserMetadata = $ConsumerGroupUserMetadata
    $NamespaceManager.CreateConsumerGroupIfNotExists($ConsumerGroupDescription)
    Write-Host "The consumer group [$ConsumerGroupName] for the [$Path] event hub has been successfully created."
    ```

## <a name="migrate-a-namespace-to-another-azure-subscription"></a>Eseguire la migrazione di uno spazio dei nomi a un'altra sottoscrizione di Azure
La sequenza di comandi seguente sposta uno spazio dei nomi da una sottoscrizione di Azure a un'altra. Per eseguire questa operazione, lo spazio dei nomi deve essere già attivo e l'utente che esegue i comandi di PowerShell deve essere un amministratore nella sottoscrizione di origine e in quella di destinazione.

```powershell
# Create a new resource group in target subscription
Select-AzureRmSubscription -SubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff'
New-AzureRmResourceGroup -Name 'targetRG' -Location 'East US'

# Move namespace from source subscription to target subscription
Select-AzureRmSubscription -SubscriptionId 'aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa'
$res = Find-AzureRmResource -ResourceNameContains mynamespace -ResourceType 'Microsoft.ServiceBus/namespaces'
Move-AzureRmResource -DestinationResourceGroupName 'targetRG' -DestinationSubscriptionId 'ffffffff-ffff-ffff-ffff-ffffffffffff' -ResourceId $res.ResourceId
```

## <a name="next-steps"></a>Passaggi successivi
In questo articolo viene fornita una descrizione di base per il provisioning delle entità del bus di servizio tramite PowerShell. Tutte le operazioni che è possibile eseguire mediante le librerie client .NET, possono essere eseguite anche in uno script PowerShell.

Esempi più dettagliati sono disponibili sui post di blog seguenti:

* [Come creare code, argomenti e sottoscrizioni del bus di servizio tramite uno script di PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Come creare uno spazio dei nomi del bus di servizio e un hub eventi tramite uno script PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)

Sono disponibili per il download anche alcuni script predefiniti:

* [Script PowerShell del bus di servizio](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Link references-->
[Purchase Options]: http://azure.microsoft.com/pricing/purchase-options/
[Member Offers]: http://azure.microsoft.com/pricing/member-offers/
[Free Trial]: http://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell]: /powershell/azureps-cmdlets-docs
[Service Bus NuGet package]: http://www.nuget.org/packages/WindowsAzure.ServiceBus/
[Get-AzureSBNamespace]: https://docs.microsoft.com/powershell/servicemanagement/azure.compute/v1.6.1/Get-AzureSBNamespace
[New-AzureSBNamespace]: https://docs.microsoft.com/powershell/servicemanagement/azure.compute/v1.6.1/new-azuresbnamespace
[Get-AzureSBAuthorizationRule]: https://docs.microsoft.com/powershell/servicemanagement/azure.compute/v1.6.1/get-azuresbauthorizationrule
[NamespaceManager]: https://docs.microsoft.com/dotnet/api/microsoft.servicebus.namespacemanager



<!--HONumber=Jan17_HO2-->


