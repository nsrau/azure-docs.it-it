---
title: Installare un'applicazione pubblicata - StreamSets Data Collector - HDInsight di Azure | Microsoft Docs
description: Installare e usare l'applicazione Hadoop di terze parti StreamSets Data Collector.
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
ms.openlocfilehash: 90775452c58457ae8ecc73687a375606474158f5
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---streamsets-data-collector"></a>Installare un'applicazione pubblicata - StreamSets Data Collector

Questo articolo descrive come installare ed eseguire l'applicazione Hadoop pubblicata [StreamSets Data Collector per HDInsight](https://streamsets.com/) in HDInsight di Azure. Per una panoramica della piattaforma applicativa HDInsight e un elenco delle applicazioni pubblicate di fornitori di software indipendente, vedere [Installare applicazioni Hadoop di terze parti](hdinsight-apps-install-applications.md). Per istruzioni sull'installazione di un'applicazione personalizzata, vedere l'articolo su come [installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md).

## <a name="about-streamsets-data-collector"></a>Informazioni su StreamSets Data Collector

StreamSets Data Collector viene distribuito di serie con un'applicazione Azure HDInsight. Offre un ambiente di sviluppo integrato completo che consente di progettare, testare, distribuire e gestire pipeline di inserimento any-to-any che uniscono dati batch e del flusso e includono varie trasformazioni all'interno del flusso, senza dover scrivere codice personalizzato.

StreamSets Data Collector consente di creare flussi dati usando numerosi componenti Big Data come HDFS, Kafka, Solr, Hive, HBASE e Kudu. Quando StreamSets Data Collector viene eseguito su un server perimetrale o su un cluster Hadoop, è possibile ottenere il monitoraggio in tempo reale sia delle anomalie dei dati sia delle operazioni sui flussi dati. Il monitoraggio include avvisi basati sulla soglia, rilevamento delle anomalie e correzione automatica di record di errore.

Progettato per isolare logicamente ogni fase di una pipeline, StreamSets Data Collector consente di soddisfare nuovi requisiti di business offrendo la possibilità di aggiungere nuovi processori e connettori senza scrivere codice e con un tempo di inattività minimo.

### <a name="streamsets-resource-links"></a>Collegamenti alle risorse di StreamSets

* [Documentazione](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html)
* [Blog](https://streamsets.com/blog/)
* [Esercitazioni](https://github.com/streamsets/tutorials)
* [Forum di supporto per sviluppatori](https://groups.google.com/a/streamsets.com/forum/#!forum/sdc-user)
* [Canale StreamSets pubblico di Slack](https://streamsetters.slack.com/)
* [Codice sorgente](https://github.com/streamsets)

## <a name="prerequisites"></a>Prerequisiti

Per installare questa app in un nuovo cluster HDInsight o in un cluster esistente, è necessario disporre della configurazione seguente:

* Livello cluster: Standard o Premium
* Versione cluster: 3.5 o successiva

## <a name="install-the-streamsets-data-collector-published-application"></a>Installare l'applicazione pubblicata StreamSets Data Collector

Per istruzioni dettagliate sull'installazione di questa e delle altre applicazioni di fornitori di software indipendenti disponibili, vedere [Installare applicazioni Hadoop di terze parti](hdinsight-apps-install-applications.md).

## <a name="launch-streamsets-data-collector"></a>Avviare StreamSets Data Collector

1. Dopo l'installazione, è possibile avviare StreamSets dal cluster nel portale di Azure accedendo al riquadro **Impostazioni** e quindi selezionando **Applicazioni** nella categoria **Generale**. Il riquadro App installate elenca le applicazioni installate.

    ![App StreamSets installata](./media/hdinsight-apps-install-streamsets/streamsets.png)

2. Quando si seleziona StreamSets Data Collector, viene visualizzato un collegamento alla pagina Web e il percorso dell'endpoint SSH. Selezionare il collegamento WEBPAGE.

3. Nella finestra di dialogo di accesso usare le credenziali seguenti per accedere: `admin` e `admin`.

4. Nella pagina delle attività iniziali fare clic su **Create new pipeline** (Crea nuova pipeline).

    ![Crea nuova pipeline](./media/hdinsight-apps-install-streamsets/get-started.png)

5. Nella finestra della nuova pipeline immettere un nome per la pipeline ("Hello World"), aggiungere una descrizione (facoltativa) e selezionare **Save** (Salva).

6. Viene visualizzata la console dell'agente di raccolta dati. Nel pannello delle proprietà vengono visualizzate le proprietà della pipeline.
 
    ![Console dell'agente di raccolta dati](./media/hdinsight-apps-install-streamsets/pipeline-canvas.png)

7. A questo punto è possibile eseguire l'[esercitazione su StreamSets](https://streamsets.com/documentation/datacollector/latest/help/#Tutorial/Tutorial-title.html), in cui vengono fornite istruzioni dettagliate per la creazione della prima pipeline.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione su StreamSets Data Collector](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html#concept_htw_ghg_jq).
* [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md): informazioni su come distribuire in HDInsight un'applicazione HDInsight non pubblicata.
* [Pubblicare applicazioni HDInsight](hdinsight-apps-publish-applications.md): informazioni su come pubblicare applicazioni HDInsight personalizzate in Azure Marketplace.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: Installare un'applicazione HDInsight): informazioni su come definire le applicazioni HDInsight.
* [Personalizzare cluster HDInsight basati su Linux tramite Azioni script](hdinsight-hadoop-customize-cluster-linux.md): informazioni su come usare Azioni script per installare applicazioni aggiuntive.
* [Usare nodi perimetrali vuoti in HDInsight](hdinsight-apps-use-edge-node.md): informazioni su come usare un nodo perimetrale vuoto per l'accesso a cluster HDInsight e per il test e l'hosting di applicazioni HDInsight.
