---
title: Gestire l'utilizzo e i costi per Azure Log Analytics | Microsoft Docs
description: Informazioni su come modificare il piano tariffario e gestire il volume e i criteri di conservazione dei dati per l'area di lavoro Log Analytics in Azure.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/29/2018
ms.author: magoedte
ms.subservice: ''
ms.openlocfilehash: a2f90c52823664df5fdc71c55220cc660c2f68e3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60782901"
---
# <a name="manage-usage-and-costs-for-log-analytics-in-azure-monitor"></a>Gestire l'utilizzo e costi per Log Analitica in Monitoraggio di Azure

> [!NOTE]
> Questo articolo descrive come controllare i costi in Log Analytics impostando il periodo di conservazione dei dati.  Per le informazioni correlate, vedere gli articoli seguenti.
> - [Analizzare l'utilizzo dei dati in Log Analytics](manage-cost-storage.md) descrive l'analisi e gli avvisi per l'utilizzo dei dati.
> - [Monitoraggio dell'utilizzo e dei costi stimati](usage-estimated-costs.md) descrive come visualizzare l'utilizzo e i costi stimati tra più funzionalità di monitoraggio di Azure per diversi modelli di determinazione dei prezzi. Illustra inoltre come modificare il modello di prezzi.

Log Analitica in Monitoraggio di Azure è progettato per scalabilità e supportare la raccolta, l'indicizzazione e l'archiviazione di enormi quantità di dati al giorno da qualsiasi origine in ambito aziendale o distribuita in Azure.  Anche se si tratta di uno strumento importante per l'organizzazione, è comunque fondamentale ottimizzare i costi. A tale scopo, è importante comprendere che il costo di un'area di lavoro Log Analytics non si basa solo sul volume dei dati raccolti, ma dipende anche dal piano selezionato e dal periodo di tempo di archiviazione dei dati generati dalle origini connesse.  

In questo articolo viene esaminato come monitorare in modo proattivo il volume dei dati e l'aumento dello spazio di archiviazione e definire i limiti per controllare i costi associati. 

Il costo dei dati può essere notevole, a seconda dei fattori seguenti: 

- Volume di dati generati e inseriti nell'area di lavoro 
    - Numero delle soluzioni di gestione abilitate
    - Numero dei sistemi monitorati
    - Tipo di dati raccolto da ciascuna risorsa monitorata 
- Il periodo di tempo in cui si decide di conservare i dati 

## <a name="understand-your-workspaces-usage-and-estimated-cost"></a>Comprendere utilizzo e costi stimati dell'area di lavoro
Log Analytics aiuta a capire quali sono i costi probabili in base ai modelli di utilizzo recenti.  A tale scopo, usare la pagina **Utilizzo e costi stimati di Log Analytics** per rivedere e analizzare l'utilizzo dei dati. Questa pagina mostra quanti dati vengono raccolti da ogni soluzione, quanti dati vengono conservati e una stima dei costi in base alla quantità di dati inseriti e a eventuali altri dati conservati oltre la quantità inclusa.

![Utilizzo e costi stimati](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Per esplorare i dati in maggiore dettaglio, fare clic sull'icona nell'angolo superiore destro di uno dei grafici nella pagina **Utilizzo e costi stimati**. Ora è possibile usare questa query per esplorare i dettagli dell'utilizzo.  

![Visualizzazione dei log](media/manage-cost-storage/logs.png)

Dalla pagina **Utilizzo e costi stimati** è possibile rivedere il volume dei dati per il mese. Sono inclusi tutti i dati ricevuti e conservati nell'area di lavoro Log Analytics.  Fare clic su **Dettagli di utilizzo** nella parte superiore della pagina per visualizzare il dashboard di utilizzo con informazioni sulle tendenze del volume di dati in base a origine, computer e offerta. Per visualizzare e impostare un limite giornaliero o per modificare il periodo di conservazione, fare clic su **Gestione del volume dati**.
 
I costi di Log Analytics vengono addebitati nella fattura di Azure. È possibile visualizzare i dettagli della fattura di Azure nella sezione Fatturazione del portale di Azure oppure nel [portale di fatturazione di Azure](https://account.windowsazure.com/Subscriptions).  

## <a name="daily-cap"></a>Limite giornaliero
È possibile configurare un limite giornaliero e limitare l'inserimento giornaliero per l'area di lavoro, tuttavia è necessario tenere presente che l'obiettivo non deve essere quello di raggiungere tale limite.  In caso contrario, si perdono i dati per il resto del giorno, situazione che può influire su altri servizi e soluzioni di Azure la cui funzionalità può dipendere da dati aggiornati disponibili nell'area di lavoro.  Di conseguenza, la possibilità di osservare e ricevere avvisi quando le condizioni di integrità delle risorse che supportano i servizi IT sono interessate.  Il limite giornaliero è finalizzato a gestire l'aumento imprevisto nel volume di dati dalle risorse gestite e rimanere entro il limite prefissato oppure quando si vuole semplicemente limitare gli addebiti non pianificati per l'area di lavoro.  

Quando viene raggiunto il limite giornaliero, la raccolta di tipi di dati fatturabili viene arrestata per il resto del giorno. Nella parte superiore della pagina per l'area di lavoro Log Analytics selezionata viene visualizzato un banner di avviso e viene inviato un evento di tipo operazione alla tabella *Operazione* nella categoria **LogManagement**. La raccolta dati riprende dopo l'ora di ripristino definita in *Daily limit will be set at* (Ora impostazione limite giornaliero). Si consiglia di definire una regola di avviso in base a questo evento operazione, per l'invio di una notifica quando viene raggiunta la soglia dei dati giornaliera. 

### <a name="identify-what-daily-data-limit-to-define"></a>Identificare la soglia dei dati giornaliera da definire 
Vedere [Utilizzo e costi stimati di Log Analytics](usage-estimated-costs.md) per informazioni sulla tendenza nell'inserimento dati e sul limite di volume giornaliero da definire. Fare attenzione nella scelta del limite, in quanto non sarà possibile monitorare le risorse una volta raggiunto il limite. 

### <a name="manage-the-maximum-daily-data-volume"></a>Gestire il volume di dati giornaliero massimo 
Di seguito viene descritto come configurare un limite per gestire il volume di dati che verranno inseriti da Log Analytics al giorno.  

1. Nell'area di lavoro selezionare **Utilizzo e costi stimati** nel riquadro a sinistra.
2. Nella pagina **Utilizzo e costi stimati** per l'area di lavoro selezionata fare clic su **Gestione del volume dati** nella parte superiore. 
3. Per impostazione predefinita, il limite giornaliero è impostato su **DISATTIVA**. Fare clic su **ATTIVA** per abilitarlo e quindi impostare il volume di dati in GB/giorno.<br><br> ![Configurazione della soglia dei dati in Log Analytics](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Avvisa al raggiungimento del limite giornaliero
Anche se nel portale di Azure viene visualizzato un segnale visivo quando viene raggiunta la soglia dei dati, questo comportamento potrebbe non soddisfare le esigenze aziendali per la gestione di problemi operativi che richiedono attenzione immediata.  Per ricevere una notifica di avviso, è possibile creare una nuova regola di avviso in Monitoraggio di Azure.  Per altre informazioni, vedere [Creare, visualizzare e gestire gli avvisi tramite Monitoraggio di Azure](alerts-metric.md).      

Per iniziare, ecco le impostazioni consigliate per l'avviso:

* Destinazione: Selezionare la risorsa di Log Analytics
* Criteri: 
   * Nome segnale: Ricerca log personalizzata
   * Query di ricerca: operazione, seguita da | con valore "OverQuota" per Detail
   * In base a: Numero di risultati
   * Condizione: Maggiore di
   * Soglia: 0
   * Periodo: 5 (minuti)
   * Frequenza: 5 (minuti)
* Nome regola di avviso: Soglia dei dati giornaliera raggiunta
* Gravità: Avviso (Gravità 1)

Una volta definito l'avviso e raggiunto il limite, viene attivato un avviso e viene eseguita la risposta definita nel gruppo di azioni. È possibile informare il team tramite posta elettronica e SMS oppure automatizzare le operazioni usando webhook o runbook di Automazione oppure tramite l'[integrazione con una soluzione di Gestione dei servizi IT esterna](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="change-the-data-retention-period"></a>Cambiare il periodo di conservazione dei dati 
La procedura seguente descrive come configurare il periodo di conservazione dei dati nell'area di lavoro.
 
1. Nell'area di lavoro selezionare **Utilizzo e costi stimati** nel riquadro a sinistra.
2. Nella pagina **Utilizzo e costi stimati** fare clic su **Gestione del volume dati** nella parte superiore.
5. Nel riquadro spostare il dispositivo di scorrimento per aumentare o diminuire il numero di giorni e quindi fare clic su **OK**.  Se si usa il livello *gratuito*, non è possibile modificare il periodo di conservazione dei dati ed è necessario eseguire l'aggiornamento al piano a pagamento per controllare questa impostazione.<br><br> ![Modificare l'impostazione di conservazione dei dati dell'area di lavoro](media/manage-cost-storage/manage-cost-change-retention-01.png)

## <a name="legacy-pricing-tiers"></a>Piani tariffari legacy

I clienti con un contratto Enterprise sottoscritto prima del 1 luglio 2018 o che hanno già creato un'area di lavoro Log Analytics in una sottoscrizione potranno comunque accedere al piano *Gratuito*. Se la sottoscrizione non è associata a una registrazione EA esistente, il livello *Gratuito* non è disponibile quando si crea un'area di lavoro in una nuova sottoscrizione dopo il 2 aprile 2018.  I dati possono essere conservati per un massimo di 7 giorni per il livello *Gratuito*.  Per i livelli legacy *Autonomo* o *Per nodo*, nonché per il piano tariffario unico 2018 corrente, i dati raccolti sono disponibili per gli ultimi 31 giorni. Il livello *Gratuito* prevede un limite di inserimento giornaliero di 500 MB. Se si supera costantemente il volume consentito, è possibile convertire l'area di lavoro in un altro piano per raccogliere dati oltre questo limite. 

> [!NOTE]
> Per usare i diritti che derivano dall'acquisto di OMS E1 Suite, OMS E2 Suite o un componente aggiuntivo di OMS per System Center, scegliere il piano tariffario *Per nodo* di Log Analytics.

## <a name="changing-pricing-tier"></a>Modifica del piano tariffario

Se l'area di lavoro Log Analytics ha accesso ai piani tariffari esistenti, modificare i piani tariffari esistenti:

1. Nel riquadro delle sottoscrizioni di Log Analytics del portale di Azure selezionare un'area di lavoro.

2. Nel riquadro dell'area di lavoro selezionare **Piano tariffario** in **Generale**.  

3. In **Piano tariffario** selezionare un piano tariffario e quindi fare clic su **Seleziona**.  
    ![Piano tariffario selezionato](media/manage-cost-storage/workspace-pricing-tier-info.png)

Se si desidera spostare l'area di lavoro nel piano tariffario corrente, è necessario [modificare il modello di prezzi di monitoraggio dell’abbonamento in Monitoraggio di Azure](usage-estimated-costs.md#moving-to-the-new-pricing-model) che modificherà il piano tariffario di tutte le aree di lavoro in tale abbonamento.


> [!NOTE]
> Per ulteriori informazioni sulle [impostando il piano tariffario tramite ARM](template-workspace-configuration.md#create-a-log-analytics-workspace) e assicurarsi che la distribuzione ARM riuscirà indipendentemente dal fatto che la sottoscrizione è nel preesistente o nuovo modello di determinazione prezzi. 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Risoluzione dei problemi se Log Analytics non sta più raccogliendo dati
Se si sta usando il piano tariffario legacy Gratuito e sono stati inviati più di 500 MB di dati in un giorno, la raccolta dati si interrompe per il resto del giorno. Il raggiungimento del limite giornaliero è un motivo comune per cui Log Analytics interrompe la raccolta dei dati o per cui i dati mancano.  Log Analytics crea un evento di tipo operazione quando la raccolta dati si avvia e si arresta. Eseguire la query seguente per verificare se si raggiunge il limite giornaliero e se i dati sono mancanti: 

`Operation | where OperationCategory == 'Data Collection Status'`

Quando la raccolta dati si interrompe, OperationStatus è Avviso. Quando la raccolta dati si avvia, OperationStatus è Riuscito. La tabella seguente descrive i motivi per cui raccolta dei dati si interrompe e un'azione consigliata per riprendere la raccolta dati:  

|Motivo dell'arresto della raccolta| Soluzione| 
|-----------------------|---------|
|Limite giornaliero del piano tariffario legacy Gratuito raggiunto |Attendere fino al giorno successivo per il riavvio automatico della raccolta oppure passare a un piano tariffario a pagamento.|
|È stato raggiunto il limite giornaliero dell'area di lavoro|Attendere il riavvio automatico della raccolta oppure aumentare il limite giornaliero per il volume di dati, come descritto nella sezione sulla gestione del volume di dati giornaliero massimo. Il tempo di ripristino del limite giornaliero viene visualizzato nella pagina **Gestione del volume dati**. |
|La sottoscrizione di Azure è in sospeso perché:<br> la versione di prova gratuita è terminata<br> Azure Pass è scaduto<br> Il limite di spesa mensile è stato raggiunto, ad esempio in una sottoscrizione MSDN o in una sottoscrizione di Visual Studio|Passare a una sottoscrizione a pagamento<br> Rimuovere il limite oppure attendere fino ripristino del limite|

Per ricevere una notifica all'interruzione della raccolta dati, usare la procedura per la *creazione di un avviso relativo al limite di dati giornaliero* per ricevere una notifica quando la raccolta dati si arresta e seguire i passaggi per aggiungere azioni alle regole di avviso per configurare un messaggio di posta elettronica, un webhook o un runbook per la regola di avviso. 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Risoluzione dei problemi che determinano un utilizzo superiore al previsto
Un utilizzo più elevato è dovuto a una o entrambe le cause seguenti:
- Più nodi del previsto inviano dati a Log Analytics
- Vengono inviati più dati del previsto a Log Analytics

Il successivo soluzio sezioni

## <a name="understanding-nodes-sending-data"></a>Informazioni sui nodi che inviano dati

Per conoscere il numero di computer (nodi) che hanno segnalato dati ogni giorno nell'ultimo mese, usare

`Heartbeat | where TimeGenerated > startofday(ago(31d))
| summarize dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart`

Per ottenere un elenco di computer che inviano **tipi di dati fatturati** (alcuni tipi di dati sono gratuiti), sfruttare la proprietà [_IsBillable](log-standard-properties.md#_isbillable):

`union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName`

Usare queste query `union withsource = tt *` solo se necessario, poiché le analisi tra tipi di dati sono costose. Questa query sostituisce quello precedente di una query su informazioni per ogni computer con il tipo di dati di utilizzo.  

Ciò può essere esteso per restituire il conteggio dei computer che inviano all'ora fatturati i tipi di dati (ovvero come Log Analitica calcola i nodi fatturabili per il nodo legacy per ogni piano tariffario):

`union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc`

## <a name="understanding-ingested-data-volume"></a>Volume di dati inserito conoscenza 

Nella pagina **Utilizzo e costi stimati** il grafico *Inserimento dati per soluzione* mostra il volume totale dei dati inviati e la quantità inviata da ogni soluzione. In questo modo è possibile determinare tendenze specifiche, ad esempio se l'utilizzo dei dati complessivo (o da parte di una particolare soluzione) sta aumentando, è stabile o sta diminuendo. La query usata per generare questi dati è

`Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

Si noti che la clausola "where IsBillable = true" esclude i tipi di dati da determinate soluzioni per le quali non è addebitato alcun inserimento. 

È possibile approfondire ulteriormente l'analisi per visualizzare le tendenze relative a tipi di dati specifici, ad esempio per studiare i dati risultanti dai log di IIS:

`Usage | where TimeGenerated > startofday(ago(31d))| where IsBillable == true
| where DataType == "W3CIISLog"
| summarize TotalVolumeGB = sum(Quantity) / 1024 by bin(TimeGenerated, 1d), Solution| render barchart`

### <a name="data-volume-by-computer"></a>Volume dati per computer

Per visualizzare il **dimensioni** degli eventi fatturabili inseriti al computer, utilizzare il `_BilledSize` proprietà ([log-standard-properties & _billedsize.md](learn more)) che fornisce la dimensione in byte:

```
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by  Computer | sort by Bytes nulls last
```

Il `_IsBillable` proprietà specifica se i dati inseriti si incorrerà in spese ([_isbillable & log-standard-properties.md](Learn more).)

Per visualizzare il **numero** di eventi inseriti per computer, usare

`union withsource = tt *
| summarize count() by Computer | sort by count_ nulls last`

Per visualizzare il numero di eventi fatturabili inseriti per computer, usare 

`union withsource = tt * 
| where _IsBillable == true 
| summarize count() by Computer  | sort by count_ nulls last`

Per sapere il numero di tipi dati fatturabili che inviano dati a uno specifico computer, usare:

```
union withsource = tt *
| where Computer == "computer name"
| where _IsBillable == true 
| summarize count() by tt | sort by count_ nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Volume di dati da risorse di Azure, gruppo di risorse o sottoscrizione

Per i dati dai nodi ospitati in Azure è possibile ottenere il **dimensioni** degli eventi fatturabili inseriti __per ogni computer__, usare il `_ResourceId` proprietà che fornisce il percorso completo della risorsa ([ log-standard-properties.md & _resourceid](learn more)):

```
union withsource = tt * 
| where _IsBillable == true 
| summarize Bytes=sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

Per i dati dai nodi ospitati in Azure è possibile ottenere il **dimensioni** degli eventi fatturabili inseriti __per ogni sottoscrizione di Azure__, analizzare il `_ResourceId` proprietà come:

```
union withsource = tt * 
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize Bytes=sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Modificando `subscriptionId` a `resourceGroup` indicherà il volume di dati inseriti fatturabili dal gruppo resouurce Azure. 


> [!NOTE]
> Benché siano ancora inclusi nello schema, alcuni campi del tipo di dati Utilizzo sono stati deprecati e i rispettivi valori non verranno più popolati. Si tratta del campo **Computer** e dei campi correlati all'inserimento, ossia **TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** e **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>L'esecuzione di query per tipi di dati comuni

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

### <a name="getting-security-and-automation-node-counts"></a>Ottenere i conteggi di nodo sicurezza e automazione 

Se si usa un piano tariffario "Per nodo (OMS)", l'addebito viene effettuato in base al numero di nodi e soluzioni usati e il numero di nodi di Informazioni dettagliate e analisi fatturati sarà visualizzato in una tabella nella pagina **Utilizzo e costi stimati**.  

Per visualizzare il numero di nodi di sicurezza distinti è possibile usare la query:

`union
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
| count`

Per visualizzare il numero di nodi di Automazione distinti usare la query:

```
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

## <a name="create-an-alert-when-data-collection-is-higher-than-expected"></a>Creare un avviso quando la raccolta dati supera le dimensioni previste

Questa sezione descrive come creare un avviso nei casi seguenti:
- Il volume di dati supera una quantità specificata.
- Si prevede che il volume di dati superi una quantità specificata.

Avvisi di Azure supporta [avvisi relativi ai log](alerts-unified-log.md) che usano query di ricerca. 

La query seguente restituisce un risultato quando vengono raccolti più di 100 GB di dati nelle ultime 24 ore:

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`

La query seguente usa una semplice formula per prevedere quando verranno inviati più di 100 GB di dati in un giorno: 

`union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`

Per generare un avviso su un volume di dati diverso, sostituire il numero 100 nelle query con il numero di GB da segnalare.

Per ricevere una notifica quando la raccolta dati supera le dimensioni previste, seguire la procedura descritta in [Creare un nuovo avviso del log](alerts-metric.md).

Quando si crea l'avviso per la prima query e la quantità di dati supera i 100 GB in 24 ore, impostare:  

- Per **Definire la condizione dell'avviso**, specificare l'area di lavoro di Log Analytics come destinazione della risorsa.
- Per **Criteri di avviso** specificare quanto segue:
   - Per **Nome segnale** selezionare **Ricerca log personalizzata**
   - **Query di ricerca** su `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1024)) by Type | where DataGB > 100`
   - **Logica avvisi** è **In base a** *numero di risultati* e **Condizione** è *Maggiore di* una **Soglia** pari a *0*
   - **Periodo di tempo** di *1440* minuti e **Frequenza di avviso** ogni *60* minuti, poiché i dati sull'utilizzo vengono aggiornati solo una volta all'ora.
- Per **Definire i dettagli dell'avviso** specificare quanto segue:
   - **Nome** su *Data volume greater than 100 GB in 24 hours* (Volume di dati maggiore di 100 GB in 24 ore)
   - **Gravità** su *Avviso*

Specificare un [gruppo di azioni](action-groups.md) esistente o crearne uno nuovo, in modo da ricevere una notifica se l'avviso del log corrisponde ai criteri.

Quando si crea l'avviso per la seconda query e si prevedono più di 100 GB di dati in 24 ore, impostare:

- Per **Definire la condizione dell'avviso**, specificare l'area di lavoro di Log Analytics come destinazione della risorsa.
- Per **Criteri di avviso** specificare quanto segue:
   - Per **Nome segnale** selezionare **Ricerca log personalizzata**
   - **Query di ricerca** su `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1024)) by Type | where EstimatedGB > 100`
   - **Logica avvisi** è **In base a** *numero di risultati* e **Condizione** è *Maggiore di* una **Soglia** pari a *0*
   - **Periodo di tempo** di *180* minuti e **Frequenza di avviso** ogni *60* minuti, poiché i dati sull'utilizzo vengono aggiornati solo una volta all'ora.
- Per **Definire i dettagli dell'avviso** specificare quanto segue:
   - **Nome** su *Data volume expected to be greater than 100 GB in 24 hours* (Volume di dati previsto maggiore di 100 GB in 24 ore)
   - **Gravità** su *Avviso*

Specificare un [gruppo di azioni](action-groups.md) esistente o crearne uno nuovo, in modo da ricevere una notifica se l'avviso del log corrisponde ai criteri.

Quando si riceve un avviso, seguire la procedura descritta nella sezione seguente per risolvere i problemi che determinano un utilizzo superiore al previsto.

## <a name="next-steps"></a>Passaggi successivi
* Per informazioni su come usare il linguaggio di ricerca, vedere [Ricerche nei log in Log Analytics](../log-query/log-query-overview.md). È possibile usare le query di ricerca per eseguire ulteriori analisi sui dati di utilizzo.
* Per ricevere una notifica quando vengono soddisfatti determinati criteri di ricerca, seguire la procedura descritta in [Creare un nuovo avviso del log](alerts-metric.md).
* Usare il [targeting della soluzione](../insights/solution-targeting.md) per raccogliere dati unicamente dai gruppi di computer necessari
* Per configurare un criterio efficace per la raccolta degli eventi, vedere [Criteri per i filtri del Centro sicurezza di Azure](../../security-center/security-center-enable-data-collection.md).
* Modificare la [configurazione del contatore delle prestazioni](data-sources-performance-counters.md).
* Per modificare le impostazioni di raccolta degli eventi, vedere la [configurazione del registro eventi](data-sources-windows-events.md).
* Per modificare le impostazioni di raccolta di SysLog, vedere la [configurazione di SysLog](data-sources-syslog.md).


