---
title: Creare report interattivi con cartelle di lavoro in Monitoraggio di Azure per le macchine virtuali
description: Semplifica la creazione di report complessi con cartelle di lavoro con parametri predefinite e personalizzate per Monitoraggio di Azure per macchine virtuali.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: a6ab126c3a5b0d2a82b17fac42dcc9e20f6aba3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480454"
---
# <a name="create-interactive-reports-azure-monitor-for-vms-with-workbooks"></a>Creare report interattivi con cartelle di lavoro in Monitoraggio di Azure per le macchine virtuali

Le cartelle di lavoro combinano testo, [query di log,](../log-query/query-language.md)metriche e parametri in report interattivi avanzati. Le cartelle di lavoro possono essere modificate da tutti gli altri membri del team che possono accedere alla stessa risorsa di Azure.

Le cartelle di lavoro sono utili per scenari quali:Workbooks are helpful for scenarios such as:

* Esplorazione dell'utilizzo della macchina virtuale quando non si conoscono in anticipo le metriche di interesse: utilizzo della CPU, spazio su disco, memoria, dipendenze di rete e così via. A differenza di altri strumenti di analisi dell'utilizzo, le cartelle di lavoro consentono di combinare più tipi di visualizzazioni e analisi, rendendole ideali per questo tipo di esplorazione in formato libero.
* Spiegare al team le prestazioni di una macchina virtuale di cui è stato eseguito il provisioning di recente, mostrando le metriche per i contatori chiave e altri eventi di log.
* Condivisione dei risultati di un esperimento di ridimensionamento della macchina virtuale con altri membri del team. È possibile spiegare gli obiettivi per l'esperimento con il testo, quindi mostrare ogni metrica di utilizzo e le query di analisi utilizzate per valutare l'esperimento, insieme a callout chiari per verificare se ogni metrica era superiore o inferiore alla destinazione.
* Segnalazione dell'impatto di un'interruzione sull'utilizzo della macchina virtuale, combinazione di dati, spiegazione testuale e discussione dei passaggi successivi per evitare interruzioni in futuro.

La tabella seguente riepiloga le cartelle di lavoro incluse in Monitoraggio di Azure per le macchine virtuali per iniziare.

| Cartella di lavoro | Descrizione | Scope |
|----------|-------------|-------|
| Prestazioni | Fornisce una versione personalizzabile della visualizzazione Elenco N principali e Grafici in un'unica cartella di lavoro che sfrutta tutti i contatori delle prestazioni di Log Analytics abilitati.| Su larga scala |
| Contatori delle prestazioni | Una visualizzazione dei grafici Top N in un ampio set di contatori delle prestazioni. | Su larga scala |
| Connessioni | Le connessioni forniscono una visualizzazione approfondita delle connessioni in ingresso e in uscita dalle macchine virtuali monitorate. | Su larga scala |
| Porte attive | Fornisce un elenco dei processi associati alle porte nelle macchine virtuali monitorate e la relativa attività nell'intervallo di tempo scelto. | Su larga scala |
| Open Ports (Porte aperte) | Fornisce il numero di porte aperte nelle macchine virtuali monitorate e i dettagli su tali porte aperte. | Su larga scala |
| Connessioni non riuscite | Visualizzare il numero di connessioni non riuscite nelle macchine virtuali monitorate, la tendenza di errore e se la percentuale di errori aumenta nel tempo. | Su larga scala |
| Security and Audit | Analisi del traffico TCP/IP che segnala connessioni globali, connessioni dannose, in cui risiedono gli endpoint IP a livello globale.  Per abilitare tutte le funzionalità, è necessario abilitare il rilevamento della sicurezza. | Su larga scala |
| Traffico TCP | Un rapporto classificato per le macchine virtuali monitorate e il relativo traffico di rete inviato, ricevuto e totale in una griglia e visualizzato come una linea di tendenza. | Su larga scala |
| Confronto traffico | Questa cartella di lavoro consente di confrontare le tendenze del traffico di rete per un singolo computer o un gruppo di computer. | Su larga scala |
| Prestazioni | Fornisce una versione personalizzabile della visualizzazione Prestazioni che sfrutta tutti i contatori delle prestazioni di Log Analytics abilitati. | Macchina virtuale singola | 
| Connessioni | Connections offre una visualizzazione approfondita delle connessioni in ingresso e in uscita dalla macchina virtuale. | Macchina virtuale singola |
 
