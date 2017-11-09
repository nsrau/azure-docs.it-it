---
title: Usare le trasformazioni dei dati per la preparazione dei dati in Azure Machine Learning | Microsoft Docs
description: Questo articolo fornisce un elenco completo delle trasformazioni disponibili per la preparazione dei dati per Azure Machine Learning
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 10/09/2017
ms.openlocfilehash: 4331bb4a16d56f027dd63a97868822fa6ecc92d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="use-data-transforms-for-data-preparation-in-azure-machine-learning"></a>Usare le trasformazioni dei dati per la preparazione dei dati in Azure Machine Learning

Una *trasformazione* in Azure Machine Learning usa i dati in un formato specificato, esegue un'operazione sui dati (ad esempio la modifica del tipo dei dati) e quindi genera dei dati nel nuovo formato. Ogni trasformazione ha interfaccia e comportamento propri. È possibile concatenare più trasformazioni tra loro tramite dei passaggi nel flusso di dati, consentendo di eseguire trasformazioni complesse e ripetibili sui dati. Si tratta di componenti di base delle funzionalità di preparazione dei dati.

Di seguito è riportato un elenco delle trasformazioni disponibili in Azure Machine Learning. 

## <a name="column-selection"></a>Selezione di colonne 
Molte trasformazioni elencate di seguito operano su una o su più colonne. Per selezionare più colonne, usare il tasto **Ctrl** o per selezionare un intervallo di colonne, usare il tasto **MAIUSC**.

## <a name="transforms-from-main-menu-andor-grid-header"></a>Trasformazioni dal menu principale e/o dall'intestazione della griglia 
È possibile accedere alle trasformazioni dall'opzione Transforms (Trasformazioni) del menu principale. Le trasformazioni possono essere selezionate anche facendo clic col tasto destro sul nome della colonna nella griglia dei dati. Se sono selezionate più colonne, facendo clic su una qualsiasi di esse si ottiene un menu di trasformazioni.

Vengono offerte solo le trasformazioni valide per il tipo di dati selezionato nel menu di scelta rapida. Nel menu principale vengono offerte tutte le trasformazioni, ma vengono disattivate le trasformazioni che non sono rilevanti per le colonne selezionate.

Un piccolo subset delle trasformazioni contestuali è disponibile facendo clic con il pulsante destro del mouse su una cella. Queste trasformazioni sono Copia, Sostituisci e Filtra. Queste trasformazioni riconoscono il tipo di dati e quindi le opzioni per una colonna numerica sono diverse rispetto a quella di una colonna contenente stringhe.

## <a name="derive-column-by-example"></a>Derive Column by Example (Deriva colonna in base all'esempio)
Questa trasformazione consente di creare una nuova colonna come derivata da uno o più colonne esistenti. Le trasformazioni esaminano le colonne di input (selezionate) e l'esempio fornito e quindi determinano l'output desiderato nella nuova colonna. 

Per usare questa trasformazione, selezionare una o più colonne. Aggiungere una nuova colonna derivata (vuota) come esempio. Digitare un esempio di ciò che si vuole visualizzare nella colonna derivata, presumendo che derivi da altre colonne, e la tecnologia "By Example" tenta di compilare tutte le altre celle nella colonna. 

Per esempi complessi potrebbe essere necessario fornire più esempi. A tale scopo, selezionare un'altra cella e digitare un altro esempio.

La tecnologia "By Example" usa le colonne selezionate per tentare di determinare il significato di un esempio. Se non sono selezionate colonne quando viene richiamata questa trasformazione, vengono usate tutte le celle per la riga corrente. La selezione solo delle colonne necessarie implica risultati più accurati.

È possibile selezionare le colonne prima di richiamare la trasformazione. Dopo avere avviato l'editor di trasformazione, controllare che le caselle nella parte superiore di ogni colonna indichino quali colonne sono selezionate come input. È possibile aggiungere o rimuovere delle colonne dalla selezione usando le caselle di controllo nelle intestazioni di colonna.

