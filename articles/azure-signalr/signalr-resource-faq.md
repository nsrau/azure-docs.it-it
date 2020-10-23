---
title: Domande frequenti sul servizio Azure SignalR
description: Ottenere le risposte alle domande frequenti relative al servizio Azure SignalR, sulla risoluzione dei problemi e sugli scenari di utilizzo tipici.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 910de9efbd132fb98a0c4bd596867800f65f5ad5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150959"
---
# <a name="azure-signalr-service-faq"></a>Domande frequenti sul servizio Azure SignalR

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Il servizio Azure SignalR può già essere usato per la produzione?

Sì, sia il supporto per [ASP.NET Core SignalR](https://dotnet.microsoft.com/apps/aspnet/signalr) che quello per [ASP.NET SignalR](/aspnet/signalr/overview/getting-started/introduction-to-signalr) sono disponibili a livello generale.

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Quando sono presenti più server applicazioni, i messaggi client vengono inviati a tutti i server o solo a uno?

Esiste un mapping uno-a-uno tra il client e il server applicazioni. I messaggi da un client vengono sempre inviati allo stesso server applicazioni.

Il mapping viene mantenuto finché il client o il server non si disconnette.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Se uno dei server applicazioni è inattivo, come è possibile trovarlo e ricevere una notifica?

Il servizio Azure SignalR monitora gli heartbeat provenienti dal server applicazioni.
Se gli heartbeat non vengono ricevuti per un periodo di tempo specificato, il server applicazioni viene considerato offline. Tutte le connessioni client mappate a questo server applicazioni verranno interrotte.

## <a name="why-does-my-custom-iuseridprovider-throw-an-exception-when-im-switching-from-aspnet-core-signalr-sdk-to-azure-signalr-service-sdk"></a>Perché l'elemento `IUserIdProvider` personalizzato ha generato un'eccezione durante il passaggio da ASP.NET Core SignalR SDK ad Azure SignalR Service SDK?

Il parametro `HubConnectionContext context` è diverso in ASP.NET Core SignalR SDK e in Azure SignalR Service SDK quando viene chiamato `IUserIdProvider`.

In ASP.NET Core SignalR `HubConnectionContext context` è il contesto dalla connessione client fisica con valori validi per tutte le proprietà.

In Azure SignalR Service SDK `HubConnectionContext context` è il contesto della connessione del client logico. All'istanza del servizio Azure SignalR viene connesso il client fisico, quindi viene fornito solo un numero limitato di proprietà.

Per il momento, solo `HubConnectionContext.GetHttpContext()` e `HubConnectionContext.User` sono disponibili per l'accesso.
È possibile [controllare il codice sorgente](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-azure-signalr-service-on-the-server-side-with-aspnet-core-signalr-for-example-can-i-disable-websocket-transport"></a>È possibile configurare i trasporti disponibili nel servizio Azure SignalR mentre sul lato server con ASP.NET Core SignalR? Ad esempio, è possibile disabilitare il trasporto WebSocket?

No.

Il servizio Azure SignalR fornisce tutti e tre i trasporti supportati da ASP.NET Core SignalR per impostazione predefinita. Non è configurabile. Il servizio Azure SignalR gestirà le connessioni e i trasporti per tutte le connessioni client.

È possibile configurare i trasporti sul lato client come descritto in [Configurazione di ASP.NET Core SignalR](/aspnet/core/signalr/configuration#configure-allowed-transports-1).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Qual è il significato di metriche come il numero di messaggi o il numero di connessioni mostrato nel portale di Azure? Quale tipo di aggregazione scegliere?

Per informazioni dettagliate su queste metriche, vedere [Messaggi e connessioni nel servizio Azure SignalR](signalr-concept-messages-and-connections.md).

Nel riquadro di panoramica delle risorse del servizio Azure SignalR è già stato scelto il tipo di aggregazione appropriato. Se si passa al riquadro di metriche, è possibile usare il tipo di aggregazione riportato in [Messaggi e connessioni nel servizio Azure SignalR](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) come riferimento.

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Qual è il significato delle modalità del servizio `Default`, `Serverless` e `Classic`? Come è possibile scegliere?

Per le nuove applicazioni, è consigliabile usare solo la modalità predefinita e quella serverless. La differenza principale riguarda la presenza di server applicazioni che stabiliscono le connessioni dei server al servizio (ad esempio, l'uso di `AddAzureSignalR()` per connettersi al servizio). Se tali server sono presenti, usare la modalità predefinita, altrimenti usare la modalità serverless.

La modalità classica è progettata per la compatibilità con le versioni precedenti per le applicazioni esistenti e non deve quindi essere usata per le nuove applicazioni.

Per altre informazioni sulle modalità del servizio, vedere [questo documento](concept-service-mode.md).

## <a name="can-i-send-message-from-client-in-serverless-mode"></a>È possibile inviare un messaggio dal client in modalità serverless?

È possibile inviare un messaggio dal client se si configura la funzionalità upstream nell'istanza di SignalR. La funzionalità upstream è un set di endpoint in grado di ricevere messaggi ed eventi di connessione dal Servizio SignalR. Se la funzionalità upstream non è configurata, i messaggi del client verranno ignorati.

Per altre informazioni sulla funzionalità upstream, vedere [questo documento](concept-upstream.md).

La funzionalità upstream è attualmente disponibile in anteprima pubblica.

## <a name="are-there-any-feature-differences-in-using-azure-signalr-service-with-aspnet-signalr"></a>Esistono differenze tra le funzionalità quando si usa il servizio Azure SignalR con ASP.NET SignalR?

Quando si usa il servizio Azure SignalR, alcune API e funzionalità di ASP.NET SignalR non sono supportate:
- La possibilità di passare uno stato arbitrario tra i client e l'hub (spesso definito `HubState`) non è supportata.
- La classe `PersistentConnection` non è supportata.
- Il *trasporto Forever Frame* non è supportato.
- Il servizio Azure SignalR non riproduce più i messaggi inviati al client quando il client è offline.
- Quando si usa il servizio Azure SignalR, il traffico per una connessione client viene sempre instradato (*sticky*) a un'istanza del server app per la durata della connessione.

Il supporto di ASP.NET SignalR è incentrato sulla compatibilità, quindi non sono supportate tutte le nuove funzionalità di ASP.NET Core SignalR. Ad esempio, *MessagePack* e *Streaming* sono disponibili solo per le applicazioni ASP.NET Core SignalR.

È possibile configurare il servizio Azure SignalR per diverse modalità: `Classic`, `Default` e `Serverless`. La modalità `Serverless` non è supportata per ASP.NET. Neanche l'API REST del piano dati è supportata.

## <a name="where-does-my-data-reside"></a>Dove si trovano i dati?

Il servizio Azure SignalR funziona come servizio di elaborazione dati. Non archivia contenuto dei clienti e la residenza dei dati è inclusa per progettazione. Se si usa il servizio Azure SignalR insieme ad altri servizi di Azure, ad esempio Archiviazione di Azure per la diagnostica, vedere [questo white paper](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) per informazioni su come mantenere la residenza dei dati nelle aree di Azure.