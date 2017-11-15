---
title: Installare applicazioni Hadoop di terze parti in Azure HDInsight | Microsoft Docs
description: Informazioni su come installare applicazioni Hadoop di terze parti in Azure HDInsight.
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: eaf5904d-41e2-4a5f-8bec-9dde069039c2
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/06/2017
ms.author: jgao
ms.openlocfilehash: b23e62d3ae0fa3468a8a9a5608eb3d316852f086
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="install-third-party-hadoop-applications-on-azure-hdinsight"></a>Installare applicazioni Hadoop di terze parti in Azure HDInsight

Informazioni su come installare applicazioni Hadoop di terze parti in Azure HDInsight. Per istruzioni sull'installazione di un'applicazione personalizzata, vedere l'articolo su come [installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md).

Un'applicazione HDInsight è un'applicazione che gli utenti possono installare in un cluster HDInsight. Queste applicazioni possono essere sviluppate da Microsoft, da fornitori di software indipendenti (ISV) o dall'utente.  

L'elenco seguente mostra le applicazioni pubblicate:

* **AtScale Intelligence Platform** attiva il cluster HDInsight in un server OLAP scale-out. L'applicazione consente di eseguire query a miliardi di righe di dati in modo interattivo tramite gli strumenti di business intelligence di Microsoft Excel, PowerBI, Tableau Software per QlikView.
* **Cask CDAP for HDInsight**: offre la prima piattaforma di integrazione unificata per Big Data che riduce i tempi per la produzione di applicazioni dati e Data Lake dell'80%. Questa applicazione supporta solo i cluster Standard HBase 3.4.
* **DATAIKU DDS on HDInsight**: consente ai professionisti che lavorano con i dati di creare prototipi, compilare e distribuire servizi altamente specifici che trasformano i dati non elaborati in stime aziendali di grande impatto.
* **Datameer**: [Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft) offre agli analisti un modo interattivo per rilevare, analizzare e visualizzare i risultati nei Big Data. Consente di effettuare il pull di altre origini dati con facilità per individuare nuove relazioni e ottenere rapidamente le risposte necessarie.
* **H2O Artificial Intelligence for HDInsight (Beta)**: H2O Sparkling Water supporta gli algoritmi distribuiti GLM, Naïve Bayes, Distributed Random Forest, Gradient Boosting Machine, Deep Neural Networks, Deep learning, K-Means, PCA, Generalized Low Rank Models, Anomaly Detection e Autoencoders.
* **Kyligence Analytics Platform**: Kyligence Analytics Platform (KAP) è un data warehouse di livello aziendale con tecnologia Apache Kylin e Apache Hadoop che supporta una latenza di query inferiore al secondo su set di dati di enormi dimensioni e semplifica l'analisi dei dati per gli utenti business e gli analisti. 
* **Preparazione dei dati self-service di Paxata**
* **SnapLogic Hadooplex**: l'esecuzione di SnapLogic Hadooplex in HDInsight consente ai clienti di ottenere più rapidamente informazioni aziendali approfondite offrendo funzionalità di preparazione e inserimento self-service di dati da quasi tutte le origini alla piattaforma cloud Microsoft Azure.
* **Spark Job Server for KNIME Spark Executor**: Spark Job Server for KNIME Spark Executor consente di connettere la piattaforma di analisi KNIME ai cluster HDInsight.
* **Streamsets Data Collector for HDnsight** offre un ambiente di sviluppo integrato (IDE) completo che consente di progettare, testare, distribuire e gestire pipeline di inserimento any-to-any che uniscono dati batch e del flusso e includono varie trasformazioni all'interno del flusso, senza dover scrivere codice personalizzato. 
* **WANdisco Fusion HDI App** consente la connettività coerente e continua ai dati ovunque. Fornisce l'accesso ai dati in qualsiasi momento e in qualsiasi posizione senza tempi di inattività e interruzioni del servizio.

Nelle istruzioni illustrate in questo articolo si usa il portale di Azure. È anche possibile esportare il modello di Azure Resource Manager dal portale oppure ottenere una copia del modello di Resource Manager dai fornitori e usare l'interfaccia della riga di comando di Azure e Azure PowerShell per distribuire il modello.  Vedere [Creare cluster Hadoop in HDInsight mediante modelli di Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Prerequisiti
Per installare applicazioni HDInsight in un cluster HDInsight esistente, è necessario un cluster HDInsight. Per crearne uno, vedere [Creare cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). È anche possibile installare applicazioni HDInsight quando si crea un cluster HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Installare applicazioni in cluster esistenti
La procedura seguente illustra come installare applicazioni HDInsight in un cluster HDInsight esistente.