Per una spiegazione più dettagliata della trasformazione **Derive Column By Example** (Deriva colonna in base all'esempio) e per altri esempi, vedere il [documento di riferimento per la trasformazione Derive Column by Example (Deriva colonna in base all'esempio)](data-prep-derive-column-by-example.md)  

## <a name="split-column-by-example"></a>Split Column by Example (Dividi colonna in base all'esempio)
Questa trasformazione usa una colonna esistente e tenta di dividere questa colonna in altre *n* colonne usando il motore "By Example". È possibile eseguire la divisione automatica per le colonne generate successive.

Per una spiegazione più dettagliata della trasformazione **Split Column By Example** (Dividi colonna in base all'esempio) e per altri esempi, vedere il [documento di riferimento per la trasformazione Split Column By Example (Dividi colonna in base all'esempio)](data-prep-split-column-by-example.md)

## <a name="expand-json"></a>Expand JSON (Espandi JSON)

Questa trasformazione consente di aggiungere più colonne espandendo una colonna con testo JSON valido.

Per una spiegazione più dettagliata della trasformazione **Expand JSON** (Espandi JSON) e per altri esempi, vedere il [documento di riferimento per la trasformazione Expand JSON (Espandi JSON)](data-prep-expand-json.md)


## <a name="combine-columns-by-example"></a>Combine Columns by Example (Combina colonne in base all'esempio)

Questa trasformazione consente di aggiungere una nuova colonna combinando i valori da più colonne. 

Per una spiegazione più dettagliata della trasformazione **Combine Columns By Example** (Combina colonne in base all'esempio) e per altri esempi, vedere il [documento di riferimento per la trasformazione Combine Columns by Example Reference (Combina colonne in base all'esempio)](data-prep-combine-columns-by-example.md)


## <a name="duplicate-column"></a>Duplicate Column (Duplica colonna)
Questa trasformazione esegue una copia esatta di una o più colonne selezionate e assegna a ciascuna di esse un nuovo nome derivato dal nome della colonna originale.

## <a name="text-clustering"></a>Text Clustering (Clustering di testo) 
Questa trasformazione è progettata per accettare valori non coerenti che dovrebbero essere gli stessi e raggrupparli insieme.  

Quando si usa questa trasformazione, i valori in una singola colonna vengono analizzati per individuarne la somiglianza e raggruppati in cluster. Per ogni cluster esiste un valore canonico, ovvero il valore che sostituisce tutte le istanze del cluster e i valori delle istanze. È possibile rimuovere cluster completi ed è possibile modificare il valore canonico. Le istanze possono essere rimosse da un cluster specifico. È possibile modificare il filtro di soglia del punteggio di somiglianza usato per raggruppare le istanze in un cluster.

Per impostazione predefinita questa trasformazione sostituisce tutti i valori dell'istanza del cluster con il valore canonico per tale cluster, creando una nuova colonna per contenere i nuovi valori. È possibile anche aggiungere il punteggio di somiglianza, per ogni istanza, a una nuova colonna (che può essere denominata) per consentire l'uso in un secondo momento nel flusso di dati.

## <a name="replace-values"></a>Replace Values (Sostituisci valori)
Questa trasformazione consente a una stringa di essere sostituita con un'altra. La stringa di origine può essere una stringa parziale o una cella completa. La sostituzione può essere applicata a una singola colonna o a numerose colonne. La stringa di ricerca supporta la ricerca di caratteri speciali e di caratteri normali. 

## <a name="replace-na-values"></a>Replace NA Values (Sostituisci valori NA)
Questa trasformazione consente di sostituire le varie forme diverse di NA (N/A, NA, null, NaN e così via) o le stringhe vuote con un singolo valore in modo da renderle coerenti. Questa trasformazione supporta uno o più colonne. Questa trasformazione è elencata solo quando viene selezionata una colonna; non è presente nel menu principale delle trasformazioni quando non sono selezionate colonne.

