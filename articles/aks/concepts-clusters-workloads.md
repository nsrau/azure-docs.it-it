---
title: Concetti - Nozioni di base di Kubernetes per il servizio Azure Kubernetes
description: Informazioni sui componenti di base del cluster e del carico di lavoro di Kubernetes e sulle loro relazioni con le funzionalità del servizio Azure Kubernetes
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: 7f964397b476d5a97ecdde0ae22bd6662a435e1a
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/20/2019
ms.locfileid: "56456521"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Concetti di base di Kubernetes per il servizio Azure Kubernetes

Poiché lo sviluppo di applicazioni è passato a un approccio basato su contenitori, la necessità di orchestrare e gestire risorse interconnesse diventa importante. Kubernetes è la piattaforma leader che offre programmazione affidabile di carichi di lavoro applicativi dotati di tolleranza agli errori. Il servizio Azure Kubernetes è un'offerta Kubernetes gestita che semplifica ulteriormente lo sviluppo e la gestione di applicazioni basate su contenitori.

Questo articolo presenta i componenti principali dell'infrastruttura Kubernetes, fra cui i *master del cluster*, i *nodi* e i *pool di nodi*. Sono presentate anche risorse del carico di lavoro come *pod*, *distribuzioni* e *set*, nonché la procedura per raggruppare risorse in *spazi dei nomi*.

## <a name="what-is-kubernetes"></a>Cos'è Kubernetes

Kubernetes è una piattaforma in rapida evoluzione che gestisce applicazioni basate su contenitori e i componenti di rete e archiviazione associati. La piattaforma è incentrata sui carichi di lavoro applicativi e non sui componenti dell'infrastruttura sottostante. Kubernetes offre un approccio dichiarativo alle distribuzioni, supportato da una gamma completa di API per operazioni di gestione.

È possibile compilare ed eseguire applicazioni basate su microservizi moderne e portabili che sfruttano la capacità di Kubernetes di orchestrare e gestire la disponibilità di tali componenti applicativi. Kubernetes supporta applicazioni sia con stato che senza stato mentre l'adozione delle applicazioni basate su microservizi da parte dei team aumenta.

In quanto piattaforma aperta, Kubernetes consente di compilare le applicazioni con il linguaggio di programmazione, il sistema operativo, le librerie e il bus di messaggistica preferiti. Gli strumenti esistenti di integrazione continua e recapito continuo (CI/CD) possono integrarsi con Kubernetes per la pianificazione e la distribuzione delle versioni.

Il servizio Azure Kubernetes è un servizio Kubernetes gestito che riduce la complessità delle attività di distribuzione e delle attività principali di gestione, tra cui il coordinamento degli aggiornamenti. I master del cluster di servizio Azure Kubernetes sono gestiti dalla piattaforma Azure e si paga solo per i nodi di servizio Azure Kubernetes che eseguono le applicazioni. AKS si basa sul motore open source del servizio Azure Kubernetes (aks-engine).

## <a name="kubernetes-cluster-architecture"></a>Architettura del cluster Kubernetes

Un cluster Kubernetes è suddiviso in due componenti:

- I nodi *master del cluster* forniscono i servizi Kubernetes principali e l'orchestrazione dei carichi di lavoro applicativi.
- I *nodi* eseguono i carichi di lavoro applicativi.

![Componenti master del cluster e nodi di Kubernetes](media/concepts-clusters-workloads/cluster-master-and-nodes.png)

## <a name="cluster-master"></a>Master del cluster

Quando si crea un cluster servizio Azure Kubernetes, viene creato e configurato automaticamente un master del cluster. Il master del cluster è fornito come risorsa di Azure gestita indipendente dall'utente. Non sono previsti costi per il master del cluster, ma solo per i nodi che fanno parte del cluster servizio Azure Kubernetes.

Il master del cluster include i componenti di Kubernetes principali seguenti:

- *kube-apiserver*: il server API indica il modo in cui le API Kubernetes sottostanti sono esposte. Questo componente fornisce l'interazione per gli strumenti di gestione, ad esempio `kubectl` o il dashboard di Kubernetes.
- *etcd*: per mantenere lo stato della configurazione e del cluster Kubernetes, l'*etcd* con disponibilità elevata è un archivio di valori chiave all'interno di Kubernetes.
- *kube-scheduler*: quando si creano o si ridimensionano applicazioni, l'Utilità di pianificazione determina quali nodi possono eseguire il carico di lavoro e li avvia.
- *kube-controller-manager*: lo strumento di gestione del controller supervisiona molti controller più piccoli che eseguono azioni, ad esempio la replica di pod e la gestione delle operazioni dei nodi.

