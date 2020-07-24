---
title: Ottimizzare l'elaborazione dati
titleSuffix: Azure Machine Learning
description: Informazioni sulle procedure consigliate per ottimizzare le velocità di elaborazione dei dati e le integrazioni Azure Machine Learning supportate per l'elaborazione dei dati su larga scala.
services: machine-learning
ms.service: machine-learning
ms.author: sgilley
author: sdgilley
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: b0badd92a3156f76f99bf1f48fca2093a2bca2f5
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012638"
---
# <a name="optimize-data-processing-with-azure-machine-learning"></a>Ottimizzare l'elaborazione dei dati con Azure Machine Learning

Questo articolo illustra le procedure consigliate che consentono di ottimizzare le velocità di elaborazione dei dati in locale e su larga scala.

Azure Machine Learning è integrato con i pacchetti e i framework open source per l'elaborazione dei dati. Usando queste integrazioni e applicando le procedure consigliate in questo articolo, è possibile migliorare le velocità di elaborazione dei dati sia localmente che su larga scala.

## <a name="parquet-and-csv-file-formats"></a>Formati di file parquet e CSV

I file con valori delimitati da virgole (CSV) sono formati di file comuni per l'elaborazione dei dati. Tuttavia, i formati di file parquet sono consigliati per le attività di machine learning.

