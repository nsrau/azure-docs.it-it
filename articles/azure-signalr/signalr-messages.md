---
title: Messaggi e connessioni in Azure SignalR
description: Panoramica dei concetti chiave relativi a messaggi e connessioni nel servizio Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: c2348df7a1a55584807a03216e294486ddadfc52
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352598"
---
# <a name="message-and-connection-in-azure-signalr-service"></a>Messaggio e connessione nel servizio Azure SignalR

Il servizio Azure SignalR ha modelli di fatturazione basati sul numero di connessioni e di messaggi. Di seguito è illustrato come vengono definiti e conteggiati i messaggi e le connessioni ai fini della fatturazione.

## <a name="message-formats-supported"></a>Formati di messaggi supportati

Il servizio Azure SignalR supporta gli stessi formati di ASP.NET Core SignalR: [JSON](https://www.json.org/) e [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>Dimensioni dei messaggi

Il servizio Azure SignalR non prevede limiti di dimensioni dei messaggi.

In pratica, i messaggi di grandi dimensioni vengono divisi in messaggi più piccoli, di non più di 2 KB ognuno, e trasmessi come messaggi separati. La divisione e l'assemblaggio dei messaggi vengono gestiti dagli SDK. Non è necessario alcun intervento da parte degli sviluppatori.

I messaggi di grandi dimensioni hanno tuttavia un impatto negativo sulle prestazioni della messaggistica. Usare dimensioni inferiori per i messaggi quando è possibile e cercare di scegliere le dimensioni ottimali dei messaggi per ogni scenario di caso d'uso.

## <a name="how-to-count-messages-for-billing-purpose"></a>Come conteggiare i messaggi per la fatturazione

Vengono conteggiati solo i messaggi in uscita dal servizio SignalR e vengono ignorati i messaggi ping tra i client e i server.

Un messaggio che supera i 2 KB viene calcolato come più messaggi da 2 KB ognuno. Il grafico del numero di messaggi nel portale di Azure viene aggiornato ogni 100 messaggi per hub.

Si supponga, ad esempio, di avere tre client e un server applicazioni. Un client invia un messaggio da 4 KB che il server dovrà trasmettere a tutti i client. I messaggi sono 8: uno inviato dal servizio al server applicazioni, tre inviati dal servizio ai client e ogni messaggio conteggiato come due messaggi di 2 KB.

Il numero di messaggi visualizzato nel portale di Azure rimane 0, finché non viene raggiunto il numero di 100 messaggi.

## <a name="how-to-count-connections"></a>Come conteggiare le connessioni

Esistono le connessioni server e le connessioni client. Per impostazione predefinita, ogni server applicazioni ha cinque connessioni per hub con il servizio SignalR e ogni client ha una connessione client con il servizio SignalR.

Il numero di connessioni visualizzato nel portale di Azure include sia le connessioni server che le connessioni client.

Si supponga, ad esempio, di avere due server applicazioni e di definire cinque hub nel codice. Le connessioni server sono 50: 2 server applicazioni * 5 hub * 5 connessioni/hub.

Per ASP.NET SignalR il calcolo delle connessioni server è diverso. È presente un hub predefinito in aggiunta a quelli definiti dal cliente. Per ogni server applicazioni sono necessarie altre 5 connessioni server per impostazione predefinita. Il numero di connessioni per l'hub predefinito rimane coerente con quello degli altri hub.

## <a name="how-to-count-inbound-traffic--outbound-traffic"></a>Come conteggiare il traffico in ingresso/in uscita

Il traffico in ingresso/in uscita viene considerato dal punto di vista del servizio SignalR. Si calcola in byte. Analogamente al numero di messaggi, il traffico include anche una frequenza di campionamento. Il grafico del traffico in ingresso/in uscita nel portale di Azure viene aggiornato ogni 100 KB per hub.

## <a name="related-resources"></a>Risorse correlate

- [Tipo di aggregazione in Monitoraggio di Azure](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [Configurazione di ASP.NET Core SignalR](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)