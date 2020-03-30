---
title: Creare, visualizzare e gestire gli avvisi di log tramite Monitoraggio di Azure Documenti Microsoft
description: Usare Monitoraggio di Azure per creare, visualizzare e gestire le regole di avviso dei log in Azure.Use the Azure Monitor to author, view, and manage log alert rules in Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 96b1bd86576f8cf34428eb60e2d3f476312311c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249425"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Creare, visualizzare e gestire gli avvisi del log tramite Monitoraggio di Azure

## <a name="overview"></a>Panoramica
Questo articolo illustra come configurare gli avvisi del log tramite l'interfaccia degli avvisi nel portale di Azure. La definizione di una regola di avviso è costituita da tre parti:
- Destinazione: risorsa specifica di Azure da monitorare
- Criteri: condizione specifica o logica che, se rilevata nel segnale, deve attivare l'azione
- Azione: chiamata specifica inviata a un ricevitore di una notifica di posta elettronica, SMS, webhook e così via.

Il termine **Avvisi log** per descrivere gli avvisi in cui signal is log query in a Log [Analytics workspace](../learn/tutorial-viewdata.md) or Application [Insights](../app/analytics.md). Altre informazioni su funzionalità, terminologia e tipi su [Avvisi del log - Panoramica](alerts-unified-log.md).