[I file parquet](https://parquet.apache.org/) archiviano i dati in un formato a colonne binario. Questo formato è utile se è necessario suddividere i dati in più file. Questo formato consente inoltre di specificare come destinazione i campi rilevanti per gli esperimenti di machine learning. Anziché leggere in un file di dati da 20 GB, è possibile ridurre il carico di dati selezionando le colonne necessarie per il training del modello ML. I file parquet possono anche essere compressi per ridurre al minimo la potenza di elaborazione e assumere meno spazio.

I file CSV vengono comunemente usati per importare ed esportare dati, perché sono facili da modificare e leggere in Excel. I dati in CSVs vengono archiviati come stringhe in un formato basato su righe e i file possono essere compressi per ridurre i carichi di trasferimento dei dati. I CSVs non compressi possono espandersi in base a un fattore di circa 2-10 e i CSVs compressi possono aumentare ancora di più. Quindi, il volume CSV da 5 GB in memoria si espande fino a 8 GB di RAM nel computer. Questo comportamento di compressione può aumentare la latenza di trasferimento dei dati, che non è ideale se si dispone di grandi quantità di dati da elaborare. 

## <a name="pandas-dataframe"></a>Dataframe Pandas

I frame di dati [Pandas](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) vengono comunemente usati per la manipolazione e l'analisi dei dati. `Pandas`funziona bene per le dimensioni dei dati inferiori a 1 GB, ma i tempi di elaborazione per i frame di dati `pandas` rallentano quando le dimensioni dei file raggiungono circa 1 GB. Questo rallentamento è dovuto al fatto che le dimensioni dei dati nell'archiviazione non corrispondono alle dimensioni dei dati in un frame di dati. Ad esempio, i dati nei file CSV possono espandersi fino a 10 volte in un frame di dati, quindi un file CSV da 1 GB può diventare 10 GB in un frame di dati.

`Pandas`è a thread singolo, ovvero le operazioni vengono eseguite una alla volta su una singola CPU. È possibile parallelizzare facilmente i carichi di lavoro a più CPU virtuali in una singola istanza di calcolo Azure Machine Learning con pacchetti come [Modin](https://modin.readthedocs.io/en/latest/) che esegue `Pandas` il wrapping usando un back-end distribuito.

Per parallelizzare le attività con `Modin` e [Dask](https://dask.org), è sufficiente modificare questa riga di codice `import pandas as pd` in `import modin.pandas as pd` .

## <a name="dataframe-out-of-memory-error"></a>Dataframe: errore di memoria insufficiente 

In genere si verifica un errore *di memoria insufficiente* quando il frame di frame si espande sopra la RAM disponibile nel computer. Questo concetto si applica anche a un Framework distribuito come `Modin` o `Dask` .  Ovvero, l'operazione tenta di caricare il dataframe in memoria in ogni nodo del cluster, ma la RAM disponibile non è sufficiente.

Una soluzione consiste nell'aumentare la RAM per adattarsi al frame di dataframe in memoria. È consigliabile che le dimensioni di calcolo e la potenza di elaborazione includano due volte le dimensioni della RAM. Quindi, se il dataframe è 10 GB, usare una destinazione di calcolo con almeno 20 GB di RAM per garantire che il frame di frame possa adattarsi alla memoria ed essere elaborato. 

Per più CPU virtuali, vCPU, tenere presente che una partizione può adattarsi comodamente alla RAM di ogni vCPU nel computer. Ovvero, se si dispone di 16 GB di RAM 4 vCPU, si desiderano circa 2 GB di dataframe per ogni vCPU.

### <a name="minimize-cpu-workloads"></a>Ridurre al minimo i carichi di lavoro della CPU

Se non è possibile aggiungere più RAM al computer, è possibile applicare le tecniche seguenti per ridurre al minimo i carichi di lavoro della CPU e ottimizzare i tempi di elaborazione. Questi consigli riguardano i sistemi singoli e distribuiti.

Tecnica | Descrizione
----|----
Compressione | Usare una rappresentazione diversa per i dati, in modo che usi meno memoria e non influisca significativamente sui risultati del calcolo.<br><br>*Esempio:* Anziché archiviare le voci sotto forma di stringa con circa 10 byte o più per ogni voce, archiviarle come valore booleano, true o false, che è possibile archiviare in 1 byte.
Suddivisione in blocchi | Caricare i dati in memoria in subset (blocchi), elaborare i dati un subset al momento o più subset in parallelo. Questo metodo funziona meglio se è necessario elaborare tutti i dati, ma non è necessario caricare contemporaneamente tutti i dati in memoria. <br><br>*Esempio:* Anziché elaborare i dati di un anno completo contemporaneamente, caricare ed elaborare i dati un mese alla volta.
Indicizzazione | Applicare e usare un indice, un riepilogo che indica dove trovare i dati a cui si è interessati. L'indicizzazione è utile quando è necessario utilizzare solo un subset di dati, anziché il set completo<br><br>*Esempio:* Se la quantità di dati di vendita dell'anno totale è ordinata in base al mese, un indice consente di cercare rapidamente il mese desiderato che si desidera elaborare.

## <a name="scale-data-processing"></a>Ridimensionare l'elaborazione dei dati

Se le indicazioni precedenti non sono sufficienti e non è possibile ottenere una macchina virtuale che soddisfi i dati, è possibile 

* Usare un Framework come `Spark` o `Dask` per elaborare i dati di memoria insufficiente. In questa opzione il frame di frame viene caricato nella partizione RAM per partizione ed elaborato, con il risultato finale raccolto alla fine.  

* Scalabilità orizzontale in un cluster con un Framework distribuito. In questa opzione, i carichi di elaborazione dei dati vengono suddivisi ed elaborati in più CPU che lavorano in parallelo, con il risultato finale raccolto alla fine.


### <a name="recommended-distributed-frameworks"></a>Framework distribuiti consigliati

La tabella seguente consiglia i Framework distribuiti integrati con Azure Machine Learning in base alla preferenza del codice o alla dimensione dei dati.

Esperienza o dimensione dei dati | Raccomandazione
------|------
Se si ha familiarità con`Pandas`| `Modin`o `Dask` dataframe
Se si preferisce`Spark` | `PySpark`
Per i dati inferiori a 1 GB | `Pandas`istanza di calcolo Azure Machine Learning locale **o** remota
Per dati di dimensioni superiori a 10 GB| Passare a un cluster usando `Ray` , `Dask` o`Spark`

È possibile creare `Dask` cluster nel cluster di calcolo di Azure ml con il pacchetto [Dask-cloudprovider](https://cloudprovider.dask.org/en/latest/#azure) . In alternativa, è possibile eseguire `Dask` localmente su un'istanza di calcolo.

## <a name="next-steps"></a>Passaggi successivi

* [Opzioni di inserimento dei dati con Azure Machine Learning](concept-data-ingestion.md).
* Inserimento [di dati con Azure Data Factory](how-to-data-ingest-adf.md).
