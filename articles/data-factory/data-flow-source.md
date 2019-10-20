---
title: Trasformazione origine nel flusso di dati di mapping-Azure Data Factory | Microsoft Docs
description: Informazioni su come configurare una trasformazione di origine nel flusso di dati di mapping.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: c7d18ab6e9018511915e9b77ea02ac60b1277c12
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596488"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Trasformazione origine per il mapping del flusso di dati 



Una trasformazione origine configura l'origine dati per il flusso di dati. Quando si progettano i flussi di dati, il primo passaggio consiste sempre nella configurazione di una trasformazione di origine. Per aggiungere un'origine, fare clic sulla casella **Aggiungi origine** nell'area di disegno del flusso di dati.

Ogni flusso di dati richiede almeno una trasformazione di origine, ma è possibile aggiungere tutte le origini necessarie per completare le trasformazioni dei dati. È possibile unire tali origini insieme a una trasformazione join, Lookup o Union.

Ogni trasformazione di origine è associata a un solo set di dati Data Factory. Il set di dati definisce la forma e il percorso dei dati da cui si desidera eseguire la scrittura o la lettura. Se si usa un set di dati basato su file, è possibile usare i caratteri jolly e gli elenchi di file nell'origine per lavorare con più di un file alla volta.

## <a name="supported-connectors-in-mapping-data-flow"></a>Connettori supportati nel flusso di dati di mapping

Il flusso di dati di mapping segue un approccio di estrazione, caricamento e trasformazione (ELT) e funziona con i set di dati di *staging* che sono tutti in Azure. Attualmente i set di dati seguenti possono essere utilizzati in una trasformazione di origine:
    
* Archiviazione BLOB di Azure
* Archiviazione Azure Data Lake di prima generazione
* Azure Data Lake Storage Gen2
* SQL Data Warehouse di Azure
* database SQL di Azure

Azure Data Factory ha accesso a oltre 80 connettori nativi. Per includere dati da tali origini nel flusso di dati, usare l'attività di copia per caricare i dati in una delle aree di gestione temporanea supportate.

## <a name="source-settings"></a>Impostazioni origine

Dopo aver aggiunto un'origine, configurare tramite la scheda **impostazioni di origine** . Qui è possibile selezionare o creare il set di dati a cui si riportano i punti di origine. È anche possibile selezionare le opzioni relative allo schema e al campionamento per i dati.

![Scheda Impostazioni di origine](media/data-flow/source1.png "Scheda Impostazioni di origine")

**Schema Drift:** la [deriva dello schema](concepts-data-flow-schema-drift.md) è data factory capacità di gestire in modo nativo schemi flessibili nei flussi di dati senza dover definire in modo esplicito le modifiche apportate alle colonne.

* Controllare la casella **Consenti la deviazione dello schema** se le colonne di origine vengono modificate spesso. Questa impostazione consente a tutti i campi di origine in ingresso di scorrere le trasformazioni nel sink.

* La scelta di **dedurre i tipi di colonna** derivati indicherà data factory per rilevare e definire i tipi di dati per ogni nuova colonna individuata. Se questa funzionalità è disattivata, tutte le colonne in sequenza saranno di tipo stringa.

**Convalida schema:** Se Convalida schema è selezionata, il flusso di dati non verrà eseguito se i dati di origine in ingresso non corrispondono allo schema definito del set di dati.

**Ignora conteggio righe:** Il campo Skip line count specifica il numero di righe da ignorare all'inizio del set di dati.

**Campionamento:** Abilitare il campionamento per limitare il numero di righe dall'origine. Usare questa impostazione quando si testano o si campionano i dati dall'origine a scopo di debug.

**Righe su più righe:** Selezionare righe su più righe se il file di testo di origine contiene valori stringa che si estendono su più righe, ad esempio le nuove righe all'interno di un valore.

