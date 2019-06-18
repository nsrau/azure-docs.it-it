---
title: Domande frequenti - Avere vFXT per Azure
description: Domande frequenti su Avere vFXT per Azure
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: v-erkell
ms.openlocfilehash: 47a4b38d39c52992b51284776ec34cb9491020e7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65595420"
---
# <a name="avere-vfxt-for-azure-faq"></a>Domande frequenti su Avere vFXT per Azure

Questo articolo risponde a una serie di domande che potranno essere utili per capire se Avere vFXT per Azure è adatto alle proprie esigenze. Offre informazioni di base su Avere vFXT e spiega come usarlo con altri componenti di Azure e con i prodotti di fornitori esterni. 

## <a name="general"></a>Generale 

### <a name="what-is-avere-vfxt-for-azure"></a>Informazioni su Avere vFXT per Azure

Avere vFXT per Azure è un file system a prestazioni elevate che memorizza nella cache i dati attivi in Calcolo di Azure per un'elaborazione efficiente dei carichi di lavoro critici.

### <a name="is-avere-vfxt-a-storage-solution"></a>Avere vFXT è una soluzione di archiviazione?

No. Avere vFXT è una *cache* di file system collegata agli ambienti di archiviazione, come EMC, NAS NetApp o un contenitore BLOB di Azure. Avere vFXT semplifica le richieste di dati dai client e memorizza nella cache i dati necessari per migliorare le prestazioni su larga scala e nel corso del tempo. Avere vFXT non archivia dati e non contiene informazioni sulla quantità di dati archiviati.

### <a name="is-avere-vfxt-a-tiering-solution"></a>Avere vFXT è una soluzione di suddivisione in livelli?

Avere vFXT non suddivide automaticamente i dati tra livelli di archiviazione ad accesso frequente e ad accesso sporadico.  

### <a name="how-do-i-know-if-an-environment-is-right-for-avere-vfxt"></a>Come si determina se un ambiente è adatto ad Avere vFXT?

Il modo migliore per rispondere a questa domanda consiste nel chiedersi se il proprio carico di lavoro è memorizzabile nella cache, ovvero se il carico di lavoro ha un rapporto lettura-scrittura elevato, ad esempio operazioni di lettura/scrittura pari a 80/20 o 70/30.

Prendere in considerazione Avere vFXT per Azure se si dispone di una pipeline di analisi basata su file che viene eseguita in un numero elevato di macchine virtuali di Azure e soddisfa una o più delle condizioni seguenti:

* Le prestazioni complessive sono lente o incoerenti a causa di tempi di accesso ai file prolungati (decine di millisecondi o secondi, a seconda dei requisiti). Questa latenza non è accettabile per il cliente.

* I dati richiesti per l'elaborazione si trovano all'estremità di un ambiente WAN e lo spostamento permanente di tali dati non è pratico. I dati potrebbero trovarsi in un'area di Azure differente o nel data center di un cliente.

* Un numero significativo di client sta richiedendo i dati, ad esempio in un cluster HPC (High Performance Computing). Il numero elevato di richieste simultanee può aumentare la latenza.

* Il cliente vuole eseguire la pipeline corrente "così com'è" nelle macchine virtuali di Azure e ha bisogno di una soluzione di archiviazione (o memorizzazione nella cache) condivisa basata su POSIX per la scalabilità. Con Avere vFXT per Azure non è necessario riprogettare la pipeline di lavoro per effettuare chiamate native all'archivio BLOB di Azure.

* L'applicazione HPC si basa su client NFSv3 (in alcuni casi può usare i client SMB 2.1, ma le prestazioni sono limitate).

Il diagramma seguente semplifica la risposta a questa domanda. Quanto più il flusso di lavoro si avvicina all'angolo in alto a destra, maggiore sarà la probabilità che la soluzione di memorizzazione nella cache Avere sia adatta all'ambiente in uso.

