---
title: Gestire l'utilizzo e i costi per i log di Monitoraggio di Azure Documenti Microsoft
description: Informazioni su come modificare il piano tariffario e gestire i criteri di conservazione e il volume di dati per l'area di lavoro log Analytics in Monitoraggio di Azure.Learn how to change the pricing plan and manage data volume and retention policy for your Log Analytics workspace in Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 9a7d0530c4f03138fad3e4aaa473d54e1cfd5b0a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686567"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Gestire l'utilizzo e i costi con i log di Monitoraggio di AzureManage usage and costs with Azure Monitor Logs

> [!NOTE]
> Questo articolo descrive come comprendere e controllare i costi per i log di Monitoraggio di Azure.This article describes how to understand and control your costs for Azure Monitor Logs. Un articolo correlato, [Monitoring usage and estimated costs](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) descrive come visualizzare l'utilizzo e i costi stimati tra più funzionalità di monitoraggio di Azure per modelli di determinazione dei prezzi diversi. Tutti i prezzi e i costi indicati in questo articolo sono solo a scopo esemplificativo. 

Azure Monitor Logs is designed to scale and support collecting, indexing, and storing massive amounts of data per day from any source in your enterprise or deployed in Azure.  Anche se si tratta di uno strumento importante per l'organizzazione, è comunque fondamentale ottimizzare i costi. A tal fine, è importante comprendere che il costo di un'area di lavoro di Log Analytics non si basa solo sul volume di dati raccolti, ma dipende anche dal piano selezionato e da quanto tempo si è scelto di archiviare i dati generati dalle origini connesse.  

In questo articolo viene esaminato come monitorare in modo proattivo il volume di dati ingere e la crescita dello spazio di archiviazione e definire i limiti per controllare i costi associati. 

## <a name="pricing-model"></a>Modello di prezzi

