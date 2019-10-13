---
title: Messaggi e connessioni in Servizio Azure SignalR
description: Panoramica dei concetti chiave relativi a messaggi e connessioni in Servizio Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: zhshang
ms.openlocfilehash: 2785d85db47ed3b214044e673566a2837b83e984
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285498"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Messaggi e connessioni in Servizio Azure SignalR

Il modello di fatturazione per Servizio Azure SignalR è basato sul numero di connessioni e di messaggi. Questo articolo illustra come vengono definiti e conteggiati i messaggi e le connessioni ai fini della fatturazione.


## <a name="message-formats"></a>Formati di messaggi 

Servizio Azure SignalR supporta gli stessi formati di ASP.NET Core SignalR: [JSON](https://www.json.org/) e [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Dimensioni dei messaggi

Servizio Azure SignalR non prevede limiti di dimensioni per i messaggi.

I messaggi di grandi dimensioni vengono divisi in messaggi più piccoli, di non più di 2 KB ognuno, e trasmessi separatamente. La divisione e l'assemblaggio dei messaggi vengono gestiti dagli SDK. Non è necessario alcun intervento da parte degli sviluppatori.

I messaggi di grandi dimensioni influiscono negativamente sulle prestazioni della messaggistica. Usare messaggi di dimensioni inferiori quando è possibile e cercare di determinare le dimensioni ottimali per ogni scenario di caso d'uso.

## <a name="how-messages-are-counted-for-billing"></a>Come vengono conteggiati i messaggi per la fatturazione

Per la fatturazione, vengono conteggiati solo i messaggi in uscita da Servizio Azure SignalR. I messaggi ping tra client e server vengono ignorati.

I messaggi di dimensioni maggiori di 2 KB vengono conteggiati come più messaggi di 2 KB ognuno. Il grafico del numero di messaggi nel portale di Azure viene aggiornato ogni 100 messaggi per hub.

Si supponga, ad esempio, di avere tre client e un server applicazioni. Un client invia un messaggio di 4 KB che il server dovrà trasmettere a tutti i client. I messaggi in questo caso sono otto: uno inviato dal servizio al server applicazioni e tre dal servizio ai client. Ogni messaggio viene conteggiato come due messaggi di 2 KB.

Il conteggio dei messaggi visualizzato nel portale di Azure rimane 0 finché non ne vengono accumulati più di 100.

## <a name="how-connections-are-counted"></a>Come vengono conteggiate le connessioni

Sono disponibili connessioni al server e client con il servizio Azure SignalR. Per impostazione predefinita, ogni server applicazioni inizia con cinque connessioni iniziali per hub e ogni client dispone di una connessione client.

Il conteggio delle connessioni visualizzato nel portale di Azure include sia le connessioni server che quelle client.

Si supponga, ad esempio, di avere due server applicazioni e di definire cinque hub nel codice. Le connessioni server sono 50: 2 server applicazioni * 5 hub * 5 connessioni per hub.

ASP.NET SignalR il calcola le connessioni server in modo diverso. Include un hub predefinito in aggiunta agli hub definiti dall'utente. Per impostazione predefinita, ogni server applicazioni necessita di altre cinque connessioni server iniziali. Il numero di connessioni iniziale per l'Hub predefinito rimane coerente con quello degli altri hub.

Durante il ciclo di vita del server applicazioni, il servizio e il server applicazioni mantengono lo stato della connessione di sincronizzazione e apportano modifiche alle connessioni server per una migliore stabilità dei servizi e delle prestazioni. Per questo motivo è possibile che le modifiche apportate al numero di connessione del server vengano apportate

## <a name="how-inboundoutbound-traffic-is-counted"></a>Come viene conteggiato il traffico in ingresso/in uscita

La distinzione tra traffico in ingresso e traffico in uscita si basa sulla prospettiva di Servizio Azure SignalR. Il traffico viene calcolato in byte. Analogamente al conteggio dei messaggi, anche per il traffico è prevista una frequenza di campionamento. Il grafico del traffico in ingresso/in uscita nel portale di Azure viene aggiornato ogni 100 KB per hub.

## <a name="related-resources"></a>Risorse correlate

- [Tipi di aggregazione in Monitoraggio di Azure](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [Configurazione di ASP.NET Core SignalR](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)
