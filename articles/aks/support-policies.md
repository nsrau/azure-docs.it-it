---
title: Criteri di supporto del servizio Azure Kubernetes
description: Informazioni sui criteri di supporto del servizio Azure Kubernetes, sulla responsabilità condivisa e sulle funzionalità disponibili in anteprima (alpha o beta).
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: c51b5c4d9cd7362aeda0a0998d8031d0ba358ce1
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/27/2020
ms.locfileid: "89012348"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Criteri di supporto del servizio Azure Kubernetes

Questo articolo fornisce informazioni dettagliate sulle limitazioni e sui criteri del supporto tecnico per il servizio Azure Kubernetes (AKS). L'articolo descrive anche la gestione dei nodi di lavoro, i componenti di un piano di controllo gestito, i componenti open source di terze parti e la gestione della sicurezza o delle patch.

## <a name="service-updates-and-releases"></a>Versioni e aggiornamenti del servizio

* Per informazioni sulle versioni, vedere [AKS release notes](https://github.com/Azure/AKS/releases) (Note sulle versioni del servizio Azure Kubernetes).
* Per informazioni sulle funzionalità disponibili in anteprima, vedere [AKS Preview Features and Related Projects](https://awesomeopensource.com/projects/aks?categoryPage=11) (Funzionalità di anteprima del servizio Azure Kubernetes e progetti correlati).

## <a name="managed-features-in-aks"></a>Funzionalità gestite nel servizio Azure Kubernetes

I componenti cloud IaaS di base, come i componenti di calcolo o di rete, consentono agli utenti di accedere ai controlli e alle opzioni di personalizzazione di basso livello. Il servizio Azure Kubernetes, al contrario, fornisce una distribuzione Kubernetes chiavi in mano che offre ai clienti il set comune di configurazioni e funzionalità effettivamente necessarie. Non avendo accesso a tutte le funzionalità, anche per le fasi di distribuzione e personalizzazione, i clienti non devono preoccuparsi dei cluster Kubernetes, né gestirli direttamente.

Con il servizio Azure Kubernetes, i clienti possono usufruire di un *piano di controllo* completamente gestito, contenente tutti i componenti e i servizi necessari per usare i cluster Kubernetes e fornirli agli utenti finali. Tutti i componenti di Kubernetes vengono gestiti e controllati da Microsoft.

Microsoft gestisce e monitorizza i componenti seguenti tramite il riquadro di controllo:

* Server API Kubernetes o Kubelet
* ETCD o un archivio chiave-valore compatibile, che fornisce Qualità del servizio (QoS), scalabilità e runtime
* Servizi DNS (ad esempio, kube-dns o CoreDNS)
* Rete o proxy Kubernetes

Il servizio Azure Kubernetes non è una soluzione cluster completamente gestita. Per alcuni componenti, come i nodi di lavoro, è prevista la *responsabilità condivisa*, ovvero gli utenti devono contribuire alla gestione del cluster AKS. È necessario l'intervento dell'utente, ad esempio, per applicare una patch di sicurezza del sistema operativo al nodo di lavoro.

I servizi vengono *gestiti* nel senso che Microsoft e il team del servizio Azure Kubernetes distribuiscono i servizi e ne sono responsabili in termini di funzionamento e disponibilità. Ai clienti non è consentito modificare questi componenti gestiti. Microsoft limita gli interventi di personalizzazione per garantire un'esperienza d'uso coerente e scalabile. Per una soluzione completamente personalizzabile, vedere [AKS Engine](https://github.com/Azure/aks-engine).

## <a name="shared-responsibility"></a>Responsabilità condivisa

Quando viene creato un cluster, il cliente definisce i nodi di lavoro di Kubernetes creati dal servizio Azure Kubernetes. Su questi nodi vengono eseguiti i carichi di lavoro dei clienti, che sono proprietari dei nodi di lavoro e possono visualizzarli o modificarli.

Poiché i nodi del cluster dei clienti eseguono codice privato e archiviano dati sensibili, il supporto tecnico Microsoft può accedervi solo in modo limitato. Il personale del supporto tecnico Microsoft non può accedere a questi nodi, né eseguire comandi su di essi o visualizzarne i log senza espressa richiesta di assistenza o autorizzazione dei clienti.

Poiché i nodi di lavoro sono riservati, Microsoft presta molta attenzione a limitare le attività di gestione in background. In molti casi, il carico di lavoro continuerà a essere eseguito anche in caso di errore nei nodi master di Kubernetes, in etcd o in altri componenti gestiti da Microsoft. Se si modifica un nodo del ruolo di lavoro senza attenzione, è possibile che si verifichi una perdita di dati e che il cluster non venga più supportato.

## <a name="aks-support-coverage"></a>Copertura del supporto per il servizio Azure Kubernetes

Microsoft fornisce supporto tecnico per gli elementi seguenti:

> [!NOTE]
> Qualsiasi azione del cluster eseguita da Microsoft/AKS viene effettuata con il consenso dell'utente con un ruolo predefinito di Kubernetes `aks-service` e un'associazione di ruolo incorporata `aks-service-rolebinding` . Questo ruolo consente ad AKS di risolvere e diagnosticare i problemi del cluster, ma non di modificare le autorizzazioni né di creare ruoli o associazioni di ruolo o altre azioni con privilegi elevati. L'accesso ai ruoli viene abilitato solo in ticket di supporto attivi con accesso just-in-time (JIT).

* Connettività a tutti i componenti di Kubernetes forniti e supportati dal servizio Kubernetes, tra cui il server API.
* Gestione, tempo di attività, Qualità del Servizio e operazioni dei servizi del piano di controllo Kubernetes (ad esempio, nodi master Kubernetes, server API, etcd e kube-dns).
* Etcd. Il supporto include backup trasparenti e automatizzati di tutti i dati etcd ogni 30 minuti per la pianificazione di emergenza e il ripristino dello stato del cluster. Questi backup non sono direttamente disponibili per i clienti o gli utenti; garantiscono tuttavia l'affidabilità e la coerenza dei dati. Etcd. il rollback o il ripristino su richiesta non è supportato come funzionalità.
* Eventuali punti di integrazione nel driver del provider di servizi cloud di Azure per Kubernetes. Sono incluse anche eventuali integrazioni in altri servizi di Azure, quali servizi di bilanciamento del carico, volumi permanenti o connessione di rete (Kubernetes e Azure CNI).
* Domande o problemi relativi alla personalizzazione dei componenti del piano di controllo, tra cui il server API Kubernetes, etcd e kube-dns.
* Problemi relativi alla connessione di rete, ad esempio Azure CNI, kubenet o altri problemi di accesso o funzionalità di rete. I problemi possono includere, ad esempio, la risoluzione DNS, la perdita di pacchetti, il routing e così via. Microsoft supporta vari scenari di rete:
  * Connessioni di rete Kubenet (base) e avanzate (Azure CNI) all'interno del cluster e dei componenti associati
  * Connettività ad altri servizi e applicazioni di Azure
  * Controller di ingresso e configurazioni di ingresso o del bilanciamento del carico
  * Prestazioni e latenza di rete

Microsoft non fornisce supporto tecnico per gli elementi seguenti:

* Domande su come usare Kubernetes. Il personale del supporto tecnico Microsoft, ad esempio, non fornisce consigli su come creare controller di ingresso personalizzati, usare i carichi di lavoro delle applicazioni o applicare strumenti o pacchetti software open source o di terze parti.
  > [!NOTE]
  > Il supporto tecnico Microsoft può fornire consigli sul funzionamento, sulla personalizzazione e sull'ottimizzazione del cluster del servizio Azure Kubernetes (ad esempio, su procedure e problemi relativi al funzionamento di Kubernetes).
* Progetti open source di terze parti che non forniti nell'ambito del piano di controllo Kubernetes o distribuiti con cluster del servizio Azure Kubernetes. Questi progetti possono includere Istio, Helm, Envoy o altri.
  > [!NOTE]
  > Microsoft può offrire il miglior supporto possibile per progetti open source di terze parti, quali Helm e Kured. Quando lo strumento open source di terze parti si integra con il provider di servizi cloud di Azure Kubernetes o altri bug specifici del servizio Kubernetes di Azure, Microsoft supporta esempi e applicazioni della documentazione Microsoft.
* Software di terze parti con codice sorgente chiuso. Questo software può includere strumenti di analisi della sicurezza e software o dispositivi di connessione in rete.
* Problemi relativi a buildout multicloud o multifornitore. Microsoft, ad esempio, non supporta problemi correlati all'esecuzione di una soluzione multipubblico federata di un fornitore di servizi cloud.
* Personalizzazioni di rete diverse da quelle elencate nella [documentazione AKS](./index.yml).
  > [!NOTE]
  > Microsoft supporta problemi e bug correlati ai gruppi di sicurezza di rete (NSG). Il personale del supporto tecnico Microsoft, ad esempio, può rispondere a domande relative a un errore di un gruppo di sicurezza di rete da aggiornare o a un comportamento imprevisto di un gruppo di sicurezza di rete o del servizio di bilanciamento del carico.

## <a name="aks-support-coverage-for-worker-nodes"></a>Copertura del supporto del servizio Azure Kubernetes per i nodi di lavoro

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Responsabilità di Microsoft per i nodi di lavoro del servizio Azure Kubernetes

Microsoft e i clienti condividono la responsabilità dei nodi di lavoro di Kubernetes nei casi seguenti:

* All'immagine di base del sistema operativo sono stati aggiunti elementi obbligatori (ad esempio, agenti di monitoraggio e di connessione di rete).
* I nodi di lavoro ricevono automaticamente le patch del sistema operativo.
* I problemi relativi ai componenti del piano di controllo Kubernetes eseguiti nei nodi di lavoro vengono corretti automaticamente. Questi componenti includono:
  * Kube-proxy
  * Tunnel di rete che forniscono percorsi di comunicazione ai componenti master di Kubernetes
  * Kubelet
  * Daemon Docker o Moby

> [!NOTE]
> In un nodo del ruolo di lavoro, se un componente del piano di controllo non è operativo, è possibile che il team del servizio Kubernetes debba riavviare singoli componenti o l'intero nodo di lavoro. Queste operazioni di riavvio vengono automatizzate e forniscono una correzione automatica per i problemi comuni. Vengono eseguite solo a livello di _nodo_ e non di cluster, a meno che non si sia verificata un'interruzione del servizio o non sia necessario un intervento di manutenzione di emergenza.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Responsabilità del cliente per i nodi di lavoro del servizio Azure Kubernetes

Microsoft non esegue il riavvio automatico dei nodi di lavoro per applicare patch a livello di sistema operativo. Anche se le patch del sistema operativo vengono recapitate ai nodi di lavoro, infatti, è il *cliente* responsabile di riavviarli per applicare le modifiche. Le patch vengono distribuite automaticamente anche nelle librerie condivise, in daemon come i dischi SSHD (Solid-State Hybrid Drive) e in altri componenti a livello di sistema o sistema operativo.

I clienti sono inoltre responsabili di eseguire gli aggiornamenti di Kubernetes. Possono effettuare questa operazione tramite il pannello di controllo di Azure o l'interfaccia della riga di comando di Azure. Questo principio vale per gli aggiornamenti che contengono miglioramenti a Kubernetes in termini di sicurezza o funzionamento.

#### <a name="user-customization-of-worker-nodes"></a>Personalizzazione dei nodi di lavoro da parte degli utenti
> [!NOTE]
> I nodi di lavoro del servizio Azure Kubernetes vengono visualizzati nel portale di Azure come normali risorse IaaS di Azure. Si tratta tuttavia di macchine virtuali distribuite in un gruppo di risorse personalizzato di Azure (caratterizzato dal prefisso MC\\*). È possibile estendere i nodi di lavoro del servizio Azure Kubernetes rispetto alle configurazioni di base. Ricorrendo a Secure Shell (SSH), ad esempio, è possibile modificare i nodi di lavoro del servizio Azure Kubernetes nello stesso modo in cui si modificano le normali macchine virtuali. Non è possibile, tuttavia, modificare l'immagine del sistema operativo di base. Eventuali modifiche personalizzate potrebbero essere annullate caso di aggiornamento, ridimensionamento, aggiornamento o riavvio. Se, **tuttavia**, si apportano modifiche *fuori banda e fuori ambito all'API del servizio Azure Kubernetes*, il cluster del servizio Azure Kubernetes non viene più supportato. Evitare quindi di modificare i nodi di lavoro a meno di non ricevere istruzioni specifiche da parte del team del supporto tecnico Microsoft.

L'esecuzione di operazioni non supportate, come la deallocazione fuori banda di tutti i nodi agente, annulla infatti il servizio di supporto del cluster. Il servizio Azure Kubernetes si riserva il diritto di archiviare i piani di controllo che non sono stati configurati nel rispetto delle linee guida del servizio di supporto per un periodo pari o superiore a 30 giorni. Il servizio Azure Kubernetes mantiene inoltre le copie di backup dei metadati etcd del cluster che consentono di riallocare immediatamente il cluster. Questa riallocazione può essere avviata da qualsiasi operazione PUT che ripristina il servizio di supporto del cluster, ad esempio l'aggiornamento o il ridimensionamento di nodi agente attivi.

Il servizio Azure Kubernetes gestisce il ciclo di vita e le operazioni dei nodi di lavoro per conto dei clienti; la modifica delle risorse IaaS associate ai nodi di lavoro non è **supportata**. Un'operazione non supportata, ad esempio, è la personalizzazione di un set di scalabilità di macchine virtuali del pool di nodi modificando manualmente le configurazioni del set di scalabilità di macchine virtuali tramite il portale o l'API di riferimento.
 
Per configurazioni o pacchetti specifici del carico di lavoro, il servizio Azure Kubernetes consiglia di usare [Oggetti DaemonSet di Kubernetes](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/).

L'uso di daemonset di Kubernetes con privilegi e di contenitori init consente ai clienti di ottimizzare/modificare o installare software di terze parti nei nodi di lavoro del cluster. Questo tipo di personalizzazione include, ad esempio, l'integrazione di strumenti di analisi della sicurezza personalizzati o l'aggiornamento delle impostazioni di sysctl.

Sebbene si tratti di un percorso consigliato nel caso in cui siano soddisfatti i requisiti indicati in precedenza, il servizio di progettazione e supporto di Azure Kubernetes non può fornire assistenza per la risoluzione o la diagnosi di problemi generati da modifiche interrotte o non funzionali o dalla distribuzione di un daemonset del cliente che rende il nodo non disponibile.

> [!NOTE]
> In qualità di *servizio gestito*, ad Azure Kubernetes vengono assegnati degli obiettivi, come la rimozione delle responsabilità per patch, aggiornamenti e raccolta di log, in modo da rendere la gestione del servizio più completa e automatica. Con la crescente capacità del servizio in fatto di gestione end-to-end, è possibile che nelle versioni future vengano omesse alcune funzioni (ad esempio, il riavvio dei nodi e l'applicazione automatica delle patch).

### <a name="security-issues-and-patching"></a>Problemi di sicurezza e applicazione di patch

Se viene rilevato un errore di sicurezza in uno o più componenti del servizio Azure Kubernetes, il team del servizio applicherà una patch a tutti i cluster interessati per attenuare il problema. In alternativa, il team fornirà agli utenti indicazioni precise per eseguire l'aggiornamento.

Per i nodi di lavoro interessati da un errore di sicurezza, se è disponibile una patch che non richiede tempi di inattività, il team del servizio Azure Kubernetes applicherà quella patch e ne informerà gli utenti.

Se una patch di sicurezza richiede il riavvio dei nodi di lavoro, Microsoft comunicherà questo requisito ai clienti, che avranno la responsabilità di eseguire il riavvio o l'aggiornamento per rendere effettiva la patch del cluster. Se gli utenti non applicano le patch secondo le indicazioni del servizio Azure Kubernetes, il cluster continuerà a essere vulnerabile al problema di sicurezza correlato.

### <a name="node-maintenance-and-access"></a>Accesso e gestione dei nodi

I nodi di lavoro sono di proprietà dei clienti ma presuppongono una responsabilità condivisa. Per questo motivo, i clienti hanno la possibilità di accedere ai propri nodi di lavoro e apportare modifiche potenzialmente dannose, come l'aggiornamento del kernel e l'installazione o la rimozione di pacchetti.

Se un cliente apporta modifiche distruttive o rende offline o non funzionante un componente del piano di controllo, il servizio Azure Kubernetes rileverà l'errore e ripristinerà automaticamente il nodo di lavoro all'ultimo stato di funzionamento valido.

Sebbene i clienti possano accedere e modificare i nodi di lavoro, questa operazione è sconsigliata perché può annullare il supporto del cluster.

## <a name="network-ports-access-and-nsgs"></a>Porte di rete, accesso e gruppi di sicurezza di rete

Come servizio gestito, Azure Kubernetes presenta specifici requisiti di connettività, meno flessibili rispetto ai requisiti dei normali componenti IaaS. In AKS, operazioni come la personalizzazione delle regole di NSG, il blocco di una porta specifica (ad esempio, l'uso di regole firewall che bloccano la porta in uscita 443) e l'aggiunta di URL a un elenco di accesso consentito possono rendere il cluster non supportato.

> [!NOTE]
> Attualmente, il servizio Azure Kubernetes non consente di bloccare completamente il traffico in uscita dal cluster. Per controllare l'elenco di URL e porte che il cluster può usare per il traffico in uscita, vedere l'argomento su come [limitare il traffico in uscita](limit-egress-traffic.md).

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Funzionalità alfa e beta di Kubernetes non supportate

Il servizio Azure Kubernetes supporta solo funzionalità stabili all'interno del progetto Kubernetes upstream. Se non diversamente documentato, il servizio Azure Kubernetes non supporta le funzionalità alfa e beta disponibili nel progetto Kubernetes upstream.

Solo in due scenari è consentita l'implementazione di funzionalità alpha o beta prima della loro disponibilità generale:

* Clienti che hanno incontrato i team di supporto, di prodotto o di progettazione del servizio Azure Kubernetes e hanno chiesto di provare le nuove funzionalità.
* Queste funzionalità vengono [abilitate da un flag di funzionalità](https://awesomeopensource.com/projects/aks?categoryPage=11). I clienti devono acconsentire esplicitamente all'uso di queste funzionalità.

## <a name="preview-features-or-feature-flags"></a>Funzionalità in anteprima o flag di funzionalità

Per le funzionalità con esigenze maggiori in termini di test e feedback degli utenti, Microsoft rilascia le nuove funzionalità in anteprima o contrassegnate da un flag di funzionalità. Queste funzionalità devono essere quindi considerate funzionalità beta o preliminari.

Le funzionalità di anteprima o con flag di funzionalità non sono destinate alla produzione. Le modifiche in corso nelle API e le modifiche a livello di comportamento, correzioni di bug e di altro tipo possono determinare tempi di inattività e instabilità nei cluster.

Le funzionalità in versione di anteprima pubblica rientrano nella categoria "miglior supporto possibile" poiché non sono destinate alla produzione e sono supportate dai team di supporto tecnico di Azure Kubernetes solo durante l'orario di ufficio. Per altre informazioni, vedere:

* [Domande frequenti relative al supporto tecnico Azure](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Le funzionalità di anteprima entrano in vigore a livello di *sottoscrizione* di Azure. Non installare quindi alcuna funzionalità di anteprima in una sottoscrizione di produzione. In una sottoscrizione di produzione, le funzionalità di anteprima possono modificare il comportamento predefinito delle API e influire sulle normali operazioni.

## <a name="upstream-bugs-and-issues"></a>Bug e problemi upstream

Considerata la velocità di sviluppo nel progetto Kubernetes upstream, si verificano invariabilmente alcuni bug, alcuni dei quali non possono essere ovviati o corretti all'interno del sistema Azure Kubernetes. Per correggere i bug sono infatti necessarie patch più grandi nei progetti upstream (ad esempio, Kubernetes, i sistemi operativi di lavoro o dei nodi e i kernel). Per i componenti di proprietà di Microsoft (ad esempio, il provider di servizi cloud di Azure), il personale di Azure e del servizio Azure Kubernetes si impegna a correggere i problemi upstream che si verificano nell'ambito della community.

Quando un problema di supporto tecnico è causato alla radice da uno o più bug upstream, i team di supporto e di progettazione del servizio Azure Container procedono come segue:

* Identificano e associano i bug upstream con eventuali dettagli di supporto per spiegare il motivo per cui il problema interessa il cluster o il carico di lavoro. I clienti ricevono i collegamenti ai repository necessari per poter esaminare i problemi e scoprire in quale versione futura verrà resa disponibile una correzione.
* Forniscono possibili soluzioni alternative o di mitigazione. Se il problema può essere risolto, verrà segnalato un [problema noto](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) nel repository del servizio Azure Kubernetes. Nella segnalazione di un problema noto vengono spiegati gli elementi seguenti:
  * Il problema, inclusi i collegamenti ai bug upstream.
  * La soluzione alternativa e informazioni dettagliate su un aggiornamento o un'altra persistenza della soluzione.
  * Le tempistiche approssimative per l'inclusione del problema, in base alla frequenza delle versioni upstream.
