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
ms.date: 09/06/2017
ms.author: bwren
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 85d4f9bc11de18f171b923b4ae55950fb0a360c0
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="log-analytics-new-log-search-faq-and-known-issues"></a>Nuova ricerca log in Log Analytics - domande frequenti e problemi noti

Questo articolo riporta le domande più frequenti e i problemi noti riguardanti l'aggiornamento di [Log Analytics al nuovo linguaggio di query](log-analytics-log-search-upgrade.md).  Prima di decidere di aggiornare l'area di lavoro, leggere l'intero articolo.


## <a name="alerts"></a>Avvisi

### <a name="question-i-have-a-lot-of-alert-rules-do-i-need-to-create-them-again-in-the-new-language-after-i-upgrade"></a>Domanda: Sono presenti molte regole di avviso. È necessario crearle nuovamente nel nuovo linguaggio dopo l'aggiornamento?  
No, le regole di avviso vengono convertite automaticamente nel nuovo linguaggio di ricerca durante l'aggiornamento.  


## <a name="computer-groups"></a>Gruppi di computer

### <a name="question-im-getting-errors-when-trying-to-use-computer-groups--has-their-syntax-changed"></a>Domanda: Durante il tentativo di usare gruppi di computer vengono visualizzati degli errori.  La sintassi è stata modificata?
Sì, quando si aggiorna l'area di lavoro, la sintassi per l'uso di gruppi di computer cambia.  Per informazioni dettagliate, vedere [Gruppi di computer nelle ricerche nei log in Log Analytics](log-analytics-computer-groups.md).

### <a name="known-issue-groups-imported-from-active-directory"></a>Problema noto: Gruppi importati da Active Directory
Attualmente, è possibile creare una query che usi un gruppo di computer importato da Active Directory.  In alternativa, finché questo problema non verrà risolto, creare un nuovo gruppo di computer da usare nella query tramite il gruppo Active Directory importato.

Di seguito viene riportata una query di esempio per creare un nuovo gruppo di computer che includa un gruppo Active Directory importato:

    ComputerGroup | where GroupSource == "ActiveDirectory" and Group == "AD Group Name" and TimeGenerated >= ago(24h) | distinct Computer


## <a name="dashboards"></a>Dashboard

### <a name="question-can-i-still-use-dashboards-in-an-upgraded-workspace"></a>Domanda: È sempre possibile usare i dashboard in un'area di lavoro aggiornata?
È possibile continuare a usare tutti i riquadri aggiunti a **My Dashboard** (Il mio dashboard) prima dell'aggiornamento dell'area di lavoro, ma non è possibile modificarli o crearne di nuovi.  È possibile continuare a creare e modificare le viste tramite [Progettazione viste](log-analytics-view-designer.md), oltre a creare dashboard nel portale di Azure.


## <a name="log-searches"></a>Ricerche log

### <a name="question-i-have-saved-searches-outside-of-my-upgraded-workspace-can-i-convert-them-to-the-new-search-language-automatically"></a>Domanda: Le ricerche salvate all'esterno dell'area di lavoro aggiornata possono essere convertite automaticamente al nuovo linguaggio di ricerca?
È possibile usare lo strumento di conversione del linguaggio nella pagina della ricerca log per convertire ogni ricerca.  Non è disponibile alcun metodo per convertire automaticamente più ricerche senza aggiornare l'area di lavoro.

