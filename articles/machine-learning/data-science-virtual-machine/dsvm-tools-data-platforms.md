---
title: Piattaforme dati supportate
titleSuffix: Azure Data Science Virtual Machine
description: Scopri le piattaforme di dati e gli strumenti supportati per la Data Science Virtual Machine di Azure.
keywords: strumenti di analisi scientifica dei dati, macchina virtuale per l'analisi scientifica dei dati, strumenti per l'analisi scientifica dei dati, analisi scientifica dei dati per Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 3e1cfa13a3fd568ab2f1253c015880c57f99ce7e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012486"
---
# <a name="data-platforms-supported-on-the-data-science-virtual-machine"></a>Piattaforme dati supportate nella Data Science Virtual Machine

Con un Data Science Virtual Machine (DSVM), è possibile creare l'analisi in un'ampia gamma di piattaforme di dati. Oltre alle interfacce di piattaforme di dati remote, la macchina virtuale per data science offre un'istanza locale per il rapido sviluppo e la creazione di prototipi.

I seguenti strumenti della piattaforma dati sono supportati in DSVM.

## <a name="sql-server-developer-edition"></a>SQL Server Developer Edition

| Category | Valore |
| ------------- | ------------- |
| Che cos'è?   | Un'istanza locale del database relazionale      |
| Edizioni DSVM supportate      | Windows 2016: SQL Server 2017, Windows 2019: SQL Server 2019      |
| Usi tipici      | <ul><li>Sviluppo rapido in locale con set di dati più piccoli</li><li>Eseguire R In-database</li></ul> |
| Collegamenti agli esempi      | <ul><li>Un piccolo campione di un set di dati di New York City viene caricato nel database SQL:<br/>  `nyctaxi`</li><li>Esempio Jupyter che illustra Microsoft Machine Learning Server e analisi nel database sono disponibili all'indirizzo:<br/> `~notebooks/SQL_R_Services_End_to_End_Tutorial.ipynb`</li></ul> |
| Strumenti correlati in DSVM       | <ul><li>SQL Server Management Studio</li><li>Driver ODBC/JDBC</li><li>pyodbc, RODBC</li><li>Apache Drill</li></ul> |

> [!NOTE]
> È possibile utilizzare SQL Server Developer Edition solo a scopo di sviluppo e test. È necessaria una licenza oppure si deve eseguire una delle macchine virtuali di SQL Server nell'ambiente di produzione.


### <a name="setup"></a>Installazione

Il server di database è già preconfigurato e i servizi Windows correlati a SQL Server ( `SQL Server (MSSQLSERVER)` ad esempio) sono impostati per l'esecuzione automatica. L'unico passaggio manuale prevede l'abilitazione di analisi nel database tramite Microsoft Machine Learning Server. È possibile abilitare l'analisi eseguendo il comando seguente come azione monouso in SQL Server Management Studio (SSMS). Eseguire questo comando dopo l'accesso come amministratore del computer, aprire una nuova query in SSMS e verificare che il database selezionato sia `master` :

```sql
CREATE LOGIN [%COMPUTERNAME%\SQLRUserGroup] FROM WINDOWS 
```

Sostituire% COMPUTERname% con il nome della macchina virtuale.

Per eseguire SQL Server Management Studio, è possibile cercare "SQL Server Management Studio" nell'elenco dei programmi oppure utilizzare Windows Search per trovarlo ed eseguirlo. Quando vengono richieste le credenziali, selezionare **autenticazione di Windows** e usare il nome del computer o ```localhost``` nel campo **nome SQL Server** .

### <a name="how-to-use-and-run-it"></a>Come usarlo ed eseguirlo

Per impostazione predefinita, il server di database con l'istanza di database predefinita viene eseguito automaticamente. È possibile usare strumenti quali SQL Server Management Studio nella macchina virtuale per accedere al database SQL Server in locale. Gli account amministratore locale dispongono dell'accesso amministrativo al database.

Inoltre, DSVM include driver ODBC e JDBC per comunicare con SQL Server, database SQL di Azure e Azure SQL Data Warehouse da applicazioni scritte in più linguaggi, tra cui Python e Machine Learning Server.

### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Come viene configurato e installato in DSVM? 

 SQL Server viene installato in modo standard. È disponibile in `C:\Program Files\Microsoft SQL Server`. L'istanza di Machine Learning Server nel database si trova in `C:\Program Files\Microsoft SQL Server\MSSQL13.MSSQLSERVER\R_SERVICES` . Il DSVM dispone anche di un'istanza autonoma Machine Learning Server autonoma, installata in `C:\Program Files\Microsoft\R Server\R_SERVER` . Queste due istanze di Machine Learning Server non condividono le librerie.


