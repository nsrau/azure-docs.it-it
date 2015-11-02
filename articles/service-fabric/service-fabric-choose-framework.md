<properties
   pageTitle="Modelli di programmazione dell’infrastruttura dei servizi | Microsoft Azure"
   description="Infrastruttura dei servizi offre due framework per la creazione di servizi: il framework per gli attori e quello per i servizi. Offrono compromessi diversi nel controllo nella semplicità e nel controllo."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="coreysa"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/15/2015"
   ms.author="seanmck"/>

# Scegliere un framework per un servizio

Service Fabric offre due framework di alto livello per la creazione di servizi: le API Reliable Actors e le API Reliable Services. Entrambi si basano sugli stessi componenti principali di Service Fabric, ma implicano compromessi diversi tra semplicità e flessibilità in termini di concorrenza, partizionamento e comunicazione. Per scegliere il framework più appropriato per un determinato servizio di un'applicazione, è utile comprendere tutti e due i modelli.

## Confronto tra le API Reliable Actors e le API Reliable Services

|**API Reliable Actors**|**API Reliable Services**|
|-----------------------|--------------------------|
|Lo spazio del problema include molte piccole unità indipendenti di stato e logica|È necessario mantenere la logica tra più componenti|
|Si intende usare oggetti a thread singolo continuando a garantire scalabilità e coerenza|Si intende usare raccolte affidabili (come dizionari e raccolte .NET) per archiviare e gestire lo stato|
|Si vuole che il framework gestisca la concorrenza e la granularità dello stato|Si intende controllare la granularità e la concorrenza dello stato|
|Si vuole che la piattaforma gestisca automaticamente le comunicazioni|Si intende gestire le comunicazioni e controllare lo schema di partizionamento del servizio|

Tenere presente che è perfettamente plausibile usare framework diversi per servizi diversi in un'app. È possibile ad esempio disporre di un servizio con stato che aggrega i dati generati da numerosi attori.

## Passaggi successivi

- [Altre informazioni sulle API Reliable Actors](service-fabric-reliable-actors-introduction.md)
- [Altre informazioni sulle API Reliable Services](../Service-Fabric/service-fabric-reliable-services-introduction.md)

<!---HONumber=Oct15_HO4-->