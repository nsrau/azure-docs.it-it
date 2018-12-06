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
ms.openlocfilehash: f72490169149d247a43e2760815d757c3606f1b3
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2018
ms.locfileid: "52163725"
---
# <a name="service-fabric-mesh-terminology"></a>Terminologia di Service Fabric Mesh

Azure Service Fabric Mesh è un servizio completamente gestito che consente agli sviluppatori di distribuire applicazioni di microservizi senza dover gestire macchine virtuali, archiviazione o connettività di rete. Questo articolo illustra in modo dettagliato la terminologia usata per Azure Service Fabric Mesh al fine di agevolare la comprensione dei termini usati nella documentazione.

## <a name="service-fabric"></a>Service Fabric

[Service Fabric](/azure/service-fabric/) è una piattaforma open source di sistemi distribuiti che semplifica la creazione di pacchetti, la distribuzione e la gestione di microservizi scalabili e affidabili. Service Fabric è l'agente di orchestrazione che consente il funzionamento di Service Fabric Mesh. Service Fabric offre varie opzioni per creare ed eseguire applicazioni di microservizi. È possibile usare qualsiasi framework per scrivere i servizi e scegliere l'ambiente in cui eseguire l'applicazione tra varie opzioni disponibili.

## <a name="application-and-service-concepts"></a>Concetti di applicazione e servizio

**Applicazione Service Fabric Mesh**: le applicazioni Service Fabric Mesh sono descritte dal [modello di risorse](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources) (file di risorse YAML e JSON) e possono essere distribuite in qualsiasi ambiente in cui viene eseguito Service Fabric.

**Applicazione nativa Service Fabric**: le applicazioni native Service Fabric sono descritte dal [modello applicativo nativo](/azure/service-fabric/service-fabric-application-model) (manifesti delle applicazioni e dei servizi basati su XML).  Non è possibile eseguire applicazioni native Service Fabric in Service Fabric Mesh.

**Applicazione**: un'applicazione Service Fabric Mesh è l'unità di distribuzione, controllo delle versioni e durata di un'applicazione Mesh. Il ciclo di vita di ogni istanza dell'applicazione può essere gestito in modo indipendente.  Le applicazioni sono composte da uno o più pacchetti di codice del servizio e dalle impostazioni. Un'applicazione viene definita in base allo schema del modello di risorse (RM) di Azure.  I servizi sono descritti come proprietà della risorsa applicazione in un modello RM.  L'applicazione fa riferimento alle reti e ai volumi che usa.  Quando si crea un'applicazione, l'applicazione, i servizi, la rete e i volumi vengono modellati in base al modello di risorsa Service Fabric.

**Servizio**: un servizio in un'applicazione rappresenta un microservizio ed esegue una funzione completa e autonoma. Ogni servizio è composto da uno o più pacchetti di codice che descrivono tutti gli elementi necessari per eseguire l'immagine del contenitore associata al pacchetto di codice.  Il numero di repliche di servizi in un'applicazione può essere aumentato o ridotto.

**Pacchetto di codice**: i pacchetti di codice descrivono tutti gli elementi necessari per eseguire l'immagine del contenitore associata al pacchetto di codice, tra cui i seguenti:

* Nome, versione e registro del contenitore
* Risorse di CPU e memoria necessarie per ogni contenitore
* Endpoint di rete
* Volumi da montare nel contenitore che fanno riferimento a una risorsa di volume separata.

## <a name="deployment-and-application-models"></a>Modelli di distribuzione e modelli applicativi 

Per distribuire i servizi, è necessario descrivere come devono essere eseguiti. Service Fabric supporta tre diversi modelli di distribuzione:

### <a name="resource-model"></a>Modello di risorsa
Le risorse Service Fabric includono tutto ciò che può essere distribuito singolarmente in Service Fabric, ad esempio applicazioni, servizi, reti e volumi. Le risorse vengono definite tramite un file JSON che può essere distribuito a un endpoint del cluster.  Per Service Fabric Mesh, viene usato lo schema del modello di risorse di Azure. Per creare più facilmente i file di definizione è anche possibile usare uno schema di file YAML. Le risorse possono essere distribuite ovunque venga eseguito Service Fabric. Il modello di risorse offre il modo più semplice per descrivere le applicazioni di Service Fabric. Questo modello si basa principalmente sulla distribuzione e la gestione semplificate dei servizi in contenitori. Per altre informazioni, vedere [Introduzione al modello di risorsa di Service Fabric](/azure/service-fabric-mesh/service-fabric-mesh-service-fabric-resources).

### <a name="native-model"></a>Modello nativo
Il modello applicativo nativo offre alle applicazioni l'accesso completo a basso livello a Service Fabric. Le applicazioni e i servizi vengono definiti come tipi registrati nei file manifesto XML.

Il modello nativo supporta il framework Reliable Services, che fornisce l'accesso alle API di runtime di Service Fabric e alle API di gestione dei cluster in C# e Java. Il modello nativo supporta anche file eseguibili e contenitori arbitrari.

Il modello nativo non è supportato nell'ambiente di Service Fabric Mesh.  Per altre informazioni, vedere la [panoramica dei modelli di programmazione](/azure/service-fabric/service-fabric-choose-framework).

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

| Tipo di applicazione | Descritto da | Azure Service Fabric Mesh | Cluster di Azure Service Fabric (qualsiasi sistema operativo)| Cluster locale | Cluster autonomo |
|---|---|---|---|---|---|---|---|---|---|
| Applicazioni Service Fabric Mesh | Modello di risorse (YAML e JSON) | Supportato |Non supportate | Windows: supportato; Linux e Mac: non supportati | Windows: non supportato |
|Applicazioni native di Service Fabric | Modello applicativo nativo (XML) | Non supportato| Supportato|Supportato|Windows: supportato|

La tabella seguente descrive i diversi modelli applicativi e gli strumenti disponibili per tali modelli rispetto a Service Fabric.

| Tipo di applicazione | Descritto da | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|---|---|---|
| Applicazioni Service Fabric Mesh | Modello di risorse (YAML e JSON) | VS 2017 |Non supportate |Non supportate | Supportato, solo ambiente Mesh | Non supportato|
|Applicazioni native di Service Fabric | Modello applicativo nativo (XML) | VS 2017 e VS 2015| Supportato|Supportato|Supportato|Supportato|

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su mesh Service Fabric, leggere la [panoramica di mesh Service Fabric](service-fabric-mesh-overview.md).

Risposte a [domande comuni](service-fabric-mesh-faq.md).
