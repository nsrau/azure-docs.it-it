---
title: Domande frequenti su Azure Spring Cloud | Microsoft Docs
description: Leggere le domande frequenti su Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: jeconnoc
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 69b3827bbb53a5f989f3bdec0d5c4ea6035130f0
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72592316"
---
# <a name="frequently-asked-questions"></a>Domande frequenti

Questo articolo tratta alcune domande frequenti relative ad Azure Spring Cloud. 

## <a name="general"></a>Generale

### <a name="why-azure-spring-cloud"></a>Perché scegliere Azure Spring Cloud?

Azure Spring Cloud fornisce una piattaforma distribuita come servizio (PaaS, Platform as a Service) per gli sviluppatori Spring. Azure Spring Cloud gestisce l'infrastruttura dell'applicazione, consentendo agli sviluppatori di dedicarsi al codice dell'applicazione e alla logica di business. Le funzionalità essenziali integrate in Azure Spring Cloud includono Eureka, Config Server, server Service Registry, Pivotal Build Service, distribuzioni blu/verde e altro ancora. Questo servizio consente inoltre agli sviluppatori di associare le loro applicazioni ai servizi di Azure, come CosmosDB, MySQL e Cache di Azure per Redis.

Azure Spring Cloud ottimizza l'esperienza di diagnostica dell'applicazione per gli sviluppatori e gli operatori grazie all'integrazione di Monitoraggio di Azure, Application Insights e Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Quali piani del servizio offre Azure Spring Cloud?

Azure Spring Cloud offre un unico piano di servizio durante il periodo di anteprima.  Una distribuzione di Spring Cloud contiene 16 core vCPU e 32 GB di memoria.  Il limite superiore per ogni istanza di microservizio all'interno di una distribuzione è di 4 core vCPU con 8 GB di memoria.

Risorsa | Amount
------- | -------
Istanze di app per ogni applicazione Spring | 20
Numero totale di istanze di app per ogni istanza del servizio Azure Spring Cloud | 50*
Istanze del servizio Azure Spring Cloud per area e per sottoscrizione | 2*
Volumi permanenti | 10 x 50 GByte

