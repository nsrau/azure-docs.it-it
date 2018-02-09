---
title: Installare un'applicazione pubblicata - H2O Sparkling Water - HDInsight di Azure | Microsoft Docs
description: Installare e usare l'applicazione Hadoop di terze parti H2O Sparkling Water.
services: hdinsight
documentationcenter: 
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 8734daa5303aa76e9f8a074b5f709727cabb58b2
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---h2o-sparkling-water"></a>Installare un'applicazione pubblicata - H2O Sparkling Water

Questo articolo descrive come installare ed eseguire l'applicazione Hadoop pubblicata [H20 Sparkling Water](http://www.h2o.ai/) in HDInsight di Azure. Per una panoramica della piattaforma applicativa HDInsight e un elenco delle applicazioni pubblicate di fornitori di software indipendente, vedere [Installare applicazioni Hadoop di terze parti](hdinsight-apps-install-applications.md). Per istruzioni sull'installazione di un'applicazione personalizzata, vedere l'articolo su come [installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md).

## <a name="about-h2o-sparkling-water"></a>Informazioni su H2O Sparkling Water

H2O Sparkling Water è una piattaforma di Machine Learning in memoria open-source interamente distribuita con scalabilità lineare. H2O Sparkling Water consente di combinare gli algoritmi veloci e scalabili di Machine Learning di H2O con le funzionalità di Spark. Con Sparkling Water gli utenti possono acquisire calcoli da Scala, R, e Python usando l'interfaccia utente di H2O Flow.

H2O Sparkling Water offre:

* **Interfacce familiari e interfaccia utente Web di facile utilizzo**: possibilità di configurare e iniziare rapidamente a usare il sistema grazie all'intuitiva interfaccia utente grafica Flow basata sul Web di H2O o ad ambienti di programmazione come R, Python, Java, Scala, JSON e le API di H2O.
* **Supporto indipendente dai dati di tutti i tipi di file e database comuni**: possibilità di esplorare e modellare facilmente Big Data da Microsoft Excel, R Studio, Tableau e altro ancora. Possibilità di connettersi a dati provenienti da origini dati HDFS, S3, SQL e NoSQL.
* **Analisi e munging di Big Data ad alta scalabilità**: con H2O i join su Big Data offrono prestazioni 7 volte superiori rispetto alle operazioni data.table di R e possono elaborare fino 10 miliardi x 10 miliardi di join di righe con scalabilità lineare.
* **Assegnazione di punteggi ai dati in tempo reale**: possibilità di distribuire rapidamente modelli nell'ambiente di produzione usando oggetti POJO (Plain-Old Java Object), oggetti MOJO (Model-Optimized Java Object) o l'API REST di H2O.

### <a name="resource-links"></a>Collegamenti alle risorse

