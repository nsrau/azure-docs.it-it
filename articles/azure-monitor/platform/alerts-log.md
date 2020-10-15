---
title: Creare, visualizzare e gestire gli avvisi di log con monitoraggio di Azure | Microsoft Docs
description: Usare monitoraggio di Azure per creare, visualizzare e gestire le regole di avviso del log
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 07/29/2019
ms.subservice: alerts
ms.openlocfilehash: 40cf46746587cbd221bd958ccb528c9e40cf18bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91299421"
---
# <a name="create-view-and-manage-log-alerts-using-azure-monitor"></a>Creare, visualizzare e gestire gli avvisi del log tramite Monitoraggio di Azure

## <a name="overview"></a>Panoramica

Gli avvisi del log consentono agli utenti di usare una query [log Analytics](../log-query/get-started-portal.md) per valutare le risorse registra ogni frequenza impostata e generare un avviso in base ai risultati. Le regole possono attivare una o più azioni utilizzando i [gruppi di azioni](./action-groups.md). [Altre informazioni sulla funzionalità e la terminologia degli avvisi del log](alerts-unified-log.md).

Questo articolo illustra come creare e gestire gli avvisi di log con monitoraggio di Azure. Le regole di avviso sono definite da tre componenti:
- Target: una risorsa di Azure specifica da monitorare.
- Criteri: logica da valutare. Se soddisfatta, viene generato l'avviso.  
- Azione: notifiche o automazione-posta elettronica, SMS, webhook e così via.

È anche possibile creare regole di avviso del log usando Azure Resource Manager modelli, descritti in [un articolo separato](alerts-log-create-templates.md).

> [!NOTE]
> I dati di log da un' [area di lavoro log Analytics](../log-query/get-started-portal.md) possono essere inviati all'archivio delle metriche di monitoraggio di Azure. Gli avvisi di metrica hanno un [comportamento diverso](alerts-metric-overview.md), che può essere più utile a seconda dei dati in uso. Per informazioni su cosa e come è possibile indirizzare i log alle metriche, vedere [avviso di metrica per i log](alerts-metric-logs.md).

## <a name="create-a-log-alert-rule-with-the-azure-portal"></a>Creare una regola per gli avvisi relativi ai log con il portale di Azure

Ecco i passaggi per iniziare a scrivere query per gli avvisi:

1. Passare alla risorsa su cui si vuole inviare l'avviso.
1. In **monitoraggio**selezionare **log**.
1. Eseguire una query sui dati del log che possono indicare il problema. È possibile usare l' [argomento degli esempi di query di avviso](../log-query/saved-queries.md) per capire cosa è possibile individuare o [iniziare a scrivere una query personalizzata](../log-query/get-started-portal.md). È anche possibile trovare [informazioni su come creare query di avviso ottimizzate](alerts-log-query.md).
1. Premere il pulsante "+ nuovo regola di avviso" per avviare il flusso di creazione dell'avviso.

    ![Log Analytics - Imposta avviso](media/alerts-log/AlertsAnalyticsCreate.png)

> [!NOTE]
> Si consiglia di creare avvisi su larga scala, quando si usa la modalità di accesso alle risorse per i log, che viene eseguita in più risorse usando un gruppo di risorse o un ambito di sottoscrizione. L'invio di avvisi su larga scala riduce il sovraccarico di gestione regole. Per essere in grado di specificare come destinazione le risorse, includere la colonna ID risorsa nei risultati. [Altre informazioni sulla suddivisione degli avvisi in base alle dimensioni](alerts-unified-log.md#split-by-alert-dimensions).

### <a name="log-alert-for-log-analytics-and-application-insights"></a>Avviso di log per Log Analytics e Application Insights

1. Se la sintassi della query è corretta, i dati cronologici per la query vengono visualizzati come un grafico con l'opzione per modificare il periodo del grafico dalle ultime sei ore alla settimana precedente.
 
    Se i risultati della query contengono dati riepilogati o colonne specifiche del [progetto](/azure/kusto/query/projectoperator) senza colonna Time, nel grafico viene visualizzato un singolo valore.

    ![Configurare una regola di avviso](media/alerts-log/AlertsPreviewAlertLog.png)

