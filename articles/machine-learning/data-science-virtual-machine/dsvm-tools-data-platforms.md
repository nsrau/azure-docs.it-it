---
title: Piattaforme dati supportate
titleSuffix: Azure Data Science Virtual Machine
description: Informazioni sulle piattaforme di dati e sugli strumenti supportati per la macchina virtuale di analisi scientifica dei dati di Azure.Learn about the supported data platforms and tools for the Azure Data Science Virtual Machine.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: cd787881957d78f179107e46b2650de4618c7724
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282325"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Piattaforme dati supportate nella Data Science Virtual Machine

Con una macchina virtuale di data science (DSVM), è possibile creare l'analisi su un'ampia gamma di piattaforme di dati. Oltre alle interfacce di piattaforme di dati remote, la macchina virtuale per data science offre un'istanza locale per il rapido sviluppo e la creazione di prototipi.

I seguenti strumenti della piattaforma dati sono supportati in DSVM.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| | |
| ------------- | ------------- |
| Che cos'è?   | Un'istanza locale del database relazionale      |
| Edizioni DSVM supportate      | Windows 2016: SQL Server 2017, Windows 2019: SQL Server 2019      |
| Usi tipici      | Sviluppo rapido in locale con set di dati più piccoli <br/> Eseguire R In-database   |
| Collegamenti agli esempi      |    Un piccolo esempio di un set di dati di New York City viene caricato nel database SQL:A small sample of a New York City dataset is loaded into the SQL database:<br/>  `nyctaxi` <br/> L'esempio Jupyter che mostra Microsoft Machine Learning Server e l'analisi nel database è disponibile all'indirizzo:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Strumenti correlati su DSVM       | SQL Server Management Studio <br/> Driver ODBC/JDBC<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> SQL Server Developer Edition può essere utilizzato solo a scopo di sviluppo e test. È necessaria una licenza oppure si deve eseguire una delle macchine virtuali di SQL Server nell'ambiente di produzione.


### <a name="setup"></a>Configurazione

Il server di database è già preconfigurato e `SQL Server (MSSQLSERVER)`i servizi di Windows correlati a SQL Server (ad esempio ) sono impostati per l'esecuzione automatica. L'unico passaggio manuale consiste nell'abilitare l'analisi nel database tramite Microsoft Machine Learning Server. È possibile abilitare l'analisi eseguendo il comando seguente come azione una tantera in SQL Server Management StudioSQL Server Management Studio (SSMS). Eseguire questo comando dopo aver eseguito l'accesso come amministratore del computer, aprire `master`una nuova query in SSMS e assicurarsi che il database selezionato sia:

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
Per eseguire SQL Server Management Studio, è possibile cercare "SQL Server Management Studio" nell'elenco dei programmi oppure utilizzare Windows Search per individuarlo ed eseguirlo. Quando vengono richieste le credenziali, selezionare **Autenticazione** di Windows e utilizzare il nome del computer o ```localhost``` nel campo Nome SQL **Server.**

### <a name="how-to-use-and-run-it"></a>Come usarlo ed eseguirlo

Per impostazione predefinita, il server di database con l'istanza di database predefinita viene eseguito automaticamente. È possibile usare strumenti quali SQL Server Management Studio nella macchina virtuale per accedere al database SQL Server in locale. Gli account amministratore locale dispongono dell'accesso di amministratore al database.

Inoltre, DSVM viene fornito con i driver ODBC e JDBC per comunicare con SQL Server, i database SQL di Azure e Azure SQL Data Warehouse da applicazioni scritte in più lingue, tra cui Python e Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Come viene configurato e installato in DSVM? 

 SQL ServerSQL Server viene installato nel modo standard. È disponibile in `C:\Program Files\Microsoft SQL Server`. L'istanza del server di Machine `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`Learning nel database si trova in . DSVM dispone inoltre di un'istanza autonoma di `C:\Program Files\Microsoft\R Server\R_SERVER`Machine Learning Server separata, installata in . Queste due istanze di Machine Learning Server non condividono le librerie.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (autonomo)

