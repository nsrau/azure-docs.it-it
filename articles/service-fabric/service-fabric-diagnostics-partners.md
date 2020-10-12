---
title: Partner di monitoraggio di Azure Service Fabric
description: Informazioni su come monitorare le applicazioni, i cluster e l'infrastruttura di Azure Service Fabric con soluzioni di monitoraggio dei partner.
author: srrengar
ms.topic: article
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: fd24d65ebdf4e458870819286024d1ea2e13d83e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "75645719"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Partner di monitoraggio di Azure Service Fabric

Questo articolo illustra come si possono monitorare applicazioni, cluster e infrastruttura di Service Fabric con alcune soluzioni dei partner. Abbiamo collaborato con ognuno dei partner riportato di seguito per creare offerte integrate per Service Fabric.

## <a name="dynatrace"></a>Dynatrace

La nostra Integrazione con Dynatrace offre molte funzionalità predefinite per monitorare i cluster di Service Fabric. L'installazione di Dynatrace OneAgent nelle istanze del set di scalabilità di macchine virtuali offre contatori delle prestazioni e una topologia della distribuzione di Service Fabric fino al livello di app. Dynatrace è anche un'ottima scelta per il monitoraggio locale. Vedere altre funzionalità elencate nell' [annuncio](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) e [istruzioni](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) per abilitare dynaTrace nel cluster. 

## <a name="datadog"></a>Datadog

Datadog include un'estensione per i set di scalabilità di macchine virtuali per le istanze di Windows e Linux. Con Datadog è possibile raccogliere i log eventi Windows e raccogliere così gli eventi della piattaforma Service Fabric in Windows. Per le istruzioni su come inviare dati di diagnostica di Datadog, leggere [qui](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

L'integrazione di Service Fabric con AppDynamics è a livello di applicazione. Aggiornando le variabili di ambiente e con l'App Dynamics NuGets, è possibile inviare i dati di telemetria dell'applicazione a AppDynamics. Fare riferimento a queste [istruzioni](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) per informazioni su come integrare le applicazioni di Service Fabric .NET con AppDynamics.

## <a name="new-relic"></a>New Relic

New Relic è un altro strumento di gestione delle prestazioni delle applicazioni che si integra opportunamente con le applicazioni di Service Fabric. È possibile installare i pacchetti NuGet di New Relic e aggiungere le variabili di ambiente specifiche nei file manifesto per inviare la telemetria dell'applicazione a New Relic. Leggere queste [istruzioni](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) per abilitare la telemetria di New Relic per le applicazioni di Service Fabric .NET.

## <a name="elk"></a>ELK 

Lo stack ELK è una raccolta di tecnologie open source: elasticsearch, logstash e Kibana. Utilizzando queste tecnologie in combinazione, è possibile raccogliere, archiviare e analizzare Service Fabric dati di monitoraggio e diagnostica. L'esercitazione su come eseguire questa operazione con applicazioni Java di Service Fabric native è disponibile [qui](service-fabric-tutorial-java-elk.md). 

## <a name="humio"></a>Humio

Humio è un servizio di raccolta log in grado di raccogliere log dalle applicazioni e dagli eventi da Service Fabric nel cloud o in locale in tempo reale. Oltre all'osservabilità in tempo reale, Humio offre funzionalità di visualizzazione e analisi all'avanguardia per la visualizzazione e la raccolta di informazioni dalla diagnostica. Humio offre piani tariffari convenienti ed è progettato per la scalabilità, mantenendo al tempo stesso una rapida velocità. Si integra direttamente con gli eventi della piattaforma Service Fabric e i dati di telemetria dell'applicazione. Per altre informazioni sull'integrazione di Humio e Service Fabric, vedere [qui](https://github.com/humio/service-fabric-humio).

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica del monitoraggio e della diagnostica](service-fabric-diagnostics-overview.md) in Service Fabric
* Informazioni su come [diagnosticare gli scenari comuni](service-fabric-diagnostics-common-scenarios.md) con i nostri strumenti proprietari
