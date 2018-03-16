---
title: Esempi di Hub eventi di Azure | Microsoft Docs
description: Esempi di Hub eventi di Azure
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/05/2018
ms.author: sethm
ms.openlocfilehash: a581b7039a3631b7f1dc35816175242f892bd7dd
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="event-hubs-samples"></a>Esempi di Hub eventi 

Il set di esempi di Hub eventi di Azure illustra le funzionalità principali di [Hub eventi di Azure](/azure/event-hubs/). Questo articolo suddivide gli esempi disponibili in categorie e fornisce una descrizione e un collegamento a ognuno di essi.

Al momento della stesura di questo articolo, gli esempi di Hub eventi si trovano in diverse posizioni:

- [Esempi di codice per sviluppatori MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5)
- [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples)

Per altre informazioni sulle diverse versioni di .NET Framework, vedere [Framework di destinazione](/dotnet/articles/standard/frameworks).

Altri esempi saranno aggiunti in seguito, è consigliabile quindi controllare spesso gli aggiornamenti.

## <a name="net-standard"></a>.NET Standard

Negli esempi seguenti viene illustrato come inviare e ricevere eventi tramite il [client di Hub eventi](https://github.com/Azure/azure-event-hubs-dotnet/blob/master/readme.md) per la [libreria .NET Standard](/dotnet/articles/standard/library).

### <a name="send-events"></a>Inviare eventi 

L'esempio per la [guida introduttiva all'invio](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) illustra come scrivere un'applicazione console .NET Core che invii gli eventi a un hub eventi.

### <a name="receive-events"></a>Ricevere eventi 

L'esempio [Get started receiving with the Event Processor Host](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) (Guida introduttiva alla ricezione con l'host processore di eventi) è un'applicazione console .NET Core che riceve i messaggi da un hub eventi usando l'host processore di eventi.

## <a name="net-framework"></a>.NET Framework   

Questi esempi illustrano altre funzionalità di Hub eventi di Azure, indirizzati alla [libreria .NET Framework](/dotnet/framework/index).
 
### <a name="notify-users-of-events-received"></a>Inviare notifiche agli utenti sugli eventi ricevuti

L'esempio [AppToNotifyUsers](https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications) invia notifiche agli utenti sui dati ricevuti dai sensori o da altri sistemi.

### <a name="get-started-with-event-hubs"></a>Introduzione all'Hub eventi 

L'esempio per l'[introduzione a Hub eventi](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097) illustra le funzionalità di base di Hub eventi, come la creazione di un hub eventi, l'invio di eventi a un hub eventi e l'uso di eventi tramite l'[host processore di eventi](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).

### <a name="scale-out-event-processing"></a>Elaborazione di eventi con aumento delle istanze 

L'esempio sull'[elaborazione di eventi con aumento delle istanze](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3) illustra come usare l'[Host processore di eventi](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) per distribuire il carico di lavoro del consumo di flusso di Hub eventi. Viene illustrato come implementare gli oggetti **EventProcessor** e **EventProcessorFactory** per gestire il flusso di eventi. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle versioni .NET Framework visitare i collegamenti seguenti:

- [Framework e destinazioni](/dotnet/articles/standard/frameworks)
- [.NET Framework 4.6 e 4.5](/dotnet/framework/index)

Per altre informazioni su Hub eventi consultare i seguenti articoli:

- [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
- [Funzionalità di Hub eventi](event-hubs-features.md)
- [Domande frequenti su Hub eventi](event-hubs-faq.md)