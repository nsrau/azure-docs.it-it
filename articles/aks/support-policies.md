---
title: Criteri di supporto per il servizio Azure Kubernetes (AKS)Support policies for Azure Kubernetes Service (AKS)
description: Informazioni sui criteri di supporto, sulla responsabilità condivisa e sulle funzionalità dell'anteprima (o alfa o beta) del servizio Azure Kubernetes.
services: container-service
author: jnoller
ms.topic: article
ms.date: 01/24/2020
ms.author: jenoller
ms.openlocfilehash: a5d90106a85a61cbf499c4c08130392b922a45f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593581"
---
# <a name="support-policies-for-azure-kubernetes-service"></a>Criteri di supporto per il servizio Azure KubernetesSupport policies for Azure Kubernetes Service

Questo articolo fornisce informazioni dettagliate sui criteri di supporto tecnico e sulle limitazioni per il servizio Azure Kubernetes Service (AKS). L'articolo descrive anche la gestione dei nodi di lavoro, i componenti del piano di controllo gestito, i componenti open source di terze parti e la sicurezza o la gestione delle patch.

## <a name="service-updates-and-releases"></a>Aggiornamenti e versioni dei serviziService updates and releases

* Per informazioni sulla versione, vedere Note sulla versione di [AKS](https://github.com/Azure/AKS/releases).
* Per informazioni sulle feature in anteprima, consultate Feature di [anteprima Di AKS e progetti correlati.](https://github.com/Azure/AKS/blob/master/previews.md)

## <a name="managed-features-in-aks"></a>Funzionalità gestite in AKS

I componenti cloud di base dell'infrastruttura come servizio (IaaS), ad esempio i componenti di elaborazione o di rete, consentono agli utenti di accedere a controlli di basso livello e opzioni di personalizzazione. Al contrario, AKS fornisce una distribuzione Kubernetes chiavi in mano che offre ai clienti il set comune di configurazioni e funzionalità necessarie. I clienti AKS hanno opzioni limitate di personalizzazione, distribuzione e altre opzioni. Questi clienti non devono preoccuparsi o gestire direttamente i cluster Kubernetes.

Con AKS, il cliente ottiene un piano di *controllo*completamente gestito. Il piano di controllo contiene tutti i componenti e i servizi di cui il cliente ha bisogno per operare e fornire i cluster Kubernetes agli utenti finali. Tutti i componenti Kubernetes sono gestiti e gestiti da Microsoft.

Microsoft gestisce e monitora i seguenti componenti tramite il riquadro di controllo:

* Server API Kubelet o Kubernetes
* Etcd o un archivio chiave-valore compatibile, che fornisce qualità del servizio (QoS), scalabilità e runtime
* Servizi DNS (ad esempio, kube-dns o CoreDNS)
* Proxy o rete Kubernetes

AKS non è una soluzione cluster completamente gestita. Alcuni componenti, ad esempio i nodi di lavoro, hanno *responsabilità condivisa*, in cui gli utenti devono gestire il cluster AKS. L'input dell'utente è necessario, ad esempio, per applicare una patch di protezione del sistema operativo del nodo di lavoro.

I servizi vengono *gestiti* nel senso che Microsoft e il team AKS distribuiscono, operano ed è responsabile della disponibilità e delle funzionalità dei servizi. I clienti non possono modificare questi componenti gestiti. Microsoft limita la personalizzazione per garantire un'esperienza utente coerente e scalabile. Per una soluzione completamente personalizzabile, vedere [AKS Engine](https://github.com/Azure/aks-engine).

> [!NOTE]
> I nodi di lavoro AKS vengono visualizzati nel portale di Azure come normali risorse di Azure IaaS.AKS worker nodes appear in the Azure portal as regular Azure IaaS resources. Ma queste macchine virtuali vengono distribuite in un gruppo\\di risorse di Azure personalizzato (con prefisso CON MC). È possibile modificare i nodi di lavoro AKS. Ad esempio, è possibile usare Secure Shell (SSH) per modificare i nodi di lavoro AKS nel modo in cui si modificano le normali macchine virtuali (non è possibile, tuttavia, modificare l'immagine del sistema operativo di base e le modifiche potrebbero non persistere durante un aggiornamento o un riavvio) ed è possibile collegare altre risorse di Azure ad AKS nodi di lavoro. Tuttavia, quando si apportano modifiche *dalla gestione e dalla personalizzazione delle bande,* il cluster AKS può diventare insostenibile. Evitare di modificare i nodi di lavoro, a meno che il supporto tecnico Microsoft non indicherà l'utente di apportare modifiche.

L'emissione di operazioni non supportate come definito in precedenza, ad esempio la deallocazione fuori banda di tutti i nodi agente, rende il cluster non supportato. AKS si riserva il diritto di archiviare piani di controllo configurati come non più guida di supporto per periodi prolungati pari a e oltre 30 giorni. AKS gestisce i backup dei metadati del cluster e così via e può riallocare facilmente il cluster. Questa riallocazione può essere avviata da qualsiasi operazione PUT che riporta il cluster al supporto, ad esempio un aggiornamento o la scalabilità ai nodi agente attivi.

## <a name="shared-responsibility"></a>Responsabilità condivisa

Quando viene creato un cluster, il cliente definisce i nodi di lavoro Kubernetes creati da AKS. I carichi di lavoro dei clienti vengono eseguiti su questi nodi. I clienti sono proprietari e possono visualizzare o modificare i nodi di lavoro.

Poiché i nodi del cluster dei clienti eseguono codice privato e archiviano dati sensibili, il supporto tecnico Microsoft può accedervi solo in modo limitato. Il supporto tecnico Microsoft non è in grado di accedere, eseguire comandi o visualizzare i log per questi nodi senza l'autorizzazione o l'assistenza del cliente.

Poiché i nodi di lavoro sono sensibili, Microsoft si preoccupa molto di limitare la gestione in background. In molti casi, il carico di lavoro continuerà a essere eseguito anche se i nodi master Kubernetes, eccd, e altri componenti gestiti da Microsoft hanno esito negativo. I nodi di lavoro modificati con noncuranza possono causare perdite di dati e carichi di lavoro e possono rendere il cluster non supportabile.

## <a name="aks-support-coverage"></a>Copertura del supporto AKS

Microsoft fornisce supporto tecnico per:

* Connettività a tutti i componenti Kubernetes forniti e supportati dal servizio Kubernetes, ad esempio il server API.
* Gestione, tempo di attività, QoS e operazioni dei servizi aerei di controllo Kubernetes (Kubernetes nodi master, server API, eccd, e kube-dns, ad esempio).
* Etcd. Il supporto include backup automatici e trasparenti di tutti i dati etcd ogni 30 minuti per la pianificazione delle emergenze e il ripristino dello stato del cluster. Questi backup non sono direttamente disponibili per clienti o utenti. Garantiscono affidabilità e coerenza dei dati.
* Qualsiasi punto di integrazione nel driver del provider cloud di Azure per Kubernetes.Any integration points in the Azure cloud provider driver for Kubernetes. Queste includono integrazioni in altri servizi di Azure, ad esempio servizi di bilanciamento del carico, volumi persistenti o rete (Kubernetes e Azure CNI).
* Domande o problemi sulla personalizzazione dei componenti del piano di controllo, ad esempio il server API Kubernetes, eccd e kube-dns.
* Problemi relativi alla rete, ad esempio Azure CNI, kubenet o altri problemi di accesso e funzionalità alla rete. I problemi possono includere la risoluzione DNS, la perdita di pacchetti, il routing e così via. Microsoft supporta vari scenari di rete:
  * Kubenet (base) e rete avanzata (CNI di Azure) all'interno del cluster e dei componenti associatiKubenet (basic) and advanced networking (Azure CNI) within the cluster and associated components
  * Connettività ad altri servizi e applicazioni di Azure
  * Controller di ingresso e configurazioni di bilanciamento del carico o in ingresso
  * Prestazioni e latenza della rete

Microsoft non fornisce supporto tecnico per gli elementi seguenti:

* Domande su come utilizzare Kubernetes. Ad esempio, il supporto tecnico Microsoft non fornisce consigli su come creare controller di ingresso personalizzati, usare carichi di lavoro dell'applicazione o applicare strumenti software di terze parti o open source.
  > [!NOTE]
  > Il supporto tecnico Microsoft può consigliare la funzionalità, la personalizzazione e l'ottimizzazione del cluster AKS (ad esempio, problemi di operazioni e procedure di Kubernetes).
* Progetti open source di terze parti che non vengono forniti come parte del piano di controllo Kubernetes o distribuiti con i cluster AKS. Questi progetti potrebbero includere Istio, Helm, Envoy o altri.
  > [!NOTE]
  > Microsoft può fornire il supporto per i progetti open source di terze parti, ad esempio Helm e Kured. Se lo strumento open source di terze parti si integra con il provider cloud di Kubernetes Azure o altri bug specifici di AKS, Microsoft supporta esempi e applicazioni della documentazione Microsoft.Where the third-party open-source tool integrates with the Kubernetes Azure cloud provider or other AKS-specific bugs, Microsoft supports examples and applications from Microsoft documentation.
* Software closed-source di terze parti. Questo software può includere strumenti di scansione di sicurezza e dispositivi di rete o software.
* Problemi relativi alle compilazioni multicloud o multivendor. Ad esempio, Microsoft non supporta i problemi relativi all'esecuzione di una soluzione di fornitore di cloud multipubblico federato.
* Personalizzazioni di rete diverse da quelle elencate nella documentazione di [AKS.](https://docs.microsoft.com/azure/aks/)
  > [!NOTE]
  > Microsoft supporta problemi e bug relativi ai gruppi di sicurezza di rete (NSG). Ad esempio, il supporto tecnico Microsoft può rispondere a domande su un errore di aggiornamento di un gruppo di sicurezza di rete o un comportamento imprevisto del gruppo di sicurezza di rete o del bilanciamento del carico.

## <a name="aks-support-coverage-for-worker-nodes"></a>Copertura del supporto AKS per i nodi di lavoroAKS support coverage for worker nodes

### <a name="microsoft-responsibilities-for-aks-worker-nodes"></a>Responsabilità Microsoft per i nodi di lavoro AKS

Microsoft e i clienti condividono la responsabilità per i nodi di lavoro Kubernetes in cui:

* L'immagine del sistema operativo di base ha richiesto aggiunte (ad esempio agenti di monitoraggio e di rete).
* I nodi di lavoro ricevono automaticamente le patch del sistema operativo.
* I problemi con i componenti del piano di controllo Kubernetes in esecuzione sui nodi di lavoro vengono risolti automaticamente. I componenti includono quanto segue:
  * Kube-proxy
  * Tunnel di rete che forniscono percorsi di comunicazione ai componenti master di Kubernetes
  * Kubelet
  * Docker o Moby daemon

> [!NOTE]
> In un nodo di lavoro, se un componente del piano di controllo non è operativo, il team AKS potrebbe essere necessario riavviare singoli componenti o l'intero nodo lavoratore. Queste operazioni di riavvio sono automatizzate e forniscono la correzione automatica per i problemi comuni. Questi riavvii si verificano solo a livello di _nodo_ e non a seconda del cluster, a meno che non si verifichi un'interruzione o un'interruzione di emergenza.

### <a name="customer-responsibilities-for-aks-worker-nodes"></a>Responsabilità del cliente per i nodi di lavoro AKS

Microsoft non riavvia automaticamente i nodi di lavoro per applicare le patch a livello di sistema operativo. Anche se le patch del sistema operativo vengono recapitate ai nodi di lavoro, il *cliente* è responsabile del riavvio dei nodi di lavoro per applicare le modifiche. Librerie condivise, daemons come unità ibrida a stato solido (SSHD) e altri componenti a livello di sistema o sistema operativo vengono automaticamente patch.

I clienti sono responsabili dell'esecuzione degli aggiornamenti di Kubernetes. Possono eseguire gli aggiornamenti tramite il pannello di controllo di Azure o l'interfaccia della riga di comando di Azure.They can execute upgrades through the Azure control panel or the Azure CLI. Questo vale per gli aggiornamenti che contengono miglioramenti di sicurezza o funzionalità a Kubernetes.

> [!NOTE]
> Poiché AKS è un *servizio gestito,* i suoi obiettivi finali includono la rimozione della responsabilità per patch, aggiornamenti e raccolta di log per rendere la gestione del servizio più completa e viva. Con l'aumentare della capacità del servizio per la gestione end-to-end, le versioni future potrebbero omettere alcune funzioni (ad esempio, il riavvio dei nodi e l'applicazione automatica di patch).

### <a name="security-issues-and-patching"></a>Problemi di sicurezza e applicazione di patch

Se viene rilevato un difetto di sicurezza in uno o più componenti di AKS, il team di AKS patchrà tutti i cluster interessati per ridurre il problema. In alternativa, il team fornirà agli utenti indicazioni sull'aggiornamento.

Per i nodi di lavoro che interessano un difetto di sicurezza, se è disponibile una patch di tempo di inattività zero, il team AKS applicherà tale patch e informerà gli utenti della modifica.

Quando una patch di protezione richiede il riavvio del nodo di lavoro, Microsoft informerà i clienti di questo requisito. Il cliente è responsabile del riavvio o dell'aggiornamento per ottenere la patch del cluster. Se gli utenti non applicano le patch in base alle linee guida di AKS, il cluster continuerà a essere vulnerabile al problema di sicurezza.

### <a name="node-maintenance-and-access"></a>Manutenzione e accesso dei nodi

I nodi di lavoro sono una responsabilità condivisa e sono di proprietà dei clienti. Per questo motivo, i clienti hanno la possibilità di accedere ai propri nodi di lavoro e apportare modifiche potenzialmente dannose, ad esempio gli aggiornamenti del kernel e l'installazione o la rimozione di pacchetti.

Se i clienti apportano modifiche distruttive o fanno sì che i componenti del piano di controllo non siano in linea o diventino non funzionanti, AKS rileverà questo errore e ripristinerà automaticamente il nodo di lavoro allo stato di lavoro precedente.

Anche se i clienti possono accedere e cambiare i nodi di lavoro, questa operazione è sconsigliata perché le modifiche possono rendere un cluster insostenibile.

## <a name="network-ports-access-and-nsgs"></a>Porte di rete, accesso e gruppi di sicurezza di rete

Come servizio gestito, AKS ha requisiti di rete e connettività specifici. Questi requisiti sono meno flessibili dei requisiti per i normali componenti IaaS. In AKS, operazioni come la personalizzazione delle regole del gruppo di sicurezza di rete, il blocco di una porta specifica (ad esempio, l'utilizzo di regole del firewall che bloccano la porta in uscita 443) e gli URL di whitelisting possono rendere il cluster insostenibile.

> [!NOTE]
> Attualmente, AKS non consente di bloccare completamente il traffico in uscita dal cluster. Per controllare l'elenco di URL e porte che il cluster può utilizzare per il traffico in uscita, vedere limitare il [traffico in uscita.](limit-egress-traffic.md)

## <a name="unsupported-alpha-and-beta-kubernetes-features"></a>Funzionalità di Kubernetes alpha e beta non supportate

AKS supporta solo funzioni stabili nell'ambito del progetto Kubernetes a monte. Se non diversamente documentato, AKS non supporta le funzionalità alfa e beta disponibili nel progetto Kubernetes a monte.

In due scenari, le funzionalità alfa o beta potrebbero essere implementate prima che siano generalmente disponibili:

* I clienti hanno incontrato il prodotto AKS, il supporto o i team di progettazione e sono stati invitati a provare queste nuove funzionalità.
* Queste funzionalità sono state [abilitate da un flag di funzionalità](https://github.com/Azure/AKS/blob/master/previews.md). I clienti devono acconsentire esplicitamente all'utilizzo di queste funzionalità.

## <a name="preview-features-or-feature-flags"></a>Visualizzare l'anteprima di funzioni o contrassegni di funzionalità

Per le caratteristiche e le funzionalità che richiedono test estesi e commenti e suggerimenti degli utenti, Microsoft rilascia nuove funzionalità di anteprima o funzionalità dietro un flag di funzionalità. Considerare queste funzionalità come funzionalità non definitive o beta.

Le funzionalità di anteprima o di contrassegno delle funzionalità non sono destinate alla produzione. Le modifiche in corso nelle API e nel comportamento, le correzioni di bug e altre modifiche possono causare cluster instabili e tempi di inattività.

Le funzionalità dell'anteprima pubblica sono sottoposte al supporto del "miglior sforzo" in quanto queste funzionalità sono in anteprima e non sono destinate alla produzione e sono supportate dai team di supporto tecnico AKS solo durante l'orario di ufficio. Per ulteriori informazioni si prega di consultare:

* [Domande frequenti relative al supporto tecnico Azure](https://azure.microsoft.com/support/faq/)

> [!NOTE]
> Le funzionalità di anteprima hanno effetto a livello di sottoscrizione di Azure.Preview features take effect at the Azure *subscription* level. Non installare le funzionalità di anteprima in un abbonamento di produzione. In una sottoscrizione di produzione, le funzionalità di anteprima possono modificare il comportamento predefinito dell'API e influire sulle operazioni regolari.

## <a name="upstream-bugs-and-issues"></a>Bug e problemi a monte

Data la velocità di sviluppo nel progetto Kubernetes a monte, sorgono invariabilmente bug. Alcuni di questi bug non possono essere patchati o aggirati all'interno del sistema AKS. Al contrario, le correzioni di bug richiedono patch più grandi per progetti a monte (ad esempio Kubernetes, sistemi operativi di nodo o di lavoro e kernel). Per i componenti di proprietà di Microsoft (ad esempio il provider cloud di Azure), il personale AKS e Azure si impegna a risolvere i problemi a monte nella community.

Quando un problema di supporto tecnico è causato dalla directory principale da uno o più bug upstream, i team di supporto e progettazione AKS:

* Identificare e collegare i bug a monte con tutti i dettagli di supporto per spiegare il motivo per cui questo problema influisce sul cluster o sul carico di lavoro. I clienti ricevono collegamenti ai repository necessari in modo da poter controllare i problemi e vedere quando una nuova versione fornirà correzioni.
* Fornire potenziali soluzioni alternative o attenuazioni. Se il problema può essere attenuato, un [problema noto](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue) verrà archiviato nel repository AKS. La presentazione dei problemi noti spiega:
  * Il problema, inclusi i collegamenti ai bug a monte.
  * La soluzione alternativa e i dettagli su un aggiornamento o un'altra persistenza della soluzione.
  * Sequenze temporali approssimative per l'inclusione del problema, in base alla cadenza di rilascio a monte.
