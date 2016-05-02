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
   ms.date="04/14/2016"
   ms.author="SubramaR"/>

# Service Fabric in Linux

Service Fabric è attualmente disponibile in anteprima limitata su Linux, con possibilità di compilare, distribuire e gestire applicazioni a disponibilità e scalabilità elevata in tale ambiente proprio come in Windows. Inoltre, i framework Service Fabric di alto livello (Reliable Services e Reliable Actors) ora possono essere compilati in Java.

## Sistemi operativi e linguaggi di programmazione supportati

L'anteprima limitata supporta la creazione di cluster di sviluppo con un solo computer, nonché di cluster con più computer in Azure che eseguono Ubuntu Server 15.10.

È possibile compilare [servizi eseguibili guest](service-fabric-deploy-existing-app.md) con qualsiasi linguaggio o framework. Si può anche usare Java per creare servizi in base ai framework Reliable Services e Reliable Actors.

>[AZURE.NOTE] Le raccolte Reliable Collections non sono ancora supportate in Java.

## Partecipare all'anteprima

Se si è interessati a partecipare al programma di anteprima limitata, [compilare il questionario](http://aka.ms/sflinuxsurvey) per fornire indicazioni più dettagliate sullo scenario e sui propri requisiti. L'anteprima sarà inizialmente molto limitata e verrà gradualmente estesa.

Si noti che Service Fabric in Linux sarà concettualmente equivalente a quello disponibile in Windows, tranne le specifiche del sistema operativo e il supporto dei linguaggi di programmazione, quindi la maggior parte della [documentazione esistente](http://aka.ms/servicefabricdocs) resta valida e utile per acquisire familiarità con questa tecnologia.

## Passaggi successivi

Acquisire familiarità con i framework di programmazione [Reliable Actors](service-fabric-reliable-actors-introduction.md) e [Reliable Services](service-fabric-reliable-services-introduction.md).

<!---HONumber=AcomDC_0420_2016-->