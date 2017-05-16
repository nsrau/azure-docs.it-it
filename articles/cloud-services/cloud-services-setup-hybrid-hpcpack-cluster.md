---
title: Configurare un cluster HPC ibrido con Microsoft HPC Pack | Documentazione Microsoft
description: Informazioni su come usare Microsoft HPC Pack e Azure per configurare un piccolo cluster di elaborazione ad alte prestazioni (HPC) ibrido
services: cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: b11f3e1b-b9e1-4b0d-8455-6607b88814e9
ms.service: cloud-services
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/06/2017
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: 0fcfcc53641ebdf8a668b353db8eebb3bc64795d
ms.lasthandoff: 03/31/2017


---
# <a name="set-up-a-hybrid-high-performance-computing-hpc-cluster-with-microsoft-hpc-pack-and-on-demand-azure-compute-nodes"></a>Configurare un cluster ibrido HPC (high performance computing) con Microsoft HPC Pack e nodi di calcolo di Azure su richiesta
Usare Microsoft HPC Pack 2012 R2 e Azure per configurare un piccolo cluster HPC (high performance computing) ibrido. Il cluster sarà composto da un nodo head locale (un computer che esegue il sistema operativo Windows Server e HPC Pack) e alcuni nodi di calcolo distribuiti su richiesta come istanze del ruolo di lavoro in un servizio cloud di Azure. In seguito sarà possibile eseguire processi di calcolo nel cluster ibrido.

![Cluster HPC ibrido][Overview] 

L'approccio descritto in questa esercitazione, denominato anche "potenziamento nel cloud", consente di usare risorse di calcolo scalabili su richiesta in Azure per eseguire applicazioni a elevato utilizzo di calcolo.

Questa esercitazione non presuppone che si abbia esperienza nell'ambito dei cluster di calcolo o di HPC Pack 2012 R2. Viene fornita solo per aiutare l'utente a distribuire rapidamente un cluster di calcolo ibrido a scopo dimostrativo. Per considerazioni e procedure sulla distribuzione di un cluster HPC Pack ibrido su scala maggiore in un ambiente di produzione o per l'uso di HPC Pack 2016, vedere le [indicazioni dettagliate](http://go.microsoft.com/fwlink/p/?LinkID=200493). Per altri scenari con HPC Pack, inclusa la distribuzione automatica di cluster nelle macchine virtuali di Azure, vedere [Opzioni per creare e gestire un cluster HPC in Azure con Microsoft HPC Pack](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="prerequisites"></a>Prerequisiti
* **Sottoscrizione di Azure** : se non è disponibile una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/) in pochi minuti.
* **Un computer locale che esegue Windows Server 2012 R2 o Windows Server 2012** : questo computer sarà il nodo head del cluster HPC. Se Windows Server non è già in esecuzione, è possibile scaricare e installare una [versione di valutazione](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2).
  
  * Il computer deve essere aggiunto a un dominio Active Directory. Per uno scenario di test con una nuova installazione di Windows Server, è possibile aggiungere il ruolo del server Servizi di dominio Active Directory e alzare di livello il computer nodo head a controller di dominio in una nuova foresta di domini. Vedere la documentazione di Windows Server.
  * Per supportare HPC Pack, è necessario che il sistema operativo sia installato in una delle lingue seguenti: inglese, giapponese o cinese (semplificato).
  * Verificare che siano installati gli aggiornamenti importanti.
