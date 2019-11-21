---
title: Frequently asked questions about Azure Spring Cloud | Microsoft Docs
description: This article answers frequently asked questions about Azure Spring Cloud.
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: jeconnoc
ms.openlocfilehash: 6835132192040b4b943b156fb78ae1547522be0c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229243"
---
# <a name="azure-spring-cloud-faq"></a>Azure Spring Cloud FAQ

This article answers frequently asked questions about Azure Spring Cloud. 

## <a name="general"></a>Informazioni di carattere generale

### <a name="why-azure-spring-cloud"></a>Perché scegliere Azure Spring Cloud?

Azure Spring Cloud provides a platform as a service (PaaS) for Spring Cloud developers. Azure Spring Cloud manages your application infrastructure so that you can focus on application code and business logic. Core features built into Azure Spring Cloud include Eureka, Config Server, Service Registry Server, Pivotal Build Service, Blue-green deployments, and more. This service also enables developers to bind their applications with other Azure services, such as Azure Cosmos DB, Azure Database for MySQL, and Azure Cache for Redis.

Azure Spring Cloud enhances the application diagnostics experience for developers and operators by integrating Azure Monitor, Application Insights, and Log Analytics.

### <a name="what-service-plans-does-azure-spring-cloud-offer"></a>Quali piani del servizio offre Azure Spring Cloud?

Azure Spring Cloud offre un unico piano di servizio durante il periodo di anteprima.  A Spring Cloud deployment contains 16 vCPU cores and 32 gigabytes (GB) of memory.  The upper bound for each microservice instance within a deployment is 4 vCPU cores with 8 GB of memory.

Gruppi | Importo
------- | -------
Istanze di app per ogni applicazione Spring | 20
Numero totale di istanze di app per ogni istanza del servizio Azure Spring Cloud | 500
Istanze del servizio Azure Spring Cloud per area e per sottoscrizione | 10
Volumi permanenti | 10 x 50 GByte

