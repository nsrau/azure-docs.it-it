---
title: Domande frequenti sul servizio Azure SignalR
description: Ottenere le risposte alle domande frequenti relative al servizio Azure SignalR, sulla risoluzione dei problemi e sugli scenari di utilizzo tipici.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.custom: devx-track-dotnet
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: d5dd765dd9b174ffbfec35b63ad5e55ce84193ad
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2020
ms.locfileid: "89489562"
---
# <a name="azure-signalr-service-faq"></a>Domande frequenti sul servizio Azure SignalR

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Il servizio Azure SignalR può già essere usato per la produzione?

Sì.
Per l'annuncio della disponibilità generale, vedere [Il servizio Azure SignalR è ora disponibile a livello generale](https://azure.microsoft.com/blog/azure-signalr-service-now-generally-available/). 

[ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction) è completamente supportato.

Il supporto per ASP.NET SignalR è ancora in *anteprima pubblica*. [Ecco un esempio di codice](https://github.com/aspnet/AzureSignalR-samples/tree/master/aspnet-samples/ChatRoom).

## <a name="the-client-connection-closes-with-the-error-message-no-server-available-what-does-it-mean"></a>La connessione del client viene chiusa con il messaggio di errore "Nessun server disponibile". Da cosa dipende il problema?

Questo errore si verifica solo quando i client inviano messaggi al servizio Azure SignalR.

Se non sono disponibili server applicazioni e si usa solo l'API REST del servizio Azure SignalR, questo comportamento è *intenzionale*.
Nell'architettura serverless le connessioni dei client sono in modalità *ascolto* e non inviano messaggi al servizio Azure SignalR.
Vedere [altre informazioni sull'API REST](./signalr-quickstart-rest-api.md).

Se invece sono disponibili server applicazioni, questo messaggio di errore indica che nessun server applicazioni è connesso all'istanza del servizio Azure SignalR.

Le possibili cause sono:
- Nessun server applicazioni è connesso al servizio Azure SignalR. Controllare i log dei server applicazioni per trovare possibili errori di connessione. Questo caso è raro nell'impostazione di disponibilità elevata con più di un server applicazioni.
- Sono presenti problemi di connettività con le istanze del servizio Azure SignalR. Questo problema è temporaneo e le istanze verranno recuperate automaticamente.
Se persiste per più di un'ora, [aprire un problema in GitHub](https://github.com/Azure/azure-signalr/issues/new) o [creare una richiesta di supporto in Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

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

È possibile configurare i trasporti sul lato client come descritto in [Configurazione di ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/configuration?view=aspnetcore-2.1&tabs=dotnet#configure-allowed-transports-2).

## <a name="what-is-the-meaning-of-metrics-like-message-count-or-connection-count-shown-in-the-azure-portal-which-kind-of-aggregation-type-should-i-choose"></a>Qual è il significato di metriche come il numero di messaggi o il numero di connessioni mostrato nel portale di Azure? Quale tipo di aggregazione scegliere?

Per informazioni dettagliate su queste metriche, vedere [Messaggi e connessioni nel servizio Azure SignalR](signalr-concept-messages-and-connections.md).

Nel riquadro di panoramica delle risorse del servizio Azure SignalR è già stato scelto il tipo di aggregazione appropriato. Se si passa al riquadro di metriche, è possibile usare il tipo di aggregazione riportato in [Messaggi e connessioni nel servizio Azure SignalR](../azure-monitor/platform/metrics-supported.md#microsoftsignalrservicesignalr) come riferimento.

## <a name="what-is-the-meaning-of-the-default-serverless-and-classic-service-modes-how-can-i-choose"></a>Qual è il significato delle modalità del servizio `Default`, `Serverless` e `Classic`? Come è possibile scegliere?

Ecco le informazioni sulle modalità:
* La modalità `Default` *richiede* un server hub. In questa modalità il servizio Azure SignalR instrada il traffico del client alle relative connessioni del server hub connesso. Il servizio Azure SignalR verifica la disponibilità di un server hub connesso. Se non viene trovato un server hub connesso, il servizio rifiuta le connessioni client in ingresso. È anche possibile usare l'*API di gestione* in questa modalità per gestire i client connessi direttamente tramite il servizio Azure SignalR.
* La modalità `Serverless` *non* consente nessuna connessione server. Ovvero, rifiuterà tutte le connessioni server. Tutti i client devono essere in modalità serverless. I client si connettono al servizio Azure SignalR e gli utenti usano in genere tecnologie serverless come *Funzioni di Azure* per gestire le logiche dell'hub. [Vedere un semplice esempio](https://docs.microsoft.com/azure/azure-signalr/signalr-quickstart-azure-functions-javascript?WT.mc_id=signalrquickstart-github-antchu) che usa la modalità serverless del servizio Azure SignalR.
* La modalità `Classic` è uno stato misto. Quando un hub include una connessione server, il nuovo client verrà instradato a un server hub. In caso contrario, il client passerà in modalità serverless. 

  Questo potrebbe causare un problema. Se ad esempio tutte le connessioni server vanno perse per un momento, alcuni client passeranno in modalità serverless invece di essere instradati a un server hub.

Ecco alcune linee guida per la scelta della modalità:
- Se non è presente alcun server hub, scegliere `Serverless`.
- Se tutti gli hub includono server hub, scegliere `Default`.
- Se alcuni hub includono server hub ma altri no, è possibile scegliere `Classic`, ma si potrebbe verificare un problema. Il modo migliore consiste nel creare due istanze, una in modalità `Serverless` e l'altra in modalità `Default`.

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
