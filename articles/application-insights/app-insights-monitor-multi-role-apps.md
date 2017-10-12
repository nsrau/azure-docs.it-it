---
title: "Supporto di Azure Application Insights per più componenti, microservizi e contenitori | Microsoft Docs"
description: "Monitoraggio di applicazioni costituite da più componenti o ruoli per le prestazioni e l'uso."
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/17/2017
ms.author: bwren
ms.openlocfilehash: ca1bb8ee886c4b4e69be9dd653d6a52b874e1f5a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-multi-component-applications-with-application-insights-preview"></a>Monitorare le applicazioni multi-componente con Application Insights (anteprima)

È possibile monitorare le app che sono costituite da più componenti, ruoli o servizi del server con [Azure Application Insights](app-insights-overview.md). L'integrità dei componenti e le relazioni tra di essi vengono visualizzati in una singola Mappa delle applicazioni. È possibile tracciare le singole operazioni tramite più componenti con correlazione automatica HTTP. È possibile integrare e correlare la diagnostica del contenitore con i dati di telemetria dell'applicazione. Usare una singola risorsa di Application Insights per tutti i componenti dell'applicazione. 

![Mappa dell'applicazione multi-componente](./media/app-insights-monitor-multi-role-apps/app-map.png)

Il termine "componente" usato di seguito indica qualsiasi parte funzionante di un'applicazione di grandi dimensioni. Ad esempio, una tipica applicazione aziendale può essere costituita dal codice client in esecuzione nei browser Web, in comunicazione con uno o più servizi app Web, che a sua volta usano i servizi di back-end. I componenti del server possono essere ospitati in locale nel cloud, possono essere ruoli Web e di lavoro di Azure oppure possono essere eseguiti in contenitori come Docker o Service Fabric. 

### <a name="sharing-a-single-application-insights-resource"></a>Condivisione di una singola risorsa di Application Insights 

La tecnica chiave consiste nell'inviare i dati di telemetria da ogni componente nell'applicazione alla stessa risorsa di Application Insights, usando la proprietà `cloud_RoleName` per distinguere i componenti quando necessario. L'SDK di Application Insights aggiunge la proprietà `cloud_RoleName` all'emissione dei componenti di telemetria. Ad esempio, l'SDK aggiungerà un nome del sito Web o il nome del ruolo di servizio alla proprietà `cloud_RoleName`. È possibile eseguire l'override di questo valore con un Telemetryinitializer. La mappa delle applicazioni usa la proprietà `cloud_RoleName` per identificare i componenti sulla mappa.

Per altre informazioni su come eseguire l'override dell proprietà `cloud_RoleName`, vedere [Aggiungere proprietà: ITelemetryInitializer](app-insights-api-filtering-sampling.md#add-properties-itelemetryinitializer).  

In alcuni casi questo può non essere appropriato e si potrebbe preferire l'uso di risorse separate per i diversi gruppi di componenti. Ad esempio, potrebbe essere necessario usare risorse diverse per la gestione o la fatturazione. Se si usano risorse separate, non sono visibili tutti i componenti visualizzati in una singola Mappa delle applicazioni e non è possibile eseguire query tra i componenti in [Analisi](app-insights-analytics.md). È anche necessario configurare le risorse separate.

Con tale avvertenza, nella parte restante di questo documento si presuppone che si desideri inviare dati da più componenti a una risorsa di Application Insights.

## <a name="configure-multi-component-applications"></a>Configurare le applicazioni multi-componente

Per ottenere una mappa delle applicazioni multi-componente, è necessario raggiungere questi obiettivi:

* **Installare l'ultima versione preliminare** del pacchetto di Application Insights in tutti i componenti dell'applicazione. 
* **Condividere una singola risorsa di Application Insights** per tutti i componenti dell'applicazione.
* **Abilitare la mappa delle applicazioni multi-ruolo** nel pannello Anteprime.

Configurare ogni componente dell'applicazione usando il metodo appropriato per il relativo tipo. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md), [JavaScript](app-insights-javascript.md).)

### <a name="1-install-the-latest-pre-release-package"></a>1. Installare l'ultima versione preliminare del pacchetto

Aggiornare o installare i pacchetti di Application Insights nel progetto per ogni componente del server. Se si usa Visual Studio:

1. Fare clic con il pulsante destro del mouse sul progetto e selezionare **Gestisci pacchetti NuGet**. 
2. Selezionare **Includi versione preliminare**.
3. Se i pacchetti di Application Insights vengono visualizzati negli aggiornamenti, selezionarli. 

    In caso contrario, cercare e installare il pacchetto appropriato:
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric - per componenti in esecuzione come file eseguibili guest e contenitori Docker in esecuzione nell'applicazione Service Fabric
    * Microsoft.ApplicationInsights.ServiceFabric.Native - per reliable services nelle applicazioni di ServiceFabric
    * Microsoft.ApplicationInsights.Kubernetes per i componenti in esecuzione in Docker su Kubernetes

### <a name="2-share-a-single-application-insights-resource"></a>2. Condividere una singola risorsa di Application Insights

* In Visual Studio fare clic con il pulsante destro del mouse sul progetto e selezionare **Configura Application Insights** oppure **Application Insights > Configurare**. Per il primo progetto, usare la procedura guidata per creare una risorsa di Application Insights. Per i progetti successivi, selezionare la stessa risorsa.
* Se non è presente alcun menu di Application Insights, configurare manualmente:

   1. Nel [portale di Azure](https://portal,azure.com) aprire la risorsa di Application Insights già creata per un altro componente.
   2. Nel pannello Panoramica aprire l'elenco a discesa Informazioni di base e copiare la **chiave di strumentazione.**
   3. Nel progetto aprire ApplicationInsights.config e inserire:`<InstrumentationKey>your copied key</InstrumentationKey>`

![Copiare la chiave di strumentazione nel file .config](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-multi-role-application-map"></a>3. Abilitare la Mappa delle applicazioni multiruolo

Nel portale di Azure aprire la risorsa per l'applicazione. Nel pannello Anteprime abilitare *Mappa delle applicazioni multiruolo*.

### <a name="4-enable-docker-metrics-optional"></a>4. Abilitare la metrica di Docker (facoltativo) 

Se un componente viene eseguito in Docker ospitato in una macchina virtuale Windows di Azure, è possibile raccogliere metriche aggiuntive dal contenitore. Inserire nel file di configurazione di [Diagnostica di Azure](../monitoring-and-diagnostics/azure-diagnostics.md):

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

## <a name="use-cloudrolename-to-separate-components"></a>Usare cloud_RoleName per separare i componenti

La proprietà `cloud_RoleName` è collegata a tutti i dati di telemetria. Identifica il componente, il ruolo o il servizio che origina i dati di telemetria. (Non è uguale a cloud_RoleInstance, che separa ruoli identici in esecuzione in parallelo su più processi server o computer.)

Nel portale è possibile filtrare o segmentare i dati di telemetria tramite questa proprietà. In questo esempio il pannello Errori viene filtrato per mostrare solo le informazioni dal servizio Web front-end, escludendo gli errori di back-end dell'API di CRM:

![Grafico della metrica segmentata in base al nome del ruolo Cloud](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-components"></a>Tracciare le operazioni tra i componenti

È possibile tracciare le chiamate da un servizio a un altro eseguite durante l'elaborazione di una singola operazione.


![Mostrare i dati di telemetria per operazione](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Fare clic per visualizzare un elenco correlato di dati di telemetria per questa operazione tra il server Web front-end e l'API di back-end:

![Ricerca tra i componenti](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>Passaggi successivi

* [Separare la telemetria da sviluppo, test e produzione](app-insights-separate-resources.md)
