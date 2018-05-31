---
title: Informazioni su Azure SignalR | Microsoft Docs
description: Una panoramica del servizio Azure SignalR.
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/17/2018
ms.author: wesmc
ms.openlocfilehash: bc144fb1d7db9251871e7e181b012417a32de7e6
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/08/2018
ms.locfileid: "33868110"
---
# <a name="what-is-azure-signalr-service"></a>Informazioni sul servizio Azure SignalR

Il servizio Microsoft Azure SignalR è attualmente disponibile in [anteprima pubblica](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Il servizio Azure SignalR è un servizio di Azure basato su [ASP.NET Core SignalR](https://docs.microsoft.com/aspnet/core/signalr/introduction). ASP.NET Core SignalR è una [libreria open source](https://github.com/aspnet/signalr) che semplifica il processo di aggiunta di funzionalità Web in tempo reale alle applicazioni tramite HTTP. Questa funzionalità in tempo reale consente al server Web di eseguire il push degli aggiornamenti dei contenuti ai client connessi. Di conseguenza, i client vengono aggiornati senza che sia necessario eseguire il polling del server o inviare nuove richieste HTTP per gli aggiornamenti.

Questo articolo offre una panoramica del servizio Azure SignalR. Per iniziare, vedere la [guida introduttiva di ASP.NET Core](signalr-quickstart-dotnet-core.md).

## <a name="what-is-signalr-service-used-for"></a>Per cosa viene usato il servizio SignalR? 

Esistono molti tipi di applicazioni che richiedono aggiornamenti dei contenuti in tempo reale. I tipi di applicazioni di esempio elencati di seguito sono buoni candidati per l'uso del servizio Azure SignalR:

* App che richiedono aggiornamenti con frequenza elevata dal server, ad esempio giochi, social network, mappe, aste e app GPS e di voto.
* Dashboard e app di monitoraggio, ad esempio dashboard aziendali, aggiornamenti di vendite istantanee o avvisi di viaggio.
* App di collaborazione, ad esempio app per lavagne e software per riunioni in team.
* App che richiedono notifiche. Social network, posta elettronica, chat, giochi, avvisi di viaggio e molte altre app usano le notifiche.

Internamente, SignalR è un'astrazione su diverse tecniche usate per la compilazione di applicazioni Web in tempo reale. I [WebSocket](https://wikipedia.org/wiki/WebSocket) costituiscono il trasporto ottimale, ma quando non sono disponibili altre opzioni vengono usate altre tecniche come [Server-Sent Events (SSE)](https://wikipedia.org/wiki/Server-sent_events) e il polling prolungato. SignalR rileva e inizializza automaticamente il trasporto appropriato in base alle funzionalità supportate nel server e nel client.

## <a name="developing-signalr-apps"></a>Sviluppo di app SignalR

Attualmente, sono disponibili due versioni di SignalR utilizzabili con le applicazioni Web: SignalR per ASP.NET e ASP.NET Core SignalR, ovvero la versione più recente. Il servizio Azure SignalR è un servizio gestito da Azure basato su ASP.NET Core SignalR. 

ASP.NET Core SignalR è una riscrittura della versione precedente. Di conseguenza, ASP.NET Core SignalR non è compatibile con la versione precedente di SignalR. Le API e i comportamenti sono diversi. ASP.NET Core SignalR SDK è .NET Standard, pertanto è ancora possibile usarlo con .NET Framework. Tuttavia, è necessario usare le nuove API al posto di quelle precedenti. Se si usa SignalR e si vuole passare ad ASP.NET Core SignalR o al servizio Azure SignalR, sarà necessario apportare modifiche al codice per gestire le differenze tra le API.

Con il servizio Azure SignalR, il componente sul lato server di ASP.NET Core SignalR è ospitato in Azure. Tuttavia, poiché la tecnologia si basa su ASP.NET Core, è possibile eseguire l'applicazione Web effettiva su più piattaforme (Windows, Linux e MacOS) durante l'hosting con [Servizio app di Azure](../app-service/app-service-web-overview.md), [IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), [Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache) e [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index). È inoltre possibile usare il self-hosting in un processo personalizzato.

Se gli obiettivi dell'applicazione includono il supporto delle funzionalità più recenti per l'aggiornamento dei client Web con gli aggiornamenti dei contenuti in tempo reale, l'esecuzione tra più piattaforme (Azure, Windows, Linux e MacOS) e l'hosting in ambienti diversi, la scelta migliore potrebbe essere quella di usare il servizio Azure SignalR.


## <a name="why-not-deploy-signalr-myself"></a>Perché non distribuire direttamente SignalR?

È ancora un approccio valido distribuire un'app Web di Azure che supporta ASP.NET Core SignalR come componente di back-end per l'applicazione Web generale.

Uno dei motivi principali per usare il servizio Azure SignalR è la semplicità. Con il servizio Azure SignalR, non è necessario gestire problemi di prestazioni, scalabilità e disponibilità. Questi problemi vengono gestiti automaticamente con un contratto di servizio con disponibilità del 99,9%.

Inoltre, i WebSocket sono in genere la tecnologia preferita per supportare gli aggiornamenti dei contenuti in tempo reale. Tuttavia, il bilanciamento del carico di un numero elevato di connessioni WebSocket permanenti diventa un problema complesso da risolvere in caso di ridimensionamento. Le soluzioni comuni usano il bilanciamento del carico DNS, i servizi di bilanciamento del carico hardware e il bilanciamento del carico software. Il servizio Azure SignalR gestisce questo problema automaticamente.

Un altro motivo potrebbe essere che non è richiesto ospitare effettivamente un'applicazione Web. La logica dell'applicazione Web può sfruttare l'[elaborazione senza server](https://azure.microsoft.com/overview/serverless-computing/). Ad esempio, il codice potrebbe essere solo ospitato ed eseguito su richiesta con trigger di [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/). Questo scenario può risultare complesso perché il codice viene eseguito solo su richiesta e non mantiene lunghe connessioni con i client. Il servizio Azure SignalR può gestire questa situazione dal momento che il servizio gestisce già automaticamente le connessioni.

## <a name="how-does-it-scale"></a>Come avviene il ridimensionamento?

È comune ridimensionare SignalR con SQL Server, il bus di servizio di Azure o Cache Redis. Il servizio Azure SignalR gestisce automaticamente l'approccio di ridimensionamento. Le prestazioni e i costi sono paragonabili a questi approcci senza la complessità di gestione di questi altri servizi. È sufficiente aggiornare il numero di unità per il servizio. Ogni unità di servizio supporta fino a 1000 connessioni client.

## <a name="next-steps"></a>Passaggi successivi
* [Guida introduttiva: Creare una chat room con Azure SignalR](signalr-quickstart-dotnet-core.md)  
  

