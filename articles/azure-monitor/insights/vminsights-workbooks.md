---
title: Creare report interattivi con cartelle di lavoro in Monitoraggio di Azure per le macchine virtuali
description: Semplifica la creazione di report complessi con cartelle di lavoro predefinite e personalizzate con parametri per Monitoraggio di Azure per le macchine virtuali.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 43cdb9de111bdea5486e49a56d58d38279b685c7
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91996384"
---
# <a name="create-interactive-reports-azure-monitor-for-vms-with-workbooks"></a>Creare report interattivi con cartelle di lavoro in Monitoraggio di Azure per le macchine virtuali

Le cartelle di lavoro combinano testo, [query di log](/azure/data-explorer/kusto/query/), metriche e parametri in report interattivi avanzati. Le cartelle di lavoro possono essere modificate da tutti gli altri membri del team che possono accedere alla stessa risorsa di Azure.

Le cartelle di lavoro sono utili per scenari come:

* Esplorazione dell'utilizzo della macchina virtuale quando non si conoscono in anticipo le metriche di interesse: utilizzo della CPU, spazio su disco, memoria, dipendenze di rete e così via. A differenza di altri strumenti di analisi dell'utilizzo, le cartelle di lavoro consentono di combinare più tipi di visualizzazioni e analisi, rendendoli eccezionali per questo tipo di esplorazione in formato libero.
* Come spiegare al team l'esecuzione di una macchina virtuale di cui è stato effettuato il provisioning di recente, mostrando le metriche per i contatori chiave e altri eventi del registro.
* Condivisione dei risultati di un esperimento di ridimensionamento della macchina virtuale con altri membri del team. È possibile illustrare gli obiettivi dell'esperimento con il testo, quindi mostrare ogni metrica di utilizzo e le query di analisi usate per valutare l'esperimento, oltre a deselezionare le chiamate per determinare se ogni metrica era superiore o inferiore alla destinazione.
* Segnalazione dell'effetto di un'interruzione sull'utilizzo della VM, combinazione di dati, spiegazione del testo e descrizione dei passaggi successivi per evitare interruzioni in futuro.

Nella tabella seguente sono riepilogate le cartelle di lavoro che Monitoraggio di Azure per le macchine virtuali include per iniziare.

| Cartella di lavoro | Descrizione | Scope |
|----------|-------------|-------|
| Prestazioni | Fornisce una versione personalizzabile della visualizzazione Top N list e Charts in una singola cartella di lavoro che utilizza tutti i contatori delle prestazioni Log Analytics abilitati.| Su larga scala |
| Contatori delle prestazioni | Visualizzazione del grafico Top N in un'ampia gamma di contatori delle prestazioni. | Su larga scala |
| Connessioni | Connessioni fornisce una visualizzazione approfondita delle connessioni in ingresso e in uscita dalle macchine virtuali monitorate. | Su larga scala |
| Porte attive | Fornisce un elenco dei processi associati alle porte nelle VM monitorate e le relative attività nell'intervallo di tempo scelto. | Su larga scala |
| Open Ports (Porte aperte) | Fornisce il numero di porte aperte sulle VM monitorate e i dettagli sulle porte aperte. | Su larga scala |
| Connessioni non riuscite | Visualizzare il numero di connessioni non riuscite nelle VM monitorate, la tendenza dell'errore e se la percentuale di errori aumenta nel tempo. | Su larga scala |
| Security and Audit | Un'analisi del traffico TCP/IP che segnala le connessioni globali, le connessioni dannose, in cui gli endpoint IP si trovano a livello globale.  Per abilitare tutte le funzionalità, è necessario abilitare il rilevamento della sicurezza. | Su larga scala |
| Traffico TCP | Un report classificato per le macchine virtuali monitorate e il traffico di rete inviato, ricevuto e totale in una griglia e visualizzato come linea di tendenza. | Su larga scala |
| Confronto traffico | Questa cartella di lavoro consente di confrontare le tendenze del traffico di rete per un singolo computer o un gruppo di computer. | Su larga scala |
| Prestazioni | Fornisce una versione personalizzabile della vista prestazioni che sfrutta tutti i contatori delle prestazioni Log Analytics abilitati. | Macchina virtuale singola | 
| Connessioni | Connections (connessioni) fornisce una visualizzazione approfondita delle connessioni in ingresso e in uscita dalla macchina virtuale. | Macchina virtuale singola |
 
## <a name="creating-a-new-workbook"></a>Creazione di una nuova cartella di lavoro

Una cartella di lavoro è composta di sezioni costituite da grafici, tabelle, testo e controlli di input modificabili in modo indipendente gli uni dagli altri. Per comprendere meglio le cartelle di lavoro, è possibile iniziare aprendo un modello e illustrando la creazione di una cartella di lavoro personalizzata. 

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Selezionare **macchine virtuali**.

