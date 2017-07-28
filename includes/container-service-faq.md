# <a name="container-service-frequently-asked-questions"></a>Domande frequenti sul servizio contenitore

## <a name="orchestrators"></a>Agenti di orchestrazione

### <a name="which-container-orchestrators-do-you-support-on-azure-container-service"></a>Quali agenti di orchestrazione contenitore sono supportati nel servizio contenitore di Azure? 

È previsto il supporto per DC/OS open source, Docker Swarm e Kubernetes. Per altre informazioni, vedere la [panoramica](../articles/container-service/kubernetes/container-service-intro-kubernetes.md).
 
### <a name="do-you-support-docker-swarm-mode"></a>La modalità Docker Swarm è supportata? 

La modalità Swarm attualmente non è supportata, ma è presente nella guida di orientamento al servizio. 

### <a name="does-azure-container-service-support-windows-containers"></a>Il servizio contenitore di Azure supporta i contenitori Windows?  

Attualmente sono supportati i contenitori Linux con tutti gli agenti di orchestrazione. Il supporto per i contenitori Windows con Kubernetes è disponibile in anteprima.

### <a name="do-you-recommend-a-specific-orchestrator-in-azure-container-service"></a>È consigliabile un agente di orchestrazione specifico nel servizio contenitore di Azure? 
In genere non viene consigliato un agente di orchestrazione specifico. Se si ha già familiarità con uno degli agenti di orchestrazione supportati, è possibile servirsi di tale esperienza per il servizio contenitore di Azure. Le tendenze dei dati suggeriscono tuttavia che DC/OS è il prodotto collaudato per i carichi di lavoro di Big Data e IoT, Kubernetes è ideale per i carichi di lavoro nativi del cloud e Docker Swarm è noto per l'integrazione con gli strumenti di Docker e la facile curva di apprendimento.

A seconda dello scenario, è anche possibile compilare e gestire soluzioni contenitore personalizzate con altri servizi di Azure. Questi servizi includono [Macchine virtuali](../articles/virtual-machines/linux/overview.md), [Service Fabric](../articles/service-fabric/service-fabric-overview.md), [App Web](../articles/app-service-web/app-service-web-overview.md) e [Batch](../articles/batch/batch-technical-overview.md).  

### <a name="what-is-the-difference-between-azure-container-service-and-acs-engine"></a>Qual è la differenza tra il servizio contenitore di Azure e il motore ACS? 
Il servizio contenitore di Azure è un servizio di Azure supportato dal Contratto di servizio con funzionalità nel portale di Azure, strumenti da riga di comando di Azure e API di Azure. Il servizio consente di implementare rapidamente e di gestire i cluster che eseguono strumenti di orchestrazione contenitore standard con un numero relativamente basso di scelte di configurazione. 

