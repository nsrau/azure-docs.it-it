---
title: Gestire l'utilizzo e i costi per i log di monitoraggio di Azure | Microsoft Docs
description: Informazioni su come modificare il piano tariffario e gestire il volume dei dati e i criteri di conservazione per l'area di lavoro Log Analytics in monitoraggio di Azure.
services: azure-monitor
documentationcenter: azure-monitor
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/17/2019
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: 1480418a70166887e7327452d407f78c2c992378
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72597310"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Gestire l'utilizzo e i costi con i log di monitoraggio di Azure

> [!NOTE]
> Questo articolo descrive come comprendere e controllare i costi per i log di monitoraggio di Azure. Un articolo correlato, [monitoraggio dell'utilizzo e dei costi stimati](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) descrive come visualizzare l'utilizzo e i costi stimati tra più funzionalità di monitoraggio di Azure per i diversi modelli tariffari.

Log di monitoraggio di Azure è progettato per scalare e supportare la raccolta, l'indicizzazione e l'archiviazione di grandi quantità di dati al giorno da qualsiasi origine nell'azienda o distribuita in Azure.  Anche se si tratta di uno strumento importante per l'organizzazione, è comunque fondamentale ottimizzare i costi. A tal fine, è importante comprendere che il costo di un'area di lavoro Log Analytics non è basato solo sul volume dei dati raccolti, dipende anche dal piano selezionato e dal tempo in cui si è scelto di archiviare i dati generati dalle origini connesse.  

In questo articolo viene esaminato come è possibile monitorare in modo proattivo il volume di dati e la crescita dell'archiviazione inseriti e definire i limiti per controllare i costi associati. 

## <a name="pricing-model"></a>Modello tariffario

Il piano tariffario predefinito per Log Analytics è un modello con **pagamento in** base al consumo basato sul volume di dati inserito e, facoltativamente, per la conservazione dei dati più lunga. Il volume dei dati viene misurato come dimensione dei dati che verranno archiviati. Ogni area di lavoro Log Analytics viene addebitata come servizio separato e contribuisce alla fatturazione per la sottoscrizione di Azure. La quantità di inserimento dei dati può essere notevole a seconda dei fattori seguenti: 

  - Numero di soluzioni di gestione abilitate e relativa configurazione (ad esempio 
  - Numero di macchine virtuali monitorate
  - Tipo di dati raccolti da ogni macchina virtuale monitorata 
  
Oltre al modello con pagamento in base al consumo, sono state introdotte le prenotazioni di **capacità** per log Analytics che consentono di risparmiare fino al 25% rispetto al prezzo con pagamento in base al consumo. I prezzi per la prenotazione della capacità ti permettono di acquistare una prenotazione a partire da 100 GB/giorno. Qualsiasi utilizzo sopra il livello di prenotazione verrà fatturato in base alla tariffa con pagamento in base al consumo. [Scopri di più](https://azure.microsoft.com/pricing/details/monitor/) su log Analytics prezzi con pagamento in base al consumo e sulla prenotazione della capacità. 

Si noti che alcune soluzioni, ad esempio il [Centro sicurezza di Azure](https://azure.microsoft.com/pricing/details/security-center/) e [Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/), hanno un proprio modello di determinazione dei prezzi. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Stima dei costi per la gestione dell'ambiente 

Se non si usano ancora i log di monitoraggio di Azure, è possibile usare il [calcolatore dei prezzi di monitoraggio di Azure](https://azure.microsoft.com/pricing/calculator/?service=monitor) per stimare il costo dell'uso di log Analytics. Per iniziare, immettere "monitoraggio di Azure" nella casella di ricerca e fare clic sul riquadro Monitoraggio di Azure risultante. Scorrere la pagina verso il basso fino a monitoraggio di Azure e selezionare Log Analytics dall'elenco a discesa tipo.  Qui è possibile immettere il numero di macchine virtuali e i GB di dati che si prevede di raccogliere da ogni macchina virtuale. Typcially da 1 a 3 GB di dati mensili viene inserito da una tipica macchina virtuale di Azure. Se si stanno già valutando i log di monitoraggio di Azure, è possibile usare le statistiche dei dati dal proprio ambiente. Vedere di seguito per informazioni su come determinare il [numero di macchine virtuali monitorate](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) e il [volume di dati che l'area di lavoro sta gestendo](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume). 

## <a name="understand-your-usage-and-estimate-costs"></a>Comprendere i costi di utilizzo e stima

Se ora si usano i log di monitoraggio di Azure, è facile comprendere quali sono i costi che probabilmente si basano sui modelli di utilizzo recenti. A tale scopo, usare **log Analytics utilizzo e i costi stimati** per esaminare e analizzare l'utilizzo dei dati. Questa pagina mostra quanti dati vengono raccolti da ogni soluzione, quanti dati vengono conservati e una stima dei costi in base alla quantità di dati inseriti e a eventuali altri dati conservati oltre la quantità inclusa.

![Utilizzo e costi stimati](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Per esplorare i dati in maggiore dettaglio, fare clic sull'icona nell'angolo superiore destro di uno dei grafici nella pagina **Utilizzo e costi stimati**. Ora è possibile usare questa query per esplorare i dettagli dell'utilizzo.  

![Visualizzazione dei log](media/manage-cost-storage/logs.png)

Dalla pagina **Utilizzo e costi stimati** è possibile rivedere il volume dei dati per il mese. Sono inclusi tutti i dati ricevuti e conservati nell'area di lavoro Log Analytics.  Fare clic su **Dettagli di utilizzo** nella parte superiore della pagina per visualizzare il dashboard Usage con informazioni sulle tendenze del volume dei dati per origine, computer e offerta. Per visualizzare e impostare un limite giornaliero o per modificare il periodo di conservazione, fare clic su **Gestione del volume dati**.
 
I costi di Log Analytics vengono addebitati nella fattura di Azure. È possibile visualizzare i dettagli della fattura di Azure nella sezione Fatturazione del portale di Azure oppure nel [portale di fatturazione di Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Visualizzazione dell'utilizzo Log Analytics nella fattura di Azure 

Azure fornisce una grande quantità di funzionalità utili in [Gestione costi di Azure](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) e nell'hub di fatturazione. Ad esempio, la funzionalità di analisi dei costi consente di visualizzare le spese per le risorse di Azure. L'aggiunta di un filtro per tipo di risorsa (a Microsoft. operationalinsights/Workspace per Log Analytics) consentirà di tenere traccia della spesa.

Una maggiore comprensione dell'utilizzo può essere ottenuta [scaricando l'utilizzo dal portale di Azure](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). Nel foglio di calcolo scaricato è possibile visualizzare l'utilizzo per ogni risorsa di Azure, ad esempio Log Analytics area di lavoro, al giorno. In questo foglio di calcolo di Excel, l'utilizzo delle aree di lavoro di Log Analytics è disponibile filtrando prima di tutto la colonna "categoria contatore" per visualizzare "Insights and Analytics" (utilizzato da alcuni dei piani tariffari legacy) e "Log Analytics" e quindi aggiungendo un filtro per l'istanza ID "la colonna" contiene l'area di lavoro ". L'utilizzo viene visualizzato nella colonna "quantità utilizzata" e l'unità per ogni voce viene visualizzata nella colonna "unità di misura".  Sono disponibili altre informazioni che consentono di [comprendere la fattura Microsoft Azure](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="manage-your-maximum-daily-data-volume"></a>Gestisci il volume di dati massimo giornaliero

È possibile configurare un limite giornaliero e limitare l'inserimento giornaliero per l'area di lavoro, tuttavia è necessario tenere presente che l'obiettivo non deve essere quello di raggiungere tale limite.  In caso contrario, si perdono i dati per il resto del giorno, situazione che può influire su altri servizi e soluzioni di Azure la cui funzionalità può dipendere da dati aggiornati disponibili nell'area di lavoro.  Di conseguenza, la possibilità di osservare e ricevere avvisi quando le condizioni di integrità delle risorse che supportano i servizi IT sono interessate.  Il limite giornaliero è destinato a essere utilizzato come metodo per gestire l'aumento imprevisto del volume di dati dalle risorse gestite e rimanere entro il limite o quando si desidera limitare gli addebiti non pianificati per l'area di lavoro.  

Quando viene raggiunto il limite giornaliero, la raccolta di tipi di dati fatturabili viene arrestata per il resto del giorno. Nella parte superiore della pagina per l'area di lavoro Log Analytics selezionata viene visualizzato un banner di avviso e viene inviato un evento di tipo operazione alla tabella *Operazione* nella categoria **LogManagement**. La raccolta dati riprende dopo l'ora di ripristino definita in *Daily limit will be set at* (Ora impostazione limite giornaliero). Si consiglia di definire una regola di avviso in base a questo evento operazione, per l'invio di una notifica quando viene raggiunta la soglia dei dati giornaliera. 

> [!NOTE]
> Il limite giornaliero non interrompe la raccolta di dati dal centro sicurezza di Azure, ad eccezione delle aree di lavoro in cui il Centro sicurezza di Azure è stato installato prima del 19 giugno 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificare la soglia dei dati giornaliera da definire

Vedere [Utilizzo e costi stimati di Log Analytics](usage-estimated-costs.md) per informazioni sulla tendenza nell'inserimento dati e sul limite di volume giornaliero da definire. Fare attenzione nella scelta del limite, in quanto non sarà possibile monitorare le risorse una volta raggiunto il limite. 

### <a name="set-the-daily-cap"></a>Imposta il limite giornaliero

Nei passaggi seguenti viene descritto come configurare un limite per gestire il volume di dati che Log Analytics area di lavoro inserirà al giorno.  

1. Nell'area di lavoro selezionare **Utilizzo e costi stimati** nel riquadro a sinistra.
2. Nella pagina **Utilizzo e costi stimati** per l'area di lavoro selezionata fare clic su **Gestione del volume dati** nella parte superiore. 
3. Per impostazione predefinita, il limite giornaliero è impostato su **DISATTIVA**. Fare clic su **ATTIVA** per abilitarlo e quindi impostare il volume di dati in GB/giorno.

    ![Log Analytics configurare il limite dei dati](media/manage-cost-storage/set-daily-volume-cap-01.png)

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

## <a name="change-the-data-retention-period"></a>Cambiare il periodo di conservazione dei dati

La procedura seguente descrive come configurare il periodo di conservazione dei dati nell'area di lavoro.

### <a name="default-retention"></a>Conservazione predefinita

Per impostare il periodo di conservazione predefinito per l'area di lavoro, 
 
1. Dall'area di lavoro del portale di Azure selezionare **utilizzo e costi stimati** nel riquadro sinistro.
2. Nella pagina **Utilizzo e costi stimati** fare clic su **Gestione del volume dati** nella parte superiore.
3. Nel riquadro spostare il dispositivo di scorrimento per aumentare o diminuire il numero di giorni e quindi fare clic su **OK**.  Se si usa il livello *gratuito*, non è possibile modificare il periodo di conservazione dei dati ed è necessario eseguire l'aggiornamento al piano a pagamento per controllare questa impostazione.

    ![Modificare l'impostazione di conservazione dei dati dell'area di lavoro](media/manage-cost-storage/manage-cost-change-retention-01.png)
    
Il periodo di memorizzazione può essere [impostato anche tramite Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) utilizzando il parametro `retentionInDays`. Inoltre, se si imposta la conservazione dei dati su 30 giorni, è possibile attivare un'eliminazione immediata dei dati meno recenti utilizzando il parametro `immediatePurgeDataOn30Days`, che può essere utile per gli scenari correlati alla conformità. Questa funzionalità viene esposta solo tramite Azure Resource Manager. 

Due tipi di dati, `Usage` e `AzureActivity`, vengono conservati per 90 giorni per impostazione predefinita e non sono previsti addebiti per questo periodo di conservazione di 90 giorni. Questi tipi di dati sono anche privi di addebiti per l'inserimento di dati. 

### <a name="retention-by-data-type"></a>Conservazione per tipo di dati

È anche possibile specificare diverse impostazioni di conservazione per i singoli tipi di dati. Ogni tipo di dati è una risorsa secondaria dell'area di lavoro. Ad esempio, la tabella SecurityEvent può essere risolta in [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) come:

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

I tipi di dati `Usage` e `AzureActivity` non possono essere impostati con la conservazione personalizzata. Verranno accettati al massimo la conservazione predefinita dell'area di lavoro o 90 giorni. 

Un ottimo strumento per connettersi direttamente a Azure Resource Manager per impostare la conservazione in base al tipo di dati è lo strumento OSS [ARMclient](https://github.com/projectkudu/ARMClient).  Altre informazioni su ARMclient sono disponibili negli articoli di [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) e [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Ecco un esempio usando ARMClient, impostando i dati di SecurityEvent su un periodo di conservazione di 730 giorni:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!NOTE]
> Per ridurre i costi di conservazione dei dati, è possibile utilizzare l'impostazione della conservazione per i singoli tipi di dati.  Per i dati raccolti a partire dal 2019 ottobre (quando questa funzionalità è stata rilasciata), la riduzione della conservazione per alcuni tipi di dati può ridurre il costo di conservazione nel tempo.  Per i dati raccolti in precedenza, l'impostazione di un periodo di conservazione inferiore per un singolo tipo non influirà sui costi di conservazione.  

## <a name="legacy-pricing-tiers"></a>Piani tariffari legacy

Le sottoscrizioni che hanno un'area di lavoro Log Analytics o una risorsa Application Insights prima del 2 aprile 2018 o sono collegate a una Enterprise Agreement avviata prima del 1 ° febbraio 2019, continueranno ad avere accesso per usare i piani tariffari legacy: **gratuito**, **Autonomo (per GB)** e **per nodo (OMS)** .  Le aree di lavoro nel piano tariffario gratuito avranno un inserimento dati giornaliero limitato a 500 MB (eccetto i tipi di dati di sicurezza raccolti dal centro sicurezza di Azure) e la conservazione dei dati è limitata a 7 giorni. Il piano tariffario gratuito è destinato solo a scopo di valutazione. Le aree di lavoro nei piani tariffari autonomo o per nodo hanno una conservazione configurabile dall'utente fino a 2 anni. 

Le aree di lavoro create prima del 2016 aprile possono accedere anche ai piani tariffari **standard** e **Premium** originali con conservazione dei dati fissa rispettivamente di 30 e 365 giorni. Non è possibile creare nuove aree di lavoro nei piani tariffari **standard** o **Premium** . se un'area di lavoro viene spostata da questi livelli, non sarà possibile spostarla di nuovo. 

Altre informazioni sulle limitazioni del piano tariffario sono disponibili [qui](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Per usare i diritti che derivano dall'acquisto di OMS E1 Suite, OMS E2 Suite o un componente aggiuntivo di OMS per System Center, scegliere il piano tariffario *Per nodo* di Log Analytics.


## <a name="changing-pricing-tier"></a>Modifica del piano tariffario

Se l'area di lavoro Log Analytics ha accesso ai piani tariffari esistenti, modificare i piani tariffari esistenti:

1. Nel riquadro delle sottoscrizioni di Log Analytics del portale di Azure selezionare un'area di lavoro.

2. Nel riquadro dell'area di lavoro selezionare **Piano tariffario** in **Generale**.  

3. In **Piano tariffario** selezionare un piano tariffario e quindi fare clic su **Seleziona**.  
    ![Piano tariffario selezionato](media/manage-cost-storage/workspace-pricing-tier-info.png)

È anche possibile [impostare il piano tariffario tramite Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) usando il parametro `sku` (`pricingTier` nel modello ARM). 

## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Risoluzione dei problemi se Log Analytics non sta più raccogliendo dati

Se si sta usando il piano tariffario legacy Gratuito e sono stati inviati più di 500 MB di dati in un giorno, la raccolta dati si interrompe per il resto del giorno. Il raggiungimento del limite giornaliero è un motivo comune per cui Log Analytics interrompe la raccolta dei dati o per cui i dati mancano.  Log Analytics crea un evento di tipo operazione quando la raccolta dati si avvia e si arresta. Eseguire la query seguente per verificare se si raggiunge il limite giornaliero e se i dati sono mancanti: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Quando la raccolta dati si interrompe, OperationStatus è un **avviso**. Quando viene avviata la raccolta dei dati, OperationStatus è **riuscito**. La tabella seguente descrive i motivi per cui raccolta dei dati si interrompe e un'azione consigliata per riprendere la raccolta dati:  

|Motivo dell'arresto della raccolta| Soluzione| 
|-----------------------|---------|
|Limite giornaliero del piano tariffario legacy Gratuito raggiunto |Attendere fino al giorno successivo per il riavvio automatico della raccolta oppure passare a un piano tariffario a pagamento.|
|È stato raggiunto il limite giornaliero dell'area di lavoro|Attendere il riavvio automatico della raccolta oppure aumentare il limite giornaliero per il volume di dati, come descritto nella sezione sulla gestione del volume di dati giornaliero massimo. Il tempo di ripristino del limite giornaliero viene visualizzato nella pagina **Gestione del volume dati**. |
|La sottoscrizione di Azure è in sospeso perché:<br> la versione di prova gratuita è terminata<br> Azure Pass è scaduto<br> Il limite di spesa mensile è stato raggiunto, ad esempio in una sottoscrizione MSDN o in una sottoscrizione di Visual Studio|Passare a una sottoscrizione a pagamento<br> Rimuovere il limite oppure attendere fino ripristino del limite|

Per ricevere una notifica quando la raccolta dati si interrompe, seguire i passaggi descritti in creare un avviso di *protezione dati giornaliera* per ricevere una notifica quando la raccolta dati viene arrestata. Usare la procedura descritta in [creare un gruppo di azione](action-groups.md) per configurare un'azione di posta elettronica, webhook o Runbook per la regola di avviso. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Risoluzione dei problemi che determinano un utilizzo superiore al previsto

Un utilizzo più elevato è dovuto a una o entrambe le cause seguenti:
- Più nodi del previsto che inviano dati all'area di lavoro Log Analytics
- Più dati del previsto inviato ad area di lavoro Log Analytics

## <a name="understanding-nodes-sending-data"></a>Informazioni sui nodi che inviano dati

Per comprendere il numero di computer che segnalano gli heartbeat ogni giorno nell'ultimo mese, utilizzare

```kusto
Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```

Per ottenere un elenco di computer che verranno fatturati come nodi se l'area di lavoro è nel piano tariffario per nodo Legacy, cercare i nodi che inviano **tipi di dati fatturati** (alcuni tipi di dati sono gratuiti). A tale scopo, utilizzare la [proprietà](log-standard-properties.md#_isbillable) `_IsBillable` e utilizzare il campo più a sinistra del nome di dominio completo. Viene restituito l'elenco dei computer con i dati fatturati:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

Il numero di nodi fatturabili visualizzati può essere stimato come segue: 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| billableNodes=dcount(computerName)
```

> [!NOTE]
> Usare queste query `union withsource = tt *` solo se necessario, poiché le analisi tra tipi di dati sono costose. Questa query sostituisce il vecchio modo per eseguire query sulle informazioni per computer con il tipo di dati Usage.  

Un calcolo più accurato di ciò che verrà effettivamente fatturato consiste nell'ottenere il numero di computer all'ora che inviano tipi di dati fatturati. Per le aree di lavoro nel piano tariffario legacy per nodo, Log Analytics calcola il numero di nodi che devono essere fatturati su base oraria. 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

## <a name="understanding-ingested-data-volume"></a>Informazioni sul volume di dati inseriti

Nella pagina **Utilizzo e costi stimati** il grafico *Inserimento dati per soluzione* mostra il volume totale dei dati inviati e la quantità inviata da ogni soluzione. In questo modo è possibile determinare tendenze specifiche, ad esempio se l'utilizzo dei dati complessivo (o da parte di una particolare soluzione) sta aumentando, è stabile o sta diminuendo. La query usata per generare questi dati è

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), Solution| render barchart
```

Si noti che la clausola "where IsBillable = true" esclude i tipi di dati da determinate soluzioni per le quali non è addebitato alcun inserimento. 

È possibile approfondire ulteriormente l'analisi per visualizzare le tendenze relative a tipi di dati specifici, ad esempio per studiare i dati risultanti dai log di IIS:

```kusto
Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1000. by bin(TimeGenerated, 1d), Solution| render barchart
```

### <a name="data-volume-by-computer"></a>Volume di dati per computer

Per visualizzare le **dimensioni** degli eventi fatturabili inseriti per computer, utilizzare la [Proprietà](log-standard-properties.md#_billedsize)`_BilledSize`, che fornisce la dimensione in byte:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize Bytes=sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

La [proprietà](log-standard-properties.md#_isbillable) `_IsBillable` specifica se i dati inseriti comporteranno addebiti.

Per visualizzare il numero di eventi **fatturabili** inseriti per computer, usare 

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount=count() by computerName  | sort by count_ nulls last
```

Per sapere il numero di tipi dati fatturabili che inviano dati a uno specifico computer, usare:

```kusto
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Volume di dati per risorsa di Azure, gruppo di risorse o sottoscrizione

Per i dati dei nodi ospitati in Azure, è possibile ottenere le **dimensioni** degli eventi fatturabili inseriti __per computer__, usare la [proprietà](log-standard-properties.md#_resourceid)_ResourceId, che fornisce il percorso completo della risorsa:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Per i dati dei nodi ospitati in Azure, è possibile ottenere le **dimensioni** degli eventi fatturabili inseriti __per ogni sottoscrizione di Azure__, analizzare la proprietà `_ResourceId` come:

```kusto
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Se si modifica `subscriptionId` per `resourceGroup`, il volume dei dati inseriti fatturabile viene visualizzato in base al gruppo di risorse di Azure. 


> [!NOTE]
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
- **Criteri di avviso** consente di specificare quanto segue:
   - Per **Nome segnale** selezionare **Ricerca log personalizzata**
   - **Query di ricerca** su `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type | where DataGB > 100`
   - **Logica avvisi** è **In base a** *numero di risultati* e **Condizione** è *Maggiore di* una **Soglia** pari a *0*
   - **Periodo di tempo** di *1440* minuti e **Frequenza di avviso** ogni *60* minuti, poiché i dati sull'utilizzo vengono aggiornati solo una volta all'ora.
- **Definire i dettagli dell'avviso** consente di specificare quanto segue:
   - **Nome** su *Data volume greater than 100 GB in 24 hours* (Volume di dati maggiore di 100 GB in 24 ore)
   - **Gravità** su *Avviso*

Specificare un [gruppo di azioni](action-groups.md) esistente o crearne uno nuovo, in modo da ricevere una notifica se l'avviso del log corrisponde ai criteri.

Quando si crea l'avviso per la seconda query e si prevedono più di 100 GB di dati in 24 ore, impostare:

- Per **Definire la condizione dell'avviso**, specificare l'area di lavoro Log Analytics come destinazione della risorsa.
- **Criteri di avviso** consente di specificare quanto segue:
   - Per **Nome segnale** selezionare **Ricerca log personalizzata**
   - **Query di ricerca** su `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`
   - **Logica avvisi** è **In base a** *numero di risultati* e **Condizione** è *Maggiore di* una **Soglia** pari a *0*
   - **Periodo di tempo** di *180* minuti e **Frequenza di avviso** ogni *60* minuti, poiché i dati sull'utilizzo vengono aggiornati solo una volta all'ora.
- **Definire i dettagli dell'avviso** consente di specificare quanto segue:
   - **Nome** su *Data volume expected to be greater than 100 GB in 24 hours* (Volume di dati previsto maggiore di 100 GB in 24 ore)
   - **Gravità** su *Avviso*

Specificare un [gruppo di azioni](action-groups.md) esistente o crearne uno nuovo, in modo da ricevere una notifica se l'avviso del log corrisponde ai criteri.

Quando si riceve un avviso, seguire la procedura descritta nella sezione seguente per risolvere i problemi che determinano un utilizzo superiore al previsto.

## <a name="data-transfer-charges-using-log-analytics"></a>Addebiti per il trasferimento dei dati tramite Log Analytics

L'invio di dati a Log Analytics potrebbe influire sulla larghezza di banda dei dati. Come descritto nella pagina dei prezzi per la [larghezza di banda di Azure](https://azure.microsoft.com/pricing/details/bandwidth/), il trasferimento dei dati tra i servizi di Azure si trova in due aree addebitate come trasferimento di dati in uscita alla tariffa normale. Il trasferimento dei dati in ingresso è gratuito. Tuttavia, questo importo è molto ridotto (pochi%) rispetto ai costi per l'inserimento dei dati Log Analytics. Di conseguenza, il controllo dei costi per Log Analytics deve concentrarsi sul volume di dati inserito e sono disponibili indicazioni utili per comprendere [questo comportamento.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume)   

## <a name="limits-summary"></a>Riepilogo dei limiti

Esistono alcuni limiti di Log Analytics aggiuntivi, alcuni dei quali dipendono dal piano tariffario Log Analytics. Questi sono descritti [qui](https://docs.microsoft.com/azure/azure-subscription-service-limits#log-analytics-workspaces).


## <a name="next-steps"></a>Passaggi successivi

- Vedere [ricerche nei log nei](../log-query/log-query-overview.md) log di monitoraggio di Azure per informazioni su come usare il linguaggio di ricerca. È possibile usare le query di ricerca per eseguire ulteriori analisi sui dati di utilizzo.
- Per ricevere una notifica quando vengono soddisfatti determinati criteri di ricerca, seguire la procedura descritta in [Creare un nuovo avviso del log](alerts-metric.md).
- Usare il [targeting della soluzione](../insights/solution-targeting.md) per raccogliere dati unicamente dai gruppi di computer necessari
- Per configurare un criterio efficace per la raccolta degli eventi, vedere [Criteri per i filtri del Centro sicurezza di Azure](../../security-center/security-center-enable-data-collection.md).
- Modificare la [configurazione del contatore delle prestazioni](data-sources-performance-counters.md).
- Per modificare le impostazioni di raccolta degli eventi, vedere la [configurazione del registro eventi](data-sources-windows-events.md).
- Per modificare le impostazioni di raccolta di SysLog, vedere la [configurazione di SysLog](data-sources-syslog.md).