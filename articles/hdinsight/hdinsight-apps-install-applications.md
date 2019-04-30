---
title: Installare applicazioni di terze parti in Azure HDInsight
description: Informazioni su come installare applicazioni Hadoop di terze parti in Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: hrasheed
ms.openlocfilehash: 7cbcc8ac05e4541406abd08c14006a8abc0c91ca
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62097360"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Installare applicazioni Apache Hadoop di terze parti in Azure HDInsight

Informazioni su come installare applicazioni [Apache Hadoop](https://hadoop.apache.org/) di terze parti in Azure HDInsight. Per istruzioni sull'installazione di un'applicazione personalizzata, vedere l'articolo su come [installare applicazioni HDInsight personalizzate](hdinsight-apps-install-custom-applications.md).

Un'applicazione HDInsight è un'applicazione che gli utenti possono installare in un cluster HDInsight. Queste applicazioni possono essere sviluppate da Microsoft, da fornitori di software indipendenti (ISV) o dall'utente.  

L'elenco seguente mostra le applicazioni pubblicate:

|Applicazione |Tipi di cluster | DESCRIZIONE |
|---|---|---|
|AtScale Intelligence Platform |Hadoop |AtScale attiva il cluster HDInsight in un server OLAP scale-out, consentendo di eseguire query a miliardi di righe di dati in modo interattivo tramite gli strumenti di BI che già conosci, proprietario e apprezzi: in Microsoft Excel, Power BI, Tableau Software per QlikView. |
|CDAP 4.2, 4.3 per HDInsight |hbase |CDAP è la prima piattaforma di integrazione unificata per big data che accelera la fase di valore per Hadoop e consente al reparto IT fornire dati self-service. Open source e CDAP estendibile, rimuove le barriere all'innovazione. Requirements: Nodi di 4 aree, min D3 v2. |
|Datameer |Hadoop |Piattaforma self-service scalabile datameer contribuiscono per la preparazione, esplorazione e che controllano i dati di analitica accelera la trasformazione dati multisource complessi in informazioni aziendali importanti, recapito più veloce, più intelligente insights a un livello aziendale. |
|Dataiku DSS in HDInsight |Hadoop, Spark |Dataiku DSS in una piattaforma di analisi scientifica dei dati aziendali che consente ai data Scientist e analisti dei dati collaborano per progettare ed eseguire di nuovo i dati prodotti e servizi in modo più efficiente, trasformare i dati non elaborati in previsioni con impatto elevate. |
|WANdisco Fusion HDI App - 2.12.3, 2.12.1, punto 2.11.2 |Hadoop, Spark,HBase,Storm,Kafka |Coerenza dei dati in un ambiente distribuito è un problema di operazioni di grandi quantità di dati. WANdisco Fusion, una piattaforma software aziendale, risolve questo problema abilitando la coerenza dei dati non strutturati in qualsiasi ambiente. |
|H2O SparklingWater per HDInsight |Spark |H2O Sparkling Water supporta i seguenti algoritmi distribuiti: GLM, Naïve Bayes, Distributed Random Forest, macchina di Boosting a gradienti, reti neurali profonde, Deep learning, K-means, analisi in componenti principali, generalizzato modelli priorità bassa, rilevamento delle anomalie, Autoencoders. |
|Striim per l'integrazione di dati in tempo reale per HDInsight |Hadoop,HBase,Storm,Spark,Kafka |Striim (pronunciato "flusso") è un end-to-end della piattaforma di intelligence + integrazione dei dati di streaming, consentendo l'inserimento continuo e l'elaborazione analitica dei flussi di dati diversi. |
|Jumbune per HDInsight |Hadoop, Spark |A livello generale, Jumbune assiste le aziende, 1. Accelerazione di Tez, motore di MapReduce e Spark basati su Hive, Java, le prestazioni del carico di lavoro in Scala. 2. Cluster Hadoop attiva il monitoraggio, 3. Definizione di gestione della qualità dei dati nel file system distribuito. |
|Kyligence Enterprise |Hadoop,HBase,Spark |Con tecnologia Apache Kylin, Kyligence Enterprise consente BI sui Big Data. Come motore OLAP enterprise su Hadoop, Kyligence Enterprise offre ai business analyst per definire l'architettura di BI in Hadoop con standard del settore data warehouse e la metodologia di BI. |
|Spark Job Server for KNIME Spark Executor |Spark |Consente di connettersi alla piattaforma Analitica KNIME ai cluster HDInsight Spark Job Server for KNIME Spark Executor. |
|Stella Presto in Azure HDInsight, stella Presto (v0.213-e) |Hadoop |Presto è un veloce e scalabile distribuito motore di query SQL. Progettato per separare l'archiviazione dal calcolo, Presto è perfetto per eseguire query sui dati in Azure Data Lake Storage, Archiviazione BLOB di Azure, database SQL e NoSQL e altre origini dati. |
|StreamSets Data Collector per HDInsight Cloud |Hadoop, HBase, Spark, Kafka |StreamSets Data Collector è un motore leggero, potente che consente di trasmettere dati in tempo reale. Usare l'agente di raccolta dati per route ed elaborare i dati nei flussi di dati. È dotato di una licenza di valutazione di 30 giorni. |
|[Trifacta Wrangler Enterprise](https://www.trifacta.com/) |Hadoop, Spark,HBase |Trifacta Wrangler Enterprise per HDInsight supporta a livello aziendale data wrangling per tutte le esigenze dei dati. Il costo dell'esecuzione Trifacta in Azure è una combinazione di Trifacta i costi di sottoscrizione e i costi di infrastruttura di Azure per le macchine virtuali. |
|Piattaforma dati di Unifi 3.1 |Hadoop,HBase,Storm,Spark |La piattaforma di dati di Unifi è una suite integrata direttamente dei dati self-service gli strumenti progettati per consentire all'utente di business affrontare sfide poste dai dati tale incremento dei ricavi di unità, ridurre i costi o complessità operativa. |
|Application Performance Monitoring Unraveldata |Spark |Individuare le app di dati per il cluster HDInsight Spark. |
|Livello dell'acqua Data Catalog |Spark |Livello dell'acqua cataloghi, organizza e gestisce i dati usando intelligenza artificiale per auto-contrassegnare i dati con i termini aziendali. Il ricco catalogo aziendale di Waterline è un componente critico e di successo per il self-service di analisi, conformità, governance e iniziative di gestione dell'IT. |

Nelle istruzioni illustrate in questo articolo si usa il portale di Azure. È anche possibile esportare il modello di Azure Resource Manager dal portale oppure ottenere una copia del modello di Resource Manager dai fornitori e usare l'interfaccia della riga di comando classica di Azure e Azure PowerShell per distribuire il modello.  Vedere [Creare cluster Apache Hadoop in HDInsight mediante modelli di Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Prerequisiti
Per installare applicazioni HDInsight in un cluster HDInsight esistente, è necessario un cluster HDInsight. Per crearne uno, vedere [Creare cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster). È anche possibile installare applicazioni HDInsight quando si crea un cluster HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Installare applicazioni in cluster esistenti
La procedura seguente illustra come installare applicazioni HDInsight in un cluster HDInsight esistente.

**Installare un'applicazione HDInsight**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu a sinistra, passare a **tutti i servizi** > **Analitica** > **i cluster HDInsight**.
3. Selezionare un cluster HDInsight dall'elenco.  Se non ci sono cluster disponibili, è necessario crearne uno.  Vedere [Creare cluster](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Sotto il **le impostazioni** categoria, seleziona **applicazioni**. È possibile visualizzare un elenco delle applicazioni installate nella finestra principale. 
   
    ![Menu del portale Applicazioni di HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Selezionare **+ Aggiungi** dal menu di scelta. È possibile visualizzare un elenco delle applicazioni disponibili.  Se **+ Aggiungi** è grigio, che significa che non vi sono alcun applicazioni per questa versione del cluster HDInsight.
   
    ![Applicazioni disponibili in Applicazioni di HDInsight](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Selezionare una delle applicazioni disponibili e quindi seguire le istruzioni per accettare le condizioni legali.

È possibile visualizzare lo stato di installazione dalle notifiche del portale (selezionare l'icona a forma di campana nella parte superiore del portale). Dopo l'installazione dell'applicazione, questa verrà visualizzata nel pannello App installate.

## <a name="install-applications-during-cluster-creation"></a>Installare applicazioni durante la creazione del cluster
È possibile installare applicazioni HDInsight quando si crea un cluster. Durante il processo, le applicazioni HDInsight vengono installate dopo la creazione del cluster e mentre è nello stato in corso di esecuzione. Per installare applicazioni durante la creazione del cluster nel portale di Azure, si utilizza il **Custom** opzione anziché il valore predefinito **creazione rapida** opzione.

## <a name="list-installed-hdinsight-apps-and-properties"></a>Elencare le app HDInsight installate e le proprietà
Il portale include un elenco delle applicazioni HDInsight installate per un cluster e le proprietà di ognuna.

**Elencare le applicazioni HDInsight e visualizzare le proprietà**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Nel menu a sinistra, passare a **tutti i servizi** > **Analitica** > **i cluster HDInsight**.
3. Selezionare un cluster HDInsight dall'elenco.
4. Sotto il **le impostazioni** categoria, seleziona **applicazioni**. È possibile visualizzare un elenco delle applicazioni installate nella finestra principale. 
   
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

