---
title: Criteri di supporto di Azure Kubernetes Service (AKS)
description: Informazioni sui criteri di supporto di Azure Kubernetes Service (AKS), responsabilità condivise, le funzionalità di anteprima e alfa/Beta.
services: container-service
author: jnoller
ms.service: container-service
ms.topic: article
ms.date: 04/01/2019
ms.author: jenoller
ms.openlocfilehash: f173fc7c794729eae8c60cceefa88d153800a816
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59502174"
---
# <a name="azure-kubernetes-service-aks-support-policies"></a>Criteri di supporto di Azure Kubernetes Service (AKS)

Questo articolo fornisce informazioni dettagliate sui criteri di supporto tecnico AKS, le limitazioni, e informazioni dettagliate, incluse la gestione di nodo di lavoro, gestita di controllare i componenti di piano, i componenti open source di terze parti e sicurezza / gestione delle patch.

## <a name="service-updates--releases"></a>Versioni e aggiornamenti del servizio

* Per informazioni di versione, vedere il [nelle note sulla versione servizio contenitore di AZURE][1]
* Per le funzionalità in anteprima pubblica, vedere [funzionalità di anteprima del servizio contenitore di AZURE e i progetti correlati][2]

## <a name="what-is-managed"></a>Che cos'è 'gestito'

A differenza di IaaS cloud i componenti di base, ad esempio calcolo o rete, che espongono i controlli di basso livello e le opzioni di personalizzazione per gli utenti di sfruttare, il servizio contenitore di AZURE offre una distribuzione di Kubernetes chiavi in mano che rappresenta il set di configurazioni comune e funzionalità necessarie per Kubernetes. I clienti che usano questo servizio hanno un numero limitato di personalizzazioni, distribuzione e altre opzioni. Questo significa anche che i clienti non sono necessario preoccuparsi o gestire il cluster Kubernetes direttamente.

Con servizio contenitore di AZURE, il cliente riceve completamente gestito **piano di controllo** , in cui il piano di controllo contiene tutti i componenti e servizi necessari per eseguire operazioni e i cluster Kubernetes di fornire agli utenti finali. Tutti i componenti di Kubernetes vengono mantenuti e gestiti da Microsoft.

Con managed **piano di controllo** gestiti e monitorati da Microsoft i componenti seguenti:

* Kubelet / Server API Kubernetes
* Etcd o in un archivio chiave/valore compatibile-tra cui qualità del servizio, scalabilità e runtime
* I servizi DNS (ad esempio, kube-dns / CoreDNS)
* Rete/Kubernetes Proxy

Servizio contenitore di AZURE non è 100% managed **cluster** soluzione. Alcuni componenti (ad esempio, i nodi di lavoro) avere determinate **responsabilità condivise** casi in cui le azioni per la manutenzione del cluster servizio contenitore di AZURE richiedono l'interazione dell'utente. Ad esempio, nodo di lavoro dell'applicazione delle patch di sicurezza del sistema operativo.

 **Managed**, significa che Microsoft e dal team del servizio contenitore di AZURE consente di distribuire, opera ed è responsabile per la disponibilità e la funzionalità di questi servizi. **I clienti non è possibile modificare questi componenti**. La personalizzazione è limitata a garantire un'esperienza utente coerente e scalabile. Per una soluzione completamente personalizzabile, vedere [AKS-Engine](https://github.com/Azure/aks-engine).

