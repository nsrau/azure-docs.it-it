<properties linkid="" urlDisplayName="" pageTitle="" metaKeywords="" description="Learn how to use Microsoft HPC Pack and Azure to set up a small, hybrid high performance computing (HPC) cluster" metaCanonical="" services="" documentationCenter="" title="Set up a Hybrid Cluster with Microsoft HPC Pack" authors="danlep" solutions="" manager="dongill" editor="mattshel" />

Configurazione di un cluster ibrido con Microsoft HPC Pack
==========================================================

In questa esercitazione viene illustrato come utilizzare Microsoft HPC Pack 2012 R2 e Azure per configurare un piccolo cluster di elaborazione ad alte prestazioni (HPC) ibrido. Il cluster sarà composto da un nodo head locale (un computer che esegue il sistema operativo Windows Server e HPC Pack) e alcuni nodi di calcolo distribuiti su richiesta come istanze del ruolo di lavoro in un servizio cloud di Azure. In seguito sarà possibile eseguire processi di calcolo nel cluster ibrido.

![Cluster HPC ibrido](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/hybrid_cluster_overview.png)

L'approccio descritto in questa esercitazione, denominato anche "potenziamento nel cloud", consente di utilizzare risorse di calcolo su richiesta in Azure per eseguire applicazioni a elevato utilizzo di calcolo.