La determinazione dei prezzi predefinita per Log Analytics è un modello con pagamento in base al **consumo** basato sul volume di dati ingerita e, facoltativamente, per una conservazione dei dati più lunga. Il volume dei dati viene misurato come dimensione dei dati che verranno archiviati. Ogni area di lavoro di Log Analytics viene addebitata come servizio separato e contribuisce alla fattura per la sottoscrizione di Azure.Each Log Analytics workspace is charged as a separate service and contributes to the bill for your Azure subscription. La quantità di inserimento dei dati può essere considerevole a seconda dei seguenti fattori: 

  - Numero di soluzioni di gestione abilitate e relative configurazioni (ad es. 
  - Numero di macchine virtuali monitorate
  - Tipo di dati raccolti da ogni macchina virtuale monitorataType of data collected from each monitored VM 
  
Oltre al modello con pagamento in base al movimento, Log Analytics dispone di livelli di **prenotazione della capacità** che consentono di risparmiare fino al 25% rispetto al prezzo con pagamento in base al costo. Il prezzo di prenotazione della capacità consente di acquistare una prenotazione a partire da 100 GB/giorno. Qualsiasi utilizzo superiore al livello di prenotazione verrà fatturato alla tariffa con pagamento in base al consumo. I livelli Di riserva capacità hanno un periodo di impegno di 31 giorni. Durante il periodo di impegno, è possibile passare a un livello superiore Prenotazione capacità (che riavvierà il periodo di impegno di 31 giorni), ma non è possibile tornare a conto con pagamento in base al numero di impegni o a un livello di prenotazione capacità inferiore fino al termine del periodo di impegno. La fatturazione per i livelli Prenotazione capacità viene eseguita su base giornaliera. [Ulteriori informazioni](https://azure.microsoft.com/pricing/details/monitor/) sui prezzi di log analytics con pagamento in base al consumo e prenotazione della capacità. 

In tutti i piani tariffari, il volume di dati viene calcolato da una rappresentazione di stringa dei dati mentre vengono preparati per essere archiviati. Diverse [proprietà comuni a tutti i tipi di dati](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) non `_ResourceId` `_ItemId`sono `_IsBillable` incluse nel calcolo della dimensione dell'evento, tra cui , e `_BilledSize`.

Si noti inoltre che alcune soluzioni, ad esempio [Centro sicurezza](https://azure.microsoft.com/pricing/details/security-center/) di Azure e [Azure Sentinel,](https://azure.microsoft.com/pricing/details/azure-sentinel/)dispongono di un proprio modello di determinazione dei prezzi. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Stima dei costi per la gestione dell'ambiente 

Se non si usano ancora i log di Monitoraggio di Azure, è possibile usare il [calcolatore](https://azure.microsoft.com/pricing/calculator/?service=monitor) dei prezzi di Monitoraggio di Azure per stimare il costo dell'uso di Log Analytics.If you're not yet using Azure Monitor Logs, you can use the Azure Monitor pricing calculator to estimate the cost of using Log Analytics. Iniziare immettendo "Monitor di Azure" nella casella di ricerca e facendo clic sul riquadro di Monitoraggio di Azure risultante. Scorrere la pagina verso il basso fino a Monitoraggio di Azure e selezionare Log Analytics dall'elenco a discesa Tipo.  Qui è possibile immettere il numero di macchine virtuali e il GB di dati che si prevede di raccogliere da ogni macchina virtuale. In genere da 1 a 3 GB di mese di dati viene esitato da una tipica macchina virtuale di Azure.Typically to 1 To 3 GB of data month is inged from a typical Azure VM. Se si sta già valutando i log di Monitoraggio di Azure, è possibile usare le statistiche dei dati dal proprio ambiente. Vedere di seguito per determinare il [numero di macchine virtuali monitorate](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) e il volume di dati in cui [l'area di lavoro sta ingerendo.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume) 

## <a name="understand-your-usage-and-estimate-costs"></a>Comprendere l'utilizzo e stimare i costi

Se si usano ora i log di Monitoraggio di Azure, è facile comprendere quali sono probabilmente i costi basati su modelli di utilizzo recenti. A tale scopo, usare Utilizzo di **Log Analytics e Costi stimati** per esaminare e analizzare l'utilizzo dei dati. Questo mostra la quantità di dati raccolti da ogni soluzione, la quantità di dati vengono conservati e una stima dei costi in base alla quantità di dati inseriti e qualsiasi conservazione aggiuntiva oltre la quantità inclusa.

![Utilizzo e costi stimati](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Per esplorare i dati in maggiore dettaglio, fare clic sull'icona nell'angolo superiore destro di uno dei grafici nella pagina **Utilizzo e costi stimati**. Ora è possibile usare questa query per esplorare i dettagli dell'utilizzo.  

![Visualizzazione dei log](media/manage-cost-storage/logs.png)

Dalla pagina **Utilizzo e costi stimati** è possibile rivedere il volume dei dati per il mese. Sono inclusi tutti i dati ricevuti e conservati nell'area di lavoro Log Analytics.  Fare clic su **Dettagli utilizzo** nella parte superiore della pagina per visualizzare il dashboard di utilizzo con informazioni sulle tendenze del volume di dati per origine, computer e offerta. Per visualizzare e impostare un limite giornaliero o per modificare il periodo di conservazione, fare clic su **Gestione del volume dati**.
 
I costi di Log Analytics vengono addebitati nella fattura di Azure. È possibile visualizzare i dettagli della fattura di Azure nella sezione Fatturazione del portale di Azure oppure nel [portale di fatturazione di Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Visualizzazione dell'utilizzo di Log Analytics nella fattura di AzureViewing Log Analytics usage on your Azure bill 

Azure offre una grande quantità di funzionalità utili nell'hub [Gestione costi di Azure e fatturazione.](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Ad esempio, la funzionalità "Analisi dei costi" consente di visualizzare le spese per le risorse di Azure.For instance, the "Cost analysis" functionality enables you to view your spends for Azure resources. L'aggiunta di un filtro per tipo di risorsa (a microsoft.operationalinsights/workspace per Log Analytics) consentirà di tenere traccia della spesa.

È possibile ottenere una maggiore comprensione dell'utilizzo [scaricando l'utilizzo dal portale](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)di Azure. Nel foglio di calcolo scaricato è possibile visualizzare l'utilizzo per risorsa di Azure (ad esempio, area di lavoro di Log Analytics) al giorno. In questo foglio di calcolo di Excel, l'utilizzo delle aree di lavoro di Log Analytics è reperibile applicando un filtro rapido nella colonna "Categoria contatore" per visualizzare "Insights and Analytics" (utilizzato da alcuni dei livelli di prezzo legacy) e "Log Analytics" e quindi aggiungendo un filtro nella colonna "Instance ID" che è "contiene area di lavoro". L'utilizzo viene visualizzato nella colonna "Quantità consumata" e l'unità per ogni voce viene visualizzata nella colonna "Unità di misura".  Sono disponibili ulteriori dettagli per comprendere la fattura di [Microsoft Azure.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) 

## <a name="changing-pricing-tier"></a>Modifica del piano tariffario

Per modificare il piano tariffario di Log Analytics dell'area di lavoro, 

1. Nel portale di Azure aprire **Utilizzo e costi stimati** dall'area di lavoro in cui verrà visualizzato un elenco dei piani tariffari disponibili per questa area di lavoro.

2. Esaminare i costi stimati per ognuno dei piani tariffari. Questa stima si basa sugli ultimi 31 giorni di utilizzo, pertanto questa stima dei costi si basa sul fatto che gli ultimi 31 giorni sono rappresentativi dell'utilizzo tipico. Nell'esempio seguente è possibile vedere come, in base ai modelli di dati degli ultimi 31 giorni, questa area di lavoro costerebbe meno nel livello con pagamento in base al consumo (#1) rispetto al livello Prenotazione capacità (#2) di 100 GB/giorno.  

    ![Piani tariffari](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. Dopo aver esaminato i costi stimati in base agli ultimi 31 giorni di utilizzo, se si decide di modificare il piano tariffario, fare clic su **Seleziona**.  

È anche possibile [impostare il piano tariffario tramite Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) usando il `sku` parametro (nel`pricingTier` modello di Azure Resource Manager). 

## <a name="legacy-pricing-tiers"></a>Piani tariffari legacy

Le sottoscrizioni che vi avevano un'area di lavoro di Log Analytics o una risorsa di Application Insights prima del 2 aprile 2018 o che sono collegate a un contratto Enterprise iniziato prima del 1 febbraio 2019 continueranno ad avere accesso per utilizzare i livelli tariffari legacy: **Gratuito** **, Autonomo (Per GB)** e **Per nodo (OMS).**  Le aree di lavoro nel piano tariffario Gratuito avranno inserimento giornaliero dei dati limitato a 500 MB (ad eccezione dei tipi di dati di sicurezza raccolti dal Centro sicurezza di Azure) e la conservazione dei dati è limitata a 7 giorni. Il piano tariffario gratuito è destinato solo ai fini della valutazione. Le aree di lavoro nei piani tariffari autonomo o Per nodo hanno una conservazione configurabile dall'utente da 30 a 730 giorni.

Il piano tariffario Per nodo addebita il livello tariffario per vm monitorata (nodo) con granularità oraria. Per ogni nodo monitorato, all'area di lavoro vengono allocati 500 MB di dati al giorno che non vengono fatturati. Questa allocazione viene aggregata a livello di area di lavoro. I dati ingeriti al di sopra dell'allocazione giornaliera dei dati aggregata vengono fatturati per GB in base all'esadio dei dati. Si noti che nella fattura, il servizio sarà **Insight e Analytics** per l'utilizzo di Log Analytics se l'area di lavoro è nel piano tariffario Per nodo. 

> [!TIP]
> Se l'area di lavoro ha accesso al piano tariffario **Per nodo,** ma ci si chiede se sarebbe meno conveniente in un livello con pagamento in base al consumo, è possibile [utilizzare la query seguente](#evaluating-the-legacy-per-node-pricing-tier) per ottenere facilmente un suggerimento. 

Le aree di lavoro create prima di aprile 2016 possono inoltre accedere ai piani **standard standard** e **Premium** originali con conservazione dei dati fissa rispettivamente di 30 e 365 giorni. Non è possibile creare nuove aree di lavoro nei piani tariffari **Standard** o **Premium** e, se un'area di lavoro viene spostata all'esterno di questi livelli, non può essere spostata nuovamente. 

Ulteriori dettagli sulle limitazioni del livello tariffario sono disponibili [qui](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Per usare i diritti che derivano dall'acquisto di OMS E1 Suite, OMS E2 Suite o un componente aggiuntivo di OMS per System Center, scegliere il piano tariffario *Per nodo* di Log Analytics.

## <a name="change-the-data-retention-period"></a>Cambiare il periodo di conservazione dei dati

La procedura seguente descrive come configurare il periodo di conservazione dei dati nell'area di lavoro. La conservazione dei dati può essere configurata da 30 a 730 giorni (2 anni) per tutte le aree di lavoro, a meno che non utilizzino il piano tariffario gratuito legacy. 

### <a name="default-retention"></a>Conservazione predefinita

Per impostare la conservazione predefinita per l'area di lavoro, 
 
1. Nel portale di Azure, nell'area di lavoro, selezionare **Utilizzo e costi stimati** nel riquadro sinistro.
2. Nella pagina **Utilizzo e costi stimati** fare clic su **Gestione del volume dati** nella parte superiore.
3. Nel riquadro spostare il dispositivo di scorrimento per aumentare o diminuire il numero di giorni e quindi fare clic su **OK**.  Se si usa il livello *gratuito*, non è possibile modificare il periodo di conservazione dei dati ed è necessario eseguire l'aggiornamento al piano a pagamento per controllare questa impostazione.

    ![Modificare l'impostazione di conservazione dei dati dell'area di lavoro](media/manage-cost-storage/manage-cost-change-retention-01.png)

Quando la conservazione viene abbassata, è presente un periodo di prova di diversi giorni prima che i dati meno recenti vengano rimossi. 
    
La conservazione può anche essere [impostata tramite Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) usando il `retentionInDays` parametro. Inoltre, se si imposta la conservazione dei dati su 30 giorni, `immediatePurgeDataOn30Days` è possibile attivare un'eliminazione immediata dei dati meno recenti utilizzando il parametro , che può essere utile per gli scenari correlati alla conformità. Questa funzionalità viene esposta solo tramite Azure Resource Manager.This functionality is only exposed via Azure Resource Manager. 

Due tipi `Usage` di `AzureActivity` dati, e 90, vengono conservati per 90 giorni per impostazione predefinita e per questa conservazione di 90 giorni è previsto alcun costo. Questi tipi di dati sono inoltre esenti da costi di inserimento dati. 



### <a name="retention-by-data-type"></a>Conservazione per tipo di dati

È anche possibile specificare impostazioni di conservazione diverse per i singoli tipi di dati da 30 a 730 giorni (ad eccezione delle aree di lavoro nel piano tariffario gratuito legacy). Ogni tipo di dati è una sottorisorsa dell'area di lavoro. Ad esempio, la tabella SecurityEvent può essere indirizzata in Azure Resource Manager come:For instance the SecurityEvent table can be addressed in [Azure Resource Manager as:](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Si noti che per il tipo di dati (tabella) viene fatta distinzione tra maiuscole e minuscole.  Per ottenere le impostazioni di conservazione per ogni tipo di dati corrente di un particolare tipo di dati (in questo esempio SecurityEvent), usare:To get the current per data type retention settings of a particular data type (in this example SecurityEvent), use:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Per ottenere le impostazioni di conservazione per ogni tipo di dati corrente per tutti i tipi di dati nell'area di lavoro, è sufficiente omettere il tipo di dati specifico, ad esempio:To get the current per data type retention settings for all data types in your workspace, just omit the specific data type, for example:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Per impostare la conservazione di un particolare tipo di dati (in questo esempio SecurityEvent) su 730 giorni,

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

I valori `retentionInDays` validi per sono compresi tra 30 e 730.

I `Usage` `AzureActivity` tipi di dati e non possono essere impostati con la conservazione personalizzata. Essi assumeranno il massimo della conservazione dell'area di lavoro predefinita o 90 giorni. 

Un ottimo strumento per connettersi direttamente a Azure Resource Manager per impostare la conservazione in base al tipo di dati è lo strumento OSS [ARMclient](https://github.com/projectkudu/ARMClient).  Scopri di più su ARMclient dagli articoli [di David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) e [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Ecco un esempio di utilizzo di ARMClient, impostando i dati SecurityEvent su una conservazione di 730 giorni:Here's an example using ARMClient, setting SecurityEvent data to a 730 day retention:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> L'impostazione della conservazione dei singoli tipi di dati può essere utilizzata per ridurre i costi per la conservazione dei dati.  Per i dati raccolti a partire da ottobre 2019 (quando questa funzionalità è stata rilasciata), ridurre la conservazione per alcuni tipi di dati può ridurre i costi di conservazione nel tempo.  Per i dati raccolti in precedenza, l'impostazione di una conservazione inferiore per un singolo tipo non influirà sui costi di conservazione.  

## <a name="manage-your-maximum-daily-data-volume"></a>Gestisci il tuo volume massimo di dati giornaliero

È possibile configurare un limite giornaliero e limitare l'inserimento giornaliero per l'area di lavoro, tuttavia è necessario tenere presente che l'obiettivo non deve essere quello di raggiungere tale limite.  In caso contrario, si perdono i dati per il resto del giorno, situazione che può influire su altri servizi e soluzioni di Azure la cui funzionalità può dipendere da dati aggiornati disponibili nell'area di lavoro.  Di conseguenza, la possibilità di osservare e ricevere avvisi quando le condizioni di integrità delle risorse che supportano i servizi IT sono interessate.  Il limite giornaliero deve essere utilizzato per gestire l'aumento imprevisto del volume di dati dalle risorse gestite e rimanere entro il limite o quando si desidera limitare gli addebiti non pianificati per l'area di lavoro.  

Subito dopo aver raggiunto il limite giornaliero, la raccolta di tipi di dati fatturabili si interrompe per il resto della giornata. La latenza insita nell'applicazione del limite giornaliero può significare che il limite non viene applicato esattamente come il livello massimo giornaliero specificato. Nella parte superiore della pagina viene visualizzato un banner di avviso per l'area di lavoro di Log Analytics selezionata e viene inviato un evento operazione alla tabella *Operazione* nella categoria **LogManagement.** La raccolta dati riprende dopo l'ora di ripristino definita in *Daily limit will be set at* (Ora impostazione limite giornaliero). Si consiglia di definire una regola di avviso in base a questo evento operazione, per l'invio di una notifica quando viene raggiunta la soglia dei dati giornaliera. 

> [!WARNING]
> Il limite giornaliero non interrompe la raccolta di dati dal Centro sicurezza di Azure, ad eccezione delle aree di lavoro in cui il Centro sicurezza di Azure è stato installato prima del 19 giugno 2017.The daily cap does not stop the collection of data from Azure Security Center, except for workspaces in which Azure Security Center was installed before June 19, 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificare la soglia dei dati giornaliera da definire

Vedere [Utilizzo e costi stimati di Log Analytics](usage-estimated-costs.md) per informazioni sulla tendenza nell'inserimento dati e sul limite di volume giornaliero da definire. Dovrebbe essere considerato con attenzione, dal momento che non sarà in grado di monitorare le risorse dopo il raggiungimento del limite. 

### <a name="set-the-daily-cap"></a>Impostare il limite giornaliero

Nei passaggi seguenti viene descritto come configurare un limite per gestire il volume di dati che Log Analytics verrà reingere all'area di lavoro al giorno.  

1. Nell'area di lavoro selezionare **Utilizzo e costi stimati** nel riquadro a sinistra.
2. Nella pagina **Utilizzo e costi stimati** per l'area di lavoro selezionata fare clic su **Gestione del volume dati** nella parte superiore. 
3. Il limite giornaliero è **OFF per** impostazione predefinita, fare clic **su ON** per attivarlo, quindi impostare il limite del volume di dati in GB/giorno.

    ![Configurazione della soglia dei dati in Log Analytics](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Avvisa quando è stato raggiunto il limite giornaliero

Anche se nel portale di Azure viene visualizzato un segnale visivo quando viene raggiunta la soglia dei dati, questo comportamento potrebbe non soddisfare le esigenze aziendali per la gestione di problemi operativi che richiedono attenzione immediata.  Per ricevere una notifica di avviso, è possibile creare una nuova regola di avviso in Monitoraggio di Azure.  Per ulteriori informazioni, vedere [come creare, visualizzare e gestire gli avvisi.](alerts-metric.md)

Per iniziare, ecco le impostazioni consigliate per l'avviso:

- Destinazione: Selezionare la risorsa di Log Analytics
- Criteri: 
   - Nome segnale: Ricerca log personalizzata
   - Query di ricerca: operazione, seguita da | con valore "OverQuota" per Detail
   - In base a: Numero di risultati
   - Condizione: Maggiore di
   - Soglia: 0
   - Periodo: 5 (minuti)
   - Frequenza: 5 (minuti)
- Nome regola di avviso: Soglia dei dati giornaliera raggiunta
- Gravità: Avviso (Gravità 1)

Una volta definito l'avviso e raggiunto il limite, viene attivato un avviso e viene eseguita la risposta definita nel gruppo di azioni. È possibile informare il team tramite posta elettronica e SMS oppure automatizzare le operazioni usando webhook o runbook di Automazione oppure tramite l'[integrazione con una soluzione di Gestione dei servizi IT esterna](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Risoluzione dei problemi che determinano un utilizzo superiore al previsto

Un utilizzo più elevato è dovuto a una o entrambe le cause seguenti:
- Più nodi del previsto per l'invio di dati all'area di lavoro di Log Analytics
- Più dati del previsto inviati all'area di lavoro di Log Analytics (forse a causa dell'inizio di una nuova soluzione o di una modifica alla configurazione di una soluzione esistente)

## <a name="understanding-nodes-sending-data"></a>Informazioni sui nodi che inviano datiUnderstanding nodes sending data

Per comprendere il numero di nodi che segnalano gli heartbeat dall'agente ogni giorno nell'ultimo mese, utilizzare

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Il get a count di nodi che inviano dati nelle ultime 24 ore utilizza la query: 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Per ottenere un elenco di nodi che inviano dati (e la quantità di dati inviati da ciascuno) è possibile utilizzare la query seguente:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!TIP]
> Usare queste query `union withsource = tt *` solo se necessario, poiché le analisi tra tipi di dati sono costose. Questa query sostituisce il vecchio modo di eseguire query per ogni computer con il tipo di dati Utilizzo.  

## <a name="understanding-ingested-data-volume"></a>Informazioni sul volume di dati ingeriti

Nella pagina **Utilizzo e costi stimati** il grafico *Inserimento dati per soluzione* mostra il volume totale dei dati inviati e la quantità inviata da ogni soluzione. In questo modo è possibile determinare tendenze specifiche, ad esempio se l'utilizzo dei dati complessivo (o da parte di una particolare soluzione) sta aumentando, è stabile o sta diminuendo. 

### <a name="data-volume-for-specific-events"></a>Volume di dati per eventi specifici

Per esaminare le dimensioni dei dati ingeriti per un particolare set di `Event`eventi, è possibile eseguire una query sulla tabella specifica (in questo esempio) e quindi limitare la query agli eventi di interesse (in questo esempio ID evento 5145 o 5156):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Si noti `where IsBillable = true` che la clausola esclude i tipi di dati da determinate soluzioni per le quali non è previsto alcun addebito per l'inserimento. 

### <a name="data-volume-by-solution"></a>Volume dati per soluzione

La query utilizzata per visualizzare il volume di dati fatturabile per soluzione nell'ultimo mese (escluso l'ultimo giorno parziale) è:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

La clausola con `TimeGenerated` è solo per garantire che l'esperienza di query nel portale di Azure ripercorrerà oltre le 24 ore predefinite. Quando si utilizza il `StartTime` `EndTime` tipo di dati Utilizzo e rappresentano gli intervalli di tempo per i quali vengono presentati i risultati. 

### <a name="data-volume-by-type"></a>Volume di dati per tipo

È possibile approfondire ulteriormente per visualizzare le tendenze dei dati per per tipo di dati:You can drill in further to see data trends for by data type:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

O per vedere una tabella per soluzione e tipo per l'ultimo mese,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Volume di dati per computer

Il `Usage` tipo di dati non include informazioni a livello di completamento. Per visualizzare la **dimensione** dei dati ingeriti per computer, utilizzare la `_BilledSize` [proprietà](log-standard-properties.md#_billedsize), che fornisce la dimensione in byte:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

La `_IsBillable` [proprietà](log-standard-properties.md#_isbillable) specifica se i dati ingeriti saranno in addebiti.

Per visualizzare il **numero** di eventi fatturabili ingeriti per computer, utilizzare 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Volume di dati per risorsa, gruppo di risorse o sottoscrizione di AzureData volume by Azure resource, resource group, or subscription

Per i dati dai nodi ospitati in Azure è possibile ottenere le **dimensioni** dei dati ingeriti __per computer__, utilizzare la [proprietà](log-standard-properties.md#_resourceid)_ResourceId , che fornisce il percorso completo della risorsa:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Per i dati dai nodi ospitati in Azure è possibile ottenere `_ResourceId` le dimensioni dei dati ingeriti per ogni sottoscrizione di Azure , analizzare la proprietà come:For data from nodes hosted in Azure you can get the **size** of inested data per Azure __subscription__, parse the property as:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

La `subscriptionId` `resourceGroup` modifica verrà visualizzato il volume di dati inserendo in modo fatturabile dal gruppo di risorse di Azure.Changing to will show the billable ingested data volume by Azure resource group. 

> [!WARNING]
> Benché siano ancora inclusi nello schema, alcuni campi del tipo di dati Utilizzo sono stati deprecati e i rispettivi valori non verranno più popolati. Si tratta del campo **Computer** e dei campi correlati all'inserimento, ossia **TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** e **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Esecuzione di query per tipi di dati comuniQuerying for common data types

Ecco alcune query di esempio utili per analizzare in maggiore profondità l'origine dei dati di un particolare tipo di dati:

+ Soluzione **Sicurezza**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Soluzione **Gestione log**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Tipo di dati **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Tipo di dati **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Tipo di dati **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Tipo di dati **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Suggerimenti per ridurre il volume di dati

Ecco alcuni suggerimenti utili per ridurre il volume dei log raccolti:

| Origine del volume di dati elevato | Come ridurre il volume di dati |
| -------------------------- | ------------------------- |
| Eventi di sicurezza            | Selezionare gli [eventi di sicurezza comuni o minimi](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier) <br> Modificare i criteri di controllo di sicurezza in modo che vengano raccolti solo gli eventi necessari. In particolare, esaminare la necessità di raccogliere eventi per: <br> - [controllo piattaforma filtro](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [controllo Registro di sistema](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [controllo file system](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [controllo oggetto kernel](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [controllo manipolazione handle](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - controllo archivi rimovibili |
| Contatori delle prestazioni       | Modificare la [configurazione del contatore delle prestazioni](data-sources-performance-counters.md) per: <br> - Ridurre la frequenza di raccolta <br> - Ridurre il numero di contatori delle prestazioni |
| Log eventi                 | Modificare la [configurazione del log eventi](data-sources-windows-events.md) per: <br> - Ridurre il numero di log eventi raccolti <br> - Raccogliere solo i livelli di eventi richiesti, ad esempio non raccogliendo gli eventi di livello *informazioni* |
| syslog                     | Modificare la [configurazione di Syslog](data-sources-syslog.md) per: <br> - Ridurre il numero di strutture raccolte <br> - Raccogliere solo i livelli di eventi richiesti, ad esempio non raccogliendo gli eventi di livello *informazioni* e *debug* |
| AzureDiagnostics           | Modificare la raccolta dei log delle risorse per: <br> - Ridurre il numero di risorse che inviano log a Log Analytics <br> - Raccogliere solo i log necessari |
| Dati della soluzione da computer che non richiedono la soluzione | Usare il [targeting della soluzione](../insights/solution-targeting.md) per raccogliere dati unicamente dai gruppi di computer necessari |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Recupero dei nodi come fatturato nel piano tariffario Per NodoGetting nodes as billed in the Per Node pricing tier

Per ottenere un elenco di computer che verranno fatturati come nodi se l'area di lavoro si trova nel piano tariffario Per Nodo legacy, cercare i nodi che inviano **tipi di dati fatturati** (alcuni tipi di dati sono gratuiti). A tale scopo, `_IsBillable` utilizzare la [proprietà](log-standard-properties.md#_isbillable) e il campo più a sinistra del nome di dominio completo. Restituisce il numero di computer con dati fatturati all'ora (che è la granularità con cui i nodi vengono conteggiati e fatturati):

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Recupero dei conteggi dei nodi di sicurezza e automazioneGetting Security and Automation node counts

Se si usa un piano tariffario "Per nodo (OMS)", l'addebito viene effettuato in base al numero di nodi e soluzioni usati e il numero di nodi di Informazioni dettagliate e analisi fatturati sarà visualizzato in una tabella nella pagina **Utilizzo e costi stimati**.  

Per visualizzare il numero di nodi di sicurezza distinti è possibile usare la query:

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Per visualizzare il numero di nodi di Automazione distinti usare la query:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>Valutazione del piano tariffario Per Node legacy

La decisione se le aree di lavoro con accesso al piano tariffario **Per Node** legacy sono migliori in tale livello o in un livello di **prenotazione** con pagamento in base al **consumo** o capacità corrente è spesso difficile da valutare per i clienti.  Ciò comporta la comprensione del compromesso tra il costo fisso per nodo monitorato nel livello tariffario Per Node e la relativa allocazione dei dati inclusi di 500 MB/nodo/giorno e il costo del solo pagamento per i dati inseriti nel livello con pagamento in base al consumo (Per GB). 

Per facilitare questa valutazione, è possibile utilizzare la query seguente per formulare un suggerimento per il piano tariffario ottimale in base ai modelli di utilizzo di un'area di lavoro.  Questa query esamina i nodi monitorati e i dati inseriti in un'area di lavoro negli ultimi 7 giorni e per ogni giorno valuta quale piano tariffario sarebbe stato ottimale. Per usare la query, è necessario specificare se l'area di lavoro usa il Centro sicurezza di Azure impostando `workspaceHasSecurityCenter` `true` su o `false`, quindi (facoltativamente) aggiornando i prezzi Per nodo e Per GB ricevuti dall'organizzazione. 

```kusto
// Set these parameters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your price per node / month 
let PerGBPrice = 2.30; // Enter your price per GB 
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
union withsource = tt * 
| where TimeGenerated >= startofday(now(-7d)) and TimeGenerated < startofday(now())
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join (
    Heartbeat 
    | where TimeGenerated >= startofday(now(-7d)) and TimeGenerated < startofday(now())
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated > ago(8d)
    | where StartTime >= startofday(now(-7d)) and EndTime < startofday(now())
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend PerGBDailyCost = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)) * PerGBPrice,
             DataGB * PerGBPrice)
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerGBPrice
| extend Recommendation = iff(PerNodeDailyCost < PerGBDailyCost, "Per Node tier", 
             iff(NonSecurityDataGB > 85., "Capacity Reservation tier", "Pay-as-you-go (Per GB) tier"))
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, Recommendation | sort by day asc
| project day, Recommendation // Comment this line to see details
| sort by day asc
```

Questa query non è una replica esatta del modo in cui viene calcolato l'utilizzo, ma funzionerà per fornire consigli del livello tariffario nella maggior parte dei casi.  

## <a name="create-an-alert-when-data-collection-is-high"></a>Creare un avviso quando la raccolta dati è elevataCreate an alert when data collection is high

Questa sezione descrive come creare un avviso nei casi seguenti:
- Il volume di dati supera una quantità specificata.
- Si prevede che il volume di dati superi una quantità specificata.

Avvisi di Azure supporta [avvisi relativi ai log](alerts-unified-log.md) che usano query di ricerca. 

La query seguente restituisce un risultato quando vengono raccolti più di 100 GB di dati nelle ultime 24 ore:

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type 
| where DataGB > 100
```

La query seguente usa una semplice formula per prevedere quando verranno inviati più di 100 GB di dati in un giorno: 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type 
| where EstimatedGB > 100
```

Per generare un avviso su un volume di dati diverso, sostituire il numero 100 nelle query con il numero di GB da segnalare.

Per ricevere una notifica quando la raccolta dati supera le dimensioni previste, seguire la procedura descritta in [Creare un nuovo avviso del log](alerts-metric.md).

Quando si crea l'avviso per la prima query e la quantità di dati supera i 100 GB in 24 ore, impostare:  

- Per **Definire la condizione dell'avviso**, specificare l'area di lavoro Log Analytics come destinazione della risorsa.
- Per **Criteri di avviso** specificare quanto segue:
   - **Nome segnale** selezionare **Ricerca log personalizzata**
   - **Query di ricerca** su `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type | where DataGB > 100`
   - **Logica avvisi** è **In base a** *numero di risultati* e **Condizione** è *Maggiore di* una **Soglia** pari a *0*
   - **Periodo di tempo** di *1440* minuti e **Frequenza di avviso** ogni *60* minuti, poiché i dati sull'utilizzo vengono aggiornati solo una volta all'ora.
- Per **Definire i dettagli dell'avviso** specificare quanto segue:
   - **Nome** su *Data volume greater than 100 GB in 24 hours* (Volume di dati maggiore di 100 GB in 24 ore)
   - **Gravità** su *Avviso*

Specificare un [gruppo di azioni](action-groups.md) esistente o crearne uno nuovo, in modo da ricevere una notifica se l'avviso del log corrisponde ai criteri.

Quando si crea l'avviso per la seconda query e si prevedono più di 100 GB di dati in 24 ore, impostare:

- Per **Definire la condizione dell'avviso**, specificare l'area di lavoro Log Analytics come destinazione della risorsa.
- Per **Criteri di avviso** specificare quanto segue:
   - **Nome segnale** selezionare **Ricerca log personalizzata**
   - **Query di ricerca** su `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`
   - **Logica avvisi** è **In base a** *numero di risultati* e **Condizione** è *Maggiore di* una **Soglia** pari a *0*
   - **Periodo di tempo** di *180* minuti e **Frequenza di avviso** ogni *60* minuti, poiché i dati sull'utilizzo vengono aggiornati solo una volta all'ora.
- Per **Definire i dettagli dell'avviso** specificare quanto segue:
   - **Nome** su *Data volume expected to be greater than 100 GB in 24 hours* (Volume di dati previsto maggiore di 100 GB in 24 ore)
   - **Gravità** su *Avviso*

Specificare un [gruppo di azioni](action-groups.md) esistente o crearne uno nuovo, in modo da ricevere una notifica se l'avviso del log corrisponde ai criteri.

Quando si riceve un avviso, seguire la procedura descritta nella sezione seguente per risolvere i problemi che determinano un utilizzo superiore al previsto.

## <a name="data-transfer-charges-using-log-analytics"></a>Spese di trasferimento dati tramite Log AnalyticsData transfer charges using Log Analytics

L'invio di dati a Log Analytics potrebbe comportare costi di larghezza di banda per i dati. Come descritto nella [pagina dei prezzi della larghezza di banda](https://azure.microsoft.com/pricing/details/bandwidth/)di Azure , il trasferimento dei dati tra i servizi di Azure situati in due aree addebitate come trasferimento di dati in uscita alla velocità normale. Il trasferimento dei dati in ingresso è gratuito. Tuttavia, questa tassa è molto piccola (pochi %) rispetto ai costi per l'inserimento di dati di Log Analytics. Di conseguenza, il controllo dei costi per Log Analytics deve concentrarsi sul volume di dati ingeriti e abbiamo indicazioni per aiutarti a capire che [qui](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume).   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Risoluzione dei problemi se Log Analytics non sta più raccogliendo dati

Se si sta usando il piano tariffario legacy Gratuito e sono stati inviati più di 500 MB di dati in un giorno, la raccolta dati si interrompe per il resto del giorno. Il raggiungimento del limite giornaliero è un motivo comune per cui Log Analytics interrompe la raccolta dei dati o per cui i dati mancano.  Log Analytics crea un evento di tipo operazione quando la raccolta dati si avvia e si arresta. Eseguire la query seguente per verificare se si raggiunge il limite giornaliero e se i dati sono mancanti: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Quando la raccolta dei dati si interrompe, OperationStatus è **Attenzione**. Quando la raccolta dei dati si avvia, OperationStatus è **Riuscito**. La tabella seguente descrive i motivi per cui raccolta dei dati si interrompe e un'azione consigliata per riprendere la raccolta dati:  

|Motivo dell'arresto della raccolta| Soluzione| 
|-----------------------|---------|
|Limite giornaliero del piano tariffario legacy Gratuito raggiunto |Attendere fino al giorno successivo per il riavvio automatico della raccolta oppure passare a un piano tariffario a pagamento.|
|È stato raggiunto il limite giornaliero dell'area di lavoro|Attendere il riavvio automatico della raccolta oppure aumentare il limite giornaliero per il volume di dati, come descritto nella sezione sulla gestione del volume di dati giornaliero massimo. Il tempo di ripristino del limite giornaliero viene visualizzato nella pagina **Gestione del volume dati**. |
|La sottoscrizione di Azure è in sospeso perché:<br> la versione di prova gratuita è terminata<br> Azure Pass è scaduto<br> Il limite di spesa mensile è stato raggiunto, ad esempio in una sottoscrizione MSDN o in una sottoscrizione di Visual Studio|Passare a una sottoscrizione a pagamento<br> Rimuovere il limite oppure attendere fino ripristino del limite|

Per ricevere una notifica quando la raccolta dei dati viene interrotta, usare i passaggi descritti in Creare avvisi *di limite dati giornalieri* per ricevere una notifica quando la raccolta dei dati viene interrotta. Utilizzare la procedura descritta in [Creare un gruppo](action-groups.md) di azioni per configurare un'azione di posta elettronica, webhook o runbook per la regola di avviso. 

## <a name="limits-summary"></a>Riepilogo dei limiti

Esistono alcuni limiti aggiuntivi di Log Analytics, alcuni dei quali dipendono dal piano tariffario di Log Analytics.There are some additional Log Analytics limits, some of which depend on the Log Analytics pricing tier. Questi sono documentati [qui](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces).


## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come usare la lingua di ricerca, vedere Ricerche nei log nei log di Monitoraggio di [Azure.See Log searches in Azure Monitor Logs](../log-query/log-query-overview.md) to learn how to use the search language. È possibile usare le query di ricerca per eseguire ulteriori analisi sui dati di utilizzo.
- Per ricevere una notifica quando vengono soddisfatti determinati criteri di ricerca, seguire la procedura descritta in [Creare un nuovo avviso del log](alerts-metric.md).
- Usare il [targeting della soluzione](../insights/solution-targeting.md) per raccogliere dati unicamente dai gruppi di computer necessari
- Per configurare un criterio efficace per la raccolta degli eventi, vedere [Criteri per i filtri del Centro sicurezza di Azure](../../security-center/security-center-enable-data-collection.md).
- Modificare [la configurazione dei contatori delle prestazioni](data-sources-performance-counters.md).
- Per modificare le impostazioni della raccolta eventi, esaminare la [configurazione del registro eventi](data-sources-windows-events.md).
- Per modificare le impostazioni della raccolta syslog, esaminare la configurazione di [syslog](data-sources-syslog.md).