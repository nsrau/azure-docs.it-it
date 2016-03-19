<properties
	pageTitle="Analizzare i dati dei sensori con Hive e Hadoop | Microsoft Azure"
	description="Informazioni su come analizzare i dati dei sensori usando Hive Query Console con HDInsight (Hadoop) e quindi visualizzare i dati in Microsoft Excel mediante Power View."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016" 
	ms.author="larryfr"/>

#Analizzare i dati dei sensori mediante Hive Query Console su Hadoop in HDInsight

Informazioni su come analizzare i dati dei sensori usando Hive Query Console con HDInsight (Hadoop) e quindi visualizzare i dati in Microsoft Excel mediante Power View.

> [AZURE.NOTE] I passaggi descritti in questo documento funzionano solo con i cluster HDInsight basati su Windows.

In questo esempio si userà Hive per elaborare i dati cronologici prodotti dai sistemi HVAC (Heating, Ventilating and Air Conditioning, ossia riscaldamento, ventilazione e condizionamento dell'aria) per identificare sistemi che non sono in grado di mantenere in modo affidabile una temperatura impostata. Si apprenderà come:

- Creare tabelle HIVE per eseguire query su dati archiviati in file con valori delimitati da virgole (CSV, Comma-Separated Value),
- Creare query HIVE per analizzare i dati.
- Usare Microsoft Excel per connettersi a HDInsight (mediante una connessione ODBC) per recuperare i dati analizzati.
- Usare Power View per visualizzare i dati.

![Diagramma dell'architettura della soluzione](./media/hdinsight-hive-analyze-sensor-data/hvac-architecture.png)

##Prerequisiti

* Un cluster HDInsight (Hadoop): per informazioni sulla creazione di un cluster, vedere [Effettuare il provisioning di cluster Hadoop in HDInsight](hdinsight-provision-clusters.md).

* Microsoft Excel 2013

	> [AZURE.NOTE] Microsoft Excel viene usato per la visualizzazione dei dati con [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=it-IT&rs=it-IT&ad=US).

* [Microsoft Hive ODBC Driver](http://www.microsoft.com/download/details.aspx?id=40886)

##Per eseguire l'esempio

1. Aprire un Web browser e passare all'URL seguente. Sostituire `<clustername>` con il nome del cluster HDInsight.

	 	https://<clustername>.azurehdinsight.net

	Quando richiesto, eseguire l'autenticazione con il nome utente e la password dell'amministratore usati durante il provisioning di questo cluster.

2. Nella pagina Web visualizzata fare clic sulla scheda **Getting Started Gallery** e quindi, nella categoria **Solutions with Sample Data**, fare clic sull'esempio **Sensor Data Analysis**.

3. Seguire le istruzioni fornite nella pagina Web per completare l'esempio.

<!---HONumber=AcomDC_0211_2016-->