\* _To raise the limit, open a [support ticket](https://azure.microsoft.com/support/faq/)._

For more information, see [Azure Support FAQ](https://azure.microsoft.com/support/faq/).

### <a name="how-secure-is-azure-spring-cloud"></a>Qual è il livello di sicurezza di Azure Spring Cloud?

Security and privacy are among the top priorities for Azure and Azure Spring Cloud customers. Azure helps ensure that only customers have access to application data, logs, or configurations by securely encrypting all of this data. Tutte le istanze del servizio in Azure Spring Cloud sono isolate le une dalle altre.

Azure Spring Cloud fornisce la gestione completa di SSL e certificati.

Le patch critiche di sicurezza per i runtime OpenJDK e Spring Cloud vengono applicate prima possibile ad Azure Spring Cloud.

### <a name="in-which-regions-is-azure-spring-cloud-available"></a>In which regions is Azure Spring Cloud available?

Stati Uniti orientali, Stati Uniti occidentali 2, Europa occidentale e Asia sud-orientale.

### <a name="what-are-the-known-limitations-of-azure-spring-cloud"></a>What are the known limitations of Azure Spring Cloud?

During preview release, Azure Spring Cloud has the following known limitations:

* `spring.application.name` will be overridden by the application name that's used to create each application.
* `server.port` is not allowed in the configuration file from the Git repo. Adding it to the configuration file will likely render your application unreachable from other applications or the internet.
* The Azure portal and Azure Resource Manager templates do not support uploading application packages. You can upload application packages only by deploying the application via the Azure CLI.
* To learn about quota limitations, see [What service plans does Azure Spring Cloud offer?](#what-service-plans-does-azure-spring-cloud-offer).

### <a name="how-can-i-provide-feedback-and-report-issues"></a>Come è possibile fornire feedback e segnalare problemi?

If you encounter any issues with Azure Spring Cloud, create an [Azure Support Request](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). To submit a feature request or provide feedback, go to [Azure Feedback](https://feedback.azure.com/forums/34192--general-feedback).

## <a name="development"></a>Sviluppo.

### <a name="i-am-a-spring-cloud-developer-but-new-to-azure-what-is-the-quickest-way-for-me-to-learn-how-to-develop-an-azure-spring-cloud-application"></a>I am a Spring Cloud developer but new to Azure. What is the quickest way for me to learn how to develop an Azure Spring Cloud application?

For the quickest way to get started with Azure Spring Cloud, follow the instructions in [Quickstart: Launch an Azure Spring Cloud application by using the Azure portal](spring-cloud-quickstart-launch-app-portal.md).

### <a name="what-java-runtime-does-azure-spring-cloud-support"></a>Quale runtime Java supporta Azure Spring Cloud?

Azure Spring Cloud supporta Java 8 e 11.

### <a name="where-can-i-view-my-spring-cloud-application-logs-and-metrics"></a>Where can I view my Spring Cloud application logs and metrics?

Le metriche sono disponibili nella scheda di panoramica dell'app e nella scheda [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#interacting-with-azure-monitor-metrics).

Azure Spring Cloud supports exporting Spring Cloud application logs and metrics to Azure Storage, EventHub, and [Log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs#log-queries). The table name in Log Analytics is *AppPlatformLogsforSpring*. To learn how to enable it, see [Diagnostic services](diagnostic-services.md).

### <a name="does-azure-spring-cloud-support-distributed-tracing"></a>Azure Spring Cloud supporta la traccia distribuita?

Sì. For more information, see [Tutorial: Use Distributed Tracing with Azure Spring Cloud](spring-cloud-tutorial-distributed-tracing.md).

### <a name="what-resource-types-does-service-binding-support"></a>Quali tipi di risorse supporta il binding di servizi?

Three services are currently supported: Azure Cosmos DB, Azure Database for MySQL, and Azure Cache for Redis.

### <a name="can-i-view-add-or-move-persistent-volumes-from-inside-my-applications"></a>Can I view, add, or move persistent volumes from inside my applications?

Sì.

## <a name="deployment"></a>Distribuzione

### <a name="does-azure-spring-cloud-support-blue-green-deployment"></a>Does Azure Spring Cloud support blue-green deployment?
Sì. For more information, see [Set up a staging environment](spring-cloud-howto-staging-environment.md).

### <a name="can-i-access-kubernetes-to-manipulate-my-application-containers"></a>È possibile accedere a Kubernetes per manipolare i contenitori dell'applicazione?

No.  Azure Spring Cloud astrae lo sviluppatore dall'architettura sottostante, consentendogli di concentrarsi sul codice dell'applicazione e sulla logica di business.

### <a name="does-azure-spring-cloud-support-building-containers-from-source"></a>Azure Spring Cloud supporta la creazione di contenitori dall'origine?

Sì. For more information, see [Launch your Spring Cloud application from source code](spring-cloud-launch-from-source.md).

### <a name="does-azure-spring-cloud-support-autoscaling-in-app-instances"></a>Azure Spring Cloud supporta il ridimensionamento automatico nelle istanze di app?

No.

### <a name="what-are-the-best-practices-for-migrating-existing-spring-cloud-microservices-to-azure-spring-cloud"></a>What are the best practices for migrating existing Spring Cloud microservices to Azure Spring Cloud?

As you're migrating existing Spring Cloud microservices to Azure Spring Cloud, it's a good idea to observe the following best practices:
* È necessario risolvere tutte le dipendenze dell'applicazione.
* Prepare your configuration entries, environment variables, and JVM parameters so that you can compare them with the deployment in Azure Spring Cloud.
* If you want to use Service Binding, go through your Azure services and ensure that you've set the appropriate access permissions.
* We recommend that you remove or disable any embedded services that might conflict with services that are managed by Azure Spring Cloud, such as our Service Discovery service, Config Server, and so on.
* We recommend that you use official, stable Pivotal Spring libraries. Unofficial, beta, or forked versions of Pivotal Spring libraries have no service-level agreement (SLA) support.

After the migration, monitor your CPU/RAM metrics and network traffic to ensure that the application instances are scaled appropriately.

## <a name="next-steps"></a>Passaggi successivi

If you have further questions, see the [Azure Spring Cloud troubleshooting guide](spring-cloud-troubleshoot.md).
