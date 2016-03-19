<properties
   pageTitle="Creare cluster Hadoop in HDInsight | Microsoft Azure"
   	description="Informazioni su come creare cluster per Azure HDInsight usando il portale di Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/06/2016"
   ms.author="jgao"/>

# Creare cluster Hadoop basati su Windows in HDInsight tramite il portale di Azure

[AZURE.INCLUDE [selettore](../../includes/hdinsight-selector-create-clusters.md)]

Informazioni su come creare un cluster Hadoop in HDInsight tramite il portale di Azure. Il [portale di Microsoft Azure](../azure-portal-overview.md) è una posizione centrale in cui è possibile effettuare il provisioning delle risorse di Azure e gestirle. Il portale di Azure è uno degli strumenti che è possibile usare per creare cluster Hadoop basati su Linux o Windows in HDInsight. Per altri strumenti e funzionalità per la creazione di cluster, fare clic sulla scheda Seleziona nella parte superiore di questa pagina o vedere [Metodi di creazione di cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

###Prerequisiti:

Per poter eseguire le istruzioni descritte nell'articolo è necessario disporre di:

- Una sottoscrizione di Azure. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

## Creare i cluster


**Per creare un cluster HDInsight**

1. Accedere al [portale di Azure](https://portal.azure.com).
2. Fare clic su **NUOVO**, fare clic su **Analisi di dati** e quindi fare clic su **HDInsight**.

  ![Creazione di un nuovo cluster nel portale di Azure](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Creazione di un nuovo cluster nel portale di Azure")

3. Digitare o selezionare i valori seguenti:

  * **Nome del cluster**: Immettere un nome per il cluster. Un segno di spunta verde verrà visualizzato accanto al nome del cluster, se disponibile.
  * **Tipo di cluster**: selezionare **Hadoop**. Altre opzioni includono **HBase**, **Storm** e **Spark**.
  * **Sistema operativo cluster**: selezionare **Windows**. Per creare un cluster basato su Linux, selezionare **Linux**.
  * **Versione**: vedere [Versioni di HDInsight](hdinsight-component-versioning.md).
  * **Sottoscrizione**: selezionare la sottoscrizione di Azure che verrà usata per creare il cluster.
  * **Gruppo di risorse**: selezionare un gruppo di risorse esistente o crearne uno nuovo. Questa voce sarà impostata su uno dei gruppi di risorse esistenti, se disponibili.
  * **Credenziali**: configurare il nome utente e la password per l'utente di Hadoop (utente HTTP). Se si abilita desktop remoto per il cluster, è necessario configurare il nome utente e la password di desktop remoto nonché la data di scadenza dell'account. Fare clic su **Seleziona** in basso per salvare le modifiche.

![Fornire le credenziali del cluster](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Fornire le credenziali del cluster")

 * **Origine dati**: creare un nuovo account di archiviazione di Azure o selezionarne uno esistente da usare come file system predefinito per il cluster.

   ![Pannello di origine dati](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Fornire la configurazione origine dati")

  * **Metodo di selezione**: impostare questo valore su **Da tutte le sottoscrizioni** per consentire l'esplorazione degli account di archiviazione da tutte le sottoscrizioni. Impostare questa voce su **Chiave di accesso** se si vuole immettere un valore nei campi **Nome archiviazione** e **Chiave di accesso** per un account di archiviazione esistente.
  * **Seleziona account di archiviazione / Crea nuovo**: fare clic su **Seleziona account di archiviazione** per cercare e selezionare un account di archiviazione esistente da associare al cluster. In alternativa, fare clic su **Crea nuovo** per creare un nuovo account di archiviazione. Utilizzare il campo che viene visualizzato per immettere il nome dell'account di archiviazione. Se il nome è disponibile, verrà visualizzato un segno di spunta verde.
  * **Scegli contenitore predefinito**: utilizzare questa opzione per immettere il nome del contenitore predefinito da utilizzare per il cluster. È possibile immettere qualsiasi nome, è consigliabile usare lo stesso nome del cluster in modo che sia facilmente intuibile che il contenitore viene usato per tale cluster specifico.
  * **Percorso**: l'area geografica dove si trova o dove verrà creato l'account di archiviazione. Questo percorso determina il percorso del cluster. Il cluster e l'account di archiviazione predefinito devono trovarsi nello stesso data center di Azure.
  	
  * **Livelli di prezzi nodo**: Impostare il numero di nodi del ruolo di lavoro necessari per il cluster. Verrà visualizzato il costo stimato del cluster all'interno del pannello.
  

![Pannello livelli dei prezzi di nodo](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specificare il numero di nodi del cluster")


 * **Configurazione facoltativa**: per selezionare la versione del cluster, nonché configurare altre impostazioni facoltative, ad esempio l'aggiunta a una **Rete virtuale** o la configurazione di un **Metastore esterno** per contenere i dati per Hive e Oozie, usare azioni script per personalizzare un cluster per l'installazione di componenti personalizzati oppure usare altri account di archiviazione con il cluster.

 * **Versione HDInsight**: selezionare la versione da usare per il cluster. Per altre informazioni, vedere [Versioni del cluster HDInsight](hdinsight-component-versioning.md).
 * **Rete virtuale**: selezionare una rete virtuale di Azure e la subnet, se si desidera posizionare il cluster in una rete virtuale.  

![Pannello della rete virtuale](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specificare i dettagli della rete virtuale")

Per informazioni sull'uso di HDInsight con una rete virtuale, inclusi i requisiti di configurazione specifici per la rete virtuale, vedere [Estendere le funzionalità di HDInsight usando Rete virtuale di Azure](hdinsight-extend-hadoop-virtual-network.md).
  

  		
* **Metastore esterni**: specificare un database SQL di Azure da usare per salvare i metadati Hive e Oozie associati al cluster.
 
  > [AZURE.NOTE] La configurazione Metastore non è disponibile per i tipi di cluster HBase.

![Pannello metastore personalizzati](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specificare metastore esterni")


Per **Usare un database SQL esistente per i metadati Hive**, fare clic su **Sì**, selezionare un database SQL e quindi specificare il nome utente e la password per il database. Ripetere questa procedura se si desidera **Utilizzare un database SQL esistente per i metadati Oozie**. Fare clic su **Seleziona** fino a quando non si visualizza di nuovo il pannello **Configurazione facoltativa**.


>[AZURE.NOTE] Il database SQL di Azure usato per il metastore deve consentire la connettività ad altri servizi di Azure, incluso Azure HDInsight. Sul lato destro del dashboard del database SQL di Azure fare clic sul nome del server, cioè il server in cui è in esecuzione l'istanza di database SQL. Nella visualizzazione server fare clic su **Configura**, quindi per **Servizi di Microsoft Azure** fare clic su **Sì** e infine su **Salva**.
		
 * **Azioni script**: se si desidera usare uno script personalizzato per personalizzare un cluster durante la creazione. Per altre informazioni sulle azioni di script, vedere [Personalizzare i cluster HDInsight mediante le azioni script](hdinsight-hadoop-customize-cluster.md). Nel pannello Azioni di Script fornire i dettagli, come illustrato nella schermata.
  	

![Pannello azione di script](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specificare l'azione di script")


 * **Chiavi di archiviazione di Azure**: specificare gli account di archiviazione aggiuntivi da associare al cluster. Nel pannello **Chiavi di archiviazione di Azure** fare clic su **Aggiungi una chiave di archiviazione**, quindi selezionare un account di archiviazione esistente o crearne uno nuovo.
    

![Pannello risorse di archiviazione aggiuntive](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specificare gli account di archiviazione aggiuntivi")


4. Fare clic su **Create**. Se si seleziona **Aggiungere a schermata iniziale**, verrà aggiunto un riquadro per il cluster alla schermata iniziale del portale. L'icona indica che il cluster è in fase di creazione e verrà visualizzata l'icona di HDInsight al completamento dell'operazione.
	
  La creazione del cluster richiederà del tempo, in genere circa 15 minuti. Usare il riquadro sulla schermata iniziale, o la voce **Notifiche** a sinistra della pagina per controllare il processo di provisioning.
	

5. Al termine della creazione, fare clic sul riquadro per il cluster dalla Schermata iniziale per avviare il pannello del cluster. Il pannello del cluster fornisce informazioni essenziali sui cluster, ad esempio nome, gruppo di risorse che a cui appartiene, percorso, sistema operativo, URL per il dashboard del cluster e così via.


![Pannello del cluster](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Proprietà del cluster")


Usare le informazioni seguenti per comprendere le icone nella parte superiore di questo pannello e nella sezione **Informazioni di base**:


* **Impostazioni** e **Tutte le impostazioni**: consentono di visualizzare il pannello**Impostazioni** per il cluster, che consente di accedere a informazioni dettagliate sulla configurazione del cluster.
* **Dashboard**, **Dashboard del cluster** e **URL**: sono tutti modi per accedere al dashboard del cluster, ovvero un portale Web per eseguire processi nel cluster.
* **Desktop remoto**: consente di attivare o disattivare il desktop remoto nei nodi del cluster.
* **Scala Cluster**: consente di modificare il numero di nodi del ruolo di lavoro per questo cluster.
* **Elimina**: consente di eliminare il cluster HDInsight.
* **Guide rapide** (![icona cloud e thunderbolt = guida rapida](./media/hdinsight-provision-clusters/quickstart.png)): Visualizza le informazioni che consentiranno di iniziare a usare HDInsight.
* **Utenti** (![icona utenti](./media/hdinsight-provision-clusters/users.png)): consente di impostare le autorizzazioni per _Gestione portale_ di questo cluster per altri utenti nella sottoscrizione Azure.
	

> [AZURE.IMPORTANT] Questa impostazione influisce _solo_ sull'accesso e sulle autorizzazioni per questo cluster nel portale, e non ha alcun effetto su chi può connettersi o inviare processi al cluster HDInsight.
		
* **Tag** (![icona tag](./media/hdinsight-provision-clusters/tags.png)): consente di impostare coppie chiave/valore per definire una tassonomia dei servizi cloud personalizzata. Ad esempio, è possibile creare una chiave denominata __progetto__ e usare un valore comune per tutti i servizi associati a un progetto specifico.

##Personalizzare i cluster

- Vedere [Personalizzare cluster HDInsight tramite Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
- Vedere [Personalizzare cluster HDInsight basati su Windows tramite Azione script](hdinsight-hadoop-customize-cluster.md).

##Passaggi successivi
Questo articolo ha spiegato vari modi per creare un cluster HDInsight. Per altre informazioni, vedere gli articoli seguenti:

* [Introduzione all'uso di Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - Informazioni su come iniziare a usare il cluster HDInsight
* [Inviare processi Hadoop a livello di codice](hdinsight-submit-hadoop-jobs-programmatically.md) - Informazioni su come inviare processi a HDInsight a livello di codice
* [Gestire cluster Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-management-portal.md)

<!---HONumber=AcomDC_0224_2016-->