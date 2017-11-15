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
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 237b8396b56fdec86cc005c121646556825d8e98
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2017
---
# <a name="service-fabric-programming-model-overview"></a>Panoramica dei modelli di programmazione di Service Fabric
Service Fabric offre diversi modi per scrivere e gestire i servizi. I servizi possono scegliere di usare le API di Service Fabric per sfruttare appieno le funzionalità della piattaforma e i framework applicazione. I servizi possono essere anche qualsiasi programma eseguibile compilato scritto in qualsiasi linguaggio o qualsiasi codice eseguito in un contenitore e ospitato in un cluster di Service Fabric.

## <a name="guest-executables"></a>Eseguibili guest
Un [eseguibile guest](service-fabric-deploy-existing-app.md) è un eseguibile arbitrario esistente, scritto in un qualsiasi linguaggio, che può essere eseguito come servizio nell'applicazione. I file eseguibili guest non chiamano direttamente le API di Service Fabric SDK. Tuttavia sfruttano le funzionalità offerte dalla piattaforma, quali individuabilità dei servizi , report di integrità e caricamento personalizzati mediante chiamate ad API REST esposte da Service Fabric. Gli eseguibili guest dispongono anche di supporto completo del ciclo di vita dell'applicazione.

Introduzione agli eseguibili guest con distribuzione della prima [applicazione eseguibile guest](service-fabric-deploy-existing-app.md).

## <a name="containers"></a>Contenitori
Per impostazione predefinita, Service Fabric distribuisce e attiva i servizi come processi. Service Fabric può anche distribuire servizi in [contenitori](service-fabric-containers-overview.md). Service Fabric supporta la distribuzione di contenitori di Linux e contenitori di Windows in Windows Server 2016. Le immagini del contenitore possono essere estratte da qualsiasi archivio del contenitore e distribuite al computer. È possibile distribuire applicazioni esistenti, come eseguibili guest, servizi senza stato di Service Fabric o servizi Reliable con stato in contenitori ed è possibile combinare nella stessa applicazione servizi nei processi e servizi nei contenitori.

[Altre informazioni sull'inserimento in contenitori dei servizi in Windows o Linux](service-fabric-deploy-container.md)

## <a name="reliable-services"></a>Reliable Services
Reliable Services è un framework leggero per la scrittura di servizi che si integrano con la piattaforma di Service Fabric sfruttandone il set completo di funzionalità. Reliable Services offre un set minimo di API che consentono al runtime di Service Fabric di gestire il ciclo di vita dei servizi e ai servizi di interagire con il runtime. Il framework dell'applicazione è minimo, perciò l'utente mantiene il controllo completo di progettazione e implementazione, e può essere usato per ospitare altri framework di applicazioni, ad esempio ASP.NET Core.

Reliable Services può essere senza stato come la maggior parte delle piattaforme di servizio, ad esempio i server Web, in cui tutte le istanze del servizio sono uguali e lo stato viene mantenuto in una soluzione esterna, ad esempio il database di Azure o l'archiviazione tabelle di Azure.

Reliable Services può essere anche con stato, esclusivamente Service Fabric, in cui lo stato viene mantenuto direttamente nel servizio stesso tramite Reliable Collections. Lo stato viene reso altamente disponibile tramite la replica e distribuito tramite partizionamento, il tutto gestito automaticamente da Service Fabric.

Vedere anche [altre informazioni su Reliable Services](service-fabric-reliable-services-introduction.md) oppure iniziare a [scrivere per la prima volta Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="aspnet-core"></a>ASP.NET Core
ASP.NET Core è un nuovo framework open source e multipiattaforma per la compilazione di applicazioni Internet moderne basate sul cloud, ad esempio app Web, app per IoT e back-end per dispositivi mobili. L'integrazione di Service Fabric con ASP.NET Core consente di scrivere applicazioni ASP.NET Core sia con stato che senza stato che sfruttano i vantaggi di Reliable Collections e delle funzionalità di orchestrazione avanzata di Service Fabric.

Per altre informazioni, vedere l'[introduzione all'uso di ASP.NET Core in Service Fabric](service-fabric-reliable-services-communication-aspnetcore.md) o iniziare a [creare la prima applicazione ASP.NET Core in Service Fabric](service-fabric-add-a-web-frontend.md).

## <a name="reliable-actors"></a>Reliable Actors
Realizzato a partire da Reliable Services, il framework Reliable Actor è un framework di applicazioni che implementa il criterio Actor virtuale, basato a sua volta sullo schema progettuale di Actor. Il framework Reliable Actor usa unità di calcolo e di stato indipendenti con esecuzione a thread singolo chiamate attori. Il framework Reliable Actors offre la comunicazione incorporata per gli attori e configurazioni di mantenimento dello stato e scalabilità preimpostate.

Dal momento che Reliable Actors è anch'esso un framework di applicazioni basato su Reliable Services, si integra completamente con la piattaforma di Service Fabric e ne sfrutta appieno il set completo di funzionalità.

Vedere anche [altre informazioni su Reliable Actors](service-fabric-reliable-actors-introduction.md) oppure iniziare a [scrivere per la prima volta un servizio di tipo Reliable Actor](service-fabric-reliable-actors-get-started.md)


[Creare un servizio front-end usando ASP.NET Core](service-fabric-add-a-web-frontend.md)

## <a name="next-steps"></a>Passaggi successivi
[Panoramica di Service Fabric e contenitori](service-fabric-containers-overview.md)

[Panoramica di Reliable Services](service-fabric-reliable-services-introduction.md)

[Panoramica di Reliable Actors](service-fabric-reliable-actors-introduction.md)

[Service Fabric e ASP.NET Core ](service-fabric-reliable-services-communication-aspnetcore.md)