* **HPC Pack 2012 R2** - [scaricare](http://go.microsoft.com/fwlink/p/?linkid=328024) il pacchetto di installazione della versione gratuita più recente e copiare i file nel computer nodo head o in un percorso di rete. Scegliere i file di installazione nella stessa lingua dell'installazione di Windows Server.

    >[!NOTE]
    > Per usare HPC Pack 2016 anziché HPC Pack 2012 R2, è necessaria una configurazione aggiuntiva. Vedere le [indicazioni dettagliate](http://go.microsoft.com/fwlink/p/?LinkID=200493).
    > 
* **Account di dominio** : per installare HPC Pack, questo account deve essere configurato con autorizzazioni di amministratore locale nel nodo head.
* **Connettività TCP sulla porta 443** dal nodo head ad Azure.

## <a name="install-hpc-pack-on-the-head-node"></a>Installazione di HPC Pack nel nodo head
Installare prima Microsoft HPC Pack in un computer locale che esegue Windows Server, che sarà il nodo head del cluster.

1. Accedere al nodo head usando un account di dominio che dispone di autorizzazione di amministratore locale.
2. Avviare l'installazione guidata di HPC Pack eseguendo Setup.exe dai file di installazione di HPC Pack.
3. Nella schermata **HPC Pack 2012 R2 Setup** (Installazione di HPC Pack 2012 R2) fare clic su **New installation or add new features to an existing installation** (Nuova installazione o aggiunta di nuove funzionalità a un'installazione esistente).
   
    ![Installazione di HPC Pack 2012][install_hpc1]
4. Nella pagina **Microsoft Software User Agreement** (Contratto di licenza software di Microsoft) fare clic su **Next** (Avanti).
5. Nella pagina **Select Installation Type** (Selezione tipo di installazione) fare clic su **Create a new HPC cluster by creating a head node** (Creare un nuovo cluster HPC creando un nodo head), quindi su **Next** (Avanti).
   
    ![Selezionare il tipo di installazione][install_hpc2]
6. Verranno eseguiti alcuni test pre-installazione. Fare clic su **Next** on the **Installation Rules** se tutti i test hanno esito positivo. In caso contrario, esaminare le informazioni fornite e apportare le modifiche necessarie nell'ambiente. Ripetere quindi i test oppure riavviare l'installazione guidata, se necessario.
   
    ![Installation Rules][install_hpc3]
7. Nella pagina **HPC DB Configuration** (Configurazione DB HPC) assicurarsi che sia selezionato **Head Node** (Nodo head) per tutti i database HPC, quindi fare clic su **Next** (Avanti).
   
    ![Configurazione di database][install_hpc4]
8. Accettare le selezioni predefinite nelle pagine restanti della procedura guidata. Nella pagina **Install Required Components** (Installazione dei componenti necessari) fare clic su **Install** (Installa).
   
    ![Installare][install_hpc6]
9. Al termine dell'installazione, deselezionare **Start HPC Cluster Manager** (Avvia Gestione cluster HPC) e fare clic su **Finish** (Fine). HPC Cluster Manager verrà avviato in un passaggio successivo.
   
    ![Finish][install_hpc7]

## <a name="prepare-the-azure-subscription"></a>Preparazione della sottoscrizione di Azure
Usare il [portale di Azure classico](https://manage.windowsazure.com) per eseguire le operazioni seguenti con la sottoscrizione di Azure. Queste operazioni sono necessarie per poter successivamente distribuire i nodi di Azure dal nodo head locale. Nelle sezioni successive sono disponibili le procedure dettagliate.

* Caricamento del certificato di gestione (necessario per le connessioni sicure tra il nodo head e i servizi di Azure)
* Creazione di un servizio cloud di Azure in cui verranno eseguiti i nodi di Azure (istanze del ruolo di lavoro)
* Creare un account di archiviazione di Azure
  
  > [!NOTE]
  > Prendere anche nota dell'ID sottoscrizione di Azure, che sarà necessario in seguito. L'ID è disponibile nel portale classico facendo clic su **Impostazioni** > **Sottoscrizioni**.
  > 
  > 

### <a name="upload-the-default-management-certificate"></a>Caricare il certificato di gestione predefinito
HPC Pack installa un certificato autofirmato nel nodo head, denominato Default Microsoft HPC Azure Management, che è possibile caricare come certificato di gestione di Azure. Questo certificato viene fornito a scopo di test e per distribuzioni di prova.

1. Dal computer nodo head, accedere al [portale di Azure classico](https://manage.windowsazure.com).
2. Fare clic su **Impostazioni** > **Certificati di gestione**.
3. Sulla barra dei comandi fare clic su **Upload**.
   
    ![Impostazioni certificato][upload_cert1]
4. Individuare il file C:\Programmi\Microsoft HPC Pack 2012\Bin\hpccert.cer nel nodo head. Fare quindi clic sul pulsante con il segno di **spunta**.
   
    ![Caricamento del certificato][install_hpc10]

Nell'elenco dei certificati di gestione verrà visualizzato il certificato **Default HPC Azure Management** .

### <a name="create-an-azure-cloud-service"></a>Creazione di un servizio cloud di Azure
> [!NOTE]
> Per prestazioni ottimali, creare il servizio cloud e l'account di archiviazione, in una fase successiva, nella stessa area geografica.
> 
> 

1. Sulla barra dei comandi del portale classico fare clic su **Nuovo**.
2. Fare clic su **Calcolo** > **Servizio cloud** > **Creazione rapida**.
3. Digitare un URL per il servizio cloud, quindi fare clic su **Create Cloud Service**.
   
    ![Creazione di un servizio][createservice1]

### <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure
1. Sulla barra dei comandi del portale classico fare clic su **Nuovo**.
2. Fare clic su **Servizi dati** > **Archiviazione** > **Creazione rapida**.
3. Digitare un URL per l'account, quindi fare clic su **Create Storage Account**.
   
    ![Creazione dell'account di archiviazione][createstorage1]

## <a name="configure-the-head-node"></a>Configurazione del nodo head
Prima di usare HPC Cluster Manager per distribuire nodi di Azure e inviare processi, è necessario eseguire alcune operazioni di configurazione del cluster.

1. Nel nodo head avviare HPC Cluster Manager. Se viene visualizzata la finestra di dialogo **Select Head Node** (Selezionare nodo head) fare clic su **Local Computer** (Computer locale). Verrà visualizzato l'elenco **Deployment To-do List** .
2. In **Required deployment tasks** (Attività di distribuzione necessarie) fare clic su **Configure your network** (Configurare la rete).
   
    ![Configurazione della rete][config_hpc2]
3. Nella configurazione guidata rete selezionare **All nodes only on an enterprise network** (Topologia 5).
   
    ![Topologia 5][config_hpc3]
   
   > [!NOTE]
   > Questa è la configurazione più semplice a scopo dimostrativo perché il nodo head richiede un'unica scheda di rete per la connessione ad Active Directory e a Internet. In questa esercitazione non vengono trattati scenari di cluster che richiedono reti aggiuntive.
   > 
   > 
4. Fare clic su **Next** per accettare i valori predefiniti nelle pagine restanti della procedura guidata. Nella scheda **Review** (Revisione) fare clic su **Configure** (Configura) per completare la configurazione di rete.
5. In **Deployment To-do List**fare clic su **Provide installation credentials**.
6. Nella finestra di dialogo **Installation Credentials** digitare le credenziali dell'account di dominio usato per installare HPC Pack. Fare quindi clic su **OK**.
   
    ![Installation Credentials][config_hpc6]
   
   > [!NOTE]
   > I servizi di HPC Pack usano le credenziali di installazione solo per distribuire nodi di calcolo aggiunti al dominio. I nodi di Azure aggiunti in questa esercitazione non sono aggiunti al dominio.
   > 
   > 
7. In **Deployment To-do List**fare clic su **Configure the naming of new nodes**.
8. Nella finestra di dialogo **Specify Node Naming Series** (Specificare serie di denominazione nodo) accettare la serie di denominazione predefinita e fare clic su **OK**.
   
    ![Denominazione dei nodi][config_hpc8]
   
   > [!NOTE]
   > La serie di denominazione genera nomi solo per i nodi di calcolo aggiunti al dominio. L'assegnazione dei nomi ai nodi di lavoro di Azure avviene automaticamente.
   > 
   > 
9. In **Deployment To-do List**fare clic su **Create a node template**. Verrà usato il modello di nodo per aggiungere nodi di Azure al cluster.
10. Nella creazione guidata modello di nodo eseguire le operazioni seguenti:
    
    a. Nella pagina **Choose Node Template Type** (Scegliere il tipo di modello nodo) fare clic su **Azure node template** (Modello di nodo Azure), quindi su **Next**.
    
    ![Modello di nodo][config_hpc10]
    
    b. Fare clic su **Next** per accettare il nome di modello predefinito.
    
    c. Nella pagina **Provide Subscription Information** (Fornire informazioni sulla sottoscrizione) immettere l'ID sottoscrizione di Azure, disponibile nelle informazioni sull'account Azure. In **Management certificate** (Certificato di gestione), fare clic su **Browse** (Sfoglia) e selezionare **Default HPC Azure Management** Quindi fare clic su **Next**.
    
    ![Modello di nodo][config_hpc12]
    
    d. Nella pagina **Provide Service Information** selezionare il servizio cloud e l'account di archiviazione creato in un passaggio precedente. Quindi fare clic su **Next**.
    
    ![Modello di nodo][config_hpc13]
    
    e. Fare clic su **Next** per accettare i valori predefiniti nelle pagine restanti della procedura guidata. Nella scheda **Review** (Revisione) fare quindi clic su **Create** (Crea) per creare il modello di nodo.
    
    > [!NOTE]
    > Per impostazione predefinita, il modello di nodo di Azure include le impostazioni per l'avvio (provisioning) e l'interruzione manuale dei nodi con HPC Cluster Manager. Facoltativamente, è possibile configurare una pianificazione per l'avvio e l'arresto automatici dei nodi di Azure.
    > 
    > 

## <a name="add-azure-nodes-to-the-cluster"></a>Aggiunta di nodi di Azure al cluster
A questo punto si usa il modello di nodo per aggiungere nodi di Azure al cluster. L'aggiunta di nodi al cluster determina l'archiviazione delle relative informazioni di configurazione per consentirne l'avvio (provisioning) in qualsiasi momento come istanze del ruolo nel servizio cloud. I costi relativi ai nodi di Azure vengono addebitati nella sottoscrizione solo dopo l'esecuzione delle istanze del ruolo nel servizio cloud.

In questa esercitazione verranno aggiunti due piccoli nodi.

1. In Gestione cluster HPC da **Node Management** (Gestione nodi), denominato **Resource Management** (Gestione risorse) in versioni recenti di HPC Pack, fare clic su **Add Node** (Aggiungi nodo) nel riquadro **Actions** (Azioni).
   
    ![Actions][add_node1]
2. Nella pagina **Select Deployment Method** (Seleziona metodo di distribuzione) dell'aggiunta guidata nodi fare clic su **Add Azure nodes** (Aggiungi nodi di Azure) e quindi su **Next** (Avanti).
   
    ![Aggiunta di un nodo di Azure][add_node1_1]
3. Nella pagina **Specify New Nodes** (Specificare nuovi nodi) selezionare il modello di nodo di Azure creato in precedenza (denominato per impostazione predefinita **Default AzureNode Template**). Specificare quindi **2** nodi di dimensioni **Small** e quindi fare clic su **Next** (Avanti).
   
    ![Specifica dei nodi][add_node2]
   
    Per informazioni dettagliate sulle dimensioni disponibili, vedere [Dimensioni dei servizi cloud](cloud-services-sizes-specs.md).
4. Nella pagina **Completing the Add Node Wizard** (Completamento aggiunta guidata nodo in corso) fare clic su **Finish** (Fine).
   
     In Gestione cluster HPC saranno ora presenti due nodi di Azure denominati **AzureCN-0001** e **AzureCN-0002**. Entrambi hanno lo stato **Not-Deployed** .
   
    ![Nodi aggiunti][add_node3]

## <a name="start-the-azure-nodes"></a>Avvio dei nodi di Azure
Quando si vogliono usare le risorse cluster in Azure, usare HPC Cluster Manager per avviare i nodi di Azure (eseguire il provisioning) e portarli online.

1. In Gestione cluster HPC fare clic su uno o entrambi i nodi in **Node Management** (Gestione nodi), denominato **Resource Management** (Gestione risorse) in versioni recenti di HPC Pack, quindi nel riquadro **Actions** (Azioni) fare clic su **Start** (Avvia).
   
   ![Avvio dei nodi][add_node4]
2. Nella finestra di dialogo **Stop Windows Azure nodes** (Arresta nodi di Microsoft Azure) fare clic su **Start** (Avvia).
   
    ![Avvio dei nodi][add_node5]
   
    Per i nodi verrà eseguita la transizione allo stato **Provisioning** . Visualizzare il log di provisioning per tenere traccia dell'avanzamento del provisioning.
   
    ![Provisioning dei nodi][add_node6]
3. Dopo alcuni minuti viene completato il provisioning dei nodi di Azure, che passeranno allo stato **Offline** . In questo stato le istanze del ruolo sono in esecuzione ma non accetteranno ancora processi cluster.
4. Per verificare che le istanze del ruolo siano in esecuzione, nel [portale classico](https://manage.windowsazure.com) fare clic su **Servizi cloud** > *nome_del_servizio_cloud* > **Istanze**.
   
    ![Esecuzione delle istanze][view_instances1]
   
    Saranno presenti due istanze del ruolo di lavoro in esecuzione nel servizio. HPC Pack distribuisce automaticamente anche due istanze **HpcProxy** (dimensione media) per gestire la comunicazione tra il nodo head e Azure.
5. Per portare online i nodi di Azure per l'esecuzione dei processi cluster, selezionare i nodi, fare clic con il pulsante destro del mouse e scegliere **Bring Online**.
   
    ![Nodi offline][add_node7]
   
    HPC Cluster Manager indica che i nodi sono in stato **Online** .

## <a name="run-a-command-across-the-cluster"></a>Esecuzione di un comando nel cluster
Per verificare l'installazione, usare il comando **clusrun** di HPC Pack per eseguire un comando o un'applicazione in uno o più nodi del cluster. Ad esempio, usare **clusrun** per ottenere la configurazione IP dei nodi di Azure.

1. Aprire il prompt dei comandi nel nodo head.
2. Digitare il comando seguente:
   
    `clusrun /nodes:azurecn* ipconfig`
3. L'output sarà simile al seguente.
   
    ![clusrun][clusrun1]

## <a name="run-a-test-job"></a>Esecuzione di un processo di test
Ora inviare un processo di test che viene eseguito nel cluster ibrido. Questo esempio è un processo parametrico di organizzazione molto semplice, ovvero un tipo di calcolo intrinsecamente parallelo. In questo esempio vengono eseguite sottoattività che aggiungono un numero intero a se stesso tramite il comando **set /a** . Tutti i nodi nel cluster contribuiscono a completare le sottoattività per i numeri interi da 1 a 100.

1. In Gestione cluster HPC, in **Job Management** (Gestione processi), nel riquadro **Actions** (Azioni) fare clic su **New Parametric Sweep Job** (Nuovo processo parametrico di organizzazione).
   
    ![Nuovo processo][test_job1]
2. Nella finestra di dialogo **New Parametric Sweep Job** (Nuovo processo parametrico di organizzazione), in **Command line** (Riga di comando), digitare `set /a *+*` sovrascrivendo la riga di comando predefinita visualizzata. Lasciare i valori predefiniti per le impostazioni rimanenti, quindi fare clic su **Submit** per inviare il processo.
   
    ![Sweep parametrico][param_sweep1]
3. Al termine del processo, fare doppio clic sul processo **My Sweep Task** .
4. Fare clic su **View Tasks**, quindi fare clic su una sottoattività per visualizzare l'output calcolato corrispondente.
   
    ![Risultati delle attività][view_job361]
5. Per visualizzare il nodo che ha eseguito il calcolo per tale sottoattività, fare clic su **Allocated Nodes** (Nodi allocati) (il nome del nodo nel proprio cluster potrebbe essere diverso).
   
    ![Risultati delle attività][view_job362]

## <a name="stop-the-azure-nodes"></a>Interruzione dei nodi di Azure
Dopo aver provato il cluster, arrestare i nodi di Azure per evitare l'addebito di costi non necessari sul proprio account. In questo modo viene interrotto il servizio cloud e vengono rimosse le istanze del ruolo di Azure.

1. In Gestione cluster HPC selezionare entrambi i nodi di Azure in **Node Management** (Gestione Nodi), denominato **Resource Management** (Gestione risorse) in versioni recenti di HPC Pack. Nel riquadro **Actions** (Azioni) fare clic su **Stop** (Arresta).
   
    ![Interruzione dei nodi][stop_node1]
2. Nella finestra di dialogo **Stop Windows Azure nodes** (Arresta nodi di Microsoft Azure) fare clic su **Stop** (Arresta).
   
    ![Interruzione dei nodi][stop_node2]
3. Per i nodi verrà eseguita la transizione allo stato **Stopping** . Dopo alcuni minuti i nodi risulteranno **Not-Deployed**in HPC Cluster Manager.
   
    ![Nodi non distribuiti][stop_node4]
4. Per verificare che le istanze del ruolo non siano più in esecuzione in Azure, nel [portale classico](https://manage.windowsazure.com) fare clic su **Servizi cloud** > *nome_del_servizio_cloud* > **Istanze**. Nessuna istanza verrà distribuita nell'ambiente di produzione.
   
    ![Nessuna istanza][view_instances2]
   
    L'esercitazione è terminata.

## <a name="next-steps"></a>Passaggi successivi
* Vedere la documentazione relativa a [HPC Pack](https://technet.microsoft.com/library/cc514029).
* Per configurare una distribuzione di cluster ibrido HPC Pack su una scala più ampia, vedere [Burst to Azure with Microsoft HPC Pack](http://go.microsoft.com/fwlink/p/?LinkID=200493)(Potenziamento della capacità di Azure con Microsoft HPC Pack).
* Per altri modi per creare un cluster HPC Pack in Azure, inclusi i modelli di Azure Resource Manager, vedere [Opzioni per cluster HPC con Microsoft HPC Pack in Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Vedere [Big Compute in Azure: risorse tecniche per Batch e HPC (High Performance Computing)](../batch/big-compute-resources.md) per altre informazioni sulla gamma di soluzioni cloud HPC e Big Compute in Azure.

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

