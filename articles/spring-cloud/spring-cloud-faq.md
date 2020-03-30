---
title: Domande frequenti su Azure Spring Cloud Documenti Microsoft
description: Questo articolo risponde alle domande frequenti su Azure Spring Cloud.This article answers frequently asked questions about Azure Spring Cloud.
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: brendm
ms.openlocfilehash: 62623bcadb35f21117ddc2601195e34598c2dff5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298765"
---
# <a name="azure-spring-cloud-faq"></a>Domande frequenti su Azure Spring Cloud

Questo articolo risponde alle domande frequenti su Azure Spring Cloud.This article answers frequently asked questions about Azure Spring Cloud. 

## <a name="general"></a>Generale

### <a name="why-azure-spring-cloud"></a>Perché scegliere Azure Spring Cloud?

Azure Spring Cloud offre una piattaforma come servizio (PaaS) per gli sviluppatori di Spring Cloud. Azure Spring Cloud gestisce l'infrastruttura dell'applicazione in modo che sia possibile concentrarsi sul codice dell'applicazione e sulla logica di business. Le funzionalità di base integrate in Azure Spring Cloud includono Eureka, Config Server, Service Registry Server, Pivotal Build Service, distribuzioni blu-verdi e altro ancora. Questo servizio consente inoltre agli sviluppatori di associare le applicazioni ad altri servizi di Azure, ad esempio Azure Cosmos DB, Database di Azure per MySQL e Cache di Azure per Redis.This service also enables developers to bind their applications with other Azure services, such as Azure Cosmos DB, Azure Database for MySQL, and Azure Cache for Redis.

Azure Spring Cloud migliora l'esperienza di diagnostica delle applicazioni per sviluppatori e operatori integrando Monitoraggio di Azure, Application Insights e Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Quali piani del servizio offre Azure Spring Cloud?

Azure Spring Cloud offre un unico piano di servizio durante il periodo di anteprima.  Una distribuzione di Spring Cloud contiene 16 core vCPU e 32 gigabyte (GB) di memoria.  Il limite superiore per ogni istanza di microservizio all'interno di una distribuzione è 4 core vCPU con 8 GB di memoria.

Risorsa | Amount
------- | -------
Istanze di app per ogni applicazione Spring | 20
Numero totale di istanze di app per ogni istanza del servizio Azure Spring Cloud | 500
Istanze del servizio Azure Spring Cloud per area e per sottoscrizione | 10
Volumi permanenti | 10 x 50 GByte

