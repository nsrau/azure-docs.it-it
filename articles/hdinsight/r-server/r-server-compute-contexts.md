---
title: Opzioni del contesto di calcolo per R Server in HDInsight - Azure | Documentazione Microsoft
description: Informazioni sulle diverse opzioni del contesto di calcolo disponibili per gli utenti con R Server in HDInsight
services: HDInsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 0deb0b1c-4094-459b-94fc-ec9b774c1f8a
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/19/2017
ms.author: bradsev
ms.openlocfilehash: 8bc7767d9903761f3338b7825185171aad74de78
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="compute-context-options-for-r-server-on-hdinsight"></a>Opzioni del contesto di calcolo per R Server su HDInsight (anteprima)

Microsoft R Server in Azure HDInsight controlla l'esecuzione delle chiamate, impostando il contesto di calcolo. In questo articolo vengono descritte le opzioni disponibili per specificare se e come l'esecuzione venga parallelizzata tra i core del nodo perimetrale o del cluster HDInsight.

Il nodo perimetrale di un cluster offre una posizione pratica per connettersi al cluster ed eseguire gli script R. Con un nodo perimetrale è possibile eseguire le funzioni distribuite parallelizzate di RevoScaleR nei core del server del nodo perimetrale. È anche possibile eseguire tali funzioni tra i nodi del cluster usando contesti di calcolo Hadoop MapReduce o Spark di RevoScaleR.

## <a name="microsoft-r-server-on-azure-hdinsight"></a>Microsoft R Server in Azure HDInsight
[Microsoft R Server in Azure HDInsight](r-server-overview.md) fornisce le funzionalità più recenti per l'analisi basata su R. Può usare i dati archiviati in un contenitore HDFS nell'account di archiviazione [BLOB di Azure](../../storage/common/storage-introduction.md "Archiviazione BLOB di Azure"), in un Data Lake Store o nel file system locale di Linux. Poiché R Server si basa su R open source, le applicazioni basate su R compilate dall'utente possono applicare gli oltre 8000 pacchetti R open source. Possono anche usare le routine di [RevoScaleR](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler), il pacchetto di analisi dei Big Data di Microsoft incluso in R Server.  

## <a name="compute-contexts-for-an-edge-node"></a>Contesti di calcolo per un nodo perimetrale
In generale, uno script R eseguito in R Server nel nodo perimetrale viene eseguito all'interno dell'interprete R in tale nodo. L'eccezione è costituita dai passaggi che chiamano una funzione RevoScaleR. Le chiamate RevoScaleR vengono eseguite in un ambiente di calcolo determinato dall'impostazione del contesto di calcolo di RevoScaleR.  Quando si esegue lo script R da un nodo perimetrale, i valori possibili del contesto di calcolo sono:

- sequenziale locale (*local*)
- parallelo locale (*localpar*)
- MapReduce
- Spark

Le opzioni *local* e *localpar* differiscono solo per la modalità di esecuzione delle chiamate **rxExec**. Entrambe eseguono chiamate ad altre funzioni di ricezione in modo parallelo tra le memorie centrali disponibili, se non diversamente specificato, mediante l'uso dell'opzione RevoScaleR **numCoresToUse**, ad esempio `rxOptions(numCoresToUse=6)`. Le opzioni di esecuzione parallela offrono prestazioni ottimali.

Nella tabella seguente vengono riepilogate le varie opzioni di contesto di calcolo per impostare l'esecuzione delle chiamate:

| Contesto di calcolo  | Come impostarlo                      | Contesto di esecuzione                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Sequenziale locale | rxSetComputeContext('local')    | Esecuzione in parallelo attraverso i core del server del nodo perimetrale, ad eccezione delle chiamate rxExec che vengono eseguite in serie |
| Parallelo locale   | rxSetComputeContext('localpar') | Esecuzione in parallelo tra i core del server del nodo perimetrale |
| Spark            | RxSpark()                       | Esecuzione parallelizzata distribuita tramite Spark tra i nodi del cluster HDInsight |
| MapReduce       | RxHadoopMR()                    | Esecuzione parallelizzata distribuita tramite MapReduce tra i nodi del cluster HDInsight |

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Linee guida per la scelta di un contesto di calcolo

Quale delle tre opzioni consenta l'esecuzione parallelizzata dipende dalla natura del proprio lavoro di analitica, dalle dimensioni e dalla posizione dei dati. Non esiste una formula fissa che indichi quale contesto di calcolo usare. Esistono tuttavia alcuni principi guida che consentono di effettuare la scelta appropriata, o almeno consentono di limitare le scelte prima di eseguire un benchmark. Ecco alcuni dei principi guida:

- Il file system locale di Linux è più veloce rispetto ad HDFS.
- Le analisi ripetute risultano più veloci se i dati sono locali e in formato XDF.
- È preferibile eseguire il flusso di piccole quantità di dati da un'origine dati di testo. Se la quantità di dati è più grande, è necessario convertirli con estensione XDF prima dell'analisi.
- Il sovraccarico dovuto alla copia o allo streaming dei dati nel nodo perimetrale per l'analisi diventa ingestibile per quantità di dati molto grandi.
- Spark è più veloce rispetto a Map Reduce per l'analisi in Hadoop.

Dati questi principi, la sezione seguente illustra alcune regole generali per la selezione di un contesto di calcolo.

### <a name="local"></a>Local
* Se la quantità di dati da analizzare è limitata e non sono richieste analisi ripetute, eseguirne il flusso direttamente in una routine di analisi usando *local* o *localpar*.
* Se la quantità di dati da analizzare è limitata o media e richiede analisi ripetute, copiare i dati nel file system locale, importarli in XDF e analizzarli con *local* o *localpar*.

### <a name="hadoop-spark"></a>Hadoop Spark
* Se la quantità di dati da analizzare è grande, importare i dati in un DataFrame Spark usando **RxHiveData** o **RxParquetData** oppure in HDFS in formato XDF, a meno che lo spazio di archiviazione non sia un problema, e analizzarli usando il contesto di calcolo di Spark.

### <a name="hadoop-map-reduce"></a>Hadoop MapReduce
* Usare il contesto di calcolo MapReduce solo se si riscontra un problema insormontabile riguardo al contesto di calcolo Spark, poiché di norma risulta essere più lento.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Guida in linea su rxSetComputeContext
Per altre informazioni ed esempi di contesti di calcolo di RevoScaleR, vedere la guida in linea di R sul metodo rxSetComputeContext, ad esempio:

    > ?rxSetComputeContext

È anche possibile fare riferimento alla [panoramica sull'elaborazione distribuita](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-distributed-computing) nella [documentazione di Microsoft Machine Learning Server](https://docs.microsoft.com/machine-learning-server/).

## <a name="next-steps"></a>Passaggi successivi
In questo articolo sono state descritte le opzioni disponibili per specificare se e come l'esecuzione venga parallelizzata tra i core del nodo perimetrale o del cluster HDInsight. Per altre informazioni sull'uso di R Server con i cluster HDInsight, vedere gli argomenti seguenti:

* [Panoramica: R Server su HDInsight (anteprima)](r-server-overview.md)
* [Introduzione all'uso di R Server su HDInsight (anteprima)](r-server-get-started.md)
* [Opzioni di Archiviazione di Azure per R Server su HDInsight](r-server-storage.md)

