---
title: Domande frequenti sul servizio Azure SignalR
description: Domande frequenti sul servizio Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 12/23/2018
ms.author: zhshang
ms.openlocfilehash: 45ceebef85254ff030d237f1af13f0750aaafeba
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/28/2018
ms.locfileid: "53812789"
---
# <a name="azure-signalr-service-faq"></a>Domande frequenti sul servizio Azure SignalR

## <a name="is-azure-signalr-service-ready-for-production-use"></a>Il servizio Azure SignalR può già essere usato per la produzione?

Sì.
Per l'annuncio della disponibilità generale, vedere [Azure SignalR Service now generally available](https://azure.microsoft.com/en-us/blog/azure-signalr-service-now-generally-available/) (Il servizio Azure SignalR è ora disponibile a livello generale). 

[ASP.NET Core SignalR](https://docs.microsoft.com/en-us/aspnet/core/signalr/introduction) è completamente supportato.

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
Se persiste per più di un'ora, [aprire un problema in GitHub](https://github.com/Azure/azure-signalr/issues/new) o [creare una richiesta di supporto in Azure](https://docs.microsoft.com/en-us/azure/azure-supportability/how-to-create-azure-support-request).

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
È possibile controllare il codice sorgente [qui](https://github.com/Azure/azure-signalr/blob/kevinzha/faq/src/Microsoft.Azure.SignalR/ServiceHubConnectionContext.cs).

## <a name="can-i-configure-the-transports-available-in-signalr-service-as-configuring-it-on-server-side-with-aspnet-core-signalr-for-example-disable-websocket-transport"></a>È possibile configurare i trasporti disponibili nel servizio SignalR mentre si esegue la configurazione lato server con ASP.NET Core SignalR? Ad esempio, disabilitando il trasporto WebSocket?

 No.

Il servizio Azure SignalR fornisce tutti e tre i trasporti supportati da ASP.NET Core SignalR per impostazione predefinita. Non è configurabile. Il servizio SignalR gestirà le connessioni e i trasporti per tutte le connessioni client.

È possibile configurare i trasporti lato client, come illustrato [qui](https://docs.microsoft.com/en-us/aspnet/core/signalr/configuration?view=aspnetcore-2.1#configure-allowed-transports).
