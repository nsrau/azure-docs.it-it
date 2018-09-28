---
title: Ridimensionare ASP.NET Core SignalR con Azure SignalR | Microsoft Docs
description: Panoramica dell'uso del servizio Azure SignalR per ridimensionare le applicazioni ASP.NET Core SignalR.
services: signalr
documentationcenter: ''
author: sffamily
manager: cfowler
editor: ''
ms.service: signalr
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 283566558584960dc2f3361c539093a4c5ee78aa
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46958238"
---
# <a name="scale-aspnet-core-signalr-applications-with-azure-signalr-service"></a>Ridimensionare le applicazioni ASP.NET Core SignalR con il servizio Azure SignalR

## <a name="developing-signalr-apps"></a>Sviluppo di app SignalR

Attualmente, sono disponibili [due versioni](https://docs.microsoft.com/en-us/aspnet/core/signalr/version-differences) di SignalR utilizzabili con le applicazioni Web: SignalR per ASP.NET e ASP.NET Core SignalR, ovvero la versione più recente. Il servizio Azure SignalR è un servizio gestito da Azure basato su ASP.NET Core SignalR. 

ASP.NET Core SignalR è una riscrittura della versione precedente. Di conseguenza, ASP.NET Core SignalR non è compatibile con la versione precedente di SignalR. Le API e i comportamenti sono diversi. ASP.NET Core SignalR SDK si basa su .NET Standard, pertanto è ancora possibile usarlo con .NET Framework. Tuttavia, è necessario usare le nuove API al posto di quelle precedenti. Se si usa SignalR e si vuole passare ad ASP.NET Core SignalR o al servizio Azure SignalR, sarà necessario apportare modifiche al codice per gestire le differenze tra le API.

Con il servizio Azure SignalR, il componente sul lato server di ASP.NET Core SignalR è ospitato in Azure. Tuttavia, poiché la tecnologia si basa su ASP.NET Core, è possibile eseguire l'applicazione Web effettiva su più piattaforme (Windows, Linux e MacOS) durante l'hosting con [Servizio app di Azure](../app-service/app-service-web-overview.md), [IIS](https://docs.microsoft.com/aspnet/core/host-and-deploy/iis/index), [Nginx](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-nginx), [Apache](https://docs.microsoft.com/aspnet/core/host-and-deploy/linux-apache) e [Docker](https://docs.microsoft.com/aspnet/core/host-and-deploy/docker/index). È inoltre possibile usare il self-hosting in un processo personalizzato.

Se gli obiettivi dell'applicazione includono il supporto delle funzionalità più recenti per l'aggiornamento dei client Web con gli aggiornamenti dei contenuti in tempo reale, l'esecuzione tra più piattaforme (Azure, Windows, Linux e MacOS) e l'hosting in ambienti diversi, la scelta migliore potrebbe essere quella di usare il servizio Azure SignalR.


## <a name="why-not-deploy-signalr-myself"></a>Perché non distribuire direttamente SignalR?

È ancora un approccio valido distribuire un'app Web di Azure che supporta ASP.NET Core SignalR come componente di back-end per l'applicazione Web generale.

Uno dei motivi principali per usare il servizio Azure SignalR è la semplicità. Con il servizio Azure SignalR, non è necessario gestire problemi di prestazioni, scalabilità e disponibilità. Questi problemi vengono gestiti automaticamente con un contratto di servizio con disponibilità del 99,9%.

Inoltre, i WebSocket sono in genere la tecnologia preferita per supportare gli aggiornamenti dei contenuti in tempo reale. Tuttavia, il bilanciamento del carico di un numero elevato di connessioni WebSocket permanenti diventa un problema complesso da risolvere in caso di ridimensionamento. Le soluzioni comuni usano il bilanciamento del carico DNS, i servizi di bilanciamento del carico hardware e il bilanciamento del carico software. Il servizio Azure SignalR gestisce questo problema automaticamente.

Un altro motivo potrebbe essere che non è richiesto ospitare effettivamente un'applicazione Web. La logica dell'applicazione Web può sfruttare l'[elaborazione senza server](https://azure.microsoft.com/overview/serverless-computing/). Ad esempio, il codice potrebbe essere solo ospitato ed eseguito su richiesta con trigger di [Funzioni di Azure](https://docs.microsoft.com/azure/azure-functions/). Questo scenario può risultare complesso perché il codice viene eseguito solo su richiesta e non mantiene lunghe connessioni con i client. Il servizio Azure SignalR può gestire questa situazione dal momento che il servizio gestisce già automaticamente le connessioni. Vedere la [panoramica su come usare il servizio SignalR con le funzioni di Azure](signalr-overview-azure-functions.md) per altri dettagli. 

## <a name="how-does-it-scale"></a>Come avviene il ridimensionamento?

È comune ridimensionare SignalR con SQL Server, il bus di servizio di Azure o Cache Redis. Il servizio Azure SignalR gestisce automaticamente l'approccio di ridimensionamento. Le prestazioni e i costi sono paragonabili a questi approcci senza la complessità di gestione di questi altri servizi. È sufficiente aggiornare il numero di unità per il servizio. Ogni unità supporta fino a 1000 connessioni client.

## <a name="next-steps"></a>Passaggi successivi
* [Guida introduttiva: Creare una chat room con Azure SignalR](signalr-quickstart-dotnet-core.md)  
  

