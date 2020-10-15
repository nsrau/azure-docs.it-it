---
title: Monitorare le data factory con Monitoraggio di Azure
description: Informazioni su come usare monitoraggio di Azure per monitorare le pipeline di/Azure Data Factory abilitando i log di diagnostica con le informazioni provenienti da Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/13/2020
ms.openlocfilehash: 6f0e688f3d483536e0d82186dd8e498cdadf97da
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87563552"
---
# <a name="monitor-and-alert-data-factory-by-using-azure-monitor"></a>Monitorare e inviare avvisi Data Factory tramite monitoraggio di Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Le applicazioni cloud sono complesse e hanno molte parti mobili. I monitoraggi forniscono i dati per garantire che le applicazioni restino operativi in uno stato integro. I monitoraggi consentono inoltre di evitare potenziali problemi e risolvere i problemi precedenti. È possibile usare i dati di monitoraggio per ottenere informazioni approfondite sulle applicazioni. Queste informazioni consentono di migliorare le prestazioni e la gestibilità delle applicazioni. Consente inoltre di automatizzare le azioni che altrimenti richiedono un intervento manuale.

Monitoraggio di Azure offre metriche e log dell'infrastruttura di livello base per la maggior parte dei servizi di Azure. I log di diagnostica di Azure sono generati da una risorsa e offrono dati completi e frequenti sul funzionamento della risorsa stessa. Azure Data Factory (ADF) può scrivere log di diagnostica in monitoraggio di Azure. Per un'introduzione di sette minuti e una dimostrazione di questa funzionalità, guardare il video seguente:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

Per altre informazioni, vedere [Panoramica di Monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor).

## <a name="keeping-azure-data-factory-metrics-and-pipeline-run-data"></a>Conservazione Azure Data Factory metriche e dati di esecuzione della pipeline

Data Factory archivia i dati di esecuzione della pipeline solo per 45 giorni. Usare monitoraggio di Azure se si vuole che i dati vengano mantenuti per un periodo di tempo più lungo. Con monitoraggio è possibile indirizzare i log di diagnostica per l'analisi a più destinazioni diverse.

* **Account di archiviazione**: salvare i log di diagnostica in un account di archiviazione per il controllo o l'ispezione manuale. È possibile usare le impostazioni di diagnostica per specificare il periodo di conservazione in giorni.
* **Hub eventi**: trasmettere i log a hub eventi di Azure. I log vengono inseriti in una soluzione di analisi personalizzata o del servizio partner come Power BI.
* **Log Analytics**: analizzare i log con log Analytics. L'integrazione di Data Factory con monitoraggio di Azure è utile negli scenari seguenti:
  * Si desidera scrivere query complesse su un set completo di metriche pubblicate da Data Factory per il monitoraggio. È possibile creare avvisi personalizzati per queste query tramite monitoraggio.
  * Per eseguire il monitoraggio nelle data factory È possibile indirizzare i dati da più data factory a una singola area di lavoro di monitoraggio.

È anche possibile usare un account di archiviazione o uno spazio dei nomi dell'hub eventi che non si trovi nella sottoscrizione della risorsa che crea i log. L'utente che configura l'impostazione deve disporre dell'accesso di controllo degli accessi in base al ruolo (RBAC) appropriato a entrambe le sottoscrizioni.

## <a name="configure-diagnostic-settings-and-workspace"></a>Configurare le impostazioni di diagnostica e l'area di lavoro

Creare o aggiungere impostazioni di diagnostica per il data factory.

1. Nel portale passare a monitoraggio. Selezionare **Impostazioni**  >  **impostazioni di diagnostica**.

1. Selezionare il data factory per cui si desidera impostare un'impostazione di diagnostica.

1. Se nella data factory selezionata non sono presenti impostazioni, viene richiesto di creare un'impostazione. Selezionare **Attiva diagnostica**.

   ![Crea un'impostazione di diagnostica se non esistono impostazioni](media/data-factory-monitor-oms/monitor-oms-image1.png)

   Se nel data factory sono presenti impostazioni esistenti, viene visualizzato un elenco di impostazioni già configurate nel data factory. Selezionare **Aggiungi impostazioni di diagnostica**.

   ![Aggiungere un'impostazione di diagnostica se esistono impostazioni](media/data-factory-monitor-oms/add-diagnostic-setting.png)

1. Assegnare un nome all'impostazione, selezionare **Invia a log Analytics**e quindi selezionare un'area di lavoro da **log Analytics area di lavoro**.

    * In modalità _diagnostica di Azure_ , i log di diagnostica fluiscono nella tabella _AzureDiagnostics_ .

    * In modalità _specifica della risorsa_ , i log di diagnostica da Azure Data Factory scorrono nelle tabelle seguenti:
      - _ADFActivityRun_
      - _ADFPipelineRun_
      - _ADFTriggerRun_
      - _ADFSSISIntegrationRuntimeLogs_
      - _ADFSSISPackageEventMessageContext_
      - _ADFSSISPackageEventMessages_
      - _ADFSSISPackageExecutableStatistics_
      - _ADFSSISPackageExecutionComponentPhases_
      - _ADFSSISPackageExecutionDataStatistics_

      È possibile selezionare diversi log rilevanti per i carichi di lavoro da inviare a Log Analytics tabelle. Se, ad esempio, non si usa SQL Server Integration Services (SSIS), non è necessario selezionare alcun log SSIS. Se si desidera registrare le operazioni di avvio/arresto/manutenzione di SSIS Integration Runtime (IR), è possibile selezionare log IR SSIS. Se si richiamano le esecuzioni di pacchetti SSIS tramite T-SQL in SQL Server Management Studio (SSMS), SQL Server Agent o altri strumenti designati, è possibile selezionare i log dei pacchetti SSIS. Se si richiamano le esecuzioni di pacchetti SSIS tramite Esegui attività del pacchetto SSIS nelle pipeline di ADF, è possibile selezionare tutti i log.

    * Se si seleziona _AllMetrics_, verranno rese disponibili diverse metriche di ADF per il monitoraggio o la generazione di avvisi, incluse le metriche per l'attività, la pipeline e le esecuzioni di trigger di ADF, nonché per le operazioni IR SSIS e le esecuzioni di pacchetti SSIS.

   ![Denominare le impostazioni e selezionare un'area di lavoro log-Analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

    > [!NOTE]
    > Poiché una tabella dei log di Azure non può contenere più di 500 colonne, è **consigliabile** selezionare la _modalità specifica della risorsa_. Per ulteriori informazioni, vedere [log Analytics limitazioni note](../azure-monitor/platform/resource-logs-collect-workspace.md#column-limit-in-azurediagnostics).

1. Selezionare **Salva**.

Dopo qualche istante, la nuova impostazione viene visualizzata nell'elenco delle impostazioni per questa data factory. I log di diagnostica vengono trasmessi a tale area di lavoro non appena vengono generati nuovi dati degli eventi. Un massimo di 15 minuti può trascorrere tra il momento in cui viene generato un evento e il momento in cui viene visualizzato in Log Analytics.

## <a name="install-azure-data-factory-analytics-solution-from-azure-marketplace"></a>Installare la soluzione Azure Data Factory Analytics da Azure Marketplace

Questa soluzione fornisce un riepilogo dello stato generale del Data Factory, con le opzioni per esaminare i dettagli e per risolvere i problemi relativi ai modelli di comportamento imprevisti. Grazie alle funzionalità avanzate, è possibile ottenere informazioni dettagliate sull'elaborazione delle chiavi, tra cui:

* Riepilogo rapido delle esecuzioni di pipeline, attività e trigger data factory
* Possibilità di analizzare data factory esecuzioni di attività in base al tipo
* Riepilogo della pipeline data factory top, errori di attività

1. Passare ad **Azure Marketplace**, scegliere filtro di **analisi** e cercare **Azure Data Factory Analytics (anteprima)**

   ![Passare a "Azure Marketplace", immettere "filtro Analytics" e selezionare "Azure Data Factory Analytics (anteprima")](media/data-factory-monitor-oms/monitor-oms-image3.png)

1. Dettagli su **Azure Data Factory Analytics (anteprima)**

   ![Dettagli su "Azure Data Factory Analytics (anteprima)"](media/data-factory-monitor-oms/monitor-oms-image4.png)

1. Selezionare **Crea** e quindi creare o selezionare l' **area di lavoro log Analytics**.

   ![Creazione di una nuova soluzione](media/data-factory-monitor-oms/monitor-log-analytics-image-5.png)

### <a name="monitor-data-factory-metrics"></a>Monitorare Data Factory metriche

Con l'installazione di questa soluzione viene creato un set predefinito di viste all'interno della sezione cartelle di lavoro dell'area di lavoro Log Analytics scelta. Di conseguenza, vengono abilitate le metriche seguenti:

* Esecuzioni di ADF-1) esecuzioni pipeline per Data Factory
* Esecuzioni di ADF-2) esecuzioni attività per Data Factory
* Esecuzioni di ADF-3) il trigger viene eseguito da Data Factory
* Errori di ADF-1) primi 10 errori della pipeline per Data Factory
* Errori di ADF-2) prime 10 esecuzioni attività per Data Factory
* Errori di ADF-3) primi 10 errori trigger per Data Factory
* Statistiche ADF-1) esecuzioni attività per tipo
* Statistiche ADF-2) esecuzioni trigger per tipo
* Statistiche ADF-3) durata massima esecuzioni pipeline

