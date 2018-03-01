---
title: Monitorare un servizio cloud di Azure | Microsoft Docs
description: Descrive cosa comporta monitorare un servizio cloud di Azure e alcune opzioni degli utenti.
services: cloud-services
documentationcenter: 
author: thraka
manager: timlt
editor: 
ms.assetid: 
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: adegeo
ms.openlocfilehash: f3a3a1beb8540ee8ab0502379396c06ea505fb44
ms.sourcegitcommit: 4723859f545bccc38a515192cf86dcf7ba0c0a67
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2018
---
# <a name="introduction-to-cloud-service-monitoring"></a>Presentazione del monitoraggio del servizio cloud

È possibile monitorare le metriche di prestazioni chiave per qualsiasi servizio cloud. Ogni ruolo del servizio cloud raccoglie dati minimi: utilizzo della CPU, utilizzo della rete e utilizzo del disco. Se il servizio cloud ha l'estensione `Microsoft.Azure.Diagnostics` applicata a un ruolo, tale ruolo può raccogliere altri dati. Questo articolo fornisce un'introduzione a Diagnostica di Azure per Servizi cloud.

Con il monitoraggio di base, i dati dei contatori delle prestazioni provenienti dalle istanze del ruolo sono campionati e raccolti a intervalli di 3 minuti. Questi dati di monitoraggio di base non vengono salvati nell'account di archiviazione e non hanno alcun costo aggiuntivo.

Con il monitoraggio avanzato, altre metriche vengono campionate e raccolte a intervalli di 5 minuti, 1 ora e 12 ore. I dati aggregati vengono salvati in un account di archiviazione all'interno di tabelle e vengono eliminati dopo 10 giorni. L'account di archiviazione usato è configurato per ruolo; è possibile usare account di archiviazione diversi per ruoli diversi. Questo è configurato con una stringa di connessione nei file con estensione [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) e [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg).


## <a name="basic-monitoring"></a>Monitoraggio di base

Come indicato nell'introduzione, un servizio cloud raccoglie automaticamente i dati di monitoraggio di base dalla macchina virtuale host. Questi dati includono percentuale della CPU, in/out di rete e lettura/scrittura su disco. I dati di monitoraggio raccolti vengono visualizzati automaticamente nelle pagine di panoramica e metriche del servizio cloud, nel portale di Azure. 

Il monitoraggio di base non richiede un account di archiviazione. 

![Riquadri del monitoraggio di base del servizio cloud](media/cloud-services-how-to-monitor/basic-tiles.png)

## <a name="advanced-monitoring"></a>Monitoraggio avanzato

