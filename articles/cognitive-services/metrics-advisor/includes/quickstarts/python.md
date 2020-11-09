---
title: Guida di avvio rapido di Advisor metriche per Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 10/14/2020
ms.author: mbullwin
ms.openlocfilehash: cf4404bbfe7e8f0ad664c9ca8dda07ff61be12d9
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186941"
---
[Documentazione di riferimento](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html) | [Codice sorgente della libreria](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/README.md) | [Pacchetto (PiPy)](https://pypi.org/project/azure-ai-metricsadvisor/) | [Esempi](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples/README.md)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [creare un account gratuito](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Una volta che si dispone di una sottoscrizione di Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Creare una risorsa di Advisor metriche"  target="_blank">creare una risorsa di Advisor metriche <span class="docon docon-navigate-external x-hidden-focus"></span></a> nel portale di Azure per distribuire l'istanza di Advisor metriche.  
* Un database SQL con dati di serie temporali.
  
> [!TIP]
> * È possibile trovare esempi di Advisor metriche per Python in [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples).
> * La risorsa di Advisor metriche può impiegare dai 10 ai 30 minuti per distribuire un'istanza del servizio da usare. Al termine della distribuzione, fare clic su **Vai alla risorsa**. Dopo la distribuzione è possibile iniziare a usare l'istanza di Advisor metriche sia con il portale Web che con l'API REST.
> * È possibile trovare l'URL dell'API REST nella sezione **Panoramica** della risorsa nel portale di Azure. simile al seguente:
>    * `https://<instance-name>.cognitiveservices.azure.com/` 
 
## <a name="setting-up"></a>Configurazione

### <a name="install-the-client-library"></a>Installare la libreria client

Dopo l'installazione di Python, è possibile installare la libreria client con:

```console
pip install azure-ai-metricsadvisor --pre
```

### <a name="create-a-new-python-application"></a>Creare una nuova applicazione Python

Creare un nuovo file Python e importare le librerie seguenti.

```python
import os
import datetime
```

Creare le variabili per l'endpoint e la chiave di Azure della risorsa.

> [!IMPORTANT]
> Accedere al portale di Azure. Se la risorsa di Advisor metriche creata nella sezione **Prerequisiti** è stata distribuita correttamente, fare clic sul pulsante **Vai alla risorsa** in **Passaggi successivi**. Le chiavi di sottoscrizione e l'endpoint saranno disponibili nella pagina **Chiavi ed endpoint** della risorsa in **Gestione risorse**. <br><br>Per recuperare la chiave API, è necessario passare a [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net). Selezionare i valori di **Directory** , **Sottoscrizioni** e **Area di lavoro** appropriati per la risorsa, quindi scegliere **Attività iniziali**. Sarà quindi possibile recuperare le chiavi API da [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key).   
>
> Al termine, ricordarsi di rimuovere la chiave dal codice e non renderlo mai pubblico. Per la produzione, è consigliabile usare un modo sicuro per archiviare e accedere alle credenziali, Per altre informazioni, vedere l'articolo sulla [sicurezza](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) di Servizi cognitivi.

```python
subscription_key = "<paste-your-metrics-advisor-subscription-key-here>"
api_key = "<paste-your-metrics-advisor-api-key-here>"
service_endpoint = "<paste-your-metrics-advisor-endpoint-here>"
```

## <a name="object-model"></a>Modello a oggetti

Le classi seguenti gestiscono alcune delle principali funzionalità di Metrics Advisor Python SDK.

|Nome|Descrizione|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient) | **Si usa pe** : <br> - Elencare gli eventi imprevisti <br> - Elencare la causa radice degli eventi imprevisti <br> - Recuperare i dati delle serie temporali originali e quelli arricchiti dal servizio. <br> - Elencare gli avvisi <br> - Aggiungere feedback per ottimizzare il modello |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html?highlight=metricsadvisoradministrationclient#azure.ai.metricsadvisor.MetricsAdvisorAdministrationClient)| **Consente di:** <br> - Gestire i feed di dati <br> - Creare, configurare, recuperare, elencare ed eliminare le configurazioni di rilevamento anomalie <br> - Creare, configurare, recuperare, elencare ed eliminare le configurazioni degli avvisi per le anomalie <br> - Gestire gli hook  | |
| [DataFeed](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeed#azure.ai.metricsadvisor.models.DataFeed)| **Quali dati inserisce Advisor metriche dall'origine dati. `DataFeed` contiene righe di:** <br> - Timestamp <br> - Zero o più dimensioni <br> - Una o più misure  |
| [Metrica](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=metric#azure.ai.metricsadvisor.models.Metric) | Per `Metric` si intende una misura quantificabile usata per monitorare e valutare lo stato di un processo aziendale specifico. Può essere una combinazione di più valori di serie temporali divisi in dimensioni. Una metrica di integrità Web può ad esempio contenere dimensioni relative al numero di utenti e al mercato statunitense. |

## <a name="code-examples"></a>Esempi di codice

Questi frammenti di codice mostrano come eseguire le operazioni seguenti con la libreria client di Advisor metriche per Python:

* [Autenticare il client](#authenticate-the-client)
* [Aggiungere un feed di dati](#add-a-data-feed)
* [Controllare lo stato dell'inserimento](#check-the-ingestion-status)
* [Impostare la configurazione del rilevamento e quella degli avvisi](#configure-anomaly-detection)
* [Creare una configurazione degli avvisi](#create-an-alert-configuration)
* [Eseguire query sui risultati del rilevamento anomalie](#query-the-alert)

## <a name="authenticate-the-client"></a>Autenticare il client

Il client in questo esempio è costituito da un oggetto `MetricsAdvisorAdministrationClient` che usa l'endpoint e da un oggetto `MetricsAdvisorKeyCredential` che contiene le chiavi. Non è necessario copiare questo esempio di codice. I metodi creati in seguito creeranno un'istanza del client. Il client alternativo è denominato `MetricsAdvisorClient`. Per altre informazioni su questo client, vedere la [documentazione di riferimento](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient).

```python
client = MetricsAdvisorAdministrationClient(service_endpoint,
                                MetricsAdvisorKeyCredential(subscription_key, api_key))
```

## <a name="add-a-data-feed"></a>Aggiungere un feed di dati

In un nuovo metodo creare istruzioni import come nell'esempio seguente. Sostituire `sql_server_connection_string` con la stringa di connessione del server SQL e `query` con una query che restituisce i dati in corrispondenza di un singolo timestamp. Sarà anche necessario modificare i valori di `metric` e `dimension` in base ai dati personalizzati.

> [!IMPORTANT]
> La query deve restituire al massimo un record per ogni combinazione di dimensioni, in corrispondenza di ogni timestamp. Tutti i record restituiti dalla query devono avere inoltre gli stessi timestamp. Advisor metriche eseguirà questa query per ogni timestamp per inserire i dati. Per altre informazioni ed esempi, vedere la [sezione delle domande frequenti sulle query](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data). 

Creare un client con le chiavi e l'endpoint, quindi usare `client.create_data_feed()` per configurare il nome, l'origine, la granularità e lo schema. È anche possibile impostare la data/ora di inserimento, le impostazioni di rollup e altro ancora.


```python
def sample_create_data_feed():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        SQLServerDataFeed,
        DataFeedSchema,
        Metric,
        Dimension,
        DataFeedOptions,
        DataFeedRollupSettings,
        DataFeedMissingDataPointFillSettings
    )
    sql_server_connection_string = "<replace-with-your-sql-server-connection-string>"
    query = "<replace-with-metrics-advisor-sql-server-query>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    data_feed = client.create_data_feed(
        name="My data feed",
        source=SQLServerDataFeed(
            connection_string=sql_server_connection_string,
            query=query,
        ),
        granularity="Daily",
        schema=DataFeedSchema(
            metrics=[
                Metric(name="cost", display_name="Cost"),
                Metric(name="revenue", display_name="Revenue")
            ],
            dimensions=[
                Dimension(name="category", display_name="Category"),
                Dimension(name="city", display_name="City")
            ],
            timestamp_column="Timestamp"
        ),
        ingestion_settings=datetime.datetime(2019, 10, 1),
        options=DataFeedOptions(
            data_feed_description="cost/revenue data feed",
            rollup_settings=DataFeedRollupSettings(
                rollup_type="AutoRollup",
                rollup_method="Sum",
                rollup_identification_value="__CUSTOM_SUM__"
            ),
            missing_data_point_fill_settings=DataFeedMissingDataPointFillSettings(
                fill_type="SmartFilling"
            ),
            access_mode="Private"
        )
    )

    return data_feed
sample_create_data_feed()
```

## <a name="check-the-ingestion-status"></a>Controllare lo stato dell'inserimento

In un nuovo metodo creare un'istruzione import come nell'esempio seguente. Sostituire `data_feed_id` con l'ID del feed di dati creato. Creare un client con le chiavi e l'endpoint e usare `client.get_data_feed_ingestion_progress()` per ottenere lo stato di inserimento. Stampare i dettagli, ad esempio l'ultimo timestamp attivo e riuscito.


```python
def sample_get_data_feed_ingestion_progress():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient

    data_feed_id = "<replace-with-your-metrics-advisor-data-feed-id>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    progress = client.get_data_feed_ingestion_progress(data_feed_id)

    print("Latest active timestamp: {}".format(progress.latest_active_timestamp))
    print("Latest successful timestamp: {}".format(progress.latest_success_timestamp))
sample_get_data_feed_ingestion_progress()
```

## <a name="configure-anomaly-detection"></a>Configurare il rilevamento anomalie

In un nuovo metodo creare istruzioni import come nell'esempio seguente. Sostituire `metric_id` con l'ID della metrica da configurare. Creare un client con le chiavi e l'endpoint e usare `client.create_metric_anomaly_detection_configuration` per creare una nuova configurazione di rilevamento. Le condizioni di soglia specificano i parametri per il rilevamento anomalie.

```python
def sample_create_detection_config():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        ChangeThresholdCondition,
        HardThresholdCondition,
        SmartDetectionCondition,
        SuppressCondition,
        MetricDetectionCondition,
    )
    metric_id = "replace-with-your-metric-id"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    change_threshold_condition = ChangeThresholdCondition(
        anomaly_detector_direction="Both",
        change_percentage=20,
        shift_point=10,
        within_range=True,
        suppress_condition=SuppressCondition(
            min_number=5,
            min_ratio=2
        )
    )
    hard_threshold_condition = HardThresholdCondition(
        anomaly_detector_direction="Up",
        upper_bound=100,
        suppress_condition=SuppressCondition(
            min_number=2,
            min_ratio=2
        )
    )
    smart_detection_condition = SmartDetectionCondition(
        anomaly_detector_direction="Up",
        sensitivity=10,
        suppress_condition=SuppressCondition(
            min_number=2,
            min_ratio=2
        )
    )

    detection_config = client.create_metric_anomaly_detection_configuration(
        name="my_detection_config",
        metric_id=metric_id,
        description="anomaly detection config for metric",
        whole_series_detection_condition=MetricDetectionCondition(
            cross_conditions_operator="OR",
            change_threshold_condition=change_threshold_condition,
            hard_threshold_condition=hard_threshold_condition,
            smart_detection_condition=smart_detection_condition
        )
    )

    return detection_config
sample_create_detection_config()
```


## <a name="create-a-hook"></a>Creare un hook

In un nuovo metodo creare istruzioni import come nell'esempio seguente. Creare un client con le chiavi e l'endpoint e usare `client.create_hook()` per creare un hook. Immettere una descrizione, un elenco di messaggi di posta elettronica a cui inviare l'avviso e un collegamento esterno per la ricezione dell'avviso.  

```python
def sample_create_hook():

    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import EmailHook

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    hook = client.create_hook(
        name="email hook",
        hook=EmailHook(
            description="my email hook",
            emails_to_alert=["alertme@contoso.com"],
            external_link="https://adwiki.azurewebsites.net/articles/howto/alerts/create-hooks.html"
        )
    )

    return hook
sample_create_hook()
```

##  <a name="create-an-alert-configuration"></a>Creare una configurazione degli avvisi

In un nuovo metodo creare istruzioni import come nell'esempio seguente. Sostituire `anomaly_detection_configuration_id` con l'ID della configurazione del rilevamento anomalie e `hook_id` con l'hook creato in precedenza. Creare un client con le chiavi e l'endpoint e usare `client.create_anomaly_alert_configuration()` per creare una nuova configurazione degli avvisi. `metric_alert_configurations` è elenco di oggetti `MetricAlertConfiguration` che specificano le condizioni e l'ambito per ogni configurazione.

```python
def sample_create_alert_config():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        MetricAlertConfiguration,
        MetricAnomalyAlertScope,
        TopNGroupScope,
        MetricAnomalyAlertConditions,
        SeverityCondition,
        MetricBoundaryCondition,
        MetricAnomalyAlertSnoozeCondition
    )
    anomaly_detection_configuration_id = "<replace-with-your-detection-configuration-id"
    hook_id = "<replace-with-your-hook-id>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    alert_config = client.create_anomaly_alert_configuration(
        name="my alert config",
        description="alert config description",
        cross_metrics_operator="AND",
        metric_alert_configurations=[
            MetricAlertConfiguration(
                detection_configuration_id=anomaly_detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="WholeSeries"
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    severity_condition=SeverityCondition(
                        min_alert_severity="Low",
                        max_alert_severity="High"
                    )
                )
            ),
            MetricAlertConfiguration(
                detection_configuration_id=anomaly_detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="TopN",
                    top_n_group_in_scope=TopNGroupScope(
                        top=10,
                        period=5,
                        min_top_count=5
                    )
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    metric_boundary_condition=MetricBoundaryCondition(
                        direction="Up",
                        upper=50
                    )
                ),
                alert_snooze_condition=MetricAnomalyAlertSnoozeCondition(
                    auto_snooze=2,
                    snooze_scope="Metric",
                    only_for_successive=True
                )
            ),
        ],
        hook_ids=[hook_id]
    )

    return alert_config
```

### <a name="query-the-alert"></a>Eseguire query sull'avviso

In un nuovo metodo creare un'istruzione import come nell'esempio seguente. Sostituire `alert_id` con l'ID dell'avviso e `alert_config_id` con l'ID configurazione degli avvisi. Creare un client con le chiavi e l'endpoint e usare `client.list_anomalies_for_alert()` per creare una nuova configurazione degli avvisi. 

```python
def sample_list_anomalies_for_alert(alert_config_id, alert_id):

    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorClient
    
    alert_id = "<replace-with-your-alert-id>"
    alert_config_id = "<replace-with-your-alert-configuration-id"
    client = MetricsAdvisorClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    results = client.list_anomalies_for_alert(
            alert_configuration_id=alert_config_id,
            alert_id=alert_id,
        )
    for result in results:
        print("Create on: {}".format(result.created_on))
        print("Severity: {}".format(result.severity))
        print("Status: {}".format(result.status))
```

### <a name="run-the-application"></a>Eseguire l'applicazione

Eseguire l'applicazione con il comando `python` nel file quickstart.

```console
python quickstart-file.py
```