![Finestra con "cartelle di lavoro (anteprima)" e "AzureDataFactoryAnalytics" evidenziato](media/data-factory-monitor-oms/monitor-oms-image6.png)

È possibile visualizzare le metriche precedenti, esaminare le query dietro a queste metriche, modificare le query, creare avvisi e intraprendere altre azioni.

![Rappresentazione grafica delle esecuzioni di pipeline per data factory "](media/data-factory-monitor-oms/monitor-oms-image8.png)

> [!NOTE]
> Azure Data Factory Analytics (anteprima) Invia i log di diagnostica alle tabelle di destinazione _specifiche delle risorse_ . È possibile scrivere query sulle tabelle seguenti: _ADFPipelineRun_, _ADFTriggerRun_e _ADFActivityRun_.

## <a name="data-factory-metrics"></a>Metriche di Data Factory

Con il monitoraggio è possibile ottenere visibilità sulle prestazioni e l'integrità dei carichi di lavoro di Azure. Il tipo più importante di dati di monitoraggio è la metrica, definita anche contatore delle prestazioni. Le metriche vengono emesse dalla maggior parte delle risorse di Azure. Il monitoraggio offre diversi modi per configurare e utilizzare queste metriche per il monitoraggio e la risoluzione dei problemi.

Di seguito sono riportate alcune delle metriche emesse da Azure Data Factory versione 2:

| **Metrica**                           | **Nome visualizzato metrica**                  | **Unità** | **Tipo di aggregazione** | **Descrizione**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | Cancelled activity runs metrics (Metrica esecuzioni attività annullate)           | Conteggio    | Totale                | Numero totale di esecuzioni di attività annullate in un intervallo di minuti. |
| ActivityFailedRuns                   | Failed activity runs metrics (Metrica esecuzioni attività non riuscite)             | Conteggio    | Totale                | Numero totale di esecuzioni di attività non riuscite in un intervallo di minuti. |
| ActivitySucceededRuns                | Succeeded activity runs metrics (Metrica esecuzioni attività riuscite)          | Conteggio    | Totale                | Numero totale di esecuzioni di attività riuscite in un intervallo di minuti. |
| PipelineCancelledRuns                 | Cancelled pipeline runs metrics (Metrica esecuzioni pipeline annullate)           | Conteggio    | Totale                | Numero totale di esecuzioni di pipeline annullate entro un intervallo di minuti. |
| PipelineFailedRuns                   | Failed pipeline runs metrics (Metrica esecuzioni pipeline non riuscite)             | Conteggio    | Totale                | Numero totale di esecuzioni di pipeline non riuscite in un intervallo di minuti. |
| PipelineSucceededRuns                | Succeeded pipeline runs metrics (Metrica esecuzioni pipeline riuscite)          | Conteggio    | Totale                | Numero totale di esecuzioni di pipeline completate in un intervallo di minuti. |
| TriggerCancelledRuns                  | Cancelled trigger runs metrics (Metrica esecuzioni trigger annullate)            | Conteggio    | Totale                | Numero totale di esecuzioni di trigger annullate in un intervallo di minuti. |
| TriggerFailedRuns                    | Failed trigger runs metrics (Metrica esecuzioni trigger non riuscite)              | Conteggio    | Totale                | Numero totale di esecuzioni di trigger non riuscite in un intervallo di minuti. |
| TriggerSucceededRuns                 | Succeeded trigger runs metrics (Metrica esecuzioni trigger riuscite)           | Conteggio    | Totale                | Numero totale di esecuzioni di trigger riuscite in un intervallo di minuti. |
| SSISIntegrationRuntimeStartCancelled  | Metriche di avvio del runtime di integrazione SSIS annullate           | Conteggio    | Totale                | Viene avviato il totale del runtime di integrazione SSIS che è stato annullato entro un intervallo di minuti. |
| SSISIntegrationRuntimeStartFailed    | Metriche di avvio del runtime di integrazione SSIS non riuscite             | Conteggio    | Totale                | Il numero totale di runtime di integrazione SSIS è stato avviato in un intervallo di minuti. |
| SSISIntegrationRuntimeStartSucceeded | Metrica di avvio del runtime di integrazione SSIS completata          | Conteggio    | Totale                | Il numero totale di runtime di integrazione SSIS viene avviato in un intervallo di minuti. |
| SSISIntegrationRuntimeStopStuck      | Metriche di interruzione del runtime di integrazione SSIS bloccate               | Conteggio    | Totale                | Il numero totale di interruzioni del runtime di integrazione SSIS bloccate in un intervallo di minuti. |
| SSISIntegrationRuntimeStopSucceeded  | Metriche di interruzione del runtime di integrazione SSIS completata           | Conteggio    | Totale                | Numero totale di interruzioni del runtime di integrazione SSIS riuscite in un intervallo di minuti. |
| SSISPackageExecutionCancelled         | Metriche di esecuzione del pacchetto SSIS annullate  | Conteggio    | Totale                | Numero totale di esecuzioni di pacchetti SSIS annullate entro un intervallo di minuti. |
| SSISPackageExecutionFailed           | Metriche di esecuzione del pacchetto SSIS non riuscite    | Conteggio    | Totale                | Numero totale di esecuzioni di pacchetti SSIS non riuscite in un intervallo di minuti. |
| SSISPackageExecutionSucceeded        | Metrica di esecuzione del pacchetto SSIS riuscita | Conteggio    | Totale                | Numero totale di esecuzioni di pacchetti SSIS riuscite in un intervallo di minuti. |

Per accedere alle metriche, seguire le istruzioni riportate nella [piattaforma dati di monitoraggio di Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-metrics).

> [!NOTE]
> Vengono emessi solo gli eventi delle esecuzioni completate, dell'attività attivata e della pipeline. In corso e le esecuzioni di debug **non** vengono emesse. D'altra parte, vengono generati gli eventi di **tutte le** esecuzioni di pacchetti SSIS, inclusi quelli che sono stati completati e in corso, indipendentemente dai metodi di chiamata. Ad esempio, è possibile richiamare le esecuzioni di pacchetti in SQL Server Data Tools abilitati per Azure (SSDT), tramite T-SQL in SSMS, SQL Server Agent o altri strumenti designati e come esecuzioni avviate o di debug delle attività Esegui pacchetto SSIS nelle pipeline di ADF.

## <a name="data-factory-alerts"></a>Avvisi Data Factory

Accedere al portale di Azure e selezionare **monitoraggio**  >  **avvisi** per creare avvisi.

![Avvisi nel menu del portale](media/monitor-using-azure-monitor/alerts_image3.png)

### <a name="create-alerts"></a>Creare avvisi

1. Selezionare **+ Nuova regola di avviso** per creare un nuovo avviso.

    ![Nuova regola di avviso](media/monitor-using-azure-monitor/alerts_image4.png)

1. Definire la condizione di avviso.

    > [!NOTE]
    > Assicurarsi di selezionare **tutto** nell'elenco a discesa **Filtra per tipo di risorsa** .

    !["Definire la condizione di avviso" > "Seleziona destinazione", che consente di aprire il riquadro "selezionare una risorsa" ](media/monitor-using-azure-monitor/alerts_image5.png)

    !["Definire la condizione di avviso" > "Aggiungi criteri", che consente di aprire il riquadro "Configura logica del segnale"](media/monitor-using-azure-monitor/alerts_image6.png)

    ![Riquadro "Configura tipo di segnale"](media/monitor-using-azure-monitor/alerts_image7.png)

1. Definire i dettagli dell'avviso.

    ![Dettagli dell'avviso](media/monitor-using-azure-monitor/alerts_image8.png)

1. Definire il gruppo di azioni.

    ![Crea una regola con evidenziato il nuovo gruppo di azioni](media/monitor-using-azure-monitor/alerts_image9.png)

    ![Creare un nuovo gruppo di azioni](media/monitor-using-azure-monitor/alerts_image10.png)

    ![Configurare posta elettronica, SMS, push e Voice](media/monitor-using-azure-monitor/alerts_image11.png)

    ![Definire un gruppo di azioni](media/monitor-using-azure-monitor/alerts_image12.png)

## <a name="set-up-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Configurare i log di diagnostica tramite l'API REST di monitoraggio di Azure

### <a name="diagnostic-settings"></a>Impostazioni di diagnostica

Usare le impostazioni di diagnostica per configurare i log di diagnostica per le risorse non di calcolo. Le impostazioni per un controllo risorsa includono le funzionalità seguenti:

* Specificano la posizione in cui vengono inviati i log di diagnostica. Gli esempi includono un account di archiviazione di Azure, un hub eventi di Azure o i log di monitoraggio.
* Specificano le categorie di log da inviare.
* Specificano per quanto tempo ogni categoria di log deve essere conservata in un account di archiviazione.
* Un periodo di conservazione di zero giorni significa che i log vengono conservati all'infinito. In caso contrario, il valore può essere un numero qualsiasi di giorni compreso tra 1 e 2.147.483.647.
* Se i criteri di conservazione sono impostati, ma la memorizzazione dei log in un account di archiviazione è disabilitata, i criteri di conservazione non hanno alcun effetto. Questa condizione può verificarsi, ad esempio, quando vengono selezionate solo le opzioni Hub eventi o registri di monitoraggio.
* I criteri di conservazione vengono applicati al giorno. Il limite tra i giorni si verifica a mezzanotte UTC (Coordinated Universal Time). Alla fine di un giorno, i log da giorni che esulano dai criteri di conservazione vengono eliminati. Se, ad esempio, si dispone di un criterio di conservazione di un giorno, all'inizio di oggi vengono eliminati i log da prima di ieri.

### <a name="enable-diagnostic-logs-via-the-azure-monitor-rest-api"></a>Abilitare i log di diagnostica tramite l'API REST di monitoraggio di Azure

#### <a name="create-or-update-a-diagnostics-setting-in-the-monitor-rest-api"></a>Creare o aggiornare un'impostazione di diagnostica nell'API REST di monitoraggio

##### <a name="request"></a>Richiesta

```
PUT
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Headers

* Sostituire `{api-version}` con `2016-09-01`.
* Sostituire `{resource-id}` con l'ID della risorsa per cui si desidera modificare le impostazioni di diagnostica. Per altre informazioni, vedere [uso di gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Impostare l'intestazione `Content-Type` su `application/json`.
* Impostare l'intestazione Authorization sul token Web JSON ottenuto dalla Azure Active Directory (Azure AD). Per altre informazioni, vedere [autenticazione delle richieste](../active-directory/develop/authentication-scenarios.md).

##### <a name="body"></a>Corpo

```json
{
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>/providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "metrics": [
        ],
        "logs": [
                {
                    "category": "PipelineRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "TriggerRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                },
                {
                    "category": "ActivityRuns",
                    "enabled": true,
                    "retentionPolicy": {
                        "enabled": false,
                        "days": 0
                    }
                }
            ]
    },
    "location": ""
}
```

| Proprietà | Type | Description |
| --- | --- | --- |
| **storageAccountId** |string | ID risorsa dell'account di archiviazione a cui si vogliono inviare i log di diagnostica. |
| **serviceBusRuleId** |string | ID regola del bus di servizio per lo spazio dei nomi del bus di servizio in cui si vuole creare hub eventi per la trasmissione dei log di diagnostica. Il formato dell'ID regola è `{service bus resource ID}/authorizationrules/{key name}` .|
| **workspaceId** | Tipo complesso | Matrice di granularità temporale metrica e relativi criteri di conservazione. Il valore di questa proprietà è vuoto. |
|**metriche**| Valori di parametri della pipeline eseguita da passare alla pipeline richiamata| Oggetto JSON che esegue il mapping dei nomi di parametro ai valori degli argomenti. |
| **log**| Tipo complesso| Nome di una categoria di log di diagnostica per un tipo di risorsa. Per ottenere l'elenco delle categorie di log di diagnostica per una risorsa, eseguire un'operazione GET Diagnostic-Settings. |
| **category**| string| Una matrice di categorie di log e i relativi criteri di conservazione. |
| **timeGrain** | string | Granularità delle metriche, acquisite nel formato di durata ISO 8601. Il valore della proprietà deve essere `PT1M` , che specifica un minuto. |
| **abilitato**| Boolean | Specifica se la raccolta della metrica o della categoria di log è abilitata per questa risorsa. |
| **retentionPolicy**| Tipo complesso| Descrive i criteri di conservazione per una metrica o categoria di log. Questa proprietà viene usata solo per gli account di archiviazione. |
|**giorni**| Int| Numero di giorni di conservazione delle metriche o dei log. Se il valore della proprietà è 0, i log vengono conservati per sempre. Questa proprietà viene usata solo per gli account di archiviazione. |

##### <a name="response"></a>Risposta

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.Storage/storageAccounts/<storageAccountName>",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.EventHub/namespaces/<eventHubName>/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/<resourceGroupName>//providers/Microsoft.OperationalInsights/workspaces/<LogAnalyticsName>",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```

#### <a name="get-information-about-diagnostics-settings-in-the-monitor-rest-api"></a>Ottenere informazioni sulle impostazioni di diagnostica nell'API REST di monitoraggio

##### <a name="request"></a>Richiesta

```
GET
https://management.azure.com/{resource-id}/providers/microsoft.insights/diagnosticSettings/service?api-version={api-version}
```

##### <a name="headers"></a>Headers

* Sostituire `{api-version}` con `2016-09-01`.
* Sostituire `{resource-id}` con l'ID della risorsa per cui si desidera modificare le impostazioni di diagnostica. Per altre informazioni, vedere [uso di gruppi di risorse per gestire le risorse di Azure](../azure-resource-manager/management/manage-resource-groups-portal.md).
* Impostare l'intestazione `Content-Type` su `application/json`.
* Impostare l'intestazione dell'autorizzazione su un token Web JSON ottenuto dal Azure AD. Per altre informazioni, vedere [autenticazione delle richieste](../active-directory/develop/authentication-scenarios.md).

##### <a name="response"></a>Risposta

200 OK.

```json
{
    "id": "/subscriptions/<subID>/resourcegroups/adf/providers/microsoft.datafactory/factories/shloadobetest2/providers/microsoft.insights/diagnosticSettings/service",
    "type": null,
    "name": "service",
    "location": null,
    "kind": null,
    "tags": null,
    "properties": {
        "storageAccountId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.Storage/storageAccounts/azmonlogs",
        "serviceBusRuleId": "/subscriptions/<subID>/resourceGroups/shloprivate/providers/Microsoft.EventHub/namespaces/shloeventhub/authorizationrules/RootManageSharedAccessKey",
        "workspaceId": "/subscriptions/<subID>/resourceGroups/ADF/providers/Microsoft.OperationalInsights/workspaces/mihaipie",
        "eventHubAuthorizationRuleId": null,
        "eventHubName": null,
        "metrics": [],
        "logs": [
            {
                "category": "PipelineRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "TriggerRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            },
            {
                "category": "ActivityRuns",
                "enabled": true,
                "retentionPolicy": {
                    "enabled": false,
                    "days": 0
                }
            }
        ]
    },
    "identity": null
}
```
Per altre informazioni, vedere [impostazioni di diagnostica](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings).

## <a name="schema-of-logs-and-events"></a>Schema di log ed eventi

### <a name="monitor-schema"></a>Schema di monitoraggio

#### <a name="activity-run-log-attributes"></a>Attributi del log di esecuzione attività

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "activityRunId":"",
   "pipelineRunId":"",
   "resourceId":"",
   "category":"ActivityRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "activityName":"",
   "start":"",
   "end":"",
   "properties":
       {
          "Input": "{
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "BlobSink"
              }
           }",
          "Output": "{"dataRead":121,"dataWritten":121,"copyDuration":5,
               "throughput":0.0236328132,"errors":[]}",
          "Error": "{
              "errorCode": "null",
              "message": "null",
              "failureType": "null",
              "target": "CopyBlobtoBlob"
        }
    }
}
```

| Proprietà | Type | Descrizione | Esempio |
| --- | --- | --- | --- |
| **Level** |string | Livello dei log di diagnostica. Per i log di esecuzione attività, impostare il valore della proprietà su 4. | `4` |
| **correlationId** |string | ID univoco per il rilevamento di una determinata richiesta. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | string | Ora dell'evento nel formato UTC di TimeSpan `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**activityRunId**| string| ID dell'esecuzione dell'attività. | `3a171e1f-b36e-4b80-8a54-5625394f4354` |
|**pipelineRunId**| string| ID dell'esecuzione della pipeline. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| string | ID associato alla risorsa di data factory. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| string | Categoria dei log di diagnostica. Impostare il valore della proprietà su `ActivityRuns`. | `ActivityRuns` |
|**level**| string | Livello dei log di diagnostica. Impostare il valore della proprietà su `Informational`. | `Informational` |
|**operationName**| string | Nome dell'attività con lo stato. Se l'attività è l'heartbeat iniziale, il valore della proprietà è `MyActivity -` . Se l'attività è l'heartbeat finale, il valore della proprietà è `MyActivity - Succeeded` . | `MyActivity - Succeeded` |
|**pipelineName**| string | Nome della pipeline | `MyPipeline` |
|**activityName**| string | Nome dell'attività. | `MyActivity` |
|**start**| string | L'ora di inizio dell'attività viene eseguita nel formato UTC di TimeSpan. | `2017-06-26T20:55:29.5007959Z`|
|**end**| string | L'ora di fine dell'attività viene eseguita nel formato UTC di TimeSpan. Se il log di diagnostica Mostra che un'attività è stata avviata ma non ancora terminata, il valore della proprietà è `1601-01-01T00:00:00Z` . | `2017-06-26T20:55:29.5007959Z` |

