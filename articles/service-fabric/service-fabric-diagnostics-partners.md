---
title: Partner di monitoraggio di Azure Service Fabric | Microsoft Docs
description: Informazioni su come eseguire il monitoraggio di Azure Service Fabric con le soluzioni di monitoraggio dei partner
services: service-fabric
documentationcenter: .net
author: srrengar
manager: chackdan
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/16/2018
ms.author: srrengar
ms.openlocfilehash: c2f953c98e41291951f07556bd0cd441d2793d1d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2019
ms.locfileid: "59259134"
---
# <a name="azure-service-fabric-monitoring-partners"></a>Partner di monitoraggio di Azure Service Fabric

Questo articolo illustra come si possono monitorare applicazioni, cluster e infrastruttura di Service Fabric con alcune soluzioni dei partner. Abbiamo collaborato con ognuno dei partner riportato di seguito per creare offerte integrate per Service Fabric.

## <a name="dynatrace"></a>Dynatrace

La nostra Integrazione con Dynatrace offre molte funzionalità predefinite per monitorare i cluster di Service Fabric. L'installazione di Dynatrace OneAgent nelle istanze del set di scalabilità di macchine virtuali offre contatori delle prestazioni e una topologia della distribuzione di Service Fabric fino al livello di app. Dynatrace è anche un'ottima scelta per il monitoraggio locale. Scopri di più le funzionalità elencate nella [annuncio](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) e [istruzioni](https://www.dynatrace.com/news/blog/automatic-end-to-end-service-fabric-monitoring-with-dynatrace/) abilitare Dynatrace nel cluster. 

## <a name="datadog"></a>Datadog

Datadog include un'estensione per i set di scalabilità di macchine virtuali per le istanze di Windows e Linux. Con Datadog è possibile raccogliere i log eventi Windows e raccogliere così gli eventi della piattaforma Service Fabric in Windows. Per le istruzioni su come inviare dati di diagnostica di Datadog, leggere [qui](https://www.datadoghq.com/blog/azure-monitoring-enhancements/#integrate-with-azure-service-fabric).

## <a name="appdynamics"></a>AppDynamics

L'integrazione di Service Fabric con AppDynamics è a livello di applicazione. Aggiornando le variabili di ambiente e con l'App Dynamics NuGets, è possibile inviare i dati di telemetria dell'applicazione a AppDynamics. Fare riferimento a queste [istruzioni](https://docs.appdynamics.com/display/AZURE/Install+AppDynamics+for+Azure+Service+Fabric) per informazioni su come integrare le applicazioni di Service Fabric .NET con AppDynamics.

## <a name="new-relic"></a>New Relic

New Relic è un altro strumento di gestione delle prestazioni delle applicazioni che si integra opportunamente con le applicazioni di Service Fabric. È possibile installare i pacchetti NuGet di New Relic e aggiungere le variabili di ambiente specifiche nei file manifesto per inviare la telemetria dell'applicazione a New Relic. Leggere queste [istruzioni](https://docs.newrelic.com/docs/agents/net-agent/azure-installation/install-net-agent-azure-service-fabric) per abilitare la telemetria di New Relic per le applicazioni di Service Fabric .NET.

## <a name="elk"></a>ELK 

Lo stack ELK è una raccolta di tecnologie open source: Elasticsearch, Logstash e Kibana. Usandole in combinazione, è possibile raccogliere, archiviare e analizzare i dati di monitoraggio e diagnostica di Service Fabric. L'esercitazione su come eseguire questa operazione con applicazioni Java di Service Fabric native è disponibile [qui](service-fabric-tutorial-java-elk.md). 

## <a name="humio"></a>Humio

Humio è un servizio di raccolta log che consente di raccogliere i log da applicazioni e gli eventi di Service Fabric nel cloud o in locale in tempo reale. Oltre all'osservabilità in tempo reale, Humio offre funzionalità di analisi e visualizzazione all'avanguardia per la visualizzazione e la raccolta di informazioni dettagliate dalla diagnostica. Humio prevede piani tariffari economicamente convenienti e incorporata di scalabilità, mantenendo lo è l'opzione velocità elevata. Si integra direttamente con gli eventi della piattaforma Service Fabric e i dati di telemetria dell'applicazione. Altre informazioni sull'integrazione Humio e Service Fabric [qui](https://github.com/humio/service-fabric-humio).

## <a name="next-steps"></a>Passaggi successivi

* [Panoramica del monitoraggio e della diagnostica](service-fabric-diagnostics-overview.md) in Service Fabric
* Informazioni su come [diagnosticare gli scenari comuni](service-fabric-diagnostics-common-scenarios.md) con i nostri strumenti proprietari
