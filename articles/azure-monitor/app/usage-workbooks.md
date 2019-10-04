---
title: Creare report interattivi con cartelle di lavoro di Monitoraggio di Azure | Microsoft Docs
description: Semplificare la creazione di report complessi con cartelle di lavoro con parametri predefinite e personalizzate
services: application-insights
documentationcenter: ''
author: NumberByColors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/19/2018
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: 6d84ad69b067f730bbfbcad9e46bdc9ae2036ead
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64569610"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Creare report interattivi con cartelle di lavoro di Monitoraggio di Azure

Le cartelle di lavoro combinano testo,  [query di Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics), metriche di Azure e parametri all'interno di report interattivi avanzati. Le cartelle di lavoro possono essere modificate da tutti gli altri membri del team che possono accedere alla stessa risorsa di Azure.

Le cartelle di lavoro sono utili per scenari simili ai seguenti:

* Esplorazione dell'utilizzo dell'app quando non si conoscono le metriche di interesse in anticipo, ovvero il numero di utenti, i tassi di fidelizzazione, i tassi di conversione e così via. A differenza di altri strumenti di analisi dell'utilizzo, le cartelle di lavoro consentono di combinare più tipi di visualizzazioni e analisi, risultando ideali per questo tipo di esplorazione in formato libero.
* Descrizione al proprio team delle prestazioni di una nuova funzionalità rilasciata visualizzando i conteggi degli utenti per le interazioni chiave e altre metriche.
* Condivisione dei risultati di un esperimento A/B nell'app con altri membri del team. È possibile descrivere gli obiettivi dell'esperimento usando testo e visualizzare ogni metrica di utilizzo e query di Analisi usate per valutare l'esperimento con callout chiari che indicano se la singola metrica è al di sopra o al di sotto del target.
* Segnalazione dell'impatto di un'interruzione nell'utilizzo dell'app, combinazione dei dati, spiegazione del testo e descrizione dei passaggi successivi per impedire interruzioni future.

## <a name="starting-with-a-template-or-saved-workbook"></a>Iniziare da un modello o da una cartella di lavoro salvata

Una cartella di lavoro è composta di sezioni costituite da grafici, tabelle, testo e controlli di input modificabili in modo indipendente gli uni dagli altri. Per comprendere meglio le cartelle di lavoro, la cosa migliore è aprire una. 

All'interno dell'esperienza Application Insights dell'app selezionare **Cartelle di lavoro** dal menu a sinistra.

![Screenshot di spostamento nelle cartelle di lavoro](./media/usage-workbooks/001-workbooks.png)

Verrà avviata una raccolta di cartelle di lavoro con un certo numero di cartelle di lavoro predefinite che consentono di iniziare più facilmente.

![Screenshot della raccolta di cartelle di lavoro](./media/usage-workbooks/002-workbook-gallery.png)

Si inizierà con il **Modello predefinito**, che si trova sotto l'intestazione **Avvio rapido**.

![Screenshot della raccolta di cartelle di lavoro](./media/usage-workbooks/003-default-template.png)

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Modifica, ridisposizione, clonazione ed eliminazione di sezioni delle cartelle di lavoro

Le cartelle di lavoro presentano due modalità: **modalità di modifica** e **modalità di lettura**. Quando la cartella di lavoro predefinita viene avviata per la prima volta, viene aperta in **modalità di modifica**, che visualizza tutto il contenuto, inclusi eventuali passaggi e parametri che in altri casi sono nascosti. La **modalità di lettura** presenta una visualizzazione semplificata sotto forma di report. In questo modo vengono rimossi gli aspetti più complessi della creazione di report, ma sono sufficienti pochi clic per richiamare i meccanismi sottostanti, se è necessario eseguire modifiche.

![Controlli di modifica delle sezioni delle cartelle di controllo di Application Insights](./media/usage-workbooks/editing-controls-new.png)

1. Dopo aver modificato una sezione, fare clic su **Modifica completata** nell'angolo inferiore sinistro della sezione.

2. Per creare un duplicato di una sezione, fare clic sull'icona **Clona questa sezione**. La creazione di sezioni duplicate è un ottimo modo per eseguire l'iterazione in una query senza perdere le iterazioni precedenti.

