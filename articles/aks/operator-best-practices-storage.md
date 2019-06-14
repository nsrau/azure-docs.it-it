---
title: Procedure consigliate per l'operatore - Archiviazione nel servizio Azure Kubernetes (AKS)
description: Procedure consigliate per l'operatore del cluster per l'archiviazione, la crittografia dei dati e i backup nel servizio Azure Kubernetes
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 5/6/2019
ms.author: iainfou
ms.openlocfilehash: e7f45a3a0e62b2b559002b71bd8816e050f062ab
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65072640"
---
# <a name="best-practices-for-storage-and-backups-in-azure-kubernetes-service-aks"></a>Procedure consigliate per archiviazione e backup nel servizio Azure Kubernetes

Quando si creano e si gestiscono cluster nel servizio Azure Kubernetes, spesso le applicazioni hanno bisogno di risorse di archiviazione. È importante comprendere le esigenze a livello di prestazioni e i metodi di accesso per i pod in modo da poter fornire le risorse di archiviazione appropriate alle applicazioni. Le dimensioni del nodo AKS possono influire su queste scelte di archiviazione. Occorre anche pianificare il backup e il test del processo di ripristino per le risorse di archiviazione associate.

Questo articolo sulle procedure consigliate è incentrato sulle considerazioni relative all'archiviazione per gli operatori del cluster. Contenuto dell'articolo:

> [!div class="checklist"]
> * Tipi di archiviazione disponibili
> * Come ridimensionare correttamente i nodi AKS per le prestazioni dell'archiviazione
> * Differenze tra provisioning dinamico e statico dei volumi
> * Modalità di backup e protezione dei volumi di dati

## <a name="choose-the-appropriate-storage-type"></a>Scelta del tipo di archiviazione appropriato

**Indicazioni sulle procedure consigliate** - Comprendere le esigenze dell'applicazione per scegliere il tipo di archiviazione più adatto. Usare l'archiviazione basata su SSD a prestazioni elevate per i carichi di lavoro di produzione. Pianificare l'archiviazione basata su rete quando sono necessarie più connessioni simultanee.

Le applicazioni spesso richiedono tipi e velocità di archiviazione diversi. Le applicazioni hanno bisogno di un tipo di archiviazione che si connette a singoli pod oppure che viene condivisa tra più pod? Lo spazio di archiviazione serve per l'accesso di sola lettura ai dati oppure per la scrittura di grandi quantità di dati strutturati? Queste esigenze di archiviazione determinano il tipo di archiviazione più appropriato da usare.

La tabella seguente descrive i tipi di archiviazione disponibili e le relative funzionalità:

| Caso d'uso | Plug-in volume | Una sola operazione di lettura/scrittura | Molte operazioni di sola lettura | Molte operazioni di lettura/scrittura | Supporto per i contenitori Windows Server |
|----------|---------------|-----------------|----------------|-----------------|--------------------|
| Configurazione condivisa       | File di Azure   | Yes | Sì | Sì | Yes |
| Dati di app strutturati        | Dischi di Azure   | Yes | No  | No  | Yes |
| Dati di app, condivisioni di sola lettura | [Dysk (anteprima)][dysk] | Yes | Sì | No  | No |
| Dati non strutturati, operazioni sui file system | [BlobFuse (anteprima)][blobfuse] | Yes | Sì | Sì | No |

I due principali tipi di archiviazione forniti per i volumi nel servizio Azure Kubernetes sono supportati da Dischi di Azure o File di Azure. Per migliorare la sicurezza, per impostazione predefinita entrambi i tipi di archiviazione usano la crittografia del servizio di archiviazione, che crittografa i dati inattivi. Attualmente i dischi non possono essere crittografati mediante Crittografia dischi di Azure a livello di nodo AKS.

File di Azure è attualmente disponibile nel livello di prestazioni Standard. Dischi di Azure è disponibile nei livelli di prestazioni Standard e Premium:

- I dischi *Premium* sono basati su dischi SSD a prestazioni elevate. Sono consigliati per tutti i carichi di lavoro di produzione.
- I dischi *Standard* sono basati su normali dischi HDD e sono adatti per i dati di archivio o i dati a cui si accede di rado.

Comprendere le esigenze di prestazioni e gli schemi di accesso dell'applicazione per scegliere il livello di archiviazione appropriato. Per altre informazioni sulle dimensioni dei dischi gestiti e sui livelli di prestazioni, vedere [Panoramica di Azure Managed Disks][managed-disks].

