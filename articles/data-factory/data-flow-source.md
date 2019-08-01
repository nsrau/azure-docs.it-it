---
title: Configurare una trasformazione origine nella funzionalità flusso di dati di mapping di Azure Data Factory
description: Informazioni su come configurare una trasformazione di origine nel flusso di dati di mapping.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 974ece9cd035ae29ada38f34b7933d86f682194f
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68696226"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Trasformazione origine per il mapping del flusso di dati 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Una trasformazione origine configura l'origine dati per il flusso di dati. Un flusso di dati può includere più di una trasformazione di origine. Quando si progettano i flussi di dati, iniziare sempre con una trasformazione di origine.

Ogni flusso di dati richiede almeno una trasformazione di origine. Aggiungere tutte le origini necessarie per completare le trasformazioni dei dati. È possibile unire in join tali origini insieme a una trasformazione join o a un'Unione.

> [!NOTE]
> Quando si esegue il debug del flusso di dati, i dati vengono letti dall'origine utilizzando l'impostazione di campionamento o i limiti dell'origine di debug. Per scrivere dati in un sink, è necessario eseguire il flusso di dati da un'attività flusso di dati della pipeline. 

![Opzioni di trasformazione origine nella scheda Impostazioni di origine](media/data-flow/source.png "origine") dati

Associare la trasformazione origine flusso di dati a un solo set di dati Data Factory. Il set di dati definisce la forma e il percorso dei dati da cui si desidera eseguire la scrittura o la lettura. È possibile usare i caratteri jolly e gli elenchi di file nell'origine per lavorare con più di un file alla volta.

L'uso di un'opzione di **modello con caratteri jolly** indicherà ad ADF di scorrere ogni cartella e file corrispondente in un'unica trasformazione origine. Si tratta di un modo molto efficace per elaborare più file all'interno di un singolo flusso. Per tenere traccia del nome del file attualmente in fase di elaborazione, impostare un nome di campo per il campo "colonna per l'archiviazione del nome file" nelle opzioni di origine.

> [!NOTE]
> Impostare più modelli di corrispondenza con caratteri jolly con il segno + accanto al modello con caratteri jolly esistente per aggiungere altre regole jolly.

## <a name="data-flow-staging-areas"></a>Aree di gestione temporanea del flusso di dati
Il flusso di dati funziona con i set di dati di *staging* che sono tutti in Azure. Usare questi set di dati per la gestione temporanea quando si trasformano i dati. 

Data Factory ha accesso a quasi 80 connettori nativi. Per includere dati da tali origini nel flusso di dati, utilizzare lo strumento attività di copia per organizzare i dati in una delle aree di gestione temporanea del set di dati del flusso di dati.

## <a name="options"></a>Opzioni

Scegliere le opzioni di schema e campionamento per i dati.

### <a name="schema-drift"></a>Deviazione schema
[Schema Drift](concepts-data-flow-schema-drift.md) è la capacità di ADF di gestire in modo nativo gli schemi flessibili nei flussi di dati senza dover definire in modo esplicito le modifiche apportate alle colonne.

* Selezionare **Consenti la deriva dello schema** se le colonne di origine vengono modificate spesso. Questa impostazione consente a tutti i campi di origine in ingresso di scorrere le trasformazioni nel sink.

* La scelta di dedurre i **tipi di colonna** derivati indicherà ad ADF di definire i tipi di dati per ogni nuova colonna individuata Se questa funzionalità è disattivata, ADF presuppone una stringa.

### <a name="validate-schema"></a>Convalida schema

Se la versione in ingresso dei dati di origine non corrisponde allo schema definito, il flusso di dati non verrà eseguito.

