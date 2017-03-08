---
title: Esempi di Hub eventi di Azure | Microsoft Docs
description: Esempi di Hub eventi
services: event-hubs
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/01/2017
ms.author: jotaub;sethm
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 32b57664fcf15daed75fe7050b672383e79c50cf
ms.lasthandoff: 03/03/2017

---

# <a name="event-hubs-samples"></a>Esempi di Hub eventi 

Negli esempi di Hub eventi vengono illustrate le funzionalità principali di [Hub eventi di Azure](/azure/event-hubs/). Questo articolo suddivide gli esempi disponibili in categorie e fornisce una descrizione e un collegamento a ognuno di essi.

Al momento della stesura di questo articolo, gli esempi di Hub eventi si trovano in diverse posizioni:

- [Esempi di codice per sviluppatori MSDN](https://code.msdn.microsoft.com/site/search?query=event%20hubs&f%5B0%5D.Value=event%20hubs&f%5B0%5D.Type=SearchText&ac=5)
- [GitHub](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples)

Per altre informazioni sulle diverse versioni di .NET Framework, vedere [Frameworks and Targets](/dotnet/articles/standard/frameworks) (Framework e destinazioni).

Altri esempi saranno aggiunti in seguito, è consigliabile quindi controllare spesso gli aggiornamenti.

## <a name="net-standard"></a>.NET Standard

Negli esempi seguenti viene illustrato come inviare e ricevere eventi tramite il [client di Hub eventi](https://github.com/Azure/azure-event-hubs-dotnet/blob/master/readme.md) per la [libreria .NET Standard](/dotnet/articles/standard/library).

### <a name="send-events"></a>Inviare eventi 

L'esempio per [Get started sending](https://github.com/Azure/azure-event-hubs-dotnet/tree/master/samples/SampleSender) (Guida introduttiva all'invio) illustra come scrivere un'applicazione console .NET Core che invii gli eventi a un Hub eventi.

### <a name="receive-events"></a>Ricevere eventi 

L'esempio [Get started receiving with the Event Processor Host](https://github.com/Azure/azure-event-hubs/tree/master/samples/SampleEphReceiver) (Guida introduttiva alla ricezione con l'Host processore di eventi) è un'applicazione console .NET Core che riceve i messaggi da un Hub eventi usando l'[Host processore di eventi](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/). 

## <a name="net-framework"></a>.NET Framework    

Questi esempi illustrano altre funzionalità di Hub eventi di Azure, indirizzati alla [libreria .NET Framework](https://msdn.microsoft.com/library/w0x726c2.aspx).
 
### <a name="notify-users-of-events-received"></a>Inviare notifiche agli utenti sugli eventi ricevuti

L'esempio [AppToNotifyUsers](https://github.com/Azure-Samples/event-hubs-dotnet-user-notifications) invia notifiche agli utenti sui dati ricevuti dai sensori o da altri sistemi.

### <a name="get-started-with-event-hubs"></a>Introduzione all'Hub eventi 

L'esempio [Introduzione all'Hub eventi](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097) illustra le funzionalità di base di Hub eventi, come la creazione di un Hub eventi, l'invio di eventi a un Hub eventi e l'uso di eventi tramite l'[Host processore di eventi](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).

### <a name="scale-out-event-processing"></a>Elaborazione di eventi con aumento delle istanze 

L'esempio sull'[elaborazione di eventi con aumento delle istanze](https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3) illustra come usare l'[Host processore di eventi](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/) per distribuire il carico di lavoro del consumo di flusso di Hub eventi. Viene illustrato come implementare gli oggetti **EventProcessor** e **EventProcessorFactory** per gestire il flusso di eventi. 

###  <a name="pull-data-from-sql-into-an-event-hub"></a>Estrazione dei dati da SQL a Hub eventi

L'esempio sull'[estrazione dei dati SQL](https://github.com/Azure-Samples/event-hubs-dotnet-import-from-sql) illustra come estrarre i dati da una tabella SQL e inserirli in un Hub eventi, da utilizzare come input per applicazioni analitiche downstream.

### <a name="pull-web-data-into-an-event-hub"></a>Estrarre i dati web in un Hub eventi 

L'esempio sull'[importazione dei dati dal web](https://github.com/Azure-Samples/event-hubs-dotnet-importfromweb) illustra come estrarre i dati dai feed pubblici (ad esempio, i feed di informazioni sul traffico dal dipartimento dei trasporti) e inserirli in un Hub eventi.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle versioni .NET Framework visitare i collegamenti seguenti:

- [Frameworks and Targets](/dotnet/articles/standard/frameworks) (Framework e destinazioni)
- [.NET Framework 4.6 e 4.5](https://msdn.microsoft.com/library/w0x726c2.aspx)

Per altre informazioni su Hub eventi consultare i seguenti articoli:

- [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
- [Create an Event Hub](event-hubs-create.md) (Creare un Hub eventi)
- [Domande frequenti su Hub eventi](event-hubs-faq.md)