## <a name="replace-missing-values"></a>Replace Missing Values (Sostituisci valori mancanti)
Questa trasformazione sostituisce dei dati mancanti con un singolo valore. Questa trasformazione supporta uno o più colonne. Questa trasformazione è elencata solo quando viene selezionata una colonna; non è presente nel menu principale delle trasformazioni quando non sono selezionate colonne.

## <a name="replace-error-values"></a>Replace Error Values (Sostituisci valori di errore)
Questa trasformazione sostituisce degli errori con un singolo valore. Questa trasformazione supporta uno o più colonne. Questa trasformazione è elencata solo quando viene selezionata una colonna; non è presente nel menu principale delle trasformazioni quando non sono selezionate colonne.

## <a name="trim-string"></a>Trim String (Taglia stringa)
Questa trasformazione rimuove i caratteri "spazio" iniziali e finali, tra cui spazi, tabulazioni, *etc.*, da una o più colonne.

## <a name="adjust-precision"></a>Adjust Precision (Regola precisione)
Questa trasformazione consente di impostare il numero di posizioni decimali per una colonna numerica.

## <a name="rename-column"></a>Rename Column (Rinomina colonna)
Questa trasformazione modifica il nome della colonna selezionata. Questa trasformazione può anche essere richiamata inline nell'intestazione di colonna facendo clic sul nome della colonna.

## <a name="remove-column"></a>Remove Column (Rimuovi colonna)
Questa trasformazione rimuove la/e colonna/e selezionata/e. Questa trasformazione opera su una o più colonne. 

## <a name="keep-column"></a>Keep Column (Mantieni colonna)
Questa trasformazione mantiene solo la/e colonna/e selezionata/e. Questa trasformazione opera su una o più colonne.

## <a name="handle-path-column"></a>Handle Path Column (Gestisci colonna percorso)
Durante l'importazione di un file viene aggiunta automaticamente del percorso al set di dati dalla procedura guidata Aggiungi origine dati. Contiene il nome file completo che costituisce il percorso del set di dati. Questa trasformazione aggiunge o rimuove la colonna aggiuntiva dal set di dati.

## <a name="convert-field-type-to-numeric"></a>Convert Field Type to Numeric (Converti tipo di campo in numerico)
Questa trasformazione modifica il tipo di colonna impostandolo su numerico. Se sono presenti dati non interi, è possibile specificare il separatore. Per impostazione predefinita questa trasformazione non visualizza una richiesta per il separatore; usare la voce di menu **Modifica** per richiamare l'editor. Questa trasformazione opera su una o più colonne.

## <a name="convert-field-type-to-date"></a>Convert Field Type to Date (Converti tipo di campo in data)
Questa trasformazione modifica il tipo di colonna impostandolo su data. Viene usato un formato di data/ora predefinito, ma può essere sottoposto a override usando le direttive `strftime`. È possibile anche anteporre i valori dell'ora con una data fissa.

Per impostazione predefinita questa trasformazione non visualizza una richiesta per il formato; usare la voce di menu **Modifica** sul passaggio risultante per richiamare l'editor. Questa trasformazione opera su una o più colonne.

Solo le date tra 9-22-1677 e 4-11-2262 possono essere convertite nel tipo data.

## <a name="convert-field-type-to-boolean"></a>Convert Field Type to Boolean (Converti tipo di campo in booleano)
Questa trasformazione modifica il tipo di colonna impostandolo su true/false. Questa trasformazione supporta il mapping di più valori a true o false ed è possibile modificare questi mapping. Supporta anche una costante su cui eseguire il mapping di valori non presenti nelle tabelle di mapping true/false. Questa trasformazione opera su una o più colonne.

## <a name="convert-field-type-to-string"></a>Convert Field Type to String (Converti tipo di campo in stringa)
Questa trasformazione modifica il tipo di colonna impostandolo su stringa. Questa trasformazione opera su una o più colonne.