Il [motore ACS](http://github.com/Azure/acs-engine) è un progetto open source che consente agli utenti esperti di personalizzare la configurazione dei cluster a ogni livello. Essendo possibile modificare la configurazione sia dell'infrastruttura che del software, non è previsto nessun Contratto di servizio per il motore ACS. Il supporto viene gestito tramite il progetto open source in GitHub invece che tramite i canali Microsoft ufficiali. 

## <a name="cluster-management"></a>Gestione dei cluster

### <a name="how-do-i-create-ssh-keys-for-my-cluster"></a>Come si creano le chiavi SSH per il cluster?

È possibile usare gli strumenti standard del sistema operativo per creare una coppia di chiavi pubblica e privata RSA SSH per l'autenticazione nelle macchine virtuali Linux per il cluster. Per i passaggi, vedere il materiale sussidiario per [OS X e Linux](../articles/virtual-machines/linux/mac-create-ssh-keys.md) o per [Windows](../articles/virtual-machines/linux/ssh-from-windows.md). 

Se si usano i [comandi dell'interfaccia della riga di comando di Azure 2.0](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) per distribuire un cluster del servizio contenitore, le chiavi SSH possono essere generate automaticamente per il cluster.

### <a name="how-do-i-create-a-service-principal-for-my-kubernetes-cluster"></a>Come si crea un'entità servizio per il cluster Kubernetes?

Per creare un cluster Kubernetes nel servizio contenitore di Azure, sono necessari anche l'ID e la password di un'entità servizio di Azure Active Directory. Per altre informazioni, vedere [About the service principal for a Kubernetes cluster](../articles/container-service/kubernetes/container-service-kubernetes-service-principal.md) (Informazioni sull'entità servizio per un cluster Kubernetes).

Se si usano i [comandi dell'interfaccia della riga di comando di Azure 2.0](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md) per distribuire un cluster Kubernetes, le credenziali dell'entità servizio possono essere generate automaticamente per il cluster.

### <a name="how-large-a-cluster-can-i-create"></a>Quali possono essere le dimensioni dei cluster creati?
È possibile creare un cluster con 1, 3 o 5 nodi master e scegliere fino a 100 nodi agente.

> [!IMPORTANT]
> Per cluster di maggiori dimensioni, a seconda della dimensione di VM scelta per i nodi potrebbe essere necessario aumentare la quota di core nella sottoscrizione. Per richiedere un aumento della quota, è possibile aprire una [richiesta di assistenza clienti online](../articles/azure-supportability/how-to-create-azure-support-request.md) senza alcun addebito. Con un [account gratuito di Azure](https://azure.microsoft.com/free/)è possibile usare solo un numero limitato di core di calcolo di Azure.
> 

### <a name="how-do-i-increase-the-number-of-masters-after-a-cluster-is-created"></a>Come si aumenta il numero di master dopo la creazione di un cluster? 
Dopo la creazione del cluster, il numero di master è fisso e non può essere modificato. Durante la creazione del cluster, è consigliabile selezionare più master per una disponibilità elevata.

### <a name="how-do-i-increase-the-number-of-agents-after-a-cluster-is-created"></a>Come si aumenta il numero di agenti dopo la creazione di un cluster? 
È possibile ridimensionare il numero di agenti nel cluster usando il portale di Azure o gli strumenti da riga di comando. Vedere [Ridimensionare un cluster del servizio contenitore di Azure](../articles/container-service/kubernetes/container-service-scale.md).

### <a name="what-are-the-urls-of-my-masters-and-agents"></a>Quali sono gli URL dei master e degli agenti? 
Gli URL delle risorse cluster nel servizio contenitore di Azure si basano sul prefisso del nome DNS specificato e sul nome dell'area di Azure scelto per la distribuzione. Ad esempio, il nome di dominio completo (FQDN) del nodo master presenta questo formato:

``` 
DNSnamePrefix.AzureRegion.cloudapp.azure.net
```

È possibile trovare gli URL di uso più frequente per il cluster nel portale di Azure, in Esplora risorse di Azure o in altri strumenti di Azure.

### <a name="how-do-i-tell-which-orchestrator-version-is-running-in-my-cluster"></a>Come si può stabilire la versione dell'agente di orchestrazione in esecuzione nel cluster?

* DC/OS: vedere la [documentazione di Mesosphere](https://support.mesosphere.com/hc/en-us/articles/207719793-How-to-get-the-DCOS-version-from-the-command-line-)
* Docker Swarm: eseguire `docker version`
* Kubernetes: eseguire `kubectl version`

### <a name="how-do-i-upgrade-the-orchestrator-after-deployment"></a>Qual è la procedura per aggiornare l'agente di orchestrazione dopo la distribuzione?

Attualmente, il servizio contenitore di Azure non fornisce strumenti per aggiornare la versione dell'agente di orchestrazione distribuito nel cluster. Se il servizio contenitore supporta una versione successiva, è possibile distribuire un nuovo cluster. Un'altra opzione consiste nell'usare gli strumenti specifici dell'agente di orchestrazione eventualmente disponibili per aggiornare un cluster sul posto. Per un esempio, vedere [DC/OS Upgrading](https://dcos.io/docs/1.8/administration/upgrading/) (Aggiornamento di DC/OS).
 
### <a name="where-do-i-find-the-ssh-connection-string-to-my-cluster"></a>Dove si trova la stringa di connessione SSH al cluster?

È possibile trovare la stringa di connessione nel portale di Azure oppure usando gli strumenti da riga di comando di Azure. 

1. Nel portale passare al gruppo di risorse per la distribuzione del cluster.  

2. Fare clic su **Panoramica** e fare clic sul collegamento **Distribuzioni** in **Informazioni di base**. 

3. Nel pannello **Cronologia distribuzioni** fare clic sulla distribuzione con il nome che inizia con **microsoft-acs** seguito da una data di distribuzione. Esempio: microsoft-acs-201701310000.  

4. Nella pagina **Riepilogo** in **Output** sono disponibili diversi collegamenti al cluster. **SSHMaster0** fornisce una stringa di connessione SSH al primo master nel cluster del servizio contenitore. 

Come già osservato, è anche possibile usare gli strumenti di Azure per trovare il nome FQDN del master. Stabilire una connessione SSH al master usando il nome FQDN del master e il nome utente specificato durante la creazione del cluster. Ad esempio:

```bash
ssh userName@masterFQDN –A –p 22 
```

Per altre informazioni, vedere [Connettersi a un cluster del servizio contenitore di Azure](../articles/container-service/kubernetes/container-service-connect.md).

## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni](../articles/container-service/kubernetes/container-service-intro-kubernetes.md) sul servizio contenitore di Azure.
* Distribuire un cluster del servizio contenitore usando il [portale](../articles/container-service/dcos-swarm/container-service-deployment.md) o l'[interfaccia della riga di comando di Azure 2.0](../articles/container-service/dcos-swarm/container-service-create-acs-cluster-cli.md).