**Per installare un'applicazione HDInsight**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Cluster HDInsight** nel menu a sinistra.
3. Fare clic su un cluster HDInsight.  Se non ci sono cluster disponibili, è necessario crearne uno.  Vedere [Creare cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Fare clic su **Applicazioni** nella categoria **Configurazioni**. Verrà visualizzato un elenco delle applicazioni installate. Se non è possibile trovare Applicazioni, significa che non sono disponibili applicazioni per questa versione del cluster HDInsight.
   
    ![Menu del portale Applicazioni di HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Fare clic su **Aggiungi** nel menu. 
   
    ![App installate in Applicazioni di HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps.png)
   
    Verrà visualizzato un elenco delle applicazioni HDInsight esistenti.
   
    ![Applicazioni disponibili in Applicazioni di HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Fare clic su una delle applicazioni, accettare le condizioni legali e quindi fare clic su **Seleziona**.

È possibile vedere lo stato di installazione dell'applicazione dalle notifiche del portale facendo clic sull'icona a forma di campana nella parte superiore del portale. Dopo l'installazione dell'applicazione, questa verrà visualizzata nel pannello App installate.

## <a name="install-applications-during-cluster-creation"></a>Installare applicazioni durante la creazione del cluster
È possibile installare applicazioni HDInsight quando si crea un cluster. Durante il processo, le applicazioni HDInsight vengono installate dopo la creazione del cluster e mentre è nello stato in corso di esecuzione. Per installare le applicazioni durante la creazione del cluster tramite il portale di Azure, si usa l'opzione --Personalizzato-- anziché il valore predefinito --Creazione rapida--.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Elencare le app HDInsight installate e le proprietà
Il portale include un elenco delle applicazioni HDInsight installate per un cluster e le proprietà di ognuna.

**Per elencare le applicazioni HDInsight e visualizzare le proprietà**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **Cluster HDInsight** nel menu a sinistra. 
3. Fare clic su un cluster HDInsight.
4. Da **Impostazioni** fare clic su **Applicazioni** nella categoria **Generale**. Le App installate sono elencate sulla destra. 
   
    ![App installate in Applicazioni di HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Fare clic su una delle applicazioni installate per visualizzare la proprietà. Elenchi delle proprietà:
   
   * Nome dell'app: nome all'applicazione.
   * Stato: stato dell'applicazione. 
   * Pagina Web: URL dell'applicazione Web distribuita nel nodo perimetrale. Le credenziali sono le stesse usate per le credenziali utente HTTP configurate per il cluster.
   * Endpoint HTTP: le credenziali sono le stesse usate per le credenziali utente HTTP configurate per il cluster. 
   * Endpoint SSH: è possibile usare SSH per connettersi al nodo perimetrale. Le credenziali SSH sono le stesse usate per le credenziali utente SSH configurate per il cluster. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).
6. Per eliminare un'applicazione, fare clic con il pulsante destro del mouse sull'applicazione e quindi scegliere **Elimina** dal menu di scelta rapida.

## <a name="connect-to-the-edge-node"></a>Connettersi al nodo perimetrale
È possibile connettersi al nodo perimetrale tramite HTTP e SSH. Le informazioni sull'endpoint sono disponibili nel [portale](#list-installed-hdinsight-apps-and-properties). Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Le credenziali dell'endpoint HTTP sono le credenziali utente HTTP configurate per il cluster HDInsight. Le credenziali dell'endpoint SSH sono le credenziali SSH configurate per il cluster HDInsight.

## <a name="troubleshoot"></a>Risoluzione dei problemi
Vedere [Risolvere i problemi di installazione](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Passaggi successivi
* [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md): informazioni su come distribuire in HDInsight un'applicazione HDInsight non pubblicata.
* [Pubblicare applicazioni HDInsight](hdinsight-apps-publish-applications.md): informazioni su come pubblicare applicazioni HDInsight personalizzate in Azure Marketplace.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: Installare un'applicazione HDInsight): informazioni su come definire le applicazioni HDInsight.
* [Personalizzare cluster HDInsight basati su Linux tramite Azioni script](hdinsight-hadoop-customize-cluster-linux.md): informazioni su come usare Azioni script per installare applicazioni aggiuntive.
* [Creare cluster Hadoop basati su Linux in HDInsight tramite modelli ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informazioni su come chiamare i modelli di Azure Resource Manager per creare cluster HDInsight.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md)(Usare nodi perimetrali vuoti in HDInsight): informazioni su come usare un nodo perimetrale vuoto per l'accesso a cluster HDInsight, il test di applicazioni HDInsight e l'hosting di applicazioni HDInsight.