![Diagramma che mostra che i carichi con intensa attività di lettura e migliaia di client sono più adatti per Avere vFXT](media/avere-vfxt-fit-assessment.png)

### <a name="at-what-scale-of-clients-does-the-avere-vfxt-solution-make-the-most-sense"></a>Con quale scalabilità dei client ha più senso la soluzione Avere vFXT?

La soluzione di memorizzazione nella cache Avere vFXT è progettata per gestire centinaia, migliaia o decine di migliaia di core di calcolo. Se si hanno solo alcune macchine che eseguono carichi leggeri, Avere vFXT non è la soluzione ideale.

I clienti tipici di Avere vFXT eseguono carichi di lavoro complessi a partire da circa 1000 core CPU. Questi ambienti possono arrivare fino a 50.000 core o più. Poiché la soluzione Avere vFXT è scalabile, è possibile aggiungere nodi per supportare questi carichi di lavoro di pari passo con la loro espansione, per poter fornire maggiore velocità effettiva o più operazioni di I/O al secondo.

### <a name="how-much-data-can-an-avere-vfxt-environment-store"></a>Quanti dati può archiviare un ambiente Avere vFXT?

Avere vFXT è una cache. Non è specificatamente progettato per archiviare i dati. Usa una combinazione di RAM e unità SSD per archiviare i dati memorizzati nella cache. I dati vengono archiviati in modo permanente in un sistema di archiviazione back-end (ad esempio, un sistema NAS NetApp o un contenitore BLOB). Il sistema Avere vFXT non contiene informazioni sulla quantità di dati archiviati. Avere vFXT memorizza nella cache solo il subset dei dati richiesti dai client.  

### <a name="what-regions-are-supported"></a>Quali aree sono supportate?

Avere vFXT per Azure è supportata in tutte le aree ad eccezione delle aree sovrane (Cina e Germania). Assicurarsi che l'area da usare riesca a supportare la quantità elevata di core di calcolo e le istanze di macchina virtuale necessarie per creare il cluster Avere vFXT.

### <a name="how-do-i-get-help-with-avere-vfxt"></a>Come ottenere assistenza con Avere vFXT?

Un gruppo di supporto specializzato offre assistenza con Avere vFXT per Azure. Seguire le istruzioni in [Ottenere assistenza per il sistema](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) per aprire un ticket di supporto dal portale di Azure. 

### <a name="is-avere-vfxt-highly-available"></a>Avere vFXT è a disponibilità elevata?

Sì, Avere vFXT viene eseguito esclusivamente come soluzione a disponibilità elevata.

### <a name="does-avere-vfxt-for-azure-also-support-other-cloud-services"></a>Avere vFXT per Azure supporta anche altri servizi cloud?

Sì, i clienti possono usare più di un provider di servizi cloud con il cluster Avere vFXT. Supporta bucket standard di AWS S3, bucket standard di Google Cloud Services e contenitori BLOB di Azure. 

> [!NOTE] 
> Per l'uso di Avere vFXT in AWS o Google Cloud è previsto un addebito per la tariffa del software, non applicabile con Azure.

## <a name="technical-compute"></a>Tecnica: Calcolo

### <a name="can-you-describe-what-an-avere-vfxt-environment-looks-like"></a>È possibile descrivere le caratteristiche di un ambiente Avere vFXT?

Avere vFXT è un'appliance di cluster costituita da più macchine virtuali di Azure. Una libreria Python gestisce la creazione, l'eliminazione e la modifica del cluster. Leggere [What is Avere vFXT for Azure?](avere-vfxt-overview.md) (Informazioni su Avere vFXT per Azure) per maggiori dettagli. 

### <a name="what-kind-of-azure-virtual-machines-does-avere-vfxt-run-on"></a>Con quali tipi di macchine virtuali di Azure può essere eseguito Avere vFXT?  

Un vFXT Avere per cluster di Azure Usa le macchine virtuali di Microsoft Azure E32s_v3. 

<!-- ### Can I mix and match virtual machine types for my cluster?

