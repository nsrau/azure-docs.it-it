<properties
   pageTitle="Gestire i cluster HDInsight con Ambari | Microsoft Azure"
   description="Informazioni sull'uso di Ambari per monitorare e gestire cluster HDInsight basati su Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/23/2015"
   ms.author="larryfr"/>

#Gestire i cluster HDInsight usando Ambari (anteprima)

Vengono fornite informazioni su come usare Ambari per gestire e monitorare cluster HDInsight di Azure basati su Linux.

> [AZURE.NOTE]Molte delle informazioni contenute in questo articolo si applicano solo ai cluster HDInsight basati su Linux. Per i cluster HDInsight basati su Windows, il monitoraggio è disponibile solo tramite l'API REST Ambari. Vedere [Monitorare Hadoop basato su Windows in HDInsight con l'API Ambari](hdinsight-monitor-use-ambari-api.md).

##<a id="whatis"></a>Cos'è Ambari?

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> semplifica la gestione di Hadoop, fornendo un'interfaccia utente Web intuitiva che può essere usata per effettuare il provisioning, la gestione e il monitoraggio dei cluster Hadoop. Gli sviluppatori possono integrare queste funzionalità nelle proprie applicazioni usando le <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">API REST Ambari</a>.

Ambari viene fornito per impostazione predefinita con i cluster HDInsight basati su Linux. I cluster HDInsight basati su Windows forniscono funzionalità di monitoraggio tramite le API REST Ambari.

##Proxy SSH

> [AZURE.NOTE]Mentre Ambari per il cluster è accessibile direttamente tramite Internet, alcuni collegamenti dall'interfaccia utente Web di Ambari (ad esempio JobTracker) non sono esposte in Internet. Di conseguenza, quando si tenta di accedere a queste funzionalità, verranno visualizzati errori di "server non trovato", a meno di usare un tunnel Secure Shell (SSH) per il proxy del traffico Web al nodo head del cluster.

Per informazioni sulla creazione di un tunnel SSH per lavorare con Ambari, vedere [Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, ResourceManager, JobHistory, NameNode, Oozie e altre interfacce Web](hdinsight-linux-ambari-ssh-tunnel.md).

##Interfaccia utente Web Ambari

L'interfaccia utente Web di Ambari è disponibile in ogni cluster HDInsight basato creato in **https://&lt;clustername>.azurehdinsight.net**.

Verrà richiesto di eseguire l'autenticazione per la pagina due volte. La prima per l'autenticazione per il cluster HDInsight e la seconda per l'autenticazione per Ambari.

* **Autenticazione cluster**: usare il nome utente (il valore predefinito è **admin**) e la password di amministratore cluster

* **Autenticazione Ambari**: il valore predefinito per il nome utente e la password è **admin**.

	> [AZURE.NOTE]Se è stata modificata la password per il nome utente **admin**, sarà necessario immettere la nuova password.

Quando si apre la pagina, si noti la barra in alto, che contiene le informazioni e i controlli seguenti:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Logo di Ambari**: apre il dashboard, che può essere usato per monitorare il cluster.

* **Numero di operazioni cluster**: visualizza il numero di operazioni in corso di Ambari. Se si seleziona il nome del cluster o **# ops**, verrà visualizzato un elenco delle operazioni in background.

* **# alerts**: eventuali messaggi di avviso o critici per il cluster. Se si seleziona questa opzione, verrà visualizzato un elenco di avvisi.

* **Dashboard**: visualizza il dashboard.

* **Services**: informazioni e impostazioni di configurazione per i servizi del cluster.

* **Hosts**: informazioni e impostazioni di configurazione per i nodi del cluster.

* **Alerts**: log di informazioni, avvertenze e avvisi critici.

* **Admin**: servizi e stack software che vengono installati nel cluster, informazioni sull'account di servizio e sicurezza Kerberos.

* **Pulsante admin**: gestione di Ambari, impostazioni utente e disconnessione.

##Monitoraggio

###Avvisi

Ambari fornisce numerosi avvisi, che potranno avere uno dei seguenti stati:

* **OK**

* **Warning**

* **CRITICAL**

* **UNKNOWN**

Gli avvisi con stato diverso da **OK** determineranno la compilazione del campo **# alerts** nella parte superiore della pagina con il numero di avvisi. Se si seleziona questa opzione, verranno visualizzati gli avvisi e il relativo stato.

Gli avvisi sono organizzati in diversi gruppi predefiniti, che possono essere visualizzati dalla pagina **Alerts**.

