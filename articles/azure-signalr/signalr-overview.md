---
title: Informazioni su Azure SignalR
description: Una panoramica del servizio Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: e66326c6c4d93a92c579255cb00b6614ecc03b8c
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53255177"
---
# <a name="what-is-azure-signalr-service"></a>Informazioni sul servizio Azure SignalR

Il servizio Azure SignalR semplifica il processo di aggiunta di funzionalità Web in tempo reale alle applicazioni tramite HTTP. Questa funzionalità in tempo reale consente al servizio di eseguire il push degli aggiornamenti di contenuto ai client connessi, ad esempio un'applicazione Web o per dispositivi mobili a pagina singola. Di conseguenza, i client vengono aggiornati senza che sia necessario eseguire il polling del server o inviare nuove richieste HTTP per gli aggiornamenti.

Questo articolo offre una panoramica del servizio Azure SignalR.

## <a name="what-is-azure-signalr-service-used-for"></a>Tipi di utilizzo del servizio Azure SignalR

Esistono molti tipi di applicazioni che richiedono aggiornamenti dei contenuti in tempo reale. Gli esempi elencati di seguito sono buoni candidati per l'uso del servizio Azure SignalR:

* App che richiedono aggiornamenti con frequenza elevata dal server, ad esempio giochi, mappe, aste e app GPS e di voto.
* Dashboard e app di monitoraggio, ad esempio dashboard aziendali e aggiornamenti di vendite istantanee.
* App di collaborazione, ad esempio app per lavagne e software per riunioni in team.
* App che richiedono notifiche. Social network, posta elettronica, chat, giochi, avvisi di viaggio e molte altre app usano le notifiche.

SignalR offre un'astrazione su diverse tecniche usate per la compilazione di applicazioni Web in tempo reale. I [WebSocket](https://wikipedia.org/wiki/WebSocket) costituiscono il trasporto ottimale, ma quando non sono disponibili altre opzioni vengono usate altre tecniche come [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) e il polling prolungato. SignalR rileva e inizializza automaticamente il trasporto appropriato in base alle funzionalità supportate nel server e nel client.

Inoltre, SignalR offre un modello di programmazione per applicazioni in tempo reale che consente al server di inviare messaggi a tutte le connessioni o a un sottoinsieme di connessioni che appartengono a un utente specifico o che sono state inserite in un gruppo arbitrario.

## <a name="how-to-use-azure-signalr-service"></a>Modalità di utilizzo del servizio Azure SignalR

Attualmente esistono tre modi per usare il servizio Azure SignalR:

- **[Ridimensionare un'app ASP.NET Core SignalR](signalr-overview-scale-aspnet-core.md)**: integrare il servizio Azure SignalR in un'applicazione ASP.NET Core SignalR per la scalabilità orizzontale fino a centinaia di migliaia di connessioni.
- **[Creare app in tempo reale senza server ](signalr-overview-azure-functions.md)**: usare l'integrazione delle Funzioni di Azure con il servizio Azure SignalR per creare applicazioni in tempo reale senza server in linguaggi come JavaScript, C# e Java.
- **[Inviare messaggi dal server ai client tramite l'API REST](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md)**: il servizio Azure SignalR offre l'API REST per consentire alle applicazioni di inviare messaggi ai client connessi con il servizio SignalR, in qualsiasi linguaggio di programmazione che supporta REST.