---
title: Domande frequenti su Azure Spring cloud | Microsoft Docs
description: Vedere le domande frequenti sul cloud Spring di Azure
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 1fb241ab1335e6b9c9be55acbbe2e21378831b69
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607237"
---
# <a name="frequently-asked-questions"></a>Domande frequenti

Questo articolo illustra le domande frequenti su Azure Spring cloud. 

## <a name="general"></a>Generale

### <a name="why-azure-spring-cloud"></a>Perché Azure Spring cloud?

Azure Spring cloud offre una piattaforma distribuita come servizio (PaaS) per gli sviluppatori di Spring. Azure Spring cloud gestisce l'infrastruttura dell'applicazione in modo da potersi concentrare sul codice dell'applicazione e sulla logica di business. Le funzionalità di base integrate nel cloud Spring di Azure includono Eureka, server di configurazione, server del registro di sistema del servizio, servizio di compilazione pivotal, distribuzioni Blue-Green e altro ancora. Questo servizio consente inoltre agli sviluppatori di associare le proprie applicazioni ai servizi di Azure, ad esempio CosmosDB, MySQL e cache di Azure per Redis.

Azure Spring cloud migliora l'esperienza di diagnostica delle applicazioni per gli sviluppatori e gli operatori integrando monitoraggio di Azure, Application Insights e Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Quali piani di servizio offre Azure Spring cloud?

Azure Spring cloud offre un piano di servizio durante il periodo di anteprima.  Una distribuzione di Spring cloud contiene 16 core vCPU e 32GB di memoria.  Il limite superiore per ogni istanza di microservizio all'interno di una distribuzione è di 4 core vCPU con 8 GB di memoria.

Gruppi | Amount
------- | -------
Istanze app per applicazione Spring | 20
Totale istanze app per ogni istanza di servizio cloud di Azure Spring | 50 *
Istanze del servizio cloud Spring di Azure per area per sottoscrizione | 2
Volumi permanenti | 10 x 50 Gbyte

