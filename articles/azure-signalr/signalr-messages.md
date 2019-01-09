---
title: Messaggi e connessioni in Azure SignalR
description: Panoramica dei concetti chiave relativi a messaggi e connessioni nel servizio Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 5a0430e9ad124319147342c49fc51e11472ac8ff
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/28/2018
ms.locfileid: "53812832"
---
# <a name="message-and-connection-in-azure-signalr-service"></a>Messaggio e connessione nel servizio Azure SignalR

Il servizio Azure SignalR ha modelli di fatturazione basati sul numero di connessioni e di messaggi. Di seguito viene illustrato come i messaggi e le connessioni vengono definiti e conteggiati ai fini della fatturazione.

## <a name="message-formats-supported"></a>Formati di messaggi supportati

Il servizio Azure SignalR supporta gli stessi formati di ASP.NET Core SignalR: [JSON](https://www.json.org/) e [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>Dimensioni dei messaggi

Il servizio Azure SignalR non prevede limiti di dimensioni dei messaggi.

In pratica, i messaggi di grandi dimensioni vengono divisi in messaggi più piccoli, di non più di 2 KB ognuno, e trasmessi come messaggi separati. La suddivisione e l'assemblaggio dei messaggi vengono gestiti dagli SDK. Non è necessario alcun intervento da parte degli sviluppatori.

I messaggi di grandi dimensioni hanno tuttavia un impatto negativo sulle prestazioni della messaggistica. Usare dimensioni inferiori per i messaggi quando è possibile e cercare di scegliere le dimensioni ottimali dei messaggi per ogni scenario di caso d'uso.

## <a name="how-to-count-messages-for-billing-purpose"></a>Come conteggiare i messaggi per la fatturazione

Vengono conteggiati solo i messaggi in uscita dal servizio SignalR e vengono ignorati i messaggi ping tra i client e i server.

Un messaggio che supera i 2 KB viene calcolato come più messaggi da 2 KB ognuno. Il grafico del numero di messaggi nel portale di Azure verrà aggiornato ogni 100 messaggi per hub.

Ad esempio, un utente ha 3 client e 1 server applicazioni. Un client invia un messaggio da 4 KB che il server dovrà trasmettere a tutti i client. Il numero dei messaggi sarà pari a 8: 1 messaggio dal servizio al server applicazioni, 3 messaggi dal servizio ai client e ogni messaggio viene calcolato come 2 messaggi da 2 KB.

Il numero di messaggi visualizzato nel portale di Azure rimane 0, finché non viene raggiunto il numero di 100 messaggi.

## <a name="how-to-count-connections"></a>Come conteggiare le connessioni

Esistono le connessioni server e le connessioni client. Per impostazione predefinita, ogni server applicazioni ha 5 connessioni per hub con il servizio SignalR e ogni client ha 1 connessione client con il servizio SignalR.

Il numero di connessioni visualizzato nel portale di Azure include sia le connessioni server che le connessioni client.

Ad esempio, un utente ha due server applicazioni e definisce 5 hub nei codici. Il numero di connessioni server visualizzato nel portale di Azure sarà 2 server app * 5 hub * 5 connessioni/hub = 50 connessioni server.

## <a name="related-resources"></a>Risorse correlate

- [Configurazione di ASP.NET Core SignalR](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)