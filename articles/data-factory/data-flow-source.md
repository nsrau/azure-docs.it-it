---
title: Configurare una trasformazione di origine nella funzionalità di Mapping del flusso di dati di Azure Data Factory
description: Informazioni su come configurare una trasformazione di origine nel flusso di dati di Mapping.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: dc0a6e008c7a1f4fb414f6d8adad3a94abc7a6b2
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65792353"
---
# <a name="source-transformation-for-mapping-data-flow"></a>Trasformazione di origine per il Mapping del flusso di dati 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Una trasformazione origine consente di configurare l'origine dati per il flusso di dati. Un flusso di dati può includere più di una trasformazione di origine. Durante la progettazione dei dati viene trasmesso, iniziano sempre con una trasformazione di origine.

Ogni flusso di dati richiede la trasformazione di almeno un'origine. Aggiungere origini tante quante sono necessarie per completare le trasformazioni di dati. È possibile aggiungere tali origini con una trasformazione di join o in una trasformazione unione.

> [!NOTE]
> Quando si esegue il debug del flusso di dati, i dati vengono letti dall'origine utilizzando l'impostazione di campionamento o i limiti di origine di debug. Per scrivere i dati a un sink, è necessario eseguire il flusso di dati da una pipeline di attività del flusso di dati. 

![Opzioni di trasformazione nella scheda Impostazioni di origine di origine](media/data-flow/source.png "origine")

Associare la trasformazione di origine del flusso di dati con un solo set di dati di Data Factory. Il set di dati definisce la forma e la posizione dei dati che si desidera scrivere o leggere dal. È possibile usare gli elenchi di file e i caratteri jolly nell'origine per lavorare con più di un file alla volta.

## <a name="data-flow-staging-areas"></a>Aree di gestione temporanea del flusso di dati

Flusso di dati funziona con *staging* i set di dati in Azure. Usare questi set di dati per la gestione temporanea quando si eseguono la trasformazione dei dati. 

Data Factory può accedere a circa 80 connettori nativi. Per includere dati da tali altre origini nel flusso di dati, utilizzare lo strumento di attività di copia per inserire temporaneamente i dati in una delle aree di gestione temporanea di set di dati del flusso di dati.

## <a name="options"></a>Opzioni

Scegliere le opzioni dello schema e il campionamento dei dati.

### <a name="allow-schema-drift"></a>Permettere la deviazione schema
Selezionare **consentire deviazione schema** se le colonne di origine verranno modificato spesso. Questa impostazione consente tutti i campi di origine in ingresso a fluire attraverso le trasformazioni al sink.

### <a name="validate-schema"></a>Convalida schema

Se la versione dei dati di origine in ingresso non corrisponde allo schema definito, il flusso di dati avrà esito negativo per l'esecuzione.

![Le impostazioni di origine pubblica, che illustra le opzioni per lo schema di convalida, la deviazione schema di Consenti e campionamento](media/data-flow/source1.png "1 di origine pubblica")

### <a name="sample-the-data"></a>I dati di esempio
Abilitare **campionamento** per limitare il numero di righe dall'origine. Usare questa impostazione durante il test o dati di esempio dall'origine a scopo di debug.

## <a name="define-schema"></a>Definizione di schema

Quando i file di origine non sono fortemente tipizzati (ad esempio, file flat anziché file Parquet), definire i tipi di dati per ogni campo qui nella trasformazione origine.  

![Le impostazioni di trasformazione nella scheda definizione dello schema di origine](media/data-flow/source2.png "2 di origine")

In un secondo momento, è possibile modificare i nomi delle colonne in una trasformazione Seleziona. Utilizzare una trasformazione colonna derivata per modificare i tipi di dati. Per le origini fortemente tipizzate, è possibile modificare i tipi di dati in una trasformazione seleziona in un secondo momento. 

![I tipi di dati in una trasformazione seleziona](media/data-flow/source003.png "i tipi di dati")

### <a name="optimize-the-source-transformation"></a>Ottimizzare la trasformazione di origine

