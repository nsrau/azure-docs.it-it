---
title: "Supporto di Azure Application Insights per più ruoli, microservizi e contenitori | Microsoft Docs"
description: "Monitoraggio di applicazioni costituite da più componenti o ruoli per le prestazioni e l&quot;uso."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: dad17277452081427a01d077128a3a137b2190f5
ms.contentlocale: it-it
ms.lasthandoff: 05/17/2017


---
# <a name="monitor-multi-role-applications-with-application-insights-preview"></a>Monitorare le applicazioni multi-ruolo con Application Insights (anteprima)

È possibile monitorare le applicazioni che sono costituite da più componenti, ruoli o servizi con [Azure Application Insights](app-insights-overview.md). L'integrità dei ruoli e le relazioni tra di essi vengono visualizzate in una singola Mappa delle applicazioni. È possibile tracciare le singole operazioni tramite più ruoli con correlazione automatica HTTP. È possibile integrare e correlare la diagnostica del contenitore con i dati di telemetria dell'applicazione. Usare una singola risorsa di Application Insights per tutti i ruoli dell'applicazione. 

![Mappa delle applicazioni multi-ruolo](./media/app-insights-monitor-multi-role-apps/app-map.png)

Il termine 'ruolo' è qui usato in senso ampio per indicare qualsiasi componente dell'applicazione o servizio che viene creato come progetto separato. Ad esempio, una tipica applicazione aziendale può comprendere più ruoli che comunicano tramite un'API REST. I ruoli possono essere ospitati in contenitori come Docker o Service Fabric, in host cloud o in locale. 

### <a name="sharing-a-single-application-insights-resource"></a>Condivisione di una singola risorsa di Application Insights 

La tecnica chiave consiste nell'inviare i dati di telemetria da ogni ruolo nell'applicazione alla stessa risorsa di Application Insights, usando la proprietà `cloud_RoleName` per distinguere i ruoli quando necessario. 

In alcuni casi questo può non essere appropriato e si potrebbe preferire l'uso di risorse separate per i diversi gruppi di ruoli. Ad esempio, potrebbe essere necessario usare risorse diverse per la gestione o la fatturazione. Se si usano risorse separate, non sono visibili tutti i ruoli visualizzati in una singola Mappa delle applicazioni e non è possibile eseguire query tra i ruoli in [Analisi](app-insights-analytics.md). È anche necessario configurare le risorse separate.

Con tale avvertenza, nella parte restante di questo documento si presuppone che si desideri inviare dati da più ruoli a una risorsa di Application Insights.

## <a name="configure-multi-role-applications"></a>Configurare le applicazioni multiruolo

Per ottenere una mappa delle applicazioni multi-ruolo, è necessario raggiungere questi obiettivi:

* **Installare l'ultima versione preliminare** del pacchetto di Application Insights in tutti i ruoli dell'applicazione. 
* **Condividere una singola risorsa di Application Insights** per tutti i ruoli dell'applicazione.
* **Abilitare la mappa delle applicazioni multi-ruolo** nel pannello Anteprime.

Configurare ogni ruolo dell'applicazione usando il metodo appropriato per il relativo tipo. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md).)

### <a name="1-install-the-latest-pre-release-package"></a>1. Installare l'ultima versione preliminare del pacchetto

Aggiornare o installare i pacchetti di Application Insights nel progetto per ogni ruolo. Se si usa Visual Studio:

1. Fare clic con il pulsante destro del mouse sul progetto e selezionare **Gestisci pacchetti NuGet**. 
2. Selezionare **Includi versione preliminare**.
3. Se i pacchetti di Application Insights vengono visualizzati negli aggiornamenti, selezionarli. 

    In caso contrario, cercare e installare il pacchetto appropriato:
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric - per ruoli in esecuzione come eseguibili guest e per contenitori Docker in esecuzione nell'applicazione ServiceFabric
    * Microsoft.ApplicationInsights.ServiceFabric.Native - per reliable services nelle applicazioni di ServiceFabric
    * Microsoft.ApplicationInsights.Kubernetes per i ruoli in esecuzione in Docker su Kubernetes

### <a name="2-share-a-single-application-insights-resource"></a>2. Condividere una singola risorsa di Application Insights

* In Visual Studio fare clic con il pulsante destro del mouse sul progetto e selezionare **Configura Application Insights** oppure **Application Insights > Configurare**. Per il primo progetto, usare la procedura guidata per creare una risorsa di Application Insights. Per i progetti successivi, selezionare la stessa risorsa.
* Se non è presente alcun menu di Application Insights, configurare manualmente:

   1. Nel [portale di Azure](https://portal,azure.com) aprire la risorsa di Application Insights già creata per un altro ruolo.
   2. Nel pannello Panoramica aprire l'elenco a discesa Informazioni di base e copiare la **chiave di strumentazione.**
   3. Nel progetto aprire ApplicationInsights.config e inserire:`<InstrumentationKey>your copied key</InstrumentationKey>`

![Copiare la chiave di strumentazione nel file .config](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-multi-role-application-map"></a>3. Abilitare la Mappa delle applicazioni multiruolo

Nel portale di Azure aprire la risorsa per l'applicazione. Nel pannello Anteprime abilitare *Mappa delle applicazioni multiruolo*.

### <a name="4-enable-docker-metrics-optional"></a>4. Abilitare la metrica di Docker (facoltativo) 

Se un ruolo viene eseguito in Docker ospitato in una macchina virtuale Windows di Azure, è possibile raccogliere metriche aggiuntive dal contenitore. Inserire nel file di configurazione di [Diagnostica di Azure](../monitoring-and-diagnostics/azure-diagnostics.md):

```
"DiagnosticMonitorConfiguration": {
        ...
        "sinks": "applicationInsights",
        "DockerSources": {
                "Stats": {
                    "enabled": true,
                    "sampleRate": "PT1M"
                }
            },
        ...
    }
    ...   
    "SinksConfig": {
        "Sink": [{
            "name": "applicationInsights",
            "ApplicationInsights": "<your instrumentation key here>"
        }]
    }
    ...
}

```

## <a name="use-cloudrolename-to-separate-roles"></a>Usare cloud_RoleName per separare i ruoli

La proprietà `cloud_RoleName` è collegata a tutti i dati di telemetria. Identifica il ruolo o il servizio che origina i dati di telemetria. (Non è uguale a cloud_RoleInstance, che separa ruoli identici in esecuzione in parallelo su più processi server o computer.)

Nel portale è possibile filtrare o segmentare i dati di telemetria tramite questa proprietà. In questo esempio il pannello Errori viene filtrato per mostrare solo le informazioni dal servizio Web front-end, escludendo gli errori di back-end dell'API di CRM:

![Grafico della metrica segmentata in base al nome del ruolo Cloud](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-roles"></a>Tracciare le operazioni tra ruoli

È possibile tracciare le chiamate da un servizio a un altro eseguite durante l'elaborazione di una singola operazione.


![Mostrare i dati di telemetria per operazione](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Fare clic per visualizzare un elenco correlato di dati di telemetria per questa operazione tra il server Web front-end e l'API di back-end:

![Ricerca tra i ruoli](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>Passaggi successivi

* [Separare la telemetria da sviluppo, test e produzione](app-insights-separate-resources.md)