3. Selezionare una macchina virtuale dall'elenco.

4. Nella sezione **monitoraggio** della pagina VM selezionare **informazioni dettagliate**.

5. Nella pagina VM Insights selezionare scheda **prestazioni** o **mappe** , quindi selezionare **Visualizza cartelle di lavoro** dal collegamento nella pagina. Nell'elenco a discesa selezionare **Vai alla raccolta**.

    ![Screenshot dell'elenco a discesa cartella di lavoro](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    Viene avviata la raccolta di cartelle di lavoro con una serie di cartelle di lavoro predefinite che consentono di iniziare.

7. Creare una nuova cartella di lavoro selezionando **nuovo**.

    ![Screenshot della raccolta di cartelle di lavoro](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Modifica delle sezioni di una cartella di lavoro

Le cartelle di lavoro presentano due modalità: **modalità di modifica** e **modalità di lettura**. Quando una nuova cartella di lavoro viene avviata per la prima volta, viene aperta in **modalità di modifica**. Mostra tutto il contenuto della cartella di lavoro, inclusi tutti i passaggi e i parametri altrimenti nascosti. La **modalità di lettura** presenta una visualizzazione semplificata sotto forma di report. La modalità di lettura consente di astrarre la complessità della creazione di un report, mantenendo al tempo stesso la meccanica sottostante solo pochi clic quando necessario per la modifica.

![Screenshot della sezione cartella di lavoro macchine virtuali in monitoraggio di Azure che mostra una nuova cartella di lavoro in modalità di modifica con i controlli di modifica evidenziati.](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Al termine della modifica di una sezione, fare clic su **modifica eseguita** nell'angolo inferiore sinistro della sezione.

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

* In che modo l'utilizzo della CPU si è verificato durante lo stesso periodo di tempo di un aumento del traffico di rete?
* Qual è la tendenza dello spazio su disco disponibile nell'ultimo mese?
* Quanti errori di connessione di rete sono stati rilevati nella macchina virtuale nelle ultime due settimane? 

Non è inoltre possibile eseguire query solo dal contesto della macchina virtuale dalla quale è stata avviata la cartella di lavoro. È possibile eseguire query su più macchine virtuali, nonché Log Analytics aree di lavoro, purché si disponga delle autorizzazioni di accesso a tali risorse.

Per includere dati da altre aree di lavoro Log Analytics o da un'app Application Insights specifica usando l'identificatore dell' **area di lavoro** . Per altre informazioni sulle query tra risorse, vedere la [Guida ufficiale](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Impostazioni avanzate per le query di analisi

In ogni sezione sono disponibili impostazioni avanzate, accessibili tramite l' ![ icona Impostazioni cartelle di lavoro di modifica dei controlli che si ](media/vminsights-workbooks/006-settings.png) trova a destra del pulsante **Aggiungi parametri** .

![Screenshot della finestra di dialogo Impostazioni avanzate nella sezione cartella di lavoro macchine virtuali di monitoraggio di Azure. Viene evidenziata l'icona che apre la finestra di dialogo.](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Imposta larghezza personalizzata per l'elemento**    | Rende una dimensione arbitraria di un elemento, in modo da poter adattare molti elementi a una singola riga consentendo di organizzare meglio i grafici e le tabelle in report interattivi avanzati.  |
| **Imposta la visibilità condizionata per questo elemento** | Specificare per nascondere i passaggi in base a un parametro in modalità lettura. |
| **Quando è selezionato un elemento del grafico, esporta un parametro**| Consenti a una riga selezionata della griglia o del grafico di fare in modo che i passaggi successivi modifichino i valori o diventino visibili.  |
| **Mostra query quando non viene modificata** | Consente di visualizzare la query sopra il grafico o la tabella anche in modalità lettura.
| **Mostra il pulsante Apri in Analisi quando non è attiva la modalità di modifica** | Aggiunge l'icona di analisi blu all'angolo destro del grafico per consentire l'accesso con un solo clic.|

La maggior parte di queste impostazioni è piuttosto intuitiva, ma per comprendere **Quando è selezionato un elemento del grafico, esporta un parametro** è consigliabile esaminare una cartella di lavoro che usa questa funzionalità.

Una delle cartelle di lavoro predefinite, ovvero il **traffico TCP**, fornisce informazioni sulle metriche di connessione da una macchina virtuale.

La prima sezione della cartella di lavoro è basata sui dati della query di log. La seconda sezione è basata anche sui dati della query di log, ma la selezione di una riga nella prima tabella aggiornerà in modo interattivo il contenuto dei grafici:

![Screenshot della sezione macchine virtuali in monitoraggio di Azure che mostra il traffico TCP della cartella di lavoro predefinita.](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

Il comportamento è possibile tramite l'utilizzo di **quando si seleziona un elemento, l'esportazione** delle impostazioni avanzate di un parametro, abilitate nella query di log della tabella.

![Screenshot della finestra di dialogo Impostazioni avanzate per una cartella di lavoro di macchine virtuali con l'opzione "quando è selezionato un elemento, esporta un parametro" selezionata.](media/vminsights-workbooks/009-settings-export.png)

La seconda query di log utilizza quindi i valori esportati quando viene selezionata una riga per creare un set di valori che vengono quindi utilizzati dall'intestazione e dai grafici della sezione. Se non è selezionata alcuna riga, l'intestazione e i grafici della sezione vengono nascosti. 

Ad esempio, il parametro hidden nella seconda sezione usa il riferimento seguente dalla riga selezionata nella griglia:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Aggiunta di sezioni di metriche

Le sezioni di metriche consentono l'accesso completo ai dati delle metriche di Monitoraggio di Azure perché sia possibile incorporarli nei report interattivi. In Monitoraggio di Azure per le macchine virtuali le cartelle di lavoro predefinite contengono in genere dati di query analitici anziché dati di metrica.  È possibile scegliere di creare cartelle di lavoro con dati di metrica, consentendo di sfruttare al meglio tutte le funzionalità disponibili in un'unica posizione. È anche possibile eseguire il pull di dati delle metriche dalle risorse di una qualsiasi delle sottoscrizioni a cui è possibile accedere.

Di seguito è riportato un esempio di dati della macchina virtuale di cui è stato eseguito il pull in una cartella di lavoro per fornire una visualizzazione griglia delle prestazioni della CPU:

![Screenshot della sezione metrica di una cartella di lavoro di una macchina virtuale in monitoraggio di Azure. Le prestazioni della CPU per ogni macchina virtuale sono visualizzate graficamente.](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Aggiunta di sezioni di parametri

I parametri di una cartella di lavoro consentono di modificare i valori nella cartella senza dover modificare manualmente le sezioni di query o di testo. In questo modo non è necessario comprendere il linguaggio delle query di analisi sottostante e il numero dei destinatari possibili dei report basati sulle cartelle di lavoro viene notevolmente ampliato.

Nelle sezioni di query, di testo o di altri parametri, al posto del valore di un parametro si trova il nome del parametro stesso tra parentesi graffe, ad esempio ``{parameterName}``. I nomi dei parametri sono limitati a regole simili come identificatori JavaScript, caratteri alfabetici o caratteri di sottolineatura, seguiti da caratteri alfanumerici o caratteri di sottolineatura. Ad esempio, il nome **a1** è consentito, ma **1a** non lo è.

I parametri sono lineari. Iniziano dalla parte superiore della cartella di lavoro e continuano verso il basso attraverso i passaggi successivi.  I parametri dichiarati in una cartella di lavoro possono eseguire l'override dei parametri dichiarati in precedenza. Consente inoltre ai parametri che usano le query di accedere ai valori dei parametri definiti in precedenza. Anche all'interno del passaggio di un parametro, i parametri sono lineari, da sinistra a destra. I parametri a destra possono dipendere da un parametro dichiarato in precedenza nello stesso passaggio.
 
Sono disponibili quattro tipi diversi di parametri, attualmente supportati:

|                  |      |
| ---------------- |:-----|
| **Text**    | Consente all'utente di modificare una casella di testo ed è possibile specificare facoltativamente una query per inserire il valore predefinito. |
| **Elenco a discesa** | Consente all'utente di scegliere un set di valori. |
| **Selezione dell'intervallo di tempo**| Consente all'utente di scegliere tra un set predefinito di valori di intervallo di tempo o di scegliere un intervallo di tempo personalizzato.|
| **Selezione risorse** | Consente all'utente di scegliere tra le risorse selezionate per la cartella di lavoro.|

### <a name="using-a-text-parameter"></a>Uso di un parametro di testo

Il valore che un utente digita nella casella di testo viene sostituito direttamente nella query, senza escape o virgolette. Se il valore necessario è una stringa, all'interno della query il parametro deve essere racchiuso tra virgolette (ad esempio **'{parametro}'**).

Il parametro text consente di usare il valore in una casella di testo in qualsiasi punto. Può essere un nome di tabella, un nome di colonna, un nome di funzione, un operatore e così via.  Il tipo di parametro text dispone di un'impostazione **Get default value from Analytics query**, che consente all'autore della cartella di lavoro di utilizzare una query per popolare il valore predefinito per tale casella di testo.

Quando si usa il valore predefinito da una query di log, come valore predefinito viene usato solo il primo valore della prima riga (riga 0, colonna 0). È pertanto consigliabile fare in modo che la query restituisca una sola riga e una sola colonna. Eventuali altri dati restituiti dalla query vengono ignorati. 

Qualsiasi valore venga restituito dalla query viene inserito direttamente, senza escape o virgolette. Se la query non restituisce alcuna riga, il risultato del parametro corrisponde a una stringa vuota (se il parametro non è obbligatorio) oppure non è definito (se il parametro è obbligatorio).

### <a name="using-a-drop-down"></a>Uso di un elenco a discesa

Il tipo di parametro DropDown consente di creare un controllo a discesa, consentendo la selezione di uno o più valori.

L'elenco a discesa viene popolato da una query di log o da JSON. Se la query restituisce una colonna, i valori della colonna sono sia il valore che l'etichetta nel controllo a discesa. Se la query restituisce due colonne, la prima colonna è il valore e la seconda colonna è l'etichetta visualizzata nell'elenco a discesa. Se la query restituisce tre colonne, la terza colonna viene utilizzata per indicare la selezione predefinita nell'elenco a discesa. Questa colonna può essere di qualsiasi tipo, ma i più semplici da usare sono i tipi numerico e booleano, con 0 corrispondente a falso e 1 a vero.

Se la colonna è di tipo stringa, una stringa Null o vuota è considerata falsa e qualsiasi altro valore è considerato vero. Per gli elenchi a discesa a selezione singola, il primo valore con un valore true viene usato come selezione predefinita.  Per gli elenchi a discesa a selezione multipla, tutti i valori con un valore true vengono usati come set selezionato predefinito. Gli elementi nell'elenco a discesa vengono visualizzati nell'ordine in cui la query ha restituito le righe. 

Esaminiamo i parametri presenti nel report Panoramica connessioni. Fare clic sul simbolo di modifica accanto a **direzione**.

![Screenshot della sezione per l'aggiunta e la modifica dei parametri del report in monitoraggio di Azure. L'icona di modifica per il parametro Direction è selezionata.](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Verrà avviata la voce di menu **modifica parametro** .

![Screenshot della finestra di dialogo Modifica parametro. Il nome del parametro è Direction, il tipo di parametro è drop down e Get data from JSON è selezionato.](media/vminsights-workbooks/012-workbook-edit-parameter.png)

JSON consente di generare una tabella arbitraria popolata con contenuto. Ad esempio, il codice JSON seguente genera due valori nell'elenco a discesa:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Un esempio più applicabile consiste nell'utilizzare un elenco a discesa per selezionare un set di contatori delle prestazioni in base al nome:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

La query visualizzerà i risultati indicati di seguito:

![Elenco a discesa contatore prestazioni](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

Gli elenchi a discesa sono strumenti incredibilmente potenti per la personalizzazione e la creazione di report interattivi.

### <a name="time-range-parameters"></a>Parametri dell'intervallo di tempo

Nonostante sia possibile creare un parametro intervallo di tempo personalizzato tramite il tipo di parametro elenco a discesa, se non è necessario un tale livello di flessibilità è anche possibile usare il tipo di parametro intervallo di tempo predefinito. 

I tipi di parametro intervallo di tempo predefiniti offrono 15 intervalli predefiniti, da 5 minuti agli ultimi 90 giorni. È anche disponibile un'opzione che consente la selezione di un intervallo di tempo personalizzato, con cui l'operatore del report può scegliere esplicitamente i valori di inizio e di fine dell'intervallo di tempo.

### <a name="resource-picker"></a>Selezione risorse

Il tipo di parametro selezione risorse offre la possibilità di definire l'ambito del report su tipi di risorse specifici. Un esempio di una cartella di lavoro predefinita che utilizza il tipo di selezione risorse è la cartella di lavoro **prestazioni** .

![Elenco a discesa aree di lavoro](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Salvataggio e condivisione delle cartelle di lavoro con il team

Le cartelle di lavoro vengono salvate in un'area di lavoro Log Analytics o in una risorsa di macchina virtuale, a seconda della modalità di accesso alla raccolta di cartelle di lavoro. La cartella di lavoro può essere salvata nella sezione **report personali** riservata all'utente o nella sezione **report condivisi** accessibile a tutti gli utenti che dispongono dell'accesso alla risorsa. Per visualizzare tutte le cartelle di lavoro della risorsa, fare clic sul pulsante **Apri** nella barra delle azioni.

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

- Per identificare le limitazioni e le prestazioni complessive della macchina virtuale, vedere [visualizzare le prestazioni delle macchine virtuali di Azure](vminsights-performance.md).

- Per informazioni sulle dipendenze delle applicazioni individuate, vedere [Visualizzare la mappa di Monitoraggio di Azure per le macchine virtuali](vminsights-maps.md).