#### <a name="pipeline-run-log-attributes"></a>Pipeline-eseguire gli attributi di log

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "runId":"",
   "resourceId":"",
   "category":"PipelineRuns",
   "level":"Informational",
   "operationName":"",
   "pipelineName":"",
   "start":"",
   "end":"",
   "status":"",
   "properties":
    {
      "Parameters": {
        "<parameter1Name>": "<parameter1Value>"
      },
      "SystemParameters": {
        "ExecutionStart": "",
        "TriggerId": "",
        "SubscriptionId": ""
      }
    }
}
```

| Proprietà | Type | Descrizione | Esempio |
| --- | --- | --- | --- |
| **Level** |string | Livello dei log di diagnostica. Per i log di esecuzione attività, impostare il valore della proprietà su 4. | `4` |
| **correlationId** |string | ID univoco per il rilevamento di una determinata richiesta. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | string | Ora dell'evento nel formato UTC di TimeSpan `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**runId**| string| ID dell'esecuzione della pipeline. | `9f6069d6-e522-4608-9f99-21807bfc3c70` |
|**resourceId**| string | ID associato alla risorsa di data factory. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| string | Categoria dei log di diagnostica. Impostare il valore della proprietà su `PipelineRuns`. | `PipelineRuns` |
|**level**| string | Livello dei log di diagnostica. Impostare il valore della proprietà su `Informational`. | `Informational` |
|**operationName**| string | Nome della pipeline insieme al relativo stato. Al termine dell'esecuzione della pipeline, il valore della proprietà è `Pipeline - Succeeded` . | `MyPipeline - Succeeded`. |
|**pipelineName**| string | Nome della pipeline | `MyPipeline` |
|**start**| string | L'ora di inizio dell'attività viene eseguita nel formato UTC di TimeSpan. | `2017-06-26T20:55:29.5007959Z`. |
|**end**| string | L'ora di fine dell'attività viene eseguita nel formato UTC di TimeSpan. Se il log di diagnostica Mostra che un'attività è stata avviata ma non ancora terminata, il valore della proprietà è `1601-01-01T00:00:00Z` .  | `2017-06-26T20:55:29.5007959Z` |
|**Stato**| string | Stato finale dell'esecuzione della pipeline. I possibili valori di proprietà sono `Succeeded` e `Failed` . | `Succeeded`|

