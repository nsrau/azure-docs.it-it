---
title: Trasformazione origine nel flusso di dati di mapping
description: Informazioni su come configurare una trasformazione di origine nel flusso di dati di mapping.
author: kromerm
ms.author: makromer
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: 128b15bd5b3ba3c3ac891719bf5c3ec8e5137cce
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023515"
---
# <a name="source-transformation-in-mapping-data-flow"></a>Trasformazione origine nel flusso di dati di mapping 

Una trasformazione origine configura l'origine dati per il flusso di dati. Quando si progettano i flussi di dati, il primo passaggio consiste sempre nella configurazione di una trasformazione di origine. Per aggiungere un'origine, fare clic sulla casella **Aggiungi origine** nell'area di disegno del flusso di dati.

Ogni flusso di dati richiede almeno una trasformazione di origine, ma è possibile aggiungere tutte le origini necessarie per completare le trasformazioni dei dati. È possibile unire tali origini insieme a una trasformazione join, Lookup o Union.

Ogni trasformazione di origine è associata a un solo set di dati Data Factory. Il set di dati definisce la forma e il percorso dei dati da cui si desidera eseguire la scrittura o la lettura. Se si usa un set di dati basato su file, è possibile usare i caratteri jolly e gli elenchi di file nell'origine per lavorare con più di un file alla volta.

## <a name="supported-source-connectors-in-mapping-data-flow"></a>Connettori di origine supportati nel flusso di dati di mapping

Il flusso di dati di mapping segue un approccio di estrazione, caricamento e trasformazione (ELT) e funziona con i set di dati di *staging* che sono tutti in Azure. Attualmente i set di dati seguenti possono essere utilizzati in una trasformazione di origine:
    
* [Archiviazione BLOB di Azure](connector-azure-blob-storage.md#mapping-data-flow-properties) (JSON, avro, testo, parquet)
* [Azure Data Lake storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) (JSON, avro, testo, parquet)
* [Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) (JSON, avro, testo, parquet)
* [Analisi delle sinapsi di Azure](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties)
* [Database SQL di Azure](connector-azure-sql-database.md#mapping-data-flow-properties)
* [Azure CosmosDB](connector-azure-cosmos-db.md#mapping-data-flow-properties)

Le impostazioni specifiche di questi connettori si trovano nella scheda **Opzioni di origine** . le informazioni su queste impostazioni sono disponibili nella documentazione del connettore. 

Azure Data Factory ha accesso a oltre [90 connettori nativi](connector-overview.md). Per includere dati da tali origini nel flusso di dati, usare l'attività di copia per caricare i dati in una delle aree di gestione temporanea supportate.

## <a name="source-settings"></a>Impostazioni origine

Dopo aver aggiunto un'origine, configurare tramite la scheda **impostazioni di origine** . Qui è possibile selezionare o creare il set di dati a cui si riportano i punti di origine. È anche possibile selezionare le opzioni relative allo schema e al campionamento per i dati.

![Scheda Impostazioni di origine](media/data-flow/source1.png "Scheda Impostazioni di origine")

**Test connessione:** Verificare se il servizio Spark del flusso di dati è in grado di connettersi al servizio collegato usato nel set di dati di origine. Per abilitare questa funzionalità, è necessario attivare la modalità di debug.

**Schema Drift:** la [deriva dello schema](concepts-data-flow-schema-drift.md) è data factory capacità di gestire in modo nativo schemi flessibili nei flussi di dati senza dover definire in modo esplicito le modifiche apportate alle colonne.

* Controllare la casella **Consenti la deviazione dello schema** se le colonne di origine vengono modificate spesso. Questa impostazione consente a tutti i campi di origine in ingresso di scorrere le trasformazioni nel sink.

* La scelta di **dedurre i tipi di colonna** derivati indicherà data factory per rilevare e definire i tipi di dati per ogni nuova colonna individuata. Se questa funzionalità è disattivata, tutte le colonne in sequenza saranno di tipo stringa.

**Convalida schema:** Se Convalida schema è selezionata, il flusso di dati non verrà eseguito se i dati di origine in ingresso non corrispondono allo schema definito del set di dati.

**Ignora conteggio righe:** Il campo Skip line count specifica il numero di righe da ignorare all'inizio del set di dati.

**Campionamento:** Abilitare il campionamento per limitare il numero di righe dall'origine. Usare questa impostazione quando si testano o si campionano i dati dall'origine a scopo di debug.

**Righe su più righe:** Selezionare righe su più righe se il file di testo di origine contiene valori stringa che si estendono su più righe, ad esempio le nuove righe all'interno di un valore. Questa impostazione è disponibile solo nei set di impostazioni DelimitedText.

Per convalidare che l'origine sia configurata correttamente, attivare la modalità di debug e recuperare un'anteprima dei dati. Per altre informazioni, vedere [modalità di debug](concepts-data-flow-debug-mode.md).

> [!NOTE]
> Quando la modalità di debug è attivata, la configurazione del limite di righe nelle impostazioni di debug sovrascriverà l'impostazione di campionamento nell'origine durante l'anteprima dei dati.

## <a name="projection"></a>Proiezione

Analogamente agli schemi nei set di dati, la proiezione in un'origine definisce le colonne di dati, i tipi e i formati dei dati di origine. Per la maggior parte dei tipi di set di dati, ad esempio SQL e parquet, la proiezione in un'origine è fissa per riflettere lo schema definito in un set di dati. Quando i file di origine non sono fortemente tipizzati, ad esempio file con estensione CSV flat anziché file parquet, è possibile definire i tipi di dati per ogni campo nella trasformazione di origine.

![Impostazioni nella scheda proiezione](media/data-flow/source3.png "Proiezione")

Se nel file di testo non è definito alcuno schema, selezionare **rileva tipo di dati** in modo che data factory campionare e dedurre i tipi di dati. Selezionare **Definisci il formato predefinito** per rilevare automaticamente i formati di dati predefiniti.

**Reimposta schema Reimposta** la proiezione su ciò che viene definito nel set di dati a cui si fa riferimento.

È possibile modificare i tipi di dati delle colonne in una trasformazione di colonna derivata da un flusso inattivo. Utilizzare una trasformazione seleziona per modificare i nomi delle colonne.

### <a name="import-schema"></a>Importa schema

Il pulsante **Importa schema** nella scheda **proiezione** consente di usare un cluster di debug attivo per creare una proiezione dello schema. Disponibile in ogni tipo di origine, l'importazione dello schema qui sostituirà la proiezione definita nel set di dati. L'oggetto DataSet non verrà modificato.

Questa operazione è utile nei set di dati, ad esempio Avro e CosmosDB che supportano strutture di dati complesse, che non richiedono la presenza di definizioni dello schema nel set di dati.

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
