---
title: Panoramica delle API di Inoltro di Azure | Documentazione Microsoft
description: Panoramica delle API di Inoltro di Azure disponibili
services: event-hubs
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: fdaa1d2b-bd80-4e75-abb9-0c3d0773af2d
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/02/2018
ms.author: spelluru
ms.openlocfilehash: 05d7ac56d6c1c48125eb458d0eee852ba396b300
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663095"
---
# <a name="available-relay-apis"></a>API di Inoltro disponibili

## <a name="runtime-apis"></a>API di runtime

La tabella seguente elenca tutti i client di runtime di Inoltro attualmente disponibili.

La sezione [Informazioni aggiuntive](#additional-information) contiene altre informazioni sullo stato di ogni libreria di runtime.

| Linguaggio/Piattaforma | Funzionalità disponibile | Pacchetto client | Repository |
| --- | --- | --- | --- |
| .NET Standard | connessioni ibride | [Microsoft.Azure.Relay](https://www.nuget.org/packages/Microsoft.Azure.Relay/) | [GitHub](https://github.com/azure/azure-relay-dotnet) |
| .NET Framework | Inoltro WCF | [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) | N/D |
| Nodo | connessioni ibride | [Websocket: `hyco-ws`](https://www.npmjs.com/package/hyco-ws)<br/>[Websocket: `hyco-websocket`](https://www.npmjs.com/package/hyco-websocket)<br/>[Richieste HTTP: `hyco-https`](https://www.npmjs.com/package/hyco-https) | [GitHub](https://github.com/Azure/azure-relay-node) |

### <a name="additional-information"></a>Informazioni aggiuntive

#### <a name="net"></a>.NET

I runtime in un ecosistema .NET sono vari, pertanto sono presenti più librerie .NET per Inoltro. La libreria .NET Standard può essere eseguita usando .NET Core o .NET Framework, mentre la libreria .NET Framework può essere eseguita solo in un ambiente .NET Framework. Per altre informazioni su .NET Framework, vedere le [versioni del framework](/dotnet/articles/standard/frameworks).

La libreria .NET Framework supporta solo il modello di programmazione WCF e si basa su un protocollo binario proprietario basato sul trasporto `net.tcp` WCF. Questo protocollo e la libreria vengono mantenuti per compatibilità con le versioni precedenti con le applicazioni esistenti.

La libreria .NET Standard è basata sulla definizione del protocollo aperto per l'inoltro di Connessioni ibride che si basa su HTTP e WebSocket. La libreria supporta un'astrazione di flusso tramite Websocket e un semplice movimento dell'API richiesta-risposta per rispondere alle richieste HTTP. L'esempio di [API Web](https://github.com/Azure/azure-relay-dotnet) illustra come integrare Connessioni ibride con ASP.NET Core per servizi Web.

#### <a name="nodejs"></a>Node.js

I moduli di Connessioni ibride elencati nella tabella precedente sostituiscono o modificano i moduli Node.js esistenti, con implementazioni alternative in ascolto sul servizio Inoltro di Azure anziché nello stack di rete locale.

Il modulo `hyco-https` modifica e sostituisce parzialmente i moduli di base Node.js `http` e `https`, fornendo un'implementazione del listener HTTPS compatibile con molti moduli e applicazioni Node.js esistenti che si basano su questi moduli principali.

I moduli `hyco-ws` e `hyco-websocket` modificano gli ormai diffusi moduli `ws` e `websocket` per Node.js, fornendo implementazioni del listener alternative che consentono a moduli e applicazioni basati su uno dei moduli di funzionare dietro l'inoltro di Connessioni ibride.

Informazioni dettagliate su questi moduli sono disponibili nel repository GitHub [azure-relay-node](https://github.com/Azure/azure-relay-node).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul servizio di inoltro di Azure, vedere i collegamenti seguenti:
* [Che cos'è il servizio di inoltro di Azure?](relay-what-is-it.md)
* [Domande frequenti sul servizio di inoltro](relay-faq.md)