![Impostazioni dell'origine pubblica, che mostrano le opzioni per convalidare lo schema, consentire la deriva dello schema e il campionamento](media/data-flow/source1.png "origine pubblica 1")

### <a name="sample-the-data"></a>Campionare i dati
Abilitare il campionamento per limitare il numero di righe dall'origine. Usare questa impostazione quando si testano o si campionano i dati dall'origine a scopo di debug.

## <a name="define-schema"></a>Definire lo schema

Quando i file di origine non sono fortemente tipizzati, ad esempio file flat anziché file parquet, definire i tipi di dati per ogni campo nella trasformazione di origine.  

![Impostazioni di trasformazione origine nella scheda Definisci schema](media/data-flow/source2.png "origine 2")

In un secondo momento è possibile modificare i nomi delle colonne in una trasformazione Select. Utilizzare una trasformazione colonna derivata per modificare i tipi di dati. Per le origini fortemente tipizzate, è possibile modificare i tipi di dati in una trasformazione SELECT successiva. 

![Tipi di dati in una trasformazione Select](media/data-flow/source003.png "tipi di dati")

### <a name="optimize-the-source-transformation"></a>Ottimizzare la trasformazione di origine

Nella scheda **ottimizza** per la trasformazione origine è possibile che venga visualizzato un tipo di partizione di **origine** . Questa opzione è disponibile solo quando l'origine è il database SQL di Azure. Questo perché Data Factory tenta di rendere le connessioni parallele per eseguire query di grandi dimensioni sull'origine del database SQL.

![Impostazioni partizione di origine](media/data-flow/sourcepart3.png "partizionamento")

Non è necessario partizionare i dati nell'origine del database SQL, ma le partizioni sono utili per le query di grandi dimensioni. È possibile basare la partizione su una colonna o su una query.

### <a name="use-a-column-to-partition-data"></a>Usare una colonna per partizionare i dati

Dalla tabella di origine selezionare una colonna in base alla quale eseguire la partizione. Impostare anche il numero di partizioni.

### <a name="use-a-query-to-partition-data"></a>Usare una query per partizionare i dati

È possibile scegliere di partizionare le connessioni in base a una query. È sufficiente immettere il contenuto di un predicato WHERE. Immettere, ad esempio, anno > 1980.

## <a name="source-file-management"></a>Gestione file di origine

Scegliere impostazioni per gestire i file nell'origine. 

![Nuove impostazioni di origine](media/data-flow/source2.png "Nuove impostazioni")

* **Percorso con caratteri jolly**: Dal contenitore di origine, scegliere una serie di file che corrispondono a un modello. Questa impostazione esegue l'override di qualsiasi file nella definizione del set di dati.

Esempi di caratteri jolly:

* ```*```Rappresenta qualsiasi set di caratteri
* ```**```Rappresenta l'annidamento delle directory ricorsive
* ```?```Sostituisce un carattere
* ```[]```Trova la corrispondenza di uno o più caratteri tra parentesi quadre

* ```/data/sales/**/*.csv```Ottiene tutti i file CSV in/data/Sales
* ```/data/sales/20??/**```Ottiene tutti i file nel ventesimo secolo
* ```/data/sales/2004/*/12/[XY]1?.csv```Ottiene tutti i file CSV in 2004 nel dicembre a partire da X o Y preceduto da un numero a 2 cifre

Il contenitore deve essere specificato nel set di dati. Il percorso del carattere jolly deve quindi includere anche il percorso della cartella nella cartella radice.

* **Percorso radice partizione**: Se sono presenti cartelle partizionate nell'origine file di un ```key=value``` formato (ad esempio, anno = 2019), è possibile chiedere ad ADF di assegnare il primo livello dell'albero delle cartelle della partizione a un nome di colonna nel flusso di dati del flusso di dati.

Per prima cosa, impostare un carattere jolly per includere tutti i percorsi che rappresentano le cartelle partizionate e i file foglia che si desidera leggere.

![Impostazioni del file di origine della partizione](media/data-flow/partfile2.png "Impostazione del file di partizione")

Usare ora l'impostazione percorso radice partizione per indicare ad ADF qual è il livello superiore della struttura di cartelle. A questo punto, quando si Visualizza il contenuto dei dati, si noterà che ADF aggiungerà le partizioni risolte presenti in ogni livello di cartella.

![Percorso radice partizione](media/data-flow/partfile1.png "Anteprima percorso radice partizione")

* **Elenco di file**: Si tratta di un set di file. Creare un file di testo che includa un elenco di file di percorso relativi da elaborare. Puntare a questo file di testo.
* **Colonna in cui archiviare il nome del file**: Archiviare il nome del file di origine in una colonna nei dati. Immettere un nuovo nome per archiviare la stringa del nome file.
* **Dopo il completamento**: Scegliere di non eseguire alcuna operazione con il file di origine dopo l'esecuzione del flusso di dati, eliminare il file di origine oppure spostare il file di origine. I percorsi per lo spostamento sono relativi.

Per spostare i file di origine in un altro percorso dopo l'elaborazione, selezionare prima di tutto "Sposta" per operazione su file. Quindi, impostare la directory "from". Se non si usano caratteri jolly per il percorso, l'impostazione "da" sarà la stessa cartella della cartella di origine.

Se si dispone di un percorso di origine con carattere jolly, la sintassi avrà un aspetto simile al seguente:

```/data/sales/20??/**/*.csv```

È possibile specificare "from" come

```/data/sales```

E "a" come

```/backup/priorSales```

In questo caso, tutti i file originati in/data/Sales vengono spostati in/backup/priorSales.

### <a name="sql-datasets"></a>Set di impostazioni SQL

Se l'origine si trova nel database SQL o in SQL Data Warehouse, sono disponibili opzioni aggiuntive per la gestione dei file di origine.

* **Query**: Immettere una query SQL per l'origine. Questa impostazione esegue l'override di qualsiasi tabella scelta nel set di dati. Si noti che le clausole **Order by** non sono supportate in questo punto, ma è possibile impostare un'istruzione SELECT from completa. È anche possibile usare funzioni di tabella definite dall'utente. **Select * from udfGetData ()** è una funzione definita dall'utente in SQL che restituisce una tabella. Questa query produrrà una tabella di origine che è possibile utilizzare nel flusso di dati.
* **Dimensioni batch**: Immettere le dimensioni del batch per suddividere i dati di grandi dimensioni in letture.
* **Livello di isolamento**: Il valore predefinito per le origini SQL nei flussi di dati di mapping di ADF è READ UNCOMMITTED. È possibile modificare il livello di isolamento in uno dei seguenti valori:
* Lettura eseguita
* Lettura non eseguita
* Lettura ripetibile
* Serializzabile
* Nessuno (ignora il livello di isolamento)

![Livello di isolamento](media/data-flow/isolationlevel.png "Livello di isolamento")

> [!NOTE]
> Le operazioni sui file vengono eseguite solo quando si avvia il flusso di dati da un'esecuzione di pipeline (esecuzione del debug o esecuzione di una pipeline) che utilizza l'attività Esegui flusso di dati in una pipeline. Le operazioni sui file *non* vengono eseguite nella modalità di debug del flusso di dati.

### <a name="projection"></a>Proiezione

Analogamente agli schemi nei set di dati, la proiezione in un'origine definisce le colonne di dati, i tipi e i formati dei dati di origine. 

![Impostazioni nella scheda proiezione](media/data-flow/source3.png "Proiezione")

Se nel file di testo non è definito alcuno schema, selezionare **rileva tipo di dati** in modo che data factory campionare e dedurre i tipi di dati. Selezionare **Definisci il formato predefinito** per rilevare automaticamente i formati di dati predefiniti. 

È possibile modificare i tipi di dati delle colonne in una trasformazione colonna derivata successiva. Utilizzare una trasformazione seleziona per modificare i nomi delle colonne.

![Impostazioni per i formati di dati predefiniti](media/data-flow/source2.png "Formati predefiniti")

### <a name="add-dynamic-content"></a>Aggiungi contenuto dinamico
Quando si fa clic all'interno dei campi nel pannello delle impostazioni, viene visualizzato un collegamento ipertestuale per "Aggiungi contenuto dinamico". Quando si sceglie di avviare Generatore di espressioni, i valori vengono impostati in modo dinamico tramite espressioni, valori letterali statici o parametri.

![Parametri] di (media/data-flow/params6.png "Parametri") di

## <a name="next-steps"></a>Passaggi successivi

Inizia la compilazione di una [trasformazione colonna derivata](data-flow-derived-column.md) e una [trasformazione selezione](data-flow-select.md).