#### <a name="trigger-run-log-attributes"></a>Trigger-eseguire gli attributi del log

```json
{
   "Level": "",
   "correlationId":"",
   "time":"",
   "triggerId":"",
   "resourceId":"",
   "category":"TriggerRuns",
   "level":"Informational",
   "operationName":"",
   "triggerName":"",
   "triggerType":"",
   "triggerEvent":"",
   "start":"",
   "status":"",
   "properties":
   {
      "Parameters": {
        "TriggerTime": "",
       "ScheduleTime": ""
      },
      "SystemParameters": {}
    }
}
```

| Proprietà | Type | Descrizione | Esempio |
| --- | --- | --- | --- |
| **Level** |string | Livello dei log di diagnostica. Per i log di esecuzione attività, impostare il valore della proprietà su 4. | `4` |
| **correlationId** |string | ID univoco per il rilevamento di una determinata richiesta. | `319dc6b4-f348-405e-b8d7-aafc77b73e77` |
| **time** | string | Ora dell'evento nel formato UTC di TimeSpan `YYYY-MM-DDTHH:MM:SS.00000Z` . | `2017-06-28T21:00:27.3534352Z` |
|**triggerId**| string| ID dell'esecuzione del trigger. | `08587023010602533858661257311` |
|**resourceId**| string | ID associato alla risorsa di data factory. | `/SUBSCRIPTIONS/<subID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |
|**category**| string | Categoria dei log di diagnostica. Impostare il valore della proprietà su `PipelineRuns`. | `PipelineRuns` |
|**level**| string | Livello dei log di diagnostica. Impostare il valore della proprietà su `Informational`. | `Informational` |
|**operationName**| string | Nome del trigger con lo stato finale, che indica se il trigger è stato attivato correttamente. Se l'heartbeat ha avuto esito positivo, il valore della proprietà è `MyTrigger - Succeeded` . | `MyTrigger - Succeeded` |
|**triggerName**| string | Nome del trigger. | `MyTrigger` |
|**triggerType**| string | Tipo di trigger. I possibili valori di proprietà sono `Manual Trigger` e `Schedule Trigger` . | `ScheduleTrigger` |
|**triggerEvent**| string | Evento del trigger. | `ScheduleTime - 2017-07-06T01:50:25Z` |
|**start**| string | Ora di inizio dell'attivazione del trigger nel formato UTC di TimeSpan. | `2017-06-26T20:55:29.5007959Z`|
|**Stato**| string | Stato finale che indica se il trigger è stato attivato correttamente. I possibili valori di proprietà sono `Succeeded` e `Failed` . | `Succeeded`|

#### <a name="ssis-integration-runtime-log-attributes"></a>Attributi del log del runtime di integrazione SSIS

Ecco gli attributi di log delle operazioni di avvio/arresto/manutenzione IR SSIS.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "resultType": "",
   "properties": {
      "message": ""
   },
   "resourceId": ""
}
```

