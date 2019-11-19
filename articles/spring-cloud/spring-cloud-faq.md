---
title: Domande frequenti su Azure Spring cloud | Microsoft Docs
description: Questo articolo fornisce le risposte alle domande frequenti su Azure Spring cloud.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 40275b3f643517236db26be8c2784144526fb7a1
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151756"
---
# <a name="azure-spring-cloud-faq"></a>Domande frequenti su Azure Spring cloud

Questo articolo fornisce le risposte alle domande frequenti su Azure Spring cloud. 

## <a name="general"></a>General

### <a name="why-azure-spring-cloud"></a>Perché scegliere Azure Spring Cloud?

Azure Spring cloud offre una piattaforma distribuita come servizio (PaaS) per gli sviluppatori di Spring cloud. Azure Spring cloud gestisce l'infrastruttura dell'applicazione in modo da potersi concentrare sul codice dell'applicazione e sulla logica di business. Le funzionalità di base integrate nel cloud Spring di Azure includono Eureka, server di configurazione, server del registro di sistema del servizio, servizio di compilazione pivotal, distribuzioni Blue-Green e altro ancora. Questo servizio consente inoltre agli sviluppatori di associare le proprie applicazioni ad altri servizi di Azure, ad esempio Azure Cosmos DB, database di Azure per MySQL e cache di Azure per Redis.

Azure Spring cloud migliora l'esperienza di diagnostica delle applicazioni per gli sviluppatori e gli operatori integrando monitoraggio di Azure, Application Insights e Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Quali piani del servizio offre Azure Spring Cloud?

Azure Spring Cloud offre un unico piano di servizio durante il periodo di anteprima.  Una distribuzione di Spring cloud contiene 16 core vCPU e 32 gigabyte (GB) di memoria.  Il limite superiore per ogni istanza di microservizio all'interno di una distribuzione è di 4 core vCPU con 8 GB di memoria.

Risorsa | Amount
------- | -------
Istanze dell'app per ogni applicazione cloud Spring | 20
Numero totale di istanze di app per ogni istanza del servizio Azure Spring Cloud | 50*
Istanze del servizio Azure Spring Cloud per area e per sottoscrizione | 2*
Volumi permanenti | 10 x 50 GB

