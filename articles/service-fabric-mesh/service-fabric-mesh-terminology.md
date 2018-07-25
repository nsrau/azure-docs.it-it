---
title: Terminologia di Azure Service Fabric Mesh | Microsoft Docs
description: Informazioni sui termini comuni relativi a Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: ''
author: rwike77
ms.author: ryanwi
ms.date: 07/12/2018
ms.topic: conceptual
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: 7c3ce5571c54d6c613114ea49999e450934c8ff4
ms.sourcegitcommit: dc646da9fbefcc06c0e11c6a358724b42abb1438
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39136199"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologia di Service Fabric Mesh

Azure Service Fabric Mesh è un servizio completamente gestito che consente agli sviluppatori di distribuire applicazioni di microservizi senza dover gestire macchine virtuali, archiviazione o connettività di rete. Questo articolo illustra in modo dettagliato la terminologia usata per Azure Service Fabric Mesh al fine di agevolare la comprensione dei termini usati nella documentazione.

## <a name="service-fabric"></a>Service Fabric

Service Fabric è una piattaforma open source di sistemi distribuiti che semplifica la creazione di pacchetti, la distribuzione e la gestione di microservizi scalabili e affidabili. Service Fabric è l'agente di orchestrazione che consente il funzionamento di Service Fabric Mesh. Service Fabric offre varie opzioni per creare ed eseguire applicazioni di microservizi. È possibile usare qualsiasi framework per scrivere i servizi e scegliere l'ambiente in cui eseguire l'applicazione tra varie opzioni disponibili.

## <a name="deployment-and-application-models"></a>Modelli di distribuzione e modelli applicativi 

Per distribuire i servizi, è necessario descrivere come devono essere eseguiti. Service Fabric supporta tre diversi modelli di distribuzione:

### <a name="resource-model"></a>Modello di risorse
Le risorse includono tutto ciò che può essere distribuito singolarmente in Service Fabric, tra cui applicazioni, servizi, reti e volumi. Le risorse vengono definite in un file YAML o JSON in base allo schema del modello di risorse di Azure.

Il modello di risorse offre il modo più semplice per descrivere le applicazioni di Service Fabric. Questo modello si basa principalmente sulla distribuzione e la gestione semplificate dei servizi in contenitori.

Le risorse possono essere distribuite ovunque venga eseguito Service Fabric.

### <a name="native-model"></a>Modello nativo
Il modello applicativo nativo offre alle applicazioni l'accesso completo a basso livello a Service Fabric. Le applicazioni e i servizi vengono definiti come tipi registrati nei file manifesto XML.

Il modello nativo supporta il framework Reliable Services, che fornisce l'accesso alle API di runtime di Service Fabric e alle API di gestione dei cluster in C# e Java. Il modello nativo supporta anche file eseguibili e contenitori arbitrari.

Il modello nativo non è supportato nell'ambiente di Service Fabric Mesh.

### <a name="docker-compose"></a>Modello di Docker Compose 
[Docker Compose](https://docs.docker.com/compose/) fa parte del progetto Docker. Service Fabric offre supporto limitato per la distribuzione di applicazioni tramite il modello di Docker Compose.

## <a name="environments"></a>Ambienti

Service Fabric è una tecnologia di piattaforma open source su cui sono basati diversi prodotti e servizi. Microsoft offre le opzioni seguenti:

 - **Service Fabric Mesh**: un servizio completamente gestito per l'esecuzione di applicazioni di Service Fabric in Microsoft Azure.
 - **Azure Service Fabric**: la piattaforma per cluster di Service Fabric ospitati in Azure. Consente l'integrazione tra Service Fabric e l'infrastruttura di Azure e offre funzionalità per la gestione degli aggiornamenti e della configurazione dei cluster di Service Fabric.
 - **Pacchetto autonomo di Service Fabric**: un set di strumenti di installazione e configurazione per [distribuire ovunque i cluster di Service Fabric](/azure/service-fabric/service-fabric-deploy-anywhere) (in locale o in qualsiasi provider di servizi cloud). Non è gestito da Azure.
 - **Cluster di sviluppo di Service Fabric**: offre un'esperienza di sviluppo locale in Windows, Linux o Mac per lo sviluppo di applicazioni di Service Fabric.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Matrice di supporto per ambienti, framework e modelli di distribuzione
I diversi ambienti offrono vari livello di supporto per framework e modelli di distribuzione. La tabella seguente descrive le combinazioni supportate di framework e modelli di distribuzione.

| Tipo di applicazione | Descritto da | Azure Service Fabric Mesh | Cluster dei servizi di Azure (qualsiasi sistema operativo)| Cluster locale: Windows | Cluster locale: Linux | Cluster locale: Mac | Cluster autonomo (Windows)
|---|---|---|---|---|---|---|---|---|---|
| Applicazioni Service Fabric Mesh | Modello di risorse (YAML e JSON) | Supportato |Non supportato | Supportato |Non supportato | Non supportato | Non supportato |
|Applicazioni native di Service Fabric | Modello applicativo nativo (XML) | Non supportato| Supportato|Supportato|Supportato|Supportato|Supportato|

La tabella seguente descrive i diversi modelli applicativi e gli strumenti disponibili per tali modelli rispetto a Service Fabric.

| Tipo di applicazione | Descritto da | Visual Studio 2017 | Visual Studio 2015 | Eclipse | Visual Studio Code | SFCTL | AZ CLI | PowerShell
|---|---|---|---|---|---|---|---|---|---|
| Applicazioni Service Fabric Mesh | Modello di risorse (YAML e JSON) | Supportato |Non supportato |Non supportato |Non supportato |Non supportato | Supportato, solo ambiente Mesh | Non supportato
|Applicazioni native di Service Fabric | Modello applicativo nativo (XML) | Supportato| Supportato|Supportato|Supportato|Supportato|Supportato|Supportato|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Service Fabric Mesh, vedere la panoramica:
- [Panoramica di Service Fabric Mesh](service-fabric-mesh-overview.md)
