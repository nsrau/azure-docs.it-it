---
title: Usare Hive di Hadoop e Desktop remoto in HDInsight | Microsoft Docs
description: Informazioni su come connettersi a un cluster Hadoop in HDInsight tramite Desktop remoto e quindi eseguire query Hive usando l'interfaccia della riga di comando di Hive.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2016
ms.author: larryfr

---
# Uso di Hive con Hadoop in HDInsight con Desktop remoto
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

In questo articolo si apprenderà come connettersi a un cluster HDInsight tramite Desktop remoto e quindi eseguire query Hive usando l'interfaccia della riga di comando di Hive.

> [!NOTE]
> Questo documento non fornisce una descrizione dettagliata delle operazioni eseguite dalle istruzioni HiveQL usate negli esempi. Per informazioni sul codice HiveQL usato in questo esempio, vedere [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md).
> 
> 

## <a id="prereq"></a>Prerequisiti
Per seguire la procedura descritta in questo articolo, è necessario quanto segue:

* Un cluster HDInsight (Hadoop in HDInsight) basato su Windows
* Un computer client che esegue Windows 10, Windows 8 o Windows 7

## <a id="connect"></a>Connettersi con Desktop remoto
Abilitare Desktop remoto per il cluster HDInsight e quindi connettersi seguendo le istruzioni disponibili in [Connettersi a cluster HDInsight tramite RDP](hdinsight-administer-use-management-portal.md#rdp).

## <a id="hive"></a>Usare il comando Hive
Una volta connessi al desktop per il cluster HDInsight, seguire questa procedura per l'uso con Hive:

1. Dal desktop di HDInsight avviare la **riga di comando di Hadoop**.
2. Immettere il seguente comando per avviare l'interfaccia della riga di comando di Hive:
   
        %hive_home%\bin\hive
   
    Dopo l'avvio dell'interfaccia della riga di comando, verrà visualizzato il prompt dell'interfaccia della riga di comando di Hive: `hive>`.
3. Usando l'interfaccia della riga di comando, immettere le seguenti istruzioni per creare una nuova tabella denominata **log4jLogs** con i dati di esempio:
   
        set hive.execution.engine=tez;
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;
   
    Di seguito sono elencate le istruzioni che eseguono queste azioni:
   
   * **DROP TABLE**: elimina la tabella e il file di dati, se la tabella esiste già.
   * **CREATE EXTERNAL TABLE**: crea una nuova tabella "external" in Hive. Le tabelle esterne archiviano solo la definizione della tabella in Hive. I dati vengono lasciati nella posizione originale.
     
     > [!NOTE]
     > È consigliabile usare le tabelle esterne quando si prevede che i dati sottostanti vengano aggiornati da un'origine esterna, ad esempio un processo automatico di caricamento dei dati, oppure da un'altra operazione MapReduce, ma si vuole che le query Hive usino sempre i dati più recenti.
     > 
     > L'eliminazione di una tabella esterna **non** comporta anche l'eliminazione dei dati. Viene eliminata solo la definizione della tabella.
     > 
     > 
   * **ROW FORMAT**: indica a Hive il modo in cui sono formattati i dati. In questo caso, i campi in ogni log sono separati da uno spazio.
   * **STORED AS TEXTFILE LOCATION**: indica a Hive dove sono archiviati i dati (la directory example/data) e che sono archiviati come testo.
   * **SELECT**: seleziona un numero di tutte le righe in cui la colonna **t4** include il valore **[ERROR]**. Dovrebbe restituire un valore pari a **3**, poiché sono presenti tre righe contenenti questo valore.
   * **INPUT\_\_FILE\_\_NAME come '%.log'** -indica ad Hive che si dovrebbero restituire solo i dati da file che terminano con. log. Questo limita la ricerca al file sample. log che contiene i dati, ed evita la restituzione di dati da altri file di dati di esempio che non corrispondono allo schema che è stato definito.
4. Usare le seguenti istruzioni per creare una nuova tabella "internal" denominata **errorLogs**:
   
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
   
    Di seguito sono elencate le istruzioni che eseguono queste azioni:
   
   * **CREATE TABLE IF NOT EXISTS**: crea una tabella, se non esiste già. Poiché non viene usata la parola chiave **EXTERNAL**, questa è una tabella interna che viene archiviata nel data warehouse di Hive e gestita completamente da Hive.
     
     > [!NOTE]
     > A differenza delle tabelle **EXTERNAL**, se si elimina una tabella interna, vengono eliminati anche i dati sottostanti.
     > 
     > 
   * **STORED AS ORC**: archivia i dati nel formato ORC (Optimized Row Columnar). Questo è un formato altamente ottimizzato ed efficiente per l'archiviazione di dati Hive.
   * **INSERT OVERWRITE ... SELECT**: seleziona dalla tabella **log4jLogs** le righe contenenti **[ERROR]**, quindi inserisce i dati nella tabella **errorLogs**.
     
     Per verificare che solo le righe contenenti **[ERROR]** nella colonna t4 siano state archiviate nella tabella **errorLogs**, usare l'istruzione seguente per restituire tutte le righe da **errorLogs**:
     
       SELECT * from errorLogs;
     
     Dovrebbero essere restituite tre righe di dati, tutte contenenti **ERROR** nella colonna t4.

## <a id="summary"></a>Riepilogo
Come è possibile osservare, il comando Hive fornisce un modo semplice per eseguire query Hive in un cluster HDInsight, monitorare lo stato del processo e recuperare l'output in modo interattivo.

## <a id="nextsteps"></a>Passaggi successivi
Per informazioni generali su Hive in HDInsight:

* [Usare Hive con Hadoop in HDInsight](hdinsight-use-hive.md)

Per informazioni su altre modalità d'uso di Hadoop in HDInsight:

* [Usare Pig con Hadoop in HDInsight](hdinsight-use-pig.md)
* [Usare MapReduce con Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Se si usa Tez con Hive, vedere i documenti seguenti per le informazioni di debug:

* [Usare l'interfaccia utente di Tez in HDInsight basato su Windows](hdinsight-debug-tez-ui.md)
* [Usare la vista Ambari Tez in HDInsight basato su Linux](hdinsight-debug-ambari-tez-view.md)

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md


[Powershell-install-configure]: ../powershell-install-configure.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

<!---HONumber=AcomDC_0914_2016-->