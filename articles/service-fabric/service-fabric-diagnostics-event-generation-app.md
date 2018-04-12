---
title: Monitoraggio dell'applicazione Azure Service Fabric | Microsoft Docs
description: Informazioni su log ed eventi a livello di servizio e applicazione usati per monitorare e diagnosticare i cluster di Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/20/2018
ms.author: dekapur
ms.openlocfilehash: f3e7b9c7432538c0f78662213544d4d691652f13
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2018
---
# <a name="application-and-service-level-logging"></a>Registrazione a livello di applicazione e servizio

La strumentazione del codice è la base di molti altri aspetti di monitoraggio dei servizi. La strumentazione è l'unico modo per rilevare eventuali problemi e individuare le correzioni necessarie. Anche se è tecnicamente possibile connettere un debugger a un servizio di produzione, questa non è una procedura comune. I dati dettagliati di strumentazione sono quindi importanti.

Alcuni prodotti instrumentano automaticamente il codice. Benché queste soluzioni siano efficaci, è quasi sempre necessaria una strumentazione manuale. Alla fine, è necessario disporre di informazioni sufficienti per eseguire un debug accurato dell'applicazione. Questo documento illustra diversi approcci alla strumentazione del codice e indicano quando è consigliabile scegliere un approccio rispetto a un altro.

Per esempi su come usare questi suggerimenti, vedere [Aggiungere la registrazione all'applicazione di Service Fabric](service-fabric-how-to-diagnostics-log.md).

## <a name="eventsource"></a>EventSource

Quando si crea una soluzione di Azure Service Fabric da un modello in Visual Studio, viene generata una classe derivata da **EventSource** (**ServiceEventSource** o **ActorEventSource**). Viene creato un modello in cui è possibile aggiungere eventi per l'applicazione o il servizio. Il nome di **EventSource** **deve** essere univoco e deve essere rinominato dalla stringa del modello predefinito MyCompany-&lt;soluzione&gt;-&lt;progetto&gt;. Se esistono più definizioni di **EventSource** con lo stesso nome, potranno verificarsi errori di runtime. Ogni evento definito deve avere un identificatore univoco. Se un identificatore non è univoco, si verificherà un errore di runtime. Alcune organizzazioni preassegnano intervalli di valori per gli identificatori, in modo da evitare conflitti tra team di sviluppo separati. Per altre informazioni, vedere il [blog di Vance](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/) o la [documentazione di MSDN](https://msdn.microsoft.com/library/dn774985(v=pandp.20).aspx).

## <a name="aspnet-core-logging"></a>Registrazione di ASP.NET Core

È importante pianificare con attenzione la strumentazione del codice. Il piano di strumentazione corretto può consentire di evitare la potenziale destabilizzazione della codebase e la conseguente necessità di ripetere la strumentazione del codice. Per ridurre il rischio, è possibile scegliere una libreria di strumentazione come [Microsoft.Extensions.Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), inclusa in Microsoft ASP.NET Core. ASP.NET Core ha un'interfaccia [ILogger](https://docs.microsoft.com/aspnet/core/api/microsoft.extensions.logging.ilogger) che può essere usata con il provider preferito, riducendo al minimo l'effetto sul codice esistente. È possibile usare il codice in ASP.NET Core in Windows e Linux e in .NET Framework completo, in modo da standardizzare la strumentazione del codice.

## <a name="choosing-a-logging-provider"></a>Scelta di un provider di registrazione

Se l'applicazione si basa sulle prestazioni elevate, **EventSource** è in genere l'approccio ottimale. **EventSource** usa *in genere* un numero minore di risorse e offre prestazioni migliori rispetto alla registrazione di ASP.NET Core o di eventuali soluzioni di terze parti disponibili.  Non è un problema per molti servizi, ma se il servizio è orientato alle prestazioni **EventSource** si rivela la scelta più indicata. Tuttavia per ottenere questi vantaggi della registrazione strutturata, **EventSource** richiede un investimento maggiore da parte del team tecnico. Se possibile, effettuare un rapido prototipo di alcune opzioni di registrazione e quindi scegliere quello che meglio soddisfa le esigenze dell'utente.

## <a name="next-steps"></a>Passaggi successivi

Dopo aver scelto il provider di accesso per instrumentare le applicazioni e i servizi, è necessario aggregare i log e gli eventi prima di inviarli a una piattaforma. Per meglio comprendere alcune delle opzioni consigliate leggere altre informazioni su [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) e [WAD](service-fabric-diagnostics-event-aggregation-wad.md).