## <a name="apache-spark-2x-standalone"></a>Apache Spark 2.x (autonomo)

| Category | Valore |
| ------------- | ------------- |
| Che cos'è?   | Istanza autonoma (a nodo singolo in-process) della piattaforma di Apache Spark più diffusa; sistema per l'elaborazione di dati veloce e su larga scala e Machine Learning     |
| Edizioni DSVM supportate      | Linux     |
| Usi tipici      | <ul><li>Sviluppo rapido di applicazioni Spark/PySpark localmente con un set di dati più piccolo e una distribuzione successiva in cluster Spark di grandi dimensioni, ad esempio Azure HDInsight</li><li>Test Microsoft Machine Learning Server contesto Spark</li><li>Usare SparkML o la libreria [MMLSpark](https://github.com/Azure/mmlspark) open source di Microsoft per compilare applicazioni ml</li></ul> |
| Collegamenti agli esempi      |    Esempio Jupyter:<ul><li>~/notebooks/SparkML/pySpark</li><li>~/notebooks/MMLSpark</li></ul><p>Microsoft Machine Learning Server (contesto Spark):/dsvm/samples/MRS/MRSSparkContextSample.R</p> |
| Strumenti correlati in DSVM       | <ul><li>PySpark, Scala</li><li>Jupyter (Spark/PySpark Kernels)</li><li>Microsoft Machine Learning Server, Sparkr, Sparklyr</li><li>Apache Drill</li></ul> |

### <a name="how-to-use-it"></a>Modo d'uso
È possibile inviare processi Spark dalla riga di comando eseguendo il `spark-submit` comando o `pyspark` . È anche possibile creare il blocco appunti Jupyter creando un nuovo blocco appunti con il kernel Spark.

È possibile usare Spark da R usando librerie come Sparkr, Sparklyr e Microsoft Machine Learning Server, che sono disponibili in DSVM. Vedere i puntatori agli esempi nella tabella precedente.

### <a name="setup"></a>Installazione
Prima di essere eseguito in un contesto Spark in Microsoft Machine Learning Server in Ubuntu Linux edizione DSVM, è necessario completare un unico passaggio di configurazione per abilitare un singolo nodo locale Hadoop HDFS e l'istanza Yarn. Per impostazione predefinita, i servizi Hadoop sono installati ma disabilitati su DSVM. Per abilitarli, eseguire i comandi seguenti come radice la prima volta:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

È possibile arrestare i servizi correlati a Hadoop quando non sono più necessari eseguendo ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` .

Un esempio che illustra come sviluppare e testare MRS in un contesto di Spark remoto, ovvero l'istanza di Spark autonoma in DSVM, viene fornito e disponibile nella `/dsvm/samples/MRS` Directory.


### <a name="how-is-it-configured-and-installed-on-the-dsvm"></a>Come viene configurato e installato in DSVM? 
|Piattaforma|Percorso di installazione ($SPARK_HOME)|
|:--------|:--------|
|Linux   | /dsvm/tools/spark-X.X.X-bin-hadoopX.X|


Le librerie per accedere ai dati dall'archiviazione BLOB di Azure o Azure Data Lake Storage, usando le librerie di Machine Learning Microsoft MMLSpark, sono preinstallate in $SPARK _HOME/Jars. Questi JAR vengono caricati automaticamente all'avvio di Spark. Per impostazione predefinita, Spark usa i dati nel disco locale. 

Per l'istanza di Spark in DSVM per accedere ai dati archiviati nell'archivio BLOB o Azure Data Lake Storage, è necessario creare e configurare il `core-site.xml` file in base al modello trovato in $SPARK _HOME/conf/core-site.xml. template. È inoltre necessario disporre delle credenziali appropriate per accedere all'archiviazione BLOB e Azure Data Lake Storage. Si noti che i file di modello usano segnaposto per l'archiviazione BLOB e le configurazioni Azure Data Lake Storage.

Per informazioni più dettagliate sulla creazione di Azure Data Lake Storage credenziali del servizio, vedere [autenticazione con Azure Data Lake storage Gen1](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-authenticate-using-active-directory). Dopo aver immesso le credenziali per l'archiviazione BLOB o Azure Data Lake Storage nel file di core-site.xml, è possibile fare riferimento ai dati archiviati in tali origini tramite il prefisso URI di wasb://o adl://.