No, you must choose one virtual machine type or the other.
    
### Can I move between virtual machine types?

Yes, there is a migration path to move from one VM type to the other. [Open a support ticket](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt) to learn how.

-->

### <a name="does-the-avere-vfxt-environment-scale"></a>L'ambiente Avere vFXT può essere ridimensionato?

Il cluster Avere vFXT può avere dimensioni che vanno da tre nodi fino a 24 nodi di macchina virtuale. Contattare il team di supporto di Azure per assistenza con la pianificazione se si ritiene di avere bisogno di un cluster di più di nove nodi. Un numero di nodi più elevato richiede un'architettura di distribuzione di dimensioni maggiori.

### <a name="does-the-avere-vfxt-environment-autoscale"></a>L'ambiente Avere vFXT può essere ridimensionato automaticamente?

No. È possibile aumentare o ridurre le dimensioni del cluster, ma l'aggiunta o la rimozione dei nodi del cluster è un'operazione manuale.

### <a name="can-i-run-the-avere-vfxt-cluster-as-a-virtual-machine-scale-set"></a>È possibile eseguire il cluster Avere vFXT come set di scalabilità di macchine virtuali?

Avere vFXT non supporta la distribuzione di un set di scalabilità di macchine virtuali. Diversi meccanismi di supporto della disponibilità incorporati sono progettati solo per le macchine virtuali atomiche che fanno parte di un cluster.  

### <a name="can-i-run-the-avere-vfxt-cluster-on-low-priority-vms"></a>È possibile eseguire il cluster Avere vFXT nelle macchine virtuali con priorità bassa?

No, il sistema richiede un set stabile di macchine virtuali sottostanti.

### <a name="can-i-run-the-avere-vfxt-cluster-in-containers"></a>È possibile eseguire il cluster Avere vFXT in contenitori?

No, Avere vFXT deve essere distribuito come applicazione indipendente.

### <a name="do-the-avere-vfxt-vms-count-against-my-compute-quota"></a>Le macchine virtuali di Avere vFXT vengono prese in considerazione per la quota di calcolo?

Sì. Assicurarsi di disporre di una quota sufficiente nell'area per supportare il cluster.  

### <a name="can-i-run-the-avere-vfxt-cluster-machines-in-different-availability-zones"></a>È possibile eseguire le macchine del cluster Avere vFXT in zone di disponibilità diverse?

No. Il modello a disponibilità elevata in Avere vFXT attualmente non supporta singoli membri del cluster Avere vFXT situati in zone di disponibilità diverse.

### <a name="can-i-clone-avere-vfxt-virtual-machines"></a>È possibile clonare macchine virtuali di Avere vFXT?

No, è necessario usare lo script Python supportato per aggiungere o rimuovere nodi nel cluster Avere vFXT. Per altre informazioni, leggere [Gestire il cluster Avere vFXT](avere-vfxt-manage-cluster.md).  

### <a name="is-there-a-vm-version-of-the-software-i-can-run-in-my-own-local-environment"></a>È disponibile una versione di "macchina virtuale" del software che è possibile eseguire nell'ambiente locale?

No, il sistema viene offerto come appliance di cluster ed è testato in tipi di macchine virtuali specifici. Questa restrizione consente ai clienti di evitare la creazione di un sistema che non riesce a supportare i requisiti di prestazioni elevate di un flusso di lavoro tipico di Avere vFXT. 

## <a name="technical-disks"></a>Tecnica: Dischi

### <a name="what-types-of-disks-are-supported-for-the-azure-vms"></a>Quali tipi di dischi sono supportati per le macchine virtuali di Azure?

Avere vFXT per Azure può usare configurazioni di unità SSD Premium da 1 TB o 4 TB. La configurazione di unità SSD Premium può essere distribuita come più dischi gestiti.

### <a name="does-the-cluster-support-unmanaged-disks"></a>Il cluster supporta dischi non gestiti?

No, il cluster richiede dischi gestiti.

