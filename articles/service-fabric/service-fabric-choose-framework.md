<properties
   pageTitle="Panoramica dei modelli di programmazione di Service Fabric | Microsoft Azure"
   description="Infrastruttura dei servizi offre due framework per la creazione di servizi: il framework per gli attori e quello per i servizi. Offrono compromessi diversi nel controllo nella semplicità e nel controllo."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/18/2016"
   ms.author="seanmck"/>

# Panoramica dei modelli di programmazione di Service Fabric

Service Fabric offre diversi modi per scrivere e gestire i servizi. È possibile far sì che i servizi usino le API di Service Fabric per sfruttare appieno le funzionalità della piattaforma e il framework delle applicazioni oppure i servizi possono consistere semplicemente in qualsiasi programma eseguibile compilato scritto in un qualsiasi linguaggio e ospitato in un cluster di Service Fabric.

## Eseguibile guest

Un eseguibile guest è un eseguibile arbitrario, scritto in un qualsiasi linguaggio, che consente di ospitare le applicazioni esistenti in un cluster di Service Fabric. Un eseguibile guest può essere compresso in un'applicazione e ospitato insieme con altri servizi. Service Fabric gestisce l'orchestrazione e la gestione dell'esecuzione dell'eseguibile, assicurando che rimanga operativo in base alla descrizione del servizio. Tuttavia, poiché gli eseguibili guest non si integrano direttamente con le API di Service Fabric, non usano il set completo di funzionalità offerte dalla piattaforma, ad esempio la personalizzazione dei report su integrità e carico, la registrazione degli endpoint di servizio e il calcolo con stato.

Introduzione agli eseguibili guest con distribuzione della prima [applicazione eseguibile guest](service-fabric-deploy-existing-app.md).

## Reliable Services

Reliable Services è un framework leggero per la scrittura di servizi che si integrano con la piattaforma di Service Fabric sfruttandone il set completo di funzionalità. Reliable Services offre un set minimo di API che consentono al runtime di Service Fabric di gestire il ciclo di vita dei servizi e ai servizi di interagire con il runtime. Il framework dell'applicazione è minimo, consentendo così all'utente di mantenere il controllo completo di progettazione e implementazione, e può essere usato per ospitare altri framework di applicazioni, ad esempio MVC ASP.NET o API Web ASP.NET.

Reliable Services può essere senza stato, come la maggior parte delle piattaforme di servizio, ad esempio server Web o i ruoli di lavoro in Servizi Cloud di Azure, in cui tutte le istanze del servizio sono uguali e lo stato viene mantenuto in una soluzione esterna, ad esempio il database di Azure o l'archiviazione tabelle di Azure.

Reliable Services può essere anche con stato, esclusivamente Service Fabric, in cui lo stato viene mantenuto direttamente nel servizio stesso tramite Reliable Collections. Lo stato viene reso altamente disponibile tramite la replica e distribuito tramite partizionamento, il tutto gestito automaticamente da Service Fabric.

Vedere [Altre informazioni su Reliable Services](service-fabric-reliable-services-introduction) o iniziare a [scrivere per la prima volta Reliable Services](service-fabric-reliable-services-quick-start.md).

## Reliable Actors

Realizzato a partire da Reliable Services, il framework Reliable Actor è un framework di applicazioni che implementa il criterio Actor virtuale, basato a sua volta sullo schema progettuale di Actor. Il framework Reliable Actor usa unità di calcolo e di stato indipendenti con esecuzione a thread singolo chiamate attori. Il framework Reliable Actors offre la comunicazione incorporata per gli attori e configurazioni di mantenimento dello stato e scalabilità preimpostate.

Dal momento che Reliable Actors è anch'esso un framework di applicazioni basato su Reliable Services, si integra completamente con la piattaforma di Service Fabric e ne sfrutta appieno il set completo di funzionalità.

## Passaggi successivi
Vedere [Altre informazioni su Reliable Actors](service-fabric-reliable-actors-introduction.md) o iniziare a [scrivere per la prima volta un servizio Reliable Actors](service-fabric-reliable-actors-get-started.md).

<!---HONumber=AcomDC_0427_2016-->