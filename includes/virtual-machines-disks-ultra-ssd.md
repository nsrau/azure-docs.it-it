---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/24/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 050308e1c8de160f1671ded991e550087299ae2f
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285726"
---
# <a name="ultra-ssd-preview-managed-disks-for-azure-virtual-machine-workloads"></a>Dischi gestiti SSD Ultra (anteprima) per carichi di lavoro delle macchine virtuali di Azure

Le unità SSD Ultra di Azure (anteprima) offrono una velocità effettiva elevata, un numero elevato di operazioni di I/O al secondo e archiviazione su disco con bassa latenza coerente per le macchine virtuali IaaS di Azure. Questa nuova offerta fornisce prestazioni all'avanguardia con gli stessi livelli di disponibilità delle offerte di dischi esistenti. I vantaggi aggiuntivi delle unità SSD Ultra includono la possibilità di modificare dinamicamente le prestazioni del disco in base ai carichi di lavoro, senza dover riavviare le macchine virtuali. Le unità SSD Ultra sono adatte per carichi di lavoro a elevato utilizzo di dati, come SAP HANA, database di alto livello e carichi di lavoro con numerose transazioni.

## <a name="ultra-ssd-features"></a>Caratteristiche delle unità SSD Ultra

**Dischi gestiti**: le unità SSD Ultra sono disponibili solo come dischi gestiti. Le unità SSD Ultra non possono essere distribuite come dischi non gestiti o BLOB di pagine. Quando si crea un disco gestito, si specifica UltraSSD_LRS come tipo di SKU del disco e si indicano le dimensioni del disco, le operazioni di I/O al secondo e la velocità effettiva necessarie, quindi Azure crea e gestisce automaticamente il disco.  

**Macchine virtuali**: le unità SSD ultra sono progettate per funzionare con tutti gli SKU di macchina virtuale di Azure abilitati per Premium SSD. Tuttavia, trattandosi di una funzionalità attualmente in anteprima, le macchine virtuali vengono impostate con le dimensioni ES/DS v3.

**Configurazione dinamica delle prestazioni**: le unità SSD Ultra consentono di modificare dinamicamente le prestazioni (operazioni di I/O al secondo e velocità effettiva) del disco in base alle esigenze dei carichi di lavoro, senza riavviare le macchine virtuali.

## <a name="scalability-and-performance-targets"></a>Obiettivi di scalabilità e prestazioni

Quando si effettua il provisioning di un'unità SSD Ultra, è possibile configurare in modo indipendente la capacità e le prestazioni del disco. Le unità SSD Ultra sono disponibili con diverse dimensioni fisse, da 4 GiB fino a 64 TiB, e offrono un modello di configurazione delle prestazioni flessibile che consente di configurare in modo indipendente il numero di operazioni di I/O al secondo e la velocità effettiva. Le unità SSD Ultra possono essere usate solo come dischi dati. È consigliabile usare unità SSD Premium come dischi del sistema operativo.

Ecco alcune delle caratteristiche principali delle unità SSD Ultra:

- Capacità del disco: le unità SSD Ultra permettono di scegliere tra una gamma di dimensioni dei dischi diverse, da 4 GiB fino a 64 TiB.
- Operazioni di I/O al secondo (IOPS) del disco: le unità SSD Ultra supportano limiti di operazioni di I/O al secondo pari a 300 IOPS/GiB, fino a un massimo di 160.000 IOPS per disco. Per ottenere la quantità di operazioni di I/O al secondo di cui è stato effettuato il provisioning, assicurarsi che il numero di operazioni di I/O al secondo del disco selezionato sia inferiore al numero di operazioni di I/O al secondo della macchina virtuale. Il numero minimo di operazioni di I/O al secondo del disco è 100.
- Velocità effettiva del disco: con le unità SSD Ultra, il limite di velocità effettiva di un singolo disco è di 256 KiB/s per le operazioni di I/O al secondo di cui è stato effettuato il provisioning, fino a un massimo di 2000 MBps per disco (dove MBps = 10^6 byte al secondo). La velocità effettiva minima del disco è di 1 MiB.

La tabella seguente riepiloga le diverse configurazioni supportate per le diverse dimensioni dei dischi:  

### <a name="ultra-ssd-managed-disk-offerings"></a>Offerte di dischi gestiti SSD Ultra

|Dimensioni disco (GiB)  |Limiti operazioni di I/O al secondo  |Limite velocità effettiva (MBps)  |
|---------|---------|---------|
|4     |1200         |300         |
|8     |2400         |600         |
|16     |4800         |1200         |
|32     |9.600         |2.000         |
|64     |19.200         |2.000         |
|128     |38.400         |2.000         |
|256     |76.800         |2.000         |
|512     |80.000         |2.000         |
|1.024-65.536 (dimensioni in questo intervallo con aumento con incrementi di 1 TiB)     |160.000         |2.000         |

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Quando si usano le unità SSD Ultra, tenere presenti le considerazioni seguenti relative alla fatturazione:

- Dimensioni dei dischi gestiti
- Operazioni di I/O al secondo di cui è stato effettuato il provisioning per i dischi gestiti
- Velocità effettiva di cui è stato effettuato il provisioning per i dischi gestiti
- Tariffa di prenotazione delle macchine virtuali con unità SSD Ultra

### <a name="managed-disk-size"></a>Dimensioni dei dischi gestiti