3. Per spostare una sezione verso l'alto in una cartella di lavoro, fare clic sull'icona **Sposta in alto** o **Sposta giù**.

4. Per rimuovere una sezione in modo permanente, fare clic sull'icona **Rimuovi**.

## <a name="adding-text-and-markdown-sections"></a>Aggiunta di sezioni di testo e Markdown

L'aggiunta di intestazioni, spiegazioni e commenti nelle cartelle di lavoro consente di trasformare un insieme di tabelle e grafici in un resoconto. Le sezioni di testo nelle cartelle di lavoro supportano la [sintassi Markdown](https://daringfireball.net/projects/markdown/) per la formattazione del testo, ad esempio intestazioni, grassetto, corsivo ed elenchi puntati.

Per aggiungere una sezione di testo alla cartella di lavoro, usare il pulsante **Aggiungi testo** nella parte inferiore della cartella di lavoro o nella parte inferiore di una sezione.

## <a name="adding-query-sections"></a>Aggiunta di sezioni di query

![Sezione di query nelle cartelle di lavoro](./media/usage-workbooks/analytics-section-new.png)

Per aggiungere una sezione di query alla cartella di lavoro, usare il pulsante **Aggiungi query** nella parte inferiore della cartella di lavoro o di una sezione.

Le sezioni di query, estremamente flessibili, possono essere usate per rispondere a domande quali:

* Quante eccezioni ha generato il sito nello stesso periodo di tempo come riduzione dell'utilizzo?
* Qual è stata la distribuzione dei tempi di caricamento della pagina per gli utenti che visualizzano una pagina?
* Quanti utenti hanno visualizzato un insieme di pagine nel sito ma non un altro insieme di pagine? Queste informazioni possono essere utili per comprendere se sono presenti cluster di utenti che usano subset di funzionalità del sito diversi (usare l'operatore `join` con il modificatore `kind=leftanti` nel [linguaggio di query Kusto](/azure/kusto/query/)).

Le query, poi, non sono limitate al contesto dell'applicazione da cui è stata avviata la cartella di lavoro. È possibile eseguire query in più app monitorate da Application Insights, nonché in aree di lavoro di Log Analytics, a condizione di avere le autorizzazioni di accesso necessarie per tali risorse.

Per eseguire query da altre risorse di Application Insights esterne, usare l'identificatore **app**.

```
union app('app01').requests, app('app02').requests, requests
| summarize count() by bin(timestamp, 1h)
```  

Questa query è la combinazione di richieste da tre applicazioni diverse, due app, denominate rispettivamente app01 e app02, e la risorsa di Application Insights locale.

Per eseguire il pull dei dati da un'area di lavoro Log Analytics esterna, usare l'identificatore **workspace**.

Per altre informazioni sulle query tra risorse, vedere il [materiale sussidiario ufficiale](https://docs.microsoft.com/azure/log-analytics/log-analytics-cross-workspace-search).

### <a name="advanced-analytic-query-settings"></a>Impostazioni avanzate per le query di analisi

Ogni sezione ha impostazioni avanzate specifiche, accessibili tramite l'icona delle impostazioni ![Controlli di modifica delle sezioni delle cartelle di lavoro di Application Insights](./media/usage-workbooks/005-settings.png) che si trova a destra del pulsante **Aggiungi parametri**.

![Controlli di modifica delle sezioni delle cartelle di controllo di Application Insights](./media/usage-workbooks/0006-settings-expanded.png)

   |         |          |
   | ---------------- |:-----|
   | **Imposta larghezza personalizzata per l'elemento**    | Impostare questa opzione per impostare una dimensione arbitraria per più elementi da inserire in un'unica riga, in modo da organizzare in modo più efficace i grafici e le tabelle all'interno di report interattivi avanzati.  |
   | **Imposta la visibilità condizionata per questo elemento** | Usare questa opzione per nascondere i passaggi basati su un parametro in modalità di lettura. |
   | **Quando è selezionato un elemento del grafico, esporta un parametro**| Consente a una riga selezionata nella griglia o nel grafico di fare in modo che nei passaggi successivi alcuni valori vengano modificati o diventino visibili.  |
   | **Mostra query quando non viene modificata** | Consente di visualizzare la query sopra il grafico o sopra la tabella anche in modalità di lettura.
   | **Mostra il pulsante Apri in Analisi quando non è attiva la modalità di modifica** | Aggiunge l'icona di Analytics di colore azzurro nell'angolo a destra del grafico per consentire l'accesso con un clic.|

La maggior parte di queste impostazioni è piuttosto intuitiva, ma per comprendere **Quando è selezionato un elemento del grafico, esporta un parametro** è consigliabile esaminare una cartella di lavoro che usa questa funzionalità.

Una delle cartelle di lavoro predefinite visualizza informazioni sugli utenti attivi.

La prima sezione della cartella di lavoro si basa sui dati di query di Analytics:

![Controlli di modifica delle sezioni delle cartelle di controllo di Application Insights](./media/usage-workbooks/003-active-users.png)

Anche la seconda sezione si basa su dati di query di Analytics. Se tuttavia si seleziona una riga della prima tabella viene aggiornato in modo interattivo il contenuto del grafico:

![Controlli di modifica delle sezioni delle cartelle di controllo di Application Insights](./media/usage-workbooks/004-active-users-trend.png)

 Ciò è possibile tramite l'uso delle impostazioni avanzate **Quando è selezionato un elemento del grafico, esporta un parametro**, abilitate nella query di Analytics della tabella.

![Controlli di modifica delle sezioni delle cartelle di controllo di Application Insights](./media/usage-workbooks/007-settings-export.png)

La seconda query di analisi usa quindi i valori esportati quando viene selezionata una riga. Se non è selezionata alcuna riga, per impostazione predefinita viene usata la riga che rappresenta i valori complessivi. 

```
let start = startofday(ago({TimeRange} + {Metric}));
union customEvents, pageViews
| where timestamp >= start
| where name in ({Activities}) or '*' in ({Activities}) or ('%' in ({Activities}) and itemType == 'pageView') or ('#' in ({Activities}) and itemType == 'customEvent')
{OtherFilters}
| where '{Filter}' == '' or '{Filter}' == '🔸 Overall' or {AnalyzeBy} == replace('🔹 ', '', '{Filter}')
| evaluate activity_engagement(user_Id, timestamp, start, now(), 1d, {Metric})
| where timestamp >= startofday(ago({TimeRange}))
| project timestamp, ["Active User"] = dcount_activities_outer
| render timechart 
```

## <a name="adding-metrics-sections"></a>Aggiunta di sezioni di metriche

Le sezioni di metriche consentono l'accesso completo ai dati delle metriche di Monitoraggio di Azure perché sia possibile incorporarli nei report interattivi. Molte delle cartelle di lavoro predefinite contengono sia dati di query di analisi che dati delle metriche, consentendo di ottenere tutti i vantaggi di entrambe le funzionalità in un'unica posizione. È anche possibile eseguire il pull di dati delle metriche dalle risorse di una qualsiasi delle sottoscrizioni a cui è possibile accedere.

Ecco un esempio di dati di macchine virtuali di cui viene eseguito il pull in una cartella di lavoro per consentire la visualizzazione sotto forma di griglia delle prestazioni della CPU:

![Controlli di modifica delle sezioni delle cartelle di controllo di Application Insights](./media/usage-workbooks/008-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Aggiunta di sezioni di parametri

I parametri di una cartella di lavoro consentono di modificare i valori nella cartella senza dover modificare manualmente le sezioni di query o di testo.  In questo modo non è necessario comprendere il linguaggio delle query di analisi sottostante e il numero dei destinatari possibili dei report basati sulle cartelle di lavoro viene notevolmente ampliato.

Nelle sezioni di query, di testo o di altri parametri, al posto del valore di un parametro si trova il nome del parametro stesso tra parentesi graffe, ad esempio ``{parameterName}``.  I nomi dei parametri sono soggetti a regole simili a quelle degli identificatori JavaScript. In pratica, possono essere composti da caratteri alfabetici o di sottolineatura seguiti da caratteri alfanumerici o di sottolineatura. Ad esempio, il nome **a1** è consentito, ma **1a** non lo è.

I parametri sono lineari. Iniziano dalla parte superiore della cartella di lavoro e continuano verso il basso attraverso i passaggi successivi.  I parametri dichiarati più avanti in una cartella di lavoro possono sostituire quelli dichiarati in precedenza.  In questo modo i parametri che usano query possono accedere anche ai valori di parametri definiti in precedenza.  Anche all'interno del passaggio di un parametro, i parametri sono lineari, da sinistra a destra. I parametri a destra possono dipendere da un parametro dichiarato in precedenza nello stesso passaggio.
 
Attualmente sono supportati quattro tipi diversi di parametri:

  |         |          |
   | ---------------- |:-----|
   | **Text**    | L'utente potrà modificare una casella di testo. Facoltativamente, è possibile specificare una query per inserire il valore predefinito. |
   | **Elenco a discesa** | L'utente potrà scegliere da un set di valori. |
   | **Selezione dell'intervallo di tempo**| L'utente potrà scegliere da un set predefinito di valori di intervallo di tempo o selezionare un intervallo di tempo personalizzato.|
   | **Selezione risorse** | L'utente potrà scegliere tra le risorse selezionate per la cartella di lavoro.|

### <a name="using-a-text-parameter"></a>Uso di un parametro di testo

Il valore digitato da un utente nella casella di testo viene sostituito direttamente nella query, senza escape o virgolette. Se il valore necessario è una stringa, all'interno della query il parametro deve essere racchiuso tra virgolette (ad esempio **'{parametro}'** ).

In questo modo il valore in una casella di testo può essere usato ovunque, in un nome di tabella, di colonna o di funzione, in un operatore e così via.

Il tipo di parametro testo ha l'impostazione **Ottieni il valore predefinito dalla query Analytics**, che consente all'autore della cartella di lavoro di usare una query per inserire il valore predefinito della casella di testo.

Quando il valore predefinito deriva da una query di analisi, corrisponde solo al primo valore della prima riga (riga 0, colonna 0). È pertanto consigliabile fare in modo che la query restituisca una sola riga e una sola colonna. Eventuali altri dati restituiti dalla query vengono ignorati. 

Qualsiasi valore venga restituito dalla query viene inserito direttamente, senza escape o virgolette. Se la query non restituisce alcuna riga, il risultato del parametro corrisponde a una stringa vuota (se il parametro non è obbligatorio) oppure non è definito (se il parametro è obbligatorio).

### <a name="using-a-dropdown"></a>Uso di un elenco a discesa

Il tipo di parametro elenco a discesa consente di creare un controllo elenco a discesa, che consente la selezione di uno o più valori.

L'elenco a discesa viene popolato da una query di analisi. Se la query restituisce una sola colonna, i valori in tale colonna costituiscono sia il **valore** che l'**etichetta** del controllo elenco a discesa. Se la query restituisce due colonne, la prima colonna costituisce il **valore** e la seconda costituisce l'**etichetta** visualizzata nell'elenco a discesa.  Se la query restituisce tre colonne, la terza colonna viene usata per indicare la selezione predefinita dell'elenco a discesa.  Questa colonna può essere di qualsiasi tipo, ma i più semplici da usare sono i tipi numerico e booleano, con 0 corrispondente a falso e 1 a vero.

 Se la colonna è di tipo stringa, una stringa Null o vuota è considerata falsa e qualsiasi altro valore è considerato vero. Per elenchi a discesa a selezione singola, come selezione predefinita viene usato il primo valore corrispondente a vero.  Per elenchi a discesa a selezione multipla, per il set di selezioni predefinito vengono usati tutti i valori corrispondenti a vero. Gli elementi nell'elenco a discesa vengono visualizzati nell'ordine delle righe restituite dalla query. 

Si esamineranno ora i parametri presenti nel report Active Users. Fare clic sul simbolo di modifica accanto a **TimeRange**.

![Controlli di modifica delle sezioni delle cartelle di controllo di Application Insights](./media/usage-workbooks/009-time-range.png)

Verrà avviata la voce di menu Modifica parametro:

![Controlli di modifica delle sezioni delle cartelle di controllo di Application Insights](./media/usage-workbooks/010-time-range-edit.png)

La query usa la funzionalità del linguaggio di query di analisi **datatable**, che consente di generare dal nulla una tabella arbitraria, completa di contenuto. Ad esempio, la query di analisi seguente:

```
datatable( column1:string, column2:string )
[
 "row 1 column 1", "row 1 column 2",
"row 2 column 1", "row 2 column 2"
]
```

Genera il risultato:

![Controlli di modifica delle sezioni delle cartelle di controllo di Application Insights](./media/usage-workbooks/011-data-table.png)

Un esempio più appropriato consiste nell'usare un elenco a discesa per selezionare da un set di paesi/aree geografiche in base al nome:

```
customEvents
| where timestamp >= ago(14d)
| summarize count() by client_CountryOrRegion
| top 100 by count_
| project  client_CountryOrRegion 
| order by client_CountryOrRegion asc
```

La query visualizzerà i risultati indicati di seguito:

![Elenco a discesa dei paesi](./media/usage-workbooks/012-country-dropdown.png)

Gli elenchi a discesa sono strumenti estremamente potenti per la personalizzazione e la creazione di report interattivi.

### <a name="time-range-parameters"></a>Parametri dell'intervallo di tempo

Nonostante sia possibile creare un parametro intervallo di tempo personalizzato tramite il tipo di parametro elenco a discesa, se non è necessario un tale livello di flessibilità è anche possibile usare il tipo di parametro intervallo di tempo predefinito. 

I tipi di parametro intervallo di tempo predefiniti offrono 15 intervalli predefiniti, da 5 minuti agli ultimi 90 giorni. È anche disponibile un'opzione che consente la selezione di un intervallo di tempo personalizzato, con cui l'operatore del report può scegliere esplicitamente i valori di inizio e di fine dell'intervallo di tempo.

### <a name="resource-picker"></a>Selezione risorse

Il tipo di parametro selezione risorse offre la possibilità di definire l'ambito del report su tipi di risorse specifici. Un esempio di cartella di lavoro predefinita che usa il tipo selezione risorse è la cartella di lavoro **Failure Insights**.

![Elenco a discesa dei paesi](./media/usage-workbooks/013-resource-picker.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Salvataggio e condivisione delle cartelle di lavoro con il team

Le cartelle di lavoro vengono salvate all'interno di una risorsa di Application Insights, ovvero nella sezione **Report personali** privata o nella sezione **Report condivisi** accessibile a tutti gli utenti che hanno accesso alla risorsa di Application Insights. Per visualizzare tutte le cartelle di lavoro della risorsa, fare clic sul pulsante **Apri** nella barra delle azioni.

Per condividere una cartella di lavoro che è attualmente in **Report personali**:

1. Fare clic su **Apri** nella barra delle azioni
2. Fare clic sul pulsante "…" accanto alla cartella di lavoro da condividere
3. Fare clic su **Sposta in Report condivisi**.

Per condividere una cartella di lavoro con un collegamento o tramite posta elettronica, fare clic su **Condividi** nella barra delle azioni. Tenere presente che i destinatari del collegamento necessitano dell'accesso alla risorsa nel portale di Azure per visualizzare la cartella di lavoro. Per apportare modifiche, i destinatari devono avere almeno le autorizzazioni di collaboratori per la risorsa.

Per aggiungere un collegamento in una cartella di lavoro in un dashboard di Azure:

1. Fare clic su **Apri** nella barra delle azioni
2. Fare clic sul pulsante "…" accanto alla cartella di lavoro da aggiungere
3. Fare clic su **Aggiungi al dashboard**.

## <a name="contributing-workbook-templates"></a>Condivisione di modelli di cartella di lavoro

Se si è creato un modello di cartella di lavoro utile ed efficiente e lo si vuole condividere con la community, visitare il [repository GitHub](https://github.com/Microsoft/Application-Insights-Workbooks/blob/master/README.md) per informazioni.

## <a name="next-steps"></a>Passaggi successivi
- Per abilitare le esperienze di utilizzo, iniziare a inviare [eventi personalizzati](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) o [visualizzazioni pagina](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Se si inviano già eventi personalizzati o visualizzazioni pagina, è possibile esplorare gli strumenti relativi all'uso per scoprire come gli utenti usano il servizio.
    - [Utenti, sessioni ed eventi](../../azure-monitor/app/usage-segmentation.md)
    - [Grafici a imbuto](../../azure-monitor/app/usage-funnels.md)
    - [Conservazione](../../azure-monitor/app/usage-retention.md)
    - [Flussi degli utenti](../../azure-monitor/app/usage-flows.md)
    - [Aggiungere il contesto utente](../../azure-monitor/app/usage-send-user-context.md)