### <a name="does-the-system-support-local-attached-ssds"></a>Il sistema supporta unità SSD locali (collegate)?

Avere vFXT per Azure attualmente non supporta unità SSD locali. I dischi usati per Avere vFXT devono poter essere arrestati e riavviati, ma le unità SSD collegate locali in questa configurazione possono essere solo terminate.

### <a name="does-the-system-support-ultra-ssds"></a>Il sistema supporta unità Ultra SSD?

No, il sistema supporta solo configurazioni di unità SSD Premium.

### <a name="can-i-detach-my-premium-ssds-and-reattach-them-later-to-preserve-cache-contents-between-use"></a>È possibile rimuovere unità SSD Premium e ricollegarle in un secondo momento per mantenere il contenuto della cache tra un uso e l'altro?

La rimozione e il ricollegamento delle unità SSD non sono supportati. I metadati o il contenuto dei file nell'origine potrebbero avere subito modifiche tra un uso e l'altro, il che potrebbe causare problemi di integrità dei dati.

### <a name="does-the-system-encrypt-the-cache"></a>Il sistema esegue la crittografia della cache?

Viene eseguito lo striping dei dati tra i dischi, ma i dati non vengono crittografati. Tuttavia, i dischi stessi possono essere crittografati. Per altre informazioni, vedere [Applicare la sicurezza e usare criteri in macchine virtuali in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/security-policy#encryption).

## <a name="technical-networking"></a>Tecnica: Rete

### <a name="what-network-is-recommended"></a>Quale rete è consigliata?

Se si usa l'archiviazione locale con Avere vFXT, è necessario avere una connessione di rete da almeno 1 Gbps. Se si ha una quantità di dati limitata e si è disposti a copiare i dati nel cloud prima di eseguire i processi, potrebbe essere sufficiente la connettività VPN. 

> [!TIP] 
> Un collegamento di rete lento implica una maggiore lentezza delle letture a freddo iniziali. A sua volta, una lentezza della lettura aumenta la latenza della pipeline di lavoro. 

### <a name="can-i-run-avere-vfxt-in-a-different-virtual-network-than-my-compute-cluster"></a>È possibile eseguire Avere vFXT in una rete virtuale diversa dal cluster di elaborazione?

Sì, è possibile creare il sistema Avere vFXT in un'altra rete virtuale. Leggere [Pianificare il sistema Avere vFXT](avere-vfxt-deploy-plan.md) per maggiori dettagli.

### <a name="does-avere-vfxt-require-its-own-subnet"></a>Avere vFXT richiede una subnet propria?

Sì. Avere vFXT viene eseguito esclusivamente come un cluster a disponibilità elevata (HA) e richiede più indirizzi IP per il funzionamento. Se il cluster è nella propria subnet, si evita il rischio di conflitti di indirizzi IP, che possono causare problemi per l'installazione e il normale funzionamento. La subnet del cluster può essere all'interno della rete virtuale esistente purché non si sovrapponga alcun indirizzo IP.

### <a name="can-i-run-avere-vfxt-on-infiniband"></a>È possibile eseguire Avere vFXT con InfiniBand?

No, Avere vFXT usa solo Ethernet/IP.

### <a name="how-do-i-access-my-on-premises-nas-environment-from-avere-vfxt"></a>Come è possibile accedere all'ambiente NAS locale da Avere vFXT?

L'ambiente Avere vFXT è come qualsiasi altra macchina virtuale di Azure, perché richiede l'accesso indirizzato tramite un gateway di rete o una rete VPN al data center del cliente (e viceversa). È consigliabile usare la connettività Azure ExpressRoute se è disponibile nell'ambiente in uso.

### <a name="what-are-the-bandwidth-requirements-for-avere-vfxt"></a>Quali sono i requisiti di larghezza di banda per Avere vFXT?

I requisiti di larghezza di banda generali dipendono da due fattori: 

* La quantità di dati richiesti dall'origine 
* La tolleranza del sistema client per la latenza durante il caricamento iniziale dei dati  

Per gli ambienti sensibili alla latenza, è consigliabile usare una soluzione fiber con una velocità di collegamento minima di 1 Gbps. Se disponibile, usare ExpressRoute.  

### <a name="can-i-run-avere-vfxt-with-public-ip-addresses"></a>È possibile eseguire Avere vFXT con indirizzi IP pubblici?

No, Avere vFXT è progettato per l'uso in un ambiente di rete protetto tramite procedure consigliate.  

### <a name="can-i-restrict-internet-access-from-my-clusters-virtual-network"></a>È possibile limitare l'accesso a internet dalla rete virtuale del cluster? 

In generale, è possibile configurare sicurezza aggiuntiva sulla rete virtuale in base alle necessità, ma alcune restrizioni possono interferire con l'operazione del cluster.

Ad esempio, limitare l'accesso a internet in uscita dalla rete virtuale può causare problemi per il cluster a meno che non è inoltre aggiungere una regola che consenta in modo esplicito l'accesso al cloud Azure. Questa situazione è descritta in [documentazione aggiuntiva su GitHub](https://github.com/Azure/Avere/tree/master/src/vfxt/internet_access.md).

Per informazioni sulla protezione personalizzate, contattare il supporto come descritto in [Ottieni assistenza per il sistema](avere-vfxt-open-ticket.md#open-a-support-ticket-for-your-avere-vfxt).

## <a name="technical-back-end-storage-core-filers"></a>Tecnica: Archiviazione back-end (core filer)

### <a name="how-many-core-filers-does-a-single-avere-vfxt-environment-support"></a>Quanti core filer supporta un singolo ambiente Avere vFXT?

Un cluster Avere vFXT supporta fino a 20 core filer. 

### <a name="how-does-the-avere-vfxt-environment-store-data"></a>In che modo l'ambiente Avere vFXT archivia i dati?

Avere vFXT non è una soluzione di archiviazione. È una cache che legge e scrive i dati da più destinazioni di archiviazione denominate core filer. I dati archiviati nei dischi SSD Premium in Avere vFXT sono temporanei e alla fine vengono scaricati nella risorsa di archiviazione dei core filer back-end.

### <a name="which-core-filers-does-avere-vfxt-support"></a>Quali core filer supporta Avere vFXT?

In termini generali, Avere vFXT per Azure supporta i sistemi seguenti come core filer: 

* Dell EMC Isilon (OneFS 7.1, 7.2, 8.0 e 8.1) 
* NetApp ONTAP (modalità Cluster 9.4, 9.3, 9.2, 9.1P1, 8.0-8.3) e (modalità 7 7.*, 8.0-8.3) 

  > [!NOTE] 
  > File di NetApp Azure attualmente non è supportato. 

* Contenitori BLOB di Azure (solo archiviazione con ridondanza locale) 
* Bucket di AWS S3 
* Bucket di Google Cloud

### <a name="why-doesnt-avere-vfxt-support-all-nfs-filers"></a>Perché Avere vFXT non supporta tutti i filer NFS?

Anche se tutte le piattaforme NFS soddisfano gli stessi standard IETF, in pratica ogni implementazione ha i propri comportamenti anomali. Questi dettagli influiscono sul modo in cui Avere vFXT interagisce con il sistema di archiviazione. I sistemi supportati sono le piattaforme più diffuse nel marketplace.

### <a name="does-avere-vfxt-support-private-object-storage-such-as-swiftstack"></a>Avere vFXT supporta l'archiviazione di oggetti privati (ad esempio SwiftStack)?

Avere vFXT non supporta l'archiviazione di oggetti privati.

### <a name="how-can-i-get-a-specific-storage-product-under-support"></a>In che modo è possibile ottenere un prodotto di archiviazione specifico nell'ambito del piano di supporto?

Il supporto è basato sull'entità della richiesta sul campo. Se c'è un numero sufficiente di richieste potenzialmente redditizie per supportare una soluzione NAS, verrà presa in considerazione. Inoltrare la propria richiesta tramite il supporto di Azure.

### <a name="can-i-use-azure-blob-storage-as-a-core-filer"></a>È possibile usare un archivio BLOB di Azure come core filer?

Sì, Avere vFXT per Azure può usare un contenitore BLOB in blocchi come core filer cloud.  

### <a name="what-are-the-storage-account-requirements-for-a-blob-core-filer"></a>Quali sono i requisiti dell'account di archiviazione per un core filer BLOB?

L'account di archiviazione deve essere un account per utilizzo generico v2 (GPv2) configurato solo per l'archiviazione con ridondanza locale. L'archiviazione con ridondanza geografica e l'archiviazione con ridondanza della zona non sono supportate.

### <a name="can-i-use-archive-blob-storage"></a>È possibile usare lo spazio di archiviazione BLOB?

No. Il contratto di servizio per lo spazio di archiviazione non è compatibile con la directory in tempo reale e i requisiti di accesso ai file del sistema Avere vFXT. 

### <a name="can-i-use-cool-blob-storage"></a>È possibile usare l'archivio BLOB ad accesso sporadico?

È possibile usare il livello di archiviazione ad accesso sporadico, ma è opportuno ricordare che la frequenza delle operazioni sarà molto più elevata. 

### <a name="how-do-i-encrypt-the-blob-container"></a>Come è possibile crittografare il contenitore BLOB?

È possibile configurare la crittografia dei BLOB in Azure (scelta consigliata) o a livello del core filer di Avere vFXT.  

### <a name="can-i-use-my-own-encryption-key-for-a-blob-core-filer"></a>È possibile usare la propria chiave di crittografia per un core filer BLOB?

Per impostazione predefinita, i dati vengono crittografati tramite chiavi gestite da Microsoft per Archiviazione BLOB, tabelle e code di Azure e per File di Azure. È possibile usare la funzionalità BYOK (Bring Your Own Key) per la crittografia per Archiviazione BLOB e File di Azure. Se si sceglie di usare la crittografia Avere vFXT, è necessario usare la chiave generata da Avere e memorizzarla in locale. 

## <a name="purchasing"></a>Acquisto

### <a name="how-do-i-get-avere-vfxt-for-azure-licensing"></a>Come si ottiene la licenza di Avere vFXT per Azure?

La licenza di Avere vFXT per Azure è disponibile tramite Azure Marketplace. Registrarsi per un account Azure e quindi seguire le istruzioni in [Distribuire il cluster Avere vFXT](avere-vfxt-deploy.md) per creare un cluster Avere vFXT. 

### <a name="how-much-does-avere-vfxt-cost"></a>Quanto costa Avere vFXT?

In Azure non è prevista alcuna tariffa aggiuntiva per la licenza per l'uso dei cluster Avere vFXT. I clienti sono responsabili per i costi di archiviazione e gli altri costi per l'utilizzo di Azure.

### <a name="can-avere-vfxt-vms-be-run-as-low-priority"></a>Le macchine virtuali di Avere vFXT possono essere eseguite con priorità bassa?

No, i cluster Avere vFXT richiedono un servizio "Always On". I cluster possono essere disattivati quando non sono necessari. 

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare Avere vFXT per Azure, vedere gli articoli seguenti per informazioni su come pianificare e distribuire il sistema:

* [Pianificare il sistema Avere vFXT](avere-vfxt-deploy-plan.md)
* [Panoramica della distribuzione](avere-vfxt-deploy-overview.md)
* [Preparare la creazione di un cluster Avere vFXT](avere-vfxt-prereqs.md)
* [Distribuire il cluster Avere vFXT](avere-vfxt-deploy.md)

Per altre informazioni sulle funzionalità e i casi d'uso per Avere vFXT, visitare il sito di [Avere vFXT per Azure](https://azure.microsoft.com/services/storage/avere-vfxt/).
