<properties 
	pageTitle="Analizzare i dati del sensore usando Hive e Microsoft Azure HDInsight (Hadoop)" 
	description="Informazioni su come usare Hive ed Excel per analizzare e visualizzare i dati del sensore con HDInsight (Hadoop)" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/30/2014" 
	ms.author="larryfr"/>

#Analisi dei dati del sensore usando Hive con HDInsight

Informazioni su come analizzare i dati del sensore usando Hive con HDInsight (Hadoop), quindi visualizzare i dati in Microsoft Excel usando Power View.

In questo esempio si userà Hive per elaborare i dati cronologici prodotti dai sistemi HVAC (Heating, Ventilating and Air Conditioning, ossia riscaldamento, ventilazione e condizionamento dell'aria) per identificare sistemi che non sono in grado di mantenere in modo affidabile una temperatura impostata. Si apprenderà come:

- Creare tabelle HIVE per l'esecuzione di query su dati archiviati in file con valori delimitati da virgole (CSV, Comma-Separated Value)
- Creare query HIVE per analizzare i dati
- Usare Microsoft Excel per connettersi a HDInsight (usando una connessione ODBC) per recuperare i dati analizzati
- Usare Power View per visualizzare i dati

![A diagram of the solution architecture](./media/hdinsight-use-hive-sensor-data-analysis/hvac-architecture.png)

##Prerequisiti:

* Un cluster HDInsight (Hadoop). Per informazioni sulla creazione di un cluster vedere [Provisioning di cluster Hadoop in HDInsight](/it-it/documentation/articles/hdinsight-provision-clusters/)

* Microsoft Excel 2013

	> [AZURE.NOTE] Microsoft Excel viene usato per la visualizzazione dei dati con [Power View](https://support.office.com/en-US/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US), attualmente disponibile solo in Windows.

* [Microsoft Hive ODBC Driver](http://www.microsoft.com/it-it/download/details.aspx?id=40886)

##Per eseguire l'esempio

1. Dal portale di gestione di Azure, fare clic sul cluster sul quale si vuole eseguire l'esempio, quindi fare clic su **Console query** nella parte inferiore. In alternativa è possibile aprire direttamente la Console query usando l'URL seguente:

	 	https://<clustername>.azurehdinsight.net

	Quando richiesto, eseguire l'autenticazione con il nome utente e la password dell'amministratore usati durante il provisioning di questo cluster.

2. Nella pagina Web che viene aperta, fare clic sulla scheda **Guida introduttiva** e quindi, sotto la categoria **Esempi**, fare clic sull'esempio **Analisi del log del sito Web**.

3. Seguire le istruzioni fornite nella pagina Web per completare l'esempio.

<!--HONumber=45--> 
