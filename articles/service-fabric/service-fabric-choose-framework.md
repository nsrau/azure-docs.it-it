---
title: Panoramica dei modelli di programmazione di Service Fabric | Microsoft Docs
description: "Infrastruttura dei servizi offre due framework per la creazione di servizi: il framework per gli attori e quello per i servizi. Offrono compromessi diversi nel controllo nella semplicità e nel controllo."
services: service-fabric
documentationcenter: .net
author: seanmck
manager: timlt
editor: vturecek
ms.assetid: 974b2614-014e-4587-a947-28fcef28b382
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/02/2017
ms.author: vturecek
ms.translationtype: Human Translation
ms.sourcegitcommit: b1d56fcfb472e5eae9d2f01a820f72f8eab9ef08
ms.openlocfilehash: 102ede8d2aafaf485a5212faad47de6781d84578
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017


---
# <a name="service-fabric-programming-model-overview"></a>Panoramica dei modelli di programmazione di Service Fabric
Service Fabric offre diversi modi per scrivere e gestire i servizi. I servizi possono scegliere di usare le API di Service Fabric per sfruttare appieno le funzionalità della piattaforma e i framework applicazione. I servizi possono essere anche qualsiasi programma eseguibile compilato scritto in qualsiasi linguaggio o qualsiasi codice eseguito in un contenitore e ospitato in un cluster di Service Fabric.

### <a name="guest-executables"></a>Eseguibili guest
Un [eseguibile guest](service-fabric-deploy-existing-app.md) è un eseguibile arbitrario esistente, scritto in un qualsiasi linguaggio, ospitato in un cluster di Service Fabric insieme ad altri servizi. Gli eseguibili guest non si integrano direttamente con le API di Service Fabric. Tuttavia sfruttano le funzionalità offerte dalla piattaforma, quali report di integrità e caricamento personalizzati e individuabilità dei servizi mediante chiamate ad API REST. Gli eseguibili guest dispongono anche di supporto completo del ciclo di vita dell'applicazione.

Introduzione agli eseguibili guest con distribuzione della prima [applicazione eseguibile guest](service-fabric-deploy-existing-app.md).

### <a name="containers"></a>Contenitori
Per impostazione predefinita, Service Fabric distribuisce e attiva i servizi come processi. Service Fabric può anche distribuire servizi in [contenitori](service-fabric-containers-overview.md). Service Fabric supporta la distribuzione di contenitori di Linux e contenitori di Windows in Windows Server 2016. È possibile distribuire applicazioni esistenti, servizi senza stato o servizi con stato in contenitori ed è possibile combinare nella stessa applicazione servizi nei processi e servizi nei contenitori.

## <a name="reliable-services"></a>Reliable Services
Reliable Services è un framework leggero per la scrittura di servizi che si integrano con la piattaforma di Service Fabric sfruttandone il set completo di funzionalità. Reliable Services offre un set minimo di API che consentono al runtime di Service Fabric di gestire il ciclo di vita dei servizi e ai servizi di interagire con il runtime. Il framework dell'applicazione è minimo, perciò l'utente mantiene il controllo completo di progettazione e implementazione, e può essere usato per ospitare altri framework di applicazioni, ad esempio ASP.NET Core.

Reliable Services può essere senza stato come la maggior parte delle piattaforme di servizio, ad esempio i server Web, in cui tutte le istanze del servizio sono uguali e lo stato viene mantenuto in una soluzione esterna, ad esempio il database di Azure o l'archiviazione tabelle di Azure.

Reliable Services può essere anche con stato, esclusivamente Service Fabric, in cui lo stato viene mantenuto direttamente nel servizio stesso tramite Reliable Collections. Lo stato viene reso altamente disponibile tramite la replica e distribuito tramite partizionamento, il tutto gestito automaticamente da Service Fabric.

Vedere anche [altre informazioni su Reliable Services](service-fabric-reliable-services-introduction.md) oppure iniziare a [scrivere per la prima volta Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="reliable-actors"></a>Reliable Actors
Realizzato a partire da Reliable Services, il framework Reliable Actor è un framework di applicazioni che implementa il criterio Actor virtuale, basato a sua volta sullo schema progettuale di Actor. Il framework Reliable Actor usa unità di calcolo e di stato indipendenti con esecuzione a thread singolo chiamate attori. Il framework Reliable Actors offre la comunicazione incorporata per gli attori e configurazioni di mantenimento dello stato e scalabilità preimpostate.

Dal momento che Reliable Actors è anch'esso un framework di applicazioni basato su Reliable Services, si integra completamente con la piattaforma di Service Fabric e ne sfrutta appieno il set completo di funzionalità.

### <a name="aspnet-core"></a>ASP.NET Core
Service Fabric è integrato con [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) per la creazione di applicazioni Web e API. 

## <a name="next-steps"></a>Passaggi successivi
Vedere anche [Altre informazioni su Reliable Actors](service-fabric-reliable-actors-introduction.md) oppure a iniziare a [scrivere un servizio di tipo Reliable Actor](service-fabric-reliable-actors-get-started.md)
[Altre informazioni sull'inserimento in contenitori dei servizi in Windows o Linux](service-fabric-deploy-container.md)
[Compilare un front-end di servizio Web per l'applicazione tramite ASP.NET Core](service-fabric-add-a-web-frontend.md)



