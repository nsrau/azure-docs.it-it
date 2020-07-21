---
title: Creare, visualizzare e gestire gli avvisi di log con monitoraggio di Azure | Microsoft Docs
description: Usare monitoraggio di Azure per creare, visualizzare e gestire le regole di avviso del log in Azure.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 57cc3624a38fbec1e5bef7bb281363d34acef2b1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86505602"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Creare, visualizzare e gestire gli avvisi del log tramite Monitoraggio di Azure

## <a name="overview"></a>Panoramica
Questo articolo illustra come creare e gestire gli avvisi del log usando l'interfaccia degli avvisi all'interno del portale di Azure. Le regole di avviso sono definite da tre componenti:
- Destinazione: una risorsa di Azure specifica da monitorare
- Criteri: condizione o logica da valutare per la verità. Se true, l'avviso viene generato.  
- Azione: chiamata specifica inviata a un ricevitore di una notifica di posta elettronica, SMS, webhook e così via.

Il termine **avviso di log** descrive gli avvisi in cui viene valutata una query di log in [log Analytics area di lavoro](../log-query/get-started-portal.md) o [Application Insights](../log-query/log-query-overview.md) e viene generato un avviso se il risultato è true. Altre informazioni su funzionalità, terminologia e tipi su [Avvisi del log - Panoramica](alerts-unified-log.md).