Il monitoraggio avanzato comporta l'uso dell'estensione **Diagnostica di Azure** (e facoltativamente di Application Insights SDK) sul ruolo che si vuole monitorare. L'estensione di diagnostica usa un file di configurazione (per ruolo) denominato **diagnostics.wadcfgx** per configurare le metriche di diagnostica monitorate. L'estensione Diagnostica di Azure raccoglie e archivia i dati in un account di archiviazione di Azure. Queste impostazioni vengono configurate nei file **.wadcfgx**, [.csdef](cloud-services-model-and-package.md#servicedefinitioncsdef) e [.cscfg](cloud-services-model-and-package.md#serviceconfigurationcscfg). Il monitoraggio avanzato comporta perciò un costo aggiuntivo.

In fase di creazione, Visual Studio aggiunge a ogni ruolo l'estensione Diagnostica di Azure. Questa estensione di diagnostica può raccogliere i tipi di informazioni seguenti:

* Contatori delle prestazioni personalizzati
* Log applicazioni
* Registri eventi di Windows
* Origine degli eventi .NET
* Log di IIS
* ETW basato su manifesto
* Dump di arresto anomalo del sistema
* Log degli errori dei clienti

> [!IMPORTANT]
> Tutti questi dati vengono aggregati nell'account di archiviazione, ma il portale **non** fornisce un modo nativo per rappresentarli graficamente. È consigliabile integrare un altro servizio, come ad esempio Application Insights, nell'applicazione.

## <a name="setup-diagnostics-extension"></a>Configurare l'estensione di Diagnostica

In primo luogo, se non si ha già un account di archiviazione **classico**, [crearne uno](../storage/common/storage-create-storage-account.md#create-a-storage-account). Verificare che l'account di archiviazione sia creato con il **modello di distribuzione classica** specificato.

Quindi passare alla risorsa **Account di archiviazione (classico)**. Selezionare **Impostazioni** > **Chiavi di accesso** e copiare il valore **Stringa di connessione primaria**. Questo valore è necessario per il servizio cloud. 

Per poter abilitare la diagnostica avanzata, è necessario modificare i due file di configurazione seguenti: **ServiceDefinition.csdef** e **ServiceConfiguration.cscfg**.

### <a name="servicedefinitioncsdef"></a>ServiceDefinition.csdef

Nel file **ServiceDefinition.csdef** aggiungere una nuova impostazione denominata `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` per ogni ruolo che usa la diagnostica avanzata. Visual Studio aggiunge questo valore al file quando viene creato un nuovo progetto. Se manca, è possibile aggiungerlo adesso. 

```xml
<ServiceDefinition name="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceDefinition" schemaVersion="2015-04.2.6">
  <WorkerRole name="WorkerRoleWithSBQueue1" vmsize="Small">
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" />
```

Questo definisce una nuova impostazione che deve essere aggiunta a ogni file **ServiceConfiguration.cscfg**. 

Molto probabilmente si avranno due file con estensione **.cscfg**, uno denominato **ServiceConfiguration.cloud.cscfg** per la distribuzione in Azure e uno denominato **ServiceConfiguration.local.cscfg** usato per le distribuzioni locali nell'ambiente emulato. Aprire e modificare ogni file con estensione **.cscfg**. Aggiungere un'impostazione denominata `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`. Impostare il valore sulla **Stringa di connessione primaria** dell'account di archiviazione classico. Se si vuole usare l'archiviazione locale nel computer di sviluppo, usare `UseDevelopmentStorage=true`.

```xml
<ServiceConfiguration serviceName="AnsurCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="4" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="WorkerRoleWithSBQueue1">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=mystorage;AccountKey=KWwkdfmskOIS240jnBOeeXVGHT9QgKS4kIQ3wWVKzOYkfjdsjfkjdsaf+sddfwwfw+sdffsdafda/w==" />
      
      <!-- or use the local development machine for storage
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
      -->
```

## <a name="use-application-insights"></a>Usare Application Insights

Quando si pubblica il servizio cloud da Visual Studio, si può scegliere di inviare i dati di diagnostica ad Application Insights. È possibile creare la risorsa Azure Application Insights in quel momento o inviare i dati a una risorsa Azure esistente. Il servizio cloud può essere monitorato da Application Insights in termini di disponibilità, prestazioni, errori e utilizzo. È possibile aggiungere grafici personalizzati ad Application Insights che mostrino i dati più importanti. I dati delle istanze dei ruoli possono essere raccolti con Application Insights SDK nel progetto del servizio cloud. Per altre informazioni su come integrare Application Insights, vedere [Application Insights per Servizi cloud di Azure](../application-insights/app-insights-cloudservices.md).

Sebbene sia possibile usare Application Insights per visualizzare i contatori delle prestazioni (e le altre impostazioni) specificati tramite l'estensione Diagnostica di Azure, per ottenere un'esperienza più completa è necessario integrare Application Insights SDK nei ruoli di lavoro e Web.


## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su Application Insights con i Servizi cloud](../application-insights/app-insights-cloudservices.md)
- [Set up performance counters](diagnostics-performance-counters.md) (Impostare i contatori delle prestazioni)

