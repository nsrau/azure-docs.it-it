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
ms.openlocfilehash: 4b5d2de2e9ccd44517e083a435e127bd5678f002
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60564342"
---
# <a name="ultra-disks-preview-managed-disks-for-azure-virtual-machine-workloads"></a>Dischi di managed disks extra (anteprima) per i carichi di lavoro di macchina virtuale di Azure

I dischi di Azure extra (anteprima) offre una velocità effettiva elevata, numero elevato di IOPS e di archiviazione su disco a bassa latenza coerente per le macchine virtuali IaaS di Azure. Questa nuova offerta fornisce prestazioni all'avanguardia con gli stessi livelli di disponibilità delle offerte di dischi esistenti. Vantaggi aggiuntivi di dischi extra includono la possibilità di modificare dinamicamente le prestazioni del disco con i carichi di lavoro senza la necessità di riavviare le macchine virtuali. Dischi extra sono adatti per carichi di lavoro a elevato utilizzo di dati, ad esempio SAP HANA, i database di livello superiore e i carichi di lavoro con intensa attività di transazione.

## <a name="ultra-disk-features"></a>Funzionalità disco extra

**Dischi gestiti**: Dischi extra sono disponibili solo come dischi gestiti. Ultra-dischi non possono essere distribuiti come un disco non gestito o un blob di pagine. Durante la creazione di un disco gestito, specificare lo sku disco digitare come UltraSSD_LRS e indicare le dimensioni del disco, il numero di IOPS e velocità effettiva necessaria, Azure crea e gestisce il disco per l'utente.  

**Macchine virtuali**: Dischi extra sono progettati per funzionare con Premium tutte le unità SSD abilitato gli SKU di macchina virtuale di Azure; Tuttavia, poiché è attualmente in anteprima, le macchine virtuali vengono ridimensionate come v3 ES/DS.

**Configurazione dinamica delle prestazioni**: Dischi extra consentono di modificare dinamicamente le prestazioni (IOPS e velocità effettiva) del disco con le esigenze del carico di lavoro senza dover riavviare le macchine virtuali.

## <a name="scalability-and-performance-targets"></a>Obiettivi di scalabilità e prestazioni

Quando si effettua il provisioning di un disco extra, si avrà la possibilità di configurare in modo indipendente la capacità e le prestazioni del disco. Dischi extra provengono in diverse dimensioni fisse da 4 GiB fino a 64 TiB e un modello di configurazione delle prestazioni flessibile che consente di configurare in modo indipendente di IOPS e velocità effettiva di funzionalità. Extra dischi possono essere usati solo come dischi dati. È consigliabile usare unità SSD Premium come dischi del sistema operativo.

Alcune funzionalità chiave dei dischi extra sono:

- Capacità del disco: Dischi extra offre una gamma di dimensioni dei dischi diversi da 4 GiB fino a 64 TiB.
- Operazioni di I/O al secondo (IOPS) del disco: I dischi extra supportano i limiti IOPS di 300 IOPS/GiB, fino a un massimo di 160 KB IOPS per disco. Per ottenere la quantità di operazioni di I/O al secondo di cui è stato effettuato il provisioning, assicurarsi che il numero di operazioni di I/O al secondo del disco selezionato sia inferiore al numero di operazioni di I/O al secondo della macchina virtuale. Il numero minimo di operazioni di I/O al secondo del disco è 100.
- Velocità effettiva del disco: Con i dischi extra, il limite di velocità effettiva di un singolo disco è 256 KB/s per IOPS, ognuna predisposte con un massimo di 2000 MBps per disco (in MBps = 10 ^ 6 byte al secondo). La velocità effettiva minima del disco è di 1 MiB.

La tabella seguente riepiloga le diverse configurazioni supportate per le diverse dimensioni dei dischi:  

### <a name="ultra-disks-managed-disk-offerings"></a>Ultra-dischi gestiti offerta su disco

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

Quando si usano dischi extra, verranno applicate le seguenti considerazioni di fatturazione:

- Dimensioni dei dischi gestiti
- Operazioni di I/O al secondo di cui è stato effettuato il provisioning per i dischi gestiti
- Velocità effettiva di cui è stato effettuato il provisioning per i dischi gestiti
- Tariffa di prenotazione Ultra-disco della macchina virtuale

### <a name="managed-disk-size"></a>Le dimensioni del disco gestito

I dischi gestiti vengono fatturati in base alle dimensioni di macchina virtuale scelte al momento del provisioning di una nuova macchina virtuale di Azure. Azure associa le dimensioni di cui è stato effettuato il provisioning (arrotondate per eccesso) all'offerta di dimensioni dei dischi più vicina. Per informazioni dettagliate sulle dimensioni dei dischi disponibili, vedere la tabella Obiettivi di scalabilità e prestazioni nella sezione precedente. Ogni disco è associato a una delle dimensioni del disco supportate di cui è stato effettuato il provisioning e la fatturazione verrà calcolata di conseguenza su base oraria. Ad esempio, se è stato effettuato il provisioning di un disco extra GiB 200 e la Elimina dopo 20 ore, verrà mappato all'offerta di 256 GiB le dimensioni del disco e ti verrà addebitato il 256 GiB per 20 ore. Questa fatturazione era basata sul consumo di ore di calcolo indipendentemente dal volume di dati effettivamente scritti sul disco.

