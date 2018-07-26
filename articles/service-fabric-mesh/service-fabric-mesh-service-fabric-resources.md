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
ms.date: 07/12/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 6a96995a46cd2ad0a1afe4f4afe49d62409f2b68
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075288"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Introduzione al modello di risorsa di Service Fabric

Il modello di risorsa di Service Fabric descrive un approccio semplice per definire le risorse che costituiscono un'applicazione mesh Service Fabric. In questo modello sono attualmente supportati i tipi di risorse seguenti:

- APPLICAZIONI
- Services
- Volumi
- Reti

Ogni risorsa è descritta in modo dichiarativo in un file di risorse, ovvero un semplice documento YAML o JSON che descrive l'applicazione mesh e di cui viene eseguito il provisioning da parte della piattaforma Service Fabric.

## <a name="resource-overview"></a>Panoramica della risorsa

### <a name="applications-and-services"></a>Applicazioni e servizi

Una risorsa applicazione è l'unità di distribuzione, di controllo delle versioni e della durata di un'applicazione mesh. È composta da una o più risorse del servizio che rappresentano un microservizio. Ogni risorsa del servizio, a sua volta, è composta da uno o più pacchetti di codice che descrivono tutti gli elementi necessari per eseguire l'immagine del contenitore associato al pacchetto di codice, inclusi gli elementi seguenti:

- Nome, versione e registro del contenitore
- Risorse di CPU e memoria necessarie per ogni contenitore
- Endpoint di rete
- Volumi da montare nel contenitore che fanno riferimento a una risorsa di volume separata.

Tutti i pacchetti di codice definiti come parte di una risorsa del servizio sono distribuiti e attivati insieme come gruppo. La risorsa del servizio descrive anche il numero di istanze del servizio da eseguire e fa inoltre riferimento ad altre risorse (ad esempio, risorse di rete) da cui dipende.

Se un'applicazione mesh è costituita da più di un servizio, non viene garantita l'esecuzione di tutti i servizi sullo stesso nodo. Inoltre, durante un aggiornamento dell'applicazione, l'errore di aggiornamento di un singolo servizio comporterà l'esecuzione del rollback di tutti i servizi alla versione precedente.



```yaml
    services:
      - name: helloWorldService
        properties:
          description: Hello world service.
          osType: linux
          codePackages:
            - name: helloworld
              image: myapp:1.0-alpine
              resources:
                requests:
                  cpu: 2
                  memoryInGB: 2
              endpoints:
                - name: helloWorldEndpoint
                  port: 8080
    replicaCount: 3
    networkRefs:
      - name: mynetwork
```

Come accennato in precedenza, il ciclo di vita di ogni istanza dell'applicazione può essere gestito in modo indipendente. Ad esempio, un'istanza dell'applicazione può essere aggiornata indipendentemente dalle altre istanze dell'applicazione. In genere, è consigliabile conservare un numero relativamente ridotto di servizi in un'applicazione poiché più servizi si aggiungono, più difficile diventa la gestione indipendente di ogni singolo servizio.

### <a name="networks"></a>Reti

La risorsa di rete è distribuibile singolarmente ed è indipendente da una risorsa di un'applicazione o di un servizio che può fare riferimento a essa come dipendenza. Viene usata per creare una rete privata per le applicazioni. Più servizi da diverse applicazioni possono far parte della stessa rete.

> [!NOTE]
> L'anteprima corrente supporta solo un mapping uno-a-uno tra le applicazioni e le reti

### <a name="volumes"></a>Volumi

I volumi sono le directory che vengono montate all'interno di istanze del contenitore che è possibile usare per mantenere la persistenza dello stato. La risorsa di volume è una modalità dichiarativa per descrivere come viene montata una directory e l'archivio di backup per essa.

## <a name="programming-models"></a>Modelli di programmazione
La risorsa del servizio richiede solo l'esecuzione di un'immagine del contenitore a cui viene fatto riferimento nei pacchetti di codice associati alla risorsa. È possibile eseguire qualsiasi codice, in qualsiasi linguaggio, usando qualsiasi framework all'interno del contenitore senza necessità di conoscere o usare le API specifiche di mesh Service Fabric. 

Il codice dell'applicazione rimane portabile anche all'esterno di mesh Service Fabric e le distribuzioni delle applicazioni rimangano coerenti indipendentemente dal linguaggio o dal framework usati per implementare i servizi. Se l'applicazione è ASP.NET Core, Go o semplicemente un set di processi e script, il modello di distribuzione delle risorse di mesh Service Fabric resta invariato. 

## <a name="deployment"></a>Distribuzione

Durante la distribuzione di mesh Service Fabric, le risorse vengono distribuite in Azure come modelli di Azure Resource Manager tramite il protocollo HTTP o l'interfaccia della riga di comando di Azure. 


## <a name="next-steps"></a>Passaggi successivi 
Per altre informazioni su mesh Service Fabric leggere la panoramica:
- [Panoramica di mesh Service Fabric](service-fabric-mesh-overview.md)
