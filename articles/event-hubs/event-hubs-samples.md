---
title: Esempi - Hub eventi di Azure | Microsoft Docs
description: Questo articolo fornisce un elenco di esempi per Hub eventi di Azure disponibili su GitHub.
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 7231b6a33c94dfe029f41f034c7674b386090d7a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53104051"
---
# <a name="git-repositories-with-samples-for-azure-event-hubs"></a>Repository GIT con esempi per Hub eventi di Azure 
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

## <a name="python-samples"></a>Esempi per Python
È possibile trovare esempi di Python per Hub eventi di Azure nel repository GitHub [azure-event-hubs-python](https://github.com/Azure/azure-event-hubs-python/tree/master/examples).

## <a name="nodejs-samples"></a>Esempi di Node. js
È possibile trovare esempi di Node.js per Hub eventi di Azure nel repository GitHub [azure-event-hubs-node](https://github.com/Azure/azure-event-hubs-node).

## <a name="go-samples"></a>Esempi di Go
È possibile trovare esempi di Go per Hub eventi di Azure nel repository GitHub [azure-event-hubs-go](https://github.com/Azure/azure-event-hubs-go/tree/master/_examples).

## <a name="azure-cli-samples"></a>Esempi dell'interfaccia della riga di comando di Azure
È possibile trovare esempi dell'interfaccia della riga di comando di Azure per Hub eventi di Azure nel repository GitHub [azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/CLI).

## <a name="azure-powershell-samples"></a>Esempi di Azure PowerShell
È possibile trovare esempi di Azure PowerShell per Hub eventi di Azure nel repository GitHub [azure-event-hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/Management/PowerShell).
 
## <a name="apache-kafka-samples"></a>Esempi di Apache Kafka
È possibile trovare esempi per Hub eventi per la funzionalità di Apache Kafka nel repository GitHub [azure-event-hubs-for-kafka](https://github.com/Azure/azure-event-hubs-for-kafka).

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni su Hub eventi consultare i seguenti articoli:

- [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)
- [Funzionalità di Hub eventi](event-hubs-features.md)
- [Domande frequenti su Hub eventi](event-hubs-faq.md)