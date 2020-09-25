---
title: Introduzione ad Azure Spring Cloud
description: Informazioni sulle funzionalità e i vantaggi di Azure Spring Cloud per la distribuzione e la gestione di applicazioni Java Spring in Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: overview
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: f622042c9a2e8cc5bc11c8dc909d02afe38e5c78
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90908356"
---
# <a name="what-is-azure-spring-cloud"></a>Cos'è Azure Spring Cloud?

Azure Spring Cloud semplifica la distribuzione di applicazioni di microservizi basate su Spring Boot in Azure, senza modificare il codice.  Azure Spring Cloud gestisce l'infrastruttura delle applicazioni Spring Cloud per consentire agli sviluppatori di concentrarsi sul codice.  Spring Cloud fornisce la gestione del ciclo di vita tramite funzionalità complete di monitoraggio e diagnostica, gestione della configurazione, individuazione dei servizi, integrazione di CI/CD, distribuzioni blu/verde e altro ancora.

Azure Spring Cloud supporta sia app [Spring Boot](https://spring.io/projects/spring-boot) per Java che [Steeltoe](https://steeltoe.io/) per ASP.NET Core. Il supporto di Steeltoe è attualmente disponibile come anteprima pubblica. Le offerte di anteprima pubblica consentono di sperimentare le nuove funzionalità prima del rilascio della versione ufficiale.  I servizi e le funzionalità di anteprima pubblica non sono destinati all'uso in produzione.  Per altre informazioni, vedere le [domande frequenti](https://azure.microsoft.com/support/faq/) o inviare una [richiesta di supporto](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

Facendo parte dell'ecosistema di Azure, Azure Spring Cloud si può associare facilmente ad altri servizi di Azure, come archiviazione, database, monitoraggio e così via.

Questa introduzione descrive le funzionalità seguenti di Azure Spring Cloud:

* Server di configurazione
* Distribuzioni blue/green
* Ridimensionamento delle applicazioni
* Integrazione con Azure DevOps
* Monitoraggio delle applicazioni

## <a name="spring-cloud-config-server"></a>Spring Cloud Config Server

Spring Cloud Config Server fornisce la configurazione esternalizzata in un sistema distribuito con il supporto sia lato server che lato client.  Offre una posizione centrale da cui gestire le proprietà delle applicazioni in tutti gli ambienti. Per altre informazioni, vedere la [documentazione di riferimento di Spring Cloud Config](https://spring.io/projects/spring-cloud-config.md). 

## <a name="bluegreen-deployments"></a>Distribuzioni blue/green

Azure Spring Cloud supporta distribuzioni blu/verde per il rilascio e l'aggiornamento di codice negli ambienti di produzione.  Questo modello di gestione del cambiamento consente agli sviluppatori di implementare funzionalità e modifiche del codice con la sicurezza di un fallback immediato, se necessario.  Gli sviluppatori possono concentrarsi sulla scrittura di codice con più ambienti di produzione per eseguire l'aggiornamento o il rollback delle modifiche del codice senza interrompere l'applicazione.  Per altre informazioni sugli ambienti di staging e le distribuzioni blu/verde, vedere questa [guida pratica](spring-cloud-howto-staging-environment.md).

## <a name="cicd-pipeline-automation"></a>Automazione della pipeline CI/CD

Azure Spring Cloud prevede l'integrazione con Azure DevOps tramite l'interfaccia della riga di comando di Azure.  Usando Azure DevOps, è possibile automatizzare l'integrazione e la distribuzione del codice nell'applicazione Spring.  Per altre informazioni, vedere questo [articolo](spring-cloud-howto-cicd.md).

## <a name="application-scaling"></a>Ridimensionamento delle applicazioni

Azure Spring Cloud consente di ridimensionare i microservizi nel dashboard di Azure Spring Cloud.  È possibile aumentare o ridurre sia il numero di vCPU che la quantità di memoria disponibile per i microservizi, in base a specifici requisiti.  Il ridimensionamento diventa effettivo dopo pochi secondi e non richiede modifiche del codice né la ridistribuzione.  Per altre informazioni, completare questa [esercitazione](spring-cloud-tutorial-scale-manual.md).

## <a name="application-monitoring"></a>Monitoraggio delle applicazioni

Gli strumenti di traccia distribuita di Spring Cloud consentono agli sviluppatori di eseguire il debug e il monitoraggio delle complesse interconnessioni tra i microservizi in un'applicazione.  Integrando [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) con Azure [Application Insights](../azure-monitor/insights/insights-overview.md), Azure offre potenti funzionalità di traccia distribuita dal portale di Azure.  Per altre informazioni, completare questa [esercitazione](spring-cloud-tutorial-distributed-tracing.md).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, completare l'argomento di [avvio rapido su Spring Cloud](spring-cloud-quickstart.md)

Esempi sono disponibili in GitHub: [Esempi di Azure Spring Cloud](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/).