\*_Per aumentare il limite, aprire un [ticket](https://azure.microsoft.com/support/faq/)_ di supporto .

Per altre informazioni, vedere Domande frequenti sul supporto di Azure.For more [information,](https://azure.microsoft.com/support/faq/)see Azure Support FAQ .

### <a name="how-secure-is-azure-spring-cloud"></a>Qual è il livello di sicurezza di Azure Spring Cloud?

Sicurezza e privacy sono tra le priorità principali per i clienti di Azure e Azure Spring Cloud. Azure garantisce che solo i clienti abbiano accesso ai dati, ai log o alle configurazioni dell'applicazione crittografandoli in modo sicuro. Tutte le istanze del servizio in Azure Spring Cloud sono isolate le une dalle altre.

Azure Spring Cloud offre TLS/SSL completo e la gestione dei certificati.

Le patch critiche di sicurezza per i runtime OpenJDK e Spring Cloud vengono applicate prima possibile ad Azure Spring Cloud.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>In quali aree è disponibile Azure Spring Cloud?

Stati Uniti orientali, Stati Uniti occidentali 2, Europa occidentale e Asia sud-orientale.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Quali sono le limitazioni note di Azure Spring Cloud?

Durante la versione di anteprima, Azure Spring Cloud presenta le limitazioni note seguenti:During preview release, Azure Spring Cloud has the following known limitations:

* `spring.application.name`verrà eseguito l'override del nome dell'applicazione utilizzato per creare ogni applicazione.
* `server.port`non è consentito nel file di configurazione dal repository Git. Aggiungendolo al file di configurazione probabilmente renderà l'applicazione irraggiungibile da altre applicazioni o da Internet.
* Il portale di Azure e i modelli di Azure Resource Manager non supportano il caricamento di pacchetti di applicazioni. È possibile caricare i pacchetti dell'applicazione solo distribuendo l'applicazione tramite l'interfaccia della riga di comando di Azure.You can upload application packages only by deploying the application via the Azure CLI.
* Per altre informazioni sulle limitazioni delle quote, vedere Quali piani di [servizio offrono Azure Spring Cloud?](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Come è possibile fornire feedback e segnalare problemi?

Se si verificano problemi con Azure Spring Cloud, creare una richiesta di supporto di [Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Per inviare una richiesta di funzionalità o fornire commenti e suggerimenti, passare a [Commenti e suggerimenti](https://feedback.azure.com/forums/34192--general-feedback)di Azure.To submit a feature request or provide feedback, go to Azure Feedback .

## <a name="development"></a>Sviluppo

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>Sono uno sviluppatore Spring Cloud ma non sono nuovo di Azure. Qual è il modo più rapido per imparare a sviluppare un'applicazione Azure Spring Cloud?

Per informazioni sul modo più rapido per iniziare a usare Azure Spring Cloud, seguire le istruzioni in [Guida introduttiva: Avviare un'applicazione Azure Spring Cloud usando il portale](spring-cloud-quickstart-launch-app-portal.md)di Azure.

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Quale runtime Java supporta Azure Spring Cloud?

Azure Spring Cloud supporta Java 8 e 11.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Dove posso visualizzare i log e le metriche delle mie applicazioni Spring Cloud?

Le metriche sono disponibili nella scheda di panoramica dell'app e nella scheda [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics).

Azure Spring Cloud supporta l'esportazione dei log e delle metriche delle applicazioni Spring Cloud in Archiviazione di Azure, EventHub e [Log Analytics.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries) Il nome della tabella in Log Analytics è *AppPlatformLogsforSpring*. Per informazioni su come abilitarlo, vedere Servizi di [diagnostica](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud supporta la traccia distribuita?

Sì. Per altre informazioni, vedere [Esercitazione: Usare](spring-cloud-tutorial-distributed-tracing.md)la traccia distribuita con Azure Spring Cloud.

### <a name="what-resource-types-does-service-binding-support"></a>Quali tipi di risorse supporta il binding di servizi?

Attualmente sono supportati tre servizi: Azure Cosmos DB, Azure Database for MySQL e Azure Cache for Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>È possibile visualizzare, aggiungere o spostare volumi persistenti dall'interno delle applicazioni?

Sì.

## <a name="deployment"></a>Distribuzione

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure Spring Cloud supporta la distribuzione blu-verde?
Sì. Per ulteriori informazioni, vedere [Impostare un ambiente](spring-cloud-howto-staging-environment.md)di gestione temporanea.

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>È possibile accedere a Kubernetes per manipolare i contenitori dell'applicazione?

No.  Azure Spring Cloud astrae lo sviluppatore dall'architettura sottostante, consentendogli di concentrarsi sul codice dell'applicazione e sulla logica di business.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud supporta la creazione di contenitori dall'origine?

Sì. Per ulteriori informazioni, consultate [Avviare l'applicazione Spring Cloud dal codice sorgente.](spring-cloud-launch-from-source.md)

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud supporta il ridimensionamento automatico nelle istanze di app?

No.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>Quali sono le procedure consigliate per la migrazione dei microservizi Spring Cloud esistenti a Azure Spring Cloud?

Durante la migrazione dei microservizi Spring Cloud esistenti a Azure Spring Cloud, è consigliabile osservare le procedure consigliate seguenti:As you're migrating existing Spring Cloud microservices to Azure Spring Cloud, it's a good idea to observe the following best practices:
* È necessario risolvere tutte le dipendenze dell'applicazione.
* Preparare le voci di configurazione, le variabili di ambiente e i parametri JVM in modo da poterli confrontare con la distribuzione in Azure Spring Cloud.Prepare your configuration entries, environment variables, and JVM parameters so that you can compare them with the deployment in Azure Spring Cloud.
* Se si vuole usare l'associazione al servizio, esaminare i servizi di Azure e verificare di aver impostato le autorizzazioni di accesso appropriate.
* È consigliabile rimuovere o disabilitare i servizi incorporati che potrebbero entrare in conflitto con i servizi gestiti da Azure Spring Cloud, ad esempio il servizio di individuazione dei servizi, il server di configurazione e così via.
* Si consiglia di utilizzare le librerie ufficiali e stabili di Pivotal Spring. Le versioni non ufficiali, beta o forked delle librerie Pivotal Spring non supportano il contratto di servizio (SLA).

Dopo la migrazione, monitorare le metriche di CPU/RAM e il traffico di rete per assicurarsi che le istanze dell'applicazione vengano ridimensionate in modo appropriato.

## <a name="next-steps"></a>Passaggi successivi

Per altre domande, vedere la guida alla [risoluzione dei problemi di Azure Spring Cloud](spring-cloud-troubleshoot.md).
