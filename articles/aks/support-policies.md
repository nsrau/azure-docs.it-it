---
title: Criteri di supporto del servizio Azure Kubernetes
description: Informazioni sui criteri di supporto del servizio Azure Kubernetes, sulla responsabilità condivisa e sulle funzionalità disponibili in anteprima (alpha o beta).
services: container-service
ms.topic: article
ms.date: 09/18/2020
ms.openlocfilehash: 188416d9ef6f8e7568b10e8ccbb405be0bff315d
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90888976"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Criteri di supporto del servizio Azure Kubernetes

Questo articolo fornisce informazioni dettagliate sulle limitazioni e sui criteri del supporto tecnico per il servizio Azure Kubernetes (AKS). L'articolo descrive anche la gestione dei nodi dell'agente, i componenti del piano di controllo gestito, i componenti open source di terze parti e la gestione della sicurezza o delle patch.

## <a name="service-updates-and-releases"></a>Versioni e aggiornamenti del servizio

* Per informazioni sulle versioni, vedere [AKS release notes](https://github.com/Azure/AKS/releases) (Note sulle versioni del servizio Azure Kubernetes).
* Per informazioni sulle funzionalità disponibili in anteprima, vedere [AKS Preview Features and Related Projects](https://awesomeopensource.com/projects/aks?categoryPage=11) (Funzionalità di anteprima del servizio Azure Kubernetes e progetti correlati).

## <a name="managed-features-in-aks"></a>Funzionalità gestite nel servizio Azure Kubernetes

I componenti cloud di base dell'infrastruttura distribuita come servizio (IaaS), ad esempio i componenti di calcolo o di rete, consentono di accedere ai controlli di basso livello e alle opzioni di personalizzazione. Al contrario, AKS fornisce una distribuzione Kubernetes chiavi in mano che offre il set comune di configurazioni e funzionalità necessarie per il cluster. In qualità di utente AKS, sono disponibili opzioni di personalizzazione e di distribuzione limitate. In Exchange non è necessario preoccuparsi o gestire direttamente i cluster Kubernetes.

Con AKS si ottiene un *piano di controllo*completamente gestito. Il piano di controllo contiene tutti i componenti e i servizi necessari per operare e fornire cluster Kubernetes agli utenti finali. Tutti i componenti di Kubernetes vengono gestiti e controllati da Microsoft.

Microsoft gestisce e monitorizza i componenti seguenti tramite il riquadro di controllo:

* Server API Kubernetes o Kubelet
* ETCD o un archivio chiave-valore compatibile, che fornisce Qualità del servizio (QoS), scalabilità e runtime
* Servizi DNS (ad esempio, kube-dns o CoreDNS)
* Rete o proxy Kubernetes
* Qualsiasi addon aggiuntivo o componente di sistema in esecuzione nello spazio dei nomi Kube-System

AKS non è una soluzione di piattaforma distribuita come servizio (PaaS). Alcuni componenti, ad esempio i nodi agente, hanno *responsabilità condivise*, in cui gli utenti devono contribuire a gestire il cluster AKS. L'input dell'utente è obbligatorio, ad esempio, per applicare una patch di sicurezza del sistema operativo del nodo agente (sistema operativo).

I servizi vengono *gestiti* nel senso che Microsoft e il team del servizio Azure Kubernetes distribuiscono i servizi e ne sono responsabili in termini di funzionamento e disponibilità. Ai clienti non è consentito modificare questi componenti gestiti. Microsoft limita gli interventi di personalizzazione per garantire un'esperienza d'uso coerente e scalabile. Per una soluzione completamente personalizzabile, vedere [AKS Engine](https://github.com/Azure/aks-engine).

## <a name="shared-responsibility"></a>Responsabilità condivisa

Quando viene creato un cluster, si definiscono i nodi dell'agente Kubernetes creati da AKS. I carichi di lavoro vengono eseguiti in questi nodi.

Poiché i nodi dell'agente eseguono codice privato e archiviano dati sensibili, supporto tecnico Microsoft possibile accedervi solo in modo molto limitato. Supporto tecnico Microsoft non può accedere a, eseguire comandi in o visualizzare i log per questi nodi senza l'autorizzazione o l'assistenza rapida.

Tutte le modifiche apportate direttamente ai nodi Agent usando una delle API di IaaS eseguono il rendering del cluster come non supportato. Tutte le modifiche apportate ai nodi dell'agente devono essere eseguite utilizzando meccanismi nativi kubernetes come `Daemon Sets` .

Analogamente, anche se è possibile aggiungere metadati al cluster e ai nodi, ad esempio tag ed etichette, la modifica di uno dei metadati creati dal sistema rende il cluster non supportato.

## <a name="aks-support-coverage"></a>Copertura del supporto per il servizio Azure Kubernetes

Microsoft fornisce supporto tecnico per gli esempi seguenti:

* Connettività a tutti i componenti di Kubernetes forniti e supportati dal servizio Kubernetes, tra cui il server API.
* Gestione, tempo di attività, QoS e operazioni dei servizi del piano di controllo di Kubernetes (ad esempio, il piano di controllo Kubernetes, il server API, ETCD e coreDNS).
* Archivio dati ETCD. Il supporto include backup trasparenti e automatizzati di tutti i dati etcd ogni 30 minuti per la pianificazione di emergenza e il ripristino dello stato del cluster. Questi backup non sono direttamente disponibili per l'utente o per tutti gli utenti. garantiscono tuttavia l'affidabilità e la coerenza dei dati. Etcd. il rollback o il ripristino su richiesta non è supportato come funzionalità.
* Eventuali punti di integrazione nel driver del provider di servizi cloud di Azure per Kubernetes. Sono incluse anche eventuali integrazioni in altri servizi di Azure, quali servizi di bilanciamento del carico, volumi permanenti o connessione di rete (Kubernetes e Azure CNI).
* Domande o problemi relativi alla personalizzazione dei componenti del piano di controllo, ad esempio il server API Kubernetes, ETCD e coreDNS.
* Problemi relativi alla connessione di rete, ad esempio Azure CNI, kubenet o altri problemi di accesso o funzionalità di rete. I problemi possono includere, ad esempio, la risoluzione DNS, la perdita di pacchetti, il routing e così via. Microsoft supporta vari scenari di rete:
  * Kubenet e Azure CNI con reti virtuali gestiti o con subnet personalizzate (Bring Your Own).
  * Connettività ad altri servizi e applicazioni di Azure
  * Controller di ingresso e configurazioni di ingresso o del bilanciamento del carico
  * Prestazioni e latenza di rete


> [!NOTE]
> Qualsiasi azione del cluster eseguita da Microsoft/AKS viene effettuata con il consenso dell'utente con un ruolo predefinito di Kubernetes `aks-service` e un'associazione di ruolo incorporata `aks-service-rolebinding` . Questo ruolo consente ad AKS di risolvere e diagnosticare i problemi del cluster, ma non di modificare le autorizzazioni né di creare ruoli o associazioni di ruolo o altre azioni con privilegi elevati. L'accesso ai ruoli viene abilitato solo in ticket di supporto attivi con accesso just-in-time (JIT).

Microsoft non fornisce supporto tecnico per gli esempi seguenti:

* Domande su come usare Kubernetes. Il personale del supporto tecnico Microsoft, ad esempio, non fornisce consigli su come creare controller di ingresso personalizzati, usare i carichi di lavoro delle applicazioni o applicare strumenti o pacchetti software open source o di terze parti.
  > [!NOTE]
  > Il supporto tecnico Microsoft può fornire consigli sul funzionamento, sulla personalizzazione e sull'ottimizzazione del cluster del servizio Azure Kubernetes (ad esempio, su procedure e problemi relativi al funzionamento di Kubernetes).
* Progetti open source di terze parti che non forniti nell'ambito del piano di controllo Kubernetes o distribuiti con cluster del servizio Azure Kubernetes. Questi progetti possono includere Istio, Helm, Envoy o altri.
  > [!NOTE]
  > Microsoft può fornire supporto per il massimo sforzo per progetti open source di terze parti, ad esempio Helm. Quando lo strumento open source di terze parti si integra con il provider di servizi cloud di Azure Kubernetes o altri bug specifici del servizio Kubernetes di Azure, Microsoft supporta esempi e applicazioni della documentazione Microsoft.
* Software di terze parti con codice sorgente chiuso. Questo software può includere strumenti di analisi della sicurezza e software o dispositivi di connessione in rete.
* Personalizzazioni di rete diverse da quelle elencate nella [documentazione di AKS](./index.yml).


## <a name="aks-support-coverage-for-agent-nodes"></a>Copertura del supporto AKS per i nodi agente

### <a name="microsoft-responsibilities-for-aks-agent-nodes"></a>Responsabilità di Microsoft per i nodi dell'agente AKS

Microsoft e gli utenti condividono la responsabilità dei nodi Kubernetes Agent in cui:

* All'immagine di base del sistema operativo sono stati aggiunti elementi obbligatori (ad esempio, agenti di monitoraggio e di connessione di rete).
* I nodi agente ricevono automaticamente le patch del sistema operativo.
* I problemi relativi ai componenti del piano di controllo Kubernetes eseguiti nei nodi dell'agente vengono corretti automaticamente. Questi componenti includono i seguenti:
  * `Kube-proxy`
  * Tunnel di rete che forniscono percorsi di comunicazione ai componenti master di Kubernetes
  * `Kubelet`
  * `Moby` o `ContainerD`

> [!NOTE]
> Se un nodo dell'agente non è operativo, AKS potrebbe riavviare i singoli componenti o l'intero nodo dell'agente. Queste operazioni di riavvio vengono automatizzate e forniscono la correzione automatica per i problemi comuni. Per altre informazioni sui meccanismi di correzione automatica, vedere [Ripristino automatico del nodo](node-auto-repair.md)

### <a name="customer-responsibilities-for-aks-agent-nodes"></a>Responsabilità del cliente per i nodi dell'agente AKS

Microsoft fornisce patch e nuove immagini per i nodi immagine settimanalmente, ma non le applica automaticamente per impostazione predefinita. Per fare in modo che i componenti del sistema operativo e del sistema operativo del nodo dell'agente vengano corretti, è consigliabile tenere una pianificazione di [aggiornamento dell'immagine del nodo](node-image-upgrade.md) normale o automatizzarla.

Analogamente, AKS rilascia periodicamente nuove patch kubernetes e versioni secondarie. Questi aggiornamenti possono contenere miglioramenti alla sicurezza o alle funzionalità per Kubernetes. Si è tenuti a mantenere aggiornata la versione di kubernetes del cluster e in base ai [criteri della versione del supporto AKS kubernetes](supported-kubernetes-versions.md).

#### <a name="user-customization-of-agent-nodes"></a>Personalizzazione utente dei nodi agente
> [!NOTE]
> I nodi dell'agente AKS vengono visualizzati nel portale di Azure come normali risorse IaaS di Azure. Tuttavia, queste macchine virtuali vengono distribuite in un gruppo di risorse di Azure personalizzato, in genere preceduto da MC_ \* . Non è possibile modificare l'immagine del sistema operativo di base o eseguire qualsiasi personalizzazione diretta a questi nodi usando le API o le risorse di IaaS. Eventuali modifiche personalizzate che non vengono eseguite tramite l'API AKS non verranno mantenute durante l'aggiornamento, la scalabilità, l'aggiornamento o il riavvio. Evitare di apportare modifiche ai nodi dell'agente a meno che supporto tecnico Microsoft non consentirà di apportare modifiche.

AKS gestisce il ciclo di vita e le operazioni dei nodi dell'agente per conto dell'utente: la modifica delle risorse IaaS associate ai nodi dell'agente **non è supportata**. Un esempio di operazione non supportata è la personalizzazione di un set di scalabilità di macchine virtuali del pool di nodi modificando manualmente le configurazioni tramite il portale o l'API del set di scalabilità di macchine virtuali.
 
Per le configurazioni o i pacchetti specifici del carico di lavoro, AKS consiglia di usare [Kubernetes `daemon sets` ](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/).

L'uso di Kubernetes Privileged `daemon sets` e dei contenitori init consente di ottimizzare o modificare o installare software di terze parti nei nodi dell'agente cluster. Questo tipo di personalizzazione include, ad esempio, l'integrazione di strumenti di analisi della sicurezza personalizzati o l'aggiornamento delle impostazioni di sysctl.

Questo percorso è consigliato se si applicano i requisiti indicati in precedenza, il servizio di progettazione e il supporto di AKS non possono supportare la risoluzione dei problemi o la diagnosi delle modifiche che rendono il nodo non disponibile a causa di una distribuzione personalizzata `daemon set` .

### <a name="security-issues-and-patching"></a>Problemi di sicurezza e applicazione di patch

Se viene rilevato un errore di sicurezza in uno o più dei componenti gestiti di AKS, il team di AKS patcherà tutti i cluster interessati per attenuare il problema. In alternativa, il team fornirà agli utenti indicazioni precise per eseguire l'aggiornamento.

Per i nodi agente interessati da un problema di sicurezza, Microsoft invierà una notifica con i dettagli sull'impatto e i passaggi per correggere o attenuare il problema di sicurezza (in genere un aggiornamento di un'immagine del nodo o un aggiornamento della patch del cluster).

### <a name="node-maintenance-and-access"></a>Accesso e gestione dei nodi

Sebbene sia possibile accedere a e modificare i nodi dell'agente, questa operazione è sconsigliata perché le modifiche possono rendere un cluster non supportato.

## <a name="network-ports-access-and-nsgs"></a>Porte di rete, accesso e gruppi di sicurezza di rete

È possibile personalizzare gruppi solo nelle subnet personalizzate. Non è possibile personalizzare gruppi nelle subnet gestite o al livello NIC dei nodi dell'agente. AKS presenta requisiti in uscita per endpoint specifici, per controllare l'uscita e garantire la connettività necessaria. vedere [limitare il traffico in uscita](limit-egress-traffic.md).

## <a name="stopped-or-de-allocated-clusters"></a>Cluster interrotti o deallocati

Come descritto in precedenza, la deallocazione manuale di tutti i nodi del cluster tramite l'API/CLI/portale di IaaS rende il cluster non supportato.
L'unico modo supportato per arrestare/deallocare tutti i nodi è quello di [arrestare il cluster AKS, che conserverà lo stato del cluster per un massimo di 12 mesi.

I cluster interrotti per più di 12 mesi non conserveranno più lo stato. 

I cluster deallocati al di fuori delle API AKS non hanno garanzie di mantenimento dello stato. I piani di controllo per i cluster in questo stato verranno archiviati dopo 30 giorni ed eliminati dopo 12 mesi.

Il servizio Azure Kubernetes si riserva il diritto di archiviare i piani di controllo che non sono stati configurati nel rispetto delle linee guida del servizio di supporto per un periodo pari o superiore a 30 giorni. Il servizio Azure Kubernetes mantiene inoltre le copie di backup dei metadati etcd del cluster che consentono di riallocare immediatamente il cluster. Questa riallocazione può essere avviata da qualsiasi operazione PUT che ripristina il servizio di supporto del cluster, ad esempio l'aggiornamento o il ridimensionamento di nodi agente attivi.

Se la sottoscrizione viene sospesa o eliminata, il piano di controllo e lo stato del cluster verranno eliminati dopo 90 giorni.

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Funzionalità alfa e beta di Kubernetes non supportate

AKS supporta solo funzionalità stabili e beta all'interno del progetto Kubernetes upstream. Se non diversamente documentato, AKS non supporta alcuna funzionalità Alpha disponibile nel progetto Kubernetes upstream.

## <a name="preview-features-or-feature-flags"></a>Funzionalità in anteprima o flag di funzionalità

Per le funzionalità e le funzionalità che richiedono test estesi e commenti e suggerimenti degli utenti, Microsoft rilascia nuove funzionalità o funzionalità di anteprima dietro un flag funzionalità. Queste funzionalità devono essere quindi considerate funzionalità beta o preliminari.

Le funzionalità di anteprima o con flag di funzionalità non sono destinate alla produzione. Le modifiche in corso nelle API e le modifiche a livello di comportamento, correzioni di bug e di altro tipo possono determinare tempi di inattività e instabilità nei cluster.

Le funzionalità in versione di anteprima pubblica rientrano nella categoria "miglior supporto possibile" poiché non sono destinate alla produzione e sono supportate dai team di supporto tecnico di Azure Kubernetes solo durante l'orario di ufficio. Per altre informazioni, vedere:

* [Domande frequenti relative al supporto tecnico Azure](https://azure.microsoft.com/support/faq/)

## <a name="upstream-bugs-and-issues"></a>Bug e problemi upstream

Considerata la velocità di sviluppo nel progetto Kubernetes upstream, si verificano invariabilmente alcuni bug, alcuni dei quali non possono essere ovviati o corretti all'interno del sistema Azure Kubernetes. Al contrario, le correzioni di bug richiedono patch più grandi per i progetti upstream, ad esempio Kubernetes, i sistemi operativi node o Agent e il kernel. Per i componenti di proprietà di Microsoft (ad esempio, il provider di servizi cloud di Azure), il personale di Azure e del servizio Azure Kubernetes si impegna a correggere i problemi upstream che si verificano nell'ambito della community.

Quando un problema di supporto tecnico è causato alla radice da uno o più bug upstream, i team di supporto e di progettazione del servizio Azure Container procedono come segue:

* Identificano e associano i bug upstream con eventuali dettagli di supporto per spiegare il motivo per cui il problema interessa il cluster o il carico di lavoro. I clienti ricevono i collegamenti ai repository necessari per poter esaminare i problemi e scoprire in quale versione futura verrà resa disponibile una correzione.
* Fornire potenziali soluzioni alternative o mitigazione. Se il problema può essere risolto, verrà segnalato un [problema noto](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) nel repository del servizio Azure Kubernetes. Nella segnalazione di un problema noto vengono spiegati gli elementi seguenti:
  * Il problema, inclusi i collegamenti ai bug upstream.
  * La soluzione alternativa e informazioni dettagliate su un aggiornamento o un'altra persistenza della soluzione.
  * Le tempistiche approssimative per l'inclusione del problema, in base alla frequenza delle versioni upstream.
