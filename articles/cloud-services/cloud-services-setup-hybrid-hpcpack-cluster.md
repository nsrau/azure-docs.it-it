<properties
	pageTitle="Configurazione di un cluster HPC ibrido con Microsoft HPC Pack | Microsoft Azure"
	description="Informazioni su come usare Microsoft HPC Pack e Azure per configurare un piccolo cluster di elaborazione ad alte prestazioni (HPC) ibrido"
	services="cloud-services"
	documentationCenter=""
	authors="dlepow"
	manager="timlt"
	editor=""
	tags="azure-service-management,hpc-pack"/>

<tags
	ms.service="cloud-services"
	ms.workload="big-compute"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/13/2016"
	ms.author="danlep"/>


# Configurazione di un cluster di elaborazione ibrido con Microsoft HPC Pack e istanze di Azure su richiesta
In questa esercitazione viene illustrato come usare Microsoft HPC Pack 2012 R2 e Azure per configurare un piccolo cluster di elaborazione ad alte prestazioni (HPC) ibrido. Il cluster sarà composto da un nodo head locale (un computer che esegue il sistema operativo Windows Server e HPC Pack) e alcuni nodi di calcolo distribuiti su richiesta come istanze del ruolo di lavoro in un servizio cloud di Azure. In seguito sarà possibile eseguire processi di calcolo nel cluster ibrido.

![Cluster HPC ibrido][Overview]

L'approccio descritto in questa esercitazione, denominato anche "potenziamento nel cloud", consente di usare risorse di calcolo scalabili su richiesta in Azure per eseguire applicazioni a elevato utilizzo di calcolo.

