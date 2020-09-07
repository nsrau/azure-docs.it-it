---
title: Introduzione ad Azure Spring Cloud
description: Informazioni sulle funzionalità e i vantaggi di Azure Spring Cloud per la distribuzione e la gestione di applicazioni Java Spring in Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 11/4/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: aa3f1032301224701f5bfc08807e89194f263da6
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89255240"
---
# <a name="what-is-azure-spring-cloud"></a>Cos'è Azure Spring Cloud?

Azure Spring Cloud semplifica la distribuzione di applicazioni di microservizi basate su Spring Boot in Azure, senza modificare il codice.  Azure Spring Cloud gestisce l'infrastruttura delle applicazioni Spring Cloud per consentire agli sviluppatori di concentrarsi sul codice.  Spring Cloud fornisce la gestione del ciclo di vita tramite funzionalità complete di monitoraggio e diagnostica, gestione della configurazione, individuazione dei servizi, integrazione di CI/CD, distribuzioni blu/verde e altro ancora.

Facendo parte dell'ecosistema di Azure, Azure Spring Cloud si può associare facilmente ad altri servizi di Azure, come archiviazione, database, monitoraggio e così via.

Questa introduzione descrive il server di configurazione di Azure Spring Cloud e spiega come abilitare le distribuzioni blu/verde, ridimensionare le app e monitorare le prestazioni delle app.

## <a name="spring-cloud-config-server"></a>Spring Cloud Config Server

Spring Cloud Config Server fornisce la configurazione esternalizzata in un sistema distribuito con il supporto sia lato server che lato client.  Offre una posizione centrale da cui gestire le proprietà delle applicazioni in tutti gli ambienti. Per altre informazioni, vedere la [documentazione di riferimento di Spring Cloud Config](https://spring.io/projects/spring-cloud-config.md). 

## <a name="enable-bluegreen-deployments"></a>Abilitare distribuzioni blu/verde

Azure Spring Cloud supporta distribuzioni blu/verde per il rilascio e l'aggiornamento di codice negli ambienti di produzione.  Questo modello di gestione del cambiamento consente agli sviluppatori di implementare funzionalità e modifiche del codice con la sicurezza di un fallback immediato, se necessario.  Gli sviluppatori possono concentrarsi sulla scrittura di codice con più ambienti di produzione per eseguire l'aggiornamento o il rollback delle modifiche del codice senza interrompere l'applicazione.  Per altre informazioni sugli ambienti di staging e le distribuzioni blu/verde, vedere questa [guida pratica](spring-cloud-howto-staging-environment.md).

## <a name="automate-cicd-pipelines"></a>Automatizzare le pipeline CI/CD

Azure Spring Cloud prevede l'integrazione con Azure DevOps tramite l'interfaccia della riga di comando di Azure.  Usando Azure DevOps, è possibile automatizzare l'integrazione e la distribuzione del codice nell'applicazione Spring.  Per altre informazioni, vedere questo [articolo](spring-cloud-howto-cicd.md).

## <a name="scale-your-application"></a>Ridimensionare l'applicazione

Azure Spring Cloud consente di ridimensionare i microservizi nel dashboard di Azure Spring Cloud.  È possibile aumentare o ridurre sia il numero di vCPU che la quantità di memoria disponibile per i microservizi, in base a specifici requisiti.  Il ridimensionamento diventa effettivo dopo pochi secondi e non richiede modifiche del codice né la ridistribuzione.  Per altre informazioni, completare questa [esercitazione](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Monitoraggio delle applicazioni

### <a name="monitor-your-application-using-distributed-tracing-and-azure-app-insights"></a>Monitorare l'applicazione con la traccia distribuita e Azure Application Insights

Gli strumenti di traccia distribuita di Spring Cloud consentono agli sviluppatori di eseguire il debug e il monitoraggio delle complesse interconnessioni tra i microservizi in un'applicazione.  Integrando [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) con Azure [Application Insights](../azure-monitor/insights/insights-overview.md), Azure offre potenti funzionalità di traccia distribuita dal portale di Azure.  Per altre informazioni, completare questa [esercitazione](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Passaggi successivi
Per iniziare, completare l'avvio rapido su Spring Cloud:
> [!div class="nextstepaction"]
> [Avvio rapido: Distribuire la prima applicazione Azure Spring Cloud](spring-cloud-quickstart.md)

Altri esempi sono disponibili in GitHub: [Esempi di Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
