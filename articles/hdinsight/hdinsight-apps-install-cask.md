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
ms.openlocfilehash: 4b83f2a2228ef0dd7fa56b5a71b267d1e4302620
ms.sourcegitcommit: ded74961ef7d1df2ef8ffbcd13eeea0f4aaa3219
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2018
---
# <a name="install-published-application---cask-data-application-platform-cdap"></a>Installare un'applicazione pubblicata - Cask Data Application Platform (CDAP)

Questo articolo descrive come installare ed eseguire l'applicazione Hadoop pubblicata [CDAP](http://cask.co/products/cdap/) in HDInsight di Azure. Per una panoramica della piattaforma applicativa HDInsight e un elenco delle applicazioni pubblicate di fornitori di software indipendente, vedere [Installare applicazioni Hadoop di terze parti](hdinsight-apps-install-applications.md). Per istruzioni sull'installazione di un'applicazione personalizzata, vedere l'articolo su come [installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md).

## <a name="about-cdap"></a>Informazioni su CDAP

Lo sviluppo di applicazioni in Hadoop può risultare piuttosto complesso.  È infatti disponibile un numero elevato e crescente di estensioni per tecnologie Hadoop, la cui integrazione può richiedere molto tempo. Per il monitoraggio del flusso di dati e la raccolta delle metriche, ad esempio, è possibile che sia necessario creare una soluzione distinta.

### <a name="how-does-cdap-help"></a>Vantaggi offerti da CDAP

Cask Data Application Platform (CDAP) è una piattaforma di integrazione per Big Data che consente di concentrare l'attenzione sulla compilazione di applicazioni anziché sull'infrastruttura sottostante.

CDAP usa concetti e astrazioni generali con cui gli sviluppatori hanno familiarità. Queste astrazioni nascondono le complessità dei sistemi interni e incoraggiano il riutilizzo delle soluzioni.

Un'estensione CDAP denominata [Cask Hydrator](http://cask.co/products/hydrator/) offre un'interfaccia utente per sviluppare e gestire pipeline di dati. Una pipeline di dati è costituita da vari *plug-in grado di eseguire attività come l'acquisizione di dati, la trasformazione, l'analisi e operazioni post-esecuzione.

Ogni plug-in CDAP presenta un'interfaccia ben definita in modo che, per valutare tecnologie diverse, sia sufficiente sostituire un plug-in con un altro, senza toccare il resto dell'applicazione.

Le *pipeline* CDAP offrono un flusso grafico generico dei dati dell'applicazione. Questa visualizzazione mostra il flusso end-to-end dei dati, dall'inserimento alle fasi di trasformazione e di analisi fino al trasferimento in un archivio dati esterno.

Nell'esempio di pipeline di dati seguente vengono inseriti dati Twitter in tempo reale e vengono quindi esclusi alcuni tweet in base a criteri predefiniti. La pipeline di dati trasforma i dati dei tweet non elaborati e li converte in un formato più leggibile, quindi raggruppa i tweet in base a un set di valori e scrive i risultati in un archivio HBase.

![Pipeline CDAP](./media/hdinsight-apps-install-cask/pipeline.png)

Questa pipeline end-to-end è stata creata usando **l'interfaccia utente di Cask Hydrator**, l'interfaccia del plug-in corrispondente e la funzionalità di trascinamento della selezione per formare connessioni tra le fasi. È possibile isolare e modificare la funzionalità di ogni plug-in modo indipendente. Con CDAP è possibile creare e convalidare pipeline simili nell'arco di poche ore, mentre in un tipico ambiente Hadoop, la compilazione di queste soluzioni può richiedere diversi giorni.

CDAP offre anche un'estensione denominata [Cask Tracker](http://cask.co/products/tracker/) che consente di tracciare visivamente i dati mentre vengono trasmessi attraverso l'applicazione. Cask Tracker aggiunge funzionalità di *data governance* al sistema, in modo che gli asset di dati possano essere formalmente gestiti attraverso l'applicazione. In questo modo, ad esempio, è possibile tenere traccia della derivazione di ogni punto dati, raccogliere le metriche pertinenti e controllare il percorso dei dati attraverso il processo.

Di seguito è riportata un'illustrazione del flusso di dati nella pipeline precedente:

![Tracciato CDAP](./media/hdinsight-apps-install-cask/tracker.png)

## <a name="prerequisites"></a>Prerequisiti

Per installare questa app in un nuovo cluster HDInsight o in un cluster esistente, è necessario disporre della configurazione seguente:

* Livello cluster: standard
* Tipo cluster: HBase
* Versione cluster: 3.4, 3.5

## <a name="install-the-cdap-published-application"></a>Installare l'applicazione pubblicata CDAP

