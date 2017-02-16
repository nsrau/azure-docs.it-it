---
title: Opzioni del contesto di calcolo per R Server in HDInsight | Documentazione Microsoft
description: Informazioni sulle diverse opzioni del contesto di calcolo disponibili per gli utenti con R Server in HDInsight
services: HDInsight
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 0deb0b1c-4094-459b-94fc-ec9b774c1f8a
ms.service: HDInsight
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 01/09/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: 841e70fa3a80bbeb7e2281246bac2f99c0de899f
ms.openlocfilehash: 169743012b1f50d67d5eafdb279e706719752eb8


---
# <a name="compute-context-options-for-r-server-on-hdinsight"></a>Opzioni del contesto di calcolo per R Server su HDInsight (anteprima)
Microsoft R Server in Azure HDInsight fornisce le funzionalità più recenti per l'analisi basata su R. Usa i dati archiviati in HDFS in un contenitore nell'account di archiviazione [BLOB di Azure](../storage/storage-introduction.md "Archiviazione BLOB di Azure"), in un Data Lake Store o nel file system locale di Linux. Poiché R Server si basa su R open source, le applicazioni basate su R compilate dall'utente possono sfruttare gli oltre 8000 pacchetti R open source. Possono inoltre sfruttare le routine di [ScaleR](http://www.revolutionanalytics.com/revolution-r-enterprise-scaler "ScaleR di Revolution Analytics"), il pacchetto di analisi dei Big Data di Microsoft incluso in R Server.  

Il nodo perimetrale di un cluster offre una posizione pratica per connettersi al cluster ed eseguire gli script R. Con un nodo perimetrale è possibile eseguire funzioni distribuite parallelizzate di ScaleR nei core del server del nodo perimetrale. È anche possibile eseguire tali funzioni tra i nodi del cluster usando contesti di calcolo Hadoop MapReduce o Spark di ScaleR.

## <a name="compute-contexts-for-an-edge-node"></a>Contesti di calcolo per un nodo perimetrale
In generale, uno script R eseguito in R Server nel nodo perimetrale viene eseguito all'interno dell'interprete R in tale nodo. L'eccezione è costituita dai passaggi che chiamano una funzione ScaleR. Le chiamate ScaleR vengono eseguite in un ambiente di calcolo determinato dall'impostazione del contesto di calcolo di ScaleR.  Quando si esegue lo script R da un nodo perimetrale, i valori possibili del contesto di calcolo sono sequenziale locale ('local'), parallelo locale ('localpar'), MapReduce e Spark.

Le opzioni 'locale' e 'localpar' differiscono solo per la modalità di esecuzione delle chiamate rxExec. Entrambe eseguono chiamate ad altre funzioni di ricezione in modo parallelo tra le memorie centrali disponibili, se non diversamente specificato, mediante l'uso dell'opzione ScaleR numCoresToUse, ad esempio rxOptions(numCoresToUse=6). Di seguito vengono riepilogate le varie opzioni del contesto di calcolo

| Contesto di calcolo  | Come impostarlo                      | Contesto di esecuzione                        |
| ---------------- | ------------------------------- | ---------------------------------------- |
| Sequenziale locale | rxSetComputeContext('local')    | Esecuzione in parallelo attraverso i core del server del nodo perimetrale, ad eccezione delle chiamate rxExec che vengono eseguite in serie |
| Parallelo locale   | rxSetComputeContext('localpar') | Esecuzione in parallelo tra i core del server del nodo perimetrale |
| Spark            | RxSpark()                       | Esecuzione parallelizzata distribuita tramite Spark tra i nodi del cluster HDInsight |
| MapReduce       | RxHadoopMR()                    | Esecuzione parallelizzata distribuita tramite MapReduce tra i nodi del cluster HDInsight |

Supponendo che si preferisca un'esecuzione parallelizzata per motivi di prestazioni, sono disponibili tre opzioni. L'opzione scelta dipende dalla natura dell'analisi e dalle dimensioni e posizione dei dati.

## <a name="guidelines-for-deciding-on-a-compute-context"></a>Linee guida per la scelta di un contesto di calcolo
Attualmente non esiste una formula che indichi quale contesto di calcolo usare. Esistono tuttavia alcuni principi guida che consentono di effettuare la scelta appropriata o, almeno, consentono di limitare le scelte prima di eseguire un benchmark. Ecco alcuni dei principi guida:

1. Il file system locale di Linux è più veloce rispetto ad HDFS.
2. Le analisi ripetute risultano più veloci se i dati sono locali e in formato XDF.
3. È preferibile eseguire lo streaming di piccole quantità di dati da un'origine dati di testo. Se la quantità di dati è maggiore, è necessario convertirli in formato XDF prima dell'analisi.
4. Il sovraccarico dovuto alla copia o allo streaming dei dati nel nodo perimetrale per l'analisi diventa ingestibile per quantità di dati molto grandi.
5. Spark è più veloce rispetto a Map Reduce per l'analisi in Hadoop.

Dati questi principi, ecco alcune regole generali per la selezione di un contesto di calcolo:

### <a name="local"></a>Local
* Se la quantità di dati da analizzare è limitata e non sono richieste analisi ripetute, eseguirne il flusso direttamente in una routine di analisi e usare 'local' o 'localpar'.
* Se la quantità di dati da analizzare è limitata o media e richiede analisi ripetute, copiare i dati nel file system locale, importarli in XDF e analizzarli con 'local' o 'localpar'.

### <a name="hadoop-spark"></a>Hadoop Spark
* Se la quantità di dati da analizzare è grande, importare i dati in un DataFrame Spark usando RxHiveData o RxParquetData oppure in HDFS in formato XDF, a meno che lo spazio di archiviazione non sia un problema, e analizzarli usando 'Spark'.

### <a name="hadoop-map-reduce"></a>Hadoop MapReduce
* Da usare solo se si riscontra un problema insormontabile riguardo all'utilizzo del contesto di calcolo Spark, poiché di norma risulta essere più lento.  

## <a name="inline-help-on-rxsetcomputecontext"></a>Guida in linea su rxSetComputeContext
Per altre informazioni ed esempi di contesti di calcolo di ScaleR, vedere la guida in linea di R sul metodo rxSetComputeContext, ad esempio:

    > ?rxSetComputeContext

È anche possibile vedere la "[Guida all'elaborazione distribuita di ScaleR](https://msdn.microsoft.com/microsoft-r/scaler-distributed-computing)" disponibile nella pagina relativa a [Server R MSDN](https://msdn.microsoft.com/library/mt674634.aspx "Server R in MSDN nella libreria MSDN").

## <a name="next-steps"></a>Passaggi successivi
In questo articolo si è appreso come creare un nuovo cluster HDInsight che include R Server. Si sono anche apprese le nozioni di base per l'suo della console R da una sessione SSH. A questo punto è possibile leggere gli articoli seguenti per scoprire altre modalità di utilizzo di R Server in HDInsight:

* [Panoramica: R Server su HDInsight (anteprima)](hdinsight-hadoop-r-server-overview.md)
* [Introduzione all'uso di R Server su HDInsight (anteprima)](hdinsight-hadoop-r-server-get-started.md)
* [Aggiungere RStudio Server a HDInsight (se non è stato aggiunto durante la creazione del cluster)](hdinsight-hadoop-r-server-install-r-studio.md)
* [Opzioni di Archiviazione di Azure per R Server su HDInsight](hdinsight-hadoop-r-server-storage.md)




<!--HONumber=Nov16_HO4-->


