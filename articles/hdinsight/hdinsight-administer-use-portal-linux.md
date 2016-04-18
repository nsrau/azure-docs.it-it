<properties
	pageTitle="Gestire cluster Hadoop basati su Linux in HDInsight tramite il portale di Azure | Microsoft Azure"
	description="Informazioni su come creare e gestire cluster basati su Linux HDInsight tramite il portale di Azure."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="larryfr"/>

#Gestire cluster Hadoop in HDInsight tramite il portale di Azure

[AZURE.INCLUDE [selettore](../../includes/hdinsight-portal-management-selector.md)]


Tramite il [Portale di Azure][preview-portal], è possibile eseguire il provisioning e gestire i cluster Hadoop basati su Linux in Azure HDInsight.

> [AZURE.NOTE] I passaggi descritti in questo documento sono specifici per i cluster basati su Linux. Per informazioni sull'utilizzo dei cluster basati su Windows, vedere [Gestire cluster Hadoop in HDInsight tramite il portale di Azure](hdinsight-administer-use-management-portal.md)


[AZURE.INCLUDE [preview-portal](../../includes/hdinsight-azure-preview-portal-nolink.md)]


## Altri strumenti per l'amministrazione di HDInsight
Oltre al portale di Azure sono disponibili altri strumenti per amministrare HDInsight.

- [Amministrare HDInsight utilizzando l’interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md): l’interfaccia della riga di comando di Azure è uno strumento della riga di comando multipiattaforma che consente di gestire i servizi di Azure

- [Amministrare HDInsight tramite Azure PowerShell](hdinsight-administer-use-powershell.md): Azure PowerShell fornisce cmdlet di PowerShell per la gestione dei servizi di Azure

## Prerequisiti

Per eseguire le procedure descritte nell'articolo è necessario:

- **Una sottoscrizione di Azure**. Vedere [Ottenere una versione di valutazione gratuita di Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

## Creare cluster HDInsight

È possibile creare cluster HDInsight nel portale di Azure. Questo argomento è descritto dettagliatamente nel documento [Creare cluster basati su Linux in HDInsight tramite il portale di Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

## Gestire un cluster

Se si seleziona un cluster nel portale di Azure vengono visualizzate le informazioni di base sul cluster, ad esempio il nome, il gruppo di risorse, il sistema operativo e l'URL del cluster usato per l'accesso ad Ambari Web per i cluster Linux.

![Dettagli cluster](./media/hdinsight-administer-use-portal-linux/clusterdetails.png)

Usare le informazioni seguenti per comprendere le icone nella parte superiore di questo pannello e nelle sezioni __Informazioni di base__ e __Collegamenti__:

* __Impostazioni__ e __Tutte le impostazioni__: visualizzano il pannello__Impostazioni__ per il cluster, che consente di accedere a informazioni dettagliate sulla configurazione del cluster.

* __Dashboard__, __Dashboard cluster__ e __URL__: sono tutti modi per accedere al dashboard del cluster. A seconda del tipo di cluster, è possibile che venga visualizzato un elenco dei dashboard del cluster. Ad esempio, il tipo di cluster Spark visualizza un elenco di dashboard quando si seleziona l'icona __Dashboard__, mentre un cluster Hadoop apre l'interfaccia utente di Ambari Web.

* __Secure Shell__: informazioni necessarie per accedere al nodo head del cluster tramite SSH.

* __Scala Cluster__: consente di modificare il numero di nodi del ruolo di lavoro per questo cluster.

* __Elimina__: consente di eliminare il cluster HDInsight.

* __Guide rapide__ (![icona cloud e thunderbolt = guida rapida](./media/hdinsight-administer-use-portal-linux/quickstart.png)): Visualizza le informazioni che consentiranno di iniziare a utilizzare HDInsight.

* __Utenti (![icona utenti](./media/hdinsight-administer-use-portal-linux/users.png))__: consente di impostare le autorizzazioni per _Gestione portale_ di questo cluster per altri utenti nella sottoscrizione Azure.

	> [AZURE.IMPORTANT] Questo influisce _solo_ sull'accesso e sulle autorizzazioni per tale cluster nel portale di Azure e non ha alcun effetto su chi può connettersi o inviare processi al cluster HDInsight.

* __Tag (![icona tag](./media/hdinsight-administer-use-portal-linux/tags.png))__: consente di impostare coppie chiave/valore per definire una tassonomia dei servizi cloud personalizzata. Ad esempio, è possibile creare una chiave denominata __progetto__, e usare un valore comune per tutti i servizi associati a un progetto specifico.

* __Documentazione__: collegamenti alla documentazione per Azure HDInsight.

> [AZURE.IMPORTANT] Per gestire i servizi forniti dal cluster HDInsight, è necessario utilizzare Ambari Web o l'API REST di Ambari. Per altre informazioni sull'uso di Ambari, vedere [Gestire i cluster HDInsight tramite Ambari](hdinsight-hadoop-manage-ambari.md).

### Impostazioni

Se si seleziona l'icona __Impostazioni__ o il collegamento __Tutte le impostazioni__, viene visualizzato il pannello Impostazioni. Molte delle funzioni disponibili nell'area __Informazioni di base__ descritte in precedenza sono disponibili anche qui, ad esempio il ridimensionamento e le informazioni di Secure Shell. Da Impostazioni è possibile accedere anche alle impostazioni seguenti:

* __Log di controllo__: informazioni registrate utili per diagnosticare i problemi di integrità dei cluster.

* __Account di accesso del cluster__: visualizza il __Nome utente dell'account di accesso del cluster__ e l'__Indirizzo remoto__ che è possibile usare per l'accesso al cluster tramite HTTPS.

* __Metastore esterni__: visualizza informazioni su eventuali metastore esterni usati dal cluster. Se non è stato configurato un metastore personalizzato durante la configurazione del cluster, non viene visualizzata alcuna informazione.

* __Azioni script__: visualizza informazioni sulle azioni script eseguite nel cluster. È anche possibile eseguire nuove azioni script e mantenere o eliminare le azioni script eseguite in precedenza. Per altre informazioni sulle azioni script, vedere [Personalizzare cluster HDInsight tramite azione script](hdinsight-hadoop-customize-cluster-linux.md).

* __App__: visualizza informazioni sulle app installate nel cluster e consente di aggiungere nuove applicazioni al cluster da Azure Marketplace.

* __Chiavi di archiviazione di Azure__: visualizza informazioni sugli account di archiviazione di Azure usati dal cluster. Se si seleziona un account di archiviazione, viene caricato il pannello dell'account di archiviazione per l'account selezionato.

* __Identità AAD del cluster__: visualizza informazioni sull'entità servizio del cluster HDInsight. L'entità servizio consente di accedere all'archivio Azure Data Lake. Se durante la creazione il cluster non è stato associato all'archivio Azure Data Lake, le voci in questo pannello appaiono come __Non configurato__. Per altre informazioni sull'uso dell'archivio Azure Data Lake con HDInsight, vedere [Creare un cluster HDInsight con Archivio Data Lake](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

### <a name="scaling"></a>Ridimensionamento

Per ridimensionare un cluster tramite il portale, selezionare il cluster HDInsight e quindi __Ridimensiona cluster__. Specificare il __Numero di nodi di lavoro__ da impostare per il cluster e quindi fare clic su __Salva__.

![immagine dell'interfaccia utente di ridimensionamento](./media/hdinsight-administer-use-portal-linux/scaling.png)

Per altre informazioni sulle operazioni di ridimensionamento, vedere [Informazioni sull'uso di HDInsight in Linux](hdinsight-hadoop-linux-information.md#scaling).

## Monitorare un cluster

La sezione __Utilizzo__ del pannello del cluster HDInsight visualizza informazioni relative al numero di core disponibili per la sottoscrizione da usare con HDInsight, il numero di core allocati al cluster e il modo in cui vengono allocati per i nodi nel cluster.

![Informazioni sull'utilizzo](./media/hdinsight-administer-use-portal-linux/usage.png)

> [AZURE.IMPORTANT] Per monitorare i servizi forniti dal cluster HDInsight, è necessario utilizzare Ambari Web o l'API REST di Ambari. Per ulteriori informazioni sull'utilizzo di Ambari, vedere [Gestire i cluster HDInsight tramite Ambari](hdinsight-hadoop-manage-ambari.md)

## Passaggi successivi
In questo articolo è stato illustrato come creare un cluster HDInsight tramite il portale di Azure e come aprire lo strumento da riga di comando di Hadoop. Per altre informazioni, vedere gli articoli seguenti:

* [Amministrare HDInsight tramite Azure PowerShell](hdinsight-administer-use-powershell.md)
* [Amministrare HDInsight tramite l’interfaccia della riga di comando di Azure](hdinsight-administer-use-command-line.md)
* [Effettuare il provisioning di cluster HDInsight](hdinsight-provision-clusters.md)
* [Inviare processi Hadoop a livello di codice](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Introduzione ad Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Versione di Hadoop inclusa in Azure HDInsight](hdinsight-component-versioning.md)

[preview-portal]: https://portal.azure.com

<!---HONumber=AcomDC_0406_2016-->