> [!NOTE]
> È importante comprendere che i nodi di lavoro di Azure Kubernetes Service vengono visualizzati nel portale di Azure come regolare le risorse IaaS di Azure, anche se queste macchine virtuali vengono distribuite in un gruppo di risorse di Azure personalizzato (con prefisso con MC\\*). Un utente può modificarli, SSH al loro interno esattamente come le macchine virtuali normali (non è possibile, tuttavia, modificare l'immagine del sistema operativo di base, e le modifiche potrebbero non persistere anche in un aggiornamento o al successivo riavvio), ed è possibile collegare altre risorse di Azure ad essi o apportare altre modifiche. **In questo modo di personalizzazione e gestione fuori banda, tuttavia, significa che il cluster AKS stesso può diventare non supportabile. Evitare la modifica di nodi di lavoro solo se indicato dal supporto tecnico Microsoft.**

## <a name="what-is-shared-responsibility"></a>Che cos'è la condivisione di responsabilità

In fase di creazione del cluster servizio contenitore di AZURE crea un numero di nodi di lavoro di Kubernetes definiti dal cliente. Questi nodi, come indicato di sono in cui vengono eseguiti i carichi di lavoro dei clienti. I clienti di proprietari e possono visualizzare o modificare questi nodi di lavoro.

Poiché questi nodi sono in esecuzione del codice privato e archiviano i dati sensibili, dispone di supporto tecnico Microsoft **accesso limitato** al cliente di tutti i nodi del cluster. Il supporto tecnico Microsoft non può accedere, eseguire i comandi o visualizzare i log per questi nodi senza autorizzazione esplicita dei clienti e/o di assistenza per eseguire i comandi di debug, come indicato dal team di supporto.

A causa della natura sensibile di questi nodi ruolo di lavoro, Microsoft ha molta attenzione a limitare la gestione "dietro le quinte" di questi nodi. Anche se il Kubernetes master nodi etcd e altri errori di componenti (gestita da Microsoft) del carico di lavoro continuerà a eseguire in molti casi. Se i nodi di lavoro vengono modificati senza care, può comportare la perdita di dati e/o del carico di lavoro e il rendering del cluster non supportabile.

## <a name="azure-kubernetes-service-support-coverage"></a>Code coverage supporto del servizio Kubernetes di Azure

**Microsoft offre supporto tecnico per le operazioni seguenti:**

* Connettività a tutti i componenti di Kubernetes fornita e supportata dal servizio di Kubernetes (ad esempio, il server API)
* Servizi piano (etcd di API del Server, i nodi Master di Kubernetes, kube-dns, ad esempio controllano la gestione, il tempo di attività, QoS e operazioni di Kubernetes.
* Il supporto Etcd include backup automatizzato e trasparente di tutti i dati di etcd ogni 30 minuti per il ripristino di emergenza cluster e la pianificazione dello stato. Questi backup non sono disponibili direttamente a clienti o gli utenti e vengono usati per garantire coerenza e affidabilità dei dati
* I punti di integrazione nel driver del Provider di Cloud di Azure per Kubernetes, ad esempio le integrazioni per altri Azure services, ad esempio servizi di bilanciamento del carico, volumi permanenti, di rete (Kubernetes e Azure CNI)
* I componenti, ad esempio il server API Kubernetes etcd e kube-dns il piano di domande o problemi riguardanti la personalizzazione del controllo.
* Problemi relativi sugli argomenti relativi alla rete, ad esempio Azure CNI, Kubenet o un'altra rete di accesso e le funzionalità (DNS risoluzione, perdita di pacchetti, routing e così via).
  * Scenari di rete supportati includono Kubenet (Basic) e Advanced Networking (Azure CNI) all'interno di cluster e componenti associati, la connettività con altre applicazioni e servizi di Azure. Inoltre, i controller di ingress e configurazione del bilanciamento del carico di traffico in ingresso /, le prestazioni di rete e latenza sono supportati da Microsoft.

**Microsoft non fornisce supporto tecnico per le operazioni seguenti:**

* Consulenza / utilizzo "Procedure" Kubernetes domande, ad esempio come creare controller di ingresso personalizzato, domande di carico di lavoro dell'applicazione, e i pacchetti third-party/OSS o strumenti esulano dall'ambito.
  * I ticket di consulenza per AKS cluster funzionalità, personalizzazione, operazioni di ottimizzazione, ad esempio Kubernetes problemi/how-tos rientrano nell'ambito.
* I progetti open source di terze parti non forniti come parte di Kubernetes piano di controllo o distribuiti con cluster servizio contenitore di AZURE, ad esempio Istio, Helm, Envoy e ad altri utenti.
  * Per i progetti open source di terze parti, ad esempio Helm e Kured, viene fornito supporto massimo sforzo per gli esempi e applicazioni fornite nella documentazione di Microsoft e in cui tale strumento open source di terze parti si integra con il provider di cloud di Azure in Kubernetes o altri bug del servizio contenitore di AZURE specifiche.
* Software di terze parti source chiuso – sono inclusi strumenti, dispositivi di rete o software di analisi della sicurezza.
* Problema riguardo a "multi-cloud" o multi-fornitore di build-esternamente non è supportati, ad esempio in esecuzione una soluzione per fornitori cloud pubblici multi federato non è supportato.
* Le personalizzazioni di rete specifici, diversi da quelli documentati in ufficiale [documentazione di AKS][3].
  > [!NOTE]
  > Problemi e i bug intorno a gruppi di sicurezza di rete è supportata. Ad esempio, il supporto può rispondere alle domande intorno Nsg mancato aggiornamento o un comportamento imprevisto di sicurezza di rete o servizio di bilanciamento del carico.

## <a name="azure-kubernetes-service-support-coverage-worker-nodes"></a>Copertura del supporto del servizio di Kubernetes Azure (nodi di lavoro)

### <a name="microsoft-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Responsabilità di Microsoft per i nodi di lavoro di Azure Kubernetes Service

Come indicato nella `shared responsibility` sezione Kubernetes con nodi di lavoro rientrano in un modello di responsabilità di attività di collaborazione, dove:

* Fornisce l'immagine del sistema operativo di base e le aggiunte necessari (ad esempio il monitoraggio e gli agenti di rete)
* Distribuzione automatica di patch del sistema operativo nei nodi di lavoro
* Correzione automatica dei problemi relativi a Kubernetes di controllare i componenti di piano in esecuzione nei nodi di lavoro, ad esempio:
  * kube-proxy
  * tunnel di rete che forniscono i percorsi di comunicazione in Kubernetes il master di componenti
  * kubelet
  * daemon docker/moby

> [!NOTE]
> Se un componente del piano di controllo non è operativo su un nodo di lavoro, il team del servizio contenitore di AZURE potrebbe essere necessario riavviare il nodo di lavoro intero per risolvere il problema. Questa operazione viene eseguita per conto di un utente e non eseguita, a meno che un'escalation dei clienti è effettuata (scadenza per l'accesso al carico di lavoro attivo di clienti e dei dati). Ogni volta che verranno possibile personale AKS lavoro per apportare le necessarie riavviare conseguenze non di applicazione.

### <a name="customer-responsibilities-for-azure-kubernetes-service-worker-nodes"></a>Responsabilità del cliente per nodi di lavoro di Azure Kubernetes Service

**Microsoft non consente di:**

- Automaticamente i nodi di lavoro di riavvio per il sistema operativo a livello di patch per rendere effettive **(corrente, vedere sotto)**

Patch del sistema operativo vengono recapitate ai nodi di lavoro, tuttavia, è il **responsabilità del cliente** per riavviare i nodi di lavoro rendere effettive le modifiche. Questa applicazione di patch automatica include le librerie condivise, daemon, ad esempio SSHD e altri componenti a livello di sistema/OS.

Per gli aggiornamenti di Kubernetes **i clienti sono responsabili per l'esecuzione dell'aggiornamento** tramite il pannello di controllo di Azure o il comando di Azure. Questo vale per gli aggiornamenti contenenti sicurezza o i miglioramenti di funzionalità in Kubernetes.

> [!NOTE]
> Servizio contenitore di AZURE è un `managed service` obiettivi finali del servizio includono la rimozione di responsabilità per le patch, aggiornamenti, raccolta di log e altro ancora per renderlo un servizio completamente gestito e indolore altre. Questi elementi (il riavvio di nodo, applicazione di patch automatica) possono essere rimosso nelle versioni future con l'aumentare delle nostre funzionalità per-to-end di gestione.

### <a name="security-issues-and-patching"></a>Problemi di sicurezza e applicazione di patch

In alcuni casi (ad esempio CVE), un difetto di protezione può trovarsi in uno o più dei componenti del servizio AKS. In questi scenari, servizio contenitore di AZURE verrà patch tutti i cluster interessati per attenuare il problema, se possibile, o fornire indicazioni sull'aggiornamento agli utenti.

Per i nodi di lavoro interessati da tali un problema di sicurezza, se una patch senza tempi di inattività del problema è disponibile, il team di AKS applicare tale correzione e inviare notifiche agli utenti della modifica.

Se una patch di sicurezza richiede il riavvio di nodo di lavoro, Microsoft informerà i clienti di questo requisito e il cliente è responsabile per l'esecuzione del riavvio o aggiornamento per ottenere la patch per i cluster. Se gli utenti non si applicano le patch al materiale sussidiario AKS, sui cluster continuerà a essere vulnerabili per i problemi.

### <a name="node-maintenance-and-access"></a>Accesso e la manutenzione di nodo

Poiché i nodi di lavoro sono una responsabilità condivisa e con la proprietà dei clienti, i clienti possono accedere a questi ruoli di lavoro ed eseguire modifiche potenzialmente dannose, ad esempio gli aggiornamenti del kernel, rimozione di pacchetti e installare nuovi pacchetti.

Se i clienti di eseguono azioni distruttive o le azioni che attivano i componenti di piano di controllo per passare offline o in caso contrario, diventano non funzionale, il servizio contenitore di AZURE rileverà l'errore ed eseguire monitoraggio e aggiornamento automatici per ripristinare il nodo di lavoro nell'uso precedente stato.

Sebbene i clienti possono accedere e modificare i nodi di lavoro, risulta *sconsigliato* perché ciò può rendere il cluster non supportabile.

## <a name="network-ports-access-and-network-security-groups"></a>Le porte di rete, accesso e i gruppi di sicurezza di rete

Come servizio gestito, servizio contenitore di AZURE presenta requisiti specifici di rete e connettività. Questi requisiti sono meno flessibili i componenti IaaS normale. A differenza di altri componenti di IaaS, determinate operazioni (ad esempio la personalizzazione delle regole del gruppo di sicurezza di rete, il blocco delle porte specifiche, l'URL nell'elenco elementi consentiti e così via) possono eseguire il rendering del cluster non supportabile (ad esempio, regole del firewall blocchi la porta in uscita 443).

> [!NOTE]
> Il blocco completo in uscita (ad esempio, dominio o la porta esplicito nell'elenco elementi consentiti) dal cluster non è un servizio contenitore di AZURE scenario supportato in questo momento. L'elenco di URL e le porte è soggette a modifiche senza preavviso e può essere fornito dal supporto tecnico di Azure mediante un ticket. L'elenco fornito è solo per i clienti che si è disposti ad accettare che *la disponibilità del cluster può esserne influenzata in qualsiasi momento.*

## <a name="alphabeta-kubernetes-features-not-supported"></a>Funzionalità di Kubernetes alfa/Beta (non supportate)

Servizio contenitore di AZURE supporta solo funzionalità stabili all'interno del progetto Kubernetes upstream. Alfa/Beta funzionalità disponibili in Kubernetes upstream non sono supportate a meno che non vengano comunicate e documentate in caso contrario.

Esistono due casi in cui può essere implementata alfa o Beta funzionalità prima della disponibilità generale:

* I clienti hanno incontrato il prodotto servizio contenitore di AZURE, il supporto o team di progettazione e viene esplicitamente richiesto di provare queste nuove funzionalità.
* Queste funzionalità sono state [abilitata tramite un Flag funzionalità] [ 2] (esplicite opt-in)

## <a name="preview-features--feature-flags"></a>Funzionalità di anteprima / flag funzionalità

Per le funzionalità che richiedono estesa community di test e commenti degli utenti, Microsoft rilascerà nuove funzionalità di anteprima o funzionalità dietro a un flag funzionalità. Queste funzionalità devono essere considerate versioni non definitive o Beta e vengono esposti per consentire agli utenti di provare queste nuove funzionalità.

Tuttavia, questi anteprima / flag funzionalità le funzionalità non sono per uso in produzione: API, modifica del comportamento, correzioni di bug e altre modifiche possono prevedere che può comportare tempi di inattività e cluster instabile. Supporto per queste funzionalità è limitato alla creazione di report di bug/problema. Non abilitare queste funzionalità in sistemi di produzione o sottoscrizioni.

> [!NOTE]
> Abilitazione della funzionalità di anteprima viene applicato di Azure **sottoscrizione** livello. Non installare le funzionalità di anteprima nella sottoscrizione di produzione poiché può modificare il comportamento delle API predefinita conseguenze per le normali operazioni.

## <a name="upstream-bugs-and-issues"></a>Problemi e i bug upstream

Considerata la velocità di sviluppo del progetto Kubernetes upstream, non ci sono invariabilmente bug che non può essere corretto o lavorato intorno all'interno del sistema del servizio contenitore di AZURE e invece richiedono le patch di dimensioni maggiori per i progetti upstream (ad esempio Kubernetes, sistemi operativi di nodo/di lavoro e i kernel). Per i componenti che è il proprietario (ad esempio, il Provider di Cloud di Azure), il personale di servizio contenitore di AZURE/Azure si impegna a risoluzione del problema upstream nella community.

Per i casi in cui una richiesta di supporto tecnico è causa radice per uno o più bug di upstream, servizio contenitore di AZURE supporta e ingegneria eseguirà gli elementi seguenti:

* Identificare e collegare i bug upstream con tutti i dettagli di supporti per quanto riguarda il motivo per cui questo problema influisce sul cluster e/o del carico di lavoro. I clienti riceveranno con collegamenti per i repository necessari/problemi guardare problemi e vedere quando una nuova versione di Kubernetes/altri includerà le soluzioni
* Potenziali soluzioni alternative o soluzioni di prevenzione: In alcuni casi potrebbe essere possibile risolvere il problema: in questo caso, un "[noto problema](https://github.com/Azure/AKS/issues?q=is%3Aissue+is%3Aopen+label%3Aknown-issue)" verranno archiviati nel repository di servizio contenitore di AZURE che spiega:
  * Il problema e il collegamento ai bug upstream
  * La soluzione alternativa e i dettagli per aggiornamento/altro persistenza della soluzione
  * Approssimative sequenze temporali per l'inclusione in base alla cadenza di rilascio upstream

[1]: https://github.com/Azure/AKS/releases
[2]: https://github.com/Azure/AKS/blob/master/previews.md
[3]: https://docs.microsoft.com/azure/aks/