servizio Azure Kubernetes fornisce un master del cluster a tenant singolo con un server API, un'Utilità di pianificazione e altri elementi dedicati. L'utente definisce il numero e la dimensioni dei nodi mentre la piattaforma Azure configura la comunicazione sicura tra il master del cluster e i nodi. L'interazione con il master del cluster si verifica mediante le API di Kubernetes, ad esempio `kubectl` o il dashboard di Kubernetes.

Grazie a questo master del cluster gestito non è necessario configurare componenti come un archivio *etcd* a disponibilità elevata, ma non è possibile accedere direttamente al master del cluster. Gli aggiornamenti di Kubernetes sono orchestrati tramite l'interfaccia della riga di comando di Azure o il portale di Azure che aggiorna il master del cluster e quindi i nodi. Per risolvere eventuali problemi è possibile esaminare il log del master del cluster tramite i log di Monitoraggio di Azure.

Se è necessario configurare il master del cluster in modo particolare o di accedervi direttamente, è possibile distribuire il proprio cluster Kubernetes usando [servizio Azure Kubernetes-engine][aks-engine].

## <a name="nodes-and-node-pools"></a>Nodi e pool di nodi

Per eseguire le applicazioni e i servizi di supporto, è necessario un *nodo* Kubernetes. Un cluster servizio Azure Kubernetes ha uno o più nodi, ovvero una macchina virtuale (VM) di Azure che esegue i componenti nodo e il runtime del contenitore di Kubernetes:

- `kubelet` è l'agente di Kubernetes che elabora le richieste di orchestrazione dal master del cluster e la pianificazione di esecuzione dei contenitori richiesti.
- La rete virtuale è gestita dal *kube-proxy* in ogni nodo. Il proxy instrada il traffico di rete e gestisce gli indirizzi IP per i servizi e i pod.
- Il *runtime del contenitore* è il componente che consente alle applicazioni in contenitori di eseguire e interagire con risorse aggiuntive, ad esempio la rete virtuale e la risorsa di archiviazione. In servizio Azure Kubernetes, Docker viene usato come runtime del contenitore.

