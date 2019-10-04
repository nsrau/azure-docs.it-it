---
title: Crea report interattivi con cartelle di lavoro di monitoraggio di Azure | Microsoft Docs
description: Semplificare report complessi con cartelle di lavoro a con parametri predefiniti e personalizzati per monitoraggio di Azure per le macchine virtuali.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2019
ms.author: magoedte
ms.openlocfilehash: 90c236347380bb5d5e51db56d0f431d2659a7258
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61387159"
---
# <a name="create-interactive-reports-with-azure-monitor-workbooks"></a>Creare report interattivi con cartelle di lavoro di Monitoraggio di Azure

Le cartelle di lavoro combinano testo [registrare query](../log-query/query-language.md), metriche e i parametri in report interattivi avanzati. Le cartelle di lavoro possono essere modificate da tutti gli altri membri del team che possono accedere alla stessa risorsa di Azure.

Le cartelle di lavoro sono utili per scenari, ad esempio:

* Esplorazione dell'utilizzo della macchina virtuale quando non si conoscono in anticipo le metriche di interesse: Utilizzo della CPU, spazio su disco, memoria, le dipendenze dalla rete e così via. A differenza di altri strumenti di analisi dell'utilizzo, le cartelle di lavoro consentono di combinare più tipi di visualizzazioni e analisi, risultando ideali per questo tipo di esplorazione in formato libero.
* Descrizione al proprio team delle prestazioni di una VM con provisioning di recente, mostrando le metriche dei principali contatori e altri eventi del log.
* Condivisione dei risultati di un esperimento di ridimensionamento della macchina virtuale con altri membri del team. È possibile descrivere gli obiettivi dell'esperimento usando testo e quindi mostrano come usare ogni query analitica e metriche usate per valutare l'esperimento con callout chiari per se ogni metrica è di sopra o sotto-destinazione.
* Segnalazione dell'utilizzo della macchina virtuale, la combinazione dei dati, spiegazione del testo e una descrizione dei passaggi successivi per impedire interruzioni in futuro l'impatto di un'interruzione del servizio.

Monitoraggio di Azure per le macchine virtuali include diverse cartelle di lavoro per iniziare a usare e la tabella seguente contiene un riepilogo.

| Cartella di lavoro | Descrizione | `Scope` |
|----------|-------------|-------|
| Prestazioni | Fornisce una versione personalizzabile dei nostri Top N elenco e visualizzazione di grafici in una singola cartella di lavoro che si basa su tutti i contatori delle prestazioni di Analitica di Log abilitati.| Su larga scala |
| Contatori delle prestazioni | Visualizzazione grafico Top N tra un ampio set di contatori delle prestazioni. | Su larga scala |
| connessioni | Le connessioni forniscono un'analisi approfondita delle connessioni in ingresso e in uscita dalle macchine virtuali monitorate. | Su larga scala |
| Porte attive | Fornisce un elenco dei processi che sono associate alle porte su macchine virtuali monitorate e delle attività nell'intervallo di tempo scelto. | Su larga scala |
| Open Ports (Porte aperte) | Fornisce il numero di porte apre nelle macchine virtuali monitorate e i dettagli su tali porte. | Su larga scala |
| Connessioni non riuscite | Visualizzare il numero di connessioni non riuscite nelle macchine virtuali monitorate, la tendenza di errore, e se la percentuale di errori aumenta nel tempo. | Su larga scala |
| Security and Audit | Un'analisi del traffico TCP/IP che segnala le connessioni generale, le connessioni dannose, in cui l'endpoint IP si trovano a livello globale.  Per abilitare tutte le funzionalità, è necessario abilitare il rilevamento di sicurezza. | Su larga scala |
| Traffico TCP | Un report con la pertinenza per le macchine virtuali monitorate e la relativa rete totale, inviato e ricevuto il traffico in una griglia, visualizzata come una linea di tendenza. | Su larga scala |
| Confronto traffico | Questo cartelle di lavoro consente di confrontare le tendenze del traffico di rete per un singolo computer o un gruppo di computer. | Su larga scala |
| Prestazioni | Fornisce una versione personalizzabile della visualizzazione prestazioni che si basa su tutti i contatori delle prestazioni di Analitica di Log abilitati. | Macchina virtuale singola | 
| connessioni | Le connessioni forniscono un'analisi approfondita delle connessioni in ingresso e in uscita dalla macchina virtuale. | Macchina virtuale singola |
 
