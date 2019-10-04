---
title: Piattaforme dati supportate
titleSuffix: Azure Data Science Virtual Machine
description: Scopri le piattaforme di dati e gli strumenti supportati per la Data Science Virtual Machine di Azure.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/16/2018
ms.openlocfilehash: 5dbaf969420f066698a07b8d137d2ba44fc99080
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2019
ms.locfileid: "70208130"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Piattaforme dati supportate nella Data Science Virtual Machine

Con un Data Science Virtual Machine (DSVM), è possibile creare l'analisi in un'ampia gamma di piattaforme di dati. Oltre alle interfacce di piattaforme di dati remote, la macchina virtuale per data science offre un'istanza locale per il rapido sviluppo e la creazione di prototipi.

I seguenti strumenti della piattaforma dati sono supportati in DSVM.

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| Che cos'è?   | Un'istanza locale del database relazionale      |
| Edizioni DSVM supportate      | Windows      |
| Usi tipici      | Sviluppo rapido in locale con set di dati più piccoli <br/> Eseguire R In-database   |
| Collegamenti agli esempi      |    Un piccolo campione di un set di dati di New York City viene caricato nel database SQL:<br/>  `nyctaxi` <br/> Esempio Jupyter che illustra Microsoft Machine Learning Server e analisi nel database sono disponibili all'indirizzo:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Strumenti correlati in DSVM       | SQL Server Management Studio <br/> Driver ODBC/JDBC<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> SQL Server 2016 Developer Edition può essere utilizzato solo a scopo di sviluppo e test. È necessaria una licenza oppure si deve eseguire una delle macchine virtuali di SQL Server nell'ambiente di produzione.


### <a name="setup"></a>Configurazione

Il server di database è già preconfigurato e i servizi Windows correlati a SQL Server (ad `SQL Server (MSSQLSERVER)`esempio) sono impostati per l'esecuzione automatica. L'unico passaggio manuale prevede l'abilitazione di analisi nel database tramite Microsoft Machine Learning Server. È possibile eseguire questa operazione eseguendo il comando seguente come azione monouso in SQL Server Management Studio (SSMS). Eseguire questo comando dopo l'accesso come amministratore del computer, aprire una nuova query in SSMS e verificare che il database selezionato sia `master`:

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Replace %COMPUTERNAME% with your VM name.)
       
Per eseguire SQL Server Management Studio, è possibile cercare "SQL Server Management Studio" nell'elenco dei programmi oppure utilizzare Windows Search per trovarlo ed eseguirlo. Quando vengono richieste le credenziali, selezionare **autenticazione di Windows** e usare il nome ```localhost``` del computer o nel campo **nome SQL Server** .

### <a name="how-to-use-and-run-it"></a>Come usarlo ed eseguirlo

Per impostazione predefinita, il server di database con l'istanza di database predefinita viene eseguito automaticamente. È possibile usare strumenti quali SQL Server Management Studio nella macchina virtuale per accedere al database SQL Server in locale. Gli account amministratore locale dispongono dell'accesso amministrativo al database.

Inoltre, DSVM include driver ODBC e JDBC per comunicare con SQL Server, database SQL di Azure e Azure SQL Data Warehouse da applicazioni scritte in più linguaggi, tra cui Python e Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Come viene configurato e installato in DSVM? 

 SQL Server viene installato in modo standard. È disponibile in `C:\Program Files\Microsoft SQL Server`. L'istanza di Machine Learning Server nel database si trova in `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. Il DSVM dispone anche di un'istanza autonoma Machine Learning Server autonoma, installata `C:\Program Files\Microsoft\R Server\R_SERVER`in. Queste due istanze di Machine Learning Server non condividono le librerie.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (autonomo)

| | |
| ------------- | ------------- |
| Che cos'è?   | Istanza autonoma (a nodo singolo in-process) della piattaforma di Apache Spark più diffusa; sistema per l'elaborazione di dati veloce e su larga scala e Machine Learning     |
| Edizioni DSVM supportate      | Linux <br /> Windows (sperimentale)      |
| Usi tipici      | * Sviluppo rapido di applicazioni Spark/PySpark localmente con un set di dati più piccolo e una distribuzione successiva in cluster Spark di grandi dimensioni, ad esempio Azure HDInsight<br/> * Test Microsoft Machine Learning Server contesto Spark <br />* Usare SparkML o la libreria [MMLSpark](https://github.com/Azure/mmlspark) open source di Microsoft per compilare applicazioni ml |
| Collegamenti agli esempi      |    Esempio Jupyter: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft Machine Learning Server (contesto Spark):/dsvm/samples/MRS/MRSSparkContextSample.R |
| Strumenti correlati in DSVM       | PySpark, Scala<br/>Jupyter (Spark/PySpark Kernels)<br/>Microsoft Machine Learning Server, Sparkr, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Come usarlo
È possibile inviare processi Spark dalla riga di comando eseguendo il `spark-submit` comando o. `pyspark` È anche possibile creare il blocco appunti Jupyter creando un nuovo blocco appunti con il kernel Spark.

È possibile usare Spark da R usando librerie come Sparkr, Sparklyr e Microsoft Machine Learning Server, che sono disponibili in DSVM. Vedere i puntatori agli esempi nella tabella precedente.

> [!NOTE]
> L'esecuzione di Microsoft Machine Learning Server nel contesto Spark di DSVM è supportata solo nell'edizione Ubuntu Linux DSVM.



### <a name="setup"></a>Configurazione
Prima di essere eseguito in un contesto Spark in Microsoft Machine Learning Server in Ubuntu Linux edizione DSVM, è necessario completare un unico passaggio di configurazione per abilitare un singolo nodo locale Hadoop HDFS e l'istanza Yarn. Per impostazione predefinita, i servizi Hadoop sono installati ma disabilitati su DSVM. Per abilitarli, eseguire i comandi seguenti come radice la prima volta:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

È possibile arrestare i servizi correlati a ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn```Hadoop quando non sono più necessari eseguendo.

Un esempio che illustra come sviluppare e testare Mrs in un contesto di Spark remoto, ovvero l'istanza di Spark autonoma in DSVM, viene fornito e disponibile nella `/dsvm/samples/MRS` directory.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Come viene configurato e installato in DSVM? 
|Piattaforma|Percorso di installazione ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Le librerie per accedere ai dati dall'archiviazione BLOB di Azure o Azure Data Lake Storage, usando le librerie di Machine Learning Microsoft MMLSpark, sono preinstallate in $SPARK _HOME/jar. Questi JAR vengono caricati automaticamente all'avvio di Spark. Per impostazione predefinita, Spark usa i dati nel disco locale. 

Per l'istanza di Spark in DSVM per accedere ai dati archiviati nell'archivio BLOB o Azure Data Lake storage, è necessario creare e configurare `core-site.xml` il file in base al modello trovato in $Spark _Home/conf/Core-site. XML. template. È inoltre necessario disporre delle credenziali appropriate per accedere all'archiviazione BLOB e Azure Data Lake Storage. Si noti che i file di modello usano segnaposto per l'archiviazione BLOB e le configurazioni Azure Data Lake Storage.

Per informazioni più dettagliate sulla creazione di Azure Data Lake Storage credenziali del servizio, vedere [autenticazione con Azure Data Lake storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Dopo aver immesso le credenziali per l'archiviazione BLOB o Azure Data Lake Storage nel file core-site. XML, è possibile fare riferimento ai dati archiviati in tali origini tramite il prefisso URI di wasb://o adl://.

