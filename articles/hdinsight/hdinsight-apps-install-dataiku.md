---
title: Installare un'applicazione pubblicata - Dataiku DDS - HDInsight di Azure | Microsoft Docs
description: Installare e usare l'applicazione Hadoop di terze parti Dataiku DDS.
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
ms.openlocfilehash: 835f433e0bf79e8bc4d9b30963196f65bc53cd0e
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---dataiku-dds"></a>Installare un'applicazione pubblicata - Dataiku DDS

Questo articolo descrive come installare ed eseguire l'applicazione Hadoop pubblicata [Dataiku DDS](https://www.dataiku.com/) in HDInsight di Azure. Per una panoramica della piattaforma applicativa HDInsight e un elenco delle applicazioni pubblicate di fornitori di software indipendente, vedere [Installare applicazioni Hadoop di terze parti](hdinsight-apps-install-applications.md). Per istruzioni sull'installazione di un'applicazione personalizzata, vedere l'articolo su come [installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md).

## <a name="about-dataiku-dss"></a>Informazioni su Dataiku DSS

Dataiku [Data Science Studio (DSS)](https://www.dataiku.com/dss/features/connectivity/) è una piattaforma collaborativa per data science che consente ai data scientist di creare e distribuire soluzioni analitiche. Offrendo DSS come applicazione di HDInsight è possibile usare il data science per compilare soluzioni Big Data ed eseguirle in scala e a livello aziendale.

È possibile usare DSS per implementare una soluzione di analisi completa, che includa inserimento dei dati, preparazione ed elaborazione. Una soluzione DSS può includere anche funzioni di formazione e applicazione di modelli di Machine Learning, di visualizzazione e di messa in funzione.

È possibile installare DSS in HDInsight usando cluster Hadoop o Spark. È possibile installare DSS in cluster in esecuzione esistenti o durante la creazione di nuovi cluster. DSS supporta anche l'utilizzo di Archiviazione BLOB di Azure come connettore per la lettura dei dati.

È possibile usare DSS per compilare progetti, che possono quindi generare processi MapReduce o Spark. Questi processi vengono eseguiti come normali processi MapReduce o Spark in HDInsight e, quindi, è possibile ridimensionare il cluster in base alle necessità.

## <a name="prerequisites"></a>Prerequisiti

Per installare questa app in un nuovo cluster HDInsight o in un cluster esistente, è necessario disporre della configurazione seguente:

* Livello cluster: Standard o Premium
* Tipo cluster: Hadoop, Spark
* Versione cluster: 3.4, 3.5

## <a name="install-the-dataiku-dss-published-application"></a>Installare l'applicazione pubblicata Dataiku DSS

Per istruzioni dettagliate sull'installazione di questa e delle altre applicazioni di fornitori di software indipendenti disponibili, vedere [Installare applicazioni Hadoop di terze parti](hdinsight-apps-install-applications.md).

## <a name="launch-dataiku-dss"></a>Avviare Dataiku DSS

1. Dopo l'installazione, è possibile avviare DSS dal cluster nel portale di Azure accedendo al riquadro **Impostazioni** e quindi facendo clic su **Applicazioni** nella categoria **Generale**. Il riquadro App installate elenca le applicazioni installate.

    ![App installata Dataiku DSS](./media/hdinsight-apps-install-dataiku/app.png)

2. Quando si seleziona DSS in HDInsight, viene visualizzato un collegamento alla pagina Web e il percorso dell'endpoint SSH. Selezionare il collegamento WEBPAGE.

3. Al primo avvio, viene visualizzato un modulo per la creazione di un nuovo account Dataiku gratuito o per l'accesso con un account esistente. È possibile anche avviare una versione di valutazione gratuita di due settimane di [Enterprise Edition](https://www.dataiku.com/dss/editions/). A questo punto, è possibile continuare immettendo una chiave di licenza per Enterprise Edition oppure usando la Community Edition.

4. Dopo aver completato l'opzione di licenza selezionata, verrà visualizzato un modulo di accesso. Immettere le credenziali predefinite visualizzate prima del modulo di accesso.

La procedura seguente consente di eseguire una semplice dimostrazione.

1. [Scaricare il file CSV degli ordini di esempio](https://doc.dataiku.com/tutorials/data/101/haiku_shirt_sales.csv).

2. Nel dashboard di DSS selezionare il collegamento **+** (Nuovo progetto) nel menu a sinistra per creare un nuovo progetto.

    ![Collegamento Nuovo progetto](./media/hdinsight-apps-install-dataiku/new-project.png)

3. Nel modulo New project (Nuovo progetto) digitare un nome nel campo **Name** (Nome). Il campo **Project Key** (Chiave progetto) viene automaticamente compilato con un valore suggerito. In questo caso, immettere "Orders" (Ordini). Fare clic su **Create** (Crea).

    ![Modulo Nuovo progetto](./media/hdinsight-apps-install-dataiku/new-project-form.png)

4. Nella pagina del nuovo progetto selezionare **+ IMPORT YOUR FIRST DATASET** (+ IMPORTA IL PRIMO SET DI DATI).

    ![Caricamento file](./media/hdinsight-apps-install-dataiku/import-dataset.png)

5. Selezionare **Upload your files** (Carica file) nell'elenco dei set di dati **Files** (File). Viene visualizzata la finestra di dialogo di caricamento. Fare clic su Add a file (Aggiungi un file), selezionare il file `haiku_shirt_sales.csv` scaricato e convalidarlo.

6. Il file viene caricato in DSS. Verificare che DSS abbia rilevato correttamente il formato CSV facendo clic sul pulsante di anteprima.

    ![Caricamento file](./media/hdinsight-apps-install-dataiku/preview.png)

7. L'importazione è quasi perfetta. Il file CSV usa un separatore di tipo Tabulatore. È possibile osservare infatti come i dati siano in formato tabulare, con colonne denominate funzionalità e righe che rappresentano osservazioni. L'unico errore è che, apparentemente, il file contiene una riga vuota tra l'intestazione e i dati. Per correggere l'errore, immettere `1` nel campo **Skip next lines** (Ignora righe successive).

    ![Salva](./media/hdinsight-apps-install-dataiku/skip-lines.png)

8. Assegnare un nome al nuovo set di dati. Immettere **haiku_shirt_sales** nel campo disponibile nella parte superiore della schermata e quindi selezionare il pulsante **Create** (Crea).

9. Verrà visualizzata una vista tabulare dei dati in cui è possibile avviare l'esplorazione. È possibile osservare come, per ogni colonna, Dataiku Science Studio abbia rilevato un tipo di dati in _blu_: in questo caso, un valore di testo, un numero o una data (non analizzata). Un misuratore indica il rapporto della colonna in cui i valori non sembrano corrispondere al tipo (in rosso) o non sono disponibili (vuoto). In questo set di dati di esempio, il reparto presenta sia valori vuoti che dati non validi.

    ![Vista tabulare](./media/hdinsight-apps-install-dataiku/viewing-dataset.png)

## <a name="data-manipulation"></a>Manipolazione dei dati

I dati reali non sono quasi mai ordinati né ben strutturati e la pulizia dei dati richiede in genere una catena di script e la relativa logica di business. Per semplificare questa attività, Dataiku DSS fornisce uno strumento **Lab** dedicato.

1. Fare clic su **Lab** nell'angolo superiore destro.

    ![Pulsante Lab](./media/hdinsight-apps-install-dataiku/lab-button.png)

2. Viene visualizzata la finestra Lab. Il laboratorio è il luogo in cui si eseguono operazioni in modo iterativo sul set di dati per analizzarlo meglio. Questa esercitazione dimostra l'aspetto dell'analisi visiva. Selezionare il pulsante **New** (Nuovo) sotto Visual analysis (Analisi visiva). Viene chiesto di specificare un nome per l'analisi. Per il momento, lasciare il nome predefinito e fare clic su **CREATE** (Crea).

    ![Creare un laboratorio](./media/hdinsight-apps-install-dataiku/create-lab.png)

3. Selezionare il pulsante **Quick columns stats** (Statistiche rapide colonne) nell'angolo superiore destro della pagina.

    ![Statistiche rapide delle colonne](./media/hdinsight-apps-install-dataiku/quick-column-stats.png)

4. Vengono visualizzate alcune statistiche per i tipi di dati e i valori, visualizzate in grafici cronologici nel pannello **Columns quick view** (Vista rapida colonne).

    ![Vista rapida colonne](./media/hdinsight-apps-install-dataiku/columns-quick-view.png)

È possibile ora esplorare DSS usando i dati di esempio. In particolare, è possibile pulire ed eseguire operazioni sui dati e creare nuove visualizzazioni.

Per esercitazioni dettagliate, vedere [Learn Dataiku DSS](https://www.dataiku.com/learn/) (Imparare a usare Dataiku DSS).

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione su Dataiku DSS](https://doc.dataiku.com/dss/latest/).
* [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md): informazioni su come distribuire in HDInsight un'applicazione HDInsight non pubblicata.
* [Pubblicare applicazioni HDInsight](hdinsight-apps-publish-applications.md): informazioni su come pubblicare applicazioni HDInsight personalizzate in Azure Marketplace.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: Installare un'applicazione HDInsight): informazioni su come definire le applicazioni HDInsight.
* [Personalizzare cluster HDInsight basati su Linux tramite Azioni script](hdinsight-hadoop-customize-cluster-linux.md): informazioni su come usare Azioni script per installare applicazioni aggiuntive.
* [Usare nodi perimetrali vuoti in HDInsight](hdinsight-apps-use-edge-node.md): informazioni su come usare un nodo perimetrale vuoto per l'accesso a cluster HDInsight e per il test e l'hosting di applicazioni HDInsight.
