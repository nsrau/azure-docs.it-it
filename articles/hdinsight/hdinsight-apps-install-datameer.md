---
title: Installare un'applicazione pubblicata - Datameer - HDInsight di Azure | Microsoft Docs
description: Installare e usare l'applicazione Hadoop di terze parti Datameer.
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
ms.openlocfilehash: 8a898b4f82cf2d7e05e8c3895e5eddd8cf02b173
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---datameer"></a>Installare un'applicazione pubblicata - Datameer

Questo articolo descrive come installare ed eseguire l'applicazione Hadoop pubblicata [Datameer](https://www.datameer.com/) in HDInsight di Azure. Per una panoramica della piattaforma applicativa HDInsight e un elenco delle applicazioni pubblicate di fornitori di software indipendente, vedere [Installare applicazioni Hadoop di terze parti](hdinsight-apps-install-applications.md). Per istruzioni sull'installazione di un'applicazione personalizzata, vedere l'articolo su come [installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md).

## <a name="about-datameer"></a>Informazioni su Datameer

Datameer è un'applicazione nativa per la piattaforma Hadoop che estende le funzionalità esistenti di HDInsight di Azure e fornisce strumenti di integrazione rapida, preparazione e analisi di dati strutturati e non strutturati. Datameer può accedere a più di 70 origini e formati: strutturati, semistrutturati e non strutturati. È possibile caricare direttamente i dati oppure usare i rispettivi collegamenti univoci per eseguire il pull dei dati su richiesta. La familiare interfaccia basata su foglio di calcolo e le funzionalità self-service di Datameer contribuiscono a ridurre la complessità delle tecnologie di Big Data e ad accelerare il tempo di analisi. L'interfaccia basata su foglio di calcolo, in particolare, offre un meccanismo semplice per immettere formule dichiarative che vengono quindi convertite in processi Hadoop ottimizzati. Combinando Datameer con le competenze personali in Excel e strumenti di Business Intelligence, è possibile usare rapidamente Hadoop nel cloud. Per altre informazioni, vedere la [documentazione su Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).

## <a name="prerequisites"></a>Prerequisiti

Per installare questa app in un nuovo cluster HDInsight o in un cluster esistente, è necessario disporre della configurazione seguente:

* Livello cluster: standard
* Tipo cluster: Hadoop
* Versione cluster: 3.4

## <a name="install-the-datameer-published-application"></a>Installare l'applicazione pubblicata Datameer

Per istruzioni dettagliate sull'installazione di questa e delle altre applicazioni di fornitori di software indipendenti disponibili, vedere [Installare applicazioni Hadoop di terze parti](hdinsight-apps-install-applications.md).

## <a name="launch-datameer"></a>Avviare Datameer

1. Dopo l'installazione, è possibile avviare Datameer dal cluster nel portale di Azure accedendo al riquadro **Impostazioni** e quindi facendo clic su **Applicazioni** nella categoria **Generale**. Il riquadro App installate elenca le applicazioni installate.

    ![App installata Datameer](./media/hdinsight-apps-install-datameer/datameer-app.png)

2. Quando si seleziona Datameer, viene visualizzato un collegamento alla pagina Web e il percorso dell'endpoint SSH. Selezionare il collegamento WEBPAGE.

3. Al primo avvio, sono disponibili due opzioni di licenza: una versione di valutazione gratuita di 14 giorni o l'attivazione di una licenza esistente.

    ![Opzioni di licenza](./media/hdinsight-apps-install-datameer/license.png)

4. Dopo aver completato l'opzione di licenza selezionata, verrà visualizzato un modulo di accesso. Immettere le credenziali predefinite visualizzate prima del modulo di accesso. Dopo aver eseguito l'accesso, accettare il contratto software per continuare.

    ![Login](./media/hdinsight-apps-install-datameer/login.png)

La procedura seguente mostra una dimostrazione di "Hello World".

