---
title: Gestire l'utilizzo e i costi per i log di monitoraggio di Azure
description: Informazioni su come modificare il piano tariffario e gestire il volume dei dati e i criteri di conservazione per l'area di lavoro Log Analytics in monitoraggio di Azure.
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
ms.date: 05/04/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 601f1c224d6e1d756c27dc2478951682ce6bb4fd
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854756"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Gestire l'utilizzo e i costi con i log di monitoraggio di Azure

> [!NOTE]
> Questo articolo descrive come comprendere e controllare i costi per i log di monitoraggio di Azure. Un articolo correlato, [monitoraggio dell'utilizzo e dei costi stimati](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) descrive come visualizzare l'utilizzo e i costi stimati tra più funzionalità di monitoraggio di Azure per i diversi modelli tariffari. Tutti i prezzi e i costi indicati in questo articolo sono solo a scopo esemplificativo. 

Log di monitoraggio di Azure è progettato per scalare e supportare la raccolta, l'indicizzazione e l'archiviazione di grandi quantità di dati al giorno da qualsiasi origine nell'azienda o distribuita in Azure.  Anche se si tratta di uno strumento importante per l'organizzazione, è comunque fondamentale ottimizzare i costi. A tal fine, è importante comprendere che il costo di un'area di lavoro Log Analytics non è basato solo sul volume dei dati raccolti, dipende anche dal piano selezionato e dal tempo in cui si è scelto di archiviare i dati generati dalle origini connesse.  

In questo articolo viene esaminato come è possibile monitorare in modo proattivo il volume di dati e la crescita dell'archiviazione inseriti e definire i limiti per controllare i costi associati. 

## <a name="pricing-model"></a>Modello di prezzi

Il piano tariffario predefinito per Log Analytics è un modello con **pagamento in** base al consumo basato sul volume di dati inserito e, facoltativamente, per la conservazione dei dati più lunga. Il volume dei dati viene misurato come dimensione dei dati che verranno archiviati. Ogni area di lavoro Log Analytics viene addebitata come servizio separato e contribuisce alla fatturazione per la sottoscrizione di Azure. La quantità di inserimento dei dati può essere notevole a seconda dei fattori seguenti: 

  - Numero di soluzioni di gestione abilitate e relativa configurazione
  - Numero di macchine virtuali monitorate
  - Tipo di dati raccolti da ogni macchina virtuale monitorata 
  
Oltre al modello con pagamento in base al consumo, Log Analytics dispone di livelli di **prenotazione di capacità** che consentono di risparmiare fino al 25% rispetto al prezzo con pagamento in base al consumo. I prezzi per la prenotazione della capacità ti permettono di acquistare una prenotazione a partire da 100 GB/giorno. Qualsiasi utilizzo sopra il livello di prenotazione verrà fatturato in base alla tariffa con pagamento in base al consumo. I livelli di prenotazione della capacità hanno un periodo di impegno di 31 giorni. Durante il periodo di impegno, è possibile passare a un livello di prenotazione di capacità superiore, che riavvierà il periodo di impegno di 31 giorni, ma non sarà possibile tornare a un livello di prenotazione con pagamento in base al consumo o a un livello di prenotazione di capacità inferiore fino al termine del periodo dell'impegno. La fatturazione per i livelli di prenotazione della capacità viene eseguita su base giornaliera. [Scopri di più](https://azure.microsoft.com/pricing/details/monitor/) su log Analytics prezzi con pagamento in base al consumo e sulla prenotazione della capacità. 

In tutti i piani tariffari, il volume di dati viene calcolato da una rappresentazione di stringa dei dati perché è pronto per essere archiviato. Diverse [Proprietà comuni a tutti i tipi di dati](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) non sono incluse nel calcolo delle dimensioni dell'evento, `_ResourceId`tra `_ItemId`cui `_IsBillable` , `_BilledSize`e.

Si noti anche che alcune soluzioni, ad esempio il [Centro sicurezza di Azure](https://azure.microsoft.com/pricing/details/security-center/), la [gestione della configurazione](https://azure.microsoft.com/pricing/details/automation/) e di [Sentinel di Azure](https://azure.microsoft.com/pricing/details/azure-sentinel/) hanno i propri modelli tariffari. 

### <a name="log-analytics-clusters"></a>Cluster Log Analytics

I cluster Log Analytics sono raccolte di aree di lavoro in un singolo cluster di Azure Esplora dati gestito per supportare scenari avanzati quali [chiavi gestite dal cliente](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys).  I cluster Log Analytics supportano solo un modello di prezzi per la prenotazione della capacità a partire da 1000 GB/giorno con uno sconto del 25% rispetto ai prezzi con pagamento in base al consumo. Qualsiasi utilizzo sopra il livello di prenotazione verrà fatturato in base alla tariffa con pagamento in base al consumo. La prenotazione della capacità del cluster ha un periodo di impegno di 31 giorni dopo l'incremento del livello di prenotazione. Durante il periodo di impegno non è possibile ridurre il livello di prenotazione della capacità, ma è possibile aumentarlo in qualsiasi momento. Altre informazioni sulla [creazione di un cluster di log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#create-cluster-resource) e sull' [associazione di aree di lavoro](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#workspace-association-to-cluster-resource).  

Il livello di prenotazione della capacità del cluster viene configurato tramite a livello con `Capacity` Azure Resource Manager utilizzando `Sku`il parametro in. `Capacity` È specificato in unità di GB e può avere valori di 1000 GB/giorno o più in incrementi di 100 GB al giorno. Questa operazione è illustrata in dettaglio [qui](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#create-cluster-resource). Se il cluster necessita di una prenotazione superiore a 2000 GB/giorno, [LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com)contattaci all'indirizzo.

Poiché la fatturazione per i dati inseriti viene eseguita a livello di cluster, le aree di lavoro associate a un cluster non hanno più un piano tariffario. Le quantità di dati inseriti da ogni area di lavoro associata a un cluster vengono aggregate per calcolare la fattura giornaliera per il cluster. Si noti che le allocazioni per nodo dal [Centro sicurezza di Azure](https://docs.microsoft.com/azure/security-center/) vengono applicate a livello di area di lavoro prima di questa aggregazione di dati aggregati in tutte le aree di lavoro del cluster. La conservazione dei dati viene comunque fatturata a livello di area di lavoro. Si noti che la fatturazione del cluster viene avviata quando viene creato il cluster, indipendentemente dal fatto che le aree di lavoro siano state associate al cluster. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Stima dei costi per la gestione dell'ambiente 

Se non si usano ancora i log di monitoraggio di Azure, è possibile usare il [calcolatore dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/calculator/?service=monitor) per stimare il costo dell'uso di log Analytics. Per iniziare, immettere "monitoraggio di Azure" nella casella di ricerca e fare clic sul riquadro Monitoraggio di Azure risultante. Scorrere la pagina verso il basso fino a monitoraggio di Azure e selezionare Log Analytics dall'elenco a discesa tipo.  Qui è possibile immettere il numero di macchine virtuali e i GB di dati che si prevede di raccogliere da ogni macchina virtuale. Generalmente da 1 a 3 GB di dati mensili viene inserito da una tipica macchina virtuale di Azure. Se si stanno già valutando i log di monitoraggio di Azure, è possibile usare le statistiche dei dati dal proprio ambiente. Vedere di seguito per informazioni su come determinare il [numero di macchine virtuali monitorate](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) e il [volume di dati che l'area di lavoro sta gestendo](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume). 

## <a name="understand-your-usage-and-estimate-costs"></a>Comprendere i costi di utilizzo e stima

Se ora si usano i log di monitoraggio di Azure, è facile comprendere quali sono i costi che probabilmente si basano sui modelli di utilizzo recenti. A tale scopo, usare **log Analytics utilizzo e i costi stimati** per esaminare e analizzare l'utilizzo dei dati. Questo mostra la quantità di dati raccolti da ogni soluzione, la quantità di dati mantenuti e una stima dei costi in base alla quantità di dati inseriti e a qualsiasi conservazione aggiuntiva oltre la quantità inclusa.

![Utilizzo e costi stimati](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Per esplorare i dati in maggiore dettaglio, fare clic sull'icona nell'angolo superiore destro di uno dei grafici nella pagina **Utilizzo e costi stimati**. Ora è possibile usare questa query per esplorare i dettagli dell'utilizzo.  

![Visualizzazione dei log](media/manage-cost-storage/logs.png)

Dalla pagina **Utilizzo e costi stimati** è possibile rivedere il volume dei dati per il mese. Sono inclusi tutti i dati ricevuti e conservati nell'area di lavoro Log Analytics.  Fare clic su **Dettagli di utilizzo** nella parte superiore della pagina per visualizzare il dashboard Usage con informazioni sulle tendenze del volume dei dati per origine, computer e offerta. Per visualizzare e impostare un limite giornaliero o per modificare il periodo di conservazione, fare clic su **Gestione del volume dati**.
 
I costi di Log Analytics vengono addebitati nella fattura di Azure. È possibile visualizzare i dettagli della fattura di Azure nella sezione Fatturazione del portale di Azure oppure nel [portale di fatturazione di Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Visualizzazione dell'utilizzo Log Analytics nella fattura di Azure 

Azure fornisce una grande quantità di funzionalità utili in [Gestione costi di Azure](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) e nell'hub di fatturazione. Ad esempio, la funzionalità di analisi dei costi consente di visualizzare le spese per le risorse di Azure. L'aggiunta di un filtro per tipo di risorsa (a Microsoft. operationalinsights/Workspace per Log Analytics) consentirà di tenere traccia della spesa.

Una maggiore comprensione dell'utilizzo può essere ottenuta [scaricando l'utilizzo dal portale di Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). Nel foglio di calcolo scaricato è possibile visualizzare l'utilizzo per ogni risorsa di Azure, ad esempio Log Analytics area di lavoro, al giorno. In questo foglio di calcolo di Excel, l'utilizzo delle aree di lavoro Log Analytics è stato trovato filtrando la colonna "categoria contatore" per visualizzare "Insights and Analytics" (utilizzato da alcuni dei piani tariffari legacy) e "Log Analytics" e quindi aggiungendo un filtro nella colonna "ID istanza" che è "contiene l'area di lavoro". L'utilizzo viene visualizzato nella colonna "quantità utilizzata" e l'unità per ogni voce viene visualizzata nella colonna "unità di misura".  Sono disponibili altre informazioni che consentono di [comprendere la fattura Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="changing-pricing-tier"></a>Modifica del piano tariffario

Per modificare il piano tariffario Log Analytics dell'area di lavoro, 

1. Nella portale di Azure aprire **utilizzo e costi stimati** dall'area di lavoro in cui verrà visualizzato un elenco di ognuno dei piani tariffari disponibili per l'area di lavoro.

2. Esaminare i costi stimati per ognuno dei piani tariffari. Questa stima è basata sugli ultimi 31 giorni di utilizzo, quindi la stima dei costi si basa sugli ultimi 31 giorni rappresentativi dell'utilizzo tipico. Nell'esempio seguente è possibile vedere in che modo, in base ai modelli di dati degli ultimi 31 giorni, l'area di lavoro potrebbe avere un costo inferiore nel piano di pagamento a consumo (#1) rispetto al livello di prenotazione della capacità di 100 GB/giorno (#2).  

    ![Piani tariffari](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. Dopo aver esaminato i costi stimati in base agli ultimi 31 giorni di utilizzo, se si decide di modificare il piano tariffario, fare clic su **Seleziona**.  

È inoltre possibile [impostare il piano tariffario tramite Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) utilizzando `sku` il parametro`pricingTier` (nel modello di Azure Resource Manager). 

## <a name="legacy-pricing-tiers"></a>Piani tariffari legacy

Le sottoscrizioni che hanno un'area di lavoro Log Analytics o una risorsa Application Insights prima del 2 aprile 2018 o sono collegate a una Enterprise Agreement avviata prima dell'1 febbraio 2019, continueranno ad avere accesso per usare i piani tariffari legacy: **gratuito**, **autonomo (per GB)** e **per nodo (OMS)**.  Le aree di lavoro nel piano tariffario gratuito avranno un inserimento dati giornaliero limitato a 500 MB (eccetto i tipi di dati di sicurezza raccolti dal centro sicurezza di Azure) e la conservazione dei dati è limitata a 7 giorni. Il piano tariffario gratuito è destinato solo a scopo di valutazione. Le aree di lavoro nei piani tariffari autonomo o per nodo hanno una conservazione configurabile dall'utente da 30 a 730 giorni.

Il piano tariffario per nodo viene addebitato per ogni macchina virtuale monitorata (nodo) in base a una granularità oraria. Per ogni nodo monitorato, all'area di lavoro vengono allocati 500 MB di dati al giorno che non vengono fatturati. Questa allocazione viene aggregata a livello di area di lavoro. I dati inseriti sopra l'allocazione giornaliera aggregata dei dati vengono fatturati per GB come eccedenze di dati. Si noti che nella fattura, il servizio sarà **Insights e Analytics** per l'utilizzo log Analytics se l'area di lavoro è nel piano tariffario per nodo. 

> [!TIP]
> Se l'area di lavoro ha accesso al piano tariffario **per nodo** , ma ci si chiede se il costo è inferiore in un livello con pagamento in base al consumo, è possibile [usare la query seguente](#evaluating-the-legacy-per-node-pricing-tier) per ottenere facilmente un suggerimento. 

Le aree di lavoro create prima del 2016 aprile possono accedere anche ai piani tariffari **standard** e **Premium** originali con conservazione dei dati fissa rispettivamente di 30 e 365 giorni. Non è possibile creare nuove aree di lavoro nei piani tariffari **standard** o **Premium** . se un'area di lavoro viene spostata da questi livelli, non sarà possibile spostarla di nuovo. 

Altre informazioni sulle limitazioni del piano tariffario sono disponibili [qui](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Per usare i diritti che derivano dall'acquisto di OMS E1 Suite, OMS E2 Suite o un componente aggiuntivo di OMS per System Center, scegliere il piano tariffario *Per nodo* di Log Analytics.

## <a name="change-the-data-retention-period"></a>Cambiare il periodo di conservazione dei dati

La procedura seguente descrive come configurare il periodo di conservazione dei dati nell'area di lavoro. La conservazione dei dati può essere configurata da 30 a 730 giorni (2 anni) per tutte le aree di lavoro, a meno che non utilizzi il piano tariffario gratuito legacy. 

### <a name="default-retention"></a>Conservazione predefinita

Per impostare il periodo di conservazione predefinito per l'area di lavoro, 
 
1. Nell'portale di Azure, dall'area di lavoro, selezionare **utilizzo e costi stimati** nel riquadro sinistro.
2. Nella pagina **Utilizzo e costi stimati** fare clic su **Gestione del volume dati** nella parte superiore.
3. Nel riquadro spostare il dispositivo di scorrimento per aumentare o diminuire il numero di giorni e quindi fare clic su **OK**.  Se si usa il livello *gratuito*, non è possibile modificare il periodo di conservazione dei dati ed è necessario eseguire l'aggiornamento al piano a pagamento per controllare questa impostazione.

    ![Modificare l'impostazione di conservazione dei dati dell'area di lavoro](media/manage-cost-storage/manage-cost-change-retention-01.png)

Quando il periodo di conservazione viene ridotto, è previsto un periodo di prova di diversi giorni prima che i dati meno recenti vengano rimossi. 
    
Il periodo di memorizzazione può essere [impostato anche tramite Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) utilizzando il `retentionInDays` parametro. Inoltre, se si imposta la conservazione dei dati su 30 giorni, è possibile attivare un'eliminazione immediata dei dati meno recenti utilizzando `immediatePurgeDataOn30Days` il parametro, che può essere utile per gli scenari correlati alla conformità. Questa funzionalità viene esposta solo tramite Azure Resource Manager. 

Per impostazione predefinita, i `Usage` due `AzureActivity` tipi di dati--e--vengono conservati per 90 giorni e non è previsto alcun addebito per questo periodo di conservazione di 90 giorni. Questi tipi di dati sono anche privi di addebiti per l'inserimento di dati. 



### <a name="retention-by-data-type"></a>Conservazione per tipo di dati

È anche possibile specificare diverse impostazioni di conservazione per i singoli tipi di dati da 30 a 730 giorni (ad eccezione delle aree di lavoro nel piano tariffario gratuito legacy). Ogni tipo di dati è una risorsa secondaria dell'area di lavoro. Ad esempio, la tabella SecurityEvent può essere risolta in [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) come:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Si noti che il tipo di dati (tabella) fa distinzione tra maiuscole e minuscole.  Per ottenere le impostazioni di conservazione correnti per tipo di dati di un particolare tipo di dati (in questo esempio SecurityEvent), usare:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Per ottenere le impostazioni di memorizzazione correnti per ogni tipo di dati per tutti i tipi di dati nell'area di lavoro, è sufficiente omettere il tipo di dati specifico, ad esempio:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Per impostare la conservazione di un determinato tipo di dati (in questo esempio SecurityEvent) su 730 giorni, fare

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

I valori validi `retentionInDays` per sono compresi tra 30 e 730.

I `Usage` tipi `AzureActivity` di dati e non possono essere impostati con la conservazione personalizzata. Verranno accettati al massimo la conservazione predefinita dell'area di lavoro o 90 giorni. 

Un ottimo strumento per connettersi direttamente a Azure Resource Manager per impostare la conservazione in base al tipo di dati è lo strumento OSS [ARMclient](https://github.com/projectkudu/ARMClient).  Altre informazioni su ARMclient sono disponibili negli articoli di [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) e [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Ecco un esempio di uso di ARMClient, che imposta i dati di SecurityEvent su un periodo di conservazione di 730 giorni:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> Per ridurre i costi di conservazione dei dati, è possibile utilizzare l'impostazione della conservazione per i singoli tipi di dati.  Per i dati raccolti a partire dal 2019 ottobre (quando questa funzionalità è stata rilasciata), la riduzione della conservazione per alcuni tipi di dati può ridurre il costo di conservazione nel tempo.  Per i dati raccolti in precedenza, l'impostazione di un periodo di conservazione inferiore per un singolo tipo non influirà sui costi di conservazione.  

## <a name="manage-your-maximum-daily-data-volume"></a>Gestisci il volume di dati massimo giornaliero

È possibile configurare un limite giornaliero e limitare l'inserimento giornaliero per l'area di lavoro, tuttavia è necessario tenere presente che l'obiettivo non deve essere quello di raggiungere tale limite.  In caso contrario, si perdono i dati per il resto del giorno, situazione che può influire su altri servizi e soluzioni di Azure la cui funzionalità può dipendere da dati aggiornati disponibili nell'area di lavoro.  Di conseguenza, la possibilità di osservare e ricevere avvisi quando le condizioni di integrità delle risorse che supportano i servizi IT sono interessate.  Il limite giornaliero è destinato a essere utilizzato come metodo per gestire l'aumento imprevisto del volume di dati dalle risorse gestite e rimanere entro il limite o quando si desidera limitare gli addebiti non pianificati per l'area di lavoro.  

Subito dopo il raggiungimento del limite giornaliero, la raccolta di tipi di dati fatturabili viene arrestata per il resto del giorno. (La latenza inerente all'applicazione del limite giornaliero può indicare che il limite non viene applicato esattamente come il livello di limite giornaliero specificato). Viene visualizzato un messaggio di avviso nella parte superiore della pagina per l'area di lavoro Log Analytics selezionata e viene inviato un evento Operation alla tabella *Operation* nella categoria **LogManagement** . La raccolta dati riprende dopo l'ora di ripristino definita in *Daily limit will be set at* (Ora impostazione limite giornaliero). Si consiglia di definire una regola di avviso in base a questo evento operazione, per l'invio di una notifica quando viene raggiunta la soglia dei dati giornaliera. 

> [!WARNING]
> Il limite giornaliero non interrompe la raccolta di dati dal centro sicurezza di Azure, ad eccezione delle aree di lavoro in cui il Centro sicurezza di Azure è stato installato prima del 19 giugno 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificare la soglia dei dati giornaliera da definire

Vedere [Utilizzo e costi stimati di Log Analytics](usage-estimated-costs.md) per informazioni sulla tendenza nell'inserimento dati e sul limite di volume giornaliero da definire. Deve essere considerato con cautela, dal momento che l'utente ha vinto? t è in grado di monitorare le risorse dopo che è stato raggiunto il limite. 

### <a name="set-the-daily-cap"></a>Imposta il limite giornaliero

Nei passaggi seguenti viene descritto come configurare un limite per gestire il volume di dati che Log Analytics area di lavoro inserirà al giorno.  

1. Nell'area di lavoro selezionare **Utilizzo e costi stimati** nel riquadro a sinistra.
2. Nella pagina **Utilizzo e costi stimati** per l'area di lavoro selezionata fare clic su **Gestione del volume dati** nella parte superiore. 
3. Il limite giornaliero è **disattivato** per impostazione predefinita? fare clic **su** attiva per abilitarla, quindi impostare il limite del volume di dati in GB/giorno.

    ![Configurazione della soglia dei dati in Log Analytics](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Avvisa quando si raggiunge il limite giornaliero

Anche se nel portale di Azure viene visualizzato un segnale visivo quando viene raggiunta la soglia dei dati, questo comportamento potrebbe non soddisfare le esigenze aziendali per la gestione di problemi operativi che richiedono attenzione immediata.  Per ricevere una notifica di avviso, è possibile creare una nuova regola di avviso in Monitoraggio di Azure.  Per ulteriori informazioni, vedere [come creare, visualizzare e gestire gli avvisi](alerts-metric.md).

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
- Più nodi del previsto che inviano dati all'area di lavoro Log Analytics
- Sono presenti più dati del previsto che vengono inviati all'area di lavoro Log Analytics (forse a causa dell'avvio dell'uso di una nuova soluzione o di una modifica della configurazione di una soluzione esistente)

## <a name="understanding-nodes-sending-data"></a>Informazioni sui nodi che inviano dati

Per comprendere il numero di nodi che segnalano gli heartbeat dall'agente ogni giorno nell'ultimo mese, usare

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Per ottenere un conteggio dei nodi che inviano dati nelle ultime 24 ore, usare la query: 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Per ottenere un elenco di nodi che inviano dati (e la quantità di dati inviati da ogni), è possibile usare la query seguente:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!TIP]
> Usare queste `union withsource = tt *` query in modo sporadico in quanto le analisi tra i tipi di dati richiedono un utilizzo [intensivo delle risorse](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane) . Questa query sostituisce il vecchio modo per eseguire query sulle informazioni per computer con il tipo di dati Usage.  

## <a name="understanding-ingested-data-volume"></a>Informazioni sul volume di dati inseriti

Nella pagina **Utilizzo e costi stimati** il grafico *Inserimento dati per soluzione* mostra il volume totale dei dati inviati e la quantità inviata da ogni soluzione. In questo modo è possibile determinare tendenze specifiche, ad esempio se l'utilizzo dei dati complessivo (o da parte di una particolare soluzione) sta aumentando, è stabile o sta diminuendo. 

### <a name="data-volume-for-specific-events"></a>Volume di dati per eventi specifici

Per esaminare le dimensioni dei dati inseriti per un determinato set di eventi, è possibile eseguire una query sulla tabella specifica (in questo esempio `Event`) e quindi limitare la query agli eventi di interesse (in questo esempio ID evento 5145 o 5156):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Si noti che la `where IsBillable = true` clausola filtra i tipi di dati di determinate soluzioni per le quali non è previsto alcun addebito per l'inserimento. 

### <a name="data-volume-by-solution"></a>Volume dati per soluzione

La query utilizzata per visualizzare il volume di dati fatturabile per soluzione nell'ultimo mese (escluso l'ultimo giorno parziale) è:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

La clausola con `TimeGenerated` è solo per assicurarsi che l'esperienza di query nel portale di Azure riguarderà oltre le 24 ore predefinite. Quando si usa il tipo di dati `StartTime` Usage `EndTime` e rappresentano i bucket temporali per i quali vengono presentati i risultati. 

### <a name="data-volume-by-type"></a>Volume di dati per tipo

È possibile eseguire ulteriormente il drill-down per visualizzare le tendenze dei dati per tipo di dati:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

O per visualizzare una tabella in base alla soluzione e al tipo per l'ultimo mese,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Volume di dati per computer

Il `Usage` tipo di dati non include informazioni a livello di computer. Per visualizzare le **dimensioni** dei dati inseriti per computer, utilizzare la `_BilledSize` [proprietà](log-standard-properties.md#_billedsize), che fornisce la dimensione in byte:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

La `_IsBillable` [Proprietà](log-standard-properties.md#_isbillable) specifica se i dati inseriti comporteranno addebiti.

Per visualizzare il **numero** di eventi fatturabili inseriti per computer, usare 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Volume di dati per risorsa di Azure, gruppo di risorse o sottoscrizione

Per i dati dei nodi ospitati in Azure, è possibile ottenere le **dimensioni** dei dati inseriti __per computer__, usare la [proprietà](log-standard-properties.md#_resourceid)_ResourceId, che fornisce il percorso completo della risorsa:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Per i dati dei nodi ospitati in Azure, è possibile ottenere le **dimensioni** dei dati inseriti __per ogni sottoscrizione__di `_ResourceId` Azure, analizzare la proprietà come:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Se `subscriptionId` si `resourceGroup` modifica in, viene visualizzato il volume dei dati inseriti fatturabile per gruppo di risorse di Azure. 

> [!WARNING]
> Benché siano ancora inclusi nello schema, alcuni campi del tipo di dati Utilizzo sono stati deprecati e i rispettivi valori non verranno più popolati. Si tratta del campo **Computer** e dei campi correlati all'inserimento, ossia **TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** e **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Esecuzione di query per i tipi di dati comuni

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

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Recupero dei nodi come fatturato nel piano tariffario per nodo

Per ottenere un elenco di computer che verranno fatturati come nodi se l'area di lavoro è nel piano tariffario per nodo Legacy, cercare i nodi che inviano **tipi di dati fatturati** (alcuni tipi di dati sono gratuiti). A tale scopo, utilizzare la `_IsBillable` [Proprietà](log-standard-properties.md#_isbillable) e utilizzare il campo più a sinistra del nome di dominio completo. Viene restituito il numero di computer con dati fatturati all'ora (ovvero la granularità con cui i nodi vengono conteggiati e fatturati):

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Recupero dei conteggi dei nodi di sicurezza e automazione

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

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>Valutazione del piano tariffario per nodo Legacy

La decisione di determinare se le aree di lavoro con accesso al piano tariffario **per nodo** legacy sono migliori rispetto a tale livello o in un attuale livello di prenotazione con **pagamento in base al** consumo o **capacità** è spesso difficile per i clienti.  Questo implica la comprensione del compromesso tra il costo fisso per ogni nodo monitorato nel piano tariffario per nodo e l'allocazione dei dati inclusa di 500 MB/nodo/giorno e il costo di pagamento solo per i dati inseriti nel livello di pagamento in base al consumo (per GB). 

Per semplificare questa valutazione, è possibile usare la query seguente per formulare un suggerimento per il piano tariffario ottimale in base ai modelli di utilizzo di un'area di lavoro.  Questa query esamina i nodi monitorati e i dati inseriti in un'area di lavoro negli ultimi 7 giorni e per ogni giorno valuta il piano tariffario ottimale. Per usare la query, è necessario specificare se l'area di lavoro usa il Centro sicurezza di Azure `workspaceHasSecurityCenter` `true` impostando `false`su o e quindi, facoltativamente, aggiornando i prezzi per nodo e per GB ricevuti dal Organizaiton. 

```kusto
// Set these parameters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerGBPrice = 2.30; // Enter your price per GB 
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
union withsource = tt * 
| where TimeGenerated >= startofday(now(-7d)) and TimeGenerated < startofday(now())
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
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

Questa query non è una replica esatta del modo in cui viene calcolato l'utilizzo, ma funzionerà per fornire indicazioni sui livelli di prezzo nella maggior parte dei casi.  

## <a name="create-an-alert-when-data-collection-is-high"></a>Crea un avviso quando la raccolta dati è elevata

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
   - **Nome del segnale** selezionare **Ricerca log personalizzata**
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
   - **Nome del segnale** selezionare **Ricerca log personalizzata**
   - **Query di ricerca** su `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`
   - **Logica avvisi** è **In base a** *numero di risultati* e **Condizione** è *Maggiore di* una **Soglia** pari a *0*
   - **Periodo di tempo** di *180* minuti e **Frequenza di avviso** ogni *60* minuti, poiché i dati sull'utilizzo vengono aggiornati solo una volta all'ora.
- Per **Definire i dettagli dell'avviso** specificare quanto segue:
   - **Nome** su *Data volume expected to be greater than 100 GB in 24 hours* (Volume di dati previsto maggiore di 100 GB in 24 ore)
   - **Gravità** su *Avviso*

Specificare un [gruppo di azioni](action-groups.md) esistente o crearne uno nuovo, in modo da ricevere una notifica se l'avviso del log corrisponde ai criteri.

Quando si riceve un avviso, seguire la procedura descritta nella sezione seguente per risolvere i problemi che determinano un utilizzo superiore al previsto.

## <a name="data-transfer-charges-using-log-analytics"></a>Addebiti per il trasferimento dei dati tramite Log Analytics

L'invio di dati a Log Analytics potrebbe influire sulla larghezza di banda dei dati. Come descritto nella pagina dei prezzi per la [larghezza di banda di Azure](https://azure.microsoft.com/pricing/details/bandwidth/), il trasferimento dei dati tra i servizi di Azure si trova in due aree addebitate come trasferimento di dati in uscita alla tariffa normale. Il trasferimento dei dati in ingresso è gratuito. Tuttavia, questo importo è molto ridotto (pochi%) rispetto ai costi per l'inserimento dei dati Log Analytics. Di conseguenza, il controllo dei costi per Log Analytics deve concentrarsi sul volume di dati inserito e sono disponibili indicazioni utili per comprendere [questo comportamento.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)   


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

Per ricevere una notifica quando la raccolta dati si interrompe, seguire i passaggi descritti in creare un avviso di *protezione dati giornaliera* per ricevere una notifica quando la raccolta dati viene arrestata. Usare la procedura descritta in [creare un gruppo di azione](action-groups.md) per configurare un'azione di posta elettronica, webhook o Runbook per la regola di avviso. 

## <a name="limits-summary"></a>Riepilogo dei limiti

Esistono alcuni limiti di Log Analytics aggiuntivi, alcuni dei quali dipendono dal piano tariffario Log Analytics. Questi sono descritti [qui](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces).


## <a name="next-steps"></a>Passaggi successivi

- Vedere [ricerche nei log nei](../log-query/log-query-overview.md) log di monitoraggio di Azure per informazioni su come usare il linguaggio di ricerca. È possibile usare le query di ricerca per eseguire ulteriori analisi sui dati di utilizzo.
- Per ricevere una notifica quando vengono soddisfatti determinati criteri di ricerca, seguire la procedura descritta in [Creare un nuovo avviso del log](alerts-metric.md).
- Usare il [targeting della soluzione](../insights/solution-targeting.md) per raccogliere dati unicamente dai gruppi di computer necessari
- Per configurare un criterio efficace per la raccolta degli eventi, vedere [Criteri per i filtri del Centro sicurezza di Azure](../../security-center/security-center-enable-data-collection.md).
- Modificare la [configurazione del contatore delle prestazioni](data-sources-performance-counters.md).
- Per modificare le impostazioni della raccolta di eventi, esaminare la [configurazione del registro eventi](data-sources-windows-events.md).
- Per modificare le impostazioni della raccolta syslog, vedere [configurazione di syslog](data-sources-syslog.md).
