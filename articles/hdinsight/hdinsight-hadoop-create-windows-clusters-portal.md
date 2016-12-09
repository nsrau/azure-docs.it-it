---
title: Creare cluster Hadoop in HDInsight | Documentazione Microsoft
description: Informazioni su come creare cluster per Azure HDInsight usando il portale di Azure.
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c8689ef3-f56f-4708-8a3a-cc00abc54e8d
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/02/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d6680fb1e33b221a74d6c5db4cf5012d9109eac6


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-the-azure-portal"></a>Creare cluster Hadoop basati su Windows in HDInsight tramite il portale di Azure
[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Informazioni su come creare un cluster Hadoop in HDInsight tramite il portale di Azure. Il [portale di Microsoft Azure](../azure-portal-overview.md) è una posizione centrale in cui è possibile effettuare il provisioning delle risorse di Azure e gestirle. Il portale di Azure è uno degli strumenti che è possibile usare per creare cluster Hadoop basati su Linux o Windows in HDInsight. Per altri strumenti e funzionalità per la creazione di cluster, fare clic sulla scheda Seleziona nella parte superiore di questa pagina o vedere [Metodi di creazione di cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

## <a name="prerequisites"></a>Prerequisiti:
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Per poter eseguire le istruzioni descritte nell'articolo è necessario disporre di:

* Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

### <a name="access-control-requirements"></a>Requisiti di controllo di accesso
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Creare i cluster
**Per creare un cluster HDInsight**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **NUOVO**, **Analisi dei dati** e quindi su **HDInsight**.
   
    ![Creazione di un nuovo cluster nel portale di Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Creating a new cluster in the Azure Portal")
3. Digitare o selezionare i valori seguenti:
   
   * **Nome del cluster**: Immettere un nome per il cluster. Un segno di spunta verde verrà visualizzato accanto al nome del cluster, se disponibile.
   * **Tipo di cluster**: selezionare **Hadoop**. Altre opzioni includono **HBase**, **Storm** e **Spark**.
     
     > [!IMPORTANT]
     > Sono disponibili molti tipi di cluster HDInsight, che corrispondono al carico di lavoro o alla tecnologia per cui è ottimizzato il cluster. Non è disponibile alcun metodo supportato per creare un cluster che combini più tipi, ad esempio Storm e HBase in un cluster.
     > 
     > 
   * **Sistema operativo cluster**: selezionare **Windows**. Per creare un cluster basato su Linux, selezionare **Linux**.
   * **Versione**: vedere [Versioni di HDInsight](hdinsight-component-versioning.md).
   * **Sottoscrizione**: selezionare la sottoscrizione di Azure che verrà usata per creare il cluster.
   * **Gruppo di risorse**: selezionare un gruppo di risorse esistente o crearne uno nuovo. Questa voce sarà impostata su uno dei gruppi di risorse esistenti, se disponibili.
   * **Credenziali**: configurare il nome utente e la password per l'utente di Hadoop (utente HTTP). Se si abilita desktop remoto per il cluster, è necessario configurare il nome utente e la password di desktop remoto nonché la data di scadenza dell'account. Fare clic su **Seleziona** in basso per salvare le modifiche.
     
          ![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")
   * **Origine dati**: creare un nuovo account di archiviazione di Azure o selezionarne uno esistente da usare come file system predefinito per il cluster.
     
          ![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")
     
         * **Metodo di selezione**: impostare questo valore su **Da tutte le sottoscrizioni** per consentire l'esplorazione degli account di archiviazione da tutte le sottoscrizioni. Impostare questa voce su **Chiave di accesso** se si vuole immettere un valore nei campi **Nome archiviazione** e **Chiave di accesso** per un account di archiviazione esistente.
         * **Seleziona account di archiviazione/Crea nuovo**: fare clic su **Seleziona account di archiviazione** per cercare e selezionare un account di archiviazione esistente da associare al cluster. In alternativa, fare clic su **Crea nuovo** per creare un nuovo account di archiviazione. Utilizzare il campo che viene visualizzato per immettere il nome dell'account di archiviazione. Se il nome è disponibile, verrà visualizzato un segno di spunta verde.
         * **Scegli contenitore predefinito**: utilizzare questa opzione per immettere il nome del contenitore predefinito da utilizzare per il cluster. È possibile immettere qualsiasi nome, è consigliabile usare lo stesso nome del cluster in modo che sia facilmente intuibile che il contenitore viene usato per tale cluster specifico.
         * **Percorso**: l'area geografica dove si trova o dove verrà creato l'account di archiviazione. Questo percorso determina il percorso del cluster.  Il cluster e l'account di archiviazione predefinito devono trovarsi nello stesso data center di Azure.
   * **Piani tariffari per il nodo**: Impostare il numero di nodi del ruolo di lavoro necessari per il cluster. Verrà visualizzato il costo stimato del cluster all'interno del pannello.

        ![Pannello livelli dei prezzi di nodo](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


    * **Configurazione facoltativa**: per selezionare la versione del cluster, nonché configurare altre impostazioni facoltative, ad esempio l'aggiunta a una **Rete virtuale** o la configurazione di un **Metastore esterno** per contenere i dati per Hive e Oozie, usare azioni script per personalizzare un cluster per l'installazione di componenti personalizzati oppure usare altri account di archiviazione con il cluster.

    * **Versione HDInsight**: selezionare la versione da usare per il cluster. Per altre informazioni, vedere [Versioni del cluster HDInsight](hdinsight-component-versioning.md).
    * **Rete virtuale**: selezionare una rete virtuale di Azure e la subnet, se si desidera posizionare il cluster in una rete virtuale.  

        ![Pannello della rete virtuale](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

        Per informazioni sull'uso di HDInsight con una rete virtuale, inclusi i requisiti di configurazione specifici per la rete virtuale, vedere [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md).



    * **Metastore esterni**: specificare un database SQL di Azure da usare per salvare i metadati Hive e Oozie associati al cluster.

        > [AZURE.NOTE] La configurazione Metastore non è disponibile per i tipi di cluster HBase.

    ![Pannello metastore personalizzati](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")

    Per **Usare un database SQL esistente per i metadati Hive?** fare clic su **Sì**, selezionare un database SQL e quindi specificare il nome utente e la password per il database. Ripetere questa procedura se si desidera **Utilizzare un database SQL esistente per i metadati Oozie**. Fare clic su **Seleziona** fino a quando non si visualizza di nuovo il pannello **Configurazione facoltativa**.

    >[AZURE.NOTE] Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Microsoft Azure** fare clic su **Sì** e infine su **Salva**.

            &nbsp;

            > [AZURE.IMPORTANT] Quando si crea un metastore personalizzato, non utilizzare un nome di database che contiene trattini o segni meno, poiché il processo di creazione del cluster non andrebbe a buon fine.

          * **Script Actions** if you want to use a custom script to customize a cluster, as the cluster is being created. For more information about script actions, see [Customize HDInsight clusters using Script Action](hdinsight-hadoop-customize-cluster.md). On the Script Actions blade provide the details as shown in the screen capture.


            ![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


        * **Chiavi di archiviazione di Azure**: specificare gli account di archiviazione aggiuntivi da associare al cluster. Nel pannello **Chiavi di archiviazione di Azure** fare clic su **Aggiungi una chiave di archiviazione**, quindi selezionare un account di archiviazione esistente o crearne uno nuovo.


            ![Pannello risorse di archiviazione aggiuntive](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


1. Fare clic su **Create**. Se si seleziona **Aggiungere a schermata iniziale** , verrà aggiunto un riquadro per il cluster alla schermata iniziale del portale. L'icona indica che il cluster è in fase di creazione e verrà visualizzata l'icona di HDInsight al completamento dell'operazione.
   
    La creazione del cluster richiederà del tempo, in genere circa 15 minuti. Usare il riquadro sulla schermata iniziale, o la voce **Notifiche** a sinistra della pagina per controllare il processo di provisioning.
2. Al termine della creazione, fare clic sul riquadro per il cluster dalla Schermata iniziale per avviare il pannello del cluster. Il pannello del cluster fornisce informazioni essenziali sui cluster, ad esempio nome, gruppo di risorse che a cui appartiene, percorso, sistema operativo, URL per il dashboard del cluster e così via.

    ![Pannello del cluster](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Cluster properties")


    Usare le informazioni seguenti per comprendere le icone nella parte superiore di questo pannello e nella sezione **Informazioni di base** :


    * **Impostazioni** e **Tutte le impostazioni**: consentono di visualizzare il pannello **Impostazioni** per il cluster, che consente di accedere a informazioni dettagliate sulla configurazione del cluster.
    * **Dashboard**, **Dashboard cluster** e **URL**: sono tutti modi per accedere al dashboard del cluster, ovvero un portale Web per eseguire processi nel cluster.
    * **Desktop remoto**: consente di attivare o disattivare il desktop remoto nei nodi del cluster.
    * **Scala Cluster**: consente di modificare il numero di nodi del ruolo di lavoro per questo cluster.
    * **Elimina**: consente di eliminare il cluster HDInsight.
    * **Avvio rapido** (![icona nuvola e lampo = avvio rapido](./media/hdinsight-provision-clusters/quickstart.png)): visualizza le informazioni che consentiranno di iniziare a usare HDInsight.
    * **Utenti** (![icona utenti](./media/hdinsight-provision-clusters/users.png)): consente di impostare le autorizzazioni per la _gestione del portale_ di questo cluster per altri utenti nella sottoscrizione di Azure.


        > [AZURE.IMPORTANT] Questa impostazione influisce _solo_ sull'accesso e sulle autorizzazioni per questo cluster nel portale, e non ha alcun effetto su chi può connettersi o inviare processi al cluster HDInsight.

    * **Tag** (![icona tag](./media/hdinsight-provision-clusters/tags.png)): consente di impostare coppie chiave/valore per definire una tassonomia dei servizi cloud personalizzata. Ad esempio, è possibile creare una chiave denominata __progetto__e usare un valore comune per tutti i servizi associati a un progetto specifico.

## <a name="customize-clusters"></a>Personalizzare i cluster
* Vedere [Personalizzare cluster HDInsight tramite Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Vedere l'articolo su come [personalizzare cluster HDInsight basati su Windows tramite un'azione script](hdinsight-hadoop-customize-cluster.md).

## <a name="next-steps"></a>Passaggi successivi
Questo articolo ha spiegato vari modi per creare un cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione all'uso di Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - Informazioni su come iniziare a usare il cluster HDInsight
* [Inviare processi Hadoop a livello di codice](hdinsight-submit-hadoop-jobs-programmatically.md) - Informazioni su come inviare processi a HDInsight a livello di codice
* [Gestire cluster Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-management-portal.md)




<!--HONumber=Nov16_HO3-->