## <a name="convert-unix-timestamp-to-datetime"></a>Convert Unix Timestamp to DateTime (Converti timestamp Unix in DateTime)
Questa trasformazione riconosce il formato di timestamp Unix, anche se viene rappresentato sotto forma di stringa nei dati e lo converte correttamente in un tipo di data nella preparazione dei dati.

## <a name="filter"></a>Filtro
Questa trasformazione supporta il filtro delle righe in base ai valori su una o molteplici colonne. Le condizioni del filtro dipendono dal tipo di dati di ogni colonna, rendendo possibile filtrare le colonne della stringa in base a "contiene" o "non contiene". Le colonne numeriche possono essere filtrate in base alle condizioni "maggiore di" "minore di".

Quando questa trasformazione viene chiamata dal menu principale o facendo clic con il pulsante destro del mouse sull'intestazione di una colonna, è disponibile l'opzione per dividere le righe con errore in un altro flusso di dati. Il flusso di dati principale continua quindi con le righe **in** filtrate e viene creato un nuovo flusso di dati che contiene tutte le righe **out** filtrate.

## <a name="use-first-row-as-headers"></a>Use first row as headers (Usa prima riga come intestazioni)
Questa trasformazione alza di livello la prima riga dal set di dati in modo che sia per i nomi della colonna. Se alcune colonne non dispongono di dati nella prima riga, allora viene generato automaticamente un nome. L'uso di questa trasformazione implica che l'importazione dei dati inizia non prima della riga 2. A seconda dell'impostazione che consente di ignorare righe, l'importazione potrebbe iniziare ancora più in basso nel set di dati. Può essere usata anche per rimuovere l'innalzamento di livello e usare solo nomi generati automaticamente.

## <a name="join"></a>Join
Questa trasformazione viene usata per creare un join tra due flussi di dati. È possibile selezionare l'output del join che deve essere il risultato: le righe con esito positivo dal join, le righe con errore a "sinistra" o le righe con errore a "destra" generate dal join.

La procedura guidata Join viene avviata da un singolo flusso di dati e seleziona quel flusso di dati come uno dei lati del Join. Viene quindi richiesto un altro flusso di dati per l'altro lato del Join. Dopo avere selezionato i due flussi, viene richiesto di selezionare una singola colonna su ciascun lato del join in base a cui creare il join. Se il join richiede più di una colonna, creare una colonna derivata prima di avviare la procedura guidata per creare una nuova colonna (concatenata) da usare solo per il join. La procedura guidata tenta di suggerire le chiavi del join e, se possibile, di generare automaticamente la colonna derivata.

Una volta completato il join, viene presentata una visualizzazione del diagramma Sankey del join. La larghezza delle linee in relazione l'una con l'altra rappresenta il numero di righe che si spostano all'interno di tale parte del flusso di join. Il pannello a destra consente di selezionare le righe con esito positivo, quello a sinistra con esito negativo o quelle a destra esclusivamente con esito negativo. È anche possibile scegliere solo un ramo.

## <a name="append-rows"></a>Append Rows (Accoda righe)
Questa trasformazione accorda i dati da un altro flusso di dati a quello corrente. Viene eseguito il mapping delle colonne in base alla posizione per aggiungere le nuove righe alla fine.

## <a name="append-columns"></a>Append Columns (Accoda colonne)
Questa trasformazione accorda le nuove colonne da un altro flusso di dati a quello corrente. Aggiunge le nuove colonne a destra. Non prova ad allineare i dati in righe.

## <a name="summarize"></a>Summarize (Riepiloga)
Questa trasformazione calcola le aggregazioni per la combinazione di valori univoci in una o più colonne selezionate. 

Le aggregazioni supportate sono: COUNT, SUM, MIN, MAX, MEAN, VARIANCE e STANDARD DEVIATION. L'elenco di aggregazioni per una determinata colonna viene filtrato in modo da indicare quelle che si applicano a questo tipo di dati. Le aggregazioni non applicabili sono disattivate. Ad esempio non è possibile calcolare la media di una colonna di tipo stringa, ma è possibile calcolare il valore min e max.

