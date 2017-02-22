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
ms.date: 1/05/2016
ms.author: seanmck
translationtype: Human Translation
ms.sourcegitcommit: d7aa8568dd6fdd806d8ad70e408f108c722ec1ce
ms.openlocfilehash: b56d75d49e0b95025dd1a0bae532f677958eac8d


---
# <a name="service-fabric-programming-model-overview"></a>Panoramica dei modelli di programmazione di Service Fabric
Service Fabric offre diversi modi per scrivere e gestire i servizi. È possibile far sì che i servizi usino le API di Service Fabric per sfruttare appieno le funzionalità della piattaforma e il framework delle applicazioni oppure i servizi possono consistere semplicemente in qualsiasi programma eseguibile compilato scritto in un qualsiasi linguaggio e ospitato in un cluster di Service Fabric.

## <a name="guest-executable"></a>Eseguibile guest
Un eseguibile guest è un eseguibile arbitrario, scritto in un qualsiasi linguaggio, che consente di ospitare le applicazioni esistenti in un cluster di Service Fabric. Un eseguibile guest può essere compresso in un'applicazione e ospitato insieme con altri servizi. Service Fabric gestisce l'orchestrazione e la gestione dell'esecuzione dell'eseguibile, assicurando che rimanga operativo in base alla descrizione del servizio. Tuttavia, poiché gli eseguibili guest non si integrano direttamente con le API di Service Fabric, non usano il set completo di funzionalità offerte dalla piattaforma, ad esempio la personalizzazione dei report su integrità e carico, la registrazione degli endpoint di servizio e il calcolo con stato.

Introduzione agli eseguibili guest con distribuzione della prima [applicazione eseguibile guest](service-fabric-deploy-existing-app.md).

## <a name="reliable-services"></a>Reliable Services
Reliable Services è un framework leggero per la scrittura di servizi che si integrano con la piattaforma di Service Fabric sfruttandone il set completo di funzionalità. Reliable Services offre un set minimo di API che consentono al runtime di Service Fabric di gestire il ciclo di vita dei servizi e ai servizi di interagire con il runtime. Il framework dell'applicazione è minimo, consentendo così all'utente di mantenere il controllo completo di progettazione e implementazione, e può essere usato per ospitare altri framework di applicazioni, ad esempio MVC ASP.NET o API Web ASP.NET.

Reliable Services può essere senza stato, come la maggior parte delle piattaforme di servizio, ad esempio server Web o i ruoli di lavoro in Servizi Cloud di Azure, in cui tutte le istanze del servizio sono uguali e lo stato viene mantenuto in una soluzione esterna, ad esempio il database di Azure o l'archiviazione tabelle di Azure.

Reliable Services può essere anche con stato, esclusivamente Service Fabric, in cui lo stato viene mantenuto direttamente nel servizio stesso tramite Reliable Collections. Lo stato viene reso altamente disponibile tramite la replica e distribuito tramite partizionamento, il tutto gestito automaticamente da Service Fabric.

Vedere anche [altre informazioni su Reliable Services](service-fabric-reliable-services-introduction.md) oppure iniziare a [scrivere per la prima volta Reliable Services](service-fabric-reliable-services-quick-start.md).

## <a name="reliable-actors"></a>Reliable Actors
Realizzato a partire da Reliable Services, il framework Reliable Actor è un framework di applicazioni che implementa il criterio Actor virtuale, basato a sua volta sullo schema progettuale di Actor. Il framework Reliable Actor usa unità di calcolo e di stato indipendenti con esecuzione a thread singolo chiamate attori. Il framework Reliable Actors offre la comunicazione incorporata per gli attori e configurazioni di mantenimento dello stato e scalabilità preimpostate.

Dal momento che Reliable Actors è anch'esso un framework di applicazioni basato su Reliable Services, si integra completamente con la piattaforma di Service Fabric e ne sfrutta appieno il set completo di funzionalità.

## <a name="next-steps"></a>Passaggi successivi
Vedere anche [altre informazioni su Reliable Actors](service-fabric-reliable-actors-introduction.md) oppure a iniziare a [scrivere un servizio di tipo Reliable Actor](service-fabric-reliable-actors-get-started.md)
[Altre informazioni sull'inserimento in contenitori dei servizi in Windows o Linux](service-fabric-deploy-container.md)




<!--HONumber=Jan17_HO2-->