Per convalidare che l'origine sia configurata correttamente, attivare la modalità di debug e recuperare un'anteprima dei dati. Per altre informazioni, vedere [modalità di debug](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Quando la modalità di debug è attivata, la configurazione del limite di righe nelle impostazioni di debug sovrascriverà l'impostazione di campionamento nell'origine durante l'anteprima dei dati.

## <a name="file-based-source-options"></a>Opzioni dell'origine basata su file

Se si usa un set di dati basato su file, ad esempio archiviazione BLOB di Azure o Azure Data Lake Storage, la scheda **Opzioni di origine** consente di gestire il modo in cui l'origine legge i file.

![Opzioni di origine](media/data-flow/sourceOPtions1.png "Opzioni di origine")

**Percorso con caratteri jolly:** L'utilizzo di un modello con caratteri jolly indicherà ad ADF di scorrere ogni cartella e file corrispondente in un'unica trasformazione origine. Si tratta di un modo efficace per elaborare più file all'interno di un singolo flusso. Aggiungere più modelli di corrispondenza con caratteri jolly con il segno + visualizzato quando si passa il mouse sul modello con caratteri jolly esistente.

Dal contenitore di origine, scegliere una serie di file che corrispondono a un modello. Nel set di dati è possibile specificare solo il contenitore. Il percorso del carattere jolly deve quindi includere anche il percorso della cartella nella cartella radice.

Esempi di caratteri jolly:

* ```*``` rappresenta qualsiasi set di caratteri
* ```**``` rappresenta l'annidamento delle directory ricorsive
* ```?``` sostituisce un carattere
* ```[]``` corrisponde a uno o più caratteri tra parentesi quadre

* ```/data/sales/**/*.csv``` Ottiene tutti i file CSV in/data/Sales
* ```/data/sales/20??/**``` Ottiene tutti i file nel ventesimo secolo
* ```/data/sales/2004/*/12/[XY]1?.csv``` ottiene tutti i file CSV 2004 nel dicembre a partire da X o Y preceduto da un numero a due cifre

**Percorso radice partizione:** Se nell'origine file sono presenti cartelle partizionate con un formato ```key=value``` (ad esempio, Year = 2019), è possibile assegnare il livello principale dell'albero delle cartelle della partizione a un nome di colonna nel flusso di dati del flusso di dati.

Per prima cosa, impostare un carattere jolly per includere tutti i percorsi che rappresentano le cartelle partizionate e i file foglia che si desidera leggere.

![Impostazioni del file di origine della partizione](media/data-flow/partfile2.png "Impostazione del file di partizione")

Utilizzare l'impostazione percorso radice partizione per definire il livello superiore della struttura di cartelle. Quando si Visualizza il contenuto dei dati tramite un'anteprima dei dati, si noterà che ADF aggiungerà le partizioni risolte presenti in ogni livello di cartella.

![Percorso radice partizione](media/data-flow/partfile1.png "Anteprima percorso radice partizione")

**Elenco di file:** Si tratta di un set di file. Creare un file di testo che includa un elenco di file di percorso relativi da elaborare. Puntare a questo file di testo.

**Colonna in cui archiviare il nome del file:** Archiviare il nome del file di origine in una colonna nei dati. Immettere qui il nome di una nuova colonna per archiviare la stringa del nome file.

**Dopo il completamento:** Scegliere di non eseguire alcuna operazione con il file di origine dopo l'esecuzione del flusso di dati, eliminare il file di origine oppure spostare il file di origine. I percorsi per lo spostamento sono relativi.

Per spostare i file di origine in un altro percorso dopo l'elaborazione, selezionare prima di tutto "Sposta" per operazione su file. Quindi, impostare la directory "from". Se non si usano caratteri jolly per il percorso, l'impostazione "da" sarà la stessa cartella della cartella di origine.

Se si dispone di un percorso di origine con carattere jolly, la sintassi avrà un aspetto simile al seguente:

```/data/sales/20??/**/*.csv```

È possibile specificare "from" come

```/data/sales```

E "a" come

```/backup/priorSales```

In questo caso, tutti i file originati in/data/Sales vengono spostati in/backup/priorSales.

> [!NOTE]
> Le operazioni sui file vengono eseguite solo quando si avvia il flusso di dati da un'esecuzione di pipeline (esecuzione del debug o esecuzione di una pipeline) che utilizza l'attività Esegui flusso di dati in una pipeline. Le operazioni sui file *non* vengono eseguite nella modalità di debug del flusso di dati.

**Filtra per Ultima modifica:** È possibile filtrare i file elaborati specificando un intervallo di date di data e ora dell'Ultima modifica. Tutte le ore di data sono in formato UTC. 

### <a name="add-dynamic-content"></a>Aggiungere contenuto dinamico

È possibile specificare tutte le impostazioni di origine come espressioni utilizzando il [linguaggio delle espressioni di trasformazione del flusso di dati di mapping](data-flow-expression-functions.md). Per aggiungere contenuto dinamico, fare clic o passare il puntatore del mouse all'interno dei campi nel pannello impostazioni. Fare clic sul collegamento ipertestuale per **Aggiungi contenuto dinamico**. Verrà avviato il generatore di espressioni in cui è possibile impostare i valori in modo dinamico tramite espressioni, valori letterali statici o parametri.

![Parameters](media/data-flow/params6.png "parameters")

## <a name="sql-source-options"></a>Opzioni origine SQL

Se l'origine si trova nel database SQL o in SQL Data Warehouse, nella scheda **Opzioni di origine** sono disponibili altre impostazioni specifiche di SQL. 

**Input:** Consente di scegliere se puntare l'origine a una tabella (equivalente a ```Select * from <table-name>```) o immettere una query SQL personalizzata.

**Query**: se si seleziona query nel campo di input, immettere una query SQL per l'origine. Questa impostazione esegue l'override di qualsiasi tabella scelta nel set di dati. Le clausole **Order by** non sono supportate in questo punto, ma è possibile impostare un'istruzione SELECT from completa. È anche possibile usare funzioni di tabella definite dall'utente. **Select * from udfGetData ()** è una funzione definita dall'utente in SQL che restituisce una tabella. Questa query produrrà una tabella di origine che è possibile utilizzare nel flusso di dati.

**Dimensioni batch**: immettere le dimensioni del batch per suddividere i dati di grandi dimensioni in letture.

**Livello di isolamento**: il valore predefinito per le origini SQL nel flusso di dati di mapping è READ UNCOMMITTED. È possibile modificare il livello di isolamento in uno dei seguenti valori:
* Read Committed
* Read uncommitted
* Repeatable Read
* Serializzabile
* Nessuno (ignora il livello di isolamento)

![Livello di isolamento](media/data-flow/isolationlevel.png "Livello di isolamento")

## <a name="projection"></a>Proiezione

Analogamente agli schemi nei set di dati, la proiezione in un'origine definisce le colonne di dati, i tipi e i formati dei dati di origine. Per la maggior parte dei tipi di set di dati, ad esempio SQL e parquet, la proiezione in un'origine è fissa per riflettere lo schema definito in un set di dati. Quando i file di origine non sono fortemente tipizzati, ad esempio file con estensione CSV flat anziché file parquet, è possibile definire i tipi di dati per ogni campo nella trasformazione di origine.

![Impostazioni nella scheda proiezione](media/data-flow/source3.png "Proiezione")

Se nel file di testo non è definito alcuno schema, selezionare **rileva tipo di dati** in modo che data factory campionare e dedurre i tipi di dati. Selezionare **Definisci il formato predefinito** per rilevare automaticamente i formati di dati predefiniti. 

È possibile modificare i tipi di dati delle colonne in una trasformazione di colonna derivata da un flusso inattivo. Utilizzare una trasformazione seleziona per modificare i nomi delle colonne.

## <a name="optimize-the-source-transformation"></a>Ottimizzare la trasformazione di origine

Nella scheda **ottimizza** per la trasformazione origine è possibile che venga visualizzato un tipo di partizione di **origine** . Questa opzione è disponibile solo quando l'origine è il database SQL di Azure. Questo perché Data Factory tenta di rendere le connessioni parallele per eseguire query di grandi dimensioni sull'origine del database SQL.

![Impostazioni partizione di origine](media/data-flow/sourcepart3.png "partizionamento")

Non è necessario partizionare i dati nell'origine del database SQL, ma le partizioni sono utili per le query di grandi dimensioni. È possibile basare la partizione su una colonna o su una query.

### <a name="use-a-column-to-partition-data"></a>Usare una colonna per partizionare i dati

Dalla tabella di origine selezionare una colonna in base alla quale eseguire la partizione. Impostare anche il numero di partizioni.

### <a name="use-a-query-to-partition-data"></a>Usare una query per partizionare i dati

È possibile scegliere di partizionare le connessioni in base a una query. Immettere il contenuto di un predicato WHERE. Immettere, ad esempio, anno > 1980.

Per ulteriori informazioni sull'ottimizzazione all'interno del flusso di dati di mapping, vedere la [scheda Optimize](concepts-data-flow-overview.md#optimize).

## <a name="next-steps"></a>Passaggi successivi

Inizia la compilazione di una [trasformazione colonna derivata](data-flow-derived-column.md) e una [trasformazione selezione](data-flow-select.md).