Nel **Ottimizza** scheda per la trasformazione di origine, è possibile visualizzare un **origine** tipo di partizione. Questa opzione è disponibile solo quando l'origine è il Database SQL di Azure. Si tratta in quanto il tentativo di stabilire connessioni parallele per eseguire query di grandi dimensioni di origine del Database SQL di Data Factory.

![Le impostazioni della partizione di origine](media/data-flow/sourcepart2.png "partizionamento")

Non è necessario partizionare i dati nell'origine Database SQL, ma le partizioni sono utili per query di grandi dimensioni. È possibile basare la partizione in una colonna o una query.

### <a name="use-a-column-to-partition-data"></a>Utilizzare una colonna di partizionamento dei dati

Dalla tabella di origine, selezionare una colonna di partizione su. Impostare anche il numero massimo di connessioni.

### <a name="use-a-query-to-partition-data"></a>Usare una query per partizionare i dati

È possibile scegliere di partizionare le connessioni basate su una query. È sufficiente immettere il contenuto di un predicato WHERE. Immettere ad esempio, anno 1980 >.

## <a name="source-file-management"></a>Gestione file di origine

Scegliere le impostazioni per gestire i file nell'origine. 

![Nuove impostazioni di origine](media/data-flow/source2.png "nuove impostazioni")

* **Percorso con caratteri jolly**: La cartella di origine, scegliere una serie di file che corrispondono ai criteri. Questa impostazione esegue l'override di qualsiasi file nella definizione del set di dati.
* **Elenco di file**: Si tratta di un set di file. Creare un file di testo che include un elenco di file di percorso relativo per l'elaborazione. Puntare a questo file di testo.
* **Colonna per archiviare il nome di file**: Store il nome del file di origine in una colonna nei dati. Immettere un nuovo nome per archiviare la stringa del nome file.
* **Dopo il completamento**: Scegliere di non eseguire alcuna operazione con il file di origine dopo che i dati di esecuzioni dei flussi, eliminare il file di origine o spostare il file di origine. I percorsi per lo spostamento sono relativi.

### <a name="sql-datasets"></a>Set di dati SQL

Se l'origine è nel Database SQL o SQL Data Warehouse, sono disponibili opzioni aggiuntive per la gestione di file di origine.

* **Query**: Immettere una query SQL per l'origine. Questa impostazione esegue l'override di qualsiasi tabella a cui che si è scelto nel set di dati. Si noti che **Order By** clausole non sono supportate in questo caso. Ma è possibile impostare qui un'istruzione SELECT FROM completa.
* **Dimensioni batch**: Immettere le dimensioni del batch per suddividere i dati di grandi dimensioni in operazioni di lettura.

> [!NOTE]
> File operazioni vengono eseguite solo quando si avvia il flusso di dati da un'esecuzione (debug di pipeline o esecuzione) della pipeline che usa l'attività di esecuzione del flusso di dati in una pipeline. Operazioni sui file *non li* eseguito in modalità di debug del flusso di dati.

### <a name="projection"></a>Proiezione

Ad esempio gli schemi nel set di dati, la proiezione in un'origine definisce le colonne di dati, tipi e formati di dati di origine. 

![Le impostazioni della scheda proiezione](media/data-flow/source3.png "proiezione")

Se il file di testo non ha uno schema definito, selezionare **rileva tipo di dati** in modo che Data Factory verrà di esempio e in grado di dedurre i tipi di dati. Selezionare **formato predefinito Definisci** per rileva automaticamente i formati dati predefiniti. 

È possibile modificare i tipi di dati di colonna in una trasformazione colonna derivata in un secondo momento. Utilizzare una trasformazione Seleziona per modificare i nomi delle colonne.

![Le impostazioni per i formati di dati predefiniti](media/data-flow/source2.png "i formati predefiniti")

## <a name="next-steps"></a>Passaggi successivi

Iniziare a creare un [trasformazione colonna derivata](data-flow-derived-column.md) e una [trasformazione selezionare](data-flow-select.md).
