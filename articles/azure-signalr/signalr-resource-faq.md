---
title: Domande frequenti sul servizio Azure SignalR
description: Consente di accedere rapidamente alle domande frequenti sul Servizio Azure SignalR, sulla risoluzione dei problemi e sugli scenari di utilizzo tipici.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: 9aa510a055cb76b30508cb98a25cd9c919eb117d
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/26/2020
ms.locfileid: "88928310"
---
# <a name="azure-signalr-service-faq"></a>Domande frequenti sul servizio Azure SignalR

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Il servizio Azure SignalR può già essere usato per la produzione?

Sì.
Per l'annuncio della disponibilità generale, vedere [Azure SignalR Service now generally available](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/) (Il servizio Azure SignalR è ora disponibile a livello generale). 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) è completamente supportato.

Il supporto per ASP.NET SignalR è ancora in *anteprima pubblica*. Ecco un [esempio di codice](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>La connessione client viene chiusa con il messaggio di errore "Nessun server disponibile". Da cosa dipende il problema?

Questo errore si verifica solo quando i client inviano messaggi al servizio SignalR.

Se non sono disponibili server applicazioni e si usa solo l'API REST del servizio SignalR, questo comportamento è **intenzionale**.
Nell'architettura serverless le connessioni client sono in modalità **ASCOLTO** e non inviano messaggi al servizio SignalR.
Sono disponibili altre informazioni sull'[API REST](./signalr-quickstart-rest-api.md).

Se invece sono disponibili server applicazioni, questo messaggio di errore indica che nessun server applicazioni è connesso all'istanza del servizio SignalR.

Le possibili cause sono:
- Nessun server applicazioni è connesso al servizio SignalR. Controllare i log dei server applicazioni per trovare possibili errori di connessione. Questo caso è raro nell'impostazione di disponibilità elevata con più di un server applicazioni.
- Sono presenti problemi di connettività con le istanze del servizio SignalR. Questo problema è temporaneo e verrà risolto automaticamente.
Se persiste per più di un'ora, [aprire un problema in GitHub](https://github.com/Azure/azure-signalr/issues/new) o [creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

## <a name="when-there-are-multiple-application-servers-are-client-messages-sent-to-all-servers-or-just-one-of-them"></a>Quando sono presenti più server applicazioni, i messaggi client vengono inviati a tutti i server o solo a uno?

Si tratta di un mapping uno-a-uno tra il client e il server applicazioni. I messaggi da un client vengono sempre inviati allo stesso server applicazioni.

Il mapping tra il client e il server applicazioni verrà mantenuto finché il client o il server applicazioni non si disconnette.

## <a name="if-one-of-my-application-servers-is-down-how-can-i-find-it-and-get-notified"></a>Se uno dei server applicazioni è inattivo, come è possibile trovarlo e ricevere una notifica?

Il servizio SignalR esegue il monitoraggio degli heartbeat dal server applicazioni.
Se gli heartbeat non vengono ricevuti per un periodo di tempo specificato, il server applicazioni viene considerato offline. Tutte le connessioni client mappate a questo server applicazioni verranno interrotte.

## <a name="why-does-my-custom-iuseridprovider-throw-exception-when-switching-from-aspnet-core-signalr--sdk-to-azure-signalr-service-sdk"></a>Perché l'elemento `IUserIdProvider` personalizzato ha generato un'eccezione durante il passaggio da ASP.NET Core SignalR SDK ad Azure SignalR Service SDK?

Il parametro `HubConnectionContext context` è diverso in ASP.NET Core SignalR SDK e in Azure SignalR Service SDK quando viene chiamato `IUserIdProvider`.

In ASP.NET Core SignalR `HubConnectionContext context` è il contesto dalla connessione client fisica con valori validi per tutte le proprietà.

In Azure SignalR Service SDK `HubConnectionContext context` è il dalla connessione client logica. La connessione client fisica viene stabilita con l'istanza del servizio SignalR, quindi viene fornito solo un numero limitato di proprietà.

Per il momento, solo `HubConnectionContext.GetHttpContext()` e `HubConnectionContext.User` sono disponibili per l'accesso.
È possibile controllare il codice sorgente [qui](https://github.com/Azure/azure-signalr/blob/dev/src/Microsoft.Azure.SignalR/HubHost/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>È possibile configurare i trasporti disponibili nel servizio SignalR mentre si esegue la configurazione lato server con ASP.NET Core SignalR? Ad esempio, disabilitando il trasporto WebSocket?

No.

Il servizio Azure SignalR fornisce tutti e tre i trasporti supportati da ASP.NET Core SignalR per impostazione predefinita. Non è configurabile. Il servizio SignalR gestirà le connessioni e i trasporti per tutte le connessioni client.

È possibile configurare i trasporti lato client, come illustrato [qui](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-showed-in-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Qual è il significato di metriche come il numero di messaggi o il numero di connessioni mostrato nel portale di Azure? Quale tipo di aggregazione scegliere?

Per informazioni dettagliate su come vengono calcolate queste metriche, vedere [qui](signalr-concept-messages-and-connections.md).

Nel pannello Panoramica delle risorse del Servizio Azure SignalR è già stato scelto il tipo di aggregazione appropriato. Se si passa al pannello Metriche, è possibile usare il tipo di aggregazione indicato [qui](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) come riferimento.

## <a name="what-is-the-meaning-of-service-mode-defaultserverlessclassic-how-can-i-choose"></a>Qual è il significato della modalità servizio `Default`/`Serverless`/`Classic`? Come è possibile scegliere?

Modalità:
* La modalità `Default` *richiede* un server di hub. In questa modalità Azure SignalR instrada il traffico del client alle relative connessioni del server hub connesso. Azure SignalR verifica la disponibilità di un server hub connesso. Se non viene trovato un server hub connesso, Azure SignalR rifiuta le connessioni client in ingresso. È anche possibile usare l'**API di gestione** in questa modalità per gestire i client connessi direttamente tramite Azure SignalR.
* La modalità `Serverless` *non* consente alcuna connessione al server, ovvero verranno rifiutate tutte le connessioni al server. Tutti i client devono essere in modalità serverless. I client si connettono ad Azure SignalR e gli utenti usano in genere tecnologie serverless come **Funzioni di Azure** per gestire le logiche dell'hub. Vedere un [semplice esempio](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu) che usa la modalità serverless di Azure SignalR.
* La modalità `Classic` è uno stato misto. Quando un hub ha una connessione al server, il nuovo client verrà indirizzato al server di hub, in caso contrario, il client entrerà in modalità serverless.

  Ciò potrebbe causare un problema, ad esempio, tutte le connessioni al server vengono perse per un momento, alcuni client entreranno in modalità serverless, invece di essere indirizzati al server di hub.

Scegliere:
1. Nessun server di hub, scegliere `Serverless`.
1. Tutti gli hub hanno un server di hub, scegliere `Default`.
1. Alcuni hub hanno un server di hub, altri no, scegliere `Classic`, ma ciò potrebbe causare un problema. Il modo migliore è creare due istanze, una è `Serverless`, un'altra è `Default`.

## <a name="any-feature-differences-when-using-azure-signalr-for-aspnet-signalr"></a>Ci sono differenze tra le funzionalità quando si usa Azure SignalR per ASP.NET SignalR?
Quando si usa Azure SignalR, alcune API e funzionalità di ASP.NET SignalR non sono più supportate:
- La possibilità di passare uno stato arbitrario tra i client e l'hub (spesso denominati `HubState`) non è supportata quando si usa Azure SignalR
- La classe `PersistentConnection` non è ancora supportata quando si usa Azure SignalR
- **Il trasporto Forever Frame** non è supportato quando si usa Azure SignalR
- Azure SignalR non riproduce più i messaggi inviati al client quando il client è offline
- Quando si usa Azure SignalR, il traffico per una connessione client viene sempre indirizzato a un'istanza del server app per la durata della connessione (denominata anche **modalità permanete**)

Il supporto di ASP.NET SignalR è incentrato sulla compatibilità, quindi non sono supportate tutte le nuove funzionalità di ASP.NET Core SignalR. Ad esempio, **MessagePack**, **Streaming** e così via, sono disponibili solo per le applicazioni ASP.NET Core SignalR.

Il servizio SignalR può essere configurato per modalità di servizio diverse: `Classic`/`Default`/`Serverles`s. In questo supporto di ASP.NET la modalità `Serverless` non è supportata. Neanche l'API REST del piano dati è supportata.

## <a name="where-do-my-data-reside"></a>Dove si trovano i dati?

Il Servizio Azure SignalR funziona come servizio di elaborazione dati. Non verrà archiviato alcun contenuto del cliente e la residenza dei dati è garantita per impostazione predefinita. Se si usa il Servizio Azure SignalR insieme ad altri servizi di Azure, ad esempio Archiviazione di Azure per la diagnostica, controllare [qui](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/) per ottenere informazioni su come conservare la residenza dei dati nelle aree di Azure.
