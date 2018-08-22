---
title: Esempi di Hub eventi di Azure | Microsoft Docs
description: Esempi di Hub eventi di Azure
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2018
ms.author: shvija
ms.openlocfilehash: fbde6e5a5ed053d6c151b3af25535c397a496ef4
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005335"
---
# <a name="event-hubs-samples"></a>Esempi di Hub eventi 
È possibile trovare gli esempi di Hub eventi in [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples). Questi esempi illustrano le funzionalità principali di [Hub eventi di Azure](/azure/event-hubs/). Questo articolo suddivide gli esempi disponibili in categorie e fornisce una descrizione e un collegamento a ognuno di essi.

## <a name="net-samples"></a>Esempi .NET

| Nome esempio | DESCRIZIONE | 
| ----------- | ----------- | 
| [SampleSender](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender) | Questo esempio illustra come scrivere un'applicazione console .NET Core che invia un set di eventi a un hub eventi. |
| [SampleEHReceiver](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver) | Questo esempio illustra come scrivere un'applicazione console .NET Core che riceve un set di eventi da un hub eventi usando la libreria dell'host processore di eventi.  | 

## <a name="java-samples"></a>Esempi di Java

| Nome esempio | DESCRIZIONE | 
| ----------- | ----------- | 
| [SendBatch](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SendBatch)  | Questo esempio illustra come inserire batch di eventi nell'hub eventi. | 
| [SimpleSend](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend) | Questo esempio illustra come inserire eventi nell'hub eventi. |
| [AdvanceSendOptions](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/AdvancedSendOptions) | Questo esempio illustra le diverse opzioni disponibili con Hub eventi per l'inserimento di eventi. |
| [ReceiveByDateTime](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveByDateTime) | Questo esempio illustra come ricevere eventi da una partizione dell'hub eventi usando un offset di data e ora specifico. |
| [ReceiveUsingOffset](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingOffset) | Questo esempio illustra come ricevere eventi da una partizione dell'hub eventi usando un offset di data specifico. |  
| [ReceiveUsingSequenceNumber](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/ReceiveUsingSequenceNumber) | Questo esempio illustra come ricevere eventi dalle partizioni di un hub eventi usando un numero di sequenza. |   
| [EventProcessorSample](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Basic/EventProcessorSample) |Questo esempio illustra come ricevere eventi da un hub eventi usando l'host processore di eventi, che offre la selezione automatica delle partizioni e il failover tra più ricevitori simultanei. | 
| [AutoScaleOnIngress](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/AutoScaleOnIngress) | Questo esempio illustra come è possibile aumentare automaticamente le prestazioni di un hub eventi in caso di carichi elevati. L'esempio invia eventi a una velocità superiore a quella configurata per un hub eventi, causando l'aumento delle prestazioni dell'hub eventi. | 
| [IngressBenchmark](https://github.com/Azure/azure-event-hubs/blob/master/samples/Java/Benchmarks/IngressBenchmark) | Questo esempio consente di misurare la velocità in ingresso. | 

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Hub eventi consultare i seguenti articoli:

- [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
- [Funzionalità di Hub eventi](event-hubs-features.md)
- [Domande frequenti su Hub eventi](event-hubs-faq.md)