### <a name="question-why-are-my-query-results-not-sorted"></a>Domanda: Perché i risultati della query non sono ordinati?
Nel nuovo linguaggio di query, i risultati non sono ordinati per impostazione predefinita.  Usare il comando [Operatore sort](https://go.microsoft.com/fwlink/?linkid=856079) per ordinare i risultati in base a una o più proprietà.

### <a name="known-issue-search-results-in-a-list-may-include-properties-with-no-data"></a>Problema noto: I risultati della ricerca in un elenco possono includere proprietà senza dati
I risultati della ricerca log in un elenco possono includere proprietà senza dati.  Prima dell'aggiornamento, queste proprietà non sono incluse.  Questo problema verrà corretto in modo che le proprietà vuote non vengano visualizzate.

### <a name="known-issue-selecting-a-value-in-a-chart-doesnt-display-detailed-results"></a>Problema noto: La selezione di un valore in un grafico non visualizza risultati dettagliati
Prima dell'aggiornamento, selezionando un valore in un grafico verrà restituito un elenco dettagliato di record corrispondente al valore selezionato.  Dopo l'aggiornamento, verrà restituita solo la riga di riepilogo singola.  Questo problema è attualmente in fase di analisi.

## <a name="log-search-api"></a>API di ricerca nei log

### <a name="question-does-the-log-search-api-get-updated-after-i-upgrade"></a>Domanda: L'API di ricerca log viene aggiornata dopo l'aggiornamento?
L'[API di ricerca log](log-analytics-log-search-api.md) legacy non funzionerà più quando si aggiorna l'area di lavoro.  Vedere [API REST di Azure Log Analytics](https://dev.loganalytics.io/) per informazioni dettagliate sulla nuova API.


## <a name="portals"></a>Portali

### <a name="question-should-i-use-the-new-advanced-analytics-portal-or-keep-using-the-log-search-portal"></a>Domanda: È consigliabile usare il nuovo portale Advanced Analytics o continuare a usare il portale di ricerca log?
È possibile visualizzare un confronto tra i due portali in [Portali per la creazione e la modifica di ricerche log in Azure Log Analytics](log-analytics-log-search-portals.md).  Ognuno presenta vantaggi distinti, per cui è possibile scegliere quello più adatto alle proprie esigenze.  È comune per scrivere le query nel portale Advanced Analytics e incollarle in altre posizioni, ad esempio Progettazione viste.  È necessario essere a conoscenza degli [aspetti da considerare](log-analytics-log-search-portals.md#advanced-analytics-portal) quando si esegue questa operazione.


## <a name="power-bi"></a>Power BI

### <a name="question-does-anything-change-with-powerbi-integration"></a>Domanda: L'aggiornamento implica modifiche per l'integrazione con Power BI?
Sì.  Dopo aver aggiornato l'area di lavoro, il processo per esportare i dati di Log Analytics in Power BI non funzionerà più.  Le pianificazioni esistenti create prima dell'aggiornamento verranno disabilitate.  Dopo l'aggiornamento, Azure Log Analytics usa la stessa piattaforma di Application Insights e il processo per esportare le query di Log Analytics in Power BI è uguale al [processo usato per esportare le query di Application Insights in Power BI](../application-insights/app-insights-export-power-bi.md#export-analytics-queries).

### <a name="known-issue-power-bi-request-size-limit"></a>Problema noto: Limite di dimensioni richiesta in Power BI
Per l'esportazione di una query Log Analytics in Power BI è attualmente prevista una dimensione massima di 8 MB.  Questo limite verrà presto incrementato.


##<a name="powershell-cmdlets"></a>Cmdlet PowerShell

### <a name="question-does-the-log-search-powershell-cmdlet-get-updated-after-i-upgrade"></a>Domanda: Il cmdlet PowerShell di ricerca log viene aggiornato dopo l'aggiornamento?
Il cmdlet [Get-AzureRmOperationalInsightsSearchResults](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/Get-AzureRmOperationalInsightsSearchResults) non è ancora stato aggiornato al nuovo linguaggio di ricerca.  Continuare a usare il linguaggio di query legacy con questo cmdlet, anche dopo l'aggiornamento dell'area di lavoro.  La documentazione aggiornata verrà resa disponibile per il cmdlet una volta aggiornato.


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

### <a name="known-issue-capacity-and-performance-solution"></a>Problema noto: Soluzione Capacità e prestazioni
Alcune parti della vista [Capacità e prestazioni](log-analytics-capacity.md) possono risultare vuote.  Una soluzione a questo problema sarà disponibile a breve.

### <a name="known-issue-device-health-solution"></a>Problema noto: Soluzione Integrità del dispositivo
La [soluzione Integrità del dispositivo](https://docs.microsoft.com/windows/deployment/update/device-health-monitor) non raccoglie alcun dato in un'area di lavoro aggiornata.  Una soluzione a questo problema sarà disponibile a breve.

### <a name="known-issue-application-insights-connector"></a>Problema noto: Connettore di Application Insights
Le prospettive nella [soluzione Connettore di Application Insights](log-analytics-app-insights-connector.md) non sono attualmente supportate in un'area di lavoro aggiornata.  Una soluzione per questo problema è attualmente in fase di analisi.

### <a name="known-issue-backup-solution"></a>Problema noto: soluzione Backup
La soluzione Backup non raccoglie dati in un'area di lavoro aggiornata. A breve verrà annunciata una nuova soluzione Backup che usa le aree di lavoro aggiornate.

## <a name="upgrade-process"></a>Processo di aggiornamento

### <a name="question-i-have-several-workspaces-can-i-upgrade-all-workspaces-at-the-same-time"></a>Domanda: Sono disponibili diverse aree di lavoro. è possibile aggiornarle tutte contemporaneamente?  
No.  L'aggiornamento viene applicato a una singola area di lavoro alla volta. Non è attualmente disponibile alcun modo per aggiornare contemporaneamente molte aree di lavoro. Si noti che l'operazione interessa anche gli altri utenti dell'area di lavoro aggiornata.  

### <a name="question-will-existing-log-data-collected-in-my-workspace-be-modified-if-i-upgrade"></a>Domanda: I dati di log esistenti raccolti in un'area di lavoro verranno modificati in seguito all'aggiornamento?  
No. I dati di log disponibili nelle ricerche dell'area di lavoro non sono interessati dall'aggiornamento. Le ricerche, gli avvisi e le visualizzazioni salvati verranno convertiti automaticamente nel nuovo linguaggio di ricerca.  

### <a name="question-what-happens-if-i-dont-upgrade-my-workspace"></a>Domanda: Cosa accade se non si aggiorna l'area di lavoro?  
La funzionalità di ricerca log legacy verrà deprecata nei prossimi mesi. Le aree di lavoro non aggiornate verranno in seguito aggiornate automaticamente.

### <a name="question-i-didnt-choose-to-upgrade-but-my-workspace-has-been-upgraded-anyway-what-happened"></a>Domanda: L'area di lavoro è stata aggiornata, anche se è stato scelto di non eseguire l'aggiornamento. Che cosa è successo?  
È possibile sia stata aggiornata da un altro amministratore dell'area di lavoro. Si noti che tutte le aree di lavoro verranno aggiornate automaticamente quando il nuovo linguaggio sarà disponibile a livello generale.  

### <a name="question-i-have-upgraded-by-mistake-and-now-need-to-cancel-it-and-restore-everything-back-what-should-i-do"></a>Domanda: Se si esegue l'aggiornamento per errore, è possibile annullare l'operazione e ripristinare tutto?  
Non c'è problema.  Prima dell'aggiornamento dell'area di lavoro viene creato uno snapshot ed è quindi possibile ripristinarlo. Tenere presente, tuttavia, che andranno perduti gli avvisi, le ricerche o le visualizzazioni salvati dopo l'aggiornamento.  Per ripristinare l'ambiente dell'area di lavoro, seguire la procedura descritta in [Dopo aver eseguito l'aggiornamento è possibile tornare indietro?](log-analytics-log-search-upgrade.md#can-i-go-back-after-i-upgrade)


## <a name="views"></a>Visualizzazioni

### <a name="question-how-do-i-create-a-new-view-with-view-designer"></a>Domanda: Come si crea una nuova vista con Progettazione viste?
Prima dell'aggiornamento, è possibile creare una nuova vista con Progettazione viste da un riquadro nel dashboard principale.  Quando l'area di lavoro viene aggiornata, questo riquadro viene rimosso.  È possibile creare una nuova vista con Progettazione viste nel portale OMS facendo clic sul pulsante verde + nel menu di sinistra.

### <a name="known-issue-see-all-option-for-line-charts-in-views-doesnt-result-in-a-line-chart"></a>Problema noto: L'opzione Tutti per i grafici a linee in Viste non dà origine a un grafico a linee
Facendo clic sull'opzione *Visualizzare tutto* nella parte inferiore di una parte di un grafico a linee in una vista verrà visualizzata una tabella.  Prima dell'aggiornamento, verrà visualizzato un grafico a linee.  Questo problema è attualmente in fase di analisi per una modifica potenziale.


## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni, sull'[aggiornamento dell'area di lavoro al nuovo linguaggio di query di Log Analytics](log-analytics-log-search-upgrade.md).

