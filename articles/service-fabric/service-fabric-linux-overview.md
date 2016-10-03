<properties
   pageTitle="Azure Service Fabric in Linux | Microsoft Azure"
   description="I cluster di Service Fabric supportano Linux e Java, per cui le applicazioni Service Fabric scritte in Java possono essere distribuite e ospitate in Linux."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>  

<tags
   ms.service="service-fabric"
   ms.devlang="Java"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="SubramaR"/>  

# Service Fabric in Linux

L'anteprima sarà disponibile a livello generale il 26 settembre come annunciato [in questo post di blog](https://azure.microsoft.com/blog/service-fabric-on-linux-support-available-this-month/). L'anteprima di Service Fabric su Linux, consente di compilare, distribuire e gestire applicazioni a disponibilità e scalabilità elevata in tale ambiente proprio come in Windows. Inoltre, i framework Service Fabric di alto livello (Reliable Services e Reliable Actors) sono ora disponibili in Java su Linux.

> [AZURE.VIDEO service-fabric-linux-preview]

## Sistemi operativi e linguaggi di programmazione supportati

L'anteprima limitata supporta la creazione di cluster di sviluppo con un solo computer, oltre ai cluster con più computer in Azure che eseguono Ubuntu Server 16.04.

È possibile compilare [servizi eseguibili guest](service-fabric-deploy-existing-app.md) con qualsiasi linguaggio o framework. Si può anche usare Java o C# per creare servizi in base ai framework Reliable Services e Reliable Actors, oltre a orchestrare contenitori Docker.

>[AZURE.NOTE] Le raccolte Reliable Collections non sono ancora supportate in Linux.

Concettualmente, Service Fabric in Linux è equivalente a ServiceFabric in Windows (tranne per le specifiche del sistema operativo e il supporto del linguaggio di programmazione). Di conseguenza, la maggior parte della [documentazione esistente](http://aka.ms/servicefabricdocs) è valida e consente di acquisire familiarità con la tecnologia.

## Passaggi successivi

Acquisire familiarità con i framework di programmazione [Reliable Actors](service-fabric-reliable-actors-introduction.md) e [Reliable Services](service-fabric-reliable-services-introduction.md).

<!---HONumber=AcomDC_0921_2016-->