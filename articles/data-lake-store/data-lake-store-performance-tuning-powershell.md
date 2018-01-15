---
title: Linee guida per l'ottimizzazione delle prestazioni per l'uso di PowerShell con Data Lake Store | Microsoft Docs
description: Suggerimenti su come migliorare le prestazioni quando si usa Azure PowerShell con Data Lake Store
services: data-lake-store
documentationcenter: 
author: stewu
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: stewu
ms.openlocfilehash: 63e1114d49b7bcb8910e8cd8205f10d1e8587f61
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2018
---
# <a name="performance-tuning-guidance-for-using-powershell-with-azure-data-lake-store"></a>Linee guida per l'ottimizzazione delle prestazioni per l'uso di PowerShell con Azure Data Lake Store

Questo articolo elenca le proprietà che possono essere ottimizzate per ottenere prestazioni migliori durante l'uso di PowerShell con Data Lake Store:

## <a name="performance-related-properties"></a>Proprietà correlate alle prestazioni

| Proprietà            | Impostazione predefinita | Descrizione |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Questo parametro consente di scegliere il numero di thread paralleli per il caricamento o il download di ogni file. Questo valore rappresenta il numero massimo di thread che può essere allocato per ogni file, ma è possibile ottenere meno thread a seconda dello scenario (ad esempio, se si sta caricando un file da 1 KB, si ottiene un thread anche se si chiedono 20 thread).  |
| ConcurrentFileCount | 10      | Questo parametro viene usato specificamente per il caricamento e il download delle cartelle. Questo parametro determina il numero di file che possono essere caricati o scaricati contemporaneamente. Questo valore rappresenta il numero massimo di file che possono essere caricati o scaricati contemporaneamente in una sola volta, ma è possibile ottenere un valore inferiore di concorrenza a seconda dello scenario (ad esempio, se si caricano due file, si ottiene il caricamento di due file contemporaneamente anche se ne vengono richiesti 15). |

**Esempio**

Questo comando scarica i file da Azure Data Lake Store al disco locale dell'utente usando 20 thread per file e 100 file contemporaneamente.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

## <a name="how-do-i-determine-the-value-for-these-properties"></a>Come è possibile determinare il valore per queste proprietà?

La domanda successiva potrebbe essere come determinare il valore da inserire per le proprietà correlate alle prestazioni. Ecco alcune linee guida che è possibile usare.

* **Passaggio 1: Determinare il numero totale di thread** - È consigliabile iniziare calcolando il numero totale di thread da usare. Come regola generale, è consigliabile usare sei thread per ogni core fisico.

        Total thread count = total physical cores * 6

    **Esempio**

    Supponendo che si eseguano i comandi PowerShell da una macchina virtuale D14 con 16 core

        Total thread count = 16 cores * 6 = 96 threads


* **Passaggio 2: Calcolare PerFileThreadCount** - Viene calcolato il parametro PerFileThreadCount in base alla dimensione dei file. Per i file di dimensioni inferiori a 2,5 GB, non è necessario modificare questo parametro, perché il valore predefinito di 10 è sufficiente. Per i file di dimensioni superiori a 2,5 GB, è necessario usare 10 thread come base per i primi 2,5 GB e aggiungere 1 thread per ogni incremento aggiuntivo di 256 MB alle dimensioni del file. Se si copia una cartella con un'ampia gamma di dimensioni dei file, è consigliabile raggrupparle in file di dimensioni simili. Il raggruppamento in file di dimensioni diverse può comportare prestazioni non ottimali. Se non è possibile raggruppare file di dimensioni simili, è necessario impostare PerFileThreadCount in base alle dimensioni del file più grande.

        PerFileThreadCount = 10 threads for the first 2.5 GB + 1 thread for each additional 256 MB increase in file size

    **Esempio**

    Se si dispone di 100 file da 1 GB a 10 GB, viene usata la dimensione di 10 GB come riferimento per l'equazione, interpretata come segue.

        PerFileThreadCount = 10 + ((10 GB - 2.5 GB) / 256 MB) = 40 threads

* **Passaggio 3: Calcolare ConcurrentFilecount** - Usare il numero totale di thread e PerFileThreadCount per calcolare ConcurrentFileCount in base all'equazione seguente:

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Esempio**

    In base ai valori di esempio usati

        96 = 40 * ConcurrentFileCount

    Pertanto, **ConcurrentFileCount** è **2.4**, che è possibile arrotondare a **2**.

## <a name="further-tuning"></a>Ottimizzazione ulteriore

È possibile richiedere un'ulteriore ottimizzazione perché è presente un intervallo di dimensioni di file da usare. Il calcolo precedente funziona bene se tutti o la maggior parte dei file è più grande e più vicina all'intervallo di 10 GB. Se invece sono presenti diverse dimensioni di file con molti file più piccoli, è possibile ridurre PerFileThreadCount. Riducendo PerFileThreadCount, è possibile aumentare il valore di ConcurrentFileCount. Pertanto, se si presume che la maggior parte dei file è minore dell'intervallo di 5 GB, è possibile eseguire nuovamente il calcolo:

    PerFileThreadCount = 10 + ((5 GB - 2.5 GB) / 256 MB) = 20

Pertanto, **ConcurrentFileCount** diventa 96/20, ovvero 4,8, arrotondato a **4**.

È possibile continuare a ottimizzare le impostazioni modificando il valore di **PerFileThreadCount** in base alla distribuzione delle dimensioni dei file.

### <a name="limitation"></a>Limitazione

* **Numero di file minore di ConcurrentFileCount**: se il numero di file che si sta caricando è minore del valore di **ConcurrentFileCount** calcolato, è necessario ridurre **ConcurrentFileCount** in modo che risulti uguale al numero di file. È possibile usare i thread rimanenti per aumentare **PerFileThreadCount**.

* **Troppi thread**: se si aumenta il numero dei thread in maniera eccessiva senza aumentare le dimensioni del cluster, si corre il rischio di ridurre le prestazioni. È possibile che si verifichino problemi di conflitto dovuti al cambio di contesto nella CPU.

* **Concorrenza insufficiente**: se la concorrenza non è sufficiente, è possibile che il cluster sia troppo piccolo. È possibile aumentare il numero di nodi del cluster in modo da avere maggiore concorrenza.

* **Errori di limitazione**: se la concorrenza è troppo elevata, è possibile che vengano visualizzati errori di limitazione. Se si riscontrano errori di limitazione, è necessario ridurre la concorrenza o contattare Microsoft.

## <a name="next-steps"></a>Passaggi successivi
* [Usare Azure Data Lake Store per i requisiti di Big Data](data-lake-store-data-scenarios.md) 
* [Proteggere i dati in Data Lake Store](data-lake-store-secure-data.md)
* [Usare Azure Data Lake Analytics con Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Usare Azure HDInsight con Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