### <a name="create-and-use-storage-classes-to-define-application-needs"></a>Creare e usare classi di archiviazione per definire le esigenze delle applicazioni

Il tipo di archiviazione usato viene definito tramite le *classi di archiviazione* di Kubernetes. Viene quindi fatto riferimento alla classe di archiviazione nel pod o nella specifica di distribuzione. Queste definizioni contribuiscono alla creazione del tipo di archiviazione appropriato e alla relativa connessione ai pod. Per altre informazioni, vedere [Classi di archiviazione][aks-concepts-storage-classes].

## <a name="size-the-nodes-for-storage-needs"></a>Ridimensionare i nodi per le esigenze di archiviazione

**Indicazioni sulle procedure consigliate** - Ogni dimensione di nodo supporta un numero massimo di dischi. Dimensioni diverse forniscono quantità diverse di spazio di archiviazione locale e larghezza di banda della rete. Pianificare le esigenze dell'applicazione per distribuire le dimensioni dei nodi appropriate.

I nodi AKS vengono eseguiti come macchine virtuali di Azure. Sono disponibili diversi tipi e dimensioni di macchina virtuale. Ogni dimensione di macchina virtuale fornisce una quantità diversa di risorse di base, come la CPU e la memoria. A queste dimensioni di macchina virtuale può essere collegato un numero massimo di dischi. Le prestazioni dell'archiviazione variano anche tra le dimensioni di macchina virtuale per le operazioni di I/O al secondo massime nei dischi locali e collegati.

Se le applicazioni richiedono Dischi di Azure come soluzione di archiviazione, pianificare e scegliere una dimensione di macchina virtuale del nodo appropriata. La quantità di CPU e memoria non è l'unico fattore da considerare nella scelta di una dimensione di macchina virtuale. Anche le funzionalità di archiviazione sono importanti. Ad esempio, le dimensioni di macchina virtuale *Standard_B2ms* e *Standard_DS2_v2* offrono una quantità simile di risorse di CPU e memoria. Ma le potenziali prestazioni di archiviazione sono diverse, come illustrato nella tabella seguente:

| Tipo e dimensioni del nodo | vCPU | Memoria (GiB) | Numero massimo di dischi dati | Operazioni di I/O al secondo del disco senza memorizzazione nella cache | Velocità effettiva massima senza memorizzazione nella cache (MBps) |
|--------------------|------|--------------|----------------|------------------------|--------------------------------|
| Standard_B2ms      | 2    | 8            | 4              | 1\.920                  | 22,5                           |
| Standard_DS2_v2    | 2    | 7            | 8              | 6\.400                  | 96                             |

Qui la dimensione *Standard_DS2_v2* consente il doppio del numero di dischi collegati e fornisce da tre a quattro volte la quantità di operazioni di I/O al secondo e velocità effettiva del disco. Se si considerassero solo le risorse di calcolo di base e si confrontassero i costi, si potrebbe scegliere la dimensione di macchina virtuale *Standard_B2ms* e ottenere prestazioni di archiviazione insufficienti e limitazioni. È opportuno consultare il team di sviluppo delle applicazioni per comprendere le esigenze di capacità e prestazioni di archiviazione. Scegliere quindi la dimensione di macchina virtuale appropriata per i nodi AKS in modo da soddisfare o superare queste esigenze di prestazioni. Stabilire regolarmente una baseline delle applicazioni per modificare la dimensione di macchina virtuale in base alle necessità.

Per altre informazioni sulle dimensioni di macchina virtuale disponibili, vedere [Dimensioni delle macchine virtuali Linux in Azure][vm-sizes].

## <a name="dynamically-provision-volumes"></a>Effettuare il provisioning dinamico dei volumi

**Indicazioni sulle procedure consigliate** - Per ridurre il sovraccarico di gestione e consentire la scalabilità, evitare di creare e assegnare in modo statico volumi permanenti. Usare invece il provisioning dinamico. Nelle classi di archiviazione definire i criteri di recupero appropriati per ridurre al minimo i costi di archiviazione non necessari dopo l'eliminazione dei pod.

Quando occorre collegare risorse di archiviazione ai pod, si usano i volumi permanenti. Questi volumi possono essere creati in modo manuale o dinamico. La creazione manuale di volumi permanenti aumenta il carico di gestione e limita le possibilità di scalabilità. Usare quindi il provisioning dinamico dei volumi permanenti per semplificare la gestione dell'archiviazione e consentire una scalabilità delle applicazioni in entrambe le direzioni, in base alle necessità.