| Proprietà                   | Type   | Descrizione                                                   | Esempio                        |
| -------------------------- | ------ | ------------------------------------------------------------- | ------------------------------ |
| **time**                   | string | Ora dell'evento in formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z` | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | string | Nome dell'operazione IR SSIS                            | `Start/Stop/Maintenance` |
| **category**               | string | Categoria dei log di diagnostica                               | `SSISIntegrationRuntimeLogs` |
| **correlationId**          | string | ID univoco per il rilevamento di un'operazione specifica             | `f13b159b-515f-4885-9dfa-a664e949f785Deprovision0059035558` |
| **dataFactoryName**        | string | Nome del file ADF                                          | `MyADFv2` |
| **integrationRuntimeName** | string | Nome del runtime di integrazione SSIS                                      | `MySSISIR` |
| **level**                  | string | Livello dei log di diagnostica                                  | `Informational` |
| **resultType**             | string | Risultato dell'operazione IR SSIS                          | `Started/InProgress/Succeeded/Failed` |
| **message**                | string | Messaggio di output dell'operazione IR SSIS                  | `The stopping of your SSIS integration runtime has succeeded.` |
| **resourceId**             | string | ID univoco della risorsa ADF                            | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-message-context-log-attributes"></a>Attributi del log del contesto del messaggio di evento SSIS