1. Consente di scegliere l'intervallo di tempo in cui valutare la condizione specificata, usando l'opzione [**period**](alerts-unified-log.md#query-time-range) .

1. Gli avvisi del log possono essere basati su due tipi di [**misure**](alerts-unified-log.md#measure):
    1. **Numero di risultati: numero** di record restituiti dalla query.
    1. **Misurazione metrica**  -  *Valore di aggregazione* calcolato utilizzando il riepilogo raggruppato per espressioni selezionate e la selezione [bin ()](/azure/kusto/query/binfunction) . Ad esempio:

    ```Kusto
    // Reported errors
    union Event, Syslog // Event table stores Windows event records, Syslog stores Linux records
    | where EventLevelName == "Error" // EventLevelName is used in the Event (Windows) records
    or SeverityLevel== "err" // SeverityLevel is used in Syslog (Linux) records
    | summarize AggregatedValue = count() by Computer, bin(TimeGenerated, 15m)
    ```

1. Per la logica di avviso delle misurazioni metrica, è possibile specificare facoltativamente come [suddividere gli avvisi in base alle dimensioni](alerts-unified-log.md#split-by-alert-dimensions) usando l'opzione **aggrega su** . L'espressione di raggruppamento di righe deve essere univoca e ordinata.

    > [!NOTE]
    > Poiché [bin ()](/azure/kusto/query/binfunction) può produrre intervalli di tempo non uniformi, il servizio avvisi convertirà automaticamente la funzione [bin ()](/azure/kusto/query/binfunction) in [bin_at funzione ()](/azure/kusto/query/binatfunction) con il tempo appropriato in fase di esecuzione, per garantire i risultati con un punto fisso.

    > [!NOTE]
    > La divisione in base alle dimensioni di avviso è disponibile solo per l'API scheduledQueryRules corrente. Se si usa l' [API log Analytics Alert](api-alerts.md)legacy, sarà necessario passare a. [Altre informazioni sul cambio](./alerts-log-api-switch.md). Gli avvisi incentrati sulle risorse alla scala sono supportati solo nella versione dell'API `2020-05-01-preview` e versioni successive.

    ![opzione aggrega in base a](media/alerts-log/aggregate-on.png)

1. Quindi, in base ai dati di anteprima, impostare l' [ **operatore**, il **valore di soglia**](alerts-unified-log.md#threshold-and-operator)e la [**frequenza**](alerts-unified-log.md#frequency).

1. Facoltativamente, è anche possibile impostare il [numero di violazioni per attivare un avviso](alerts-unified-log.md#number-of-violations-to-trigger-alert) utilizzando **violazioni totali o consecutive**.

1. Selezionare **Operazione completata**. 

1. Definire il **nome**e la **Descrizione**della regola di avviso e selezionare la **gravità**dell'avviso. Questi dettagli vengono usati in tutte le azioni di avviso. Inoltre, è possibile scegliere di non attivare la regola di avviso durante la creazione selezionando **Abilita regola al momento della creazione**.

1. Scegliere se si desidera eliminare le azioni regola per un periodo di tempo dopo la generazione di un avviso, utilizzare l'opzione non [**visualizzare avvisi**](alerts-unified-log.md#state-and-resolving-alerts) . La regola verrà comunque eseguita e creerà avvisi, ma le azioni non verranno attivate per evitare il rumore. Il valore di azioni mute deve essere maggiore della frequenza di avviso per essere effettivo.

    ![Elimina avvisi per gli avvisi del log](media/alerts-log/AlertsPreviewSuppress.png)

1. Consente di specificare se la regola di avviso deve attivare uno o più [**gruppi di azioni**](action-groups.md#webhook) quando viene soddisfatta la condizione di avviso.

    > [!NOTE]
    > Vedere i [limiti del servizio di sottoscrizione di Azure](../../azure-resource-manager/management/azure-subscription-service-limits.md) per i limiti sulle azioni che è possibile eseguire.  

1. Facoltativamente, è possibile personalizzare le azioni nelle regole di avviso del log:

    - **Oggetto del messaggio**di posta elettronica personalizzato: sostituisce l' *oggetto del messaggio* di posta elettronica delle azioni. Non è possibile modificare il corpo del messaggio e questo campo **non è per gli indirizzi di posta elettronica**.
    - **Includi payload JSON personalizzato**: esegue l'override del JSON webhook usato dai gruppi di azioni presupponendo che il gruppo di azioni contenga un'azione webhook. Altre informazioni sull' [azione webhook per gli avvisi del log](./alerts-log-webhook.md).

    ![Override dell'azione per gli avvisi del log](media/alerts-log/AlertsPreviewOverrideLog.png)

1. Se tutti i campi sono impostati correttamente, il pulsante **Crea regola di avviso** può essere selezionato e viene creato un avviso.

    Dopo pochi minuti l'avviso è funzionante e si attiva come descritto in precedenza.

    ![Creazione di regole](media/alerts-log/AlertsPreviewCreate.png)

#### <a name="creating-log-alert-for-log-analytics-and-application-insights-from-the-alerts-management"></a>Creazione di un avviso del log per Log Analytics e Application Insights dalla gestione degli avvisi

> [!NOTE]
> La creazione dalla gestione degli avvisi non è attualmente supportata per i log incentrati sulle risorse

1. Nel [portale](https://portal.azure.com/)selezionare **monitoraggio** , quindi scegliere **avvisi**.

    ![Monitoraggio](media/alerts-log/AlertsPreviewMenu.png)

1. Selezionare **nuova regola di avviso**. 

    ![Aggiungi avviso](media/alerts-log/AlertsPreviewOption.png)

1. Verrà visualizzato il riquadro **Crea avviso** . È costituito da quattro parti: 
    - Risorsa a cui viene applicato l'avviso.
    - Condizione da controllare.
    - Azioni da eseguire se la condizione è true.
    - Dettagli per denominare e descrivere l'avviso. 

    ![Creare una regola](media/alerts-log/AlertsPreviewAdd.png)

1. Premere il pulsante **Seleziona risorsa** . Filtrare scegliendo la *sottoscrizione*, il *tipo di risorsa*e selezionare una risorsa. Verificare che la risorsa disponga di log disponibili.

   ![Selezionare una risorsa](media/alerts-log/Alert-SelectResourceLog.png)

1. Usare quindi il pulsante Aggiungi **condizione** per visualizzare l'elenco delle opzioni di segnale disponibili per la risorsa. Selezionare l'opzione di **Ricerca log personalizzato** .

   ![Selezionare una risorsa - ricerca log personalizzata](media/alerts-log/AlertsPreviewResourceSelectionLog.png)

   > [!NOTE]
   > Il portale degli avvisi elenca le query salvate da Log Analytics e Application Insights e possono essere usate come query di avviso del modello.

1. Una volta selezionata, scrivere, incollare o modificare la query di avviso nel campo della **query di ricerca** .

1. Continuare con i passaggi successivi descritti nell' [ultima sezione](#log-alert-for-log-analytics-and-application-insights).

### <a name="log-alert-for-all-other-resource-types"></a>Avviso di log per tutti gli altri tipi di risorse

> [!NOTE]
> Non sono attualmente previsti addebiti aggiuntivi per la versione dell'API `2020-05-01-preview` e gli avvisi del log incentrato sulle risorse.  I prezzi per le funzionalità in anteprima verranno annunciati in futuro e un avviso fornito prima di iniziare la fatturazione. Se si sceglie di continuare a usare la nuova versione dell'API e gli avvisi del log incentrato sulle risorse dopo il periodo di preavviso, l'addebito sarà addebitato alla tariffa applicabile.

1. Iniziare dalla scheda **condizione** :

    1. Verificare che la [**misura**](alerts-unified-log.md#measure), il [**tipo di aggregazione**](alerts-unified-log.md#aggregation-type)e la [**granularità di aggregazione**](alerts-unified-log.md#aggregation-granularity) siano corretti. 
        1. Per impostazione predefinita, la regola conta il numero di risultati negli ultimi 5 minuti.
        1. Se si rilevano i risultati delle query riepilogate, la regola verrà aggiornata automaticamente entro pochi secondi per poterla acquisire.

    1. Scegliere la [suddivisione degli avvisi in base alle dimensioni](alerts-unified-log.md#split-by-alert-dimensions), se necessario: 
       - La **colonna ID risorsa** viene selezionata automaticamente, se rilevata, e modifica il contesto dell'avviso attivato sulla risorsa del record. 
       - La **colonna ID risorsa** può essere deselezionata per generare avvisi per la sottoscrizione o i gruppi di risorse. La deselezione è utile quando i risultati della query sono basati su risorse incrociate. Ad esempio, una query che verifica se il 80% delle macchine virtuali del gruppo di risorse sta riscontrando un utilizzo elevato della CPU.
       - È possibile selezionare anche fino a sei suddivisioni aggiuntive per qualsiasi tipo di colonna numerico o di testo utilizzando la tabella dimensioni.
       - Gli avvisi vengono generati separatamente in base alla suddivisione in base a combinazioni univoche e il payload degli avvisi include queste informazioni.
    
        ![Selezione dei parametri di aggregazione e suddivisione](media/alerts-log/select-aggregation-parameters-and-splitting.png)

    1. Il grafico di **Anteprima** Mostra i risultati delle valutazioni delle query nel tempo. È possibile modificare il periodo del grafico o selezionare diverse serie temporali derivanti dalla suddivisione univoca degli avvisi in base alle dimensioni.

        ![Grafico di anteprima](media/alerts-log/preview-chart.png)

    1. Quindi, in base ai dati di anteprima, impostare la **logica di avviso**. [ **Operatore**, **valore di soglia**](alerts-unified-log.md#threshold-and-operator)e [**frequenza**](alerts-unified-log.md#frequency).

        ![Grafico di anteprima con soglia e logica di avviso](media/alerts-log/chart-and-alert-logic.png)

    1. Facoltativamente, è possibile impostare [**il numero di violazioni per attivare l'avviso**](alerts-unified-log.md#number-of-violations-to-trigger-alert) nella sezione **Opzioni avanzate** .
    
        ![Advanced Options](media/alerts-log/advanced-options.png)

1. Nella scheda **azioni** selezionare o creare i [gruppi di azioni](action-groups.md)necessari.

    ![Scheda azioni](media/alerts-log/actions-tab.png)

1. Nella scheda **Dettagli** definire i dettagli della **regola di avviso**e i **Dettagli del progetto**. Facoltativamente, è possibile impostare se non **avviare l'esecuzione**o [**disattivare le azioni**](alerts-unified-log.md#state-and-resolving-alerts) per un periodo di tempo dopo il quale la regola di avviso viene attivata.

    > [!NOTE]
    > Le regole di avviso del log sono attualmente senza stato e generano un'azione ogni volta che viene creato un avviso, a meno che non sia stato definito muting.

    ![Scheda Dettagli](media/alerts-log/details-tab.png)

1. Nella scheda **tag** impostare gli eventuali tag necessari per la risorsa della regola di avviso.

    ![Scheda Tag](media/alerts-log/tags-tab.png)

1. Nella scheda **Verifica e crea** , viene eseguita una convalida e viene segnalata la presenza di eventuali problemi. Esaminare e approvare la definizione della regola.
1. Se tutti i campi sono corretti, selezionare il pulsante **Crea** e completare la creazione della regola di avviso. Tutti gli avvisi possono essere visualizzati dalla gestione degli avvisi.
 
    ![Esaminare e creare la scheda](media/alerts-log/review-and-create-tab.png)

## <a name="view--manage-log-alerts-in-azure-portal"></a>Visualizzare e gestire gli avvisi del log nel portale di Azure

1. Nel [portale](https://portal.azure.com/)selezionare la risorsa pertinente o il servizio di **monitoraggio** . Quindi selezionare **avvisi** nella sezione monitoraggio.

1. Gestione avvisi consente di visualizzare tutti gli avvisi generati. Ulteriori [informazioni sulla gestione degli avvisi](alerts-managing-alert-instances.md).

    > [!NOTE]
    > Le regole di avviso del log sono attualmente senza stato e non vengono [risolte](alerts-unified-log.md#state-and-resolving-alerts).

1. Per modificare le regole, fare clic sul pulsante **Gestisci regole** nella barra superiore:

    ![ gestione delle regole di avviso](media/alerts-log/manage-alert-rules.png)

## <a name="managing-log-alerts-using-powershell"></a>Gestione degli avvisi di log con PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!NOTE]
> PowerShell non è attualmente supportato nella versione dell'API `2020-05-01-preview`

I cmdlet di PowerShell elencati di seguito sono disponibili per gestire le regole con l' [API delle regole di query pianificate](/rest/api/monitor/scheduledqueryrules/).

- [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) : cmdlet di PowerShell per creare una nuova regola di avviso del log.
- [Set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) : cmdlet di PowerShell per aggiornare una regola di avviso del log esistente.
- [New-AzScheduledQueryRuleSource](/powershell/module/az.monitor/new-azscheduledqueryrulesource) : cmdlet di PowerShell per creare o aggiornare un oggetto che specifica i parametri di origine per un avviso del log. Usato come input mediante il cmdlet [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) e [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleSchedule](/powershell/module/az.monitor/new-azscheduledqueryruleschedule): cmdlet di PowerShell per creare o aggiornare un oggetto che specifica i parametri di pianificazione per un avviso del log. Usato come input mediante il cmdlet [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) e [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) : cmdlet di PowerShell per creare o aggiornare un oggetto che specifica i parametri di azione per un avviso del log. Usato come input mediante il cmdlet [New-AzScheduledQueryRule](/powershell/module/az.monitor/new-azscheduledqueryrule) e [set-AzScheduledQueryRule](/powershell/module/az.monitor/set-azscheduledqueryrule) .
- [New-AzScheduledQueryRuleAznsActionGroup](/powershell/module/az.monitor/new-azscheduledqueryruleaznsactiongroup) : cmdlet di PowerShell per creare o aggiornare un oggetto che specifica i parametri dei gruppi di azioni per un avviso del log. Usato come input dal cmdlet [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) .
- [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) : cmdlet di PowerShell per creare o aggiornare un oggetto specificando i parametri della condizione trigger per l'avviso del log. Usato come input dal cmdlet [New-AzScheduledQueryRuleAlertingAction](/powershell/module/az.monitor/new-azscheduledqueryrulealertingaction) .
- [New-AzScheduledQueryRuleLogMetricTrigger](/powershell/module/az.monitor/new-azscheduledqueryrulelogmetrictrigger) : cmdlet di PowerShell per creare o aggiornare un oggetto specificando i parametri della condizione del trigger di metrica per l' [Avviso del log di tipo misurazione metrica](./alerts-unified-log.md#calculation-of-measure-based-on-a-numeric-column-such-as-cpu-counter-value). Usato come input dal cmdlet [New-AzScheduledQueryRuleTriggerCondition](/powershell/module/az.monitor/new-azscheduledqueryruletriggercondition) .
- [Get-AzScheduledQueryRule](/powershell/module/az.monitor/get-azscheduledqueryrule) : cmdlet di PowerShell per elencare le regole di avviso del log esistenti o una regola di avviso del log specifica
- [Update-AzScheduledQueryRule](/powershell/module/az.monitor/update-azscheduledqueryrule) : cmdlet di PowerShell per abilitare o disabilitare la regola di avviso del log
- [Remove-AzScheduledQueryRule](/powershell/module/az.monitor/remove-azscheduledqueryrule): cmdlet di PowerShell per eliminare una regola di avviso del log esistente

> [!NOTE]
> I cmdlet di PowerShell per ScheduledQueryRules possono gestire solo le regole create nell' [API delle regole di query pianificate](/rest/api/monitor/scheduledqueryrules/)correnti. Le regole di avviso del log create tramite l' [API di avviso log Analytics](api-alerts.md) legacy possono essere gestite solo tramite PowerShell solo dopo il [trasferimento all'API delle regole di query pianificate](alerts-log-api-switch.md).

Di seguito sono riportati i passaggi di esempio per la creazione di una regola di avviso del log usando PowerShell:

```powershell
$source = New-AzScheduledQueryRuleSource -Query 'Heartbeat | summarize AggregatedValue = count() by bin(TimeGenerated, 5m), _ResourceId' -DataSourceId "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.OperationalInsights/workspaces/servicews"

$schedule = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 15 -TimeWindowInMinutes 30

$metricTrigger = New-AzScheduledQueryRuleLogMetricTrigger -ThresholdOperator "GreaterThan" -Threshold 2 -MetricTriggerType "Consecutive" -MetricColumn "_ResourceId"

$triggerCondition = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator "LessThan" -Threshold 5 -MetricTrigger $metricTrigger

$aznsActionGroup = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup "/subscriptions/a123d7efg-123c-1234-5678-a12bc3defgh4/resourceGroups/contosoRG/providers/microsoft.insights/actiongroups/sampleAG" -EmailSubject "Custom email subject" -CustomWebhookPayload "{ `"alert`":`"#alertrulename`", `"IncludeSearchResults`":true }"

$alertingAction = New-AzScheduledQueryRuleAlertingAction -AznsAction $aznsActionGroup -Severity "3" -Trigger $triggerCondition

New-AzScheduledQueryRule -ResourceGroupName "contosoRG" -Location "Region Name for your Application Insights App or Log Analytics Workspace" -Action $alertingAction -Enabled $true -Description "Alert description" -Schedule $schedule -Source $source -Name "Alert Name"
```

È anche possibile creare l'avviso del log usando un [modello e](./alerts-log-create-templates.md) i file dei parametri usando PowerShell:

```powershell
Connect-AzAccount

Select-AzSubscription -SubscriptionName <yourSubscriptionName>

New-AzResourceGroupDeployment -Name AlertDeployment -ResourceGroupName ResourceGroupofTargetResource `
  -TemplateFile mylogalerttemplate.json -TemplateParameterFile mylogalerttemplate.parameters.json
```

## <a name="managing-log-alerts-using-cli"></a>Gestione degli avvisi di log con CLI

> [!NOTE]
> Il supporto dell'interfaccia della riga di comando di Azure è disponibile solo per la versione API scheduledQueryRules `2020-05-01-preview` e versioni successive. La versione dell'API precedente può usare l'interfaccia della riga di comando Azure Resource Manager con i modelli come descritto di seguito. Se si usa l' [API log Analytics Alert](api-alerts.md)legacy, sarà necessario passare a usare l'interfaccia della riga di comando. [Altre informazioni sul cambio](./alerts-log-api-switch.md).

Nelle sezioni precedenti è stato descritto come creare, visualizzare e gestire le regole di avviso del log utilizzando portale di Azure. Questa sezione descrive come eseguire la stessa operazione usando l'[interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli) multipiattaforma. [Azure Cloud Shell](../../cloud-shell/overview.md) è il metodo più rapido per iniziare a usare l'interfaccia della riga di comando di Azure. Per questo articolo verrà usato Cloud Shell.

1. Passare a portale di Azure, selezionare **cloud Shell**.

1. Al prompt è possibile usare i comandi con l'opzione ``--help`` per altre informazioni sul comando e su come usarlo. Il comando seguente, ad esempio, Mostra l'elenco dei comandi disponibili per la creazione, la visualizzazione e la gestione degli avvisi di log:

    ```azurecli
    az monitor scheduled-query --help
    ```

1. È possibile creare una regola di avviso del log che monitora il numero di errori degli eventi di sistema:

    ```azurecli
    az monitor scheduled-query create -g {ResourceGroup} -n {nameofthealert} --scopes {vm_id} --condition "count \'union Event, Syslog | where TimeGenerated > ago(1h) | where EventLevelName == \"Error\" or SeverityLevel== \"err\"\' > 2" --description {descriptionofthealert}
    ```

1. È possibile visualizzare tutti gli avvisi del log in un gruppo di risorse usando il comando seguente:

    ```azurecli
    az monitor scheduled-query list -g {ResourceGroup}
    ```

1. È possibile visualizzare i dettagli di una particolare regola di avviso del log usando il nome o l'ID della risorsa della regola:

    ```azurecli
    az monitor scheduled-query show -g {ResourceGroup} -n {AlertRuleName}
    ```

    ```azurecli
    az monitor scheduled-query show --ids {RuleResourceId}
    ```

1. È possibile disabilitare una regola di avviso del log usando il comando seguente:

    ```azurecli
    az monitor scheduled-query update -g {ResourceGroup} -n {AlertRuleName} --enabled false
    ```

1. È possibile eliminare una regola di avviso del log usando il comando seguente:

    ```azurecli
    az monitor scheduled-query delete -g {ResourceGroup} -n {AlertRuleName}
    ```

È anche possibile usare Azure Resource Manager interfaccia della riga di comando con i file dei [modelli](./alerts-log-create-templates.md) :

```azurecli
az login

az group deployment create \
    --name AlertDeployment \
    --resource-group ResourceGroupofTargetResource \
    --template-file mylogalerttemplate.json \
    --parameters @mylogalerttemplate.parameters.json
```

Al completamento della creazione, viene restituito 201. In seguito all'esito positivo dell'aggiornamento, viene restituito 200.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sugli [avvisi del log](./alerts-unified-log.md).
* Creare avvisi del log usando [modelli di Azure Resource Manager](./alerts-log-create-templates.md).
* Informazioni sulle [azioni webhook per gli avvisi del log](./alerts-log-webhook.md).
* Altre informazioni sulle [query di log](../log-query/log-query-overview.md).

