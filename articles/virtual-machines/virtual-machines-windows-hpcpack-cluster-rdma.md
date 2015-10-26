<properties
 pageTitle="Configurare un cluster RDMA Windows per l'esecuzione di applicazioni MPI | Microsoft Azure"
 description="Informazioni su come creare un cluster Windows HPC Pack con macchine virtuali di dimensioni A8 o A9 per usare la rete RDMA in Azure per eseguire app MPI."
 services="virtual-machines"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-service-management"/>
<tags
ms.service="virtual-machines"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/28/2015"
 ms.author="danlep"/>

# Configurare un cluster RDMA Windows con HPC Pack e istanze di dimensioni A8 e A9 per l'esecuzione di applicazioni MPI

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Modello Gestione risorse.


Questo articolo descrive come configurare un cluster RDMA Windows in Azure con [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) e [istanze di dimensioni A8 e A9 a elevato utilizzo di calcolo](virtual-machines-a8-a9-a10-a11-specs.md) per l'esecuzione di applicazioni MPI (Message Passing Interface). Quando si configurano istanze di dimensioni A8 e A9 basate su Windows Server per eseguire un'implementazione MPI supportata, le applicazioni MPI comunicano in modo efficiente tramite una rete a bassa latenza e a velocità effettiva elevata in Azure che è basata sulla tecnologia Accesso diretto a memoria remota (RDMA).

>[AZURE.NOTE]La tecnologia Windows RDMA in Azure è attualmente supportata per applicazioni MPI che usano l'interfaccia Microsoft Network Direct per la comunicazione tra le istanze A8 e A9.
>
> Azure offre inoltre le istanze di dimensioni A10 e A11 a elevato utilizzo di calcolo, con funzionalità di elaborazione identiche a quelle delle istanze A8 e A9, ma senza una connessione a una rete back-end RDMA. Per eseguire carichi di lavoro MPI in Azure, in genere si otterranno prestazioni migliori con le istanze A8 e A9.

Per eseguire carichi di lavoro MPI in macchine virtuali Linux che accedono alla rete RDMS in Azure, vedere [Configurare un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-cluster-rdma.md).

## Opzioni di distribuzione del cluster HPC Windows
Microsoft HPC Pack è lo strumento consigliato per creare cluster HPC basati su Windows Server in Azure. Se usato con istanze di dimensioni A8 e A9, HPC Pack offre un modo efficiente per eseguire applicazioni MPI basate su Windows che accedono alla rete RDMA in Azure. HPC Pack include un ambiente di runtime per l'implementazione Microsoft dell'interfaccia MPI (Message Passing Interface) per Windows.

Questo articolo descrive due scenari per la distribuzione di istanze A8 e A9 in cluster con Microsoft HPC Pack.

* Scenario 1. Distribuzione di istanze del ruolo di lavoro a elevato utilizzo di calcolo (PaaS)

* Scenario 2. Distribuzione di nodi di calcolo in macchine virtuali a elevato utilizzo di calcolo (IaaS)

## Prerequisiti

* **Rivedere le [considerazioni e informazioni di carattere generale](virtual-machines-a8-a9-a10-a11-specs.md)** sulle istanze a elevato utilizzo di calcolo


* **Sottoscrizione Azure**: se non si dispone di un account, è possibile creare un account di prova gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).


* **Quota di core**: potrebbe essere necessario aumentare la quota di core per distribuire un cluster di macchine virtuali A8 o A9. Ad esempio, per distribuire 8 istanze A9 con HPC Pack saranno necessari almeno 128 core. Per aumentare una quota, è possibile [aprire una richiesta di assistenza clienti online](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) senza alcun addebito.

## Scenario 1. Distribuzione di istanze del ruolo di lavoro a elevato utilizzo di calcolo (PaaS)


Da un cluster HPC Pack esistente, aggiungere risorse di calcolo aggiuntive sotto forma di istanze del ruolo di lavoro di Azure (nodi di Azure) in esecuzione in un servizio cloud (PaaS). Questa funzionalità, denominata anche "burst in Azure" da HPC Pack, supporta una gamma di dimensioni per le istanze del ruolo di lavoro. Per usare le istanze con utilizzo intensivo di calcolo, specificare una dimensione A8 o A9 durante l'aggiunta di nodi di Azure.