Ecco gli attributi di log delle condizioni correlate ai messaggi di evento generati dalle esecuzioni del pacchetto SSIS nel runtime di integrazione SSIS. Forniscono informazioni simili come la [tabella o la vista del contesto del messaggio di evento SSISDB Catalog (SSISDB)](https://docs.microsoft.com/sql/integration-services/system-views/catalog-event-message-context?view=sql-server-ver15) che mostra i valori di runtime di molte proprietà del pacchetto SSIS. Vengono generati quando si seleziona `Basic/Verbose` il livello di registrazione e sono utili per il debug e la verifica della conformità.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "contextDepth": "",
      "packagePath": "",
      "contextType": "",
      "contextSourceName": "",
      "contextSourceId": "",
      "propertyName": "",
      "propertyValue": ""
   },
   "resourceId": ""
}
```

| Proprietà                   | Type   | Descrizione                                                          | Esempio                        |
| -------------------------- | ------ | -------------------------------------------------------------------- | ------------------------------ |
| **time**                   | string | Ora dell'evento in formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`        | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | string | Questa impostazione è impostata su `YourSSISIRName-SSISPackageEventMessageContext`       | `mysqlmissisir-SSISPackageEventMessageContext` |
| **category**               | string | Categoria dei log di diagnostica                                      | `SSISPackageEventMessageContext` |
| **correlationId**          | string | ID univoco per il rilevamento di un'operazione specifica                    | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | string | Nome del file ADF                                                 | `MyADFv2` |
| **integrationRuntimeName** | string | Nome del runtime di integrazione SSIS                                             | `MySSISIR` |
| **level**                  | string | Livello dei log di diagnostica                                         | `Informational` |
| **operationId**            | string | ID univoco per il rilevamento di una particolare operazione in SSISDB          | `1` (1 indica le operazioni correlate a pacchetti **non** archiviati in SSISDB/richiamati tramite T-SQL) |
| **contextDepth**           | string | Profondità del contesto del messaggio di evento                              | `0` (0 indica il contesto prima dell'avvio dell'esecuzione del pacchetto, 1 indica il contesto in cui si verifica un errore e aumenta man mano che il contesto è più lontano dall'errore) |
| **packagePath**            | string | Percorso dell'oggetto pacchetto come origine del contesto del messaggio di evento      | `\Package` |
| **contextType**            | string | Tipo di oggetto pacchetto come origine del contesto del messaggio di evento      | `60`(vedere [altri tipi di contesto](https://docs.microsoft.com/sql/integration-services/system-views/catalog-event-message-context?view=sql-server-ver15#remarks)) |
| **contextSourceName**      | string | Nome dell'oggetto pacchetto come origine del contesto del messaggio di evento      | `MyPackage` |
| **contextSourceId**        | string | ID univoco dell'oggetto pacchetto come origine del contesto del messaggio di evento | `{E2CF27FB-EA48-41E9-AF6F-3FE938B4ADE1}` |
| **propertyName**           | string | Nome della proprietà del pacchetto per l'origine del contesto del messaggio di evento   | `DelayValidation` |
| **propertyValue**          | string | Valore della proprietà del pacchetto per l'origine del contesto del messaggio di evento  | `False` |
| **resourceId**             | string | ID univoco della risorsa ADF                                   | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-event-messages-log-attributes"></a>Attributi di log dei messaggi di evento SSIS

Ecco gli attributi di log dei messaggi di evento generati dalle esecuzioni del pacchetto SSIS nel runtime di integrazione SSIS. Contengono informazioni simili come la [tabella o la vista dei messaggi di evento SSISDB](https://docs.microsoft.com/sql/integration-services/system-views/catalog-event-messages?view=sql-server-ver15) che mostra il testo o i metadati dettagliati dei messaggi di evento. Vengono generati a qualsiasi livello di registrazione eccetto `None` .

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "operationId": "",
      "messageTime": "",
      "messageType": "",
      "messageSourceType": "",
      "message": "",
      "packageName": "",
      "eventName": "",
      "messageSourceName": "",
      "messageSourceId": "",
      "subcomponentName": "",
      "packagePath": "",
      "executionPath": "",
      "threadId": ""
   }
}
```

| Proprietà                   | Type   | Descrizione                                                        | Esempio                        |
| -------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                   | string | Ora dell'evento in formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | string | Questa impostazione è impostata su `YourSSISIRName-SSISPackageEventMessages`           | `mysqlmissisir-SSISPackageEventMessages` |
| **category**               | string | Categoria dei log di diagnostica                                    | `SSISPackageEventMessages` |
| **correlationId**          | string | ID univoco per il rilevamento di un'operazione specifica                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | string | Nome del file ADF                                               | `MyADFv2` |
| **integrationRuntimeName** | string | Nome del runtime di integrazione SSIS                                           | `MySSISIR` |
| **level**                  | string | Livello dei log di diagnostica                                       | `Informational` |
| **operationId**            | string | ID univoco per il rilevamento di una particolare operazione in SSISDB        | `1` (1 indica le operazioni correlate a pacchetti **non** archiviati in SSISDB/richiamati tramite T-SQL) |
| **messageTime**            | string | Data e ora di creazione del messaggio di evento in formato UTC          | `2017-06-28T21:00:27.3534352Z` |
| **messageType**            | string | Tipo di messaggio di evento                                     | `70`(vedere [altri tipi di messaggi](https://docs.microsoft.com/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database?view=sql-server-ver15#remarks)) |
| **messageSourceType**      | string | Tipo di origine del messaggio di evento                              | `20`(vedere [altri tipi di origine del messaggio](https://docs.microsoft.com/sql/integration-services/system-views/catalog-operation-messages-ssisdb-database?view=sql-server-ver15#remarks)) |
| **message**                | string | Testo del messaggio di evento                                     | `MyPackage:Validation has started.` |
| **packageName**            | string | Nome del file di pacchetto eseguito                             | `MyPackage.dtsx` |
| **eventName**              | string | Nome dell'evento di run-time correlato                                 | `OnPreValidate` |
| **messageSourceName**      | string | Nome del componente del pacchetto come origine del messaggio di evento         | `Data Flow Task` |
| **messageSourceId**        | string | ID univoco del componente del pacchetto come origine del messaggio di evento    | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |
| **subcomponentName**       | string | Nome del componente flusso di dati come origine del messaggio di evento       | `SSIS.Pipeline` |
| **packagePath**            | string | Percorso dell'oggetto pacchetto come origine del messaggio di evento            | `\Package\Data Flow Task` |
| **Percorso**          | string | Percorso completo dal pacchetto padre al componente eseguito            | `\Transformation\Data Flow Task` (Questo percorso acquisisce anche le iterazioni del componente) |
| **threadId**               | string | ID univoco del thread eseguito quando viene registrato il messaggio di evento | `{1a45a5a4-3df9-4f02-b818-ebf583829ad2}    ` |

#### <a name="ssis-executable-statistics-log-attributes"></a>Attributi di log delle statistiche eseguibili SSIS

Ecco gli attributi di log delle statistiche eseguibili generate dalle esecuzioni del pacchetto SSIS nel runtime di integrazione SSIS, in cui i file eseguibili sono contenitori o attività nel flusso di controllo dei pacchetti. Forniscono informazioni simili come la [tabella o la vista delle statistiche eseguibili di SSISDB](https://docs.microsoft.com/sql/integration-services/system-views/catalog-executable-statistics?view=sql-server-ver15) che mostra una riga per ogni eseguibile in esecuzione, incluse le relative iterazioni. Vengono generati a qualsiasi livello di registrazione, ad eccezione di `None` e utili per identificare i colli di bottiglia a livello di attività/errori.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "executionPath": "",
      "startTime": "",
      "endTime": "",
      "executionDuration": "",
      "executionResult": "",
      "executionValue": ""
   },
   "resourceId": ""
}
```

| Proprietà                   | Type   | Descrizione                                                      | Esempio                        |
| -------------------------- | ------ | ---------------------------------------------------------------- | ------------------------------ |
| **time**                   | string | Ora dell'evento in formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`    | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | string | Questa impostazione è impostata su `YourSSISIRName-SSISPackageExecutableStatistics`  | `mysqlmissisir-SSISPackageExecutableStatistics` |
| **category**               | string | Categoria dei log di diagnostica                                  | `SSISPackageExecutableStatistics` |
| **correlationId**          | string | ID univoco per il rilevamento di un'operazione specifica                | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | string | Nome del file ADF                                             | `MyADFv2` |
| **integrationRuntimeName** | string | Nome del runtime di integrazione SSIS                                         | `MySSISIR` |
| **level**                  | string | Livello dei log di diagnostica                                     | `Informational` |
| **executionId**            | string | ID univoco per il rilevamento di una particolare esecuzione in SSISDB      | `1` (1 indica le esecuzioni correlate ai pacchetti **non** archiviati in SSISDB/richiamati tramite T-SQL) |
| **Percorso**          | string | Percorso completo dal pacchetto padre al componente eseguito          | `\Transformation\Data Flow Task` (Questo percorso acquisisce anche le iterazioni del componente) |
| **startTime**              | string | Tempo durante il quale l'eseguibile immette la fase di pre-esecuzione in formato UTC  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | string | Tempo durante il quale l'eseguibile entra nella fase successiva all'esecuzione in formato UTC | `2017-06-28T21:00:27.3534352Z` |
| **executionDuration**      | string | Tempo di esecuzione del file eseguibile in millisecondi                   | `1,125` |
| **executionResult**        | string | Risultato dell'esecuzione dell'eseguibile                                 | `0` (0 indica esito positivo, 1 indica un errore, 2 indica il completamento e 3 indica l'annullamento) |
| **executionValue**         | string | Valore definito dall'utente restituito dall'esecuzione del file eseguibile            | `1` |
| **resourceId**             | string | ID univoco della risorsa ADF                               | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-component-phases-log-attributes"></a>Attributi log delle fasi del componente di esecuzione SSIS

Ecco gli attributi di log delle statistiche di runtime per i componenti del flusso di dati generati dalle esecuzioni del pacchetto SSIS nel runtime di integrazione SSIS. Forniscono informazioni simili come la [tabella o la vista delle fasi del componente di esecuzione di SSISDB](https://docs.microsoft.com/sql/integration-services/system-views/catalog-execution-component-phases?view=sql-server-ver15) che mostra il tempo impiegato dai componenti del flusso di dati in tutte le fasi di esecuzione. Vengono generati quando si seleziona il `Performance/Verbose` livello di registrazione e sono utili per l'acquisizione delle statistiche di esecuzione del flusso di dati.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "subcomponentName": "",
      "phase": "",
      "startTime": "",
      "endTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Proprietà                   | Type   | Descrizione                                                         | Esempio                        |
| -------------------------- | ------ | ------------------------------------------------------------------- | ------------------------------ |
| **time**                   | string | Ora dell'evento in formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`       | `2017-06-28T21:00:27.3534352Z` |
| **operationName**          | string | Questa impostazione è impostata su `YourSSISIRName-SSISPackageExecutionComponentPhases` | `mysqlmissisir-SSISPackageExecutionComponentPhases` |
| **category**               | string | Categoria dei log di diagnostica                                     | `SSISPackageExecutionComponentPhases` |
| **correlationId**          | string | ID univoco per il rilevamento di un'operazione specifica                   | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**        | string | Nome del file ADF                                                | `MyADFv2` |
| **integrationRuntimeName** | string | Nome del runtime di integrazione SSIS                                            | `MySSISIR` |
| **level**                  | string | Livello dei log di diagnostica                                        | `Informational` |
| **executionId**            | string | ID univoco per il rilevamento di una particolare esecuzione in SSISDB         | `1` (1 indica le esecuzioni correlate ai pacchetti **non** archiviati in SSISDB/richiamati tramite T-SQL) |
| **packageName**            | string | Nome del file di pacchetto eseguito                              | `MyPackage.dtsx` |
| **taskName**               | string | Nome dell'attività flusso di dati eseguita                                 | `Data Flow Task` |
| **subcomponentName**       | string | Nome del componente flusso di dati                                     | `Derived Column` |
| **fase**                  | string | Nome della fase di esecuzione                                         | `AcquireConnections` |
| **startTime**              | string | Ora di inizio della fase di esecuzione in formato UTC                  | `2017-06-28T21:00:27.3534352Z` |
| **endTime**                | string | Data e ora di fine della fase di esecuzione in formato UTC                    | `2017-06-28T21:00:27.3534352Z` |
| **Percorso**          | string | Percorso di esecuzione dell'attività flusso di dati                            | `\Transformation\Data Flow Task` |
| **resourceId**             | string | ID univoco della risorsa ADF                                  | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

#### <a name="ssis-execution-data-statistics-log-attributes"></a>Attributi di log delle statistiche dei dati di esecuzione SSIS

Ecco gli attributi di log dei movimenti dei dati attraverso ogni parte delle pipeline del flusso di dati, dall'upstream ai componenti downstream, generati dalle esecuzioni del pacchetto SSIS nel runtime di integrazione SSIS. Forniscono informazioni simili come la [tabella o la vista delle statistiche sui dati di esecuzione di SSISDB](https://docs.microsoft.com/sql/integration-services/system-views/catalog-execution-data-statistics?view=sql-server-ver15) che mostra i conteggi delle righe dei dati spostati tramite le attività del flusso di dati. Vengono generati quando si seleziona il `Verbose` livello di registrazione e sono utili per il calcolo della velocità effettiva del flusso di dati.

```json
{
   "time": "",
   "operationName": "",
   "category": "",
   "correlationId": "",
   "dataFactoryName": "",
   "integrationRuntimeName": "",
   "level": "",
   "properties": {
      "executionId": "",
      "packageName": "",
      "taskName": "",
      "dataflowPathIdString": "",
      "dataflowPathName": "",
      "sourceComponentName": "",
      "destinationComponentName": "",
      "rowsSent": "",
      "createdTime": "",
      "executionPath": ""
   },
   "resourceId": ""
}
```

| Proprietà                     | Type   | Descrizione                                                        | Esempio                        |
| ---------------------------- | ------ | ------------------------------------------------------------------ | ------------------------------ |
| **time**                     | string | Ora dell'evento in formato UTC: `YYYY-MM-DDTHH:MM:SS.00000Z`      | `2017-06-28T21:00:27.3534352Z` |
| **operationName**            | string | Questa impostazione è impostata su `YourSSISIRName-SSISPackageExecutionDataStatistics` | `mysqlmissisir-SSISPackageExecutionDataStatistics` |
| **category**                 | string | Categoria dei log di diagnostica                                    | `SSISPackageExecutionDataStatistics` |
| **correlationId**            | string | ID univoco per il rilevamento di un'operazione specifica                  | `e55700df-4caf-4e7c-bfb8-78ac7d2f28a0` |
| **dataFactoryName**          | string | Nome del file ADF                                               | `MyADFv2` |
| **integrationRuntimeName**   | string | Nome del runtime di integrazione SSIS                                           | `MySSISIR` |
| **level**                    | string | Livello dei log di diagnostica                                       | `Informational` |
| **executionId**              | string | ID univoco per il rilevamento di una particolare esecuzione in SSISDB        | `1` (1 indica le esecuzioni correlate ai pacchetti **non** archiviati in SSISDB/richiamati tramite T-SQL) |
| **packageName**              | string | Nome del file di pacchetto eseguito                             | `MyPackage.dtsx` |
| **taskName**                 | string | Nome dell'attività flusso di dati eseguita                                | `Data Flow Task` |
| **dataflowPathIdString**     | string | ID univoco per il rilevamento del percorso del flusso di dati                          | `Paths[SQLDB Table3.ADO NET Source Output]` |
| **dataflowPathName**         | string | Nome del percorso del flusso di dati                                         | `ADO NET Source Output` |
| **sourceComponentName**      | string | Nome del componente flusso di dati che invia dati                    | `SQLDB Table3` |
| **destinationComponentName** | string | Nome del componente flusso di dati che riceve i dati                 | `Derived Column` |
| **rowsSent**                 | string | Numero di righe inviate dal componente di origine                        | `500` |
| **createdTime**              | string | Data e ora in cui vengono ottenuti i valori di riga in formato UTC                | `2017-06-28T21:00:27.3534352Z` |
| **Percorso**            | string | Percorso di esecuzione dell'attività flusso di dati                           | `\Transformation\Data Flow Task` |
| **resourceId**               | string | ID univoco della risorsa ADF                                 | `/SUBSCRIPTIONS/<subscriptionID>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/FACTORIES/<dataFactoryName>` |

### <a name="log-analytics-schema"></a>Schema Log Analytics

Log Analytics eredita lo schema da monitoraggio con le eccezioni seguenti:

* La prima lettera del nome di ogni colonna è maiuscola. Il nome di colonna "correlationId" in monitor, ad esempio, è "CorrelationId" in Log Analytics.
* Non esiste alcuna colonna "Level".
* La colonna dinamica "Properties" viene mantenuta come il seguente tipo di BLOB JSON dinamici.

    | Colonna monitoraggio di Azure | Log Analytics colonna | Type |
    | --- | --- | --- |
    | $. Properties. UserProperties | UserProperties | Dynamic |
    | $. Properties. Annotazioni | annotazioni | Dynamic |
    | $. Properties. Input | Input | Dynamic |
    | $. Properties. Output | Output | Dynamic |
    | $. Properties. Errore. errorCode | ErrorCode | INT |
    | $. Properties. Errore. messaggio | ErrorMessage | string |
    | $. Properties. Errore | Errore | Dynamic |
    | $. Properties. Predecessori | Predecessori | Dynamic |
    | $. Properties. Parametri | Parametri | Dynamic |
    | $.properties.SystemParameters | SystemParameters | Dynamic |
    | $. Properties. Tag | Tag | Dynamic |

## <a name="monitor-ssis-operations-with-azure-monitor"></a>Monitorare le operazioni SSIS con monitoraggio di Azure

Per sollevare & spostare i carichi di lavoro SSIS, è possibile eseguire il [provisioning del runtime di integrazione SSIS in ADF](https://docs.microsoft.com/azure/data-factory/tutorial-deploy-ssis-packages-azure) che supporta:

- Esecuzione di pacchetti distribuiti nel catalogo SSIS (SSISDB) ospitato da un server di database SQL di Azure o da un'istanza gestita (modello di distribuzione del progetto)
- Esecuzione di pacchetti distribuiti nel file system, in File di Azure o in un database SQL Server (MSDB) ospitato da Istanza gestita di database SQL di Azure (modello di distribuzione del pacchetto)

Una volta effettuato il provisioning, è possibile [controllare lo stato operativo del runtime di integrazione SSIS con Azure PowerShell o nell'hub di **monitoraggio** del portale di ADF](https://docs.microsoft.com/azure/data-factory/monitor-integration-runtime#azure-ssis-integration-runtime). Con il modello di distribuzione del progetto, i log di esecuzione dei pacchetti SSIS vengono archiviati nelle tabelle o viste interne di SSISDB, quindi è possibile eseguire query, analizzarli e visualizzarli visivamente usando strumenti designati come SSMS. Con il modello di distribuzione del pacchetto, i log di esecuzione dei pacchetti SSIS possono essere archiviati in file system o File di Azure come file CSV che è ancora necessario analizzare ed elaborare usando altri strumenti designati prima di poter eseguire query, analizzarli e visualizzarli visivamente.

Con l'integrazione di [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform) , è possibile eseguire query, analizzare e presentare visivamente tutte le metriche e i log generati dalle operazioni IR SSIS e dalle esecuzioni di pacchetti ssis in portale di Azure. Inoltre, è anche possibile generare avvisi su di essi.

### <a name="configure-diagnostic-settings-and-workspace-for-ssis-operations"></a>Configurare le impostazioni di diagnostica e l'area di lavoro per le operazioni SSIS

Per inviare tutte le metriche e i log generati dalle operazioni del runtime di integrazione SSIS e dalle esecuzioni di pacchetti SSIS a monitoraggio di Azure, è necessario [configurare le impostazioni di diagnostica e l'area di lavoro per il file ADF](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#configure-diagnostic-settings-and-workspace).

### <a name="ssis-operational-metrics"></a>Metriche operative SSIS

Le [metriche](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics) operative SSIS sono contatori delle prestazioni o valori numerici che descrivono lo stato delle operazioni di avvio e arresto del runtime di integrazione SSIS, nonché le esecuzioni di pacchetti SSIS in un determinato momento. Sono parte della [metrica di ADF in monitoraggio di Azure](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#data-factory-metrics).

Quando si configurano le impostazioni di diagnostica e l'area di lavoro per il file ADF in monitoraggio di Azure, selezionando la casella di controllo _AllMetrics_ le metriche operative di SSIS vengono rese disponibili per l' [analisi interattiva tramite Esplora metriche di Azure](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started), la [presentazione nel dashboard di Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards)e gli avvisi in [tempo quasi reale](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric).

![Denominare le impostazioni e selezionare un'area di lavoro log-Analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="ssis-operational-alerts"></a>Avvisi operativi SSIS

Per generare avvisi sulle metriche operative di SSIS dal portale di ADF, [selezionare la pagina **avvisi & metriche** dell'hub **monitoraggio** ADF e seguire le istruzioni dettagliate fornite](https://docs.microsoft.com/azure/data-factory/monitor-visually#alerts).

![Generazione di avvisi operativi SSIS dal portale di ADF](media/data-factory-monitor-oms/data-factory-monitor-alerts-ssis.png)

Per generare avvisi sulle metriche operative di SSIS da portale di Azure, [selezionare la pagina **avvisi** dell'hub **monitoraggio** di Azure e seguire le istruzioni dettagliate fornite](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#data-factory-alerts).

![Generazione di avvisi operativi SSIS da portale di Azure](media/data-factory-monitor-oms/azure-monitor-alerts-ssis.png)

### <a name="ssis-operational-logs"></a>Log operativi SSIS

I [log](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-logs) operativi SSIS sono eventi generati da operazioni IR SSIS e da esecuzioni di pacchetti SSIS che forniscono un contesto sufficiente per tutti i problemi identificati e sono utili per l'analisi della causa radice. 

Quando si configurano le impostazioni di diagnostica e l'area di lavoro per il file ADF in monitoraggio di Azure, è possibile selezionare i log operativi SSIS pertinenti e inviarli a Log Analytics basati su Esplora dati di Azure. In questa pagina verranno resi disponibili per l' [analisi usando il linguaggio di query avanzato](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview), la [presentazione nel dashboard di Azure](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-app-dashboards)e gli [avvisi in tempo quasi reale](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log).

![Denominare le impostazioni e selezionare un'area di lavoro log-Analytics](media/data-factory-monitor-oms/monitor-oms-image2.png)

Gli schemi e il contenuto dei log di esecuzione del pacchetto SSIS in monitoraggio di Azure e Log Analytics sono simili a quelli delle tabelle o viste interne di SSISDB.

| Categorie di log di monitoraggio di Azure          | Tabelle Log Analytics                     | Viste/tabelle interne di SSISDB              |
| ------------------------------------- | ---------------------------------------- | ----------------------------------------- |
| `SSISIntegrationRuntimeLogs`          | `ADFSSISIntegrationRuntimeLogs`          |                                           |
| `SSISPackageEventMessageContext`      | `ADFSSISPackageEventMessageContext`      | `[internal].[event_message_context]`      |
| `SSISPackageEventMessages`            | `ADFSSISPackageEventMessages`            | `[internal].[event_messages]`             |
| `SSISPackageExecutableStatistics`     | `ADFSSISPackageExecutableStatistics`     | `[internal].[executable_statistics]`      |
| `SSISPackageExecutionComponentPhases` | `ADFSSISPackageExecutionComponentPhases` | `[internal].[execution_component_phases]` |
| `SSISPackageExecutionDataStatistics`  | `ADFSSISPackageExecutionDataStatistics`  | `[internal].[execution_data_statistics]`  |

Per altre informazioni sugli attributi/proprietà del log operativo SSIS, vedere [monitoraggio di Azure e schemi di log Analytics per ADF](https://docs.microsoft.com/azure/data-factory/monitor-using-azure-monitor#schema-of-logs-and-events).

I log di esecuzione del pacchetto SSIS selezionati vengono sempre inviati a Log Analytics indipendentemente dai metodi di chiamata. Ad esempio, è possibile richiamare le esecuzioni di pacchetti in SSDT abilitato per Azure, tramite T-SQL in SSMS, SQL Server Agent o altri strumenti designati, nonché le esecuzioni attivate o di debug delle attività Esegui pacchetto SSIS nelle pipeline di ADF.

Quando si eseguono query sui log dell'operazione IR SSIS nei log Analytics, è possibile usare le proprietà **OperationName** e **ResultType** impostate `Start/Stop/Maintenance` rispettivamente su e `Started/InProgress/Succeeded/Failed` . 

![Esecuzione di query sui log delle operazioni IR SSIS in Log Analytics](media/data-factory-monitor-oms/log-analytics-query.png)

Quando si eseguono query sui log di esecuzione dei pacchetti SSIS in log Analytics, è possibile unirli usando le proprietà CorrelationId di **operationId** / **ExecutionID** / **CorrelationId** . **OperationId** / **ExecutionID** sono sempre impostati su `1` per tutte le operazioni/esecuzioni correlate ai pacchetti **non** archiviati in SSISDB/richiamati tramite T-SQL.

![Esecuzione di query sui log di esecuzione dei pacchetti SSIS in Log Analytics](media/data-factory-monitor-oms/log-analytics-query2.png)

## <a name="next-steps"></a>Passaggi successivi
[Monitorare e gestire pipeline a livello di codice](monitor-programmatically.md)
