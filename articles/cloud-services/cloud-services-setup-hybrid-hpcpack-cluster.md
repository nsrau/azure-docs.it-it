---
title: Configurare un cluster HPC Pack ibrido in Azure | Microsoft Docs
description: Informazioni su come usare Microsoft HPC Pack e Azure per configurare un piccolo cluster di elaborazione ad alte prestazioni (HPC) ibrido
services: cloud-services
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: hpc-pack
ms.assetid: 
ms.service: cloud-services
ms.workload: big-compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/22/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: f6dc9657e64160be1e68a7356863b53131e9b3c3
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="set-up-a-hybrid-high-performance-computing-hpc-cluster-with-microsoft-hpc-pack-and-on-demand-azure-compute-nodes"></a>Configurare un cluster ibrido HPC (high performance computing) con Microsoft HPC Pack e nodi di calcolo di Azure su richiesta
Usare Microsoft HPC Pack 2012 R2 e Azure per configurare un piccolo cluster HPC (high performance computing) ibrido. Il cluster illustrato in questo articolo è costituito da un nodo head HPC Pack locale e da alcuni nodi di calcolo distribuiti su richiesta in un servizio cloud di Azure. In seguito sarà possibile eseguire processi di calcolo nel cluster ibrido.

![Cluster HPC ibrido][Overview] 

L'approccio descritto in questa esercitazione, denominato anche "potenziamento nel cloud", consente di usare risorse di Azure scalabili su richiesta per eseguire applicazioni a elevato utilizzo di calcolo.