### <a name="managed-disk-provisioned-iops"></a>IOPS predisposte nel disco gestito

Le operazioni di I/O al secondo (IOPS) indicano il numero di richieste inviate dall'applicazione ai dischi al secondo. Un'operazione di input/output può essere di lettura o di scrittura, sequenziale o casuale. A seconda delle dimensioni del disco o del numero di dischi collegati alla macchina virtuale, il numero medio di IOPS viene fatturato su base oraria. Per informazioni dettagliate sulle operazioni di I/O al secondo disponibili per i dischi, vedere la tabella nella sezione Obiettivi di scalabilità e prestazioni precedente.

### <a name="managed-disk-provisioned-throughput"></a>Velocità effettiva con provisioning del disco gestito

La velocità effettiva indica la quantità di dati che l'applicazione invia ai dischi in un intervallo specifico, misurata in byte/secondo. Se l'applicazione esegue operazioni di input/output di grandi dimensioni, necessita di una velocità effettiva elevata.  

Come illustrato dalla formula seguente, esiste una relazione tra la velocità effettiva e le operazioni di I/O al secondo (IOPS):  IOPS x dimensioni I/O = velocità effettiva

È quindi importante determinare i valori ottimali per la velocità effettiva e IOPS richiesti dall'applicazione. I tentativi di ottimizzazione di uno dei valori influiscono anche sull'altro. È consigliabile partire da una velocità effettiva corrispondente alle dimensioni I/O di 16 KiB e apportare modifiche se è necessaria una velocità effettiva maggiore.

Per informazioni dettagliate sulla velocità effettiva del disco supportate nei dischi extra, vedere la tabella nella sezione precedente relativa obiettivi di scalabilità e prestazioni. Analogamente alle dimensioni dei dischi e alle operazioni di I/O al secondo, la velocità effettiva di cui è stato effettuato il provisioning viene fatturata su base oraria, per MBps di cui è stato effettuato il provisioning.

### <a name="ultra-disk-vm-reservation-fee"></a>Tariffa di prenotazione Ultra-disco della macchina virtuale

Viene introdotto una funzionalità nella macchina virtuale che indica che la macchina virtuale è Ultra-disco compatibile. Una macchina virtuale compatibile con dischi Ultra alloca una capacità di larghezza di banda dedicata tra l'istanza della macchina virtuale di calcolo e l'unità di scala di archiviazione a blocchi per ottimizzare le prestazioni e ridurre la latenza. L'aggiunta di questa capacità alla macchina virtuale comporta un addebito per la prenotazione, che viene applicato solo se si abilita la funzionalità per i dischi Ultra nella macchina virtuale senza collegare un disco Ultra. Quando un disco extra è collegato alla macchina virtuale compatibile extra, questo addebito non verrà applicato. L'addebito viene calcolato per ogni vCPU di cui è stato effettuato il provisioning nella macchina virtuale.

Fare riferimento al [pagina dei prezzi Azure Disks](https://azure.microsoft.com/pricing/details/managed-disks/) per i nuovi extra dischi prezzo dettagli disponibili in anteprima limitata.

### <a name="ultra-disk-preview-scope-and-limitations"></a>Limitazioni e ambito di disco Ultra-anteprima

In fase di anteprima, ultra-dischi:

- Saranno inizialmente supportati nell'area Stati Uniti orientali 2 in una singola zona di disponibilità  
- Può essere utilizzato solo con le zone di disponibilità (set di disponibilità e le distribuzioni di singola macchina virtuale all'esterno saranno le zone non hanno la possibilità di collegare un disco extra)
- Sono supportati solo nelle macchine virtuali ES/DS v3
- Sono disponibili solo come dischi dati e supportano solo dimensioni di settori fisici di 4k  
- Possono essere creati solo come dischi vuoti  
- Attualmente possono essere distribuiti solo usando modelli di Resource Manager, l'interfaccia della riga di comando e Python SDK.
- Al momento non supportano snapshot di dischi, immagini di macchine virtuali, set di disponibilità, set di scalabilità di macchine virtuali e Crittografia dischi di Azure.
- Al momento non supportano l'integrazione con Backup di Azure o Azure Site Recovery.
- Come per la  [maggior parte delle versioni di anteprima](https://azure.microsoft.com/support/legal/preview-supplemental-terms/), questa funzionalità non deve essere usata per i carichi di lavoro in produzione fino al momento della disponibilità generale.