*_Aprire un [ticket di supporto](https://azure.microsoft.com/support/faq/) per aumentare il limite._

Per altre informazioni, vedere le [domande frequenti sul supporto tecnico di Azure](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Qual è il livello di sicurezza di Azure Spring Cloud?

La sicurezza e la privacy rientrano tra le principali priorità per i clienti di Azure e Azure Spring Cloud. Azure assicura che solo il cliente abbia accesso ai dati, ai log o alle configurazioni dell'applicazione applicando la crittografia sicura a tutti i dati. Tutte le istanze del servizio in Azure Spring Cloud sono isolate le une dalle altre.

Azure Spring Cloud fornisce la gestione completa di SSL e certificati.

Le patch critiche di sicurezza per i runtime OpenJDK e Spring Cloud vengono applicate prima possibile ad Azure Spring Cloud.

### <a name="which-regions-azure-spring-cloud-are-available"></a>Quali aree di Azure Spring Cloud sono disponibili?

Stati Uniti orientali, Stati Uniti occidentali 2, Europa occidentale e Asia sud-orientale.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>Quali sono le limitazioni note di Azure Spring Cloud?

Ecco le limitazioni note di Azure Spring Cloud durante la fase di anteprima del servizio.

* `spring.application.name` verrà sostituito dal nome usato per creare ogni applicazione.
* `server.port` non è consentito nel file di configurazione del repository Git. Se viene aggiunto al file di configurazione, è probabile che l'applicazione risulti irraggiungibile da altre applicazioni o da Internet.
* Il portale di Azure e i modelli di Resource Manager non supportano il caricamento di pacchetti dell'applicazione. Questa operazione può essere eseguita solo tramite distribuzione dell'applicazione con l'interfaccia della riga di comando di Azure.
* Per le limitazioni delle quote, vedere[Quali piani del servizio offre Azure Spring Cloud](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Come è possibile fornire feedback e segnalare problemi?

Se sono state create istanze personalizzate del servizio Spring in Azure Spring Cloud, è possibile creare una [richiesta di supporto di Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Se l'onboarding ad Azure Spring Cloud non è stato ancora eseguito, è possibile accedere al sito di [feedback di Azure](https://feedback.azure.com/) per richiedere funzionalità o inviare feedback.

## <a name="development"></a>Sviluppo.

### <a name="i-am-a-spring-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-aazure-spring-cloud-application"></a>Per gli sviluppatori Spring non esperti di Azure, qual è il modo più rapido per imparare a sviluppare applicazioni Azure Spring Cloud?

Il modo più rapido per iniziare a usare Azure Spring Cloud consiste nel seguire questo [argomento di avvio rapido](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Quale runtime Java supporta Azure Spring Cloud?

Azure Spring Cloud supporta Java 8 e 11.

### <a name="where-can-i-see-my-spring-application-logs-and-metrics"></a>Dove è possibile visualizzare i log e le metriche delle applicazioni Spring?

Le metriche sono disponibili nella scheda di panoramica dell'app e nella scheda [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics).

Azure Spring Cloud supporta l'esportazione di log e metriche delle applicazioni Spring in Archiviazione di Azure, Hub eventi e [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). Il nome della tabella in Log Analytics è `AppPlatformLogsforSpring`. Per abilitare la funzionalità, vedere questo articolo sui [servizi di diagnostica](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud supporta la traccia distribuita?

Sì, per altre informazioni vedere [Traccia distribuita](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>Quali tipi di risorse supporta il binding di servizi?

Attualmente sono supportati tre servizi: Azure Cosmos DB, Database di Azure per MySQL e Cache di Azure per Redis.

### <a name="can-i-viewaddmove-persistent-volumes-from-inside-my-applications"></a>È possibile visualizzare, aggiungere e/o spostare volumi persistenti dall'interno delle applicazioni?
Sì.

## <a name="deployment"></a>Distribuzione

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Azure Spring Cloud supporta le distribuzioni blu/verde?
Sì, per altre informazioni vedere la [guida all'ambiente di staging](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>È possibile accedere a Kubernetes per manipolare i contenitori dell'applicazione?

No.  Azure Spring Cloud astrae lo sviluppatore dall'architettura sottostante, consentendogli di concentrarsi sul codice dell'applicazione e sulla logica di business.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud supporta la creazione di contenitori dall'origine?

Sì, per altre informazioni vedere [Distribuire dall'origine](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud supporta il ridimensionamento automatico nelle istanze di app?

No.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-microservices-to-azure-spring-cloud"></a>Quali sono le procedure consigliate per eseguire la migrazione degli attuali microservizi Spring ad Azure Spring Cloud?

Prima di eseguire la migrazione degli attuali microservizi Spring ad Azure Spring Cloud:
* È necessario risolvere tutte le dipendenze dell'applicazione.
* Preparare le voci di configurazione, le variabili di ambiente e i parametri JVM in modo da confrontarli con la distribuzione in Azure Spring Cloud.
* Se si vuole usare il binding di servizi, esaminare i servizi di Azure e assicurarsi di avere le autorizzazioni di accesso appropriate.
* È consigliabile rimuovere o disabilitare i servizi incorporati che potrebbero entrare in conflitto con i servizi gestiti da Azure Spring Cloud, ad esempio il Servizio di individuazione, Config Server e così via.
*-* È consigliabile usare librerie Pivotal Spring ufficiali e stabili. Le versioni delle librerie Pivotal Spring non ufficiali, beta o copiate tramite fork non sono supportate da contratti di servizio.

Dopo la migrazione, monitorare le metriche relative a CPU/RAM e il traffico di rete per verificare il corretto dimensionamento delle istanze dell'applicazione.

## <a name="next-steps"></a>Passaggi successivi

[In caso di altre domande, vedere la guida alla risoluzione di problemi](spring-cloud-troubleshoot.md).