Questa esercitazione non presuppone che si abbia esperienza nell'ambito dei cluster di calcolo o di HPC Pack. Viene fornita solo per aiutare l'utente a distribuire rapidamente un cluster di calcolo ibrido a scopo dimostrativo. Per considerazioni e procedure sulla distribuzione di un cluster HPC Pack ibrido su scala maggiore in un ambiente di produzione o per l'uso di HPC Pack 2016, vedere le [indicazioni dettagliate](http://go.microsoft.com/fwlink/p/?LinkID=200493). Per altri scenari con HPC Pack, inclusa la distribuzione automatica di cluster nelle macchine virtuali di Azure, vedere [Opzioni per creare e gestire un cluster HPC in Azure con Microsoft HPC Pack](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="prerequisites"></a>Prerequisiti
* **Sottoscrizione di Azure** : se non è disponibile una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/) in pochi minuti.
* **Un computer locale che esegue Windows Server 2012 R2 o Windows Server 2012**: usare questo computer come nodo head del cluster HPC. Se Windows Server non è già in esecuzione, è possibile scaricare e installare una [versione di valutazione](https://www.microsoft.com/evalcenter/evaluate-windows-server-2012-r2).
  
  * Il computer deve essere aggiunto a un dominio Active Directory. A scopo di test, è possibile configurare il computer del nodo head come controller di dominio. Per aggiungere il ruolo del server Active Directory Domain Services e alzare di livello il computer del nodo head a controller di dominio, vedere la documentazione di Windows Server.
  * Per supportare HPC Pack, è necessario che il sistema operativo sia installato in una delle lingue seguenti: inglese, giapponese o cinese (semplificato).
  * Verificare che siano installati gli aggiornamenti importanti.
* **HPC Pack 2012 R2** - [scaricare](http://go.microsoft.com/fwlink/p/?linkid=328024) il pacchetto di installazione della versione gratuita più recente e copiare i file nel computer del nodo head. Scegliere i file di installazione nella stessa lingua dell'installazione di Windows Server.

    >[!NOTE]
    > Per usare HPC Pack 2016 anziché HPC Pack 2012 R2, è necessaria una configurazione aggiuntiva. Vedere le [indicazioni dettagliate](http://go.microsoft.com/fwlink/p/?LinkID=200493).
    > 
* **Account di dominio** : per installare HPC Pack, questo account deve essere configurato con autorizzazioni di amministratore locale nel nodo head.
* **Connettività TCP sulla porta 443** dal nodo head ad Azure.

## <a name="install-hpc-pack-on-the-head-node"></a>Installazione di HPC Pack nel nodo head
Installare prima Microsoft HPC Pack in un computer locale che esegue Windows Server. Questo computer diventa il nodo head del cluster.

1. Accedere al nodo head usando un account di dominio che dispone di autorizzazione di amministratore locale.

2. Avviare l'installazione guidata di HPC Pack eseguendo Setup.exe dai file di installazione di HPC Pack.

3. Nella schermata **HPC Pack 2012 R2 Setup** (Installazione di HPC Pack 2012 R2) fare clic su **New installation or add new features to an existing installation** (Nuova installazione o aggiunta di nuove funzionalità a un'installazione esistente).

    ![Installazione di HPC Pack 2012][install_hpc1]

4. Nella pagina **Microsoft Software User Agreement** (Contratto di licenza software di Microsoft) fare clic su **Next** (Avanti).

5. Nella pagina **Select Installation Type** (Selezione tipo di installazione) fare clic su **Create a new HPC cluster by creating a head node** (Creare un nuovo cluster HPC creando un nodo head), quindi su **Next** (Avanti).

6. Verranno eseguiti alcuni test pre-installazione. Fare clic su **Next** on the **Installation Rules** se tutti i test hanno esito positivo. In caso contrario, esaminare le informazioni fornite e apportare le modifiche necessarie nell'ambiente. Ripetere quindi i test oppure riavviare l'installazione guidata, se necessario.
7. Nella pagina **HPC DB Configuration** (Configurazione DB HPC) assicurarsi che sia selezionato **Head Node** (Nodo head) per tutti i database HPC, quindi fare clic su **Next** (Avanti). 

    ![Configurazione di database][install_hpc4]

8. Accettare le selezioni predefinite nelle pagine restanti della procedura guidata. Nella pagina **Install Required Components** (Installazione dei componenti necessari) fare clic su **Install** (Installa).
   
    ![Installare][install_hpc6]

9. Al termine dell'installazione, deselezionare **Start HPC Cluster Manager** (Avvia Gestione cluster HPC) e fare clic su **Finish** (Fine). HPC Cluster Manager viene avviato in un passaggio successivo.
   
    ![Finish][install_hpc7]

## <a name="prepare-the-azure-subscription"></a>Preparazione della sottoscrizione di Azure
Seguire questa procedura nel [portale di Azure](https://portal.azure.com) con la sottoscrizione di Azure. Dopo avere completato questi passaggi, è possibile distribuire i nodi di Azure dal nodo head locale. 
  
  > [!NOTE]
  > Prendere anche nota dell'ID sottoscrizione di Azure, che sarà necessario in seguito. Trovare l'ID in **Sottoscrizioni** nel portale.
  > 

### <a name="upload-the-default-management-certificate"></a>Caricare il certificato di gestione predefinito
HPC Pack installa un certificato autofirmato nel nodo head, denominato Default Microsoft HPC Azure Management, che è possibile caricare come certificato di gestione di Azure. Questo certificato viene fornito per le distribuzioni di test e di modello di verifica per proteggere le connessioni tra il nodo head e Azure.

1. Dal computer del nodo head, accedere al [portale di Azure](https://portal.azure.com).

2. Fare clic su **Sottoscrizioni** > *nome_della_sottoscrizione*.

3. Fare clic su **Certificati di gestione** > **Carica**. 4. Individuare il file C:\Programmi\Microsoft HPC Pack 2012\Bin\hpccert.cer nel nodo head. Fare quindi clic su **Carica**.

   
Il certificato **Default HPC Azure Management** viene visualizzato nell'elenco di certificati di gestione.

### <a name="create-an-azure-cloud-service"></a>Creazione di un servizio cloud di Azure
> [!NOTE]
> Per prestazioni ottimali, creare il servizio cloud e l'account di archiviazione, in una fase successiva, nella stessa area geografica.
> 
> 

1. Nel portale fare clic su **Servizi cloud (versione classica)** > **+Aggiungi**.

2.  Digitare un nome DNS per il servizio, scegliere un gruppo di risorse e una posizione e quindi fare clic su **Crea**.


### <a name="create-an-azure-storage-account"></a>Creare un account di archiviazione di Azure
1. Nel portale fare clic su **Account di archiviazione (versione classica)** > **+Aggiungi**.

2. Digitare un nome per l'account e selezionare il modello di distribuzione **Classica**.

3. Scegliere un gruppo di risorse e mantenere i valori predefiniti per le altre impostazioni. Fare quindi clic su **Crea**.

## <a name="configure-the-head-node"></a>Configurazione del nodo head
Prima di usare HPC Cluster Manager per distribuire nodi di Azure e inviare processi, è necessario eseguire alcune operazioni di configurazione del cluster.

1. Nel nodo head avviare HPC Cluster Manager. Se viene visualizzata la finestra di dialogo **Select Head Node** (Selezionare nodo head) fare clic su **Local Computer** (Computer locale). Verrà visualizzato l'elenco **Deployment To-do List** .

2. In **Required deployment tasks** (Attività di distribuzione necessarie) fare clic su **Configure your network** (Configurare la rete).
   
    ![Configurazione della rete][config_hpc2]

3. Nella configurazione guidata rete selezionare **All nodes only on an enterprise network** (Topologia 5). Questa configurazione di rete è la più semplice a scopo dimostrativo.
   
    ![Topologia 5][config_hpc3]
   
4. Fare clic su **Next** per accettare i valori predefiniti nelle pagine restanti della procedura guidata. Nella scheda **Review** (Revisione) fare clic su **Configure** (Configura) per completare la configurazione di rete.

5. In **Deployment To-do List**fare clic su **Provide installation credentials**.

6. Nella finestra di dialogo **Installation Credentials** digitare le credenziali dell'account di dominio usato per installare HPC Pack. Fare quindi clic su **OK**. 
   
    ![Installation Credentials][config_hpc6]
   
7. In **Deployment To-do List**fare clic su **Configure the naming of new nodes**.

8. Nella finestra di dialogo **Specify Node Naming Series** (Specificare serie di denominazione nodo) accettare la serie di denominazione predefinita e fare clic su **OK**. Completare questo passaggio anche se i nodi di Azure aggiunti in questa esercitazione vengono denominati automaticamente.
   
    ![Denominazione dei nodi][config_hpc8]
   
9. In **Deployment To-do List**fare clic su **Create a node template**. Più avanti in questa esercitazione, si userà il modello di nodo per aggiungere nodi di Azure al cluster.

10. Nella creazione guidata modello di nodo eseguire le operazioni seguenti:
    
    a. Nella pagina **Choose Node Template Type** (Scegliere il tipo di modello nodo) fare clic su **Windows Azure node template** (Modello di nodo Windows Azure), quindi su **Avanti**.
    
    ![Modello di nodo][config_hpc10]
    
    b. Fare clic su **Next** per accettare il nome di modello predefinito.
    
    c. Nella pagina **Provide Subscription Information** (Fornire informazioni sulla sottoscrizione) immettere l'ID sottoscrizione di Azure, disponibile nelle informazioni sull'account Azure. In **Certificato di gestione**cercare **Default Microsoft HPC Azure Management**. Quindi fare clic su **Next**.
    
    ![Modello di nodo][config_hpc12]
    
    d. Nella pagina **Provide Service Information** selezionare il servizio cloud e l'account di archiviazione creato in un passaggio precedente. Quindi fare clic su **Next**.
    
    ![Modello di nodo][config_hpc13]
    
    e. Fare clic su **Next** per accettare i valori predefiniti nelle pagine restanti della procedura guidata. Nella scheda **Review** (Revisione) fare quindi clic su **Create** (Crea) per creare il modello di nodo.
    
    > [!NOTE]
    > Per impostazione predefinita, il modello di nodo di Azure include le impostazioni per l'avvio (provisioning) e l'interruzione manuale dei nodi con HPC Cluster Manager. Facoltativamente, è possibile configurare una pianificazione per l'avvio e l'arresto automatici dei nodi di Azure.
    > 
    > 

## <a name="add-azure-nodes-to-the-cluster"></a>Aggiunta di nodi di Azure al cluster
Usare ora il modello di nodo per aggiungere nodi di Azure al cluster. L'aggiunta di nodi al cluster determina l'archiviazione delle relative informazioni di configurazione per consentirne l'avvio (provisioning) in qualsiasi momento nel servizio cloud. I costi relativi ai nodi di Azure vengono addebitati nella sottoscrizione solo dopo l'esecuzione delle istanze nel servizio cloud.

Seguire questa procedura per aggiungere due nodi piccoli.

1. In HPC Cluster Manager (Gestione cluster HPC) fare clic su **Node Management** (Gestione nodi), denominato **Resource Management** (Gestione risorse) nelle versioni correnti di HPC Pack, > **Add Node** (Aggiungi nodo).
   
    ![Actions][add_node1]

2. Nella pagina **Select Deployment Method** (Seleziona metodo di distribuzione) dell'aggiunta guidata nodi fare clic su **Add Azure nodes** (Aggiungi nodi di Azure) e quindi su **Next** (Avanti).
   
    ![Aggiunta di un nodo di Azure][add_node1_1]

3. Nella pagina **Specify New Nodes** (Specificare nuovi nodi) selezionare il modello di nodo di Azure creato in precedenza (denominato per impostazione predefinita **Default AzureNode Template**). Specificare quindi **2** nodi di dimensioni **Small** e quindi fare clic su **Next** (Avanti).
   
    ![Specifica dei nodi][add_node2]
   
4. Nella pagina **Completing the Add Node Wizard** (Completamento aggiunta guidata nodo in corso) fare clic su **Finish** (Fine).
    
     In Gestione cluster HPC saranno ora presenti due nodi di Azure denominati **AzureCN-0001** e **AzureCN-0002**. Entrambi hanno lo stato **Not-Deployed** .
   
    ![Nodi aggiunti][add_node3]

## <a name="start-the-azure-nodes"></a>Avvio dei nodi di Azure
Quando si vogliono usare le risorse cluster in Azure, usare HPC Cluster Manager per avviare i nodi di Azure (eseguire il provisioning) e portarli online.

1. In HPC Cluster Manager (Gestione cluster HPC) fare clic su **Node Management** (Gestione nodi), denominato **Resource Management** (Gestione risorse) nelle versioni correnti di HPC Pack, e selezionare i nodi di Azure.

2. Fare clic su **Start** (Avvia) e quindi su **OK**.
   
   ![Avvio dei nodi][add_node4]
   
    Per i nodi verrà eseguita la transizione allo stato **Provisioning** . Visualizzare il log di provisioning per tenere traccia dell'avanzamento del provisioning.
   
    ![Provisioning dei nodi][add_node6]

3. Dopo alcuni minuti viene completato il provisioning dei nodi di Azure, che passeranno allo stato **Offline** . In questo stato le istanze del ruolo sono in esecuzione, ma non possono ancora accettare processi cluster.

4. Per verificare che le istanze del ruolo siano in esecuzione, nel portale di Azure fare clic su **Servizi cloud (versione classica)** > *nome_del_servizio_cloud*.
   
   Verranno visualizzate due istanze (nodi) **HpcWorkerRole** in esecuzione nel servizio. HPC Pack distribuisce automaticamente anche due istanze **HpcProxy** (dimensione media) per gestire la comunicazione tra il nodo head e Azure.

   ![Esecuzione delle istanze][view_instances1]

5. Per portare online i nodi di Azure per l'esecuzione dei processi cluster, selezionare i nodi, fare clic con il pulsante destro del mouse e scegliere **Bring Online**.
   
    ![Nodi offline][add_node7]
   
    HPC Cluster Manager indica che i nodi sono in stato **Online** .

## <a name="run-a-command-across-the-cluster"></a>Esecuzione di un comando nel cluster
Per verificare l'installazione, usare il comando **clusrun** di HPC Pack per eseguire un comando o un'applicazione in uno o più nodi del cluster. Ad esempio, usare **clusrun** per ottenere la configurazione IP dei nodi di Azure.

1. Nel nodo head aprire un prompt dei comandi come amministratore.

2. Digitare il comando seguente:
   
    `clusrun /nodes:azurecn* ipconfig`

3. Se richiesto, immettere la password dell'amministratore del cluster. L'output del comando dovrebbe essere simile al seguente.
   
    ![clusrun][clusrun1]

## <a name="run-a-test-job"></a>Esecuzione di un processo di test
Ora inviare un processo di test che viene eseguito nel cluster ibrido. Questo esempio è un processo parametrico di organizzazione semplice, ovvero un tipo di calcolo intrinsecamente parallelo. In questo esempio vengono eseguite sottoattività che aggiungono un numero intero a se stesso tramite il comando **set /a** . Tutti i nodi nel cluster contribuiscono a completare le sottoattività per i numeri interi da 1 a 100.

1. In HPC Cluster Manager (Gestione cluster HPC) fare clic su **Job Management** (Gestione processi) > **New Parametric Sweep Job** (Nuovo processo parametrico di organizzazione).
   
    ![Nuovo processo][test_job1]

2. Nella finestra di dialogo **New Parametric Sweep Job** (Nuovo processo parametrico di organizzazione), in **Command line** (Riga di comando), digitare `set /a *+*` sovrascrivendo la riga di comando predefinita visualizzata. Lasciare i valori predefiniti per le impostazioni rimanenti, quindi fare clic su **Submit** per inviare il processo.
   
    ![Sweep parametrico][param_sweep1]

3. Al termine del processo, fare doppio clic sul processo **My Sweep Task** .

4. Fare clic su **View Tasks**, quindi fare clic su una sottoattività per visualizzare l'output calcolato corrispondente.
   
    ![Risultati delle attività][view_job361]

5. Per visualizzare il nodo che ha eseguito il calcolo per tale sottoattività, fare clic su **Allocated Nodes** (Nodi allocati) (il nome del nodo nel proprio cluster potrebbe essere diverso).
   
    ![Risultati delle attività][view_job362]

## <a name="stop-the-azure-nodes"></a>Interruzione dei nodi di Azure
Dopo aver provato il cluster, arrestare i nodi di Azure per evitare l'addebito di costi non necessari sul proprio account. Questo passaggio arresta il servizio cloud e vengono rimosse le istanze del ruolo di Azure.

1. In HPC Cluster Manager (Gestione cluster HPC) selezionare entrambi i nodi di Azure in **Node Management** (Gestione Nodi), denominato **Resource Management** (Gestione risorse) in versioni precedenti di HPC Pack. Fare quindi clic su **Stop** (Arresta).
   
    ![Interruzione dei nodi][stop_node1]

2. Nella finestra di dialogo **Stop Windows Azure nodes** (Arresta nodi di Microsoft Azure) fare clic su **Stop** (Arresta). 
   
3. Per i nodi verrà eseguita la transizione allo stato **Stopping** . Dopo alcuni minuti i nodi risulteranno **Not-Deployed**in HPC Cluster Manager.
   
    ![Nodi non distribuiti][stop_node4]

4. Per verificare che le istanze del ruolo non siano più in esecuzione in Azure, nel portale di Azure fare clic su **Servizi cloud (versione classica)** > *nome_del_servizio_cloud*. Nessuna istanza viene distribuita nell'ambiente di produzione. 
   
    L'esercitazione è terminata.

## <a name="next-steps"></a>Passaggi successivi
* Vedere la documentazione relativa a [HPC Pack](https://technet.microsoft.com/library/cc514029).
* Per configurare una distribuzione di cluster ibrido HPC Pack su una scala più ampia, vedere [Burst to Azure with Microsoft HPC Pack](http://go.microsoft.com/fwlink/p/?LinkID=200493)(Potenziamento della capacità di Azure con Microsoft HPC Pack).
* Per altri modi per creare un cluster HPC Pack in Azure, inclusi i modelli di Azure Resource Manager, vedere [Opzioni per cluster HPC con Microsoft HPC Pack in Azure](../virtual-machines/windows/hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


[Overview]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/hybrid_cluster_overview.png
[install_hpc1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc1.png
[install_hpc4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc4.png
[install_hpc6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc6.png
[install_hpc7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc7.png
[install_hpc10]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/install_hpc10.png
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
[add_node6]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node6.png
[add_node7]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/add_node7.png
[view_instances1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances1.png
[clusrun1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/clusrun1.png
[test_job1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/test_job1.png
[param_sweep1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/param_sweep1.png
[view_job361]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job361.png
[view_job362]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_job362.png
[stop_node1]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node1.png
[stop_node4]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/stop_node4.png
[view_instances2]: ./media/cloud-services-setup-hybrid-hpcpack-cluster/view_instances2.png