![Attestazioni di volume permanente in un cluster del servizio Azure Kubernetes](media/concepts-storage/persistent-volume-claims.png)

Un'attestazione di volume permanente consente di creare dinamicamente le risorse di archiviazione necessarie. I dischi di Azure sottostanti vengono creati man mano che i pod li richiedono. Nella definizione del pod si richiede che un volume venga creato e collegato a un determinato percorso di montaggio.

Per i concetti relativi alla creazione dinamica e all'uso dei volumi, vedere [Attestazioni di volume permanente][aks-concepts-storage-pvcs].

Per una dimostrazione di questi volumi in azione, vedere come creare in modo dinamico e usare un volume permanente con [Dischi di Azure][dynamic-disks] o [File di Azure][dynamic-files].

Nell'ambito delle definizioni di classe di archiviazione impostare i criteri *reclaimPolicy* appropriati. I criteri reclaimPolicy controllano il comportamento della risorsa di archiviazione di Azure sottostante quando il pod viene eliminato e il volume permanente potrebbe non essere più necessario. La risorsa di archiviazione sottostante può essere eliminata o conservata per l'uso con un pod futuro. I criteri reclaimPolicy possono essere impostati per *conservare* o *eliminare*. Comprendere le esigenze delle applicazioni e implementare regolari controlli dello spazio di archiviazione che viene conservato per ridurre al minimo la quantità di spazio di archiviazione inutilizzato che viene fatturato.

Per altre informazioni sulle opzioni delle classi di archiviazione, vedere [Classi di archiviazione][reclaim-policy].

## <a name="secure-and-back-up-your-data"></a>Proteggere ed eseguire il backup dei dati

**Procedure consigliate** : backup di dati usando uno strumento appropriato per il tipo di archiviazione, ad esempio Velero o da Azure Site Recovery. Verificare l'integrità e la sicurezza di tali backup.

Quando le applicazioni archiviano e utilizzano dati salvati in modo permanente su dischi o in file, è necessario eseguire regolari backup o snapshot di tali dati. Dischi di Azure supporta l'uso di tecnologie snapshot integrate. Potrebbe essere necessario un hook che consenta all'applicazione di scaricare le scritture su disco prima di eseguire l'operazione snapshot. [Velero] [ velero] può eseguire il backup di volumi permanenti con le risorse del cluster aggiuntivi e le configurazioni. Se non è possibile [rimuovere lo stato dall'applicazione][remove-state], eseguire il backup dei dati di volumi permanenti e testare regolarmente le operazioni di ripristino per verificare l'integrità dei dati e i processi necessari.

Comprendere le limitazioni dei diversi approcci ai backup dei dati e la necessità o meno di disattivare i dati prima di creare lo snapshot. Non sempre i backup dei dati consentono di ripristinare l'ambiente applicativo della distribuzione cluster. Per altre informazioni su questi scenari, vedere le [procedure consigliate per continuità aziendale e ripristino di emergenza nel servizio Azure Kubernetes][best-practices-multi-region].

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono state illustrate in particolare le procedure consigliate di archiviazione nel servizio Azure Kubernetes. Per altre informazioni di base sull'archiviazione in Kubernetes, vedere [Opzioni di archiviazione per le applicazioni nel servizio Kubernetes di Azure (AKS)][aks-concepts-storage].

<!-- LINKS - External -->
[velero]: https://github.com/heptio/velero
[dysk]: https://github.com/Azure/kubernetes-volume-drivers/tree/master/flexvolume/dysk
[blobfuse]: https://github.com/Azure/azure-storage-fuse

<!-- LINKS - Internal -->
[aks-concepts-storage]: concepts-storage.md
[vm-sizes]: ../virtual-machines/linux/sizes.md
[dynamic-disks]: azure-disks-dynamic-pv.md
[dynamic-files]: azure-files-dynamic-pv.md
[reclaim-policy]: concepts-storage.md#storage-classes
[aks-concepts-storage-pvcs]: concepts-storage.md#persistent-volume-claims
[aks-concepts-storage-classes]: concepts-storage.md#storage-classes
[managed-disks]: ../virtual-machines/linux/managed-disks-overview.md
[best-practices-multi-region]: operator-best-practices-multi-region.md
[remove-state]: operator-best-practices-multi-region.md#remove-service-state-from-inside-containers