I dischi gestiti vengono fatturati in base alle dimensioni di macchina virtuale scelte al momento del provisioning di una nuova macchina virtuale di Azure. Azure associa le dimensioni di cui è stato effettuato il provisioning (arrotondate per eccesso) all'offerta di dimensioni dei dischi più vicina. Per informazioni dettagliate sulle dimensioni dei dischi disponibili, vedere la tabella Obiettivi di scalabilità e prestazioni nella sezione precedente. Ogni disco è associato a una delle dimensioni del disco supportate di cui è stato effettuato il provisioning e la fatturazione verrà calcolata di conseguenza su base oraria. Se, ad esempio, è stato effettuato il provisioning di un disco SSD Ultra da 200 GiB che è stato eliminato dopo 20 ore, il disco verrà associato all'offerta di dischi di dimensioni pari a 256 GiB e la fattura includerà addebiti per 256 GiB per 20 ore, Questa fatturazione era basata sul consumo di ore di calcolo indipendentemente dal volume di dati effettivamente scritti sul disco.

### <a name="managed-disk-provisioned-iops"></a>Operazioni di I/O al secondo di cui è stato effettuato il provisioning per i dischi gestiti

Le operazioni di I/O al secondo (IOPS) indicano il numero di richieste inviate dall'applicazione ai dischi al secondo. Un'operazione di input/output può essere di lettura o di scrittura, sequenziale o casuale. A seconda delle dimensioni del disco o del numero di dischi collegati alla macchina virtuale, il numero medio di IOPS viene fatturato su base oraria. Per informazioni dettagliate sulle operazioni di I/O al secondo disponibili per i dischi, vedere la tabella nella sezione Obiettivi di scalabilità e prestazioni precedente.

### <a name="managed-disk-provisioned-throughput"></a>Velocità effettiva di cui è stato effettuato il provisioning per i dischi gestiti

La velocità effettiva indica la quantità di dati che l'applicazione invia ai dischi in un intervallo specifico, misurata in byte/secondo. Se l'applicazione esegue operazioni di input/output di grandi dimensioni, necessita di una velocità effettiva elevata.  

Come illustrato dalla formula seguente, c'è una relazione tra la velocità effettiva e le operazioni di I/O al secondo: IOPS x dimensioni I/O = Velocità effettiva

È quindi importante determinare i valori ottimali per la velocità effettiva e IOPS richiesti dall'applicazione. I tentativi di ottimizzazione di uno dei valori influiscono anche sull'altro. È consigliabile partire da una velocità effettiva corrispondente alle dimensioni I/O di 16 KiB e apportare modifiche se è necessaria una velocità effettiva maggiore.

Per informazioni dettagliate sulla velocità effettiva dei dischi supportata per le unità SSD Ultra, vedere la tabella nella sezione Obiettivi di scalabilità e prestazioni precedente. Analogamente alle dimensioni dei dischi e alle operazioni di I/O al secondo, la velocità effettiva di cui è stato effettuato il provisioning viene fatturata su base oraria, per MBps di cui è stato effettuato il provisioning.

### <a name="ultra-ssd-vm-reservation-fee"></a>Tariffa di prenotazione delle macchine virtuali con unità SSD Ultra

Verrà introdotta una funzionalità nella macchina virtuale che indica che la macchina virtuale è compatibile con le unità SSD Ultra. Una macchina virtuale compatibile con le unità SSD Ultra alloca una capacità di larghezza di banda dedicata tra l'istanza della macchina virtuale di calcolo e l'unità di scala di archiviazione a blocchi per ottimizzare le prestazioni e ridurre la latenza. L'aggiunta di questa capacità alla macchina virtuale comporta un addebito per la prenotazione, che viene applicato solo se si abilita la funzionalità SSD Ultra nella macchina virtuale senza collegare un disco SSD Ultra. Quando alla macchina virtuale compatibile con le unità SSD Ultra viene collegato un disco SSD Ultra, questo addebito non viene applicato. L'addebito viene calcolato per ogni vCPU di cui è stato effettuato il provisioning nella macchina virtuale.

Vedere la [pagina dei prezzi dei dischi di Azure](https://azure.microsoft.com/pricing/details/managed-disks/) per informazioni sui prezzi dei nuovi dischi SSD Ultra disponibili in versione di anteprima limitata.

### <a name="ultra-ssd-preview-scope-and-limitations"></a>Limitazioni e ambito della versione di anteprima delle unità SSD Ultra

In fase di anteprima, i dischi SSD Ultra:

- Saranno inizialmente supportati nell'area Stati Uniti orientali 2 in una singola zona di disponibilità  
- Possono essere usati solo con le zone di disponibilità (i set di disponibilità e le distribuzioni di macchine virtuali singole all'esterno delle zone non consentono di collegare un disco SSD Ultra)
- Sono supportati solo nelle macchine virtuali ES/DS v3
- Sono disponibili solo come dischi dati e supportano solo dimensioni di settori fisici di 4k  
- Possono essere creati solo come dischi vuoti  
- Attualmente possono essere distribuiti solo usando modelli di Resource Manager, l'interfaccia della riga di comando e Python SDK.
- Al momento non supportano snapshot di dischi, immagini di macchine virtuali, set di disponibilità, set di scalabilità di macchine virtuali e Crittografia dischi di Azure.
- Al momento non supportano l'integrazione con Backup di Azure o Azure Site Recovery.
- Come per la  [maggior parte delle versioni di anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), questa funzionalità non deve essere usata per i carichi di lavoro in produzione fino al momento della disponibilità generale.
