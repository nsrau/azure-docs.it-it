---
title: Installare applicazioni di terze parti in Azure HDInsight
description: Informazioni su come installare applicazioni di Apache Hadoop di terze parti in Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 68bbe22a4a6f7697c2bfab291412aae85efc32e9
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70916983"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Installare applicazioni Apache Hadoop di terze parti in Azure HDInsight

Informazioni su come installare applicazioni [Apache Hadoop](https://hadoop.apache.org/) di terze parti in Azure HDInsight. Per istruzioni sull'installazione di un'applicazione personalizzata, vedere l'articolo su come [installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md).

Un'applicazione HDInsight è un'applicazione che gli utenti possono installare in un cluster HDInsight. Queste applicazioni possono essere sviluppate da Microsoft, da fornitori di software indipendenti (ISV) o dall'utente.  

L'elenco seguente mostra le applicazioni pubblicate:

|Applicazione |Tipi di cluster | DESCRIZIONE |
|---|---|---|
|[Piattaforma AtScalena Intelligence](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/atscale.atscale) |Hadoop |AtScale consente di trasformare il cluster HDInsight in un server OLAP con scalabilità orizzontale, consentendo di eseguire query su miliardi di righe di dati in modo interattivo usando gli strumenti di business intelligence già conoscibili, personalizzati e apprezzati, da Microsoft Excel, Power BI, Tableau Software a QlikView. |
|[CDAP per HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cask.cdap-for-hdinsight) |hbase |CDAP è la prima piattaforma di integrazione unificata per Big Data che accelera il time-to-value per Hadoop e consente di fornire dati self-service. Open source ed estendibile, CDAP rimuove le barriere all'innovazione. Requisiti: 4 nodi area, min D3 V2. |
|[Datameer](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/datameer.datameer) |Hadoop |La piattaforma scalabile self-service di datameer per la preparazione, l'esplorazione e la gestione dei dati per l'analisi accelera la trasformazione dei dati multiorigine complessi in importanti informazioni aziendali, con informazioni più veloci e più intelligenti su scala aziendale. |
|[Dataiku DSS in HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS in una piattaforma data science aziendale che consente a data scientist e analisti di dati di collaborare per progettare ed eseguire nuovi prodotti e servizi dati in modo più efficiente, trasformando i dati non elaborati in stime con conseguenze. |
|[App HDI Fusion WANdisco](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Spark, HBase, Storm, Kafka |Mantenere la coerenza dei dati in un ambiente distribuito è una grande sfida per le operazioni sui dati. WANdisco Fusion, una piattaforma software di livello aziendale, risolve questo problema abilitando la coerenza dei dati non strutturati in qualsiasi ambiente. |
|[SparklingWater H2O per HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling water supporta gli algoritmi distribuiti seguenti: GLM, Naive Bayes, foresta casuale distribuita, computer con boosting a gradienti, reti neurali profonde, apprendimento avanzato, K-medie, PCA, modelli generalizzati di rango basso, rilevamento di anomalie, autocodificatori. |
|[StriIm per l'integrazione dei dati in tempo reale con HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/striim.hdinsightintegration) |Hadoop, HBase, Storm, Spark, Kafka |StriIm (pronunciato "Stream") è una piattaforma di integrazione dei dati di streaming e di Intelligence end-to-end, che consente l'inserimento continuo, l'elaborazione e l'analisi di flussi di dati diversi. |
|[Jumbune Enterprise-accelerazione BigData Analytics](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |A un livello elevato, Jumbune assiste le aziende per 1. Accelerazione di TeZ, MapReduce & motore Spark basato su hive, Java, prestazioni del carico di lavoro scala. 2. Monitoraggio proattivo del cluster Hadoop, 3. Determinazione della gestione della qualità dei dati nei file system distribuiti. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/kyligence.kyligence) |Hadoop, HBase, Spark |Basato su Apache Kylin, Kyligence Enterprise Abilita BI sui Big Data. Come motore OLAP aziendale in Hadoop, Kyligence Enterprise consente agli analisti aziendali di progettare BI in Hadoop con metodologie data warehouse e BI standard del settore. |
|[Starburst presto per Azure HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto è un motore di query SQL distribuito veloce e scalabile. Progettato per separare l'archiviazione dal calcolo, Presto è perfetto per eseguire query sui dati in Azure Data Lake Storage, Archiviazione BLOB di Azure, database SQL e NoSQL e altre origini dati. |
|[Agente di raccolta dati StreamSets per HDInsight cloud](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop, HBase, Spark, Kafka |StreamSets Data Collector è un motore leggero e potente che trasmette i dati in tempo reale. Usare l'agente di raccolta dati per instradare ed elaborare i dati nei flussi di dati. Viene fornita con una licenza di valutazione di 30 giorni. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/trifacta.tr01) |Hadoop, Spark, HBase |Trifacta Wrangler Enterprise per HDInsight supporta data wrangling a livello aziendale per qualsiasi scala di dati. Il costo dell'esecuzione di trifactora in Azure è costituito da una combinazione di costi di sottoscrizione trifacta oltre ai costi dell'infrastruttura di Azure per le macchine virtuali. |
|[Piattaforma dati UniFi](https://unifisoftware.com/platform/) |Hadoop, HBase, Storm, Spark |La piattaforma di dati UniFi è una suite integrata di strumenti per i dati self-service progettati per consentire all'utente di business di affrontare le problematiche relative ai dati che spingono i ricavi incrementali, riducono i costi o la complessità operativa. |
|[APM Unraveldata](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Districare l'app dati per il cluster HDInsight Spark. |
|[Data Catalog di base per intelligenza artificiale](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/waterline_data.waterline_data) |Spark |I cataloghi di contornamento, organizzano e governano i dati usando l'intelligenza artificiale per contrassegnare automaticamente i dati con termini aziendali. Il ricco catalogo aziendale di Waterline è un componente critico e di successo per il self-service di analisi, conformità, governance e iniziative di gestione dell'IT. |

Nelle istruzioni illustrate in questo articolo si usa il portale di Azure. È anche possibile esportare il modello di Azure Resource Manager dal portale oppure ottenere una copia del modello di Resource Manager dai fornitori e usare l'interfaccia della riga di comando classica di Azure e Azure PowerShell per distribuire il modello.  Vedere [Creare cluster Apache Hadoop in HDInsight mediante modelli di Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Prerequisiti
Per installare applicazioni HDInsight in un cluster HDInsight esistente, è necessario un cluster HDInsight. Per crearne uno, vedere [Creare cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). È anche possibile installare applicazioni HDInsight quando si crea un cluster HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Installare applicazioni in cluster esistenti
La procedura seguente illustra come installare applicazioni HDInsight in un cluster HDInsight esistente.

**Installare un'applicazione HDInsight**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Dal menu a sinistra passare a **tutti i servizi** > **analisi** > dei**cluster HDInsight**.
3. Selezionare un cluster HDInsight dall'elenco.  Se non ci sono cluster disponibili, è necessario crearne uno.  Vedere [Creare cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Nella categoria **Impostazioni** selezionare **applicazioni**. È possibile visualizzare un elenco di applicazioni installate nella finestra principale. 
   
    ![Menu del portale Applicazioni di HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Selezionare **+ Aggiungi** dal menu. È possibile visualizzare un elenco di applicazioni disponibili.  Se **+ Add** è disattivato, significa che non sono disponibili applicazioni per questa versione del cluster HDInsight.
   
    ![Applicazioni disponibili in Applicazioni di HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. Selezionare una delle applicazioni disponibili, quindi seguire le istruzioni per accettare le condizioni legali.

È possibile visualizzare lo stato di installazione dalle notifiche del portale (selezionare l'icona a campana nella parte superiore del portale). Dopo l'installazione dell'applicazione, questa verrà visualizzata nel pannello App installate.

## <a name="install-applications-during-cluster-creation"></a>Installare applicazioni durante la creazione del cluster
È possibile installare applicazioni HDInsight quando si crea un cluster. Durante il processo, le applicazioni HDInsight vengono installate dopo la creazione del cluster e mentre è nello stato in corso di esecuzione. Per installare le applicazioni durante la creazione del cluster usando il portale di Azure, è possibile usare l'opzione **personalizzata** al posto dell'opzione predefinita di **creazione rapida** .

## <a name="list-installed-hdinsight-apps-and-properties"></a>Elencare le app HDInsight installate e le proprietà
Il portale include un elenco delle applicazioni HDInsight installate per un cluster e le proprietà di ognuna.

**Elencare le applicazioni HDInsight e visualizzare le proprietà**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Dal menu a sinistra passare a **tutti i servizi** > **analisi** > dei**cluster HDInsight**.
3. Selezionare un cluster HDInsight dall'elenco.
4. Nella categoria **Impostazioni** selezionare **applicazioni**. È possibile visualizzare un elenco di applicazioni installate nella finestra principale. 
   
    ![App installate in Applicazioni di HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Selezionare una delle applicazioni installate per visualizzare la proprietà. Elenchi delle proprietà:

    |Proprietà | DESCRIZIONE |
    |---|---|
    |Nome app |Nome dell'applicazione. |
    |Stato |Stato dell'applicazione. |
    |Pagina Web |URL dell'applicazione Web distribuita nel nodo perimetrale. Le credenziali sono le stesse usate per le credenziali utente HTTP configurate per il cluster. |
    |Endpoint SSH |È possibile usare SSH per connettersi al nodo perimetrale. Le credenziali SSH sono le stesse usate per le credenziali utente SSH configurate per il cluster. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |DESCRIZIONE | Descrizione dell'applicazione. |

6. Per eliminare un'applicazione, fare clic con il pulsante destro del mouse sull'applicazione e quindi scegliere **Elimina** dal menu di scelta rapida.

## <a name="connect-to-the-edge-node"></a>Connettersi al nodo perimetrale
È possibile connettersi al nodo perimetrale tramite HTTP e SSH. Le informazioni sull'endpoint sono disponibili nel [portale](#list-installed-hdinsight-apps-and-properties). Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Le credenziali dell'endpoint HTTP sono le credenziali utente HTTP configurate per il cluster HDInsight. Le credenziali dell'endpoint SSH sono le credenziali SSH configurate per il cluster HDInsight.

## <a name="troubleshoot"></a>Risolvere problemi
Vedere [Risolvere i problemi di installazione](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Passaggi successivi
* [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md): informazioni su come distribuire in HDInsight un'applicazione HDInsight non pubblicata.
* [Pubblicare applicazioni HDInsight](hdinsight-apps-publish-applications.md): Informazioni su come pubblicare applicazioni HDInsight personalizzate in Azure Marketplace.
* [MSDN: Installare un'applicazione HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Informazioni su come definire le applicazioni HDInsight.
* [Personalizzare cluster HDInsight basati su Linux tramite Azioni script](hdinsight-hadoop-customize-cluster-linux.md): informazioni su come usare Azioni script per installare applicazioni aggiuntive.
* [Creare cluster Apache Hadoop basati su Linux in HDInsight tramite modelli ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informazioni su come chiamare i modelli di Azure Resource Manager per creare cluster HDInsight.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md)(Usare nodi perimetrali vuoti in HDInsight): informazioni su come usare un nodo perimetrale vuoto per l'accesso a cluster HDInsight, il test di applicazioni HDInsight e l'hosting di applicazioni HDInsight.

