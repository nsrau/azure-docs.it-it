---
title: Criteri di supporto per Azure Kubernetes Service (AKS)
description: Informazioni sui criteri di supporto di Azure Kubernetes Service (AKS), sulla responsabilità condivisa e sulle funzionalità disponibili in anteprima (o Alpha o beta).
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: 8c1736fcd4356e0778c3b36301b4490764efab76
ms.sourcegitcommit: 992e070a9f10bf43333c66a608428fcf9bddc130
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2019
ms.locfileid: "71240891"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Criteri di supporto per il servizio Azure Kubernetes

Questo articolo fornisce informazioni dettagliate sui criteri e sulle limitazioni del supporto tecnico per il servizio Azure Kubernetes (AKS). L'articolo descrive anche la gestione dei nodi di lavoro, i componenti del piano di controllo gestito, i componenti open source di terze parti e la gestione della sicurezza o delle patch.

## <a name="service-updates-and-releases"></a>Aggiornamenti e versioni del servizio

* Per informazioni sulla versione, vedere le [Note sulla versione di AKS](https://github.com/Azure/AKS/releases).
* Per informazioni sulle funzionalità disponibili in anteprima, vedere [funzionalità di anteprima di AKS e progetti correlati](https://github.com/Azure/AKS/blob/master/previews.md).

## <a name="managed-features-in-aks"></a>Funzionalità gestite in AKS

I componenti cloud di base dell'infrastruttura distribuita come servizio (IaaS), ad esempio i componenti di calcolo o di rete, consentono agli utenti di accedere ai controlli di basso livello e alle opzioni di personalizzazione. Al contrario, AKS fornisce una distribuzione Kubernetes chiavi in mano che offre ai clienti il set comune di configurazioni e funzionalità necessarie. I clienti AKS hanno una personalizzazione, una distribuzione e altre opzioni limitate. Questi clienti non devono preoccuparsi o gestire direttamente i cluster Kubernetes.

Con AKS, il cliente ottiene un piano di *controllo*completamente gestito. Il piano di controllo contiene tutti i componenti e i servizi che il cliente deve usare e fornire cluster Kubernetes agli utenti finali. Tutti i componenti di Kubernetes vengono gestiti e gestiti da Microsoft.

Microsoft gestisce e monitora i componenti seguenti tramite il riquadro di controllo:

* Server API Kubelet o Kubernetes
* ETCD o un archivio chiave-valore compatibile, che fornisce qualità del servizio (QoS), scalabilità e Runtime
* Servizi DNS (ad esempio, Kube-DNS o CoreDNS)
* Proxy Kubernetes o rete

AKS non è una soluzione cluster completamente gestita. Alcuni componenti, ad esempio i nodi del ruolo di lavoro, hanno *responsabilità condivise*, in cui gli utenti devono contribuire alla gestione del cluster AKS. L'input dell'utente è obbligatorio, ad esempio, per applicare una patch di sicurezza del sistema operativo del nodo di lavoro.

I servizi vengono *gestiti* nel senso che Microsoft e il team AKS distribuiscono, operano ed è responsabile della disponibilità e della funzionalità dei servizi. I clienti non possono modificare questi componenti gestiti. Microsoft limita la personalizzazione per garantire un'esperienza utente coerente e scalabile. Per una soluzione completamente personalizzabile, vedere [motore AKS](https://github.com/Azure/aks-engine).

> [!NOTE]
> I nodi del ruolo di lavoro AKS vengono visualizzati nel portale di Azure come normali risorse IaaS di Azure. Tuttavia, queste macchine virtuali vengono distribuite in un gruppo di risorse di Azure personalizzato (\\con il prefisso MC *). È possibile modificare i nodi del ruolo di lavoro AKS. Ad esempio, è possibile usare Secure Shell (SSH) per modificare i nodi del ruolo di lavoro AKS nel modo in cui si modificano le normali macchine virtuali (non è possibile, tuttavia, modificare l'immagine del sistema operativo di base e le modifiche potrebbero non essere mantenute tramite un aggiornamento o un riavvio) ed è possibile aggiungere altre risorse di Azure a AKS nodi di lavoro. Tuttavia, quando si apportano modifiche *fuori banda gestione e personalizzazione,* il cluster AKS può diventare non supportato. Evitare di modificare i nodi del ruolo di lavoro a meno che supporto tecnico Microsoft non convenga a apportare modifiche.

## <a name="shared-responsibility"></a>Responsabilità condivisa

Quando viene creato un cluster, il cliente definisce i nodi del ruolo di lavoro di Kubernetes creati da AKS. I carichi di lavoro dei clienti vengono eseguiti in questi nodi. I clienti sono proprietari e possono visualizzare o modificare i nodi del ruolo di lavoro.

Poiché i nodi del cluster del cliente eseguono codice privato e archiviano dati sensibili, supporto tecnico Microsoft possibile accedervi solo in modo limitato. Supporto tecnico Microsoft non è in grado di accedere a, eseguire comandi in o visualizzare i log per questi nodi senza autorizzazione o assistenza per i clienti Express.

Poiché i nodi del ruolo di lavoro sono sensibili, Microsoft presta molta attenzione a limitare la gestione in background. In molti casi, il carico di lavoro continuerà a essere eseguito anche se i nodi master di Kubernetes, ETCD e altri componenti gestiti da Microsoft hanno esito negativo. I nodi del ruolo di lavoro modificati in modo sconsiderato possono causare perdite di dati e carichi di lavoro e possono rendere il cluster non supportato.

## <a name="aks-support-coverage"></a>Copertura del supporto AKS

Microsoft fornisce supporto tecnico per gli elementi seguenti:

* Connettività a tutti i componenti di Kubernetes forniti e supportati dal servizio Kubernetes, ad esempio il server API.
* Gestione, tempo di attività, QoS e operazioni dei servizi del piano di controllo Kubernetes (nodi master Kubernetes, Server API, ETCD e Kube-DNS, ad esempio).
* ETCD. Il supporto include backup automatizzati e trasparenti di tutti i dati di ETCD ogni 30 minuti per la pianificazione di emergenza e il ripristino dello stato del cluster. Questi backup non sono direttamente disponibili per i clienti o gli utenti. Garantiscono l'affidabilità e la coerenza dei dati.
* Tutti i punti di integrazione nel driver del provider di servizi cloud di Azure per Kubernetes. Sono incluse le integrazioni in altri servizi di Azure, ad esempio servizi di bilanciamento del carico, volumi permanenti o rete (Kubernetes e Azure CNI).
* Domande o problemi relativi alla personalizzazione dei componenti del piano di controllo, ad esempio il server API Kubernetes, ETCD e Kube-DNS.
* Problemi relativi alla rete, ad esempio Azure CNI, kubenet o altri problemi di accesso alla rete e funzionalità. I problemi possono includere la risoluzione DNS, la perdita di pacchetti, il routing e così via. Microsoft supporta diversi scenari di rete:
  * Kubenet (Basic) e Advanced Networking (Azure CNI) all'interno del cluster e dei componenti associati
  * Connettività ad altri servizi e applicazioni di Azure
  * Controller in ingresso e configurazioni del servizio di bilanciamento del carico o in ingresso
  * Prestazioni e latenza della rete

Microsoft non fornisce supporto tecnico per gli elementi seguenti:

* Domande su come usare Kubernetes. Ad esempio, supporto tecnico Microsoft non fornisce consigli su come creare controller di ingresso personalizzati, usare i carichi di lavoro dell'applicazione o applicare strumenti o pacchetti software open source o di terze parti.
  > [!NOTE]
  > Supporto tecnico Microsoft può consigliare sulla funzionalità del cluster AKS, sulla personalizzazione e sull'ottimizzazione (ad esempio, i problemi e le procedure delle operazioni Kubernetes).
* Progetti open source di terze parti che non vengono forniti come parte del piano di controllo Kubernetes o distribuiti con cluster AKS. Questi progetti possono includere Istio, Helm, inviato o altri.
  > [!NOTE]
  > Microsoft può fornire supporto per il massimo sforzo per progetti open source di terze parti, ad esempio Helm e KURED. Quando lo strumento open source di terze parti si integra con il provider di servizi cloud di Azure Kubernetes o altri bug specifici di AKS, Microsoft supporta esempi e applicazioni della documentazione Microsoft.
* Software di terze parti con codice sorgente chiuso. Questo software può includere strumenti di analisi della sicurezza e dispositivi di rete o software.
* Problemi relativi a build-out multicloud o multifornitore. Microsoft, ad esempio, non supporta problemi correlati all'esecuzione di una soluzione di fornitore di cloud multipubblico federata.
* Personalizzazioni di rete diverse da quelle elencate nella [documentazione di AKS](https://docs.microsoft.com/azure/aks/).
  > [!NOTE]
  > Microsoft supporta problemi e bug correlati ai gruppi di sicurezza di rete (gruppi). Ad esempio, supporto tecnico Microsoft possibile rispondere a domande relative a un errore NSG da aggiornare o a un comportamento imprevisto di NSG o di bilanciamento del carico.

## <a name="aks-support-coverage-for-worker-nodes"></a>Copertura del supporto AKS per i nodi del ruolo di lavoro

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Responsabilità di Microsoft per i nodi del ruolo di lavoro AKS

Microsoft e i clienti condividono la responsabilità dei nodi Kubernetes Worker nei casi seguenti:

* L'immagine del sistema operativo di base ha aggiunte obbligatorie (ad esempio, gli agenti di monitoraggio e di rete).
* I nodi del ruolo di lavoro ricevono automaticamente le patch del sistema operativo.
* I problemi relativi ai componenti del piano di controllo Kubernetes eseguiti nei nodi di lavoro vengono corretti automaticamente. I componenti includono quanto segue:
  * Kube-proxy
  * Tunnel di rete che forniscono percorsi di comunicazione ai componenti Master Kubernetes
  * Kubelet
  * Daemon Docker o Moby

> [!NOTE]
> In un nodo del ruolo di lavoro, se un componente del piano di controllo non è operativo, il team AKS potrebbe dover riavviare i singoli componenti o l'intero nodo di lavoro. Queste operazioni di riavvio vengono automatizzate e forniscono la correzione automatica per i problemi comuni. Questi riavvii vengono eseguiti solo a livello di _nodo_ e non al cluster, a meno che non si tratti di una manutenzione o un'interruzione di emergenza.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Responsabilità del cliente per i nodi del ruolo di lavoro AKS

Microsoft non riavvia automaticamente i nodi del ruolo di lavoro per applicare le patch a livello di sistema operativo. Sebbene le patch del sistema operativo vengano recapitate ai nodi di lavoro, il *cliente* è responsabile del riavvio dei nodi del ruolo di lavoro per applicare le modifiche. Le librerie condivise, i daemon quali SSHD (Solid-State Hybrid Drive) e altri componenti a livello del sistema o del sistema operativo vengono automaticamente corretti.

I clienti sono responsabili dell'esecuzione degli aggiornamenti di Kubernetes. Possono eseguire gli aggiornamenti tramite il pannello di controllo di Azure o l'interfaccia della riga di comando di Azure. Questo vale per gli aggiornamenti che contengono miglioramenti alla sicurezza o alle funzionalità di Kubernetes.

> [!NOTE]
> Poiché AKS è un *servizio gestito*, gli obiettivi finali includono la rimozione della responsabilità per le patch, gli aggiornamenti e la raccolta di log, in modo da rendere la gestione dei servizi più completa e a portata di mano. Con l'aumentare della capacità del servizio per la gestione end-to-end, le versioni future potrebbero omettere alcune funzioni (ad esempio, il riavvio del nodo e l'applicazione automatica delle patch).

### <a name="security-issues-and-patching"></a>Problemi di sicurezza e applicazione di patch

Se viene rilevato un errore di sicurezza in uno o più componenti di AKS, il team di AKS patcherà tutti i cluster interessati per attenuare il problema. In alternativa, il team fornirà indicazioni per l'aggiornamento degli utenti.

Per i nodi del ruolo di lavoro interessati da un difetto di sicurezza, se è disponibile una patch con zero inattività, il team AKS applicherà tale patch e invierà una notifica agli utenti.

Quando una patch di sicurezza richiede il riavvio del nodo di lavoro, Microsoft invierà una notifica ai clienti di questo requisito. Il cliente è responsabile del riavvio o dell'aggiornamento per ottenere la patch del cluster. Se gli utenti non applicano le patch secondo le indicazioni di AKS, il cluster continuerà a essere vulnerabile al problema di sicurezza.

### <a name="node-maintenance-and-access"></a>Gestione e accesso ai nodi

I nodi del ruolo di lavoro sono una responsabilità condivisa e sono di proprietà dei clienti. Per questo motivo, i clienti hanno la possibilità di accedere ai propri nodi di lavoro e apportare modifiche potenzialmente dannose, ad esempio gli aggiornamenti del kernel e l'installazione o la rimozione di pacchetti.

Se i clienti apportano modifiche distruttive o fanno sì che i componenti del piano di controllo passino offline o diventino non funzionanti, AKS rileverà l'errore e ripristinerà automaticamente il nodo di lavoro allo stato di lavoro precedente.

Sebbene i clienti possano accedere e modificare i nodi del ruolo di lavoro, questa operazione è sconsigliata perché le modifiche possono rendere un cluster non supportato.

## <a name="network-ports-access-and-nsgs"></a>Porte di rete, accesso e gruppi

Come servizio gestito, AKS presenta requisiti specifici per la rete e la connettività. Questi requisiti sono meno flessibili rispetto ai requisiti per i componenti IaaS normali. In AKS, operazioni come la personalizzazione delle regole di NSG, il blocco di una porta specifica (ad esempio, l'uso di regole del firewall che bloccano la porta in uscita 443) e gli URL di inserimento nella whitelist possono rendere il cluster non supportato.

> [!NOTE]
> Attualmente, AKS non consente di bloccare completamente il traffico in uscita dal cluster. Per controllare l'elenco di URL e porte che il cluster può usare per il traffico in uscita, vedere [limitare il traffico in uscita](limit-egress-traffic.md).

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Funzionalità di Kubernetes alfa e beta non supportate

AKS supporta solo le funzionalità stabili all'interno del progetto Kubernetes upstream. Se non diversamente documentato, AKS non supporta le funzionalità Alpha e beta disponibili nel progetto Kubernetes upstream.

In due scenari, le funzionalità Alpha o beta potrebbero essere implementate prima che siano disponibili a livello generale:

* I clienti hanno incontrato i team del prodotto, del supporto o del reparto AKS e hanno chiesto di provare le nuove funzionalità.
* Queste funzionalità sono state [abilitate da un flag funzionalità](https://github.com/Azure/AKS/blob/master/previews.md). I clienti devono acconsentire esplicitamente a usare queste funzionalità.

## <a name="preview-features-or-feature-flags"></a>Funzionalità in anteprima o flag funzionalità

Per le funzionalità e le funzionalità che richiedono test estesi e commenti e suggerimenti degli utenti, Microsoft rilascia nuove funzionalità o funzionalità di anteprima dietro un flag funzionalità. Considerare queste funzionalità come funzionalità di versioni non definitive o beta.

Le funzionalità di anteprima o le funzionalità dei flag funzionalità non sono destinate alla produzione. Le modifiche in corso per le API e il comportamento, le correzioni di bug e altre modifiche possono causare instabilità in cluster e tempi di inattività.

Le funzionalità della versione di anteprima pubblica sono rientrate nel supporto "Best Effort" poiché queste funzionalità sono in anteprima e non sono destinate alla produzione e sono supportate dai team di supporto tecnico AKS solo durante l'orario di ufficio. Per ulteriori informazioni, vedere:

* [Domande frequenti relative al supporto tecnico Azure](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Le funzionalità di anteprima hanno effetto a livello di *sottoscrizione* di Azure. Non installare le funzionalità di anteprima in una sottoscrizione di produzione. In una sottoscrizione di produzione, le funzionalità di anteprima possono modificare il comportamento predefinito dell'API e influire sulle normali operazioni.

## <a name="upstream-bugs-and-issues"></a>Bug e problemi upstream

Data la velocità di sviluppo nel progetto Kubernetes upstream, si verificano invariabilmente i bug. Alcuni di questi bug non possono essere corretti o aggirati all'interno del sistema AKS. Al contrario, le correzioni di bug richiedono patch più grandi per i progetti upstream, ad esempio Kubernetes, i sistemi operativi node o Worker e i kernel. Per i componenti di proprietà di Microsoft (ad esempio il provider di servizi cloud di Azure), AKS e il personale di Azure si impegnano a correggere i problemi a Monte nella community.

Quando un problema di supporto tecnico è causato dalla radice da uno o più bug upstream, i team di supporto e di progettazione di AKS:

* Identificare e collegare i bug upstream con i dettagli di supporto per spiegare il motivo per cui il problema interessa il cluster o il carico di lavoro. I clienti ricevono i collegamenti ai repository necessari per poter controllare i problemi e vedere quando una nuova versione fornirà correzioni.
* Fornire soluzioni alternative o mitigazioni potenziali. Se il problema può essere risolto, nel repository AKS verrà archiviato un [problema noto](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) . La presentazione del problema noto spiega:
  * Il problema, inclusi i collegamenti ai bug upstream.
  * Soluzione alternativa e informazioni dettagliate su un aggiornamento o un'altra persistenza della soluzione.
  * Sequenze temporali approssimative per l'inclusione del problema in base alla cadenza a Monte.
