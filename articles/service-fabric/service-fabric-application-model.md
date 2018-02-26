---
title: Modello di applicazione di Azure Service Fabric | Documentazione Microsoft
description: Come modellare e descrivere le applicazioni e servizi in infrastruttura di servizi.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: mani-ramaswamy
ms.assetid: 17a99380-5ed8-4ed9-b884-e9b827431b02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 12/07/2017
ms.author: ryanwi
ms.openlocfilehash: d5f6fbb9d9c0bc0d9762f8d6b4b4eb3b02d29adc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="model-an-application-in-service-fabric"></a>Modellare un'applicazione in Service Fabric
Questo articolo fornisce una panoramica del modello applicativo di Azure Service Fabric e come definire un'applicazione e un servizio attraverso file manifesto.

## <a name="understand-the-application-model"></a>Informazioni sul modello applicativo
Un'applicazione è una raccolta di servizi costituenti che eseguono determinate funzioni. Un servizio esegue una funzione completa e autonoma e può essere avviato ed eseguito in modo indipendente da altri servizi.  Un servizio è costituito da codice, configurazione e dati. Per ogni servizio, il codice è costituito dai file binari eseguibili, la configurazione è costituita dalle impostazioni del servizio che possono essere caricate in fase di esecuzione e i dati sono costituiti da dati statici arbitrari che devono essere usati dal servizio. Per ogni componente di questo modello applicativo gerarchico è possibile eseguire il controllo delle versioni e l'aggiornamento in modo indipendente.

![Modello di applicazione di Service Fabric][appmodel-diagram]

Un tipo di applicazione è una categorizzazione di un'applicazione e consiste in un'aggregazione di tipi di servizi. Un tipo di servizio è una categorizzazione di un servizio. La categorizzazione di un servizio può disporre di impostazioni e configurazioni diverse, ma la funzionalità di base resta la stessa. Le istanze di un servizio sono le diverse varianti di configurazione dello stesso tipo di servizio.  

Le classi (o "tipi") di applicazioni e servizi vengono descritte nei file XML (manifesti dell'applicazione e manifesti del servizio).  I manifesti descrivono applicazioni e servizi e sono i modelli in base ai quali è possibile creare istanze delle applicazioni dall'archivio immagini del cluster.  I manifesti sono descritti in dettaglio in [Manifesti dell'applicazione e del servizio](service-fabric-application-and-service-manifests.md). La definizione dello schema per i file ServiceManifest.xml e ApplicationManifest.xml viene installata con l'SDK e gli strumenti di Service Fabric in *C:\Programmi\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.xsd*. Lo schema XML è documentato in [Documentazione dello schema ServiceFabricServiceModel.xsd](service-fabric-service-model-schema.md).

Il codice di istanze di applicazioni diverse viene eseguito come processo separato anche se ospitato dallo stesso nodo di Service Fabric. Il ciclo di vita di ogni istanza dell'applicazione può inoltre essere gestito, ad esempio aggiornato, in modo indipendente. Il diagramma seguente illustra come i tipi di applicazioni siano costituiti da tipi di servizi, che a loro volta sono costituiti da codice, configurazione e pacchetti di dati. Per semplificare il diagramma, vengono visualizzati solo i pacchetti codice/configurazione/dati relativi a `ServiceType4`, anche se ogni tipo di servizio può includere tutti questi tipi di pacchetti o solo alcuni.

![Tipi di applicazioni di Service Fabric e tipi di servizio][cluster-imagestore-apptypes]

Nel cluster possono essere attive una o più istanze di un tipo di servizio. Le istanze o le repliche del servizio con stato ad esempio raggiungono un'elevata affidabilità replicando lo stato tra le repliche contenute in nodi diversi del cluster. Essenzialmente, la replica fornisce ridondanza in modo che il servizio sia disponibile anche se un nodo in un cluster ha un malfunzionamento. Un [servizio partizionato](service-fabric-concepts-partitioning.md) suddivide ulteriormente il proprio stato e i modelli di accesso a tale stato tra i nodi del cluster.

Il diagramma seguente illustra la relazione tra applicazioni e istanze di servizi, partizioni e repliche.

![Partizioni e repliche in un servizio][cluster-application-instances]

> [!TIP]
> È possibile visualizzare il layout delle applicazioni in un cluster usando lo strumento Service Fabric Explorer disponibile all'indirizzo http://&lt;yourclusteraddress&gt;:19080/Explorer. Per altre informazioni, vedere [Visualizzare il cluster con Service Fabric Explorer](service-fabric-visualizing-your-cluster.md).
> 
> 


## <a name="next-steps"></a>Passaggi successivi
- Informazioni sulla [scalabilità delle applicazioni](service-fabric-concepts-scalability.md).
- Informazioni sul servizio [stato](service-fabric-concepts-state.md), [partizionamento](service-fabric-concepts-partitioning.md) e [disponibilità](service-fabric-availability-services.md).
- Informazioni sulla definizione di applicazioni e servizi in [Manifesti dell'applicazione e del servizio](service-fabric-application-and-service-manifests.md).
- [Modelli di hosting dell'applicazione](service-fabric-hosting-model.md) descrive la relazione tra le repliche (o istanze) di un servizio distribuito e il processo host del servizio.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


