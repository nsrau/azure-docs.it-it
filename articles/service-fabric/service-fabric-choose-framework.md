<properties
   pageTitle="Modelli di programmazione dell’infrastruttura dei servizi | Microsoft Azure"
   description="Infrastruttura dei servizi offre due framework per la creazione di servizi: il framework per gli attori e quello per i servizi. Si offrono compromessi diversi in semplicità e controllo."
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/26/2016"
   ms.author="seanmck"/>

# Scegliere un framework per un servizio

Azure Service Fabric offre due framework di alto livello per la creazione di servizi: l'API Reliable Services e l'API Reliable Actors. Entrambi si basano sugli stessi componenti principali di Service Fabric, ma implicano compromessi diversi tra semplicità e flessibilità in termini di concorrenza, partizionamento e comunicazione. Per scegliere il framework più appropriato per un determinato servizio di un'applicazione, è utile comprendere tutti e due i modelli.

## Confronto tra le API Reliable Actors e le API Reliable Services

|**Quando scegliere le API Reliable Actors**|**Quando usare le API Reliable Services**|
|-----------------------|--------------------------|
|Lo spazio del problema include un ingente numero (oltre 1000) di piccole unità indipendenti di stato e logica.|È necessario mantenere la logica tra più componenti.|
|Si intende usare oggetti a thread singolo che non richiedono una significativa interazione esterna.|Si intende usare Reliable Collections (come ReliableDictionary e ReliableQueue .NET) per archiviare e gestire lo stato.|
|Si vuole che la piattaforma gestisca automaticamente le comunicazioni.|Si intende gestire le comunicazioni e controllare lo schema di partizionamento del servizio.|

Tenere presente che è perfettamente plausibile usare framework diversi per servizi diversi in un'app. È possibile ad esempio disporre di un servizio con stato che aggrega i dati generati da un numero di attori.

## Passaggi successivi

- [Altre informazioni sulle API Reliable Actors](service-fabric-reliable-actors-introduction.md)
- [Altre informazioni sulle API Reliable Services](../Service-Fabric/service-fabric-reliable-services-introduction.md)

<!---HONumber=AcomDC_0128_2016-->