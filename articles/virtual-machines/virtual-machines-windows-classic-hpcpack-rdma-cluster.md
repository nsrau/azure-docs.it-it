---
title: Configurare un cluster RDMA Windows per l&quot;esecuzione di applicazioni MPI | Microsoft Docs
description: Informazioni su come creare un cluster Windows HPC Pack con macchine virtuali di dimensioni H16r, H16mr, A8 o A9 per usare la rete RDMA in Azure per eseguire app MPI.
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-service-management,hpc-pack
ms.assetid: 7d9f5bc8-012f-48dd-b290-db81c7592215
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: ff9fb5f0b2229a470ea3f5c736622ee1e9228c93
ms.openlocfilehash: 4120ca24bab11f21fe66d5219f06608e33b527d1


---
# <a name="set-up-a-windows-rdma-cluster-with-hpc-pack-to-run-mpi-applications"></a>Configurare un cluster RDMA di Windows con HPC Pack per eseguire applicazioni MPI
Configurare un cluster RDMA Windows in Azure con [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) e [istanze di macchine virtuali serie H o serie A a elevato utilizzo di calcolo](virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) per l'esecuzione di applicazioni MPI (Message Passing Interface) parallele. Quando si configurano nodi con supporto per RDMA e basati su Windows Server in un cluster HPC Pack, le applicazioni MPI comunicano in modo efficiente tramite una rete a bassa latenza e a velocità effettiva elevata in Azure, sulla base della tecnologia di accesso diretto a memoria remota (RDMA).