\* _per aumentare il limite, aprire un [ticket di supporto](https://azure.microsoft.com/support/faq/)._

Per altre informazioni, vedere [domande frequenti sul supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Qual è il livello di sicurezza di Azure Spring Cloud?

Sicurezza e privacy sono tra le priorità principali per i clienti di Azure e Azure Spring cloud. Azure contribuisce a garantire che solo i clienti abbiano accesso a dati, log o configurazioni dell'applicazione crittografando in modo sicuro tutti questi dati. Tutte le istanze del servizio in Azure Spring Cloud sono isolate le une dalle altre.

Azure Spring Cloud fornisce la gestione completa di SSL e certificati.

Le patch critiche di sicurezza per i runtime OpenJDK e Spring Cloud vengono applicate prima possibile ad Azure Spring Cloud.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>In quali aree è disponibile Azure Spring cloud?

Stati Uniti orientali, Stati Uniti occidentali 2, Europa occidentale e Asia sud-orientale.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Quali sono le limitazioni note di Azure Spring cloud?

Durante la versione di anteprima, Azure Spring cloud presenta le limitazioni note seguenti:

* `spring.application.name` verrà eseguito l'override del nome dell'applicazione usato per creare ogni applicazione.
* `server.port` non è consentita nel file di configurazione dal repository git. L'aggiunta al file di configurazione probabilmente renderà l'applicazione irraggiungibile da altre applicazioni o da Internet.
* I modelli portale di Azure e Azure Resource Manager non supportano il caricamento di pacchetti dell'applicazione. È possibile caricare i pacchetti dell'applicazione solo distribuendo l'applicazione tramite l'interfaccia della riga di comando di Azure.
* Per informazioni sulle limitazioni della quota, vedere [quali piani di servizio offre Azure Spring cloud?](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Come è possibile fornire feedback e segnalare problemi?

Se si verificano problemi con Azure Spring cloud, creare una [richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Per inviare una richiesta di funzionalità o fornire commenti e suggerimenti, passare a [commenti e suggerimenti su Azure](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Sviluppo

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Sono uno sviluppatore di Spring cloud, ma è una novità di Azure. Qual è il modo più rapido per imparare a sviluppare un'applicazione Azure Spring cloud?

Per il modo più rapido per iniziare a usare Azure Spring cloud, seguire le istruzioni riportate nella [Guida introduttiva: avviare un'applicazione Azure Spring cloud usando il portale di Azure](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Quale runtime Java supporta Azure Spring Cloud?

Azure Spring Cloud supporta Java 8 e 11.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Dove è possibile visualizzare i log e le metriche dell'applicazione Spring cloud?

Le metriche sono disponibili nella scheda di panoramica dell'app e nella scheda [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics).

Il cloud Spring di Azure supporta l'esportazione di metriche e log applicazioni di Spring cloud in archiviazione di Azure, EventHub e [log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). Il nome della tabella in Log Analytics è *AppPlatformLogsforSpring*. Per informazioni su come abilitarla, vedere [servizi di diagnostica](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud supporta la traccia distribuita?

Sì. Per altre informazioni, vedere [esercitazione: usare la traccia distribuita con Azure Spring cloud](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>Quali tipi di risorse supporta il binding di servizi?

Sono attualmente supportati tre servizi: Azure Cosmos DB, database di Azure per MySQL e cache di Azure per Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>È possibile visualizzare, aggiungere o spostare volumi permanenti dall'interno delle applicazioni?

Sì.

## <a name="deployment"></a>Distribuzione

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure Spring cloud supporta la distribuzione Blue-Green?
Sì. Per ulteriori informazioni, vedere la pagina relativa alla [configurazione di un ambiente di gestione temporanea](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>È possibile accedere a Kubernetes per manipolare i contenitori dell'applicazione?

No.  Azure Spring Cloud astrae lo sviluppatore dall'architettura sottostante, consentendogli di concentrarsi sul codice dell'applicazione e sulla logica di business.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud supporta la creazione di contenitori dall'origine?

Sì. Per altre informazioni, vedere [avviare l'applicazione Spring cloud dal codice sorgente](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud supporta il ridimensionamento automatico nelle istanze di app?

No.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Quali sono le procedure consigliate per la migrazione di microservizi cloud primaverili esistenti al cloud Spring di Azure?

Quando si esegue la migrazione di microservizi cloud primaverili esistenti al cloud Spring di Azure, è consigliabile osservare le procedure consigliate seguenti:
* È necessario risolvere tutte le dipendenze dell'applicazione.
* Preparare le voci di configurazione, le variabili di ambiente e i parametri JVM in modo da consentirne il confronto con la distribuzione nel cloud Spring di Azure.
* Se si vuole usare l'associazione al servizio, esaminare i servizi di Azure e assicurarsi di avere impostato le autorizzazioni di accesso appropriate.
* Si consiglia di rimuovere o disabilitare i servizi incorporati che potrebbero essere in conflitto con i servizi gestiti da Azure Spring cloud, ad esempio il servizio di individuazione dei servizi, il server di configurazione e così via.
* Si consiglia di usare le librerie di primavera pivotal stabili e ufficiali. Le versioni non ufficiale, beta o con fork delle librerie di Spring pivot non hanno supporto per il contratto di servizio (SLA).

Dopo la migrazione, monitorare le metriche della CPU/RAM e il traffico di rete per assicurarsi che le istanze dell'applicazione vengano ridimensionate in modo appropriato.

## <a name="next-steps"></a>Passaggi successivi

Per altre domande, vedere la guida alla [risoluzione dei problemi di Azure Spring cloud](spring-cloud-troubleshoot.md).
