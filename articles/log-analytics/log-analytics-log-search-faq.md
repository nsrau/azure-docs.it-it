---
title: "Domande frequenti sulla nuova funzionalità di ricerca log di Log Analytics | Microsoft Docs"
description: "Questo articolo riporta le domande più frequenti riguardanti l'aggiornamento di Log Analytics al nuovo linguaggio di query."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 6dfee26d7585c8ec295a1f0ea1bd0bc14a34cc5a
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2018
---
# <a name="log-analytics-new-log-search-faq-and-known-issues"></a>Nuova ricerca log in Log Analytics - domande frequenti e problemi noti

Questo articolo riporta le domande più frequenti e i problemi noti riguardanti l'aggiornamento di [Log Analytics al nuovo linguaggio di query](log-analytics-log-search-upgrade.md).  Prima di decidere di aggiornare l'area di lavoro, leggere l'intero articolo.


## <a name="alerts"></a>Avvisi

### <a name="question-i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>Domanda: Sono presenti molte regole di avviso. È necessario crearle nuovamente nel nuovo linguaggio dopo l'aggiornamento?  
No, le regole di avviso vengono convertite automaticamente nel nuovo linguaggio di ricerca durante l'aggiornamento.  

### <a name="question-i-have-alert-rules-with-webhook-and-runbook-actions-will-these-continue-to-work-when-i-upgrade"></a>Domanda: Sono presenti regole di avviso con azioni webhook e runbook. Continueranno a funzionare dopo l'aggiornamento?

No, alcune modifiche delle azioni webhook e runbook potrebbero richiedere di cambiare la modalità di esecuzione del payload. Queste modifiche sono state eseguite per standardizzare i vari formati di output e ridurre le dimensioni del payload. Per informazioni dettagliate sui formati, vedere [Aggiungere azioni alle regole di avviso in Log Analytics](log-analytics-alerts-actions.md).


## <a name="computer-groups"></a>Gruppi di computer

### <a name="question-im-getting-errors-when-trying-to-use-computer-groups--has-their-syntax-changed"></a>Domanda: Durante il tentativo di usare gruppi di computer vengono visualizzati degli errori.  La sintassi è stata modificata?
Sì, quando si aggiorna l'area di lavoro, la sintassi per l'uso di gruppi di computer cambia.  Per informazioni dettagliate, vedere [Gruppi di computer nelle ricerche nei log in Log Analytics](log-analytics-computer-groups.md).


## <a name="dashboards"></a>Dashboard

### <a name="question-can-i-still-use-dashboards-in-an-upgraded-workspace"></a>Domanda: È sempre possibile usare i dashboard in un'area di lavoro aggiornata?
Con l'aggiornamento inizierà il processo di deprecazione di **Dashboard**.  È possibile continuare a usare tutti i riquadri aggiunti al dashboard prima dell'aggiornamento dell'area di lavoro, ma non sarà possibile modificarli o crearne di nuovi.  È possibile continuare a creare e modificare le viste tramite [Progettazione viste](log-analytics-view-designer.md), che ha un set di funzionalità più avanzate, oltre a creare dashboard nel portale di Azure.


## <a name="log-searches"></a>Ricerche log

### <a name="question-i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>Domanda: Le ricerche salvate all'esterno dell'area di lavoro aggiornata possono essere convertite automaticamente al nuovo linguaggio di ricerca?
È possibile usare lo strumento di conversione del linguaggio nella pagina della ricerca log per convertire ogni ricerca.  Non è disponibile alcun metodo per convertire automaticamente più ricerche senza aggiornare l'area di lavoro.