1. [Scaricare il file CSV di esempio](https://datameer.box.com/s/wzzw27za3agic4yjj8zrn6vfrph0ppnf).

2. Fare clic sul segno **+** nella parte superiore del dashboard di Datameer e fare clic su **File Upload** (Caricamento file).

    ![Caricamento file](./media/hdinsight-apps-install-datameer/upload.png)

3. Nella finestra di dialogo di caricamento, cercare e selezionare il file **Hello World.csv** scaricato. Verificare che **File Type** (Tipo file) sia impostato su CSV / TSV. Selezionare **Avanti**. Tenere premuto **Next** (Avanti) fino a quando non si raggiunge la fine della procedura guidata.

    ![Caricamento file](./media/hdinsight-apps-install-datameer/upload-browse.png)

4. Assegnare al file il nome **Hello World** in una nuova cartella. Assegnare alla nuova cartella il nome "Demo". Selezionare **Salva**.

    ![Salva](./media/hdinsight-apps-install-datameer/save.png)

5. Fare di nuovo clic sul segno **+** e selezionare **Workbook** (Cartella di lavoro) per creare una nuova cartella di lavoro per i dati.

    ![Aggiungere una cartella di lavoro](./media/hdinsight-apps-install-datameer/add-workbook.png)

6. Espandere la cartella **Data** (Dati), **FileUploads** e quindi la cartella **Demo** creata durante il salvataggio del file "Hello World". Selezionare **Hello World** dall'elenco di file e quindi fare clic su **Add Data** (Aggiungi dati).

    ![Salva](./media/hdinsight-apps-install-datameer/select-file.png)

7. Verranno visualizzati i dati caricati in un'interfaccia basata su foglio di calcolo. Per selezionare un subset dei dati, selezionare il pulsante **Filter** (Filtro) sulla barra degli strumenti.

    ![Pulsante Filter (Filtro)](./media/hdinsight-apps-install-datameer/filter-button.png)

8. Nella finestra di dialogo Apply filter (Applica filtro) selezionare la colonna **City** (Città), l'operatore **equals** (uguale a) e digitare **Chicago** nella casella di testo del filtro. Selezionare la casella di controllo **Create filter in new sheet** (Crea filtro in un nuovo figlio) e quindi selezionare **Create Filter** (Crea filtro).

    ![Applica filtro](./media/hdinsight-apps-install-datameer/apply-filter.png)

9. Salvare la cartella di lavoro facendo clic **File** e quindi su **Save** (Salva). Specificare un nome, ad esempio "Cartella di lavoro Hello World".

10. Vengono visualizzate alcune opzioni per decidere come e quando eseguire la cartella di lavoro. Per il momento, lasciare tutte le opzioni sui rispetti valori predefiniti, selezionare **Start calculation process immediately after save** (Avvia il processo di calcolo immediatamente dopo il salvataggio) e quindi scegliere **Save** (Salva).

    ![Salvare la cartella di lavoro](./media/hdinsight-apps-install-datameer/save-workbook.png)

11. Datameer offre avanzati strumenti di visualizzazione. Per visualizzare i dati, creare un'infografica. Selezionare il segno **+** nella parte superiore del dashboard e quindi selezionare **Infographic** (Infografica).

    ![Aggiungere un'infografica](./media/hdinsight-apps-install-datameer/infographic-button.png)

12. Trascinare un widget a forma di grafico a barre dall'elenco dei widget sulla sinistra, come illustrato nel passaggio 1 del diagramma seguente. Spostarti sulla cartella Dati nel browser dati sulla destra, espandere la cartella di lavoro personale e quindi il foglio di lavoro aggiunto con il filtro (passaggio 2). Trascinare la colonna **Name** (Nome) nella parte superiore del grafico a barre e rilasciarla nella destinazione **Label** (Etichetta) per impostare la colonna Name (Nome) della cartella di lavoro come campo dell'etichetta del grafico (passaggio 3).

    ![Infografica](./media/hdinsight-apps-install-datameer/infographic.png)

13. Per impostare l'età come asse Y del grafico, trascinare la colonna **Age** (Età) nel campo **Data** (Dati) del grafico.

    ![Infografica](./media/hdinsight-apps-install-datameer/infographic-age.png)

Congratulazioni! È stata creata una visualizzazione dei dati senza scrivere codice. È ora possibile esplorare variazioni e visualizzazioni aggiuntive.

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione su Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).
* [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md): informazioni su come distribuire in HDInsight un'applicazione HDInsight non pubblicata.
* [Pubblicare applicazioni HDInsight](hdinsight-apps-publish-applications.md): informazioni su come pubblicare applicazioni HDInsight personalizzate in Azure Marketplace.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: Installare un'applicazione HDInsight): informazioni su come definire le applicazioni HDInsight.
* [Personalizzare cluster HDInsight basati su Linux tramite Azioni script](hdinsight-hadoop-customize-cluster-linux.md): informazioni su come usare Azioni script per installare applicazioni aggiuntive.
* [Usare nodi perimetrali vuoti in HDInsight](hdinsight-apps-use-edge-node.md): informazioni su come usare un nodo perimetrale vuoto per l'accesso a cluster HDInsight e per il test e l'hosting di applicazioni HDInsight.