![Macchina virtuale di Azure e risorse di supporto per un nodo di Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

La dimensione della macchina virtuale di Azure per i nodi definisce il numero di CPU, la quantità di memoria e la dimensione e tipo della risorsa di archiviazione disponibile, ad esempio unità SSD a prestazioni elevate o HDD normale. Se si prevede di aver bisogno di applicazioni che richiedono grandi quantità di CPU e memoria o archiviazione a prestazioni elevate, pianificare di conseguenza la dimensione dei nodi. È anche possibile aumentare il numero di nodi del cluster servizio Azure Kubernetes in base alle esigenze.

In servizio Azure Kubernetes l'immagine della macchina virtuale per i nodi del cluster è attualmente basata su Ubuntu Linux. Quando si crea un cluster servizio Azure Kubernetes o si aumenta il numero di nodi, la piattaforma Azure crea il numero richiesto di macchine virtuali e le configura. Non è richiesta alcuna configurazione manuale.

Se è necessario usare un sistema operativo host diverso, un altro runtime del contenitore o includere pacchetti personalizzati, è possibile distribuire il proprio cluster Kubernetes usando [servizio Azure Kubernetes-engine][aks-engine]. `aks-engine` upstream rilascia funzionalità e offre opzioni di configurazione prima che siano supportate ufficialmente nei cluster del servizio Azure Kubernetes. Ad esempio, se si desidera usare contenitori di Windows o un runtime del contenitore diverso da Docker, è possibile utilizzare `aks-engine` per configurare e distribuire un cluster Kubernetes che soddisfi le esigenze correnti.

### <a name="resource-reservations"></a>Prenotazioni di risorse

Non è necessario gestire i componenti principali di Kubernetes in ogni nodo, come il *kubelet*, il *kube-proxy* e il *kube-dns*, ma effettivamente usano parte delle risorse di calcolo disponibili. Per mantenere la funzionalità e le prestazioni del nodo, le seguenti risorse di calcolo sono prenotate in ogni nodo:

- **CPU** - 60 ms
- **Memoria** - 20% fino a 4 GiB

Queste prenotazioni implicano che la quantità disponibile di CPU e memoria per le applicazioni può risultare inferiore a quanto contenuto dal nodo stesso. Se sono presenti vincoli delle risorse a causa del numero di applicazioni in esecuzione, le prenotazioni assicurano che CPU e memoria rimangano disponibili per i componenti principali di Kubernetes. Le prenotazioni di risorse non possono essere modificate.

Ad esempio: 

- Un nodo di dimensione **DS2 Standard v2** contiene 2 vCPU e 7 GiB di memoria
    - 20% di 7 GiB di memoria = 1,4 GiB
    - È disponibile un totale di *(7 - 1,4) = 5,6 GiB* di memoria per il nodo
    
- Un nodo di dimensione **E4s Standard v3** contiene 4 vCPU e 32 GiB di memoria
    - 20% di 32 GiB di memoria = 6,4 GiB, ma servizio Azure Kubernetes riserva solo un massimo di 4 GiB
    - È disponibile un totale di *(32 - 4) = 28 GiB* di memoria per il nodo
    
Il sistema operativo del nodo sottostante richiede anche una certa quantità di risorse di CPU e memoria per completare le proprie funzioni principali.

### <a name="node-pools"></a>Pool di nodi

I nodi della stessa configurazione sono raggruppati in *pool di nodi*. Un cluster Kubernetes contiene uno o più pool di nodi. Il numero e la dimensione iniziale dei nodi sono definiti quando si crea un cluster servizio Azure Kubernetes, infatti viene creato un *pool di nodi predefinito*. Questo pool di nodi predefinito in servizio Azure Kubernetes contiene le macchine virtuali sottostanti che eseguono i nodi dell'agente.

Quando si ridimensiona o si aggiorna un cluster servizio Azure Kubernetes, l'azione viene eseguita sul pool di nodi predefinito. Per le operazioni di aggiornamento, i contenitori in esecuzione vengono pianificati in altri nodi del pool fino a quando non vengono aggiornati tutti i nodi.

## <a name="pods"></a>Pod

Kubernetes usa i *pod* per eseguire un'istanza dell'applicazione. Un pod rappresenta una singola istanza dell'applicazione. I pod hanno in genere un mapping 1:1 con i contenitori, anche se vi sono scenari avanzati in cui un pod può contenere più contenitori. I pod multi-contenitore sono pianificati insieme nello stesso nodo e consentono ai contenitori di condividere le risorse correlate.

Quando si crea un pod è possibile definire *limiti per le risorse* per richiedere una certa quantità di risorse della CPU o di memoria. L'Utilità di pianificazione di Kubernetes tenta di pianificare i pod per l'esecuzione in un nodo con le risorse disponibili per soddisfare la richiesta. È anche possibile specificare limiti massimi per le risorse che impediscono a un pod dato di usare troppe risorse di calcolo del nodo sottostante. Una procedura consigliata consiste nell'includere limiti per le risorse per tutti i pod, per consentire all'l'Utilità di pianificazione di Kubernetes di capire quali risorse sono necessarie e consentite.

Per altre informazioni, vedere [Pod di Kubernetes][kubernetes-pods] e [Ciclo di vita dei pod di Kubernetes][kubernetes-pod-lifecycle].

Un pod è una risorsa logica, i carichi di lavoro applicativi sono eseguiti nei contenitori. I pod sono in genere risorse temporanee ed eliminabili e i pod con pianificazione individuale perdono alcune delle funzionalità di disponibilità elevate e ridondanza offerte da Kubernetes. I pod sono in genere distribuiti e gestiti da *controller* di Kubernetes, ad esempio il controller di distribuzione.

## <a name="deployments-and-yaml-manifests"></a>Distribuzioni e manifesti YAML

Una *distribuzione* rappresenta uno o più pod identici gestiti dal controller di distribuzione di Kubernetes. Una distribuzione specifica il numero di *repliche* (pod) da creare e l'Utilità di pianificazione di Kubernetes assicura che, in caso di problemi dei pod o dei nodi, vengano pianificati pod aggiuntivi su nodi integri.

È possibile aggiornare le distribuzioni per modificare la configurazione dei pod, l'immagine del contenitore utilizzata o la risorsa di archiviazione collegata. Il controller di distribuzione svuota e termina un determinato numero di repliche, crea repliche dalla nuova definizione della distribuzione e continua il processo fino a quando non vengono aggiornate tutte le repliche della distribuzione.

La maggior parte delle applicazioni senza stato in servizio Azure Kubernetes dovrebbe usare il modello di distribuzione anziché pianificare singoli pod. Kubernetes può monitorare l'integrità e lo stato delle distribuzioni per garantire che il numero di repliche richiesto si esegua all'interno del cluster. Quando si pianificano solo pod singoli, i pod non vengono riavviati in caso di problemi e non vengono ripianificati in nodi integri se si verificano problemi nel nodo corrente.

Se un'applicazione richiede la disponibilità costante di un quorum specifico di istanze per consentire la presa di decisioni di gestione, è preferibile che il processo di aggiornamento non comprometta tale capacità. I *budget di interruzione dei pod* possono essere utilizzati per definire il numero di repliche di una distribuzione che possono essere arrestate durante un aggiornamento o un aggiornamento dei nodi. Ad esempio, se si hanno *5* repliche nella distribuzione, è possibile definire un valore di interruzione di pod pari a *4* per consentire l'eliminazione/ripianificazione di una sola replica alla volta. Come con i limiti delle risorse di pod, una procedura consigliata è definire i budget di interruzione dei pod nelle applicazioni che richiedono la presenza costante di un numero minimo di repliche.

Le distribuzioni vengono in genere create e gestite con `kubectl create` o `kubectl apply`. Per creare una distribuzione si definisce un file manifesto nel formato YAML (YAML Ain't Markup Language). L'esempio seguente crea una distribuzione di base del server Web NGINX. La distribuzione specifica che devono essere create *3* repliche e che la porta *80* deve essere aperta nel contenitore. Richieste e limiti relativi alle risorse sono definiti anche per la CPU e la memoria.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

È possibile creare applicazioni più complesse includendo anche servizi come quelli di bilanciamento del carico all'interno del manifesto YAML.

Per altre informazioni, vedere le [distribuzioni Kubernetes][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Gestione dei pacchetti con Helm

Un approccio comune alla gestione di applicazioni in Kubernetes è l'uso di [Helm][helm]. È possibile creare e usare *grafici* Helm pubblici esistente che contengono una versione pacchetto del codice applicativo e manifesti YAML di Kubernetes per distribuire le risorse. Questi grafici Helm possono essere archiviati in locale e spesso in un repository remoto, ad esempio un [repository per grafici Helm di Registro Azure Container][acr-helm].

Per poter usare Helm, viene installato un componente server denominato *Tiller* nel cluster Kubernetes. Tiller gestisce l'installazione dei grafici all'interno del cluster. Il client Helm è installato localmente nel computer o può essere usato in [Azure Cloud Shell][azure-cloud-shell]. È possibile cercare o creare grafici Helm con il client e quindi installarli nel cluster Kubernetes.

![Helm include un componente client e un componente Tiller lato-server che crea risorse all'interno del cluster Kubernetes](media/concepts-clusters-workloads/use-helm.png)

Per altre informazioni, vedere [Installare le applicazioni con Helm nel servizio Azure Kubernetes][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>Oggetti StatefulSet e DaemonSet

Il controller di distribuzione usa l'Utilità di pianificazione di Kubernetes per eseguire un determinato numero di repliche su qualsiasi nodo disponibile con le risorse disponibili. Questo approccio di usare le distribuzioni può essere sufficiente per le applicazioni senza stato, ma non per le applicazioni che richiedono una convenzione di denominazione o una risorsa di archiviazione permanente. Per le applicazioni che richiedono una replica per ogni nodo o per nodi selezionati di un cluster, il controller di distribuzione non esamina come le repliche sono distribuite tra i nodi.

Esistono due risorse di Kubernetes che consentono di gestire questi tipi di applicazioni:

- Gli oggetti *StatefulSet* mantengono lo stato delle applicazioni oltre il ciclo di vita di un singolo pod, ad esempio la risorsa di archiviazione.
- Gli oggetti *DaemonSet* assicurano che ci sia un'istanza in esecuzione in ogni nodo in una delle prime fasi del processo di bootstrap di Kubernetes.

### <a name="statefulsets"></a>Oggetti StatefulSet

Molte applicazioni sviluppate attualmente sono senza stato, mentre gli oggetti *StatefulSet* possono essere usati per le applicazioni con stato, ad esempio le applicazioni che includono componenti di database. Un oggetto StatefulSet è simile a una distribuzione per il fatto che vengono creati e gestiti uno o più pod identici. Le repliche in un oggetto StatefulSet seguono un approccio sequenziale normale alla distribuzione, il ridimensionamento, gli aggiornamenti e la chiusura. Con un oggetto StatefulSet, la convenzione di denominazione, i nomi di rete e l'archiviazione sono mantenuti quando le repliche vengono ripianificate.

Si definisce l'applicazione nel formato YAML tramite `kind: StatefulSet` e poi il controller StatefulSet gestisce la distribuzione e la gestione delle repliche necessarie. I dati vengono scritti nella risorsa di archiviazione permanente fornita da Managed Disks di Azure o File di Azure. Con gli oggetti StatefulSet, la risorsa di archiviazione permanente sottostante rimane anche quando viene eliminato l'oggetto StatefulSet.

Per altre informazioni, vedere [Oggetti StatefulSet di Kubernetes][kubernetes-statefulsets].

Le repliche in un oggetto StatefulSet sono pianificate ed eseguire su tutti i nodi disponibili in un cluster servizio Azure Kubernetes. Se è necessario assicurarsi che almeno un pod del set venga eseguito in un nodo, è possibile usare un oggetto DaemonSet.

### <a name="daemonsets"></a>Oggetti DaemonSet

Per esigenze specifiche di raccolta di log o monitoraggio potrebbe essere necessario eseguire un pod specifico su tutti i nodi o su nodi selezionati. Un oggetto *DaemonSet* anche in questo caso viene usato per distribuire uno o più pod identici, ma il controller DaemonSet garantisce che ogni nodo specificato esegua un'istanza del pod.

Il controller DaemonSet può pianificare i pod nei nodi in una delle prime fasi del processo di avvio del cluster, prima che sia avviata l'utilità di pianificazione predefinita di Kubernetes. Questa capacità garantisce che i pod di un oggetto DaemonSet vengono avviati prima che siano pianificati i pod tradizionali in una distribuzione o in un oggetto StatefulSet.

Come gli oggetti StatefulSet, un oggetto DaemonSet viene definito come parte di una definizione YAML usando `kind: DaemonSet`.

Per altre informazioni, vedere [Oggetti DaemonSet di Kubernetes][kubernetes-daemonset].

## <a name="namespaces"></a>Spazi dei nomi

Le risorse di Kubernetes, ad esempio i pod e le distribuzioni, vengono raggruppate logicamente in uno *spazio dei nomi*. Questi raggruppamenti consentono di dividere un cluster servizio Azure Kubernetes e limitare l'accesso per creare, visualizzare o gestire le risorse in modo logico. Ad esempio, è possibile creare spazi dei nomi per separare gruppi aziendali. Gli utenti possono interagire solo con le risorse all'interno degli spazi dei nomi assegnati.

![Spazi dei nomi di Kubernetes per dividere in modo logico risorse e applicazioni](media/concepts-clusters-workloads/namespaces.png)

Quando si crea un cluster servizio Azure Kubernetes, sono disponibili gli spazi dei nomi seguenti:

- *predefinito*: lo spazio dei nomi dove i pod e le distribuzioni vengono creati per impostazione predefinita se non ne viene specificato un altro. Negli ambienti più piccoli è possibile distribuire le applicazioni direttamente nello spazio dei nomi predefinito senza creare altre suddivisioni logiche. Quando si interagisce con l'API di Kubernetes, ad esempio `kubectl get pods`, viene usato lo spazio dei nomi predefinito se non ne viene specificato un altro.
- *kube system*: lo spazio dei nomi in cui sono presenti le risorse principali, ad esempio le funzionalità di rete come DNS e proxy o il dashboard di Kubernetes. In genere non si distribuiscono le proprie applicazioni in questo spazio dei nomi.
- *kube-public*: questo spazio dei nomi solitamente non viene usato ma può essere usato per le risorse che devono essere visibili nell'intero cluster ed essere visualizzate da tutti gli utenti.

Per altre informazioni, vedere [Spazi dei nomi di Kubernetes][kubernetes-namespaces].

## <a name="next-steps"></a>Passaggi successivi

Questo articolo tratta alcuni dei componenti principali di Kubernetes descrivendo come si applicano ai cluster servizio Azure Kubernetes. Per altre informazioni sui concetti fondamentali relativi a Kubernetes e al servizio Azure Kubernetes, vedere gli articoli seguenti:

- [Kubernetes / Accesso e identità per servizio Azure Kubernetes][aks-concepts-identity]
- [Kubernetes / Sicurezza di servizio Azure Kubernetes][aks-concepts-security]
- [Kubernetes / Reti virtuali in servizio Azure Kubernetes][aks-concepts-network]
- [Kubernetes / Archiviazione in servizio Azure Kubernetes][aks-concepts-storage]
- [Kubernetes / Ridimensionamento in AKS][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
