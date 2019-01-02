---
title: Piattaforme dati per la Data Science Virtual Machine - Azure | Microsoft Docs
description: Informazioni su strumenti e piattaforme dati supportati nella Data Science Virtual Machine.
keywords: strumenti di analisi scientifica dei dati, data science virtual machine, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.component: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: f73f90b76900a3a5213297f8a28ff13204852039
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53105548"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Piattaforme dati supportate nella Data Science Virtual Machine

La Data Science Virtual Machine (DSVM) consente di creare una soluzione di analisi per un'ampia gamma di piattaforme dati. Oltre alle interfacce di piattaforme dati remote, la Data Science Virtual Machine offre un'istanza locale per eseguire rapidamente attività di sviluppo e creazione di prototipi. 

Di seguito sono indicati gli strumenti per piattaforme dati supportati nella Data Science Virtual Machine. 

## <a name="sql-server-2016-developer-edition"></a>SQL Server 2016 Developer Edition

| | |
| ------------- | ------------- |
| Che cos'è?   | Un'istanza locale del database relazionale      |
| Edizioni di Data Science Virtual Machine supportate      | Windows      |
| Usi tipici      | Sviluppo rapido in locale con set di dati più piccoli <br/> Eseguire R In-database   |
| Collegamenti agli esempi      |    Un piccolo esempio di set di dati di New York City viene caricato nel database SQL `nyctaxi`. <br/> Esempio di Jupyter che mostra che Microsoft R e le analisi nel database sono reperibili in:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`  |
| Strumenti correlati nella Data Science Virtual Machine       | SQL Server Management Studio <br/> Driver di ODBC/JDBC<br/> pyodbc, RODBC<br />Apache Drill      |

> [!NOTE]
> È possibile usare SQL Server 2016 Developer Edition solo a scopo di sviluppo e test. È necessaria una licenza oppure si deve eseguire una delle macchine virtuali di SQL Server nell'ambiente di produzione. 


### <a name="setup"></a>Configurazione

Il server del database è già preconfigurato e i servizi Windows correlati a SQL Server, ad esempio `SQL Server (MSSQLSERVER)`, sono impostati per l'esecuzione automatica. L'unica operazione manuale da eseguire consiste nell'abilitare l'analisi nel database usando Microsoft R. A tale scopo eseguire il comando seguente, come azione unica in SQL Server Management Studio dopo aver effettuato l'accesso come amministratore del computer, aprire "Nuova query" in SQL Server Management Studio, verificare che il database selezionato sia `master` ed eseguire: 

        CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 

        (Please replace the %COMPUTERNAME% with your VM name)
       
Per eseguire SQL Server Management Studio, è possibile cercare "SQL Server Management Studio" nell'elenco dei programmi o usare Windows Search per trovarlo ed eseguirlo. Quando vengono richieste le credenziali, scegliere "Autenticazione di Windows" e usare il nome del computer o ```localhost``` in Nome server SQL. 

### <a name="how-to-use--run-it"></a>Come usarlo o eseguirlo?  

Il server di database con l'istanza del database predefinito viene eseguito automaticamente per impostazione predefinita. È possibile usare strumenti quali SQL Server Management Studio nella macchina virtuale per accedere al database SQL Server in locale. L'account degli amministratori locali dispone di accesso come amministratore al database. 

Anche la Data Science Virtual Machine contiene i driver ODBC e JDBC per comunicare con SQL Server, database SQL di Azure e Azure SQL Data Warehouse da applicazioni scritte in più linguaggi inclusi Python, R. 

### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Come viene configurato o installato nella Data Science Virtual Machine? 

SQL Server è installato nella modalità standard. È disponibile in `C:\Program Files\Microsoft SQL Server`. L'istanza In-database R si trova in `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES`. Nella Data Science Virtual Machine è installata anche un'istanza del Server R autonomo in `C:\Program Files\Microsoft\R Server\R_SERVER`. Queste due istanze R non condividono le librerie.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (autonomo)

| | |
| ------------- | ------------- |
| Che cos'è?   | Un'istanza autonoma, ovvero un nodo singolo in-process, della nota piattaforma Apache Spark, un sistema per la rapida elaborazione di dati su larga scala e il processo di machine learning     |
| Edizioni di Data Science Virtual Machine supportate      | Linux <br /> Windows (sperimentale)      |
| Usi tipici      | * Rapido sviluppo di applicazioni Spark/PySpark in locale con set di dati più piccoli e successiva distribuzione nei cluster Spark di grandi dimensioni, ad esempio Azure HDInsight<br/> * Testare il contesto di Spark in Microsoft R Server <br />* Usare la libreria open source [MMLSpark](https://github.com/Azure/mmlspark) di Microsoft o di SparkML per compilare le applicazioni ML  |
| Collegamenti agli esempi      |    Esempio Jupyter: <br />&nbsp;&nbsp;* ~/notebooks/SparkML/pySpark <br /> &nbsp;&nbsp;* ~/notebooks/MMLSpark <br /> Microsoft R Server (Spark Context): /dsvm/samples/MRS/MRSSparkContextSample.R |
| Strumenti correlati nella Data Science Virtual Machine       | PySpark, Scala<br/>Jupyter (Spark/PySpark Kernels)<br/>Microsoft R Server, SparkR, Sparklyr <br />Apache Drill      |

### <a name="how-to-use-it"></a>Come usarlo
È possibile eseguire Spark inviando i processi di Spark nella riga di comando con i comandi `spark-submit` o `pyspark`. È anche possibile creare un notebook di Jupyter creando un nuovo notebook con il kernel Spark. 

È possibile usare Spark da R usando le librerie come SparkR, Sparklyr o Microsoft R Server disponibili nella Data Science Virtual Machine. Vedere i puntatori agli esempi nella tabella precedente. 

> [!NOTE]
> L'esecuzione di Microsoft R Server nel contesto di Spark di Data Science Virtual Machine è supportata solo nell'edizione di Data Science Virtual Machine per Ubuntu Linux. 



### <a name="setup"></a>Configurazione
Prima di eseguire nel contesto Spark in Microsoft R Server nell'edizione di Data Science Virtual Machine per Ubuntu Linux, è necessario eseguire un unico passaggio di configurazione per abilitare Hadoop Distributed File System con un solo nodo locale e un'istanza Yarn. Per impostazione predefinita, i servizi Hadoop sono installati ma disabilitati nella Data Science Virtual Machine. Per attivarli, è necessario eseguire i comandi seguenti come radice la prima volta:

    echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
    cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
    chmod 0600 ~hadoop/.ssh/authorized_keys
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa
    chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
    chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
    systemctl start hadoop-namenode hadoop-datanode hadoop-yarn

È possibile arrestare i servizi relativi a Hadoop quando non servono eseguendo ````systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn````. Un esempio che illustra come sviluppare e verificare che MRS nel contesto di Spark remoto, ovvero l'istanza di Spark autonoma nella Data Science Virtual Machine, sia implementato e disponibile nella directory `/dsvm/samples/MRS`. 


### <a name="how-is-it-configured--installed-on-the-dsvm"></a>Come viene configurato o installato nella Data Science Virtual Machine? 
|Piattaforma|Percorso di installazione ($SPARK_HOME)|
|:--------|:--------|
|Windows | c:\dsvm\tools\spark-X.X.X-bin-hadoopX.X|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Le librerie di accesso ai dati di BLOB di Azure o dell'archiviazione di Azure Data Lake tramite l'uso delle librerie di machine learning MMLSpark di Microsoft sono preinstallate in $SPARK_HOME/JAR. Questi JAR vengono caricati automaticamente all'avvio di Spark. Per impostazione predefinita Spark usa i dati sul disco locale. Affinché l'istanza di Spark nella Data Science Virtual Machine acceda ai dati archiviati nel BLOB di Azure o in Azure Data Lake Storage, è necessario creare o configurare il file `core-site.xml` basato sul modello che si trova in $SPARK_HOME/conf/core-site.xml.template, in cui sono presenti i segnaposto per le configurazione BLOB e di Azure Data Lake Storage, con le credenziali appropriate per i BLOB di Azure e Azure Data Lake Storage. Una procedura più dettagliata sulla creazione delle credenziali del servizio Azure Data Lake Storage è disponibile [qui](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory#create-an-active-directory-application). Dopo aver inserito le credenziali per il BLOB di Azure o di Azure Data Lake Storage nel file core-site.xml file, è possibile fare riferimento ai dati archiviati nelle origini con il prefisso URI wasb:// o adl://. 

