---
title: Trasformazione origine del flusso di dati di mapping in Azure Data Factory
description: Trasformazione origine del flusso di dati di mapping in Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 38a01b4f81b76ba90a5fda4909d0e65e6307057e
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408715"
---
# <a name="mapping-data-flow-source-transformation"></a>Trasformazione origine del flusso di dati di mapping

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La trasformazione origine configura un'origine dati che si desidera usare per inserire dati nel proprio flusso di dati. Si può avere più di una trasformazione origine in un singolo flusso di dati. Iniziare sempre a progettare il flusso di dati con un'origine.

> [!NOTE]
> Ogni flusso di dati richiede almeno una trasformazione origine. Aggiungere tutte le origini aggiuntive necessarie per completare le trasformazioni di dati. È possibile aggiungere tali origini insieme a una trasformazione Join o unione.

![Opzioni di trasformazione origine](media/data-flow/source.png "origine")

Ogni trasformazione origine del flusso di dati deve essere associata esattamente al set di dati Data Factory che definisce la forma e la posizione dei dati da cui scrivere o da cui leggere. È possibile usare elenchi di file e caratteri jolly nell'origine per lavorare con più di un file alla volta.

## <a name="data-flow-staging-areas"></a>Aree di gestione temporanea del flusso di dati

Il flusso di dati funziona con i set di dati "staging", che si trovano tutti in Azure. Questi set di dati del flusso di dati vengono usati per il processo di gestione temporanea di questi ultimi, così da eseguire le operazioni di trasformazioni dati. Data Factory può accedere a circa 80 diversi connettori nativi. Per includere i dati provenienti da tali fonti nel proprio flusso di dati, è necessario innanzitutto inserire i dati in una di queste aree di processo di gestione temporanea dei set di dati del flusso di dati usando l'attività di copia.

## <a name="options"></a>Opzioni

### <a name="allow-schema-drift"></a>Permettere la deviazione schema
Selezionare Consenti deviazione schema in caso di modifica frequente delle colonne di origine. Questa impostazione consentirà a tutti i campi in arrivo dall'origine di fluire al sink attraverso le trasformazioni.

### <a name="validate-schema"></a>Convalida schema

![Origine pubblica](media/data-flow/source1.png "origine pubblica 1")

Se la versione dei dati di origine in ingresso non corrisponde allo schema definito, l'esecuzione del flusso di dati avrà esito negativo.

### <a name="sampling"></a>campionamento
Usare il campionamento per limitare il numero di righe dall'origine.  Ciò è utile quando è necessario solo un esempio dei dati di origine per il test e a fini di debug.

## <a name="define-schema"></a>Definizione di Schema

![Trasformazione origine](media/data-flow/source2.png "origine 2")

Per i tipi di file di origine che non sono fortemente tipizzati (ad esempio file flat, invece di file Parquet) è necessario definire i tipi di dati per ogni campo nella trasformazione origine. Successivamente, è possibile modificare i nomi delle colonne in una trasformazione Select e i tipi di dati in una trasformazione Colonna derivata. 

![Trasformazione origine](media/data-flow/source003.png "tipi di dati")

Per le origini fortemente tipizzate, è possibile modificare 

### <a name="optimize"></a>Eseguire l'ottimizzazione

![Partizioni origine](media/data-flow/sourcepart.png "partizionamento")

Nella scheda Ottimizza per la trasformazione origine, si noterà un tipo di partizionamento aggiuntivo denominato "Source". Questo si attiverà solo dopo aver selezionato il database SQL di Azure come origine. Questo avviene perché il file di definizione dell'applicazione (ADF) dovrà parallelizzare le connessioni per eseguire query di grandi dimensioni sull'origine del database SQL di Azure.

Il partizionamento dei dati nell'origine del database SQL è facoltativo, ma è utile per query di grandi dimensioni. Sono disponibili due opzioni:

### <a name="column"></a>Colonna

Selezionare una colonna per la partizione dalla tabella di origine. È anche necessario impostare il numero massimo di connessioni.

### <a name="query-condition"></a>Esegui query della condizione

Facoltativamente, è possibile scegliere di eseguire una partizione delle connessioni in base a una query. Per questa opzione, è sufficiente immettere il contenuto di un predicato WHERE. Ad esempio anno > 1980

## <a name="source-file-management"></a>Gestione file di origine
![Nuove impostazioni di origine](media/data-flow/source2.png "nuove impostazioni")

* Percorso carattere jolly per selezionare una serie di file dalla cartella di origine che corrispondono a un modello. Questa impostazione sostituirà tutti i file che sono stati impostati nella definizione del set di dati.
* Elenco di file. Uguale a un set di file. Puntare a un file di testo che si crea con un elenco di file di percorso relativo da elaborare.
* La colonna per memorizzare il nome del file memorizzerà il nome del file dall'origine in una colonna dei dati. Immettere un nuovo nome per archiviare la stringa del nome file.
* Dopo il completamento, è possibile scegliere di non eseguire alcuna operazione con il file di origine dopo l'esecuzione da parte del flusso di dati, eliminare i file di origine o spostare i file di origine. I percorsi di spostamento sono percorsi relativi.

### <a name="sql-datasets"></a>Set di dati SQL

Quando si usa un database SQL di Azure o Azure SQL DW come origine, saranno disponibili opzioni aggiuntive.

* Query: Immettere una query SQL per l'origine. L'impostazione di una query eseguirà l'override di qualsiasi tabella che è stata scelta nel set di dati. Si noti che le clausole Order By non sono supportate.

* Dimensioni batch: Immettere una dimensione batch per suddividere dati di grandi dimensioni in letture di dimensioni batch.

> [!NOTE]
> Le impostazioni operative del file verranno eseguite solo quando il flusso di dati viene eseguito da una pipeline (debug o esecuzione della pipeline) con l'attività di esecuzione del flusso di dati in una pipeline. Le operazioni sui file NON vengono eseguite in modalità di debug del flusso di dati.

### <a name="projection"></a>Proiezione

![Projection](media/data-flow/source3.png "Projection")

Come per gli schemi nel set di dati, la proiezione nell'origine definisce le colonne di dati, i tipi di dati e formati di dati dall'origine dati. Se si dispone di un file di testo senza schema definito, fare clic su "Rileva tipo di dati" per chiedere al file di definizione dell'applicazione (ADF) di tentare di eseguire il campionamento e dedurre i tipi di dati. È possibile impostare i formati predefiniti dei dati per il rilevamento automatico con il pulsante "Define Default Format" (Definisci formato predefinito). È possibile modificare i tipi di dati delle colonne in una successiva trasformazione Colonna derivata. I nomi delle colonne possono essere modificati tramite la trasformazione Select.

![Formati predefiniti](media/data-flow/source2.png "Formati predefiniti")

## <a name="next-steps"></a>Passaggi successivi

Iniziare a creare la trasformazione dei dati con [Derived Column](data-flow-derived-column.md) (Colonna derivata) e [Select](data-flow-select.md) (Select).
