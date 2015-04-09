<properties 
	pageTitle="Usare Hive in HDInsight Hadoop per l'analisi del log dei siti Web | Azure" 
	description="Informazioni su come usare Hive con HDInsight per analizzare i log dei siti Web. Si userà un file di log come input in una tabella di HDInsight e HiveQL per eseguire query sui dati." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/06/2015" 
	ms.author="nitinme"/>

# Usare Hive con HDInsight per analizzare i log dei siti Web

Informazioni su come usare HiveQL con HDInsight per analizzare i log di un sito Web. L'analisi del log dei siti Web può essere usata per segmentare il proprio pubblico in base ad attività simile, classificare i visitatori di un sito in base a dati demografici, scoprire i contenuti che visualizzano, da quali siti Web sono stati indirizzati e così via.

In questo esempio verrà usato un cluster HDInsight per analizzare i log dei siti Web e ottenere informazioni dettagliate sulla frequenza delle visite di siti Web esterni nell'arco di un giorno nonché per visualizzare un riepilogo degli errori relativi ai siti Web incontrati dagli utenti. Si apprenderà come:

- Connettersi al BLOB di archiviazione di Azure contenente i file di log dei siti Web
- Creare tabelle HIVE per eseguire una query su tali log
- Creare query HIVE per analizzare i dati
- Usare Microsoft Excel per connettersi a HDInsight (usando una connessione ODBC) per recuperare i dati analizzati

![HDI.Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##Prerequisiti

- È necessario avere completato il provisioning di un **cluster HDInsight**. Per le istruzioni, vedere [Effettuare il provisioning di cluster HDInsight][hdinsight-provision]. 
- È necessario aver installato Microsoft Excel 2010 oppure Microsoft Excel 2013.
- Per importare i dati da Hive in Excel è necessario aver installato [Microsoft Hive ODBC Driver](http://www.microsoft.com/download/details.aspx?id=40886).


##Per eseguire l'esempio

1. Dal portale di gestione di Azure, fare clic sul cluster sul quale si vuole eseguire l'esempio, quindi fare clic su **Console query** nella parte inferiore. In alternativa è possibile aprire direttamente la Console query usando l'URL seguente:

	 	https://<clustername>.azurehdinsight.net
	
	Quando richiesto, eseguire l'autenticazione con il nome utente e la password dell'amministratore usati durante il provisioning di questo cluster.
  
2. Nella pagina Web che viene aperta, fare clic sulla scheda **Guida introduttiva** e quindi, sotto la categoria **Esempi**, fare clic sull'esempio **Analisi del log del sito Web**.
3. Seguire le istruzioni fornite nella pagina Web per completare l'esempio.

##Passaggi successivi
Provare a eseguire l'esempio su come analizzare i dati del sensore usando Azure HDInsight. Vedere [Analisi dei dati del sensore usando Hive con HDInsight][hdinsight-sensor-data-sample].


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png

<!--HONumber=49-->