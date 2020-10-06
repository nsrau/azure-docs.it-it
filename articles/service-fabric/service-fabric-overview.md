---
title: Panoramica di Azure Service Fabric
description: Service Fabric è una piattaforma di sistemi distribuiti per la creazione di microservizi scalabili, affidabili e facilmente gestibili.
ms.topic: overview
ms.date: 09/22/2020
ms.custom: contentperfq1
ms.openlocfilehash: 3c282178decc1a07b2c0acc102b279688c42d52e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91300650"
---
# <a name="overview-of-azure-service-fabric"></a>Panoramica di Azure Service Fabric

Azure Service Fabric è una [piattaforma di sistemi distribuiti](#container-orchestration) che semplifica la disposizione in pacchetti, la distribuzione e la gestione di microservizi e contenitori scalabili e affidabili. Service Fabric fa fronte anche alle principali problematiche correlate allo [sviluppo e alla gestione](#application-lifecycle-management) di applicazioni cloud native.

Un fattore essenziale per la differenziazione di Service Fabric è la creazione di servizi con stato. È possibile usare il [modello di programmazione](#stateless-and-stateful-microservices) di Service Fabric o eseguire i servizi con stato in contenitori scritti in qualsiasi linguaggio o codice. È possibile creare [cluster Service Fabric ovunque](#any-os-any-cloud), tra cui Windows Server e Linux in locale e in altri cloud pubblici, oltre che in Azure.

![La piattaforma Service Fabric offre gestione del ciclo di vita, disponibilità, orchestrazione, modelli di programmazione, integrità e monitoraggio, strumenti di sviluppo e operativi e scalabilità automatica: in Azure, in locale, in altri cloud e nel computer di sviluppo][Image1]

Service Fabric viene attualmente usato in numerosi servizi Microsoft, tra cui database SQL di Azure, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Hub eventi di Azure, Hub IoT di Azure, Dynamics 365, Skype for Business e molti servizi di base di Azure.

## <a name="container-orchestration"></a>Orchestrazione dei contenitori

Service Fabric è l'[agente di orchestrazione dei contenitori](service-fabric-cluster-resource-manager-introduction.md) di Microsoft per la distribuzione e la gestione di microservizi in un cluster di computer, traendo vantaggio dalle lezioni apprese per l'esecuzione di servizi Microsoft su vasta scala. Service Fabric può distribuire applicazioni in pochi secondi e a densità elevata, con centinaia o migliaia di applicazioni o contenitori per ogni computer. Con Service Fabric è possibile combinare sia i servizi in processi che i servizi in contenitori nella stessa applicazione.

Vedere [questo articolo](service-fabric-content-roadmap.md) per altre informazioni sui concetti di base, i modelli di programmazione, il ciclo di vita dell'applicazione, i test, i cluster e il monitoraggio dell'integrità di Service Fabric.

## <a name="stateless-and-stateful-microservices"></a>Microservizi con e senza stato

Service Fabric offre un runtime sofisticato che supporta microservizi con e senza stato. Una differenza essenziale rispetto a Service Fabric è data dal supporto affidabile per la creazione di servizi con stato, tramite i [modelli di programmazione incorporati](service-fabric-choose-framework.md) di Service Fabric o i servizi con stato in contenitori.

Vedere [questo articolo](service-fabric-application-scenarios.md) per altre informazioni sugli scenari di applicazione che traggono vantaggio dai servizi Service Fabric con stato.

## <a name="application-lifecycle-management"></a>Gestione del ciclo di vita delle applicazioni

Service Fabric offre un supporto per l'intero ciclo di vita e i processi CI/CD delle applicazioni cloud, inclusi i contenitori: dallo sviluppo alla distribuzione, al monitoraggio giornaliero, alla gestione, alla manutenzione e infine alla rimozione delle autorizzazioni. Service Fabric è integrato con gli strumenti di integrazione continua e distribuzione continua, ad esempio [Azure Pipelines](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html) e [Octopus Deploy](https://octopus.com/), e può essere usato con qualsiasi altro strumento di integrazione continua e distribuzione continua.

Per altre informazioni sulla gestione del ciclo di vita delle applicazioni, vedere l'articolo [Ciclo di vita dell'applicazione](service-fabric-application-lifecycle.md). Per la distribuzione di applicazioni esistenti in Service Fabric, vedere [Distribuire un eseguibile guest](service-fabric-deploy-existing-app.md).

## <a name="any-os-any-cloud"></a>Qualsiasi sistema operativo, qualsiasi ambiente cloud

È possibile creare cluster di Service Fabric in molti ambienti, tra cui [Azure o in locale](service-fabric-deploy-anywhere.md), [in Windows Server o su Linux](service-fabric-linux-windows-differences.md), nonché in altri cloud pubblici. L'ambiente di sviluppo in Service Fabric SDK è identico all'ambiente di produzione, senza alcun emulatore. In altre parole, i componenti in esecuzione nel cluster di sviluppo locale vengono distribuiti anche nei cluster presenti in altri ambienti.

Per lo [sviluppo in Windows](service-fabric-get-started.md), Service Fabric .NET SDK è integrato con Visual Studio e PowerShell. Per lo [sviluppo in Linux](service-fabric-get-started-linux.md), Service Fabric Java SDK è integrato con Eclipse e viene usato Yeoman per generare modelli per Java, .NET Core e applicazioni contenitore.

## <a name="compliance"></a>Conformità

Il provider di risorse di Azure Service Fabric è disponibile in tutte le aree di Azure ed è conforme a tutti i certificati di conformità di Azure, tra cui SOC, ISO, PCI DSS, HIPAA e GDPR. Per un elenco completo, vedere [Offerte di conformità Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings).

## <a name="next-steps"></a>Passaggi successivi

Creare e distribuire la prima applicazione in Azure Service Fabric:

> [!div class="nextstepaction"]
> [Avvio rapido di Service Fabric][sf-quickstart]

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
[sf-quickstart]: ./service-fabric-quickstart-dotnet.md
