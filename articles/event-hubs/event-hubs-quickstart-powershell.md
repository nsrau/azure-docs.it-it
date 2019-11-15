---
title: 'Guida introduttiva: Creare un hub eventi tramite PowerShell - Hub eventi di Azure'
description: Questa guida introduttiva illustra come creare un hub eventi usando Azure PowerShell e inviare e ricevere eventi usando .NET Standard SDK.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 11/05/2019
ms.author: shvija
ms.openlocfilehash: 2a88fe340037c31a71cb34181682095f31400c36
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73720603"
---
# <a name="quickstart-create-an-event-hub-using-azure-powershell"></a>Guida introduttiva: Creare un hub eventi usando Azure PowerShell

Hub eventi di Azure è una piattaforma di Big Data streaming e un servizio di inserimento di eventi che consente di ricevere ed elaborare milioni di eventi al secondo. Hub eventi consente di elaborare e archiviare eventi, dati o dati di telemetria generati dal software distribuito e dai dispositivi. I dati inviati a un hub eventi possono essere trasformati e archiviati usando qualsiasi provider di analisi in tempo reale o adattatori di invio in batch/archiviazione. Per una panoramica dettagliata di Hub eventi, vedere [Panoramica di Hub eventi](event-hubs-about.md) e [Funzionalità di Hub eventi](event-hubs-features.md).

In questa guida introduttiva viene creato un hub eventi usando Azure PowerShell.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Per completare questa esercitazione, accertarsi di avere:

- Sottoscrizione di Azure. Se non se ne ha una, [creare un account gratuito][] prima di iniziare.
- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [NET Standard SDK](https://www.microsoft.com/net/download/windows) versione 2.0 o successiva.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se si usa PowerShell in locale, è necessario eseguire la versione più recente di PowerShell per completare questa guida introduttiva. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Un gruppo di risorse è una raccolta logica per le risorse di Azure ed è necessario per creare un hub eventi. 

L'esempio seguente crea un gruppo di risorse nell'area Stati Uniti orientali. Sostituire `myResourceGroup` con il nome del gruppo di risorse che si vuole usare:

```azurepowershell-interactive
New-AzResourceGroup –Name myResourceGroup –Location eastus
```

## <a name="create-an-event-hubs-namespace"></a>Creare uno spazio dei nomi di Hub eventi

Dopo aver creato il gruppo di risorse, creare uno spazio dei nomi di Hub eventi all'interno di tale gruppo di risorse. Uno spazio dei nomi di Hub eventi specifica un nome di dominio univoco completo in cui è possibile creare l'hub eventi. Sostituire `namespace_name` con un nome univoco per lo spazio dei nomi:

```azurepowershell-interactive
New-AzEventHubNamespace -ResourceGroupName myResourceGroup -NamespaceName namespace_name -Location eastus
```

## <a name="create-an-event-hub"></a>Creare un hub eventi

Una volta creato uno spazio dei nomi di Hub eventi, crearvi all'interno un hub eventi:  
Il periodo consentito per `MessageRetentionInDays` è compreso tra 1 e 7 giorni.

```azurepowershell-interactive
New-AzEventHub -ResourceGroupName myResourceGroup -NamespaceName namespace_name -EventHubName eventhub_name -MessageRetentionInDays 3
```

Congratulazioni! È stato usato Azure PowerShell per creare uno spazio dei nomi di Hub eventi e un hub eventi nello spazio dei nomi. 

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato creato lo spazio dei nomi di Hub eventi e sono state usate applicazioni di esempio per inviare e ricevere eventi dall'hub eventi. Per istruzioni dettagliate relative all'invio o alla ricezione di eventi da un hub eventi, vedere le esercitazioni per l'**invio e la ricezione di eventi**: 

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [.NET Framework](event-hubs-dotnet-framework-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [Node.js](event-hubs-node-get-started-send.md)
- [Go](event-hubs-go-get-started-send.md)
- [C (solo invio)](event-hubs-c-getstarted-send.md)
- [Apache Storm (solo ricezione)](event-hubs-storm-getstarted-receive.md)


[Creare un account gratuito]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Install and Configure Azure PowerShell]: https://docs.microsoft.com/powershell/azure/install-az-ps
[New-AzResourceGroup]: https://docs.microsoft.com/powershell/module/az.resources/new-Azresourcegroup
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[3]: ./media/event-hubs-quickstart-powershell/sender1.png
[4]: ./media/event-hubs-quickstart-powershell/receiver1.png
[5]: ./media/event-hubs-quickstart-powershell/metrics.png
