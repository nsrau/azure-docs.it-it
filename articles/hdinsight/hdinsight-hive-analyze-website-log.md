<properties 
	pageTitle="Usare Hive con Hadoop per l'analisi dei log dei siti Web | Microsoft Azure" 
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
	ms.date="07/11/2015" 
	ms.author="nitinme"/>

# Usare Hive con HDInsight per analizzare i log dei siti Web

Informazioni su come usare HiveQL con HDInsight per analizzare i log di un sito Web. L'analisi dei log dei siti Web può essere usata per segmentare il proprio pubblico in base ad attività simili, classificare i visitatori di un sito in base a dati demografici e scoprire i contenuti che visualizzano, da quali siti Web sono stati indirizzati e così via.

In questo esempio si userà un cluster HDInsight per analizzare i file di log dei siti Web e ottenere informazioni dettagliate sulla frequenza delle visite da siti Web esterni nell'arco di una giornata. Si genererà inoltre un riepilogo degli errori dei siti Web riscontrati dagli utenti. Si apprenderà come:

- Connettersi a un archivio BLOB di Azure, che contiene i file di log dei siti Web.
- Creare tabelle HIVE per eseguire query su tali log.
- Creare query HIVE per analizzare i dati.
- Usare Microsoft Excel per connettersi a HDInsight (mediante una connessione ODBC) per recuperare i dati analizzati.

![HDI.Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##Prerequisiti

- È necessario avere completato il provisioning di un cluster Hadoop in Azure HDInsight. Per ottenere istruzioni, vedere [Provisioning di cluster HDInsight][hdinsight-provision]. 
- È necessario aver installato Microsoft Excel 2013 o Excel 2010.
- Per importare i dati da Hive in Excel è necessario aver installato [Microsoft Hive ODBC Driver](http://www.microsoft.com/download/details.aspx?id=40886).


##Per eseguire l'esempio

1. Dal portale di Azure fare clic sul cluster nel quale si vuole eseguire l'esempio e quindi fare clic su **Query Console** nella parte inferiore. In alternativa, è possibile aprire direttamente Query Console usando l'URL seguente:

	 	https://<clustername>.azurehdinsight.net
	
	Quando richiesto, eseguire l'autenticazione con il nome utente e la password dell'amministratore usati durante il provisioning del cluster.
  
2. Nella pagina Web visualizzata, fare clic sulla scheda **Getting Started Gallery** e quindi, nella categoria **Samples**, fare clic sull'esempio **Website Log Analysis**.

3. Seguire le istruzioni fornite nella pagina Web per completare l'esempio.

##Passaggi successivi
Provare l'esempio seguente: [Analizzare i dati dei sensori mediante Hive con HDInsight](hdinsight-hive-analyze-sensor-data.md).


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
 

<!---HONumber=August15_HO6-->