Questa esercitazione non presuppone che si abbia esperienza nell'ambito dei cluster di calcolo o di HPC Pack. Viene fornita solo per aiutare l'utente a distribuire rapidamente un cluster di calcolo ibrido a scopo dimostrativo. Per considerazioni e procedure per la distribuzione di un cluster HPC Pack ibrido su scala maggiore in un ambiente di produzione, vedere le [indicazioni dettagliate](http://go.microsoft.com/fwlink/p/?LinkID=200493). Per altri scenari con HPC Pack, inclusa la distribuzione automatica di cluster in macchine virtuali di Azure, vedere [Opzioni per creare e gestire un cluster HPC in Azure con Microsoft HPC Pack](../virtual-machines/virtual-machines-linux-hpcpack-cluster-options.md).


>[AZURE.NOTE] Azure offre una [gamma di dimensioni](../virtual-machines/virtual-machines-windows-sizes.md) per le risorse di calcolo, adatta a diversi carichi di lavoro. Le istanze A8 e A9, ad esempio, combinano prestazioni elevate e accesso a una rete di applicazioni a bassa latenza e velocità effettiva elevata necessaria per determinate applicazioni HPC. Vedere [Informazioni sulle istanze A8, A9, A10 e A11 a elevato utilizzo di calcolo](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md).


## Prerequisiti

* **Sottoscrizione Azure**: se non si dispone di un account, è possibile creare un account di prova gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Un computer locale che esegue Windows Server 2012 R2 o Windows Server 2012**. Questo computer sarà il nodo head del cluster HPC. Se Windows Server non è già in esecuzione, è possibile scaricare e installare una [versione di valutazione](http://technet.microsoft.com/evalcenter/dn205286.aspx).

	* Il computer deve essere aggiunto a un dominio Active Directory.

	* Verificare che non siano installati ulteriori ruoli server o servizi di ruolo.

	* Per supportare HPC Pack, è necessario che il sistema operativo sia installato in una delle lingue seguenti: inglese, giapponese o cinese (semplificato).

	* Verificare che siano installati gli aggiornamenti importanti.

* **HPC Pack 2012 R2**: [scaricare](http://go.microsoft.com/fwlink/p/?linkid=328024) il pacchetto di installazione della versione più recente gratuita e copiare i file nel computer nodo head o in un percorso di rete. Scegliere i file di installazione nella stessa lingua dell'installazione di Windows Server.

* **Account di dominio**: questo account deve essere configurato con autorizzazioni di amministratore locale nel nodo head per installare HPC Pack.

* **Connettività TCP sulla porta 443** dal nodo head ad Azure.

## Installazione di HPC Pack nel nodo head

Installare innanzitutto Microsoft HPC Pack in un computer locale che esegue Windows Server, che sarà il nodo head del cluster.

1. Accedere al nodo head usando un account di dominio che dispone di autorizzazione di amministratore locale.

2. Avviare l'installazione guidata di HPC Pack eseguendo Setup.exe dai file di installazione di HPC Pack.

3. Nella schermata **HPC Pack 2012 R2 Setup** fare clic su **New installation or add new features to an existing installation**.

	![Installazione di HPC Pack 2012][install_hpc1]

4. Nella pagina **Microsoft Software User Agreement** fare clic su **Next**.

5. Nella pagina **Select Installation Type** fare clic su **Create a new HPC cluster by creating a head node**, quindi su **Next**.

	![Selezionare il tipo di installazione][install_hpc2]

6. Verranno eseguiti alcuni test pre-installazione. Fare clic su **Next** nella pagina **Installation Rules** se tutti i test hanno esito positivo. In caso contrario, esaminare le informazioni fornite e apportare le modifiche necessarie nell'ambiente. Ripetere quindi i test oppure riavviare l'installazione guidata, se necessario.

	![Regole di installazione][install_hpc3]

7. Nella pagina **HPC DB Configuration** assicurarsi che sia selezionato **Head Node** per tutti i database HPC, quindi fare clic su **Next**.

	![Configurazione di database][install_hpc4]

8. Accettare le selezioni predefinite nelle pagine restanti della procedura guidata. Nella pagina **Install Required Components** fare clic su **Install**.

	![Installa][install_hpc6]

9. Al termine dell'installazione, deselezionare **Start HPC Cluster Manager** e fare clic su **Finish**. HPC Cluster Manager verrà avviato in un passaggio successivo.

	![Fine][install_hpc7]

## Preparazione della sottoscrizione di Azure
Usare il [portale di Azure classico](https://manage.windowsazure.com) per eseguire le operazioni seguenti con la sottoscrizione di Azure. Queste operazioni sono necessarie per poter successivamente distribuire i nodi di Azure dal nodo head locale.

- Caricamento del certificato di gestione (necessario per le connessioni sicure tra il nodo head e i servizi di Azure)

- Creazione di un servizio cloud di Azure in cui verranno eseguiti i nodi di Azure (istanze del ruolo di lavoro)

- Creare un account di archiviazione di Azure

	>[AZURE.NOTE]Prendere anche nota dell'ID sottoscrizione di Azure, che sarà necessario in seguito. Questo ID è reperibile nelle [informazioni account](https://account.windowsazure.com/Subscriptions)</a> di Azure.

### Caricare il certificato di gestione predefinito
HPC Pack installa un certificato autofirmato nel nodo head, denominato Default Microsoft HPC Azure Management, che è possibile caricare come certificato di gestione di Azure. Questo certificato viene fornito a scopo di test e per distribuzioni di prova.

1. Dal computer nodo head, accedere al [portale di Azure classico](https://manage.windowsazure.com).

2. Fare clic su **Settings**, quindi su **Management Certificates**.

3. Sulla barra dei comandi fare clic su **Upload**.

	![Impostazioni certificato][upload_cert1]

4. Individuare il file C:\\Programmi\\Microsoft HPC Pack 2012\\Bin\\hpccert.cer nel nodo head. Fare quindi clic sul pulsante con il segno di spunta.

	![Caricamento del certificato][install_hpc10]

Nell'elenco dei certificati di gestione verrà visualizzato il certificato **Default HPC Azure Management**.

### Creazione di un servizio cloud di Azure

>[AZURE.NOTE]Per prestazioni ottimali, creare il servizio cloud e l'account di archiviazione nella stessa area geografica.

1. Sulla barra dei comandi del portale classico fare clic su **Nuovo**.

2. Fare clic su **Compute**, quindi su **Cloud Service** e infine su **Quick Create**.

3. Digitare un URL per il servizio cloud, quindi fare clic su **Create Cloud Service**.

	![Creazione di un servizio][createservice1]

### Creare un account di archiviazione di Azure

1. Sulla barra dei comandi del portale classico fare clic su **Nuovo**.

2. Fare clic su **Data Services**, quindi su **Storage** e infine su **Quick Create**.

3. Digitare un URL per l'account, quindi fare clic su **Create Storage Account**.

	![Creazione dell'account di archiviazione][createstorage1]

## Configurazione del nodo head

Prima di usare HPC Cluster Manager per distribuire nodi di Azure e inviare processi, è necessario eseguire alcune operazioni di configurazione del cluster.

1. Nel nodo head avviare HPC Cluster Manager. Se viene visualizzata la finestra di dialogo **Select Head Node** fare clic su **Local Computer**. Verrà visualizzato l'elenco **Deployment To-do List**.

2. In **Required deployment tasks** fare clic su **Configure your network**.

	![Configurazione della rete][config_hpc2]

3. Nella configurazione guidata rete selezionare **All nodes only on an enterprise network** (Topologia 5).

	![Topologia 5][config_hpc3]

	>[AZURE.NOTE]Questa è la configurazione più semplice a scopo dimostrativo perché il nodo head richiede un'unica scheda di rete per la connessione ad Active Directory e a Internet. In questa esercitazione non vengono trattati scenari di cluster che richiedono reti aggiuntive.

4. Fare clic su **Next** per accettare i valori predefiniti nelle pagine restanti della procedura guidata. Quindi, nella scheda **Review** fare clic su **Configure** per completare la configurazione di rete.

5. In **Deployment To-do List** fare clic su **Provide installation credentials**.

6. Nella finestra di dialogo **Installation Credentials** digitare le credenziali dell'account di dominio usato per installare HPC Pack. Fare quindi clic su **OK**.

	![Credenziali di installazione][config_hpc6]

	>[AZURE.NOTE]I servizi di HPC Pack usano le credenziali di installazione solo per distribuire nodi di calcolo aggiunti al dominio. I nodi di Azure aggiunti in questa esercitazione non sono aggiunti al dominio.

7. In **Deployment To-do List** fare clic su **Configure the naming of new nodes**.

8. Nella finestra di dialogo **Specify Node Naming Series** accettare la serie di denominazione predefinita e fare clic su **OK**.

	![Denominazione dei nodi][config_hpc8]

	>[AZURE.NOTE]La serie di denominazione genera nomi solo per i nodi di calcolo aggiunti al dominio. L'assegnazione dei nomi ai nodi di lavoro di Azure avviene automaticamente.

9. In **Deployment To-do List** fare clic su **Create a node template**. Verrà usato il modello di nodo per aggiungere nodi di Azure al cluster.

10. Nella creazione guidata modello di nodo eseguire le operazioni seguenti:

	a. Nella pagina **Choose Node Template Type** fare clic su **Azure node template**, quindi su **Next**.

	![Modello di nodo][config_hpc10]

	b. Fare clic su **Next** per accettare il nome di modello predefinito.

	c. Nella pagina **Provide Subscription Information** immettere l'ID sottoscrizione di Azure, disponibile nelle proprie informazioni account di Azure. Quindi, in **Management certificate**, fare clic su **Browse** e selezionare **Default HPC Azure Management.** Quindi fare clic su **Avanti**.

	![Modello di nodo][config_hpc12]

	d. Nella pagina **Provide Service Information** selezionare il servizio cloud e l'account di archiviazione creato in un passaggio precedente. Quindi fare clic su **Next**.

	![Modello di nodo][config_hpc13]

	e. Fare clic su **Next** per accettare i valori predefiniti nelle pagine restanti della procedura guidata. Quindi, nella scheda **Review** fare clic su **Create** per creare il modello di nodo.

	>[AZURE.NOTE]Per impostazione predefinita, il modello di nodo di Azure include le impostazioni per l'avvio (provisioning) e l'interruzione manuale dei nodi. Facoltativamente, è possibile configurare una pianificazione per l'avvio e l'arresto automatici dei nodi di Azure.

## Aggiunta di nodi di Azure al cluster

A questo punto si usa il modello di nodo per aggiungere nodi di Azure al cluster. L'aggiunta di nodi al cluster determina l'archiviazione delle relative informazioni di configurazione per consentirne l'avvio (provisioning) in qualsiasi momento come istanze del ruolo nel servizio cloud. I costi relativi ai nodi di Azure vengono addebitati nella sottoscrizione solo dopo l'esecuzione delle istanze del ruolo nel servizio cloud.

In questa esercitazione verranno aggiunti due piccoli nodi.

1. In HPC Cluster Manager, in **Node Management** nel riquadro **Actions**, fare clic su **Add Node**.

	![Aggiunta di un nodo][add_node1]

2. Nella pagina **Select Deployment Method** dell'aggiunta guidata nodi fare clic su **Add Azure nodes** e quindi su **Next**.

	![Aggiunta di un nodo di Azure][add_node1_1]

3. Nella pagina **Specify New Nodes** selezionare il modello di nodo di Azure creato in precedenza (denominato per impostazione predefinita **Default AzureNode Template**). Specificare quindi **2** nodi di dimensioni **Small**, quindi fare clic su **Next**.

	![Specifica dei nodi][add_node2]

	Per informazioni dettagliate sulle dimensioni disponibili, vedere [Dimensioni dei servizi Cloud](../cloud-services/cloud-services-sizes-specs.md).

4. Nella pagina **Completing the Add Node Wizard** fare clic su **Finish**.

	 A questo punto, in HPC Cluster Manager saranno presenti due nodi di Azure denominati **AzureCN-0001** e **AzureCN-0002**. Entrambi sono in stato **Not-Deployed**.

	![Nodi aggiunti][add_node3]

## Avvio dei nodi di Azure
Quando si vogliono usare le risorse cluster in Azure, usare HPC Cluster Manager per avviare i nodi di Azure (eseguire il provisioning) e portarli online.

1.	In HPC Cluster Manager fare clic su uno o entrambi i nodi in **Node Management**, **Resource Management** in alcune versioni di HPC Pack, quindi nel riquadro **Actions** fare clic su **Start**.

	![Avvio dei nodi][add_node4]

2. Nella finestra di dialogo **Start Azure Nodes** fare clic su **Start**.

	![Avvio dei nodi][add_node5]

	Per i nodi verrà eseguita la transizione allo stato **Provisioning**. Visualizzare il log di provisioning per tenere traccia dell'avanzamento del provisioning.

	![Provisioning dei nodi][add_node6]

3. Dopo alcuni minuti viene completato il provisioning dei nodi di Azure, che passeranno allo stato **Offline**. In questo stato le istanze del ruolo sono in esecuzione ma non accetteranno ancora processi cluster.

4. Per verificare che le istanze del ruolo siano in esecuzione, nel [portale classico](https://manage.windowsazure.com) fare clic su **Servizi cloud**, fare clic sul nome del servizio cloud, quindi su **Istanze**.

	![Esecuzione delle istanze][view_instances1]

	Saranno presenti due istanze del ruolo di lavoro in esecuzione nel servizio. HPC Pack distribuisce automaticamente anche due istanze **HpcProxy** (dimensione media) per gestire la comunicazione tra il nodo head e Azure.

5. Per portare online i nodi di Azure per l'esecuzione dei processi cluster, selezionare i nodi, fare clic con il pulsante destro del mouse e scegliere **Bring Online**.

	![Nodi offline][add_node7]

	HPC Cluster Manager indica che i nodi sono in stato **Online**.

## Esecuzione di un comando nel cluster

Per verificare l'installazione, usare il comando **clusrun** di HPC Pack per eseguire un comando o un'applicazione in uno o più nodi del cluster. Ad esempio, usare **clusrun** per ottenere la configurazione IP dei nodi di Azure.

1. Aprire il prompt dei comandi nel nodo head.

2. Digitare il comando seguente:

	`clusrun /nodes:azurecn* ipconfig`

3. L'output sarà simile al seguente.

	![Clusrun][clusrun1]

## Esecuzione di un processo di test

Ora inviare un processo di test che viene eseguito nel cluster ibrido. In questo esempio si usa un semplice processo "sweep parametrico" (un tipo di calcolo intrinsecamente parallelo) che esegue sottoattività che aggiungono un numero intero a se stesso usando il comando **set /a**. Tutti i nodi nel cluster contribuiscono a completare le sottoattività per i numeri interi da 1 a 100.

1. In HPC Cluster Manager, in **Job Management** nel riquadro **Actions**, fare clic su **New Parametric Sweep Job**.

	![Nuovo processo][test_job1]

2. Nella finestra di dialogo **New Parametric Sweep Job**, in **Command line**, digitare `set /a *+*` (sovrascrivendo la riga di comando predefinita visualizzata). Lasciare i valori predefiniti per le impostazioni rimanenti, quindi fare clic su **Submit** per inviare il processo.

	![Sweep parametrico][param_sweep1]

3. Al termine del processo, fare doppio clic sul processo **My Sweep Task**.

4. Fare clic su **View Tasks**, quindi fare clic su una sottoattività per visualizzare l'output calcolato corrispondente.

	![Risultati delle attività][view_job361]

5. Per visualizzare il nodo che ha eseguito il calcolo per tale sottoattività, fare clic su **Allocated Nodes** (il nome del nodo nel proprio cluster potrebbe essere diverso).

	![Risultati delle attività][view_job362]

## Interruzione dei nodi di Azure

Dopo aver provato il cluster, arrestare i nodi di Azure per evitare l'addebito di costi non necessari sul proprio account. In questo modo viene interrotto il servizio cloud e vengono rimosse le istanze del ruolo di Azure.

1. In HPC Cluster Manager selezionare entrambi i nodi di Azure in **Node Management**. Quindi, nel riquadro **Actions** fare clic su **Stop**.

	![Interruzione dei nodi][stop_node1]

2. Nella finestra di dialogo **Stop Azure Nodes** fare clic su **Stop**.

	![Interruzione dei nodi][stop_node2]

3. Per i nodi verrà eseguita la transizione allo stato **Stopping**. Dopo alcuni minuti i nodi risulteranno **Not-Deployed** in HPC Cluster Manager.

	![Nodi non distribuiti][stop_node4]

4. Per verificare che le istanze del ruolo non siano più in esecuzione in Azure, nel [portale](https://manage.windowsazure.com) fare clic su **Servizi cloud**, fare clic sul nome del servizio cloud e quindi su **Istanze**. Nessuna istanza verrà distribuita nell'ambiente di produzione.

	![Nessuna istanza][view_instances2]

	L'esercitazione è terminata.

## Risorse correlate

* [HPC Pack 2012 R2 e HPC Pack 2012](http://go.microsoft.com/fwlink/p/?LinkID=263697

* [Potenziare le istanze del ruolo di lavoro di Azure con Microsoft HPC Pack](http://go.microsoft.com/fwlink/p/?LinkID=200493)
* [Opzioni per cluster HPC con Microsoft HPC Pack in Azure](../virtual-machines/virtual-machines-linux-hpcpack-cluster-options.md)
* [Big Compute in Azure: Risorse tecniche per Batch e HPC (High Performance Computing)](../batch/big-compute-resources.md)


[Overview]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/hybrid_cluster_overview.png
[install_hpc1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc1.png
[install_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc2.png
[install_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc3.png
[install_hpc4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc4.png
[install_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc6.png
[install_hpc7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc7.png
[install_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc10.png
[upload_cert1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/upload_cert1.png
[createstorage1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/createstorage1.png
[createservice1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/createservice1.png
[config_hpc2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc2.png
[config_hpc3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc3.png
[config_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc6.png
[config_hpc8]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc8.png
[config_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc10.png
[config_hpc12]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc12.png
[config_hpc13]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/config_hpc13.png
[add_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1.png
[add_node1_1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node1_1.png
[add_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node2.png
[add_node3]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node3.png
[add_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node4.png
[add_node5]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node5.png
[add_node6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node6.png
[add_node7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node7.png
[view_instances1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances1.png
[clusrun1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/clusrun1.png
[test_job1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/test_job1.png
[param_sweep1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/param_sweep1.png
[view_job361]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job361.png
[view_job362]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job362.png
[stop_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node1.png
[stop_node2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node2.png
[stop_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node4.png
[view_instances2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances2.png

<!---HONumber=AcomDC_0323_2016-->