Di seguito sono riportati i passaggi per il potenziamento in istanze di Azure A8 o A9 da un cluster esistente (in genere locale). È possibile usare procedure simili per aggiungere istanze del ruolo di lavoro a un nodo head HPC Pack distribuito in una macchina virtuale di Azure.

>[AZURE.NOTE]Per un'esercitazione che esegue il burst in Azure con HPC Pack, vedere [Configurare un cluster ibrido con HPC Pack](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Si noti che le considerazioni nella procedura seguente si applicano specificatamente ai nodi di Azure di dimensioni A8 e A9.

![Burst in Azure][burst]



4. **Distribuire e configurare un nodo head HPC Pack 2012 R2**

    Scaricare il pacchetto di installazione di HPC Pack più recente dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=47755). Per i requisiti e le istruzioni per la preparazione di una distribuzione burst in Azure, vedere [Guida introduttiva per HPC Pack](https://technet.microsoft.com/library/jj884144.aspx) e [Burst in Azure con Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx)

5. **Configurare un certificato di gestione nella sottoscrizione di Azure**

    Configurare un certificato per proteggere la connessione fra il nodo head e Azure. Per altre opzioni e procedure, vedere la pagina relativa agli [scenari per configurare il certificato di gestione di Azure per HPC Pack](http://technet.microsoft.com/library/gg481759.aspx).

6. **Creare un nuovo servizio cloud e un account di archiviazione**

    Usare il portale di gestione di Azure per creare un servizio cloud e un account di archiviazione per la distribuzione in un'area in cui le istanze a elevato utilizzo di calcolo sono disponibili. Non associare il servizio cloud e l'account di archiviazione a un gruppo di affinità esistente usato per altre distribuzioni.

7. **Creare un modello di nodo di Azure**

    Usare la Creazione guidata modello di nodo in Gestione cluster HPC. Per la procedura, vedere la sezione relativa alla [creazione di un modello di nodo di Azure](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Templ) nell'articolo che illustra la "procedura per distribuire i nodi di Azure con Microsoft HPC Pack".

    Per i test iniziali, è consigliabile configurare i criteri di disponibilità manuali nel modello.

8. **Aggiungere i nodi al cluster**

    Usare l'Aggiunta guidata nodi in Gestione cluster HPC. Per altre informazioni, vedere l'argomento relativo all'[aggiunta di nodi di Azure al cluster HPC Windows](http://technet.microsoft.com/library/gg481758.aspx#BKMK_Add).

    Quando si specificano le dimensioni dei nodi, selezionare A8 o A9.

9. **Avviare i nodi (effettuare il provisioning) e portarli online per eseguire i processi**

    Selezionare i nodi e usare l'azione **Start** in HPC Cluster Manager. Al termine del provisioning, selezionare i nodi e usare l'azione **Bring Online** in HPC Cluster Manager. I nodi sono pronti a eseguire i processi.

10. **Inviare processi al cluster**

    Usare gli strumenti di invio di processi di HPC Pack per eseguire processi cluster. Vedere l'argomento relativo alla [gestione dei processi con Microsoft HPC Pack](http://technet.microsoft.com/library/jj899585.aspx).

11. **Arrestare i nodi (effettuare il deprovisioning)**

    Al termine dell'esecuzione dei processi, portare i nodi offline e usare l'azione **Stop** in HPC Cluster Manager.


### Considerazione aggiuntive

* **Nodi proxy**: in ogni distribuzione burst in Azure con le istanze a elevato utilizzo di calcolo, HPC Pack distribuisce automaticamente almeno altre 2 istanze con dimensioni A8 come nodi proxy, oltre alle istanze del ruolo di lavoro di Azure specificate. Per altre informazioni, vedere l'argomento relativo all'[impostazione del numero di nodi proxy di Azure](https://technet.microsoft.com/library/jj899633.aspx). I modi proxy usano core allocati alla sottoscrizione e comportano un addebito insieme alle istanze del ruolo di lavoro di Azure.

* **Rete virtuale**: al momento HPC Pack non supporta la configurazione di una VPN da punto a sito per le distribuzioni PaaS.


## Scenario 2. Distribuzione di nodi di calcolo in macchine virtuali a elevato utilizzo di calcolo (IaaS)

In questo scenario si distribuisce il nodo head HPC Pack e i nodi di calcolo del cluster in macchine virtuali aggiunte a un dominio di Active Directory in una rete virtuale di Azure. Lo [script di distribuzione IaaS di HPC Pack](virtual-machines-hpcpack-cluster-powershell-script.md) consente di eseguire automaticamente la maggior parte di questo processo e offre opzioni flessibili per la distribuzione, inclusa la possibilità di specificare le dimensioni di macchina virtuale A8 o A9 per i nodi del cluster. Attenersi alla seguente procedura per usare questo metodo di distribuzione automatica. In alternativa, è possibile distribuire il cluster con il modello di distribuzione di Gestione risorse usando un modello di Guida introduttiva di Azure. Per le distribuzioni di test, è anche possibile distribuire manualmente il dominio di Active Directory, la macchina virtuale del nodo head, le macchine virtuali del nodo di calcolo e altre parti dell'infrastruttura del cluster HPC Pack in Azure. Vedere [Opzioni per creare e gestire un cluster HPC con Microsoft HPC Pack in Azure](virtual-machines-hpcpack-cluster-options.md).

![Cluster in macchine virtuali di Azure][iaas]


1. **Creare macchine virtuali del nodo head del cluster e del nodo di calcolo eseguendo lo script di distribuzione IaaS di HPC Pack in un computer client**

 Scaricare il pacchetto dello script di distribuzione IaaS di HPC Pack dall'[Area download Microsoft](https://www.microsoft.com/download/details.aspx?id=47755).

 Per preparare il computer client, creare il file di configurazione dello script ed eseguire lo script. Vedere [Creare un cluster HPC Windows con lo script di distribuzione IaaS di HPC Pack](virtual-machines-hpcpack-cluster-powershell-script.md). Per distribuire nodi di calcolo di dimensioni A8 e A9, vedere le considerazioni aggiuntive più avanti in questo articolo.

2. **Portare online i nodi di calcolo per eseguire i processi**

    Selezionare i nodi e usare l'azione **Bring Online** in HPC Cluster Manager. I nodi sono pronti a eseguire i processi.

3. **Inviare processi al cluster**

    Connettersi al nodo head per inviare i processi oppure configurare un computer locale a questo scopo. Per altre informazioni, vedere [Inviare processi a un cluster HPC in Azure](virtual-machines-hpcpack-cluster-submit-jobs.md).

4. **Portare offline i nodi e arrestarli (deallocarli)**

    Al termine dell'esecuzione dei processi, portare offline i nodi in HPC Cluster Manager. Quindi usare gli strumenti di gestione di Azure per arrestarli.

### Considerazioni aggiuntive per l'esecuzione dello script di distribuzione del cluster
* **Rete virtuale**: specificare una nuova rete virtuale in un'area in cui sono disponibili le istanze A8 e A9.


* **Sistema operativo Windows Server**: per supportare la connettività RDMA, specificare un sistema operativo Windows Server 2012 R2 o Windows Server 2012 per le macchine virtuali del nodo di calcolo di dimensioni A8 o A9.


* **Servizi cloud**: è consigliabile eseguire la distribuzione del nodo head in un servizio cloud e dei nodi di calcolo A8 e A9 in un altro servizio cloud.


* **Dimensioni del nodo head**: quando si aggiungono macchine virtuali del nodo di calcolo nelle dimensioni A8 o A9, valutare l'uso di un nodo head con dimensioni di almeno A4 (Extra Large).


* **Estensione HpcVmDrivers**: lo script di distribuzione installa l'agente di macchine virtuali Azure e l'estensione HpcVmDrivers automaticamente quando si distribuiscono nodi di calcolo di dimensioni A8 o A9 con un sistema operativo Windows Server. L'estensione HpcVmDrivers installa i driver nelle macchine virtuali del nodo di calcolo in modo che possano connettersi alla rete RDMA. Vedere [Estensioni VM e funzionalità di Azure](virtual-machines-extensions-features.md).


* **Configurazione della rete del cluster**: lo script di distribuzione configura automaticamente il cluster HPC Pack nella topologia 5 (tutti i nodi nella rete aziendale). Questa topologia è obbligatoria per tutte le distribuzioni del cluster HPC Pack nelle macchine virtuali, incluse quelle con nodi di calcolo di dimensioni A8 o A9. Non modificare la topologia della rete del cluster in seguito.

## Eseguire applicazioni MPI in istanze A8 e A9

### Esempio: Eseguire mpipingpong su un cluster HPC Pack

Per verificare una distribuzione HPC Pack delle istanze a elevato utilizzo di calcolo, è possibile eseguire il comando **mpipingpong** di HPC Pack nel cluster. **mpipingpong** invia ripetutamente pacchetti di dati tra nodi associati per calcolare le misure e le statistiche di latenza e velocità effettiva per la rete dell'applicazione abilitata per RDMA. Questo esempio illustra un modello tipico per l'esecuzione di un processo MPI (in questo caso **mpipingpong**) usando il comando **mpiexec** del cluster.

L'esempio presuppone che i nodi di Azure siano stati aggiunti con una configurazione "burst in Azure" ([Scenario 1](#scenario-1.-deploy-compute-intensive-worker-role-instances-(PaaS)) in questo articolo). Se HPC Pack è stato distribuito in un cluster di macchine virtuali di Azure, sarà necessario modificare la sintassi del comando per specificare un gruppo di nodi diverso e impostare ulteriori variabili di ambiente per indirizzare il traffico di rete alla rete RDMA.


Per eseguire mpipingpong sul cluster:


1. Avviare un prompt dei comandi nel nodo head o in un computer client configurato in modo appropriato.

2. Per stimare la latenza tra coppie di nodi in una distribuzione di potenziamento in Azure di 4 nodi, digitare il comando seguente per inviare un processo per eseguire mpipingpong con pacchetti di piccole dimensioni e un numero elevato di iterazioni:

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 1:100000 -op -s nul
    ```

    Il comando restituisce l'ID del processo inviato.

    In un cluster HPC Pack distribuito in macchine virtuali di Azure specificare un gruppo di nodi contenente le macchine virtuali del nodo di calcolo distribuite in un singolo servizio cloud e modificare il comando **mpiexec** come segue:

  ```
  job submit /nodegroup:vmcomputenodes /numnodes:4 mpiexec -c 1 -affinity -env MSMPI\_DISABLE\_SOCK 1 -env MSMPI\_PRECONNECT all -env MPICH\_NETMASK 172.16.0.0/255.255.0.0 mpipingpong -p 1:100000 -op -s nul
  ```

3. Al termine del processo, per visualizzare l'output, in questo caso l'output dell'attività 1 del processo, digitare quanto riportato di seguito:

    ```
    task view <JobID>.1
    ```

    dove &lt;*JobID*&gt; rappresenta l'ID del processo inviato.

    Nell'output saranno inclusi risultati relativi alla latenza simili a quanto riportato di seguito.

    ![Latenza ping pong][pingpong1]

4. Per stimare la velocità effettiva tra coppie di nodi di potenziamento in Azure, digitare il comando seguente per inviare un processo per eseguire **mpipingpong** con pacchetti di grandi dimensioni e un numero limitato di iterazioni:

    ```
    job submit /nodegroup:azurenodes /numnodes:4 mpiexec -c 1 -affinity mpipingpong -p 4000000:1000 -op -s nul
    ```

    Il comando restituisce l'ID del processo inviato.

    In un cluster HPC Pack distribuito in macchine virtuali di Azure modificare il comando come indicato nel passaggio 2.

5. Al termine del processo, per visualizzare l'output, in questo caso l'output dell'attività 1 del processo, digitare quanto segue: task view &lt;JobID&gt;.1

  Nell'output saranno inclusi risultati relativi alla velocità effettiva simili a quanto riportato di seguito.

  ![Velocità effettiva ping pong][pingpong2]


### Considerazioni sulle applicazioni MPI


Di seguito sono riportate alcune considerazioni per l'esecuzione di applicazioni MPI in istanze di Azure. Alcune di esse sono applicabili solo alle distribuzioni di nodi di Azure (istanze del ruolo di lavoro aggiunte in una configurazione "potenziamento in Azure").

* Le istanze del ruolo di lavoro in un servizio cloud vengono sottoposte periodicamente a nuovo provisioning senza preavviso da Azure, ad esempio per la manutenzione del sistema o in caso di errore di un'istanza. Se viene eseguito di nuovo il provisioning di un'istanza durante l'esecuzione di un processo MPI, tutti i relativi dati andranno persi e l'istanza verrà riportata allo stato in cui si trovava al momento della prima distribuzione. Queste condizioni possono determinare il mancato completamento del processo MPI. Durante l'esecuzione di un processo, più è elevato il numero di nodi utilizzati per un singolo processo MPI e più è lunga l'esecuzione del processo, maggiore sarà la probabilità che una delle istanze venga sottoposta di nuovo a provisioning. È consigliabile considerare questo aspetto anche se si definisce un singolo nodo nella distribuzione come file server.


* Non è necessario usare le istanze A8 e A9 per eseguire processi MPI in Azure. È possibile usare qualsiasi dimensione di istanza supportata da HPC Pack. Tuttavia, le istanze A8 e A9 sono consigliate per eseguire su larga scala processi MPI sono sensibili alla latenza e alla larghezza di banda della rete tramite cui vengono connessi i nodi. Se si usano istanze diverse dalle A8 e A9 per l'esecuzione di processi MPI sensibili alla latenza e alla larghezza di banda, è consigliabile eseguire piccoli processi in cui una singola attività viene eseguita solo su alcuni nodi.

* Le applicazioni distribuite in istanze di Azure sono soggette ai termini delle licenze associati all'applicazione. Verificare le licenze del fornitore di qualsiasi applicazione commerciale o altre restrizioni per l'esecuzione nel cloud. Non tutti i fornitori offrono licenze con pagamento in base al consumo.


* Le istanze di Azure richiedono procedure di configurazione aggiuntive per l'accesso a nodi, condivisioni e server licenze locali. Ad esempio, per consentire ai nodi di Azure di accedere a un server licenze locale, è possibile configurare una rete virtuale di Azure da sito a sito.


* Per eseguire applicazioni MPI in istanze di Azure, è necessario registrare ogni applicazione MPI con Windows Firewall nelle istanze eseguendo il comando **hpcfwutil**. In questo modo vengono consentite le comunicazioni MPI su una porta assegnata dinamicamente dal firewall.

    >[AZURE.NOTE]Per le distribuzioni di potenziamento in Azure, è inoltre possibile configurare un comando di eccezione del firewall da eseguire automaticamente in tutti i nuovi nodi di Azure aggiunti al cluster. Dopo aver eseguito il comando **hpcfwutil** e verificato il funzionamento dell'applicazione, aggiungere il comando a uno script di avvio per i nodi di Azure. Per altre informazioni, vedere l'articolo che illustra l'[utilizzo di uno script di avvio per i nodi di Azure].https://technet.microsoft.com/library/jj899632(v=ws.10).aspx).



* HPC Pack usa la variabile di ambiente cluster CCP\_MPI\_NETMASK per specificare un intervallo di indirizzi validi per la comunicazione MPI. A partire da HPC Pack 2012 R2, la variabile di ambiente cluster CCP\_MPI\_NETMASK interessa solo la comunicazione MPI tra i nodi di calcolo del cluster aggiunti a un dominio (in locale o in macchine virtuali di Azure). La variabile viene ignorata dai nodi aggiunti in una configurazione di potenziamento in Azure.


* I processi MPI non possono essere eseguiti in istanze di Azure distribuite in diversi servizi cloud, ad esempio nelle distribuzioni burst in Azure con modelli di nodo diversi oppure in nodi di calcolo di macchine virtuali di Azure distribuiti in più servizi cloud. Se si dispone di più distribuzioni di nodi di Azure avviate con modelli di nodo diversi, il processo MPI deve essere eseguito solo in un set di nodi di Azure.


* Quando si aggiungono nodi di Azure al cluster e vengono portati online, tramite il servizio utilità di pianificazione processi HPC viene tentato immediatamente l'avvio dei processi nei nodi. Se solo una parte del carico di lavoro può essere eseguita in Azure, assicurarsi di aggiornare o creare modelli di processo per definire i tipi di processo eseguibili in Azure. Ad esempio, per assicurarsi che i processi inviati con un modello di processo vengano eseguiti solo in nodi di Azure, è possibile aggiungere la proprietà Gruppi di nodi al modello di processo e selezionare AzureNodes come valore obbligatorio. Per creare gruppi personalizzati per i nodi di Azure, è possibile usare il cmdlet di HPC PowerShell Add-HpcGroup.


## Passaggi successivi

* Per eseguire applicazioni MPI Linux che accedono alla rete RDMA in Azure, vedere [Configurare un cluster Linux RDMA per eseguire applicazioni MPI](virtual-machines-linux-cluster-rdma.md).

<!--Image references-->
[burst]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/burst.png
[iaas]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/iaas.png
[pingpong1]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/pingpong1.png
[pingpong2]: ./media/virtual-machines-windows-hpcpack-cluster-rdma/pingpong2.png

<!---HONumber=Oct15_HO3-->