*_aprire un [ticket di supporto](https://azure.microsoft.com/support/faq/) per aumentare il limite._

Per altri dettagli, vedere [domande frequenti sul supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Quanto è sicuro Azure Spring cloud?

Sicurezza e privacy sono una delle principali priorità per i clienti di Azure e di Azure Spring cloud. Azure garantisce che solo il cliente abbia accesso ai dati, ai log o alle configurazioni dell'applicazione crittografando in modo sicuro tutti questi dati. Tutte le istanze del servizio in Azure Spring cloud sono isolate l'una dall'altra.

Il cloud Spring di Azure fornisce la gestione completa di certificati e SSL.

Le patch di sicurezza critiche per OpenJDK e i runtime di Spring cloud vengono applicate al cloud Spring di Azure il prima possibile.

### <a name="which-regions-azure-spring-cloud-are-available"></a>Quali aree di Azure Spring cloud sono disponibili?

Stati Uniti orientali, Stati Uniti occidentali 2, Europa occidentale e Asia sud-orientale.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Quali sono le limitazioni note di Azure Spring cloud

Ecco le limitazioni note di Azure Spring cloud mentre il servizio è in versione di anteprima.

* `spring.application.name` verrà eseguito l'override del nome dell'applicazione usato per creare ogni applicazione.
* `server.port` non è consentita nel file di configurazione dal repository git. Se si aggiunge il file di configurazione, l'applicazione potrebbe non essere raggiungibile da altre applicazioni o da Internet.
* I modelli portale di Azure e Gestione risorse non supportano il caricamento di pacchetti dell'applicazione. Questa operazione può essere eseguita solo tramite la distribuzione dell'applicazione da Azure CLI.
* Per le limitazioni della quota, vedere [quali piani di servizio offre Azure Spring cloud](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Come è possibile inviare commenti e suggerimenti e segnalare problemi?

Se si verificano problemi con Azure Spring cloud, creare una [richiesta di supporto per Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Per le richieste di funzionalità, rivolgersi a [commenti e suggerimenti su Azure](https://feedback.azure.com/forums/34192--general-feedback) per richiedere funzionalità o per inviare commenti e suggerimenti.

## <a name="development"></a>Sviluppo

### <a name="i-am-a-spring-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-aazure-spring-cloud-application"></a>Sono uno sviluppatore di Spring ma un nuovo utente di Azure, qual è il modo più rapido per imparare a sviluppare un'applicazione le Spring cloud?

Il modo più rapido per iniziare a usare Azure Spring cloud è seguire [questa Guida introduttiva](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Quali sono i runtime Java supportati dal cloud di Azure Spring?

Azure Spring cloud supporta Java 8 e 11.

### <a name="where-can-i-see-my-spring-application-logs-and-metrics"></a>Dove è possibile visualizzare i log e le metriche dell'applicazione Spring?

Trovare le metriche nella scheda Panoramica app e nella scheda [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics) .

Il cloud Spring di Azure supporta l'esportazione dei log e delle metriche delle applicazioni Spring in archiviazione di Azure, EventHub e [log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). Il nome della tabella in Log Analytics è `AppPlatformLogsforSpring`. Per abilitarla, vedere questo articolo sui [servizi di diagnostica](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring cloud supporta la traccia distribuita?

Sì, per altre informazioni, visitare la [traccia distribuita](spring-cloud-tutorial-distributed-tracing.md) .

### <a name="what-resource-types-does-service-binding-support"></a>Quali tipi di risorse supportano l'associazione al servizio?

Sono attualmente supportati tre servizi: Azure Cosmos DB, database di Azure per MySQL e cache di Azure per Redis.

### <a name="can-i-viewaddmove-persistent-volumes-from-inside-my-applications"></a>È possibile visualizzare/aggiungere o spostare volumi permanenti dall'interno delle applicazioni?
Sì.

## <a name="deployment"></a>Distribuzione

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure Spring cloud supporta la distribuzione Blue-Green?
Sì, per altri dettagli, vedere la [Guida dell'ambiente di gestione temporanea](spring-cloud-howto-staging-environment.md) .

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>È possibile accedere a Kubernetes per modificare i contenitori di applicazioni?

No.  Azure Spring cloud estrae lo sviluppatore dall'architettura sottostante, consentendo di concentrarsi sul codice dell'applicazione e sulla logica di business.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring cloud supporta la creazione di contenitori dall'origine?

Sì, per altri dettagli, visitare la pagina relativa alla [distribuzione dall'origine](spring-cloud-launch-from-source.md) .

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring cloud supporta la scalabilità automatica nelle istanze delle app?

No.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-microservices-to-azure-spring-cloud"></a>Quali sono le procedure consigliate per la migrazione di microservizi Spring esistenti al cloud Spring di Azure?

Prima di migrare i microservizi Spring esistenti al cloud Spring di Azure,
* Tutte le dipendenze dell'applicazione devono essere risolte.
* Preparare le voci di configurazione, le variabili di ambiente e i parametri JVM in modo da consentirne il confronto con la distribuzione nel cloud Spring di Azure.
* Se si vuole usare l'associazione al servizio, esaminare i servizi di Azure e assicurarsi di avere impostato le autorizzazioni di accesso appropriate.
* Si consiglia di rimuovere o disabilitare i servizi incorporati che potrebbero essere in conflitto con i servizi gestiti dal cloud Spring di Azure, ad esempio il servizio di individuazione dei servizi, il server di configurazione e così via.
*-* Si consiglia di usare le librerie di primavera pivotal ufficiali e stabili. Le versioni non ufficiale, beta o con fork delle librerie Spring pivot non hanno supporto per il contratto di servizio.

Al termine della migrazione, monitorare le metriche della CPU/RAM e il traffico di rete per assicurarsi che le istanze dell'applicazione vengano ridimensionate in modo appropriato.

## <a name="next-steps"></a>Passaggi successivi

[Per altre domande, consultare la guida alla risoluzione dei problemi](spring-cloud-troubleshoot.md).