## <a name="starting-with-a-template-or-saved-workbook"></a>Iniziare da un modello o da una cartella di lavoro salvata

Una cartella di lavoro è composta di sezioni costituite da grafici, tabelle, testo e controlli di input modificabili in modo indipendente gli uni dagli altri. Per comprendere meglio le cartelle di lavoro, aprire un modello e descrive la creazione di una cartella di lavoro personalizzato. 

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **Macchine virtuali**.

3. Selezionare una macchina virtuale dall'elenco.

4. Nella sezione **Monitoraggio** della pagina della macchina virtuale selezionare **Insights (anteprima)** .

5. Nella pagina informazioni dettagliate della macchina virtuale, selezionare **prestazioni** oppure **Maps** scheda e quindi selezionare **cartelle di lavoro di visualizzazione** dal collegamento nella pagina. 

    ![Screenshot di spostamento nelle cartelle di lavoro](media/vminsights-workbooks/workbook-option-01.png)

6. Nell'elenco a discesa, selezionare **Vai alla raccolta** dalla parte inferiore dell'elenco.

    ![Screenshot dell'elenco di riepilogo a discesa della cartella di lavoro](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Avvia la raccolta di cartella di lavoro con un numero di cartelle di lavoro predefiniti che consentono di iniziare.

7. Si inizierà con il **Modello predefinito**, che si trova sotto l'intestazione **Avvio rapido**.

    ![Screenshot della raccolta di cartelle di lavoro](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Modifica delle sezioni della cartella di lavoro

Le cartelle di lavoro presentano due modalità: **modalità di modifica** e **modalità di lettura**. Quando la cartella di lavoro modello predefinito prima di tutto viene avviata, viene aperta **modalità di modifica**. Mostra tutto il contenuto della cartella di lavoro, inclusi eventuali passaggi e i parametri che sono altrimenti sarebbe nascosto. La **modalità di lettura** presenta una visualizzazione semplificata sotto forma di report. Modalità di lettura consente sottraggono la complessità che si è verificato un errore nella creazione di un report mantenendo al contempo i meccanismi sottostanti solo pochi clic del mouse quando necessario per la modifica.

![Monitoraggio di Azure per la sezione delle cartelle di lavoro di macchine virtuali i controlli di modifica](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Dopo aver modificato una sezione, fare clic su **modifica completata** nell'angolo inferiore sinistro della sezione.

2. Per creare un duplicato di una sezione, fare clic sull'icona **Clona questa sezione**. La creazione di sezioni duplicate è un ottimo modo per eseguire l'iterazione in una query senza perdere le iterazioni precedenti.

3. Per spostare una sezione verso l'alto in una cartella di lavoro, fare clic sull'icona **Sposta in alto** o **Sposta giù**.

4. Per rimuovere una sezione in modo permanente, fare clic sull'icona **Rimuovi**.

## <a name="adding-text-and-markdown-sections"></a>Aggiunta di sezioni di testo e Markdown

L'aggiunta di intestazioni, spiegazioni e commenti nelle cartelle di lavoro consente di trasformare un insieme di tabelle e grafici in un resoconto. Le sezioni di testo nelle cartelle di lavoro supportano la [sintassi Markdown](https://daringfireball.net/projects/markdown/) per la formattazione del testo, ad esempio intestazioni, grassetto, corsivo ed elenchi puntati.

Per aggiungere una sezione di testo alla cartella di lavoro, usare il pulsante **Aggiungi testo** nella parte inferiore della cartella di lavoro o nella parte inferiore di una sezione.

## <a name="adding-query-sections"></a>Aggiunta di sezioni di query

![Sezione di query nelle cartelle di lavoro](media/vminsights-workbooks/005-workbook-query-section.png)

Per aggiungere una sezione di query alla cartella di lavoro, usare il pulsante **Aggiungi query** nella parte inferiore della cartella di lavoro o di una sezione.

Le sezioni di query, estremamente flessibili, possono essere usate per rispondere a domande quali:

* Come è stato l'utilizzo della CPU durante il periodo di tempo stesso un aumento del traffico di rete?
* Qual è la tendenza nello spazio disponibile su disco nell'ultimo mese?
* Quanti errori di connessione di rete VM esperienza nelle ultime due settimane? 

Inoltre non sono solo limitati per l'esecuzione di query dal contesto della macchina virtuale viene avviata la cartella di lavoro da. È possibile eseguire query tra più macchine virtuali, nonché le aree di lavoro di Log Analitica, purché si disponga dell'autorizzazione di accesso a tali risorse.

Per includere dati da altre aree di lavoro di Log Analitica o da una specifica app Application Insights usando il **dell'area di lavoro** identificatore. Per altre informazioni sulle query tra risorse, vedere la [linee guida ufficiali](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Impostazioni avanzate per le query di analisi

Ogni sezione ha le proprie impostazioni avanzate, che sono accessibili tramite le impostazioni ![controlli di modifica delle sezioni delle cartelle](media/vminsights-workbooks/006-settings.png) icona che si trova a destra del **aggiungere parametri** pulsante.

![Monitoraggio di Azure per la sezione delle cartelle di lavoro di macchine virtuali i controlli di modifica](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Imposta larghezza personalizzata per l'elemento**    | L'elemento è una dimensione arbitraria, pertanto è possibile inserire molti elementi in una singola riga consente di organizzare meglio le tabelle e grafici nel report interattivi avanzati.  |
| **Imposta la visibilità condizionata per questo elemento** | Consente di nascondere i passaggi in base a un parametro quando in modalità di lettura. |
| **Quando è selezionato un elemento del grafico, esporta un parametro**| Consentire una riga selezionata nella griglia o nel grafico per fare in modo i passaggi successivi modificare i valori o diverranno visibili.  |
| **Mostra query quando non viene modificata** | Visualizza la query sopra il grafico o la tabella anche se in modalità di lettura.
| **Mostra il pulsante Apri in Analisi quando non è attiva la modalità di modifica** | Aggiunge l'icona di Analitica blu nell'angolo a destra del grafico per consentire l'accesso con un clic.|

La maggior parte di queste impostazioni è piuttosto intuitiva, ma per comprendere **Quando è selezionato un elemento del grafico, esporta un parametro** è consigliabile esaminare una cartella di lavoro che usa questa funzionalità.

Una delle cartelle di lavoro predefiniti - **il traffico TCP**, vengono fornite informazioni sulle metriche di connessione da una macchina virtuale.

La prima sezione della cartella di lavoro si basa sui dati di query di log. La seconda sezione è inoltre basata sui dati di query di log, ma selezionando una riga della prima tabella verrà aggiornata in modo interattivo il contenuto dei grafici:

![Monitoraggio di Azure per la sezione delle cartelle di lavoro di macchine virtuali i controlli di modifica](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

Il comportamento è possibile tramite l'uso del **quando viene selezionato un elemento, esportare un parametro** le impostazioni avanzate, che vengono abilitate in query di log della tabella.

![Monitoraggio di Azure per la sezione delle cartelle di lavoro di macchine virtuali i controlli di modifica](media/vminsights-workbooks/009-settings-export.png)

La seconda query di log Usa quindi i valori esportati quando è selezionata una riga per creare un set di valori che vengono quindi usate per l'intestazione della sezione e grafici. Se è selezionata alcuna riga, nasconde l'intestazione della sezione e grafici. 

Ad esempio, il parametro nascosto nella seconda sezione Usa il riferimento seguente dalla riga selezionata nella griglia:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Aggiunta di sezioni di metriche

Le sezioni di metriche consentono l'accesso completo ai dati delle metriche di Monitoraggio di Azure perché sia possibile incorporarli nei report interattivi. In Monitoraggio di Azure per le macchine virtuali, le cartelle di lavoro predefiniti in genere non contengono dati di query analitiche anziché dati relativi alle metriche.  È possibile scegliere di creare le cartelle di lavoro con dati relativi alle metriche, consentendo di sfruttare il meglio di entrambe le funzionalità in un'unica posizione. È anche possibile eseguire il pull di dati delle metriche dalle risorse di una qualsiasi delle sottoscrizioni a cui è possibile accedere.

Di seguito è riportato un esempio di dati della macchina virtuale in corso il pull in una cartella di lavoro per fornire una visualizzazione griglia di prestazioni della CPU:

![Monitoraggio di Azure per la sezione delle cartelle di lavoro di macchine virtuali i controlli di modifica](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Aggiunta di sezioni di parametri

I parametri di una cartella di lavoro consentono di modificare i valori nella cartella senza dover modificare manualmente le sezioni di query o di testo. In questo modo non è necessario comprendere il linguaggio delle query di analisi sottostante e il numero dei destinatari possibili dei report basati sulle cartelle di lavoro viene notevolmente ampliato.

Nelle sezioni di query, di testo o di altri parametri, al posto del valore di un parametro si trova il nome del parametro stesso tra parentesi graffe, ad esempio ``{parameterName}``. I nomi dei parametri sono limitati a regole simili come identificatori JavaScript, i caratteri alfabetici o caratteri di sottolineatura, seguiti da caratteri alfanumerici o caratteri di sottolineatura. Ad esempio, il nome **a1** è consentito, ma **1a** non lo è.

I parametri sono lineari. Iniziano dalla parte superiore della cartella di lavoro e continuano verso il basso attraverso i passaggi successivi.  I parametri dichiarati più avanti in una cartella di lavoro è possono sostituire i parametri che sono stati dichiarati in precedenza. In questo modo anche i parametri che utilizzano le query per accedere ai valori dei parametri definiti in precedenza. Anche all'interno del passaggio di un parametro, i parametri sono lineari, da sinistra a destra. I parametri a destra possono dipendere da un parametro dichiarato in precedenza nello stesso passaggio.
 
Esistono quattro diversi tipi di parametri, che sono attualmente supportati:

|                  |      |
| ---------------- |:-----|
| **Text**    | Consente all'utente di modificare una casella di testo e, facoltativamente, è possibile fornire una query per inserire il valore predefinito. |
| **Elenco a discesa** | Consente all'utente di scegliere da un set di valori. |
| **Selezione dell'intervallo di tempo**| Consente all'utente di scegliere da un set predefinito di valori di intervallo di tempo o selezionare da un intervallo di tempo personalizzato.|
| **Selezione risorse** | Consente all'utente di scegliere tra le risorse selezionate per la cartella di lavoro.|

### <a name="using-a-text-parameter"></a>Uso di un parametro di testo

Il valore di un utente digita nella casella di testo viene sostituito direttamente nella query, senza eseguire l'escape o virgolette. Se il valore necessario è una stringa, all'interno della query il parametro deve essere racchiuso tra virgolette (ad esempio **'{parametro}'** ).

Il parametro di testo consente un valore in una casella di testo per essere utilizzati ovunque. in un nome di tabella, di colonna o di funzione, in un operatore e così via.  Il tipo di parametro di testo ha un'impostazione **ottenere il valore predefinito dalla query analitica**, che consente all'autore della cartella di lavoro di usare una query per popolare il valore predefinito per tale casella di testo.

Quando si usa il valore predefinito da una query di log, solo il primo valore della prima riga (riga 0 e una colonna 0) viene utilizzato come valore predefinito. È pertanto consigliabile fare in modo che la query restituisca una sola riga e una sola colonna. Eventuali altri dati restituiti dalla query vengono ignorati. 

Qualsiasi valore venga restituito dalla query viene inserito direttamente, senza escape o virgolette. Se la query non restituisce alcuna riga, il risultato del parametro corrisponde a una stringa vuota (se il parametro non è obbligatorio) oppure non è definito (se il parametro è obbligatorio).

### <a name="using-a-drop-down"></a>Usando un elenco a discesa

Il tipo di parametro di elenco a discesa consente di creare un controllo elenco a discesa, che consente la selezione di uno o più valori.

L'elenco a discesa viene popolato da una query di log o JSON. Se la query restituisce una colonna, i valori in tale colonna sono sia il valore e l'etichetta nel controllo elenco a discesa. Se la query restituisce due colonne, la prima colonna è il valore e la seconda colonna è l'etichetta visualizzata nell'elenco a discesa. Se la query restituisce tre colonne, la terza colonna consente di indicare la selezione predefinita in tale elenco a discesa. Questa colonna può essere di qualsiasi tipo, ma i più semplici da usare sono i tipi numerico e booleano, con 0 corrispondente a falso e 1 a vero.

Se la colonna è di tipo stringa, una stringa Null o vuota è considerata falsa e qualsiasi altro valore è considerato vero. Per elenchi a discesa di selezione singola, viene utilizzato il primo valore con un valore true come selezione predefinita.  Per elenchi a discesa di selezione più, tutti i valori con un valore true vengono utilizzati come il set predefinito selezionato. Gli elementi nell'elenco a discesa vengono visualizzati in qualsiasi ordine dalla query vengono restituite righe. 

Esaminiamo i parametri presenti nel report panoramica delle connessioni. Fare clic sul simbolo di modifica accanto a **direzione**.

![Monitoraggio di Azure per la sezione delle cartelle di lavoro di macchine virtuali i controlli di modifica](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Verrà avviata il **Modifica parametro** voce di menu.

![Monitoraggio di Azure per la sezione delle cartelle di lavoro di macchine virtuali i controlli di modifica](media/vminsights-workbooks/012-workbook-edit-parameter.png)

Il codice JSON consente di generare una tabella arbitraria inserita contenuto. Il codice JSON seguente, ad esempio, genera due valori nell'elenco a discesa:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Un esempio più applicabile utilizza un elenco a discesa per selezionare da un set di contatori delle prestazioni in base al nome:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

La query visualizzerà i risultati indicati di seguito:

![Elenco a discesa del contatore delle prestazioni](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Elenchi a discesa sono estremamente potenti strumenti per la personalizzazione e creazione di report interattivi.

### <a name="time-range-parameters"></a>Parametri dell'intervallo di tempo

Nonostante sia possibile creare un parametro intervallo di tempo personalizzato tramite il tipo di parametro elenco a discesa, se non è necessario un tale livello di flessibilità è anche possibile usare il tipo di parametro intervallo di tempo predefinito. 

I tipi di parametro intervallo di tempo predefiniti offrono 15 intervalli predefiniti, da 5 minuti agli ultimi 90 giorni. È anche disponibile un'opzione che consente la selezione di un intervallo di tempo personalizzato, con cui l'operatore del report può scegliere esplicitamente i valori di inizio e di fine dell'intervallo di tempo.

### <a name="resource-picker"></a>Selezione risorse

Il tipo di parametro selezione risorse offre la possibilità di definire l'ambito del report su tipi di risorse specifici. Un esempio di una cartella di lavoro predefinito che sfrutta il tipo di selezione della risorsa è il **prestazioni** della cartella di lavoro.

![Elenco a discesa di aree di lavoro](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Salvataggio e condivisione delle cartelle di lavoro con il team

Le cartelle di lavoro vengono salvate all'interno di un'area di lavoro di Log Analitica o di una risorsa di macchina virtuale, a seconda della modalità di accesso alla raccolta di cartelle di lavoro. La cartella di lavoro può essere salvato nel **report personali** privato o nella sezione la **report condivisi** sezione accessibile a tutti gli utenti con accesso alla risorsa. Per visualizzare tutte le cartelle di lavoro della risorsa, fare clic sul pulsante **Apri** nella barra delle azioni.

Per condividere una cartella di lavoro che è attualmente in **Report personali**:

1. Fare clic su **Apri** nella barra delle azioni
2. Fare clic sul pulsante "…" accanto alla cartella di lavoro da condividere
3. Fare clic su **Sposta in Report condivisi**.

Per condividere una cartella di lavoro con un collegamento o tramite posta elettronica, fare clic su **Condividi** nella barra delle azioni. Tenere presente che i destinatari del collegamento necessitano dell'accesso alla risorsa nel portale di Azure per visualizzare la cartella di lavoro. Per apportare modifiche, i destinatari devono avere almeno le autorizzazioni di collaboratori per la risorsa.

Per aggiungere un collegamento in una cartella di lavoro in un dashboard di Azure:

1. Fare clic su **Apri** nella barra delle azioni
2. Fare clic sul pulsante "…" accanto alla cartella di lavoro da aggiungere
3. Fare clic su **Aggiungi al dashboard**.

## <a name="next-steps"></a>Passaggi successivi
Per informazioni su come usare la funzionalità di integrità, vedere [Visualizza stato della macchina virtuale di Azure](vminsights-health.md), o per visualizzare le dipendenze individuate dell'applicazione, vedere [visualizzazione Monitoraggio di Azure per la mappa VMs](vminsights-maps.md). 