| | |
| ------------- | ------------- |
| Che cos'è?   | Un'istanza autonoma (singolo nodo in-process) della popolare piattaforma Apache Spark; un sistema per l'elaborazione rapida e su larga scala dei dati e l'apprendimento automatico     |
| Edizioni DSVM supportate      | Linux     |
| Usi tipici      | - Sviluppo rapido di applicazioni Spark/PySpark in locale con un set di dati più piccolo e una distribuzione successiva in cluster Spark di grandi dimensioni, ad esempio Azure HDInsight<br/> Testare il contesto Spark di Microsoft Machine Learning Server <br />Utilizzare SparkML o la libreria [MMLSpark](https://github.com/Azure/mmlspark) open source di Microsoft per creare applicazioni ML |
| Collegamenti agli esempi      |    Esempio Jupyter: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft Machine Learning Server (contesto Spark): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Strumenti correlati su DSVM       | PySpark, Scala<br/>Jupyter (Spark/PySpark Kernels)<br/>Microsoft Machine Learning Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Come usarlo
È possibile inviare i processi Spark `spark-submit` `pyspark` nella riga di comando eseguendo il comando o . È anche possibile creare il blocco appunti Jupyter creando un nuovo blocco appunti con il kernel Spark.

È possibile usare Spark da R usando librerie come SparkR, Sparklyr e Microsoft Machine Learning Server, disponibili in DSVM. Vedere i puntatori agli esempi nella tabella precedente.

### <a name="setup"></a>Configurazione
Prima di eseguire in un contesto Spark in Microsoft Machine Learning Server in Ubuntu Linux DSVM edition, è necessario completare un passaggio di installazione una tantera per abilitare un nodo singolo locale Hadoop HDFS e Yarn istanza. Per impostazione predefinita, i servizi Hadoop sono installati ma disabilitati su DSVM. Per abilitarli, eseguire i seguenti comandi come root la prima volta:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

È possibile arrestare i servizi correlati ad Hadoop ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```quando non sono più necessari eseguendo .

Un esempio che illustra come sviluppare e testare MRS in un contesto Spark remoto (che è l'istanza Spark autonoma nella DSVM) è fornito e disponibile nella `/dsvm/samples/MRS` directory.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Come viene configurato e installato in DSVM? 
|Piattaforma|Percorso di installazione ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Le librerie per accedere ai dati dall'archiviazione BLOB di Azure o da Archiviazione di Azure Lake, usando le librerie di machine learning Microsoft MMLSpark, sono preinstallate in $SPARK_HOME/jars. Questi JAR vengono caricati automaticamente all'avvio di Spark. Per impostazione predefinita, Spark utilizza i dati sul disco locale. 

Affinché l'istanza Spark in DSVM acceda ai dati archiviati nell'archivio BLOB o in Archiviazione data lake di Azure, è necessario creare e configurare il `core-site.xml` file in base al modello disponibile in $SPARK_HOME/conf/core-site.xml.template. È inoltre necessario disporre delle credenziali appropriate per accedere all'archiviazione BLOB e ad Archiviazione di Azure Data Lake.You must also have the appropriate credentials to access Blob storage and Azure Data Lake Storage. Si noti che i file modello usano segnaposto per l'archiviazione BLOB e le configurazioni di Archiviazione di Azure Data Lake.

Per informazioni più dettagliate sulla creazione delle credenziali del servizio di archiviazione di Azure Data Lake, vedere [Autenticazione con Azure Data Lake Storage Gen1.](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory) Dopo aver immesso le credenziali per l'archiviazione BLOB o Archiviazione lake di Azure nel file core-site.xml, è possibile fare riferimento ai dati archiviati in tali origini tramite il prefisso URI di wasb:// o adl://.