## <a name="creating-a-new-workbook"></a>Creazione di una nuova cartella di lavoro

Una cartella di lavoro è composta di sezioni costituite da grafici, tabelle, testo e controlli di input modificabili in modo indipendente gli uni dagli altri. Per comprendere meglio le cartelle di lavoro, iniziamo aprendo un modello ed è possibile creare una cartella di lavoro personalizzata. 

1. Accedere al [portale](https://portal.azure.com)di Azure .

2. Selezionare **Macchine virtuali**.

3. Selezionare una macchina virtuale dall'elenco.

4. Nella sezione **Monitoraggio** della pagina VM selezionare **Insights.**

5. Nella pagina Informazioni dettagliate macchina virtuale selezionare la scheda **Prestazioni** o **Mappe** e quindi selezionare **Visualizza cartelle di lavoro** dal collegamento nella pagina. Nell'elenco a discesa selezionare **Vai alla raccolta**.

    ![Screenshot dell'elenco a discesa della cartella di lavoro](media/vminsights-workbooks/workbook-dropdown-gallery-01.png)

    In questo modo viene avviata la raccolta di cartelle di lavoro con una serie di cartelle di lavoro predefinite per iniziare.

7. Creare una nuova cartella di lavoro selezionando **Nuovo**.

    ![Screenshot della raccolta di cartelle di lavoro](media/vminsights-workbooks/workbook-gallery-01.png)

## <a name="editing-workbook-sections"></a>Modifica delle sezioni di una cartella di lavoro

Le cartelle di lavoro presentano due modalità: **modalità di modifica** e **modalità di lettura**. Quando una nuova cartella di lavoro viene avviata per la prima volta, viene aperta in modalità di **modifica.** Mostra tutto il contenuto della cartella di lavoro, inclusi eventuali passaggi e parametri altrimenti nascosti. La **modalità di lettura** presenta una visualizzazione semplificata sotto forma di report. La modalità di lettura consente di eliminare la complessità che è andato nella creazione di un report pur avendo la meccanica sottostante a pochi clic di distanza quando necessario per la modifica.

![Sezione Monitoraggio di Azure per le cartelle di lavoro per macchine virtuali che modifica i controlli](media/vminsights-workbooks/workbook-new-workbook-editor-01.png)

1. Al termine della modifica di una sezione, fai clic su **Modifica completata** nell'angolo in basso a sinistra della sezione.

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

* Come è stato l'utilizzo della CPU durante lo stesso periodo di tempo come un aumento del traffico di rete?
* Qual è stata la tendenza nello spazio disponibile su disco nell'ultimo mese?
* Quanti errori di connessione di rete si sono verificate nelle ultime due settimane nella macchina virtuale? 

Inoltre, non si è limitati solo a eseguire query dal contesto della macchina virtuale da cui è stata avviata la cartella di lavoro. È possibile eseguire query su più macchine virtuali e aree di lavoro di Log Analytics, purché si disponga dell'autorizzazione di accesso a tali risorse.

Per includere dati da altre aree di lavoro di Log Analytics o da un'app di Application Insights specifica usando l'identificatore **dell'area di lavoro.** Per ulteriori informazioni sulle query tra risorse, fare riferimento alle [linee guida ufficiali](../log-query/cross-workspace-query.md).

### <a name="advanced-analytic-query-settings"></a>Impostazioni avanzate per le query di analisi

Ogni sezione ha le proprie impostazioni avanzate, ![che sono](media/vminsights-workbooks/006-settings.png) accessibili tramite l'icona di controlli di modifica della sezione Cartelle di lavoro a destra del pulsante **Aggiungi parametri.**

![Sezione Monitoraggio di Azure per le cartelle di lavoro per macchine virtuali che modifica i controlli](media/vminsights-workbooks/007-settings-expanded.png)

|         |          |
| ---------------- |:-----|
| **Imposta larghezza personalizzata per l'elemento**    | Rende un elemento di dimensioni arbitrarie, in modo da poter inserire molti elementi su una singola riga consentendo di organizzare meglio i grafici e le tabelle in report interattivi avanzati.  |
| **Imposta la visibilità condizionata per questo elemento** | Specificare se nascondere i passaggi in base a un parametro in modalità di lettura. |
| **Quando è selezionato un elemento del grafico, esporta un parametro**| Consentire a una riga selezionata nella griglia o nel grafico di modificare i valori o diventare visibile nei passaggi successivi.  |
| **Mostra query quando non viene modificata** | Visualizza la query sopra il grafico o la tabella anche in modalità di lettura.
| **Mostra il pulsante Apri in Analisi quando non è attiva la modalità di modifica** | Aggiunge l'icona blu di Analytics nell'angolo destro del grafico per consentire l'accesso con un clic.|

La maggior parte di queste impostazioni è piuttosto intuitiva, ma per comprendere **Quando è selezionato un elemento del grafico, esporta un parametro** è consigliabile esaminare una cartella di lavoro che usa questa funzionalità.

Una delle cartelle di lavoro predefinite - **Traffico TCP**, fornisce informazioni sulle metriche di connessione da una macchina virtuale.

La prima sezione della cartella di lavoro è basata sui dati della query di log. La seconda sezione si basa anche sui dati della query di log, ma la selezione di una riga nella prima tabella aggiornerà in modo interattivo il contenuto dei grafici:

![Sezione Monitoraggio di Azure per le cartelle di lavoro per macchine virtuali che modifica i controlli](media/vminsights-workbooks/008-workbook-tcp-traffic.png)

Il comportamento è possibile tramite l'utilizzo delle impostazioni avanzate **Quando un elemento è selezionato, esportare un parametro** avanzato, che sono abilitate nella query di log della tabella.

![Sezione Monitoraggio di Azure per le cartelle di lavoro per macchine virtuali che modifica i controlli](media/vminsights-workbooks/009-settings-export.png)

La seconda query di log utilizza quindi i valori esportati quando viene selezionata una riga per creare un set di valori che vengono quindi utilizzati dall'intestazione di sezione e dai grafici. Se non è selezionata alcuna riga, nasconde l'intestazione di sezione e i grafici. 

Ad esempio, il parametro nascosto nella seconda sezione utilizza il seguente riferimento dalla riga selezionata nella griglia:

```
VMConnection
| where TimeGenerated {TimeRange}
| where Computer in ("{ComputerName}") or '*' in ("{ComputerName}") 
| summarize Sent = sum(BytesSent), Received = sum(BytesReceived) by bin(TimeGenerated, {TimeRange:grain})
```

## <a name="adding-metrics-sections"></a>Aggiunta di sezioni di metriche

Le sezioni di metriche consentono l'accesso completo ai dati delle metriche di Monitoraggio di Azure perché sia possibile incorporarli nei report interattivi. In Monitoraggio di Azure per le macchine virtuali le cartelle di lavoro predefinite conterranno in genere dati di query analitici anziché dati metrici.  È possibile scegliere di creare cartelle di lavoro con dati metriche, consentendo di sfruttare appieno il meglio di entrambe le funzionalità in un'unica posizione. È anche possibile eseguire il pull di dati delle metriche dalle risorse di una qualsiasi delle sottoscrizioni a cui è possibile accedere.

Di seguito è riportato un esempio di dati di macchina virtuale estratti in una cartella di lavoro per fornire una visualizzazione griglia delle prestazioni della CPU:Here is an example of virtual machine data being pulled into a workbook to provide a grid visualization of CPU performance:

![Sezione Monitoraggio di Azure per le cartelle di lavoro per macchine virtuali che modifica i controlli](media/vminsights-workbooks/010-metrics-grid.png)

## <a name="adding-parameter-sections"></a>Aggiunta di sezioni di parametri

I parametri di una cartella di lavoro consentono di modificare i valori nella cartella senza dover modificare manualmente le sezioni di query o di testo. In questo modo non è necessario comprendere il linguaggio delle query di analisi sottostante e il numero dei destinatari possibili dei report basati sulle cartelle di lavoro viene notevolmente ampliato.

Nelle sezioni di query, di testo o di altri parametri, al posto del valore di un parametro si trova il nome del parametro stesso tra parentesi graffe, ad esempio ``{parameterName}``. I nomi dei parametri sono limitati a regole simili a quelle degli identificatori JavaScript, dei caratteri alfabetici o dei caratteri di sottolineatura, seguiti da caratteri alfanumerici o caratteri di sottolineatura. Ad esempio, il nome **a1** è consentito, ma **1a** non lo è.

I parametri sono lineari. Iniziano dalla parte superiore della cartella di lavoro e continuano verso il basso attraverso i passaggi successivi.  I parametri dichiarati in un secondo momento in una cartella di lavoro possono eseguire l'override dei parametri dichiarati in precedenza. Ciò consente inoltre di specificare i parametri che utilizzano query per accedere ai valori dei parametri definiti in precedenza. Anche all'interno del passaggio di un parametro, i parametri sono lineari, da sinistra a destra. I parametri a destra possono dipendere da un parametro dichiarato in precedenza nello stesso passaggio.
 
Esistono quattro diversi tipi di parametri, attualmente supportati:

|                  |      |
| ---------------- |:-----|
| **Testo**    | Consente all'utente di modificare una casella di testo ed è possibile fornire facoltativamente una query per inserire il valore predefinito. |
| **Elenco a discesa** | Consente all'utente di scegliere da un set di valori. |
| **Selezione dell'intervallo di tempo**| Consente all'utente di scegliere tra un set predefinito di valori dell'intervallo di tempo o di scegliere tra un intervallo di tempo personalizzato.|
| **Selezione risorse** | Consente all'utente di scegliere tra le risorse selezionate per la cartella di lavoro.|

### <a name="using-a-text-parameter"></a>Uso di un parametro di testo

Il valore digitato da un utente nella casella di testo viene sostituito direttamente nella query, senza escquandosi o citare tra virgolette. Se il valore necessario è una stringa, all'interno della query il parametro deve essere racchiuso tra virgolette (ad esempio **'{parametro}'**).

Il parametro text consente di utilizzare il valore in una casella di testo ovunque. Può essere un nome di tabella, un nome di colonna, un nome di funzione, un operatore e così via.  Il tipo di parametro di testo include un'impostazione **Ottieni valore predefinito da query**di analisi , che consente all'autore della cartella di lavoro di utilizzare una query per popolare il valore predefinito per tale casella di testo.

Quando si utilizza il valore predefinito di una query di log, solo il primo valore della prima riga (riga 0, colonna 0) viene utilizzato come valore predefinito. È pertanto consigliabile fare in modo che la query restituisca una sola riga e una sola colonna. Eventuali altri dati restituiti dalla query vengono ignorati. 

Qualsiasi valore venga restituito dalla query viene inserito direttamente, senza escape o virgolette. Se la query non restituisce alcuna riga, il risultato del parametro corrisponde a una stringa vuota (se il parametro non è obbligatorio) oppure non è definito (se il parametro è obbligatorio).

### <a name="using-a-drop-down"></a>Utilizzo di un menu a discesa

Il tipo di parametro a discesa consente di creare un controllo elenco a discesa, consentendo la selezione di uno o più valori.

L'elenco a discesa viene popolato da una query di log o JSON. Se la query restituisce una colonna, i valori in tale colonna sono sia il valore che l'etichetta nel controllo elenco a discesa. Se la query restituisce due colonne, la prima colonna è il valore e la seconda colonna è l'etichetta visualizzata nell'elenco a discesa. Se la query restituisce tre colonne, la terza colonna viene utilizzata per indicare la selezione predefinita nell'elenco a discesa. Questa colonna può essere di qualsiasi tipo, ma i più semplici da usare sono i tipi numerico e booleano, con 0 corrispondente a falso e 1 a vero.

Se la colonna è di tipo stringa, una stringa Null o vuota è considerata falsa e qualsiasi altro valore è considerato vero. Per i menu a discesa a selezione singola, il primo valore con un valore true viene utilizzato come selezione predefinita.  Per l'elenco a discesa di selezione multipla, tutti i valori con un valore true vengono utilizzati come set selezionato predefinito. Gli elementi nell'elenco a discesa vengono visualizzati nell'ordine in cui la query ha restituito le righe. 

Esaminiamo i parametri presenti nel report Panoramica connessioni. Fare clic sul simbolo di modifica accanto a **Direzione**.

![Sezione Monitoraggio di Azure per le cartelle di lavoro per macchine virtuali che modifica i controlli](media/vminsights-workbooks/011-workbook-using-dropdown.png)

Verrà avviata la voce di menu **Modifica parametro.**

![Sezione Monitoraggio di Azure per le cartelle di lavoro per macchine virtuali che modifica i controlli](media/vminsights-workbooks/012-workbook-edit-parameter.png)

Il codice JSON consente di generare una tabella arbitraria popolata con contenuto. Ad esempio, il codice JSON seguente genera due valori nell'elenco a discesa:For example, the following JSON generates two values in the drop-down:

```
[
    { "value": "inbound", "label": "Inbound"},
    { "value": "outbound", "label": "Outbound"}
]
```

Un esempio più applicabile consiste nell'utilizzare un elenco a discesa per scegliere da un set di contatori delle prestazioni in base al nome:A more applicable example is using a drop-down to pick from a set of performance counters by name:

```
Perf
| summarize by CounterName, ObjectName
| order by ObjectName asc, CounterName asc
| project Counter = pack('counter', CounterName, 'object', ObjectName), CounterName, group = ObjectName
```

La query visualizzerà i risultati indicati di seguito:

![Menu a discesa Contatore perf](media/vminsights-workbooks/013-workbook-edit-parameter-perf-counters.png)

I menu a discesa sono strumenti incredibilmente potenti per la personalizzazione e la creazione di report interattivi.

### <a name="time-range-parameters"></a>Parametri dell'intervallo di tempo

Nonostante sia possibile creare un parametro intervallo di tempo personalizzato tramite il tipo di parametro elenco a discesa, se non è necessario un tale livello di flessibilità è anche possibile usare il tipo di parametro intervallo di tempo predefinito. 

I tipi di parametro intervallo di tempo predefiniti offrono 15 intervalli predefiniti, da 5 minuti agli ultimi 90 giorni. È anche disponibile un'opzione che consente la selezione di un intervallo di tempo personalizzato, con cui l'operatore del report può scegliere esplicitamente i valori di inizio e di fine dell'intervallo di tempo.

### <a name="resource-picker"></a>Selezione risorse

Il tipo di parametro selezione risorse offre la possibilità di definire l'ambito del report su tipi di risorse specifici. Un esempio di cartella di lavoro predefinita che sfrutta il tipo di selezione risorse è la cartella di lavoro **Prestazioni.An** example of a prebuilt workbook that leverages the resource picker type is the Performance workbook.

![menu a discesa Aree di lavoro](media/vminsights-workbooks/014-workbook-edit-parameter-workspaces.png)

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Salvataggio e condivisione delle cartelle di lavoro con il team

Le cartelle di lavoro vengono salvate in un'area di lavoro di Log Analytics o in una risorsa di macchina virtuale, a seconda della modalità di accesso alla raccolta di cartelle di lavoro. La cartella di lavoro può essere salvata nella sezione **Report** personali privata per l'utente o nella sezione **Report condivisi** accessibile a tutti gli utenti con accesso alla risorsa. Per visualizzare tutte le cartelle di lavoro della risorsa, fare clic sul pulsante **Apri** nella barra delle azioni.

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

- Per identificare le limitazioni e le prestazioni complessive della macchina virtuale, vedere Visualizzare le prestazioni delle macchine virtuali di Azure.To identify limitations and overall VM [performance,](vminsights-performance.md)see View Azure VM Performance .

- Per altre informazioni sulle dipendenze delle applicazioni individuate, vedere [Visualizzare Azure Monitor per le macchine virtuali Mappare](vminsights-maps.md).