Questa esercitazione non presuppone che si abbia esperienza nell'ambito dei cluster di calcolo o di HPC Pack. Viene fornita solo per aiutare l'utente a distribuire rapidamente un cluster ibrido a scopo dimostrativo. Per considerazioni e procedure per la distribuzione di un cluster HPC Pack ibrido su scala maggiore in un ambiente di produzione, vedere le [indicazioni dettagliate](http://go.microsoft.com/fwlink/p/?LinkID=200493).

> [WACOM.NOTE] Azure offre una [gamma di dimensioni](http://go.microsoft.com/fwlink/p/?LinkId=389844) per le risorse di calcolo, adatta a diversi carichi di lavoro. Le istanze A8 e A9, ad esempio, combinano prestazioni elevate e accesso a una rete di applicazioni a bassa latenza e velocità effettiva elevata necessaria per determinate applicazioni HPC. Per informazioni, vedere [Istanze con utilizzo intensivo di calcolo A8 e A9: avvio rapido con HPC Pack](http://go.microsoft.com/fwlink/p/?Linkid=328042).

In questa esercitazione vengono descritte le operazioni di base seguenti:

-   [Prerequisiti](#BKMK_Prereq)
-   [Installazione di HPC Pack nel nodo head](#BKMK_DeployHN)
-   [Preparazione della sottoscrizione di Azure](#BKMK_Prpare)
-   [Configurazione del nodo head](#BKMK_ConfigHN)
-   [Aggiunta di nodi di Azure al cluster](#BKMK_worker)
-   [Avvio dei nodi di Azure](#BKMK_start)
-   [Esecuzione di un comando nel cluster](#BKMK_RunCommand)
-   [Esecuzione di un processo di test](#BKMK_RunJob)
-   [Interruzione dei nodi di Azure](#BKMK_stop)

Prerequisiti
------------

> [WACOM.NOTE]Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/it-it/pricing/free-trial/).

Per completare l'esercitazione, è necessario anche quanto segue.

-   Un computer locale che esegue un'edizione di Windows Server 2012 R2 o Windows Server 2012. Questo computer sarà il nodo head del cluster HPC. Se Windows Server non è già in esecuzione, è possibile scaricare e installare una [versione di valutazione](http://technet.microsoft.com/evalcenter/dn205286.aspx).

    -   Il computer deve essere aggiunto a un dominio Active Directory.

    -   Verificare che non siano installati ulteriori ruoli server o servizi di ruolo.

    -   Per supportare HPC Pack, è necessario che il sistema operativo sia installato in una delle lingue seguenti: inglese, giapponese o cinese (semplificato).

    -   Verificare che siano installati gli aggiornamenti importanti.

-   I file di installazione per HPC Pack 2012 R2, disponibile gratuitamente. [Scaricare](http://go.microsoft.com/fwlink/p/?linkid=389557) il pacchetto di installazione completo e copiare i file nel computer nodo head o in un percorso di rete. Scegliere i file di installazione nella stessa lingua dell'installazione di Windows Server.

-   Un account di dominio con autorizzazioni di amministratore locale sul nodo head.

-   Connettività TCP sulla porta 443 dal nodo head ad Azure.

Installazione di HPC Pack nel nodo head
---------------------------------------

Installare innanzitutto Microsoft HPC Pack in un computer locale che sarà il nodo head del cluster.

1.  Accedere al nodo head utilizzando un account di dominio che dispone di autorizzazione di amministratore locale.

2.  Avviare l'installazione guidata di HPC Pack eseguendo Setup.exe dai file di installazione di HPC Pack.

3.  Nella schermata **HPC Pack 2012 R2 Setup** fare clic su **New installation or add new features to an existing installation**.

    ![Installazione di HPC Pack 2012](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc1.png)

4.  Nella pagina **Microsoft Software User Agreement** fare clic su **Next**.

5.  Nella pagina **Select Installation Type** fare clic su **Create a new HPC cluster by creating a head node**, quindi su **Next**.

    ![Selezionare il tipo di installazione](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc2.png)

6.  Verranno eseguiti alcuni test pre-installazione. Fare clic su **Next** nella pagina **Installation Rules** se tutti i test hanno esito positivo. In caso contrario, esaminare le informazioni fornite e apportare le modifiche necessarie nell'ambiente. Ripetere quindi i test oppure riavviare l'installazione guidata, se necessario.

    ![Regole di installazione](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc3.png)

7.  Nella pagina **HPC DB Configuration** assicurarsi che sia selezionato **Head Node** per tutti i database HPC, quindi fare clic su **Next**.

    ![Configurazione di database](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc4.png)

8.  Accettare le selezioni predefinite nelle pagine restanti della procedura guidata. Nella pagina **Install Required Components** fare clic su **Install**.

    ![Installazione](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc6.png)

9.  Al termine dell'installazione, deselezionare **Start HPC Cluster Manager** e fare clic su **Finish**. In un passaggio successivo verrà avviato HPC Cluster Manager per completare la configurazione del nodo head.

    ![Fine](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc7.png)

Preparazione della sottoscrizione di Azure
------------------------------------------

Utilizzare il [portale di gestione](https://manage.windowsazure.com) per eseguire le operazioni seguenti con la sottoscrizione di Azure. Queste operazioni sono necessarie per poter successivamente distribuire i nodi di Azure dal nodo head locale.

-   Caricamento del certificato di gestione (necessario per le connessioni sicure tra il nodo head e i servizi di Azure)

-   Creazione di un servizio cloud di Azure in cui verranno eseguiti i nodi di Azure (istanze del ruolo di lavoro)

-   Creazione di un account di archiviazione di Azure

    > [WACOM.NOTE]Prendere anche nota dell'ID sottoscrizione di Azure, che sarà necessario in seguito. Questo ID è reperibile nelle [informazioni account]([https://account.windowsazure.com/Subscriptions) di Azure.

### Caricare il certificato di gestione predefinito

HPC Pack installa un certificato autofirmato nel nodo head, denominato Default Microsoft HPC Azure Management, che è possibile caricare come certificato di gestione di Azure. Questo certificato viene fornito a scopo di test e per distribuzioni di prova.

1.  Dal computer nodo head accedere al [portale di gestione](https://manage.windowsazure.com).

2.  Fare clic su **Settings**, quindi su **Management Certificates**.

3.  Sulla barra dei comandi fare clic su **Upload**.

    ![Impostazioni certificato](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/upload_cert1.png)

4.  Individuare il file C:\\Programmi\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer nel nodo head, quindi fare clic sul pulsante **Check**.

    ![Caricamento del certificato](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/install_hpc10.png)

Nell'elenco dei certificati di gestione verrà visualizzato il certificato **Default HPC Azure Management**.

### Creazione di un servizio cloud di Azure

> [WACOM.NOTE]Per prestazioni ottimali, creare il servizio cloud e l'account di archiviazione nella stessa area geografica o nello stesso gruppo di affinità, se configurato.

1.  Sulla barra dei comandi del portale di gestione fare clic su **New**.

2.  Fare clic su **Compute**, quindi su **Cloud Service** e infine su **Quick Create**.

3.  Digitare un URL per il servizio cloud, quindi fare clic su **Create Cloud Service**.

    ![Creazione di un servizio](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createservice1.png)

### Creazione di un account di archiviazione di Azure

1.  Sulla barra dei comandi del portale di gestione fare clic su **New**.

2.  Fare clic su **Data Services**, quindi su **Storage** e infine su **Quick Create**.

3.  Digitare un URL per l'account, quindi fare clic su **Create Storage Account**.

    ![Creazione dell'account di archiviazione](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/createstorage1.png)

Configurazione del nodo head
----------------------------

Prima di utilizzare HPC Cluster Manager per distribuire nodi di Azure e inviare processi, è necessario eseguire alcune operazioni di configurazione del cluster.

1.  Nel nodo head avviare HPC Cluster Manager. Se viene visualizzata la finestra di dialogo **Select Head Node** fare clic su **Local Computer**. Verrà visualizzato l'elenco **Deployment To-do List**.

2.  In **Required deployment tasks** fare clic su **Configure your network**.

    ![Configurazione della rete](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc2.png)

3.  Nella configurazione guidata rete selezionare **All nodes only on an enterprise network** (Topologia 5).

    ![Topologia 5](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc3.png)

    > [WACOM.NOTE]Questa è la configurazione più semplice a scopo dimostrativo perché il nodo head richiede un'unica scheda di rete per la connessione ad Active Directory e a Internet. In questa esercitazione non vengono trattati scenari di cluster che richiedono reti aggiuntive.

4.  Fare clic su **Next** per accettare i valori predefiniti nelle pagine restanti della procedura guidata. Quindi, nella scheda **Review** fare clic su **Configure** per completare la configurazione di rete.

5.  In **Deployment To-do List** fare clic su **Provide installation credentials**.

6.  Nella finestra di dialogo **Installation Credentials** digitare le credenziali dell'account di dominio utilizzato per installare HPC Pack. Fare quindi clic su **OK**.

    ![Credenziali di installazione](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc6.png)

    > [WACOM.NOTE]I servizi di HPC Pack utilizzano le credenziali di installazione solo per distribuire nodi di calcolo locali.

7.  In **Deployment To-do List** fare clic su **Configure the naming of new nodes**.

8.  Nella finestra di dialogo **Specify Node Naming Series** accettare la serie di denominazione predefinita e fare clic su **OK**.

    ![Denominazione dei nodi](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc8.png)

    > [WACOM.NOTE]La serie di denominazione genera nomi solo per i nodi di calcolo aggiunti al dominio. L'assegnazione dei nomi ai nodi di Azure avviene automaticamente.

9.  In **Deployment To-do List** fare clic su **Create a node template**. Verrà utilizzato il modello di nodo per aggiungere nodi di Azure al cluster.

10. Nella creazione guidata modello di nodo eseguire le operazioni seguenti:

    a. Nella pagina **Choose Node Template Type** fare clic su **Azure node template**, quindi su **Next**.

    ![Modello di nodo](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc10.png)

    b. Fare clic su **Next** per accettare il nome di modello predefinito.

    c. Nella pagina **Provide Subscription Information** immettere l'ID sottoscrizione di Azure (disponibile nelle proprie [informazioni account]([https://account.windowsazure.com/Subscriptions) di Azure). Quindi, in **Management certificate**, fare clic su **Browse** e selezionare **Default HPC Azure Management.** Fare quindi clic su **Next**.

    ![Modello di nodo](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc12.png)

    d. Nella pagina **Provide Service Information** selezionare il servizio cloud e l'account di archiviazione creato in un passaggio precedente. Fare quindi clic su **Next**.

    ![Modello di nodo](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/config_hpc13.png)

    e. Fare clic su **Next** per accettare i valori predefiniti nelle pagine restanti della procedura guidata. Quindi, nella scheda **Review** fare clic su **Create** per creare il modello di nodo.

    > [WACOM.NOTE]Per impostazione predefinita, il modello di nodo di Azure include le impostazioni per l'avvio (provisioning) e l'interruzione manuale dei nodi. È anche possibile configurare una pianificazione per l'avvio e l'interruzione automatica dei nodi di Azure.

Aggiunta di nodi di Azure al cluster
------------------------------------

A questo punto si utilizza il modello di nodo per aggiungere nodi di Azure al cluster. L'aggiunta di nodi al cluster determina l'archiviazione delle relative informazioni di configurazione per consentirne l'avvio (provisioning) in qualsiasi momento come istanze del ruolo nel servizio cloud. I costi relativi ai nodi di Azure vengono addebitati nella sottoscrizione solo dopo l'esecuzione delle istanze del ruolo nel servizio cloud.

In questa esercitazione verranno aggiunti due piccoli nodi.

1.  In HPC Cluster Manager, in **Node Management** nel riquadro **Actions**, fare clic su **Add Node**.

    ![Aggiunta di un nodo](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1.png)

2.  Nella pagina **Select Deployment Method** dell'aggiunta guidata nodi fare clic su **Add Azure nodes** e quindi su **Next**.

    ![Aggiunta di un nodo di Azure](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node1_1.png)

3.  Nella pagina **Specify New Nodes** selezionare il modello di nodo di Azure creato in precedenza (denominato per impostazione predefinita **Default AzureNode Template**). Specificare quindi **2** nodi di dimensioni **Small**, quindi fare clic su **Next**.

    ![Specifica dei nodi](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node2.png)

    Per informazioni dettagliate sulle dimensioni disponibili per le macchine virtuali, vedere [Virtual Machine and Cloud Service Sizes for Azure](http://msdn.microsoft.com/library/windowsazure/dn197896.aspx).

4.  Nella pagina **Completing the Add Node Wizard** fare clic su **Finish**.

    A questo punto, in HPC Cluster Manager saranno presenti due nodi di Azure denominati **AzureCN-0001** e **AzureCN-0002**. Entrambi sono in stato **Not-Deployed**.

    ![Nodi aggiunti](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node3.png)

Avvio dei nodi di Azure
-----------------------

Quando si desidera utilizzare le risorse cluster in Azure, utilizzare HPC Cluster Manager per avviare i nodi di Azure (eseguire il provisioning) e portarli online.

1.  In HPC Cluster Manager fare clic su uno o entrambi i nodi in **Node Management**, quindi nel riquadro **Actions** fare clic su **Start**.

    ![Avvio dei nodi](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node4.png)

2.  Nella finestra di dialogo **Start Azure Nodes** fare clic su **Start**.

    ![Avvio dei nodi](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node5.png)

    Per i nodi verrà eseguita la transizione allo stato **Provisioning**. È possibile visualizzare il log di provisioning per tenere traccia dell'avanzamento del provisioning.

    ![Provisioning dei nodi](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node6.png)

3.  Dopo alcuni minuti viene completato il provisioning dei nodi di Azure, che passeranno allo stato **Offline**. In questo stato le istanze del ruolo sono in esecuzione ma non accetteranno ancora processi cluster.

4.  Per verificare che le istanze del ruolo siano in esecuzione, nel [portale di gestione](https://manage.windowsazure.com) fare clic su **Cloud Services**, fare clic sul nome del servizio cloud, quindi su **Instances**.

    ![Esecuzione delle istanze](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances1.png)

    Saranno presenti due istanze del ruolo di lavoro in esecuzione nel servizio. HPC Pack distribuisce automaticamente anche due istanze **HpcProxy** (dimensione media) per gestire la comunicazione tra il nodo head e Azure.

5.  Per portare online i nodi di Azure per l'esecuzione dei processi cluster, selezionare i nodi, fare clic con il pulsante destro del mouse e scegliere **Bring Online**.

    ![Nodi offline](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/add_node7.png)

    HPC Cluster Manager indica che i nodi sono in stato **Online**.

Esecuzione di un comando nel cluster
------------------------------------

È possibile utilizzare il comando **clusrun** di HPC Pack per eseguire un comando o un'applicazione in uno o più nodi del cluster. Ad esempio, utilizzare **clusrun** per ottenere la configurazione IP dei nodi di Azure.

1.  Aprire il prompt dei comandi nel nodo head.

2.  Digitare il comando seguente:

    `clusrun /nodes:azurecn* ipconfig`

3.  L'output sarà simile al seguente.

    ![Clusrun](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/clusrun1.png)

Esecuzione di un processo di test
---------------------------------

È possibile inviare un processo di test che viene eseguito nel cluster ibrido. In questo esempio viene utilizzato un semplice processo "sweep parametrico" (un tipo di calcolo intrinsecamente parallelo) che esegue sottoattività che aggiungono un numero intero a se stesso utilizzando il comando **set /a**. Tutti i nodi nel cluster contribuiscono a completare le sottoattività per i numeri interi da 1 a 100.

1.  In HPC Cluster Manager, in **Job Management** nel riquadro **Actions**, fare clic su **New Parametric Sweep Job**.

    ![Nuovo processo](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/test_job1.png)

2.  Nella finestra di dialogo **New Parametric Sweep Job**, in **Command line**, digitare `set /a *+*` (sovrascrivendo la riga di comando predefinita visualizzata). Lasciare i valori predefiniti per le impostazioni rimanenti, quindi fare clic su **Submit** per inviare il processo.

    ![Sweep parametrico](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/param_sweep1.png)

3.  Al termine del processo, fare doppio clic sul processo **My Sweep Task**.

4.  Fare clic su **View Tasks**, quindi fare clic su una sottoattività per visualizzare l'output calcolato corrispondente.

    ![Risultati delle attività](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job361.png)

5.  Per visualizzare il nodo che ha eseguito il calcolo per tale sottoattività, fare clic su **Allocated Nodes** (il nome del nodo nel proprio cluster potrebbe essere diverso).

    ![Risultati delle attività](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_job362.png)

Interruzione dei nodi di Azure
------------------------------

Dopo avere provato il cluster, è possibile utilizzare HPC Cluster Manager per interrompere i nodi di Azure per evitare l'addebito di costi non necessari sul proprio account. In questo modo viene interrotto il servizio cloud e vengono rimosse le istanze del ruolo di Azure.

1.  In HPC Cluster Manager selezionare entrambi i nodi di Azure in **Node Management**. Quindi, nel riquadro **Actions** fare clic su **Stop**.

    ![Interruzione dei nodi](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node1.png)

2.  Nella finestra di dialogo **Stop Azure Nodes** fare clic su **Stop**.

    ![Interruzione dei nodi](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node2.png)

3.  Per i nodi verrà eseguita la transizione allo stato **Stopping**. Dopo alcuni minuti i nodi risulteranno **Not-Deployed** in HPC Cluster Manager.

    ![Nodi non distribuiti](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/stop_node4.png)

4.  Per verificare che le istanze del ruolo non siano più in esecuzione in Azure, nel [portale di gestione](https://manage.windowsazure.com) fare clic su **Cloud Services**, fare clic sul nome del servizio cloud e quindi su **Instances**. Nessuna istanza verrà distribuita nell'ambiente di produzione.

    ![Nessuna istanza](./media/set-up-hybrid-cluster-microsoft-hpc-pack-2012-sp1/view_instances2.png)

    L'esercitazione è terminata.

Risorse correlate
-----------------

-   [HPC Pack 2012 R2 e HPC Pack 2012](http://go.microsoft.com/fwlink/p/?LinkID=263697)
-   [Potenziamento in Azure con Microsoft HPC Pack](http://go.microsoft.com/fwlink/p/?LinkID=200493)
-   [Distribuzione di applicazioni in nodi di Azure](http://go.microsoft.com/fwlink/p/?LinkId=325317)
-   [Pacchetto Microsoft HPC in una macchina virtuale di Azure](http://go.microsoft.com/fwlink/p/?linkid=330375)

