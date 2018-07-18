---
title: 'Guida introduttiva di Azure: elaborare flussi di eventi con PowerShell | Microsoft Docs'
description: Questa guida introduttiva descrive come inviare e ricevere eventi di Hub eventi di Azure con PowerShell e include un'applicazione .NET di esempio.
services: event-hubs
author: sethmanheim
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 06/26/2018
ms.author: sethm
ms.openlocfilehash: 9216372038db7a6f97cfc8034f715b34de08d83c
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37132297"
---
# <a name="quickstart-process-event-streams-using-powershell-and-net-standard"></a>Guida introduttiva: elaborare flussi di eventi con PowerShell e .NET Standard

Hub eventi di Azure è una piattaforma di streaming di dati e un servizio di inserimento degli eventi estremamente scalabile, che consente di ricevere ed elaborare milioni di eventi al secondo. Questa guida introduttiva illustra come creare un hub eventi usando Azure PowerShell e inviare e ricevere da un hub eventi usando .NET Standard SDK.

Per completare questa guida introduttiva è necessaria una sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito][] prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, accertarsi di avere:

- [Visual Studio 2017 Update 3 (versione 15.3, 26730.01)](http://www.visualstudio.com/vs) o versioni successive.
- [NET Standard SDK](https://www.microsoft.com/net/download/windows) versione 2.0 o successiva.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si usa PowerShell in locale, è necessario eseguire la versione più recente di PowerShell per completare questa guida introduttiva. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/en-us/powershell/azure/install-azurerm-ps?view=azurermps-5.7.0).

## <a name="provision-resources"></a>Effettuare il provisioning delle risorse

### <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è una raccolta logica per le risorse di Azure ed è necessario per creare un hub eventi. 

L'esempio seguente crea un gruppo di risorse nell'area Stati Uniti orientali. Sostituire `myResourceGroup` con il nome del gruppo di risorse che si vuole usare:

```azurepowershell-interactive
New-AzureRmResourceGroup –Name myResourceGroup –Location eastus
```

### <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

Dopo aver creato il gruppo di risorse, creare uno spazio dei nomi di Hub eventi all'interno di tale gruppo di risorse. Uno spazio dei nomi di Hub eventi specifica un nome di dominio univoco completo in cui è possibile creare l'hub eventi. Sostituire `namespace_name` con un nome univoco per lo spazio dei nomi:

```azurepowershell-interactive
New-AzureRmEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

### <a name="create-an-event-hub"></a>Creare un hub eventi

Una volta creato uno spazio dei nomi di Hub eventi, crearvi all'interno un hub eventi:

```azurepowershell-interactive
New-AzureRmEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name
```

### <a name="create-a-storage-account-for-event-processor-host"></a>Creare un account di archiviazione per l'host del processore di eventi

L'host del processore di eventi semplifica la ricezione di eventi da Hub eventi gestendo checkpoint e ricevitori paralleli. Per eseguire il checkpoint, l'host del processore di eventi richiede un account di archiviazione. Per creare un account di archiviazione e ottenere le chiavi relative, eseguire i comandi seguenti:

```azurepowershell-interactive
# Create a standard general purpose storage account 
New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name storage_account_name -Location eastus -SkuName Standard_LRS 
e
# Retrieve the storage account key for accessing it
Get-AzureRmStorageAccountKey -ResourceGroupName myResourceGroup -Name storage_account_name
```

### <a name="get-the-connection-string"></a>Ottenere la stringa di connessione

È necessaria una stringa di connessione per connettersi all'hub eventi ed elaborare gli eventi. Per ottenere la stringa di connessione, eseguire:

```azurepowershell-interactive
Get-AzureRmEventHubKey -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Name RootManageSharedAccessKey
```

## <a name="stream-into-event-hubs"></a>Trasmettere un flusso a Hub eventi

È ora possibile avviare lo streaming in Hub eventi. Gli esempi possono essere scaricati o clonati da Git dal [repository di Hub eventi](https://github.com/Azure/azure-event-hubs)

### <a name="ingest-events"></a>Inserire eventi

Per avviare lo streaming di eventi, scaricare [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) da GitHub o clonare il [repository GitHub di Hub eventi](https://github.com/Azure/azure-event-hubs) eseguendo il comando seguente:

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Passare alla cartella \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleSender e caricare il file SampleSender.sln in Visual Studio.

Successivamente, aggiungere il pacchetto Nuget [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) al progetto.

Nel file Program.cs sostituire i segnaposto seguenti con il nome dell'hub eventi e la stringa di connessione:

```C#
private const string EhConnectionString = "Event Hubs connection string";
private const string EhEntityPath = "Event Hub name";

```

Compilare ed eseguire l'esempio. È possibile visualizzare gli eventi mentre vengono inseriti nell'hub eventi:

![][3]

### <a name="receive-and-process-events"></a>Ricevere ed elaborare eventi

Scaricare l'esempio di ricevitore dell'host del processore di eventi che riceve i messaggi appena inviati. Scaricare [SampleEphReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) da GitHub o clonare il [repository GitHub di Hub eventi](https://github.com/Azure/azure-event-hubs) eseguendo il comando seguente:

```bash
git clone https://github.com/Azure/azure-event-hubs.git
```

Passare alla cartella \azure-event-hubs\samples\DotNet\Microsoft.Azure.EventHubs\SampleEphReceiver e caricare la soluzione SampleEphReceiver.sln in Visual Studio.

Aggiungere al progetto i pacchetti NuGet [Microsoft.Azure.EventHubs](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) e [Microsoft.Azure.EventHubs.Processor](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/).

Nel file Program.cs sostituire le costanti seguenti con i relativi valori corrispondenti:

```C#
private const string EventHubConnectionString = "Event Hubs connection string";
private const string EventHubName = "Event Hub name";
private const string StorageContainerName = "Storage account container name";
private const string StorageAccountName = "Storage account name";
private const string StorageAccountKey = "Storage account key";
```

Compilare ed eseguire l'esempio. È possibile visualizzare gli eventi ricevuti nell'applicazione di esempio:

![][4]

Nel portale di Azure è possibile visualizzare la frequenza con cui gli eventi vengono elaborati per un determinato spazio dei nomi di Hub eventi come illustrato in seguito:

![][5]

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver completato questa guida introduttiva, è possibile eliminare il gruppo di risorse e lo spazio dei nomi, l'account di archiviazione e l'hub eventi in esso contenuti. Sostituire `myResourceGroup` con il nome del gruppo di risorse creato. 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati creati lo spazio dei nomi di Hub eventi e altre risorse necessarie per inviare e ricevere eventi dall'hub eventi. Per altre informazioni, passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Visualizzare anomalie dei dati nei flussi di dati di Hub eventi](event-hubs-tutorial-visualize-anomalies.md)

[Creare un account gratuito]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-azurerm-ps
[New-AzureRmResourceGroup]: https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