> [!NOTE]
> I dati di log popolari da [un'area](../../azure-monitor/learn/tutorial-viewdata.md) di lavoro di Log Analytics sono ora disponibili anche nella piattaforma metrica in Monitoraggio di Azure.Popular log data from a Log Analytics workspace is now available on the metric platform in Azure Monitor. Per i dettagli, vedere [Metric Alert for Logs](alerts-metric-logs.md) (Avvisi di metrica per i log).

## <a name="managing-log-alerts-from-the-azure-portal"></a>Gestione degli avvisi del log dal portale di Azure

Di seguito è riportata una Guida dettagliata all'uso degli avvisi del log tramite l'interfaccia del portale di Azure.

### <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Creare una regola per gli avvisi relativi ai log con il portale di Azure

1. Nel [portale](https://portal.azure.com/)selezionare **Monitoraggio** e nella sezione MONITORAGGIO scegliere **Avvisi**.

    ![Monitoraggio](media/alerts-log/AlertsPreviewMenu.png)

1. Selezionare il pulsante **Nuova regola di avviso** per creare un nuovo avviso in Azure.

    ![Aggiungi avviso](media/alerts-log/AlertsPreviewOption.png)

1. Viene visualizzata la sezione Crea avviso composta da tre parti: *Definire la condizione dell'avviso*, *Definire i dettagli dell'avviso* e *Definire il gruppo di azioni*.

    ![Creare una regola](media/alerts-log/AlertsPreviewAdd.png)

1. Definire la condizione dell'avviso tramite il collegamento **Seleziona risorsa** e specificare la destinazione selezionando una risorsa. Filtrare scegliendo la _Sottoscrizione_, il _Tipo di risorsa_ e infine la _Risorsa_ necessaria.

   > [!NOTE]
   > Prima di procedere, per creare un avviso relativo ai log verificare che il segnale di **log** sia disponibile per la risorsa selezionata.
   >  ![Selezionare una risorsa](media/alerts-log/Alert-SelectResourceLog.png)

1. *Avvisi del log*: verificare che **Tipo di risorsa** sia un'origine dati di analisi come *Log Analytics* o *Application Insights* e tipo di segnale **Log** quindi, dopo aver selezionato la **risorsa** appropriata, fare clic su *Operazione completata*. Usare quindi il pulsante **Aggiungi criteri** per visualizzare l'elenco delle opzioni di segnale disponibili per la risorsa e nell'elenco dei segnali selezionare l'opzione di **ricerca log personalizzata** per il servizio di monitoraggio del log scelto, ad esempio *Log Analytics* o *Application Insights*.

   ![Selezionare una risorsa - ricerca log personalizzata](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > Avvisi può importare query di Analytics come tipo di segnale **Log (query salvata)**, come illustrato nella figura precedente. Gli utenti possono pertanto perfezionare la query in Analytics e quindi salvarla per un uso futuro negli avvisi: ulteriori dettagli sull'uso della query di salvataggio disponibile in uso la query di [log in Monitoraggio](../log-query/log-query-overview.md) di Azure o la query condivisa [nell'analisi](../app/app-insights-overview.md)delle informazioni sulle applicazioni.

1. *Avvisi del log*: una volta selezionata, la query per gli avvisi può essere immessa nel campo **Query di ricerca**. Se la sintassi della query non è corretta, nel campo viene visualizzato un errore in ROSSO. Se la sintassi della query è corretta, i dati cronologici della query specificata vengono visualizzati sotto forma di grafico come riferimento con un'opzione per modificare l'intervallo di tempo dalle ultime sei ore alla settimana precedente.

    ![Configurare una regola di avviso](media/alerts-log/AlertsPreviewAlertLog.png)

   > [!NOTE]
   > 
   > La visualizzazione dei dati cronologici è possibile solo se i risultati della query includono dettagli temporali. Se la query restituisce dati di riepilogo o specifici valori di colonna, questi dati vengono visualizzati come tracciato singolo.
   > Per il tipo di avvisi di log Unità di misura della metrica in cui viene usato Application Insights o [la nuova API](alerts-log-api-switch.md), è possibile indicare la variabile specifica per raggruppare i dati con l'opzione **Aggrega in base a**, come illustrato di seguito:
   > 
   > ![opzione aggrega in base a](media/alerts-log/aggregate-on.png)

1. *Avvisi del log*: con la visualizzazione impostata, è possibile selezionare in **Logica avvisi** una delle opzioni visualizzate: Condizione, Aggregazione e infine Soglia. Specificare l'ora in cui valutare la condizione specificata nella logica tramite l'opzione **Periodo**. Specificare la frequenza con cui l'avviso deve essere eseguito selezionando **Frequenza**. Gli **avvisi di log** possono basarsi su:
    - [Numero di record](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules): viene creato un avviso se il numero di record restituiti dalla query è maggiore o minore del valore specificato.
    - [Unità di misura della metrica](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules): viene creato un avviso se ogni *valore di aggregazione* nei risultati supera il valore di soglia specificato ed è *raggruppato* in base al valore scelto. Il numero di violazioni per un avviso è il numero di volte in cui viene superata la soglia nel periodo di tempo scelto. È possibile specificare le violazioni totali per qualsiasi combinazione di violazioni nei set di risultati o le violazioni consecutive necessarie che devono verificarsi in campioni consecutivi.


1. Come secondo passaggio, definire un nome per l'avviso nel campo **Nome regola di avviso** insieme a una **Descrizione** con le specifiche per l'avviso e il valore di **Gravità** nelle opzioni disponibili. Questi dettagli vengono riutilizzati in tutti i messaggi di avviso, le notifiche o notifiche push eseguite da Monitoraggio di Azure. Inoltre, l'utente può scegliere di attivare immediatamente la regola di avviso durante la creazione attivando in modo appropriato l'opzione **Abilita regola alla creazione**.

    Solo per gli **avvisi del log** sono disponibili alcune funzionalità aggiuntive nei dettagli degli avvisi:

    - **Elimina avvisi**: quando si attiva l'eliminazione per la regola di avviso, le azioni della regola vengono disabilitate per un periodo di tempo definito dopo la creazione di un nuovo avviso. La regola è ancora in esecuzione e, se i criteri vengono soddisfatti, crea i record di avviso. L'utente ha il tempo necessario per risolvere il problema senza eseguire azioni duplicate.

        ![Elimina avvisi per gli avvisi del log](media/alerts-log/AlertsPreviewSuppress.png)

        > [!TIP]
        > Specificare un valore di eliminazione dell'avviso maggiore della frequenza di avviso per garantire che le notifiche vengano arrestate senza sovrapposizione

1. Come terzo e ultimo passaggio, specificare se un **gruppo di azioni** deve essere attivato per la regola di avviso quando viene soddisfatta la condizione dell'avviso. È possibile scegliere qualsiasi gruppo di azioni esistente con un avviso o creare un nuovo gruppo di azioni. In base al gruppo di azioni selezionato, quando viene attivato l'avviso, Azure eseguirà queste operazioni: invio di messaggi di posta elettronica, invio di SMS, chiamata di webhook, correzione dell'uso di runbook di Azure, invio di notifiche push allo strumento Gestione dei servizi IT e così via. Ulteriori informazioni sui [gruppi di azioni](action-groups.md).

    > [!NOTE]
    > Vedere i [vincoli dei servizi di sottoscrizione di Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) per i vincoli sui payload di Runbook attivati per gli avvisi del log tramite i gruppi di azioni di Azure

    Per gli **avvisi del log** sono disponibili alcune funzionalità aggiuntive per eseguire l'override di quelle predefinite:

    - **Notifica di posta elettronica**: esegue l'override dell'*oggetto di posta elettronica* nel messaggio di posta elettronica inviato tramite il gruppo di azioni, se esistono una o più azioni di posta elettronica nel gruppo di azioni. Non è possibile modificare il corpo del messaggio e questo campo **non** è destinato agli indirizzi di posta elettronica.
    - **Includi payload JSON personalizzato**: esegue l'override del webhook JSON usato dai gruppi di azioni, se nel gruppo di azioni sono presenti una o più azioni webhook. Un utente può specificare il formato JSON da usare per tutti i webhook configurati nel gruppo di azioni associato. Per altre informazioni sui formati webhook, vedere [Azioni webhook per le regole di avviso relative ai log](../../azure-monitor/platform/alerts-log-webhook.md). L'opzione di visualizzazione Webhook consente di verificare il formato tramite i dati JSON di esempio.

        ![Override dell'azione per gli avvisi del log](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Se tutti i campi sono validi e hanno un segno di spunta verde, il pulsante **Crea regola di avviso** può essere selezionato e viene creato un avviso in Monitoraggio di Azure - Avvisi. Tutti gli avvisi possono essere visualizzati nella dashboard di Avvisi.

     ![Creazione di regole](media/alerts-log/AlertsPreviewCreate.png)

     Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

Gli utenti possono anche finalizzare la query di analisi [nell'analisi](../log-query/portals.md) dei log e quindi spingerla per creare un avviso tramite il pulsante 'Imposta avviso', quindi seguire le istruzioni dal passaggio 6 in poi nell'esercitazione precedente.

 ![Log Analytics - Imposta avviso](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Visualizzare e gestire gli avvisi del log nel portale di Azure

1. Nel [portale](https://portal.azure.com/)selezionare **Monitoraggio** e nella sezione MONITORAGGIO scegliere **Avvisi**.

1. Viene visualizzato il **dashboard degli avvisi,** in cui tutti gli avvisi di Azure (inclusi gli avvisi di log) vengono visualizzati in una singola bacheca. incluse tutte le istanze di quando la regola di avviso del log è stata attivata. Per altre informazioni, consultare [Gestione avvisi](https://aka.ms/managealertinstances).
    > [!NOTE]
    > Le regole di avviso del log comprendono la logica personalizzata basata su query fornita dagli utenti e quindi senza stato risolto. Per questo motivo viene generata ogni volta che vengono soddisfatte le condizioni specificate nella regola di avviso del log.

1. Selezionare il pulsante **Gestisci regole** nella barra superiore per passare alla sezione di gestione delle regole in cui sono elencate tutte le regole di avviso create, inclusi gli avvisi che sono stati disabilitati.
    ![gestione delle regole di avviso](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Gestione degli avvisi del log tramite il modello di risorsa di Azure

Gli avvisi dei log in Monitoraggio di Azure sono associati al tipo di risorsa `Microsoft.Insights/scheduledQueryRules/`. Per altre informazioni su questo tipo di risorsa, vedere il [riferimento all'API per regole di query pianificate in Monitoraggio di Azure](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/). Gli avvisi dei log per Application Insights o Log Analytics possono essere creati usando l'[API Regole di query pianificate](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/).

> [!NOTE]
> Gli avvisi dei log per Log Analytics possono essere gestiti anche usando l'[API degli avvisi di Log Analytics](api-alerts.md) legacy e i modelli legacy di [avvisi e di ricerche salvate di Log Analytics](../insights/solutions-resources-searches-alerts.md). Per altre informazioni sull'uso della nuova API ScheduledQueryRules illustrata qui in dettaglio per impostazione predefinita, vedere [Passare alla nuova API per gli avvisi di Log Analytics](alerts-log-api-switch.md).


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Creazione di avvisi di log di esempio tramite il modello di risorse di Azure

Di seguito viene riportata la struttura per la [creazione di regole di query pianificate](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) basate su modello di risorse in cui viene usata una query di ricerca log standard di [regola di avviso Numero di risultati](alerts-unified-log.md#number-of-results-alert-rules) con un set di dati di esempio come variabili.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "southcentralus",
        "alertName": "samplelogalert",
        "alertDescription": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"requests",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/components/sampleAIapplication",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4"
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/myRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescription')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "aznsAction":{
                    "actionGroup":"[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]"
                }
            }
        }
    } ]
}

```

Per usare il codice JSON di esempio in questa procedura dettagliata, è possibile salvarlo con il nome sampleScheduledQueryRule.json. Può essere distribuito usando [Azure Resource Manager nel portale di Azure](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).


### <a name="log-alert-with-cross-resource-query-using-azure-resource-template"></a>Avviso di log con query tra risorse in cui viene usato il modello di risorse di Azure

Di seguito viene riportata la struttura per la [creazione di regole di query pianificate](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate) basate su modello di risorse in cui viene usata una [query di ricerca log tra risorse](../../azure-monitor/log-query/cross-workspace-query.md) di [avviso di log di tipo Unità di misura della metrica](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) con un set di dati di esempio come variabili.

```json

{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "alertLocation": "Region Name for your Application Insights App or Log Analytics Workspace",
        "alertName": "sample log alert",
        "alertDescr": "Sample log search alert",
        "alertStatus": "true",
        "alertSource":{
            "Query":"union workspace(\"servicews\").Update, app('serviceapp').requests | summarize AggregatedValue = count() by bin(TimeGenerated,1h), Classification",
            "Resource1": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Resource2": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/components/serviceapp",
            "SourceId": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews",
            "Type":"ResultCount"
        },
        "alertSchedule":{
            "Frequency": 15,
            "Time": 60
        },
        "alertActions":{
            "SeverityLevel": "4",
            "SuppressTimeinMin": 20
        },
        "alertTrigger":{
            "Operator":"GreaterThan",
            "Threshold":"1"
        },
        "metricMeasurement": {
            "thresholdOperator": "Equal",
            "threshold": "1",
            "metricTriggerType": "Consecutive",
            "metricColumn": "Classification"
        },
        "actionGrp":{
            "ActionGroup": "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG",
            "Subject": "Customized Email Header",
            "Webhook": "{ \"alertname\":\"#alertrulename\", \"IncludeSearchResults\":true }"
        }
    },
    "resources":[ {
        "name":"[variables('alertName')]",
        "type":"Microsoft.Insights/scheduledQueryRules",
        "apiVersion": "2018-04-16",
        "location": "[variables('alertLocation')]",
        "properties":{
            "description": "[variables('alertDescr')]",
            "enabled": "[variables('alertStatus')]",
            "source": {
                "query": "[variables('alertSource').Query]",
                "authorizedResources": "[concat(array(variables('alertSource').Resource1), array(variables('alertSource').Resource2))]",
                "dataSourceId": "[variables('alertSource').SourceId]",
                "queryType":"[variables('alertSource').Type]"
            },
            "schedule":{
                "frequencyInMinutes": "[variables('alertSchedule').Frequency]",
                "timeWindowInMinutes": "[variables('alertSchedule').Time]"
            },
            "action":{
                "odata.type": "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.Microsoft.AppInsights.Nexus.DataContracts.Resources.ScheduledQueryRules.AlertingAction",
                "severity":"[variables('alertActions').SeverityLevel]",
                "throttlingInMin": "[variables('alertActions').SuppressTimeinMin]",
                "aznsAction":{
                    "actionGroup": "[array(variables('actionGrp').ActionGroup)]",
                    "emailSubject":"[variables('actionGrp').Subject]",
                    "customWebhookPayload":"[variables('actionGrp').Webhook]"
                },
                "trigger":{
                    "thresholdOperator":"[variables('alertTrigger').Operator]",
                    "threshold":"[variables('alertTrigger').Threshold]",
                    "metricTrigger":{
                        "thresholdOperator": "[variables('metricMeasurement').thresholdOperator]",
                        "threshold": "[variables('metricMeasurement').threshold]",
                        "metricColumn": "[variables('metricMeasurement').metricColumn]",
                        "metricTriggerType": "[variables('metricMeasurement').metricTriggerType]"
                    }
                }
            }
        }
    } ]
}

```

> [!IMPORTANT]
> Quando si usano query tra risorse in un avviso di log, l'uso di [authorizedResources](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/createorupdate#source) è obbligatorio e l'utente deve avere accesso all'elenco delle risorse indicate.

Per usare il codice JSON di esempio in questa procedura dettagliata, è possibile salvarlo con il nome sampleScheduledQueryRule.json. Può essere distribuito usando [Azure Resource Manager nel portale di Azure](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="managing-log-alerts-using-powershell"></a>Gestione degli avvisi di log tramite PowerShellManaging log alerts using PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Monitoraggio di Azure: [l'API delle regole](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) di query pianificate è un'API REST e completamente compatibile con l'API REST di Azure Resource Manager.Azure Monitor - Scheduled Query Rules API is a REST API and fully compatible with Azure Resource Manager REST API. E i cmdlet di PowerShell elencati di seguito sono disponibili per sfruttare l'API Regole di [query pianificate.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/)

1. [New-AzScheduledQueryRule:](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) cmdlet Powershell per creare una nuova regola di avviso del log.
1. [Set-AzScheduledQueryRule:](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule) cmdlet Powershell per aggiornare una regola di avviso del log esistente.
1. [New-AzScheduledQueryRuleSource:](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulesource) cmdlet di PowerShell per creare o aggiornare l'oggetto che specifica i parametri di origine per un avviso di log. Utilizzato come input dal cmdlet [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) e [Set-AzScheduledQueryRule.](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule)
1. [New-AzScheduledQueryRuleSchedule](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleSchedule): cmdlet di PowerShell per creare o aggiornare l'oggetto che specifica i parametri di pianificazione per un avviso di log. Utilizzato come input dal cmdlet [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) e [Set-AzScheduledQueryRule.](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule)
1. [New-AzScheduledQueryRuleAlertingAction:](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction) cmdlet di Powershell per creare o aggiornare l'oggetto che specifica i parametri di azione per un avviso di log. Utilizzato come input dal cmdlet [New-AzScheduledQueryRule](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrule) e [Set-AzScheduledQueryRule.](https://docs.microsoft.com/powershell/module/az.monitor/set-azscheduledqueryrule)
1. [New-AzScheduledQueryRuleAznsActionGroup:](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) cmdlet di Powershell per creare o aggiornare l'oggetto che specifica i parametri dei gruppi di azioni per un avviso di log. Utilizzato come input dal cmdlet [New-AzScheduledQueryRuleAlertingAction.](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction)
1. [New-AzScheduledQueryRuleTriggerCondition:](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) cmdlet di Powershell per creare o aggiornare l'oggetto che specifica i parametri della condizione di trigger per l'avviso di log. Utilizzato come input dal cmdlet [New-AzScheduledQueryRuleAlertingAction.](https://docs.microsoft.com/powershell/module/az.monitor/New-AzScheduledQueryRuleAlertingAction)
1. [New-AzScheduledQueryRuleLogMetricTrigger:](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) cmdlet Powershell per creare o aggiornare l'oggetto che specifica i parametri della condizione di trigger metrica per l'avviso del log del tipo di [misurazione metrica](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules). Utilizzato come input dal cmdlet [New-AzScheduledQueryRuleTriggerCondition.](https://docs.microsoft.com/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition)
1. [Get-AzScheduledQueryRule:](https://docs.microsoft.com/powershell/module/az.monitor/get-azscheduledqueryrule) cmdlet Powershell per elencare le regole di avviso del log esistenti o una regola di avviso del log specificaGet-AzScheduledQueryRule : Powershell cmdlet to list existing log alert rules or a specific log alert rule
1. [Update-AzScheduledQueryRule:](https://docs.microsoft.com/powershell/module/az.monitor/update-azscheduledqueryrule) cmdlet di PowerShell per abilitare o disabilitare la regola di avviso del logUpdate-AzScheduledQueryRule : Powershell cmdlet to enable or disable log alert rule
1. [Remove-AzScheduledQueryRule:](https://docs.microsoft.com/powershell/module/az.monitor/remove-azscheduledqueryrule)cmdlet di Powershell per eliminare una regola di avviso del log esistenteRemove-AzScheduledQueryRule : Powershell cmdlet to delete an existing log alert rule

> [!NOTE]
> I cmdlet di PowerShell ScheduledQueryRules possono gestire solo le regole create appositamente il cmdlet o tramite Monitoraggio di Azure - API regole di [query pianificate.](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) Le regole di avviso per i log create utilizzando [l'API](api-alerts.md) di avviso di Log Analytics legacy e i modelli legacy delle [ricerche salvate e degli avvisi](../insights/solutions-resources-searches-alerts.md) di Log Analytics possono essere gestiti utilizzando i cmdlet di PowerShell ScheduledQueryRules solo dopo che l'utente cambia la preferenza API per Log Analytics [Alerts](alerts-log-api-switch.md).

Di seguito sono illustrati i passaggi per la creazione di una regola di avviso del log di esempio utilizzando i cmdlet di PowerShell scheduledQueryRules.
```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>Gestione degli avvisi di log tramite l'interfaccia della riga di comando o l'APIManaging log alerts using CLI

Monitoraggio di Azure: [l'API delle regole](https://docs.microsoft.com/rest/api/monitor/scheduledqueryrules/) di query pianificate è un'API REST e completamente compatibile con l'API REST di Azure Resource Manager.Azure Monitor - Scheduled Query Rules API is a REST API and fully compatible with Azure Resource Manager REST API. Di conseguenza può essere usato tramite Powershell usando i comandi di Resource Manager per l'interfaccia della riga di comando di Azure.So it can be used via Powershell using Resource Manager commands for Azure CLI.


> [!NOTE]
> Gli avvisi dei log per Log Analytics possono essere gestiti anche usando l'[API degli avvisi di Log Analytics](api-alerts.md) legacy e i modelli legacy di [avvisi e di ricerche salvate di Log Analytics](../insights/solutions-resources-searches-alerts.md). Per altre informazioni sull'uso della nuova API ScheduledQueryRules illustrata qui in dettaglio per impostazione predefinita, vedere [Passare alla nuova API per gli avvisi di Log Analytics](alerts-log-api-switch.md).

Gli avvisi di log attualmente non dispongono di comandi CLI dedicati; ma come illustrato di seguito può essere usato tramite il comando CLI di Azure Resource Manager per il modello di risorse di esempio illustrato in precedenza (sampleScheduledQueryRule.json) nella sezione Modello di risorse:But illustrated below can be used via Azure Resource Manager CLI command for sample Resource Template shown earlier (sampleScheduledQueryRule.json) in the Resource Template section:

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Se l'operazione è stata eseguita correttamente, sarà restituito il valore 201 a indicare la creazione della nuova regola di avviso. Se invece è stata modificata una regola di avviso esistente, sarà restituito il valore 200.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sugli avvisi di log in Avvisi di [AzureLearn](../../azure-monitor/platform/alerts-unified-log.md) about Log Alerts in Azure Alerts
* Comprendere le [azioni webhook per gli avvisi del log](../../azure-monitor/platform/alerts-log-webhook.md)
* Ulteriori informazioni su [Application Insights](../../azure-monitor/app/analytics.md)
* Ulteriori informazioni sulle query di [log](../log-query/log-query-overview.md).