![pagina degli avvisi](./media/hdinsight-hadoop-manage-ambari/alerts.png)

È possibile gestire i gruppi scegliendo **Manage Alert Groups** dal menu **Actions**. In questo modo è possibile modificare i gruppi esistenti o crearne dei nuovi.

![finestra di dialogo Manage Alert Groups](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

È inoltre possibile creare notifiche scegliendo Alert Notifications dal menu **Actions**. In questo modo è possibile creare trigger che inviano notifiche tramite **EMAIL** o **SNMP** quando si verificano combinazioni specifiche di avviso/gravità. Ad esempio, è possibile inviare un avviso quando uno degli avvisi nel gruppo **YARN Default** è impostato su **Critical**.

![finestra di dialogo Create Alert](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

###HDInsight

La scheda **Metrics** del dashboard contiene una serie di widget che consentono di monitorare lo stato del cluster in modo immediato. Widget diversi, ad esempio **CPU Usage**, forniscono informazioni aggiuntive quando vengono selezionati.

![dashboard con metriche](./media/hdinsight-hadoop-manage-ambari/metrics.png)

La scheda **Heatmaps** visualizza le metriche come mappe termiche colorate, dal verde al rosso.

![dashboard con mappe termiche](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Per altre informazioni sui nodi del cluster, selezionare **Hosts**, quindi il nodo specifico desiderato.

![dettagli dell'host](./media/hdinsight-hadoop-manage-ambari/host-details.png)

###Servizi

La barra laterale **Services** sul dashboard fornisce informazioni rapide sullo stato dei servizi in esecuzione nel cluster. Vengono usate diverse icone per indicare lo stato o le azioni da intraprendere, ad esempio un simbolo giallo di riciclo indica che un servizio deve essere riciclato.

![barra laterale dei servizi](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

Selezionando un servizio verranno visualizzate informazioni più dettagliate su di esso.

![informazioni di riepilogo sul servizio](./media/hdinsight-hadoop-manage-ambari/service-details.png)

####Collegamenti rapidi

Alcuni servizi visualizzano un collegamento **Quick Links** nella parte superiore della pagina. Questi possono essere usati per accedere alle interfacce utente Web specifiche del servizio, ad esempio:

* **Job History**: cronologia dei processi MapReduce.

* **Resource Manager**: interfaccia utente di gestione risorse di YARN.

* **NameNode**: interfaccia utente NameNode del file system distribuito Hadoop (HDFS).

* **Oozie Web UI**: interfaccia utente Oozie.

Selezionando uno di questi collegamenti, verrà aperta una nuova scheda nel browser che visualizzerà la pagina selezionata.

> [AZURE.NOTE]Se si seleziona un collegamento **Quick Links** per un servizio, verrà visualizzato un errore di "server non trovato" a meno che non si usi un tunnel SSL (Secure Sockets Layer) per inoltrare il traffico Web al cluster. Questo comportamento è dovuto al fatto che le applicazioni Web usate per visualizzare queste informazioni non sono esposte in Internet.
>
> Per informazioni sull'uso di un tunnel SSL con HDInsight, vedere [Usare il tunneling SSH per accedere all'interfaccia Web di Ambari, ResourceManager, JobHistory, NameNode, Oozie e altre interfacce Web](hdinsight-linux-ambari-ssh-tunnel.md)

##gestione

###Utenti, gruppi e autorizzazioni Ambari

Non eseguire la gestione di utenti, gruppi e autorizzazioni con l'anteprima di HDInsight basato su Linux.

###Hosts

La pagina **Hosts** elenca tutti gli host del cluster. Per gestire gli host, seguire questa procedura.

![pagina Hosts](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [AZURE.NOTE]Non eseguire l'aggiunta, la rimozione o il ripristino di un host con i cluster HDInsight.

1. Selezionare uno o più host che si desidera gestire.

2. Usare il menu **Actions** per selezionare l'azione da eseguire:

	* **Start all components**: avvia tutti i componenti nell'host.

	* **Stop all components**: arresta tutti i componenti nell'host.

	* **Restart all components**: arresta e avvia tutti i componenti nell'host.

	* **Turn on maintenance mode**: elimina gli avvisi per l'host. Questa opzione deve essere abilitata se si eseguono azioni che genereranno avvisi, ad esempio il riavvio di un servizio da cui dipendono i servizi in esecuzione.

	* **Turn off maintenance mode**: ripristina la gestione normale degli avvisi dell'host.

	* **Stop**: arresta DataNode o NodeManagers nell'host.

	* **Start**: avvia DataNode o NodeManagers nell'host.

	* **Restart**: arresta e avvia DataNode o NodeManagers nell'host.

	* **Decommission**: rimuove un host dal cluster.

		> [AZURE.NOTE]Non usare questa azione nei cluster HDInsight.

	* **Recommission**: aggiunge al cluster un host precedentemente rimosso.

		> [AZURE.NOTE]Non usare questa azione nei cluster HDInsight.

###<a id="service"></a>Services

Nella pagina **Dashboard** o **Services** usare il pulsante **Actions** nella parte inferiore dell'elenco di servizi per arrestare e avviare tutti i servizi.

![azioni dei servizi](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [AZURE.WARNING]Sebbene __Aggiungi servizio__ sia elencato in questo menu, non deve essere utilizzato per aggiungere servizi al cluster HDInsight. È necessario aggiungere nuovi servizi.utilizzando un'azione di Script durante il provisioning del cluster. Per altre informazioni su come utilizzare le azioni di script, vedere [Personalizzare cluster HDInsight mediante l'azione script](hdinsight-hadoop-customize-cluster-linux.md).


Il pulsante **Actions** consente di riavviare tutti i servizi, ma spesso si desidera avviare, arrestare o riavviare un servizio specifico. Seguire questa procedura per effettuare azioni per un singolo servizio:

1. Nella pagina **Dashboard** o **Services** selezionare un servizio.

2. Nella parte superiore della scheda **Summary** fare clic sul pulsante **Service Actions** e selezionare l'azione da intraprendere. Il servizio verrà riavviato in tutti i nodi.

	![azione del servizio](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

	> [AZURE.NOTE]Se si riavviano alcuni servizi mentre il cluster è in esecuzione, è possibile che vengano generati avvisi. Per evitare questo problema, è possibile abilitare la modalità di manutenzione facendo clic sul pulsante **Service Actions** e selezionando **Turn On Maintenance Mode** per il servizio prima di eseguire il riavvio.

3. Dopo aver selezionato un'azione, il valore nel campo **# op** nella parte superiore della pagina aumenterà per indicare che è in corso un'operazione in background. Se configurato, viene visualizzato l'elenco delle operazioni in background.

	> [AZURE.NOTE]Se è stata abilitata la modalità di manutenzione selezionando **Turn On Maintenance Mode** per il servizio, ricordarsi di disabilitarla usando il pulsante **Service Actions** al termine dell'operazione.

Per configurare un servizio, seguire questa procedura:

1. Nella pagina **Dashboard** o **Services** selezionare un servizio.

2. Selezionare la scheda **Configs**. Verrà visualizzata la configurazione corrente insieme a un elenco delle configurazioni precedenti.

	![configurazioni](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Usare i campi visualizzati per modificare la configurazione, quindi selezionare **Save**. In alternativa, selezionare una configurazione precedente, quindi **Make current** per ripristinare le impostazioni precedenti.

##API REST

Ambari Web si basa su un'API REST sottostante, che è possibile usare per creare i propri strumenti di gestione e monitoraggio. Sebbene l'API sia relativamente semplice da usare, esistono alcuni aspetti specifici di Azure di cui tenere conto:

* **Autenticazione**: è necessario usare il nome utente (quello predefinito è **admin**) e la password di amministratore cluster per l'autenticazione per il servizio.

* **Sicurezza**: Ambari usa l'autenticazione di base, pertanto è consigliabile usare sempre HTTP sicuro (HTTPS) per le comunicazioni con l'API.

* **Indirizzi IP**: gli indirizzi restituiti per gli host all'interno di un cluster non sono accessibili dall'esterno del cluster, a meno che il cluster non sia membro di una rete virtuale di Azure. Gli indirizzi IP saranno quindi accessibili per altri membri della rete virtuale, ma non dall'esterno della rete.

* **Alcune funzionalità non sono abilitate**: alcune funzionalità di Ambari sono disabilitate, poiché vengono gestite dal servizio cloud HDInsight, ad esempio l'aggiunta o la rimozione di host dal cluster o l’aggiunta di nuovi servizi. Altre funzionalità potrebbero non essere implementate completamente con l'anteprima di HDInsight basato su Linux.

Per informazioni tecniche complete sull'API REST, vedere la pagina relativa alle [informazioni di riferimento per l'API Ambari V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

<!---HONumber=Oct15_HO1-->