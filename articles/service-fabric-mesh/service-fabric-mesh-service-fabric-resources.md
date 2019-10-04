---
title: Introduzione al modello di risorsa di Azure Service Fabric | Microsoft Docs
description: Informazioni sul modello di risorsa di Service Fabric, un approccio semplificato per definire le applicazioni mesh Service Fabric.
services: service-fabric-mesh
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 3cee0ada75c4ea265c7e9c598408eb6b01477d6c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60810701"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Introduzione al modello di risorsa di Service Fabric

Il modello di risorsa Service Fabric descrive un approccio semplice per definire le risorse che costituiscono un'applicazione mesh Service Fabric. Le singole risorse possono essere distribuite in qualsiasi ambiente Service Fabric.  Il modello di risorse Service Fabric è anche compatibile con il modello Azure Resource Manager. In questo modello sono attualmente supportati i tipi di risorse seguenti:

- Applicazioni e servizi
- Reti
- Gateway
- Segreti e segreti/valori
- Volumi

Ogni risorsa è descritta in modo dichiarativo in un file di risorse, ovvero un semplice documento YAML o JSON che descrive l'applicazione mesh e di cui viene eseguito il provisioning da parte della piattaforma Service Fabric.

## <a name="applications-and-services"></a>Applicazioni e servizi

Una risorsa applicazione è l'unità di distribuzione, di controllo delle versioni e della durata di un'applicazione mesh. È composta da una o più risorse del servizio che rappresentano un microservizio. Ogni risorsa del servizio, a sua volta, è composta da uno o più pacchetti di codice che descrivono tutti gli elementi necessari per eseguire l'immagine del contenitore associata al pacchetto di codice.

![App e servizi][Image1]

Una risorsa del servizio dichiara quanto segue:

- Nome, versione e registro del contenitore
- Risorse di CPU e memoria necessarie per ogni contenitore
- Endpoint di rete
- Riferimenti ad altre risorse, come esempio reti, volumi e segreti 

Tutti i pacchetti di codice definiti come parte di una risorsa del servizio sono distribuiti e attivati insieme come gruppo. La risorsa del servizio descrive anche il numero di istanze del servizio da eseguire e fa inoltre riferimento ad altre risorse (ad esempio, risorse di rete) da cui dipende.

Se un'applicazione mesh è costituita da più di un servizio, non viene garantita l'esecuzione di tutti i servizi sullo stesso nodo. Inoltre, durante un aggiornamento dell'applicazione, l'errore di aggiornamento di un singolo servizio comporterà l'esecuzione del rollback di tutti i servizi alla versione precedente.

Come accennato in precedenza, il ciclo di vita di ogni istanza dell'applicazione può essere gestito in modo indipendente. Ad esempio, un'istanza dell'applicazione può essere aggiornata indipendentemente dalle altre istanze dell'applicazione. In genere, è consigliabile conservare un numero relativamente ridotto di servizi in un'applicazione poiché più servizi si aggiungono, più difficile diventa la gestione indipendente di ogni singolo servizio.

## <a name="networks"></a>Reti

La risorsa di rete è distribuibile singolarmente ed è indipendente da una risorsa di un'applicazione o di un servizio che può fare riferimento a essa come dipendenza. Viene usata per creare una rete per le applicazioni. Più servizi da diverse applicazioni possono far parte della stessa rete.  Per altre informazioni, leggere [networking nelle applicazioni Service Fabric Mesh](service-fabric-mesh-networks-and-gateways.md).

> [!NOTE]
> L'anteprima corrente supporta solo un mapping uno-a-uno tra le applicazioni e le reti

![Rete e gateway][Image2]

## <a name="gateways"></a>Gateway
Una risorsa gateway collega due reti e instrada il traffico.  Un gateway consente ai servizi di comunicare con i client esterni e fornisce un ingresso nel/i servizio/i.  Un gateway può essere usato anche per collegare l'applicazione Mesh con la propria rete virtuale esistente. Per altre informazioni, leggere [networking nelle applicazioni Service Fabric Mesh](service-fabric-mesh-networks-and-gateways.md).

![Rete e gateway][Image2]

## <a name="secrets"></a>Segreti

Le risorse segrete sono distribuibili indipendentemente da un'applicazione o risorsa di servizio che può riferirsi a essa come loro dipendenza. Viene usata per fornire in modo sicuro i segreti alle applicazioni. Più servizi di applicazioni diverse possono fare riferimento ai valori dello stesso segreto.

## <a name="volumes"></a>Volumi

I contenitori spesso rendono disponibili i dischi temporanei. I dischi sono temporanei, quindi si riceve un nuovo disco temporaneo e si perdono le informazioni in caso di arresto anomalo di un contenitore. È anche difficile condividere le informazioni sui dischi temporanei con altri contenitori. I volumi sono le directory che vengono montate all'interno di istanze del contenitore che è possibile usare per mantenere la persistenza dello stato. I volumi offrono una risorsa di archiviazione file per utilizzo generico e consentono di leggere/scrivere i file usando le normali API di file I/O del disco. La risorsa di volume è una modalità dichiarativa per descrivere come viene montata una directory e la risorsa di archiviazione di backup per essa (Volume di file di Azure o Reliable Volume di Service Fabric).  Per altre informazioni, leggere [stato di archiviazione](service-fabric-mesh-storing-state.md#volumes).

![Volumi][Image3]

## <a name="programming-models"></a>Modelli di programmazione
La risorsa del servizio richiede solo l'esecuzione di un'immagine del contenitore a cui viene fatto riferimento nei pacchetti di codice associati alla risorsa. È possibile eseguire qualsiasi codice, in qualsiasi linguaggio, usando qualsiasi framework all'interno del contenitore senza necessità di conoscere o usare le API specifiche di mesh Service Fabric. 

Il codice dell'applicazione rimane portabile anche all'esterno di mesh Service Fabric e le distribuzioni delle applicazioni rimangano coerenti indipendentemente dal linguaggio o dal framework usati per implementare i servizi. Se l'applicazione è ASP.NET Core, Go o semplicemente un set di processi e script, il modello di distribuzione delle risorse di mesh Service Fabric resta invariato. 

## <a name="packaging-and-deployment"></a>Creazione di pacchetti e distribuzione

Con le applicazioni mesh Service Fabric basate sul modello di risorsa vengono creati pacchetti come contenitori Docker.  Mesh Service Fabric è un ambiente condiviso multi-tenant e i contenitori offrono un elevato livello di isolamento.  Queste applicazioni vengono descritte usando un formato JSON o un formato YAML (che viene quindi convertito in JSON). Quando si distribuisce un'applicazione Mesh in Azure Service Fabric Mesh, il codice JSON usato per descrivere l'applicazione è un modello Azure Resource Manager. Viene eseguito il mapping delle risorse sulle risorse di Azure.  Quando si distribuisce un'applicazione Mesh in un cluster di Service Fabric (autonomo o ospitate in Azure), il codice JSON usato per descrivere l'applicazione è un formato simile a un modello Azure Resource Manager.  Una volta distribuite, le applicazioni mesh possono essere gestite tramite le interfacce HTTP o l'interfaccia della riga di comando di Azure. 


## <a name="next-steps"></a>Passaggi successivi 
Per altre informazioni su mesh Service Fabric leggere la panoramica:
- [Panoramica di mesh Service Fabric](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
