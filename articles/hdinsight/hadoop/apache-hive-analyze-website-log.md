---
title: Usare Hive con Hadoop per l'analisi dei log dei siti Web - Azure HDInsight
description: Informazioni su come usare Hive con HDInsight per analizzare i log dei siti Web. Si userà un file di log come input in una tabella di HDInsight e HiveQL per eseguire query sui dati.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/17/2016
ms.author: jasonh
ROBOTS: NOINDEX
ms.openlocfilehash: 47175ed4bc01a43d1f21cbfc84b8a788f11b7338
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048025"
---
# <a name="use-hive-with-windows-based-hdinsight-to-analyze-logs-from-websites"></a>Usare Hive con HDInsight basato su Windows per analizzare i log dei siti Web
Informazioni su come usare HiveQL con HDInsight per analizzare i log di un sito Web. L'analisi dei log dei siti Web può essere usata per segmentare il proprio pubblico in base ad attività simili, classificare i visitatori di un sito in base a dati demografici e scoprire i contenuti che visualizzano, da quali siti Web sono stati indirizzati e così via.

> [!IMPORTANT]
> I passaggi descritti in questo documento funzionano solo con i cluster HDInsight basati su Windows. HDInsight è disponibile in Windows solo per le versioni precedenti a HDInsight 3.4. Linux è l'unico sistema operativo usato in HDInsight versione 3.4 o successiva. Per altre informazioni, vedere la sezione relativa al [ritiro di HDInsight in Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

In questo esempio si usa un cluster HDInsight per analizzare i file di log dei siti Web e ottenere informazioni dettagliate sulla frequenza delle visite da siti Web esterni nell'arco di una giornata. Si genererà inoltre un riepilogo degli errori del sito Web riscontrati dagli utenti. Si apprenderà come:

* Connettersi a un archivio BLOB di Azure, che contiene i file di log del sito Web.
* Creare tabelle HIVE per eseguire query su tali log.
* Creare query HIVE per analizzare i dati.
* Usare Microsoft Excel per connettersi a HDInsight (mediante una connessione ODBC) per recuperare i dati analizzati.

![HDI.Samples.Website.Log.Analysis](./media/apache-hive-analyze-website-log/hdinsight-weblogs-sample.png)

## <a name="prerequisites"></a>Prerequisiti
* È necessario avere completato il provisioning di un cluster Hadoop in Azure HDInsight. Per istruzioni, vedere [Provisioning del cluster HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* È necessario aver installato Microsoft Excel 2013 o Excel 2010.
* Per importare i dati da Hive in Excel è necessario aver installato [Microsoft Hive ODBC Driver](http://www.microsoft.com/download/details.aspx?id=40886) .

## <a name="to-run-the-sample"></a>Per eseguire l'esempio
1. Dal [portale di Azure](https://portal.azure.com/), nella schermata iniziale, se il cluster è stato aggiunto lì, fare clic sul riquadro del cluster in cui si desidera eseguire l'esempio.
2. Dal pannello del cluster, in **Collegamenti rapidi**, fare clic su **Dashboard cluster**, quindi dal pannello **Dashboard cluster** fare clic su **Dashboard cluster HDInsight**. In alternativa, è possibile aprire direttamente la dashboard usando l'URL seguente:

         https://<clustername>.azurehdinsight.net

    Quando richiesto, eseguire l'autenticazione con il nome utente e la password dell'amministratore usati durante il provisioning del cluster.
3. Nella pagina Web visualizzata fare clic sulla scheda **Getting Started Gallery** (Guida introduttiva) e quindi nella categoria **Solutions with Sample Data** (Soluzioni con dati di esempio) fare clic sull'esempio **Analisi dei log del sito Web**.
4. Seguire le istruzioni fornite nella pagina Web per completare l'esempio.

## <a name="next-steps"></a>Passaggi successivi
Provare l'esempio seguente: [Analizzare i dati dei sensori mediante Hive con HDInsight](apache-hive-analyze-sensor-data.md).

[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md
