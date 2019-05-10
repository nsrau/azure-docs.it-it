---
title: Criteri di supporto per Azure Kubernetes Service (AKS)
description: Informazioni sui criteri di supporto, responsabilità condivisa e funzionalità in anteprima (o alfa o beta) di Azure Kubernetes Service (AKS).
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: 9b779021eca11638e8ee52ec11d082e5b0e89cd4
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506691"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Criteri di supporto per Azure Kubernetes Service

Questo articolo fornisce informazioni dettagliate sui criteri di supporto tecnico e limitazioni per Azure Kubernetes Service (AKS). L'articolo illustra anche la gestione di nodi di lavoro, componenti del piano di controllo gestito, i componenti open source di terze parti e sicurezza o un patch di gestione.

## <a name="service-updates-and-releases"></a>Versioni e aggiornamenti del servizio

* Per informazioni sulla versione, vedere [note sulla versione AKS](https://github.com/Azure/AKS/releases).
* Per informazioni sulle funzionalità in anteprima, vedere [AKS visualizzare in anteprima le funzionalità e i progetti correlati](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Gestito funzionalità nel servizio contenitore di AZURE

Infrastruttura di base come un componenti di cloud service (IaaS), ad esempio calcolo o componenti di rete e consentire agli utenti l'accesso ai controlli di basso livello e le opzioni di personalizzazione. Al contrario, servizio contenitore di AZURE offre una distribuzione di Kubernetes chiavi in mano che offre ai clienti il set comune di configurazioni e le funzionalità che necessarie. Clienti servizio contenitore di AZURE non dispongono di personalizzazione, distribuzione e altre opzioni. Questi clienti non devono preoccuparsi o gestire i cluster Kubernetes direttamente.

Con servizio contenitore di AZURE, il cliente riceve completamente gestito *piano di controllo*. Il piano di controllo contiene tutti i componenti e servizi, che il cliente deve operare e offrire i cluster Kubernetes per gli utenti finali. Tutti i componenti di Kubernetes vengono mantenuti e gestiti da Microsoft.

Microsoft gestisce e monitora i componenti seguenti tramite il pannello di controllo:

* Server Kubelet o API di Kubernetes
* Etcd o in un archivio chiave-valore compatibile, fornendo Quality of Service (QoS), la scalabilità e runtime
* Servizi DNS (ad esempio, kube-dns o CoreDNS)
* Kubernetes proxy o alla rete

Servizio contenitore di AZURE non è una soluzione cluster completamente gestito. Alcuni componenti, ad esempio nodi di lavoro, sono disponibili *responsabilità condivise*, in cui gli utenti devono mantenere il cluster AKS. Input dell'utente è necessaria, ad esempio, per applicare una patch di sicurezza del sistema operativo (OS) nodo ruolo di lavoro.

I servizi si trovano *gestito* nel senso che Microsoft e dal team del servizio contenitore di AZURE distribuisce, opera e ha la responsabilità per la disponibilità dei servizi e funzionalità. I clienti non è possibile modificare questi componenti gestiti. Microsoft limita la personalizzazione per garantire un'esperienza utente coerente e scalabile. Per una soluzione completamente personalizzabile, vedere [AKS motore](https://github.com/Azure/aks-engine).

> [!NOTE]
> Nodi di lavoro servizio contenitore di AZURE vengono visualizzati nel portale di Azure come normali risorse IaaS di Azure. Ma tali macchine virtuali vengono distribuite in un gruppo di risorse di Azure personalizzata (prefisso MC\\*). È possibile modificare i nodi di lavoro servizio contenitore di AZURE. Ad esempio, è possibile usare Secure Shell (SSH) per modificare la modalità è modificare normali macchine virtuali di nodi di lavoro servizio contenitore di AZURE (non è possibile, tuttavia, modificare l'immagine del sistema operativo di base, e le modifiche potrebbero non persistere anche in un aggiornamento o al successivo riavvio), e altre risorse di Azure è possibile collegare al servizio contenitore di AZURE nodi di lavoro. Ma quando si apportano modifiche *gestione fuori banda e la personalizzazione,* può diventare non supportabile il cluster AKS. Evitare di modificare i nodi di lavoro, a meno che il supporto tecnico Microsoft permette di apportare modifiche.

## <a name="shared-responsibility"></a>Responsabilità condivisa

Quando viene creato un cluster, il cliente definisce i nodi di lavoro di Kubernetes che crea servizio contenitore di AZURE. I carichi di lavoro dei clienti vengono eseguite in tali nodi. I clienti di proprietari e possono visualizzare o modificare i nodi di lavoro.

Poiché i nodi del cluster cliente eseguire codice privato non chiamato e archiviano dati sensibili, il supporto tecnico Microsoft possibile accedervi in modo limitato. Il supporto tecnico Microsoft non può accedere, eseguire i comandi in o visualizzare i log per questi nodi senza autorizzazione esplicita dei clienti o richiedere assistenza.

Poiché i nodi di lavoro sono sensibili, Microsoft ha molta attenzione a limitare la gestione in background. In molti casi, il carico di lavoro continuerà a funzionare anche se il Kubernetes master nodi etcd e altri errori di componenti gestiti da Microsoft. Nodi di lavoro modificati senza prestare particolare attenzione che possono causare perdite di dati e i carichi di lavoro e possono eseguire il rendering del cluster non supportabile.

## <a name="aks-support-coverage"></a>Servizio contenitore di AZURE supportano code coverage

Microsoft offre supporto tecnico per le operazioni seguenti:

* Connettività a tutti i componenti di Kubernetes che il servizio di Kubernetes fornisce e supporta, ad esempio il server API.
* Gestione, il tempo di attività, QoS e operazioni di Kubernetes controllano i servizi di piano (nodi master di Kubernetes, un server API, etcd e kube-dns, ad esempio).
* Etcd. Il supporto include i backup automatizzati e trasparenti di tutti i dati etcd ogni 30 minuti per il ripristino di emergenza cluster e la pianificazione dello stato. Questi backup non sono direttamente disponibili ai clienti o gli utenti. Assicurano la coerenza e affidabilità dei dati.
* I punti di integrazione nel driver del provider di cloud di Azure per Kubernetes. Ad esempio le integrazioni in altri servizi di Azure, ad esempio servizi di bilanciamento del carico, volumi permanenti o alla rete (Kubernetes e Azure CNI).
* I componenti, ad esempio il server API Kubernetes etcd e kube-dns il piano di domande o problemi sulla personalizzazione del controllo.
* Problema riguardo a problemi di rete, ad esempio Azure CNI, kubenet, o altri l'accesso alla rete e funzionalità. Può includere i problemi di DNS risoluzione, perdita di pacchetti, routing e così via. Microsoft supporta vari scenari di rete:
  * Kubenet (basic) e advanced networking (Azure CNI) all'interno del cluster e componenti associati
  * Connettività ad altre applicazioni e servizi di Azure
  * Controller di ingresso e in ingresso o caricamento delle configurazioni del servizio di bilanciamento
  * La latenza e prestazioni di rete

Microsoft non fornisce supporto tecnico per le operazioni seguenti:

* Domande sull'uso di Kubernetes. Ad esempio, il supporto tecnico Microsoft non fornisce consigli su come creare controller di ingresso personalizzato, usare i carichi di lavoro dell'applicazione oppure applicare i pacchetti software di terze parti o open source o strumenti.
  > [!NOTE]
  > Il supporto tecnico Microsoft possono fornire consigli sul servizio contenitore di AZURE la funzionalità di cluster, personalizzazione e l'ottimizzazione (ad esempio, i problemi di operazioni di Kubernetes e procedure).
* I progetti open source di terze parti che non sono forniti come parte di Kubernetes piano di controllo o distribuito con i cluster AKS. Questi progetti potrebbero includere Istio, Helm, Envoy o ad altri utenti.
  > [!NOTE]
  > Microsoft può fornire supporto migliore per progetti open source di terze parti, ad esempio Helm e Kured. In cui lo strumento open source di terze parti si integra con il provider di cloud di Azure in Kubernetes o altri bug del servizio contenitore di AZURE specifico, Microsoft supporta applicazioni dalla documentazione di Microsoft e gli esempi.
* Software di terze parti source chiuso. Questo software può includere gli strumenti di analisi di sicurezza e i dispositivi di rete o software.
* Problema riguardo a multi-cloud o i build-esternamente. Ad esempio, Microsoft non supporta i problemi relativi all'esecuzione di una soluzione per fornitori cloud multipublic federato.
* Personalizzazioni diversi da quelli elencati nella rete le [documentazione di AKS](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > Microsoft supporta i problemi e i bug correlati ai gruppi di sicurezza di rete (Nsg). Ad esempio, il supporto tecnico Microsoft possono rispondere alle domande su un errore di sicurezza di rete per l'aggiornamento o un comportamento di bilanciamento del carico o sicurezza di rete imprevisto.

## <a name="aks-support-coverage-for-worker-nodes"></a>AKS supporta code coverage per i nodi di lavoro

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Responsabilità di Microsoft per i nodi di lavoro servizio contenitore di AZURE

Microsoft e clienti condividono la responsabilità di nodi di lavoro di Kubernetes in cui:

* L'immagine del sistema operativo di base ha richiesto l'aggiunta (ad esempio il monitoraggio e gli agenti di rete).
* I nodi di lavoro ricevono automaticamente patch del sistema operativo.
* Problemi di Kubernetes controllano piano componenti che vengono eseguiti nei nodi di lavoro vengono risolti automaticamente. I componenti seguenti:
  * kube-proxy
  * tunnel di rete che forniscono i percorsi di comunicazione in Kubernetes il master di componenti
  * kubelet
  * Daemon docker o Moby

> [!NOTE]
> In un nodo di lavoro, se un componente del piano di controllo non è operativo, il team del servizio contenitore di AZURE potrebbe essere necessario riavviare il nodo di lavoro intero. A causa delle loro accesso al limitato per carico di lavoro attivo del cliente e i dati, il team del servizio contenitore di AZURE riavvia un nodo di lavoro solo se il cliente esegue l'escalation del problema. Laddove possibile, il team del servizio contenitore di AZURE funziona per evitare un riavvio obbligatorio che interessano l'applicazione.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Responsabilità del cliente per nodi ruolo di lavoro del servizio contenitore di AZURE

Microsoft non riavvia automaticamente i nodi di lavoro per applicare patch a livello del sistema operativo. Sebbene le patch del sistema operativo vengono recapitate ai nodi di lavoro, il *cliente* è responsabile per il riavvio di nodi di lavoro per applicare le modifiche. Le librerie condivise, daemon, ad esempio unità SSD ibrida (SSHD) e altri componenti a livello di sistema o del sistema operativo vengono applicate automaticamente patch.

I clienti sono responsabili dell'esecuzione degli aggiornamenti di Kubernetes. È possibile eseguire gli aggiornamenti tramite il pannello di controllo di Azure o il comando di Azure. Questo vale per gli aggiornamenti che contengono sicurezza o i miglioramenti di funzionalità in Kubernetes.

> [!NOTE]
> In quanto servizio contenitore di AZURE è un *managed service*, gli obiettivi finali includono la rimozione di responsabilità per le patch, aggiornamenti e raccolta per rendere la gestione dei servizi più completo e indolore di log. Man mano che aumenta la capacità del servizio per la gestione end-to-end, le versioni future potrebbero omettere alcune funzioni (ad esempio, il riavvio di nodo e l'applicazione di patch automatica).

### <a name="security-issues-and-patching"></a>Problemi di sicurezza e applicazione di patch

Se viene trovato un difetto di protezione in uno o più componenti del servizio contenitore di AZURE, il team del servizio contenitore di AZURE verrà patch tutti i cluster interessati per attenuare il problema. In alternativa, il team fornirà agli utenti informazioni sull'aggiornamento.

Per i nodi di lavoro che interessa un difetto sicurezza, se una patch senza tempi di inattività è disponibile, il team di AKS tale installazione della patch e inviare notifiche agli utenti della modifica.

Quando una patch di sicurezza richiede il riavvio di nodo di lavoro, Microsoft informerà i clienti di questo requisito. Il cliente è responsabile per il riavvio o l'aggiornamento per ottenere la patch di cluster. Se gli utenti non si applicano le patch in base alle linee guida servizio contenitore di AZURE, i cluster continuerà a essere vulnerabili al problema di protezione.

### <a name="node-maintenance-and-access"></a>Accesso e la manutenzione di nodo

Nodi di lavoro sono una responsabilità condivisa e sono di proprietà da parte dei clienti. Per questo motivo, i clienti hanno la possibilità di accedere ai relativi nodi di lavoro e apportare modifiche potenzialmente dannose, ad esempio gli aggiornamenti del kernel e l'installazione o rimozione di pacchetti.

Se i clienti di apportano modifiche distruttive o passaggio del controllo componenti piano passare alla modalità offline o diventano non funzionale, servizio contenitore di AZURE rileverà l'errore e ripristinare automaticamente il nodo di lavoro allo stato funzionante precedente.

Sebbene i clienti possono accedere e modificare i nodi di lavoro, in questo modo è sconsigliato perché le modifiche possono creare un cluster, non supportabile.

## <a name="network-ports-access-and-nsgs"></a>Le porte di rete, accesso e gli Nsg

Come servizio gestito, servizio contenitore di AZURE presenta requisiti specifici di rete e connettività. Questi requisiti sono meno flessibili i requisiti per i componenti IaaS normale. Nel servizio contenitore di AZURE, operazioni come la personalizzazione delle regole di sicurezza di rete, il blocco di una porta specifica (ad esempio, usando le regole del firewall che bloccano la porta 443 in uscita) e gli URL di inserimento nella whitelist possono rendere il cluster non supportabile.

> [!NOTE]
> Servizio contenitore di AZURE non consente attualmente bloccare completamente l'uscita dal cluster (ad esempio, dominio esplicito o porta nell'elenco elementi consentiti). L'elenco di URL e le porte è soggette a modifiche senza preavviso. È possibile ottenere l'elenco aggiornato mediante la creazione di un ticket di supporto tecnico di Azure. L'elenco è solo per i clienti che sono disposti ad accettare che la disponibilità di cluster può esserne influenzata *in qualsiasi momento.*

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Funzionalità di Kubernetes alfa e beta non supportate

Servizio contenitore di AZURE supporta solo stabile funzionalità all'interno del progetto Kubernetes upstream. Se non diversamente documentato, servizio contenitore di AZURE non supporta le funzionalità di versioni beta e alfa sono disponibili nel progetto Kubernetes upstream.

In due scenari, le funzionalità beta o alfa potrebbero roll-out vengono rese disponibili a livello generale:

* I clienti hanno incontrato il prodotto servizio contenitore di AZURE, il supporto o team di progettazione e viene richiesto di provare queste nuove funzionalità.
* Queste funzionalità sono state [abilitata per un flag funzionalità](https://github.com/Azure/AKS/blob/master/previews.md). I clienti in modo esplicito devono optare per usare queste funzionalità.

## <a name="preview-features-or-feature-flags"></a>Le funzionalità di anteprima o flag funzionalità

Per le caratteristiche e funzionalità che richiedono test estesi e commenti degli utenti, Microsoft rilascia nuove funzionalità di anteprima o funzionalità dietro a un flag funzionalità. Prendere in considerazione queste funzionalità come funzionalità beta o di versione preliminare.

Funzionalità di anteprima o di flag di funzionalità non sono pensate per ambiente di produzione. Le modifiche in corso nell'API e comportamento, correzioni di bug e altre modifiche possono comportare tempi di inattività e cluster instabile.

Funzionalità in anteprima pubblica sono autunno 'best effort' previsto il supporto perché queste funzionalità sono disponibili in anteprima e non destinato all'ambiente di produzione e sono supportate dai team di supporto tecnico di AKS durante le ore lavorative solo. Per altre informazioni, vedere:

* [Domande frequenti relative al supporto tecnico Azure](https://azure.microsoft.com/en-us/support/faq/)

> [!NOTE]
> Le funzionalità di anteprima diventeranno effettive di Azure *sottoscrizione* livello. Non installare le funzionalità di anteprima in una sottoscrizione di produzione. Su una sottoscrizione di produzione, le funzionalità di anteprima per modificare il comportamento delle API predefinita e influire sulle normali operazioni.

## <a name="upstream-bugs-and-issues"></a>Problemi e i bug upstream

Considerata la velocità di sviluppo del progetto Kubernetes upstream, i bug invariabilmente si verificano. Alcuni di questi bug non può essere corretto o ovviare all'interno del sistema del servizio contenitore di AZURE. Correzioni di bug, invece, richiedono le patch di dimensioni maggiori per i progetti upstream (ad esempio Kubernetes, i sistemi operativi di nodo o di lavoro e i kernel). Per i componenti di Microsoft (ad esempio il provider di cloud di Azure), servizio contenitore di AZURE e il personale di Azure si impegna a risoluzione dei problemi a monte della community.

Quando una richiesta di supporto tecnico è root-causati da uno o più errori upstream, i team di supporto e ingegneria del servizio contenitore di AZURE saranno:

* Identificare e collegare i bug upstream con tutti i dettagli di supporti aiuta a spiegare il motivo per cui questo problema interessa i cluster o un carico di lavoro. I clienti ricevono i collegamenti per i repository necessari affinché possano guardare problemi e vedere quando una nuova versione offrirà correzioni.
* Fornire potenziali soluzioni alternative o soluzioni di attenuazione. Se il problema può essere risolta, un [problema noto](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) verranno archiviati nel repository del servizio contenitore di AZURE. Viene illustrato il deposito problema noto:
  * Il problema, inclusi i collegamenti ai bug upstream.
  * La soluzione alternativa e i dettagli sull'aggiornamento o un'altra persistenza della soluzione.
  * Approssimativo sequenze temporali per l'inclusione del problema, in base alla cadenza di rilascio a monte.
