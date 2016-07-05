<properties
	pageTitle="Esplorare i dati nelle tabelle Hive con query Hive | Microsoft Azure"
	description="Esplorare i dati nelle tabelle Hive mediante le query Hive."
	services="machine-learning"
	documentationCenter=""
	authors="bradsev"
	manager="paulettm" 
	editor="cgronlun"  />

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/14/2016"
	ms.author="hangzh;bradsev" />

# Esplorare i dati nelle tabelle Hive con query Hive. 

Questo documento fornisce alcuni script Hive di esempio che sono utilizzati per esplorare i dati nelle tabelle Hive in un cluster HDInsight Hadoop.

Il **menu** seguente collega ad argomenti che descrivono come usare gli strumenti per esplorare i dati da vari ambienti di archiviazione.

[AZURE.INCLUDE [cap-explore-data-selector](../../includes/cap-explore-data-selector.md)]

## Prerequisiti
Questo articolo presuppone che l'utente abbia:

* Creato un account di archiviazione di Azure. Per istruzioni, vedere [Creare un account di archiviazione di Azure](../hdinsight-get-started.md#storage).
* Eseguito il provisioning di un cluster Hadoop personalizzato con il servizio HDInsight. Per istruzioni, vedere [Personalizzazione di cluster Hadoop di Azure HDInsight per l'analisi scientifica dei dati](machine-learning-data-science-customize-hadoop-cluster.md).
* Caricato i dati nelle tabelle Hive dei cluster Hadoop di Azure HDInsight. Se questa operazione non è stata effettuata, seguire le istruzioni riportate in [Creazione e caricamento di dati nelle tabelle Hive](machine-learning-data-science-move-hive-tables.md) per caricare innanzitutto i dati nelle tabelle Hive.
* Abilitato l'accesso remoto al cluster. Per istruzioni, vedere [Accesso al nodo head del cluster Hadoop](machine-learning-data-science-customize-hadoop-cluster.md#headnode).
* Per istruzioni su come inviare query Hive, vedere [Come inviare query Hive](machine-learning-data-science-move-hive-tables.md#submit)

## Script delle query Hive di esempio per l'esplorazione dei dati

1. Visualizzare il numero di osservazioni per partizione `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`

2. Visualizzare il numero di osservazioni per giorno `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`

3. Visualizzare i livelli in una colonna di categoria `SELECT  distinct <column_name> from <databasename>.<tablename>`

4. Visualizzare il numero di livelli in una combinazione di due colonne di categoria `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`

5. Visualizzare la distribuzione per colonne numeriche `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`

6. Estrarre i record dall'unione di due tabelle

	    SELECT
			a.<common_columnname1> as <new_name1>,
			a.<common_columnname2> as <new_name2>,
    		a.<a_column_name1> as <new_name3>,
    		a.<a_column_name2> as <new_name4>,
    		b.<b_column_name1> as <new_name5>,
    		b.<b_column_name2> as <new_name6>
    	FROM
    		(
    		SELECT <common_columnname1>,
    			<common_columnname2>,
				<a_column_name1>,
				<a_column_name2>,
			FROM <databasename>.<tablename1>
			) a
			join
			(
			SELECT <common_columnname1>,
    			<common_columnname2>,
				<b_column_name1>,
				<b_column_name2>,
			FROM <databasename>.<tablename2>
			) b
			ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## Script delle query aggiuntive per gli scenari relativi ai dati delle corse dei taxi

Nell'[archivio GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) sono inoltre disponibili alcuni esempi di query specifiche per gli scenari relativi ai [dati dei tragitti dei taxi di NYC](http://chriswhong.com/open-data/foil_nyc_taxi/). Tali query dispongono già di un determinato schema dei dati e possono essere inviate e usate immediatamente.

 

<!---HONumber=AcomDC_0622_2016-->