Per eseguire carichi di lavoro MPI in macchine virtuali Linux che accedono alla rete RDMS in Azure, vedere [Configurare un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

## <a name="hpc-pack-cluster-deployment-options"></a>Opzioni di distribuzione del cluster HPC Pack
Microsoft HPC Pack è uno strumento disponibile senza costi aggiuntivi che consente di creare cluster HPC localmente o in Azure per eseguire applicazioni Windows o Linux HPC. HPC Pack include un ambiente di runtime per l'implementazione Microsoft di Message Passing Interface per Windows (MS-MPI). Se usato con istanze con supporto per RDMA e in esecuzione su un sistema operativo Windows Server supportato, HPC Pack offre un modo efficiente per eseguire applicazioni Windows MPI con accesso alla rete RDMA di Azure. 

Questo articolo descrive due scenari e fornisce i collegamenti a indicazioni dettagliate per la configurazione di un cluster Windows RDMA con Microsoft HPC Pack. 

* Scenario 1. Distribuzione di istanze del ruolo di lavoro a elevato utilizzo di calcolo (PaaS)
* Scenario 2. Distribuzione di nodi di calcolo in macchine virtuali a elevato utilizzo di calcolo (IaaS)

Per i prerequisiti di carattere generale riguardanti l'uso di istanze a elevato utilizzo di calcolo con Windows, vedere [Informazioni sulle macchine virtuali serie H e serie A a elevato utilizzo di calcolo](virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) .

## <a name="scenario-1-deploy-compute-intensive-worker-role-instances-paas"></a>Scenario 1: Distribuzione di istanze del ruolo di lavoro a elevato utilizzo di calcolo (PaaS)
Da un cluster HPC Pack esistente, aggiungere risorse di calcolo aggiuntive sotto forma di istanze del ruolo di lavoro di Azure (nodi di Azure) in esecuzione in un servizio cloud (PaaS). Questa funzionalità, denominata anche "burst in Azure" da HPC Pack, supporta una gamma di dimensioni per le istanze del ruolo di lavoro. Per l'aggiunta di nodi di Azure, specificare una delle dimensioni con supporto per RDMA.

Di seguito sono riportati le considerazioni e i passaggi per il burst in istanze di Azure con supporto per RDMA da un cluster esistente (in genere locale). È possibile usare procedure simili per aggiungere istanze del ruolo di lavoro a un nodo head HPC Pack distribuito in una macchina virtuale di Azure.

> [!NOTE]
> Per un'esercitazione che esegue il burst in Azure con HPC Pack, vedere [Configurare un cluster ibrido con HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Si noti che le considerazioni riportate nella procedura seguente si applicano specificatamente ai nodi di Azure con supporto per RDMA.
> 
> 

![Burst in Azure][burst]

### <a name="steps"></a>Passaggi
1. **Distribuire e configurare un nodo head HPC Pack 2012 R2**
   
    Scaricare il pacchetto di installazione di HPC Pack più recente dall' [Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=49922). Per i requisiti e le istruzioni per la preparazione di una distribuzione burst in Azure, vedere la [Burst to Azure Worker Instances with Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx) (Burst in Azure con Microsoft HPC Pack).
2. **Configurare un certificato di gestione nella sottoscrizione di Azure**
   
    Configurare un certificato per proteggere la connessione fra il nodo head e Azure. Per altre opzioni e procedure, vedere la pagina relativa agli [scenari per configurare il certificato di gestione di Azure per HPC Pack](http://technet.microsoft.com/library/gg481759.aspx). Per le distribuzioni di test, HPC Pack installa un certificato di gestione di Azure per HPC Microsoft predefinito che è possibile caricare rapidamente nella sottoscrizione di Azure.
3. **Creare un nuovo servizio cloud e un account di archiviazione**
   
    Usare il portale di Azure classico per creare un servizio cloud e un account di archiviazione per la distribuzione in un'area in cui sono disponibili le istanze con supporto per RDMA.
4. **Creare un modello di nodo di Azure**
   
    Usare la Creazione guidata modello di nodo in Gestione cluster HPC. Per la procedura, vedere la sezione relativa alla [creazione di un modello di nodo di Azure](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) nell'articolo che illustra la "procedura per distribuire i nodi di Azure con Microsoft HPC Pack".
   
    Per i test iniziali, è consigliabile configurare i criteri di disponibilità manuali nel modello.
5. **Aggiungere i nodi al cluster**
   
    Usare l'Aggiunta guidata nodi in Gestione cluster HPC. Per altre informazioni, vedere l'argomento relativo all' [aggiunta di nodi di Azure al cluster HPC Windows](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).
   
    Quando si specificano le dimensioni dei nodi, selezionare una delle dimensioni delle istanze con supporto per RDMA.
   
   > [!NOTE]
   > In ogni burst nella distribuzione di Azure con le istanze a elevato utilizzo di calcolo, HPC Pack distribuisce automaticamente due istanze con supporto per RDMA (ad esempio A8) come nodi proxy, oltre alle istanze del ruolo di lavoro di Azure specificate. I nodi proxy usano core allocati alla sottoscrizione e comportano un addebito insieme alle istanze del ruolo di lavoro di Azure.
   > 
   > 
6. **Avviare i nodi (effettuare il provisioning) e portarli online per eseguire i processi**
   
    Selezionare i nodi e usare l'azione **Start** in HPC Cluster Manager. Al termine del provisioning, selezionare i nodi e usare l'azione **Bring Online** in HPC Cluster Manager. I nodi sono pronti a eseguire i processi.
7. **Inviare processi al cluster**
   
   Usare gli strumenti di invio di processi di HPC Pack per eseguire processi cluster. Vedere l'argomento relativo alla [gestione dei processi con Microsoft HPC Pack](http://technet.microsoft.com/library/jj899585.aspx).
8. **Arrestare i nodi (effettuare il deprovisioning)**
   
   Al termine dell'esecuzione dei processi, portare i nodi offline e usare l'azione **Stop** in HPC Cluster Manager.

## <a name="scenario-2-deploy-compute-nodes-in-compute-intensive-vms-iaas"></a>Scenario 2: Distribuzione di nodi di calcolo in macchine virtuali a elevato utilizzo di calcolo (IaaS)
In questo scenario si distribuisce il nodo head HPC Pack e i nodi di calcolo del cluster in macchine virtuali in una rete virtuale di Azure. HPC Pack fornisce una serie di [opzioni di distribuzione nelle macchine virtuali di Azure](virtual-machines-linux-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), inclusi script di distribuzione automatizzati e modelli di avvio rapido di Azure. Ad esempio, le considerazioni e i passaggi seguenti consentono di usare lo [script di distribuzione di HPC Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) per automatizzare la distribuzione di un cluster HPC Pack 2012 R2 in Azure.

![Cluster in macchine virtuali di Azure][iaas]

### <a name="steps"></a>Passaggi
1. **Creare macchine virtuali del nodo head del cluster e del nodo di calcolo eseguendo lo script di distribuzione IaaS di HPC Pack in un computer client**
   
    Scaricare il pacchetto dello script di distribuzione IaaS di HPC Pack dall' [Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=49922).
   
    Per preparare il computer client, creare il file di configurazione dello script ed eseguire lo script, vedere [Creare un cluster Windows HPC (High Performance Computing) con lo script di distribuzione IaaS di HPC Pack](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). 
   
    Per distribuire nodi di calcolo con supporto per RDMA, si tengano presenti le considerazioni aggiuntive seguenti:
   
   * **Rete virtuale**: specificare una nuova rete virtuale in un'area in cui siano disponibili le istanze con supporto per RDMA che si vuole usare.
   * **Sistema operativo Windows Server**: per supportare la connettività RDMA, specificare un sistema operativo Windows Server 2012 R2 o Windows Server 2012 per le macchine virtuali del nodo di calcolo.
   * **Servizi cloud**: è consigliabile eseguire la distribuzione del nodo head in un servizio cloud e dei nodi di calcolo in un altro servizio cloud.
   * **Dimensioni nodo head**: per questo scenario prendere in considerazione dimensioni pari almeno ad A4 (Molto grande) per il nodo head.
   * **Estensione HpcVmDrivers**: lo script di distribuzione installa l'agente di macchine virtuali Azure e l'estensione HpcVmDrivers automaticamente quando si distribuiscono nodi di calcolo di dimensioni A8 o A9 con un sistema operativo Windows Server. L'estensione HpcVmDrivers installa i driver nelle macchine virtuali del nodo di calcolo in modo che possano connettersi alla rete RDMA. Nelle macchine virtuali serie H con supporto per RDMA è necessario installare manualmente l'estensione HpcVmDrivers. Vedere [Informazioni sulle VM serie H e serie A a elevato utilizzo di calcolo](virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#access-to-the-rdma-network).
   * **Configurazione della rete del cluster**: lo script di distribuzione configura automaticamente il cluster HPC Pack nella topologia 5 (tutti i nodi nella rete aziendale). Questa topologia è obbligatoria per tutte le distribuzioni di cluster HPC Pack nelle VM. Non modificare la topologia della rete del cluster in seguito.
2. **Portare online i nodi di calcolo per eseguire i processi**
   
    Selezionare i nodi e usare l'azione **Bring Online** in HPC Cluster Manager. I nodi sono pronti a eseguire i processi.
3. **Inviare processi al cluster**
   
    Connettersi al nodo head per inviare i processi oppure configurare un computer locale a questo scopo. Per altre informazioni, vedere [Inviare processi a un cluster HPC in Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
4. **Portare offline i nodi e arrestarli (deallocarli)**
   
    Al termine dell'esecuzione dei processi, portare offline i nodi in HPC Cluster Manager. Quindi usare gli strumenti di gestione di Azure per arrestarli.

## <a name="run-mpi-applications-on-the-cluster"></a>Eseguire applicazioni MPI nel cluster
### <a name="example-run-mpipingpong-on-an-hpc-pack-cluster"></a>Esempio: Eseguire mpipingpong su un cluster HPC Pack
Per verificare una distribuzione HPC Pack delle istanze con supporto per RDMA, è possibile eseguire il comando **mpipingpong** di HPC Pack nel cluster. **mpipingpong** invia ripetutamente pacchetti di dati tra nodi associati per calcolare le misure e le statistiche di latenza e velocità effettiva per la rete dell'applicazione abilitata per RDMA. Questo esempio illustra un modello tipico per l'esecuzione di un processo MPI, in questo caso **mpipingpong** usando il comando **mpiexec** del cluster.

L'esempio presuppone che i nodi di Azure siano stati aggiunti in una configurazione "burst in Azure" ([Scenario 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-\(PaaS\) in this article). Se HPC Pack è stato distribuito in un cluster di macchine virtuali di Azure, sarà necessario modificare la sintassi del comando per specificare un gruppo di nodi diverso e impostare ulteriori variabili di ambiente per indirizzare il traffico di rete alla rete RDMA.

Per eseguire mpipingpong sul cluster:

1. Aprire un prompt dei comandi nel nodo head o in un computer client configurato in modo appropriato.
2. Per stimare la latenza tra coppie di nodi in una distribuzione di potenziamento in Azure di quattro nodi, digitare il comando seguente per inviare un processo per eseguire mpipingpong con pacchetti di piccole dimensioni e un numero elevato di iterazioni:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```
   
    Il comando restituisce l'ID del processo inviato.
   
    In un cluster HPC Pack distribuito in macchine virtuali di Azure specificare un gruppo di nodi contenente le macchine virtuali del nodo di calcolo distribuite in un singolo servizio cloud e modificare il comando **mpiexec** come segue:
   
    ```Command
    job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI_DISABLE_SOCK 1 -env MSMPI_PRECONNECT all -env MPICH_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
    ```
3. Al termine del processo, per visualizzare l'output, in questo caso l'output dell'attività 1 del processo, digitare quanto riportato di seguito:
   
    ```Command
    task view <JobID>.1
    ```
   
    dove &lt;*JobID*&gt;è l'ID del processo inviato.
   
    Nell'output sono inclusi risultati relativi alla latenza simili a quanto riportato di seguito.
   
    ![Latenza ping pong][pingpong1]
4. Per stimare la velocità effettiva tra coppie di nodi di potenziamento in Azure, digitare il comando seguente per inviare un processo per eseguire **mpipingpong** con pacchetti di grandi dimensioni e alcune iterazioni:
   
    ```Command
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```
   
    Il comando restituisce l'ID del processo inviato.
   
    In un cluster HPC Pack distribuito in macchine virtuali di Azure modificare il comando come indicato nel passaggio 2.
5. Al termine del processo, per visualizzare l'output (in questo caso l'output dell'attività 1 del processo) digitare quanto riportato di seguito:
   
    ```Command
    task view <JobID>.1
    ```
   
   Nell'output sono inclusi risultati relativi alla velocità effettiva simili a quanto riportato di seguito.
   
   ![Velocità effettiva ping pong][pingpong2]

### <a name="mpi-application-considerations"></a>Considerazioni sulle applicazioni MPI
Di seguito sono riportate alcune considerazioni riguardanti l'esecuzione di applicazioni MPI con HPC Pack in Azure. Alcune di esse sono applicabili solo alle distribuzioni di nodi di Azure (istanze del ruolo di lavoro aggiunte in una configurazione "potenziamento in Azure").

* Le istanze del ruolo di lavoro in un servizio cloud vengono sottoposte periodicamente a nuovo provisioning senza preavviso da Azure, ad esempio per la manutenzione del sistema o in caso di errore di un'istanza. Se viene eseguito di nuovo il provisioning di un'istanza durante l'esecuzione di un processo MPI, tutti i relativi dati andranno persi e l'istanza verrà riportata allo stato in cui si trovava al momento della prima distribuzione. Queste condizioni possono determinare il mancato completamento del processo MPI. Durante l'esecuzione di un processo, più è elevato il numero di nodi utilizzati per un singolo processo MPI e più è lunga l'esecuzione del processo, maggiore è la probabilità che una delle istanze venga sottoposta di nuovo a provisioning. Si consideri questo aspetto anche se si definisce un singolo nodo nella distribuzione come file server.
* Per eseguire processi MPI in Azure, non usare le istanze con supporto per RDMA. È possibile usare qualsiasi dimensione di istanza supportata da HPC Pack. Tuttavia, le istanze con supporto per RDMA sono consigliate per eseguire su larga scala processi MPI sensibili alla latenza e alla larghezza di banda della rete tramite cui vengono connessi i nodi. Se si usano altre dimensioni per l'esecuzione di processi MPI sensibili alla latenza e alla larghezza di banda, è consigliabile eseguire piccoli processi in cui una singola attività viene eseguita solo su alcuni nodi.
* Le applicazioni distribuite in istanze di Azure sono soggette ai termini delle licenze associati all'applicazione. Verificare le licenze del fornitore di qualsiasi applicazione commerciale o altre restrizioni per l'esecuzione nel cloud. Non tutti i fornitori offrono licenze con pagamento in base al consumo.
* Le istanze di Azure richiedono procedure di configurazione aggiuntive per l'accesso a nodi, condivisioni e server licenze locali. Ad esempio, per consentire ai nodi di Azure di accedere a un server licenze locale, è possibile configurare una rete virtuale di Azure da sito a sito.
* Per eseguire applicazioni MPI in istanze di Azure, è necessario registrare ogni applicazione MPI con Windows Firewall nelle istanze eseguendo il comando **hpcfwutil** . In questo modo vengono consentite le comunicazioni MPI su una porta assegnata dinamicamente dal firewall.
  
  > [!NOTE]
  > Per le distribuzioni di potenziamento in Azure, è inoltre possibile configurare un comando di eccezione del firewall da eseguire automaticamente in tutti i nuovi nodi di Azure aggiunti al cluster. Dopo aver eseguito il comando **hpcfwutil** e verificato il funzionamento dell'applicazione, aggiungere il comando a uno script di avvio per i nodi di Azure. Per ulteriori informazioni, vedere l'articolo relativo a come [usare uno script di avvio per i nodi di Azure](https://technet.microsoft.com/library/jj899632.aspx).
  > 
  > 
* HPC Pack usa la variabile di ambiente cluster CCP_MPI_NETMASK per specificare un intervallo di indirizzi validi per la comunicazione MPI. A partire da HPC Pack 2012 R2, la variabile di ambiente cluster CCP_MPI_NETMASK interessa solo la comunicazione MPI tra i nodi di calcolo del cluster aggiunti a un dominio (in locale o in macchine virtuali di Azure). La variabile viene ignorata dai nodi aggiunti in una configurazione di potenziamento in Azure.
* I processi MPI non possono essere eseguiti in istanze di Azure distribuite in diversi servizi cloud, ad esempio nelle distribuzioni burst in Azure con modelli di nodo diversi oppure in nodi di calcolo di macchine virtuali di Azure distribuiti in più servizi cloud. Se si dispone di più distribuzioni di nodi di Azure avviate con modelli di nodo diversi, il processo MPI deve essere eseguito solo in un set di nodi di Azure.
* Quando si aggiungono nodi di Azure al cluster e vengono portati online, tramite il servizio utilità di pianificazione processi HPC viene tentato immediatamente l'avvio dei processi nei nodi. Se solo una parte del carico di lavoro può essere eseguita in Azure, assicurarsi di aggiornare o creare modelli di processo per definire i tipi di processo eseguibili in Azure. Ad esempio, per assicurarsi che i processi inviati con un modello di processo vengano eseguiti solo in nodi di Azure, è possibile aggiungere la proprietà Gruppi di nodi al modello di processo e selezionare AzureNodes come valore obbligatorio. Per creare gruppi personalizzati per i nodi di Azure, è possibile usare il cmdlet di HPC PowerShell Add-HpcGroup.

## <a name="next-steps"></a>Passaggi successivi
* In alternativa all'uso di HPC Pack, eseguire lo sviluppo con il servizio Azure Batch per eseguire applicazioni MPI in pool gestiti di nodi di calcolo in Azure. Vedere [Usare le attività a istanze multiple per eseguire applicazioni MPI (Message Passing Interface) in Azure Batch](../batch/batch-mpi.md).
* Per eseguire applicazioni MPI Linux che accedono alla rete RDMA in Azure, vedere [Configurare un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-classic-rdma-cluster.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/burst.png
[iaas]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/iaas.png
[pingpong1]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong1.png
[pingpong2]: ./media/virtual-machines-windows-classic-hpcpack-rdma-cluster/pingpong2.png



<!--HONumber=Jan17_HO1-->