Per istruzioni dettagliate sull'installazione di questa e delle altre applicazioni di fornitori di software indipendenti disponibili, vedere [Installare applicazioni Hadoop di terze parti](hdinsight-apps-install-applications.md).

## <a name="launch-cdap"></a>Avviare CDAP

1. Dopo l'installazione, avviare CDAP dal cluster nel portale di Azure accedendo al riquadro **Impostazioni** e quindi selezionando **Applicazioni** nella categoria **Generale**. Il riquadro App installate elenca tutte le applicazioni installate.

    ![App CDAP installata](./media/hdinsight-apps-install-cask/cdap-app.png)

2. Quando si seleziona CDAP, viene visualizzato un collegamento alla pagina Web e all'endpoint HTTP e il percorso dell'endpoint SSH. Selezionare il collegamento WEBPAGE.

3. Quando richiesto, immettere le credenziali di amministratore del cluster.

    ![Authentication](./media/hdinsight-apps-install-cask/auth.png)

4. Dopo aver eseguito l'accesso, viene visualizzata la pagina iniziare dell'interfaccia utente di Cask CDAP.

    ![Pagina iniziale interfaccia utente di Cask](./media/hdinsight-apps-install-cask/gui.png)

5. Per esplorare l'interfaccia di CDAP, fare clic sul collegamento di menu **Cask Market** (Mercato Cask) nella parte superiore della pagina.

    ![Collegamento Cask Market](./media/hdinsight-apps-install-cask/cask-market.png)

6. Selezionare **Access Log Sample** (Log di accesso di esempio) dall'elenco.

    ![Log di accesso di esempio](./media/hdinsight-apps-install-cask/market-log-sample.png)

7. Fare clic su **Load** (Carica) per confermare.

    ![Fare clic su Load (Carica)](./media/hdinsight-apps-install-cask/market-load.png)

8. Viene visualizzata una schermata dei dati di esempio inclusi. Selezionare **Avanti**.

    ![Log di accesso di esempio - Vista dati](./media/hdinsight-apps-install-cask/market-view-data.png)

9. Selezionare **Stream** (Flusso) come tipo di destinazione, immettere un nome per la destinazione e quindi selezionare **Finish** (Fine).

    ![Log di accesso di esempio - Selezione destinazione](./media/hdinsight-apps-install-cask/market-destination.png)

10. Dopo che il datapack è stato caricato, selezionare **View Stream Details** (Visualizza dettagli flusso).

    ![Datapack caricato](./media/hdinsight-apps-install-cask/market-view-details.png)

11. Per abilitare i metadati per lo spazio dei nomi, selezionare **Enable** (Abilita) nella scheda Usage (Utilizzo) della pagina dei dettagli del log di accesso.

    ![Log di accesso di esempio - Caricato - Abilitazione metadati](./media/hdinsight-apps-install-cask/log-loaded.png)

12. Dopo aver abilitato i metadati, viene visualizzato un grafico con informazioni sul messaggio di controllo.

    ![Log di accesso di esempio - Metadati abilitati](./media/hdinsight-apps-install-cask/log-metadata.png)

13. Per esplorare i dati del log, selezionare l'icona **Explore** nella parte superiore della pagina.

    ![Log di accesso di esempio - Esplorazione](./media/hdinsight-apps-install-cask/log-explore.png)

14. Viene visualizzata una query SQL di esempio. Modificarla come desiderato e quindi selezionare **Execute** (Esegui).

    ![Log di accesso di esempio - Esplorazione del set di dati con una query](./media/hdinsight-apps-install-cask/log-query.png)

15. Al termine della query, selezionare l'icona **View** (Visualizza) nella colonna Azioni.

    ![Log di accesso di esempio - Visualizzazione query completata](./media/hdinsight-apps-install-cask/log-query-view.png)

16. Vengono visualizzati i risultati della query.

    ![Log di accesso di esempio - Risultati della query](./media/hdinsight-apps-install-cask/log-query-results.png)

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione su Cask](http://cask.co/resources/documentation/).
* [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md): informazioni su come distribuire in HDInsight un'applicazione HDInsight non pubblicata.
* [Pubblicare applicazioni HDInsight](hdinsight-apps-publish-applications.md): informazioni su come pubblicare applicazioni HDInsight personalizzate in Azure Marketplace.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: Installare un'applicazione HDInsight): informazioni su come definire le applicazioni HDInsight.
* [Personalizzare cluster HDInsight basati su Linux tramite Azioni script](hdinsight-hadoop-customize-cluster-linux.md): informazioni su come usare Azioni script per installare applicazioni aggiuntive.
* [Usare nodi perimetrali vuoti in HDInsight](hdinsight-apps-use-edge-node.md): informazioni su come usare un nodo perimetrale vuoto per l'accesso a cluster HDInsight e per il test e l'hosting di applicazioni HDInsight.
