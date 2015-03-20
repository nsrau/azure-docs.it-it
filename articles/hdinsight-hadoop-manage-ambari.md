<properties
   pageTitle="Gestire i cluster HDInsight tramite Ambari | Azure"
   description="Informazioni sull'uso di Ambari per monitorare e gestire cluster HDInsight basati su Linux."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Gestione di cluster HDInsight tramite Ambari (anteprima)

Vengono fornite informazioni su come usare Ambari per gestire e monitorare cluster HDInsight basati su Linux.

> [AZURE.NOTE] Molte delle informazioni contenute in questo articolo si applicano solo ai cluster HDInsight basati su Linux. Per i cluster HDInsight basati su Windows, il monitoraggio è disponibile solo tramite l'API REST Ambari. Vedere [Monitorare i cluster Hadoop in HDInsight tramite l'API Ambari](../hdinsight-monitor-use-ambari-api/).

## <a id="whatis"></a>Informazioni su Ambari

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> semplifica la gestione di Hadoop, fornendo un'interfaccia utente Web intuitiva che può essere usata per eseguire il provisioning, la gestione e il monitoraggio dei cluster Hadoop. Gli sviluppatori possono integrare queste funzionalità nelle proprie applicazioni usando le <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">API REST Ambari</a>. 

Ambari viene fornito per impostazione predefinita con i cluster HDInsight basati su Linux. I cluster HDInsight basati su Windows forniscono funzionalità di monitoraggio tramite le API REST Ambari.

## Proxy SSH

> [AZURE.NOTE] Sebbene Ambari per il cluster sia accessibile direttamente su Internet, alcune funzionalità si basano sull'accesso ai nodi con il nome di dominio interno usato dal cluster. Poiché si tratta di un nome di dominio interno e non pubblico, si riceveranno errori di server non trovato se si tenta di accedere ad alcune funzionalità tramite Internet.

Per risolvere questo problema, usare un tunnel SSH per inoltrare il traffico Web al nodo head del cluster, che è in grado di risolvere i nomi di dominio interno. Usare i seguenti articoli per creare un tunnel SSH da una porta nel computer locale al cluster.

* <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X</a>: passaggi relativi alla creazione di un tunnel SSH tramite il comando `ssh`

* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Usare SSH con Hadoop basato su Linux in HDInsight da Windows</a>: passaggi relativi all'uso di PuTTY per creare un tunnel SSH

## Interfaccia utente Web Ambari

L'interfaccia utente Web di Ambari è disponibile in ogni cluster HDInsight basato su Linux creato in **https://&lt;clustername>.azurehdinsight.net**. È anche possibile passare a questa pagina usando il pulsante **Ambari Web** nella parte inferiore del dashboard del cluster nel portale Azure.

![ambari web icon](./media/hdinsight-hadoop-manage-ambari/ambari-web.png)

Verrà richiesto di eseguire l'autenticazione per la pagina due volte. La prima per l'autenticazione per il cluster HDInsight e la seconda per l'autenticazione per Ambari.

* **Autenticazione cluster**: usare il nome utente (il valore predefinito è **admin**) e la password di amministratore cluster

* **Autenticazione Ambari**: il valore predefinito per il nome utente e la password è **admin**

	> [AZURE.NOTE] Se è stata modificata la password per il nome utente **admin**, sarà necessario immettere la nuova password.

Quando si apre la pagina, si noti la barra nella parte superiore. Contiene i seguenti controlli e le seguenti informazioni:

![ambari-nav](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Logo Ambari**: apre il **Dashboard**.

* **Numero di operazioni cluster**: visualizza il numero di operazioni in corso di Ambari. Se si seleziona il nome del cluster o **# ops**, verrà visualizzato un elenco delle operazioni in background.

* **# alerts**: eventuali messaggi di avviso o critici per il cluster. Se si seleziona questa opzione, verrà visualizzato un elenco di avvisi.

* **Dashboard**: visualizza il dashboard, che può essere usato per monitorare il cluster.

* **Services**: informazioni e impostazioni di configurazione per i servizi del cluster.

* **Hosts**: informazioni e impostazioni di configurazione per i nodi del cluster.

* **Alerts**: informazioni, avvertenze e avvisi critici.

* **Admin**: servizi e stack software che vengono installati o possono essere aggiunti al cluster, informazioni sull'account di servizio e sicurezza Kerberos.

* **Pulsante admin**: gestione di Ambari, impostazioni utente e disconnessione.

### Monitoraggio

#### Avvisi

Ambari fornisce numerosi avvisi, che potranno avere uno dei seguenti stati:

* **OK**

* **Warning**

* **CRITICAL**

* **UNKNOWN**

Gli avvisi con stato diverso da **OK** determineranno la compilazione del campo **# alerts** nella parte superiore della pagina con il numero di avvisi. Se si seleziona questa opzione, verranno visualizzati gli avvisi e il relativo stato.

Gli avvisi sono organizzati in diversi gruppi predefiniti, che possono essere visualizzati dalla pagina **Alerts**. 

![alerts page](./media/hdinsight-hadoop-manage-ambari/alerts.png)

È possibile gestire i gruppi scegliendo **Manage Alert Groups** dal menu **Actions**. In questo modo è possibile modificare i gruppi esistenti o crearne dei nuovi.

![manage alert groups dialog](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

È inoltre possibile creare notifiche scegliendo **Alert Notifications** dal menu **Actions**. In questo modo è possibile creare trigger che inviano notifiche tramite **e-mail** o **SNMP** quando si verificano combinazioni specifiche di avviso/gravità. Ad esempio, è possibile inviare un avviso quando uno degli avvisi nel gruppo **YARN Default** è impostato su **Critical**.

![Create alert dialog](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

#### Cluster

La scheda **Metrics** del **Dashboard** contiene una serie di widget che consentono di monitorare lo stato del cluster in modo immediato. Widget diversi, ad esempio **CPU Usage**, forniscono informazioni aggiuntive quando vengono selezionati.

![dashboard with metrics](./media/hdinsight-hadoop-manage-ambari/metrics.png)

La scheda **Heatmaps** visualizza le metriche come mappe termiche colorate, dal verde al rosso.

![dashboard with heatmaps](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Per altre informazioni sui nodi del cluster, selezionare **Hosts**, quindi il nodo specifico desiderato.

![host details](./media/hdinsight-hadoop-manage-ambari/host-details.png)

#### Servizio

La barra laterale **Servizi** sul dashboard fornisce informazioni rapide sullo stato dei servizi in esecuzione nel cluster. Vengono usate diverse icone per indicare lo stato o le azioni da intraprendere, ad esempio un simbolo giallo di riciclo indica che un servizio deve essere riciclato.

![services side-bar](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

Selezionando un servizio verranno visualizzate informazioni più dettagliate su di esso.

![service summary information](./media/hdinsight-hadoop-manage-ambari/service-details.png)

##### Collegamenti rapidi

Alcuni servizi visualizzano un collegamento **Quick Links** nella parte superiore della pagina. Questi possono essere usati per accedere all'interfaccia utente Web specifica del servizio, ad esempio:

* **Job History**: cronologia dei processi MapReduce

* **Resource Manager**: interfaccia utente di gestione risorse di YARN

* **NameNode**: interfaccia utente HDFS NameNode

* **Oozie Web UI**: interfaccia utente Oozie

Selezionando uno di questi collegamenti, verrà aperta una nuova scheda nel browser che visualizzerà la pagina selezionata.

> [AZURE.NOTE] Se si seleziona un collegamento **Quick Links** per un servizio, verrà visualizzato un errore di server non trovato a meno che non si usi un tunnel SSL per inoltrare il traffico Web al cluster. Infatti, Ambari usa il nome di dominio interno per questi collegamenti.
> 
> Per informazioni sull'uso di un tunnel SSL con HDInsight, vedere uno dei seguenti argomenti:
> 
> * <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Usare SSH con Hadoop basato su Linux in HDInsight da Linux, Unix o OS X</a>: passaggi relativi alla creazione di un tunnel SSH tramite il comando `ssh`
>
>* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Usare SSH con Hadoop basato su Linux in HDInsight da Windows</a>: passaggi relativi all'uso di PuTTY per creare un tunnel SSH

### Gestione

#### Utenti, gruppi e autorizzazioni Ambari

Non eseguire la gestione di utenti, gruppi e autorizzazioni con l'anteprima di HDInsight basato su Linux.

#### Host

La pagina **Hosts** elenca tutti gli host del cluster. Per gestire gli host, seguire questa procedura.

![hosts page](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [AZURE.NOTE] Non eseguire l'aggiunta, la rimozione o il ripristino di un host con i cluster HDInsight.

1. Selezionare uno o più host che si desidera gestire.

2. Usare il menu **Actions** per selezionare l'azione da eseguire.

	* **Start all components**: avvia tutti i componenti nell'host.

	* **Stop all components**: arresta tutti i componenti nell'host.

	* **Restart all components**: arresta e avvia tutti i componenti nell'host.

	* **Turn on maintenance mode**: elimina gli avvisi per l'host. Questa opzione deve essere abilitata se si eseguono azioni che genereranno avvisi, ad esempio il riavvio di un servizio da cui dipendono i servizi in esecuzione.

	* **Turn off maintenance mode**: ripristina la gestione normale degli avvisi dell'host.

	* **Stop**: arresta DataNode o NodeManagers nell'host.

	* **Start**: avvia DataNode o NodeManagers nell'host.

	* **Restart**: arresta e avvia DataNode o NodeManagers nell'host.

	* **Decommission**: rimuove un host dal cluster.

		> [AZURE.NOTE] Non usare questa azione nei cluster HDInsight.

	* **Recommission**: aggiunge al cluster un host precedentemente rimosso.

		> [AZURE.NOTE] Non usare questa azione nei cluster HDInsight.

#### <a id="service"></a>Servizio

Nella pagina **Dashboard** o **Services** usare il pulsante **Actions** nella parte inferiore dell'elenco di servizi per **aggiungere** nuovi servizi o **arrestare** e **avviare** tutti i servizi.

![service actions](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

Di seguito è riportata la procedura generale per **aggiungere un servizio**:

1. Nella pagina **Dashboard** o **Services** fare clic sul pulsante**Actions** e selezionare **Add Service**.

2. In **Add Service Wizard** selezionare il servizio da aggiungere, quindi fare clic su **Next**.

	![add service](./media/hdinsight-hadoop-manage-ambari/add-service.png)

3. Continuare la procedura guidata specificando le informazioni di configurazione per il servizio. Per altre informazioni sui requisiti di configurazione, consultare la documentazione relativa al servizio specifico che si desidera aggiungere.

4. Nella pagina **Review** è possibile selezionare **Print** per stampare le informazioni di configurazione o **Deploy** per distribuire il servizio nel cluster.

5. Una volta distribuito il servizio, la pagina **Install, Start and Test** visualizzerà le informazioni sullo stato man mano che il servizio viene installato e testato. Quando la barra nella colonna **Status** è verde, fare clic su **Next**.

	![image of install, start, and test page](./media/hdinsight-hadoop-manage-ambari/install-start-test.png)

6. La pagina **Summary** visualizza un riepilogo del processo di installazione, nonché le possibili azioni da eseguire, ad esempio il riavvio di altri servizi. Selezionare **Complete** per uscire dalla procedura guidata.

Il pulsante **Actions** consente di riavviare tutti i servizi, ma spesso si desidera avviare, arrestare o riavviare un servizio specifico. Seguire questa procedura per **effettuare azioni per un singolo servizio**:

1. Nella pagina **Dashboard** o **Services** selezionare un servizio.

2. Nella parte superiore della scheda **Summary** fare clic sul pulsante **Service Actions** e selezionare l'azione da intraprendere. Il servizio verrà riavviato in tutti i nodi.

	![service action](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

	> [AZURE.NOTE] Se si riavviano alcuni servizi mentre il cluster è in esecuzione, è possibile che vengano generati avvisi. Per evitare questo problema, è possibile abilitare la modalità di manutenzione facendo clic sul pulsante **Service Actions** e selezionando **Turn On Maintenance Mode** per il servizio prima di eseguire il riavvio.

3. Dopo aver selezionato un'azione, il valore nel campo **# op** nella parte superiore della pagina aumenterà per indicare che è in corso un'operazione in background. Se configurato, verrà visualizzato l'elenco delle operazioni in background.

	> [AZURE.NOTE] Se è stata abilitata la modalità di manutenzione selezionando **Turn On Maintenance Mode** per il servizio, ricordarsi di disabilitarla mediante il pulsante **Service Actions** al termine dell'operazione.

Per **configurare un servizio**, seguire questa procedura:

1. Nella pagina **Dashboard** o **Services** selezionare un servizio.

2. Selezionare la scheda **Configs**. Verrà visualizzata la configurazione corrente insieme a un elenco delle configurazioni precedenti.

	![configurations](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Usare i campi visualizzati per modificare la configurazione, quindi selezionare **Save**. In alternativa, selezionare una configurazione precedente, quindi **Make current** per ripristinare le impostazioni precedenti.

## API REST

Ambari Web si basa su un'API REST sottostante, che è possibile usare per creare i propri strumenti di gestione e monitoraggio. Sebbene l'API sia relativamente semplice da usare, esistono alcuni aspetti specifici di Azure di cui tenere conto.

* **Autenticazione**: è necessario usare il nome utente (impostazione predefinita **admin**)
*  e la password di amministratore cluster per l'autenticazione per il servizio.

* **Sicurezza**: Ambari usa l'autenticazione di base, pertanto è consigliabile usare sempre HTTPS per le comunicazioni con l'API.

* **Indirizzi IP**: gli indirizzi restituiti per gli host all'interno di un cluster non sono accessibili dall'esterno del cluster, a meno che il cluster non sia membro di una rete virtuale di Azure. Gli indirizzi IP saranno quindi accessibili per altri membri della rete virtuale, ma non dall'esterno della rete.

* **Alcune funzionalità non sono abilitate**: alcune funzionalità di Ambari sono disabilitate, poiché vengono gestite dal servizio cloud HDInsight, ad esempio l'aggiunta o la rimozione di host dal cluster. Altre funzionalità potrebbero non essere implementate completamente con l'anteprima di HDInsight basato su Linux.

Per informazioni tecniche complete sull'API REST, vedere la pagina relativa alle [informazioni di riferimento per l'API Ambari V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).


<!--HONumber=47-->
