---
title: Installare applicazioni di terze parti in Azure HDInsight
description: Informazioni su come installare applicazioni Apache Hadoop di terze parti in Azure HDInsight.Learn how to install third-party Apache Hadoop applications on Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 22d27d50a7d2c304e5d1a04a9a5eaa03d2f0bfa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366343"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Installare applicazioni Apache Hadoop di terze parti in Azure HDInsight

Informazioni su come installare applicazioni [Apache Hadoop](https://hadoop.apache.org/) di terze parti in Azure HDInsight. Per istruzioni sull'installazione di un'applicazione personalizzata, vedere l'articolo su come [installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md).

Un'applicazione HDInsight è un'applicazione che gli utenti possono installare in un cluster HDInsight. Queste applicazioni possono essere sviluppate da Microsoft, da fornitori di software indipendenti (ISV) o dall'utente.  

L'elenco seguente mostra le applicazioni pubblicate:

|Applicazione |Tipo/i cluster | Descrizione |
|---|---|---|
|[Piattaforma AtScale Intelligence](https://azuremarketplace.microsoft.com/marketplace/apps/atscaleinc.atscale) |Hadoop |AtScale trasforma il cluster HDInsight in un server OLAP con scalabilità orizzontale, consentendo di eseguire query su miliardi di righe di dati in modo interattivo utilizzando gli strumenti di BI già conosciuti, di cui si è già proprietari, da Microsoft Excel, Power BI, Tableau Software a QlikView. |
|[CDAP per HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |hbase |CDAP è la prima piattaforma di integrazione unificata per i Big Data che accelera il time-to-value per Hadoop e consente all'IT di fornire dati self-service. Open source ed estensibile, CDAP rimuove gli ostacoli all'innovazione. Requisiti: 4 nodi di regione, min D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |La piattaforma autonoma di Datameer per la preparazione, l'esplorazione e la governance dei dati per l'analisi accelera trasformando i dati multisource complessi in preziose informazioni pronte per l'azienda, fornendo informazioni più rapide e intelligenti su scala aziendale. |
|[Dataiku DSS on HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS in una piattaforma di data science aziendale che consente ai data scientist e agli analisti di dati di collaborare per progettare ed eseguire nuovi prodotti e servizi di dati in modo più efficiente, trasformando i dati grezzi in previsioni di impatto. |
|[WANdisco Fusion HDI App](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Scintilla,HBase,Tempesta,Kafka |Mantenere la coerenza dei dati in un ambiente distribuito è una sfida enorme per le operazioni sui dati. WANdisco Fusion, una piattaforma software di classe enterprise, risolve questo problema abilitando la coerenza dei dati non strutturati in qualsiasi ambiente. |
|[H2O SparklingWater per HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling Water supporta i seguenti algoritmi distribuiti: GLM, Naive Bayes, Distributed Random Forest, Gradient Boosting Machine, Deep Neural Networks, Deep learning, K-means, PCA, Generalized Low Rank Models, Anomaly Detection, Autoencoders. |
|[Striim per l'integrazione dei dati in tempo reale in HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/striim.hdinsightintegration) |Hadoop,HBase,Storm,Spark,Kafka |Striim (si pronuncia come è scritto) è una piattaforma di intelligence e di integrazione dei dati di streaming end-to-end, che consente l'inserimento continuo, l'elaborazione e l'analisi di flussi di dati di vario genere. |
|[Jumbune Enterprise-Accelerazione BigData Analytics](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |Ad alto livello, Jumbune assiste le imprese, 1. Accelerare le prestazioni del carico di lavoro Hive, Java, Scala di Tez, MapReduce & Spark. 2. Monitoraggio proattivo del cluster Hadoop, 3. Definizione della gestione della qualità dei dati nel file system distribuito. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop,HBase,Scintilla |Alimentata da Apache Kylin, Kyligence Enterprise abilita la BI sui Big Data. In qualità di motore OLAP aziendale su Hadoop, Kyligence Enterprise consente al business analyst di progettare BI su Hadoop con un data warehouse e una metodologia BI standard del settore. |
|[Starburst Presto per Azure HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto è un motore di query SQL distribuito veloce e scalabile. Progettato per separare l'archiviazione dal calcolo, Presto è perfetto per eseguire query sui dati in Azure Data Lake Storage, Archiviazione BLOB di Azure, database SQL e NoSQL e altre origini dati. |
|[Raccolta dati StreamSets per HDInsight Cloud](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop,HBase,Scintilla,Kafka |StreamSets Data Collector è un motore leggero e potente che trasmette i dati in tempo reale. Usare Data Collector per instradare ed elaborare i dati nei flussi di dati. Viene fornito con una licenza di prova di 30 giorni. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.tr01) |Hadoop, Scintilla,HBase |Trifacta Wrangler Enterprise per HDInsight supporta il wrangling dei dati a livello aziendale per qualsiasi scala di dati. Il costo dell'esecuzione di Trifacta in Azure è una combinazione dei costi di sottoscrizione Trifacta più i costi dell'infrastruttura di Azure per le macchine virtuali. |
|[Piattaforma dati Unifi](https://unifisoftware.com/platform/) |Hadoop,HBase,Storm,Spark |Unifi Data Platform è una suite perfettamente integrata di strumenti per i dati self-service progettati per consentire all'utente aziendale di affrontare le sfide legate ai dati che generano entrate incrementali, riducono i costi o la complessità operativa. |
|[Svelaldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Unravel Data app per il cluster HDInsight Spark. |
|[Catalogo dati basato sull'ia della linea di galleggiamento](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |I cataloghi, gli organizza e governano i dati tramite l'iA per contrassegnare automaticamente i dati in base alle condizioni aziendali. Il catalogo business-literate di Waterline è un componente fondamentale e di successo per l'analisi self-service, la conformità e la governance e le iniziative di gestione IT. |

Nelle istruzioni illustrate in questo articolo si usa il portale di Azure. È anche possibile esportare il modello di Azure Resource Manager dal portale oppure ottenere una copia del modello di Resource Manager dai fornitori e usare l'interfaccia della riga di comando classica di Azure e Azure PowerShell per distribuire il modello.  Vedere [Creare cluster Apache Hadoop in HDInsight mediante modelli di Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Prerequisiti
Per installare applicazioni HDInsight in un cluster HDInsight esistente, è necessario un cluster HDInsight. Per crearne uno, vedere [Creare cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md). È anche possibile installare applicazioni HDInsight quando si crea un cluster HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Installare applicazioni in cluster esistenti
La procedura seguente illustra come installare applicazioni HDInsight in un cluster HDInsight esistente.

**Installare un'applicazione HDInsight**

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Dal menu a sinistra, passare a **Tutti i servizi** > **Analytics** > **cluster HDInsight**.
3. Selezionare un cluster HDInsight dall'elenco.  Se non ci sono cluster disponibili, è necessario crearne uno.  Vedere [Creare cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md).
4. Nella categoria **Impostazioni** selezionare **Applicazioni**. È possibile visualizzare un elenco delle applicazioni installate nella finestra principale. 
   
    ![Menu del portale Applicazioni di HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Dal menu, seleziona **"Aggiungi".** È possibile visualizzare un elenco delle applicazioni disponibili.  Se **l'opzione Aggiungi** è disattivata, significa che non sono presenti applicazioni per questa versione del cluster HDInsight.
   
    ![Applicazioni disponibili in Applicazioni di HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. Selezionare una delle applicazioni disponibili, quindi seguire le istruzioni per accettare le condizioni legali.

È possibile visualizzare lo stato di installazione dalle notifiche del portale (selezionare l'icona a campana nella parte superiore del portale). Dopo l'installazione dell'applicazione, questa verrà visualizzata nel pannello App installate.

## <a name="install-applications-during-cluster-creation"></a>Installare applicazioni durante la creazione del cluster

È possibile installare applicazioni HDInsight quando si crea un cluster. Durante il processo, le applicazioni HDInsight vengono installate dopo la creazione del cluster e mentre è nello stato in corso di esecuzione. Per installare le applicazioni durante la creazione del cluster tramite il portale di Azure, nella scheda **Configurazione e prezzi** selezionare Aggiungi **applicazione**.

![Applicazioni di configurazione del cluster del portale di AzureAzure portal cluster configuration applications](./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png)

## <a name="list-installed-hdinsight-apps-and-properties"></a>Elencare le app HDInsight installate e le proprietà
Il portale include un elenco delle applicazioni HDInsight installate per un cluster e le proprietà di ognuna.

**Elencare le applicazioni HDInsight e visualizzare le proprietà**

1. Accedere al [portale](https://portal.azure.com)di Azure .
2. Dal menu a sinistra, passare a **Tutti i servizi** > **Analytics** > **cluster HDInsight**.
3. Selezionare un cluster HDInsight dall'elenco.
4. Nella categoria **Impostazioni** selezionare **Applicazioni**. È possibile visualizzare un elenco delle applicazioni installate nella finestra principale. 
   
    ![App installate in Applicazioni di HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Selezionare una delle applicazioni installate per visualizzare la proprietà. Elenchi delle proprietà:

    |Proprietà | Descrizione |
    |---|---|
    |Nome app |Nome dell'applicazione. |
    |Stato |Stato della domanda. |
    |Pagina Web |URL dell'applicazione Web distribuita nel nodo perimetrale. Le credenziali sono le stesse usate per le credenziali utente HTTP configurate per il cluster. |
    |Endpoint SSH |È possibile usare SSH per connettersi al nodo perimetrale. Le credenziali SSH sono le stesse usate per le credenziali utente SSH configurate per il cluster. Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Descrizione | Descrizione dell'applicazione. |

6. Per eliminare un'applicazione, fare clic con il pulsante destro del mouse sull'applicazione e quindi scegliere **Elimina** dal menu di scelta rapida.

## <a name="connect-to-the-edge-node"></a>Connettersi al nodo perimetrale
È possibile connettersi al nodo perimetrale tramite HTTP e SSH. Le informazioni sull'endpoint sono disponibili nel [portale](#list-installed-hdinsight-apps-and-properties). Per altre informazioni, vedere [Usare SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Le credenziali dell'endpoint HTTP sono le credenziali utente HTTP configurate per il cluster HDInsight. Le credenziali dell'endpoint SSH sono le credenziali SSH configurate per il cluster HDInsight.

## <a name="troubleshoot"></a>Risolvere i problemi
Vedere [Risolvere i problemi di installazione](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Passaggi successivi
* [Installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md): informazioni su come distribuire in HDInsight un'applicazione HDInsight non pubblicata.
* [Pubblicare applicazioni HDInsight](hdinsight-apps-publish-applications.md): informazioni su come pubblicare applicazioni HDInsight personalizzate in Azure Marketplace.
* [MSDN: Install an HDInsight application](https://msdn.microsoft.com/library/mt706515.aspx)(MSDN: Installare un'applicazione HDInsight): informazioni su come definire le applicazioni HDInsight.
* [Personalizzare cluster HDInsight basati su Linux tramite Azioni script](hdinsight-hadoop-customize-cluster-linux.md): informazioni su come usare Azioni script per installare applicazioni aggiuntive.
* [Creare cluster Apache Hadoop basati su Linux in HDInsight tramite modelli ARM](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informazioni su come chiamare i modelli di Azure Resource Manager per creare cluster HDInsight.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md)(Usare nodi perimetrali vuoti in HDInsight): informazioni su come usare un nodo perimetrale vuoto per l'accesso a cluster HDInsight, il test di applicazioni HDInsight e l'hosting di applicazioni HDInsight.