Dopo che l'editor è diventato disponibile, eseguire il trascinamento dall'intestazione di colonna verso l'alto nel pannello in alto a sinistra. Dove vengono visualizzate le colonne da aggregare. Questo pannello è gerarchico ed è quindi possibile eseguire aggregazioni annidate. Il pannello dell'editor in alto a destra viene usato per selezionare l'aggregazione da applicare a una colonna. Una singola colonna può essere aggregata una o più volte. Dopo avere selezionato almeno un'aggregazione, la griglia in basso a destra visualizza in anteprima i dati in forma aggregata. 

Questa trasformazione è analoga a un `ANSI-SQL GROUP BY`.

## <a name="remove-duplicates"></a>Remove Duplicates (Rimuovi duplicati)
Questa trasformazione rimuove l'intera riga in cui sono presenti valori duplicati in una o molteplici colonne selezionate. Se non vengono selezionate colonne, le uniche righe rimosse sono quelle in tutti i valori di colonna coincidono.

## <a name="sort"></a>Ordina
Questa trasformazione ordina i dati. L'ordinamento può essere eseguito in base a una singola o a molte colonne e ogni colonna può essere ordinata in modo crescente (impostazione predefinita) o decrescente (modificabile dall'editor).

Questa trasformazione è analoga a un `ANSI-SQL ORDER BY`.

## <a name="output-transforms"></a>Trasformazioni dell'output
Quanto segue trasforma i dati di output. È possibile disporre di più blocchi di scrittura in un singolo flusso per poter scrivere i dati in punti diversi.

### <a name="write-to-csv"></a>Write To CSV (Scrivi in Excel)
Questa trasformazione scrive i dati in formato CSV dal punto corrente nel flusso di dati. Consente il controllo della posizione (locale o remota) e delle varie impostazioni relative al file.

### <a name="write-to-parquet"></a>Write to Parquet (Scrivi in Parquet)
Questa trasformazione scrive i dati in formato Parquet dal punto corrente nel flusso di dati. Consente il controllo della posizione (locale o remota) e delle varie impostazioni relative al file.

## <a name="script-based-transforms"></a>Trasformazioni basate su script
Le trasformazioni seguenti usano script (Python) per eseguire le funzionalità mancanti nel prodotto principale. Prima di usare una di queste trasformazioni, leggere [Using Python extensibility](data-prep-python-extensibility-overview.md) (Uso dell'estendibilità di Python).

### <a name="add-column-script"></a>Add Column - Script (Aggiungi colonna - Script)
Questa trasformazione consente di aggiungere una colonna ai dati usando un'espressione di Python.
Per altre informazioni, vedere [Codice Python di esempio per la derivazione di nuove colonne](data-prep-appendix10-sample-custom-column-transforms-python.md).

### <a name="advanced-filter-script"></a>Advanced Filter - Script (Filtro avanzato - Script)
Questa trasformazione consente di scrivere un filtro a livello di riga Python.
Per altre informazioni, vedere [Espressioni filtro di esempio](data-prep-appendix6-sample-filter-expressions-python.md).

### <a name="transform-dataflow-script"></a>Transform Dataflow - Script (Trasforma flusso di dati - Script)
Questa trasformazione consente di applicare Python all'intero set di dati.
Per altre informazioni, vedere [Trasformazioni di esempio Trasformare il flusso di dati](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="transform-dataflow-script"></a>Transform Dataflow - Script (Trasforma flusso di dati - Script)
Questa trasformazione consente di applicare Python a un'intera partizione di dati.
Per altre informazioni, vedere [Trasformazioni di esempio Trasformare il flusso di dati](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Write Dataflow - Script (Scrivi flusso di dati - Script)
Questa trasformazione consente di applicare Python alla scrittura di un intero set di dati.
Per altre informazioni, vedere [Python di esempio per la derivazione di nuove colonne](data-prep-appendix9-sample-destination-connections-python.md).