* [Roadmap di progettazione di H2O.ai](https://jira.h2o.ai/)
* [Home page di H2O.ai](http://www.h2o.ai/)
* [Documentazione su H2O.ai](http://docs.h2o.ai/)
* [Supporto per H2O.ai](https://support.h2o.ai/)
* [Base di codici open-source H2O.ai](https://github.com/h2oai/)

## <a name="prerequisites"></a>Prerequisiti

Per installare questa app in un nuovo cluster HDInsight o in un cluster esistente, è necessario disporre della configurazione seguente:

* Livello cluster: Standard o Premium
* Tipo cluster: Spark
* Versione cluster: 3.5 o 3.6

## <a name="install-the-h2o-sparkling-water-published-application"></a>Installare l'applicazione pubblicata H2O Sparkling Water

Per istruzioni dettagliate sull'installazione di questa e delle altre applicazioni di fornitori di software indipendenti disponibili, vedere [Installare applicazioni Hadoop di terze parti](hdinsight-apps-install-applications.md).

## <a name="launch-h2o-sparkling-water"></a>Avviare H2O Sparkling Water

1. Dopo l'installazione, è possibile iniziare a usare H2O Sparkling Water (h2o-sparklingwater) dal cluster nel portale di Azure aprendo Notebook di Jupyter (`https://<ClusterName>.azurehdinsight.net/jupyter`). Per accedere a Jupyter è possibile anche selezionare **Dashboard cluster** dal pannello del cluster nel portale e quindi selezionare **Notebook di Jupyter**. Viene richiesto di immettere le credenziali. Immettere le credenziali di Hadoop del cluster specificate durante la creazione del cluster.

2. In Jupyter vengono visualizzate tre cartelle: H2O-PySparkling-Examples, PySpark Examples e Scala Examples. Selezionare la cartella **H2O-PySparkling-Examples**.

    ![Pagina iniziale di Notebook di Jupyter](./media/hdinsight-apps-install-h2o/jupyter-home.png)

3. Quando si crea un nuovo notebook, il primo passaggio consiste nella creazione dell'ambiente Spark. Queste informazioni sono incluse nell'esempio **Sentiment_analysis_with_Sparkling_Water**. Quando si configura l'ambiente Spark, assicurarsi di usare il file JAR corretto e di specificare l'indirizzo IP fornito dall'output della prima cella.

    ![Pagina iniziale di Notebook di Jupyter](./media/hdinsight-apps-install-h2o/spark-config.png)

4. Avviare il cluster H2O.

    ![Avvio del cluster](./media/hdinsight-apps-install-h2o/start-cluster.png)

5. Quando il cluster H2O è operativo, aprire H2O Flow accedendo a **`https://<ClusterName>-h2o.apps.azurehdinsight.net:443`**.

    > [!NOTE]
    > Se non è possibile aprire H2O Flow, provare a cancellare la cache del browser. Se il sito continua a non essere raggiungibile, è probabile che non si disponga di risorse sufficienti nel cluster. Provare ad aumentare il numero di nodi di ruolo di lavoro sotto l'opzione **Ridimensiona cluster** nel riquadro del cluster.

    ![Dashboard di H2O Flow](./media/hdinsight-apps-install-h2o/h2o-flow.png)

6. Selezionare l'esempio **Million_Songs.flow** dal menu a destra. Quando richiesto con un messaggio di avviso, fare clic su **Load Notebook** (Carica Notebook). Questa demo è stata progettata per essere eseguita in pochi minuti con dati reali. L'obiettivo consiste nello stimare dai dati se il brano è stato rilasciato prima o dopo il 2004 usando la classificazione binaria.

    ![Selezionare Million_Songs.flow](./media/hdinsight-apps-install-h2o/million-songs.png)

7. Trovare il percorso contenente **milsongs-cls-train.csv.gz** e sostituire l'intero percorso con **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-train.csv.gz**.

8. Trovare il percorso contenente **milsongs-cls-test.csv.gz** e sostituirlo con **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-test.csv.gz**.

9. Per eseguire tutte le istruzioni all'interno delle celle del notebook, selezionare il pulsante **Run all** (Esegui tutto) sulla barra degli strumenti.

    ![Esegui tutto](./media/hdinsight-apps-install-h2o/run-all.png)

10. Dopo alcuni minuti, viene visualizzato un output simile al seguente.

    ![Output](./media/hdinsight-apps-install-h2o/output.png)

L'operazione è terminata. In pochi minuti sono state applicate funzionalità di intelligenza artificiale in Spark. È possibile ora esplorare altri esempi in H2O Flow che illustrano diversi tipi di algoritmi di Machine Learning.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione su H2O](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/index.html)
* [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md): informazioni su come distribuire in HDInsight un'applicazione HDInsight non pubblicata.
* [Pubblicare applicazioni HDInsight](hdinsight-apps-publish-applications.md): informazioni su come pubblicare applicazioni HDInsight personalizzate in Azure Marketplace.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: Installare un'applicazione HDInsight): informazioni su come definire le applicazioni HDInsight.
* [Personalizzare cluster HDInsight basati su Linux tramite Azioni script](hdinsight-hadoop-customize-cluster-linux.md): informazioni su come usare Azioni script per installare applicazioni aggiuntive.
* [Usare nodi perimetrali vuoti in HDInsight](hdinsight-apps-use-edge-node.md): informazioni su come usare un nodo perimetrale vuoto per l'accesso a cluster HDInsight e per il test e l'hosting di applicazioni HDInsight.