> [!NOTE]
> I dati di log da [un'area di lavoro log Analytics](../log-query/get-started-portal.md) possono anche essere indirizzati al database di metriche di monitoraggio di Azure. Gli avvisi di metrica hanno un [comportamento diverso](alerts-metric-overview.md), che può essere più utile a seconda dei dati in uso.   Per informazioni su cosa e come è possibile indirizzare i log alle metriche, vedere [avviso di metrica per i log](alerts-metric-logs.md).

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Creare una regola per gli avvisi relativi ai log con il portale di Azure

1. Nel [portale](https://portal.azure.com/)selezionare **monitoraggio**. In questa sezione scegliere **avvisi**.

    ![Monitoraggio](media/alerts-log/AlertsPreviewMenu.png)

1. Fare clic su **nuova regola di avviso**. 

    ![Aggiungi avviso](media/alerts-log/AlertsPreviewOption.png)

1. Verrà visualizzato il riquadro **Crea avviso** . È costituito da quattro parti: 
    - Risorsa a cui viene applicato l'avviso
    - Condizione da controllare
    - Azione da eseguire se la condizione è true
    - Dettagli per denominare e descrivere l'avviso. 

    ![Creare una regola](media/alerts-log/AlertsPreviewAdd.png)

1. Definire la condizione dell'avviso tramite il collegamento **Seleziona risorsa** e specificare la destinazione selezionando una risorsa. Filtrare scegliendo la *Sottoscrizione*, il *Tipo di risorsa* e infine la *Risorsa* necessaria. 

   ![Selezionare una risorsa](media/alerts-log/Alert-SelectResourceLog.png)

1. Verificare che il **tipo di risorsa** sia un'origine di analisi, ad esempio *log Analytics* o *Application Insights* e il tipo di segnale come *log*. Fare clic su **Done**. Usare quindi il pulsante **Aggiungi criteri** per visualizzare l'elenco delle opzioni di segnale disponibili per la risorsa. Trovare e scegliere l'opzione di **Ricerca log personalizzata** per *log Analytics* o *Application Insights*, a seconda della posizione in cui si trovano i dati per gli avvisi del log.

   ![Selezionare una risorsa - ricerca log personalizzata](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > 
   > Avvisi può importare query di Analytics come tipo di segnale **Log (query salvata)**, come illustrato nella figura precedente. Quindi, gli utenti possono perfezionare la query in Analytics e salvarli per un uso futuro negli avvisi. Per altri dettagli sull'uso delle query salvate, vedere [uso di query di log in monitoraggio di Azure](../log-query/log-query-overview.md) e [query condivise in Application Insights Analytics](../app/app-insights-overview.md).

1. Una volta selezionata, creare la query di avviso nel campo della **query di ricerca** . Se la sintassi della query non è corretta, il campo Visualizza ed esegue un errore in rosso. 

1. Se la sintassi della query è corretta, i dati cronologici per la query vengono visualizzati come un grafico con l'opzione per modificare l'intervallo di tempo dalle ultime sei ore alla settimana precedente.

    ![Configurare una regola di avviso](media/alerts-log/AlertsPreviewAlertLog.png)

   La visualizzazione dei dati cronologici viene visualizzata solo se i risultati della query sono dettagliati sul tempo. Se la query restituisce dati riepilogati o valori di colonna specifici, la visualizzazione Mostra un singolo tracciato.
  
   Per le misurazioni della metrica usando Application Insights o [l'API log Analytics](/rest/api/monitor/scheduledqueryrules), è possibile specificare quale variabile specifica per raggruppare i dati usando l'opzione **aggrega in base a** . come illustrato di seguito: 
  
   ![opzione aggrega in base a](media/alerts-log/aggregate-on.png)



1. Scegliere quindi la condizione, l'aggregazione e la soglia della **logica di avviso** . 

1. Scegliere il periodo di tempo in cui valutare la condizione specificata, usando l'opzione **period** . 

1. Scegliere la frequenza con cui l'avviso viene eseguito in **frequenza**. 

    Gli **avvisi di log** possono basarsi su:
    - [Numero di record](../../azure-monitor/platform/alerts-unified-log.md#number-of-results-alert-rules): viene creato un avviso se il numero di record restituiti dalla query è maggiore o minore del valore specificato.
    - [Unità di misura della metrica](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules): viene creato un avviso se ogni *valore di aggregazione* nei risultati supera il valore di soglia specificato ed è *raggruppato* in base al valore scelto. Il numero di violazioni per un avviso è il numero di volte in cui viene superata la soglia nel periodo di tempo scelto. È possibile specificare le violazioni totali per qualsiasi combinazione di violazioni nei set di risultati o le violazioni consecutive necessarie che devono verificarsi in campioni consecutivi.


1. Fare clic su **Done**. 

1. Definire un nome per l'avviso nel campo **Nome regola di avviso** insieme a una **Descrizione** che descrive in dettaglio le specifiche per il valore di avviso e **gravità** dalle opzioni fornite. Questi dettagli vengono riutilizzati in tutti i messaggi di posta elettronica di avviso, le notifiche o i push eseguiti da monitoraggio di Azure. Inoltre, è possibile scegliere di attivare immediatamente la regola di avviso durante la creazione facendo clic su **Abilita regola al momento della creazione**.

1. Scegliere se si desidera **disattivare gli avvisi** per un determinato periodo di tempo.  Quando si attiva l'eliminazione per la regola di avviso, le azioni della regola vengono disabilitate per un periodo di tempo definito dopo la creazione di un nuovo avviso. La regola viene comunque eseguita e crea record di avviso purché i criteri siano soddisfatti. Questa impostazione consente di correggere il problema senza eseguire azioni duplicate.

   ![Elimina avvisi per gli avvisi del log](media/alerts-log/AlertsPreviewSuppress.png)

    > [!TIP]
    > Specificare un valore di avviso di eliminazione maggiore della frequenza di avviso per assicurarsi che le notifiche vengano arrestate senza sovrapposizione

1. Come terzo e ultimo passaggio, specificare se la regola di avviso deve attivare uno o più **gruppi di azioni** quando viene soddisfatta la condizione di avviso. È possibile scegliere qualsiasi gruppo di azioni esistente o crearne uno nuovo. Con i gruppi di azioni è possibile inviare una serie di azioni, ad esempio inviare messaggi di posta elettronica, inviare SMS, chiamare webhook, correggere l'uso di Azure manuali operativi, effettuare il push allo strumento ITSM e altro ancora. Altre informazioni sui [gruppi di azioni](action-groups.md).

    > [!NOTE]
    > Vedere i [limiti del servizio di sottoscrizione di Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) per i limiti sulle azioni che è possibile eseguire.  

    Alcune funzionalità aggiuntive sono disponibili per eseguire l'override delle azioni predefinite:

    - **Notifica tramite posta elettronica**: sostituisce l'oggetto del messaggio di *posta* elettronica inviato tramite il gruppo di azioni. Non è possibile modificare il corpo del messaggio e questo campo **non** è destinato agli indirizzi di posta elettronica.
    - **Includi payload JSON personalizzato**: esegue l'override del JSON webhook usato dai gruppi di azioni presupponendo che il gruppo di azioni contenga un tipo di webhook. Per altre informazioni sui formati dei webhook, vedere [azione webhook per gli avvisi del log](../../azure-monitor/platform/alerts-log-webhook.md). L'opzione di visualizzazione Webhook consente di verificare il formato tramite i dati JSON di esempio.

        ![Override dell'azione per gli avvisi del log](media/alerts-log/AlertsPreviewOverrideLog.png)


1. Se tutti i campi sono validi e hanno un segno di spunta verde, il pulsante **Crea regola di avviso** può essere selezionato e viene creato un avviso in Monitoraggio di Azure - Avvisi. Tutti gli avvisi possono essere visualizzati nella dashboard di Avvisi.

     ![Creazione di regole](media/alerts-log/AlertsPreviewCreate.png)

     Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

Gli utenti possono anche completare la query di analisi in [log Analytics](../log-query/log-query-overview.md) e quindi eseguirne il push per creare un avviso tramite il pulsante "Imposta avviso", quindi seguendo le istruzioni del passaggio 6 nell'esercitazione precedente.

 ![Log Analytics - Imposta avviso](media/alerts-log/AlertsAnalyticsCreate.png)

### <a name="view--manage-log-alerts-in-azure-portal"></a>Visualizzare e gestire gli avvisi del log nel portale di Azure

1. Nel [portale](https://portal.azure.com/)selezionare **Monitoraggio** e nella sezione MONITORAGGIO scegliere **Avvisi**.

1. Viene visualizzato il **dashboard degli avvisi** , in cui tutti gli avvisi di Azure (inclusi gli avvisi del log) vengono visualizzati in una bacheca singola; inclusione di ogni istanza di quando viene attivata la regola di avviso del log. Per altre informazioni, consultare [Gestione avvisi](https://aka.ms/managealertinstances).
    > [!NOTE]
    > Le regole di avviso del log comprendono la logica personalizzata basata su query fornita dagli utenti e quindi senza stato risolto. Per questo motivo viene generata ogni volta che vengono soddisfatte le condizioni specificate nella regola di avviso del log.

1. Selezionare il pulsante **Gestisci regole** nella barra superiore per passare alla sezione di gestione delle regole in cui sono elencate tutte le regole di avviso create, inclusi gli avvisi che sono stati disabilitati.
    ![gestione delle regole di avviso](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-azure-resource-template"></a>Gestione degli avvisi del log tramite il modello di risorsa di Azure

Gli avvisi dei log in Monitoraggio di Azure sono associati al tipo di risorsa `Microsoft.Insights/scheduledQueryRules/`. Per altre informazioni su questo tipo di risorsa, vedere il [riferimento all'API per regole di query pianificate in Monitoraggio di Azure](/rest/api/monitor/scheduledqueryrules/). Gli avvisi dei log per Application Insights o Log Analytics possono essere creati usando l'[API Regole di query pianificate](/rest/api/monitor/scheduledqueryrules/).

> [!NOTE]
> Gli avvisi dei log per Log Analytics possono essere gestiti anche usando l'[API degli avvisi di Log Analytics](api-alerts.md) legacy e i modelli legacy di [avvisi e di ricerche salvate di Log Analytics](../insights/solutions.md). Per altre informazioni sull'uso della nuova API ScheduledQueryRules illustrata qui in dettaglio per impostazione predefinita, vedere [Passare alla nuova API per gli avvisi di Log Analytics](alerts-log-api-switch.md).


### <a name="sample-log-alert-creation-using-azure-resource-template"></a>Creazione di avvisi di log di esempio tramite il modello di risorse di Azure

Di seguito viene riportata la struttura per la [creazione di regole di query pianificate](/rest/api/monitor/scheduledqueryrules/createorupdate) basate su modello di risorse in cui viene usata una query di ricerca log standard di [regola di avviso Numero di risultati](alerts-unified-log.md#number-of-results-alert-rules) con un set di dati di esempio come variabili.

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

Di seguito viene riportata la struttura per la [creazione di regole di query pianificate](/rest/api/monitor/scheduledqueryrules/createorupdate) basate su modello di risorse in cui viene usata una [query di ricerca log tra risorse](../../azure-monitor/log-query/cross-workspace-query.md) di [avviso di log di tipo Unità di misura della metrica](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules) con un set di dati di esempio come variabili.

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
> Quando si usano query tra risorse in un avviso di log, l'uso di [authorizedResources](/rest/api/monitor/scheduledqueryrules/createorupdate#source) è obbligatorio e l'utente deve avere accesso all'elenco delle risorse indicate.

Per usare il codice JSON di esempio in questa procedura dettagliata, è possibile salvarlo con il nome sampleScheduledQueryRule.json. Può essere distribuito usando [Azure Resource Manager nel portale di Azure](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="managing-log-alerts-using-powershell"></a>Gestione degli avvisi di log con PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Monitoraggio di Azure: l' [API per le regole di query pianificate](/rest/api/monitor/scheduledqueryrules/) è un'API REST ed è completamente compatibile con Azure Resource Manager API REST. E i cmdlet di PowerShell elencati di seguito sono disponibili per sfruttare l' [API pianificate](/rest/api/monitor/scheduledqueryrules/)per le regole di query.

- [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) : cmdlet di PowerShell per creare una nuova regola di avviso del log.
- [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) : cmdlet di PowerShell per aggiornare una regola di avviso del log esistente.
- [New-AzScheduledQueryRuleSource](/powershell/module/az.monitor/new-azscheduledqueryrulesource) : cmdlet di PowerShell per creare o aggiornare un oggetto che specifica i parametri di origine per un avviso del log. Usato come input mediante il cmdlet [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) e [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleSchedule](/powershell/module/az.monitor/new-azscheduledqueryruleschedule): cmdlet di PowerShell per creare o aggiornare un oggetto che specifica i parametri di pianificazione per un avviso del log. Usato come input mediante il cmdlet [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) e [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) : cmdlet di PowerShell per creare o aggiornare un oggetto che specifica i parametri di azione per un avviso del log. Usato come input mediante il cmdlet [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) e [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleAznsActionGroup](/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : cmdlet di PowerShell per creare o aggiornare un oggetto che specifica i parametri dei gruppi di azioni per un avviso del log. Usato come input dal cmdlet [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) .
- [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : cmdlet di PowerShell per creare o aggiornare un oggetto specificando i parametri della condizione trigger per l'avviso del log. Usato come input dal cmdlet [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) .
- [New-AzScheduledQueryRuleLogMetricTrigger](/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : cmdlet di PowerShell per creare o aggiornare un oggetto specificando i parametri della condizione del trigger di metrica per l' [Avviso del log di tipo misurazione metrica](../../azure-monitor/platform/alerts-unified-log.md#metric-measurement-alert-rules). Usato come input dal cmdlet [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) .
- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule) : cmdlet di PowerShell per elencare le regole di avviso del log esistenti o una regola di avviso del log specifica
- [Update-AzScheduledQueryRule](/powershell/module/az.monitor/update-azscheduledqueryrule) : cmdlet di PowerShell per abilitare o disabilitare la regola di avviso del log
- [Remove-AzScheduledQueryRule](/powershell/module/az.monitor/remove-azscheduledqueryrule): cmdlet di PowerShell per eliminare una regola di avviso del log esistente

> [!NOTE]
> I cmdlet di PowerShell per ScheduledQueryRules possono gestire solo il cmdlet creato dalle regole oppure l'API di monitoraggio di Azure per [le regole di query pianificate](/rest/api/monitor/scheduledqueryrules/). Le regole di avviso del log create usando l' [api log Analytics Alert](api-alerts.md) legacy e i modelli legacy di [log Analytics le ricerche salvate e gli avvisi](../insights/solutions.md) possono essere gestiti usando i cmdlet di PowerShell di ScheduledQueryRules solo dopo che l'utente ha attivato le [Preferenze API per log Analytics avvisi](alerts-log-api-switch.md).

Di seguito sono illustrati i passaggi per la creazione di una regola di avviso del log di esempio mediante i cmdlet di PowerShell per scheduledQueryRules.

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

## <a name="managing-log-alerts-using-cli-or-api"></a>Gestione degli avvisi di log con CLI o API

Monitoraggio di Azure: l' [API per le regole di query pianificate](/rest/api/monitor/scheduledqueryrules/) è un'API REST ed è completamente compatibile con Azure Resource Manager API REST. Quindi, può essere usato tramite PowerShell usando Gestione risorse comandi per l'interfaccia della riga di comando di Azure.


> [!NOTE]
> Gli avvisi dei log per Log Analytics possono essere gestiti anche usando l'[API degli avvisi di Log Analytics](api-alerts.md) legacy e i modelli legacy di [avvisi e di ricerche salvate di Log Analytics](../insights/solutions.md). Per altre informazioni sull'uso della nuova API ScheduledQueryRules illustrata qui in dettaglio per impostazione predefinita, vedere [Passare alla nuova API per gli avvisi di Log Analytics](alerts-log-api-switch.md).

Gli avvisi del log attualmente non hanno comandi dell'interfaccia della riga di comando dedicati. come illustrato di seguito, è possibile usare Azure Resource Manager comando CLI per il modello di risorsa di esempio illustrato in precedenza (sampleScheduledQueryRule.js) nella sezione modello di risorsa:

```azurecli
az group deployment create --resource-group contosoRG --template-file sampleScheduledQueryRule.json
```

Se l'operazione è stata eseguita correttamente, sarà restituito il valore 201 a indicare la creazione della nuova regola di avviso. Se invece è stata modificata una regola di avviso esistente, sarà restituito il valore 200.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sugli [avvisi del log in avvisi di Azure](../../azure-monitor/platform/alerts-unified-log.md)
* Comprendere le [azioni webhook per gli avvisi del log](../../azure-monitor/platform/alerts-log-webhook.md)
* Altre informazioni su [Application Insights](../log-query/log-query-overview.md)
* Altre informazioni sulle [query di log](../log-query/log-query-overview.md).
