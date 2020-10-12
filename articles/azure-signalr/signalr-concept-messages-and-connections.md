---
title: Messaggi e connessioni in Servizio Azure SignalR
description: Panoramica dei concetti chiave relativi a messaggi e connessioni in Servizio Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: zhshang
ms.openlocfilehash: 5483e10e817ce8a0a7e7c82d817b7bdbbdd9176b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87853450"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Messaggi e connessioni in Servizio Azure SignalR

Il modello di fatturazione per Servizio Azure SignalR è basato sul numero di connessioni e di messaggi. Questo articolo illustra come vengono definiti e conteggiati i messaggi e le connessioni ai fini della fatturazione.


## <a name="message-formats"></a>Formati di messaggi 

Il servizio Azure SignalR supporta gli stessi formati di ASP.NET Core SignalR: [JSON](https://www.json.org/) e [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Dimensioni dei messaggi

Servizio Azure SignalR non prevede limiti di dimensioni per i messaggi.

I messaggi di grandi dimensioni vengono divisi in messaggi più piccoli, di non più di 2 KB ognuno, e trasmessi separatamente. La divisione e l'assemblaggio dei messaggi vengono gestiti dagli SDK. Non è necessario alcun intervento da parte degli sviluppatori.

I messaggi di grandi dimensioni influiscono negativamente sulle prestazioni della messaggistica. Usare messaggi di dimensioni inferiori quando è possibile e cercare di determinare le dimensioni ottimali per ogni scenario di caso d'uso.

## <a name="how-messages-are-counted-for-billing"></a>Come vengono conteggiati i messaggi per la fatturazione

Per la fatturazione, vengono conteggiati solo i messaggi in uscita da Servizio Azure SignalR. I messaggi ping tra client e server vengono ignorati.

I messaggi di dimensioni maggiori di 2 KB vengono conteggiati come più messaggi di 2 KB ognuno. Il grafico del numero di messaggi nel portale di Azure viene aggiornato ogni 100 messaggi per hub.

Si supponga, ad esempio, di disporre di un server applicazioni e di tre client:

Il server applicazioni trasmette un messaggio da 1 KB a tutti i client connessi. il messaggio dal server app al servizio viene considerato un messaggio in ingresso libero. Solo i tre messaggi inviati dal servizio a ogni client vengono fatturati come messaggi in uscita.

Il client A invia un messaggio da 1 KB a un altro client B, senza passare attraverso il server app. Il messaggio dal client A al servizio è un messaggio in ingresso gratuito. Il messaggio dal servizio al client B viene fatturato come messaggio in uscita.

Se si dispone di tre client e un server applicazioni. Un client invia un messaggio di 4 KB che il server dovrà trasmettere a tutti i client. Il numero di messaggi fatturati è otto: un messaggio dal servizio al server applicazioni e tre messaggi dal servizio ai client. Ogni messaggio viene conteggiato come due messaggi di 2 KB.

## <a name="how-connections-are-counted"></a>Come vengono conteggiate le connessioni

Sono disponibili connessioni al server e client con il servizio Azure SignalR. Per impostazione predefinita, ogni server applicazioni inizia con cinque connessioni iniziali per hub e ogni client dispone di una connessione client.

Il conteggio delle connessioni visualizzato nel portale di Azure include sia le connessioni server che quelle client.

Si supponga, ad esempio, di disporre di due server applicazioni e di definire cinque hub nel codice. Il numero di connessioni server sarà 50:2 server applicazioni * 5 Hub * 5 connessioni per hub.

ASP.NET SignalR il calcola le connessioni server in modo diverso. Include un hub predefinito in aggiunta agli hub definiti dall'utente. Per impostazione predefinita, ogni server applicazioni necessita di altre cinque connessioni server iniziali. Il numero di connessioni iniziale per l'Hub predefinito rimane coerente con altri hub.

Il servizio e il server applicazioni continuano a sincronizzare lo stato della connessione e a eseguire la regolazione delle connessioni server per ottenere prestazioni migliori e una maggiore stabilità dei servizi.  Per questo motivo è possibile che le modifiche apportate al numero di connessione del server vengano apportate

## <a name="how-inboundoutbound-traffic-is-counted"></a>Come viene conteggiato il traffico in ingresso/in uscita

Il messaggio inviato al servizio è un messaggio in ingresso. Il messaggio inviato dal servizio è un messaggio in uscita. Il traffico viene calcolato in byte.

## <a name="related-resources"></a>Risorse correlate

- [Tipi di aggregazione in Monitoraggio di Azure](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [Configurazione di ASP.NET Core SignalR](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