### <a name="question-why-are-my-query-results-not-sorted"></a>Domanda: Perché i risultati della query non sono ordinati?
Nel nuovo linguaggio di query, i risultati non sono ordinati per impostazione predefinita.  Usare il comando [Operatore sort](https://go.microsoft.com/fwlink/?linkid=856079) per ordinare i risultati in base a una o più proprietà.

### <a name="question-where-did-the-metrics-view-go-after-i-upgraded"></a>Domanda: Dove è stata spostata la visualizzazione metriche dopo l'aggiornamento?
La visualizzazione metriche offriva una rappresentazione grafica dei dati sulle prestazioni da una ricerca nei log.  Questa visualizzazione non è più disponibile dopo l'aggiornamento.  È possibile usare l'[operatore di rendering](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator) per formattare l'output di una query in un diagramma temporale.

### <a name="question-where-did-minify-go-after-i-upgraded"></a>Domanda: Dove è stata spostata la funzionalità minify dopo l'aggiornamento?
Minify è una funzionalità che offre una visualizzazione riepilogata dei risultati della ricerca.  Dopo l'aggiornamento,l'opzione Minify non viene più visualizzata nel portale Ricerca log.  È possibile ottenere funzionalità simili con il nuovo linguaggio di ricerca tramite [reduce](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/reduce-operator) o [autocluster_v2](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/evaluate-operator/autocluster). 

    Event
    | where TimeGenerated > ago(10h)
    | reduce by RenderedDescription

    Event
    | where TimeGenerated > ago(10h)
    | evaluate autocluster_v2()



## <a name="log-search-api"></a>API di ricerca nei log

### <a name="question-does-the-log-search-api-get-updated-after-i-upgrade"></a>Domanda: L'API di ricerca log viene aggiornata dopo l'aggiornamento?
L'[API di ricerca log](log-analytics-log-search-api.md) legacy non funzionerà più quando si aggiorna l'area di lavoro.  Vedere [API REST di Azure Log Analytics](https://dev.loganalytics.io/) per informazioni dettagliate sulla nuova API.


## <a name="portals"></a>Portali

### <a name="question-should-i-use-the-new-advanced-analytics-portal-or-keep-using-the-log-search-portal"></a>Domanda: È consigliabile usare il nuovo portale Advanced Analytics o continuare a usare il portale di ricerca log?
È possibile visualizzare un confronto tra i due portali in [Portali per la creazione e la modifica di ricerche log in Azure Log Analytics](log-analytics-log-search-portals.md).  Ognuno presenta vantaggi distinti, per cui è possibile scegliere quello più adatto alle proprie esigenze.  È comune per scrivere le query nel portale Advanced Analytics e incollarle in altre posizioni, ad esempio Progettazione viste.  È necessario essere a conoscenza degli [aspetti da considerare](log-analytics-log-search-portals.md#advanced-analytics-portal) quando si esegue questa operazione.


### <a name="question--after-upgrade-i-get-an-error-trying-to-run-queries-and-am-also-seeing-errors-in-my-views"></a>Domanda: Se dopo l'aggiornamento tento di eseguire query viene visualizzato un errore. Vengono visualizzati errori anche nelle viste.

Dopo l'aggiornamento, per l'esecuzione di query Log Analytics il browser richiede l'accesso agli indirizzi seguenti.  Se il browser accede al portale di Azure attraverso un firewall, è necessario abilitare l'accesso a questi indirizzi.

| Uri | IP | Porte |
|:---|:---|:---|
| portal.loganalytics.io | Dinamico | 80,443 |
| api.loganalytics.io    | Dinamico | 80,443 |
| docs.loganalytics.io   | Dinamico | 80,443 |



## <a name="power-bi"></a>Power BI

### <a name="question-does-anything-change-with-powerbi-integration"></a>Domanda: L'aggiornamento implica modifiche per l'integrazione con Power BI?
Sì.  Dopo aver aggiornato l'area di lavoro, il processo per esportare i dati di Log Analytics in Power BI non funzionerà più.  Le pianificazioni esistenti create prima dell'aggiornamento verranno disabilitate.  

Dopo l'aggiornamento, Azure Log Analytics usa la stessa piattaforma di Application Insights e il processo per esportare le query di Log Analytics in Power BI è uguale al [processo usato per esportare le query di Application Insights in Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).  L'esportazione in Power BI ora chiama direttamente l'endpoint API. Ciò consente di ottenere fino a 500.000 righe o 64.000.000 di byte di dati, esportare query lunghe e personalizzare il timeout della query (il timeout predefinito è di 3 minuti , mentre il timeout massimo è di 10 minuti).

## <a name="powershell-cmdlets"></a>Cmdlet PowerShell

### <a name="question-does-the-log-search-powershell-cmdlet-get-updated-after-i-upgrade"></a>Domanda: Il cmdlet PowerShell di ricerca log viene aggiornato dopo l'aggiornamento?
Dopo il completamento dell'aggiornamento per tutte le aree di lavoro, il cmdlet [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/Get-AzureRmOperationalInsightsSearchResults) verrà deprecato.  Per eseguire ricerche log nelle aree di lavoro aggiornate usare il [cmdlet Invoke-LogAnalyticsQuery](https://dev.loganalytics.io/documentation/Tools/PowerShell-Cmdlets).




## <a name="resource-manager-templates"></a>Modelli di Gestione risorse

### <a name="question-can-i-create-an-upgraded-workspace-with-a-resource-manager-template"></a>Domanda: È possibile creare un'area di lavoro aggiornata con un modello di Resource Manager?
Sì.  È necessario usare la versione API di anteprima del 15-03-2017 e includere una sezione **funzionalità** nel modello, come nell'esempio seguente.

    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2017-03-15-preview",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceRegion')]",
            "properties": {
                "sku": {
                    "name": "Free"
                },
                "features": {
                    "legacy": 0,
                    "searchVersion": 1
                }
            }
        }
    ],



## <a name="solutions"></a>Soluzioni

### <a name="question-will-my-solutions-continue-to-work"></a>Domanda: Le mie soluzioni continueranno a funzionare correttamente?
Tutte le soluzioni continueranno a funzionare in un'area di lavoro aggiornata, anche se le prestazioni miglioreranno se convertite nel nuovo linguaggio di query.  Esistono alcuni problemi noti con alcune soluzioni esistenti che sono descritte in questa sezione.

### <a name="known-issue-perspectives-in-application-insights-connector"></a>Problema noto: Prospettive nel connettore di Application Insights
Le prospettive nella [soluzione Connettore di Application Insights](log-analytics-app-insights-connector.md) non sono più supportate della soluzione Connettore di Application Insights.  Per creare viste personalizzate con i dati di Application Insights è possibile usare Progettazione viste.

### <a name="known-issue-backup-solution"></a>Problema noto: soluzione Backup
La soluzione Backup non può raccogliere i dati se è stata installata prima dell'aggiornamento di un'area di lavoro. Disinstallare la soluzione e quindi installare la versione più recente.  La nuova versione della soluzione non supporta gli insiemi di credenziali di Backup classici, pertanto per continuare a usare la soluzione è necessario eseguire l'aggiornamento anche agli insiemi di credenziali dei Servizi di ripristino.

## <a name="upgrade-process"></a>Processo di aggiornamento

### <a name="question-i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>Domanda: Sono disponibili diverse aree di lavoro. è possibile aggiornarle tutte contemporaneamente?  
di serie  L'aggiornamento viene applicato a una singola area di lavoro alla volta. Non è attualmente disponibile alcun modo per aggiornare contemporaneamente molte aree di lavoro. Si noti che l'operazione interessa anche gli altri utenti dell'area di lavoro aggiornata.  

### <a name="question-will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>Domanda: I dati di log esistenti raccolti in un'area di lavoro verranno modificati in seguito all'aggiornamento?  
di serie I dati di log disponibili nelle ricerche dell'area di lavoro non sono interessati dall'aggiornamento. Le ricerche, gli avvisi e le visualizzazioni salvati verranno convertiti automaticamente nel nuovo linguaggio di ricerca.  

### <a name="question-what-happens-if-i-dont-upgrade-my-workspace"></a>Domanda: Cosa accade se non si aggiorna l'area di lavoro?  
La funzionalità di ricerca log legacy verrà deprecata nei prossimi mesi. Le aree di lavoro non aggiornate verranno in seguito aggiornate automaticamente.

### <a name="question-can-i-revert-back-after-i-upgrade"></a>Domanda: Dopo l'aggiornamento è possibile ripristinare la situazione precedente?
Prima della disponibilità generale, era possibile ripristinare l'area di lavoro dopo l'aggiornamento.  Ora che il nuovo linguaggio ha raggiunto il livello di disponibilità generale, questa funzionalità è stata rimossa, dato che è iniziato il ritiro della piattaforma legacy.


## <a name="views"></a>Visualizzazioni

### <a name="question-how-do-i-create-a-new-view-with-view-designer"></a>Domanda: Come si crea una nuova vista con Progettazione viste?
Prima dell'aggiornamento è possibile creare una nuova vista con la progettazione vista da un riquadro nel dashboard principale del portale OMS.  Quando l'area di lavoro viene aggiornata, questo riquadro viene rimosso.  È possibile creare una nuova vista con Progettazione viste nel portale OMS facendo clic sul pulsante verde + nel menu di sinistra.  Facendo clic sul riquadro della progettazione vista si continua a creare una nuova vista con il portale di Azure.


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni, sull'[aggiornamento dell'area di lavoro al nuovo linguaggio di query di Log Analytics](log-analytics-log-search-upgrade.md).
