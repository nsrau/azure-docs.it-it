---
title: Selezionare un tipo di disco per le macchine virtuali IaaS di Azure - dischi gestiti
description: Informazioni sui tipi di dischi di Azure disponibili per le macchine virtuali, tra cui dischi Ultra, SSD Premium, SSD Standard e HDD Standard.
author: roygara
ms.author: rogarana
ms.date: 06/03/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: bfc1a818aeb85eca04faca713d4c7acbbce3626a
ms.sourcegitcommit: 07166a1ff8bd23f5e1c49d4fd12badbca5ebd19c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2020
ms.locfileid: "90088494"
---
# <a name="what-disk-types-are-available-in-azure"></a>Quali tipi di dischi sono disponibili in Azure?

Azure Managed Disks offre attualmente quattro tipi di dischi, ciascuno dei quali è destinato a scenari specifici del cliente.

## <a name="disk-comparison"></a>Confronto dei dischi

La tabella seguente fornisce un confronto tra dischi Ultra, unità SSD Premium, unità SSD standard e unità disco rigido standard (HDD) per Managed disks che consentono di decidere cosa usare.

| Dettagli | Disco Ultra | SSD Premium | SSD Standard | HDD Standard |
| ------ | ---------- | ----------- | ------------ | ------------ |
|Tipo di disco   |SSD   |SSD   |SSD   |HDD   |
|Scenario   |Carichi di lavoro intensivo di i/o, come [SAP Hana](workloads/sap/hana-vm-operations-storage.md), database di livello superiore (ad esempio, SQL, Oracle) e altri carichi di lavoro con transazioni pesanti.   |Carichi di lavoro di produzione con requisiti particolari di prestazioni   |Server Web, applicazioni aziendali usate poco di frequente e sviluppo/test   |Backup, carichi di lavoro non critici, accesso poco frequente   |
|Dimensioni massime disco   |65.536 gibibyte (GiB)    |32.767 GiB    |32.767 GiB   |32.767 GiB   |
|Velocità effettiva massima   |2\.000 MB/s    |900 MB/s   |750 MB/s   |500 MB/s   |
|Operazioni di I/O al secondo max   |160.000    |20.000   |6.000   |2.000   |

## <a name="ultra-disk"></a>Disco Ultra

I dischi Ultra di Azure offrono una velocità effettiva elevata, un numero elevato di operazioni di I/O al secondo e archiviazione su disco con bassa latenza coerente per le macchine virtuali IaaS di Azure. Alcuni vantaggi aggiuntivi di ultra disks includono la possibilità di modificare dinamicamente le prestazioni del disco, insieme ai carichi di lavoro, senza dover riavviare le macchine virtuali (VM). I dischi Ultra sono idonei per carichi di lavoro a elevato utilizzo di dati, come SAP HANA, database di alto livello e carichi di lavoro con numerose transazioni. I dischi Ultra possono essere usati solo come dischi dati. È consigliabile usare dischi SSD Premium come dischi del sistema operativo.

### <a name="performance"></a>Prestazioni

Quando si effettua il provisioning di un disco Ultra, è possibile configurare in modo indipendente la capacità e le prestazioni del disco. I dischi Ultra includono diverse dimensioni fisse, comprese tra 4 GiB e 64 TiB, e dispongono di un modello di configurazione delle prestazioni flessibile che consente di configurare in modo indipendente IOPS e velocità effettiva.

Di seguito sono riportate alcune funzionalità chiave di dischi Ultra:

- Capacità disco: la capacità dei dischi Ultra è compresa tra 4 GiB e 64 TiB.
- IOPS del disco: i dischi Ultra supportano i limiti di IOPS di 300 IOPS/GiB, fino a un massimo di 160 K di IOPS per disco. Per ottenere il valore di IOPS di cui è stato effettuato il provisioning, verificare che le operazioni di i/o al secondo del disco selezionate siano inferiori al limite di IOPS Il numero minimo di operazioni di i/o al secondo per disco è di 2 IOPS/GiB, con una linea di base complessiva minima di 100 IOPS. Se, ad esempio, si dispone di un disco con 4 GiB Ultra, si avrà almeno 100 IOPS, anziché otto IOPS.
- Velocità effettiva del disco: con dischi Ultra, il limite di velocità effettiva di un singolo disco è 256 KiB/s per ogni IOPS di cui è stato effettuato il provisioning, fino a un massimo di 2000 MBps per disco (dove MBps = 10 ^ 6 byte al secondo). La velocità effettiva minima garantita per ogni disco è 4KiB/s per ogni IOPS di cui è stato effettuato il provisioning, con una linea di base complessiva minima di 1 MBps.
- I dischi Ultra supportano la regolazione degli attributi relativi alle prestazioni del disco (IOPS e velocità effettiva) in fase di esecuzione senza scollegare il disco dalla macchina virtuale. Dopo l'esecuzione di un'operazione di ridimensionamento delle prestazioni in un disco, può essere necessario attendere fino a un'ora prima che la modifica abbia effetto. È previsto un limite di quattro operazioni di ridimensionamento delle prestazioni in un intervallo di 24 ore. È possibile che un'operazione di ridimensionamento delle prestazioni abbia esito negativo a causa della mancanza di capacità della larghezza di banda delle prestazioni.

### <a name="disk-size"></a>Dimensioni del disco

|Dimensioni disco (GiB)  |Limite IOPS  |Limite velocità effettiva (MBps)  |
|---------|---------|---------|
|4     |1.200         |300         |
|8     |2400         |600         |
|16     |4.800         |1.200         |
|32     |9.600         |2.000         |
|64     |19.200         |2\.000         |
|128     |38.400         |2.000         |
|256     |76.800         |2.000         |
|512     |80.000         |2\.000         |
|1.024-65.536 (dimensioni in questo intervallo con aumento con incrementi di 1 TiB)     |160.000         |2.000         |

### <a name="ga-scope-and-limitations"></a>Ambito e limitazioni di GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]


Per iniziare a usare i dischi Ultra, vedere l'articolo sull'argomento relativo all'uso di [dischi ultra di Azure](disks-enable-ultra-ssd.md).

## <a name="premium-ssd"></a>SSD Premium

I dischi SSD Premium di Azure offrono prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali (VM) con carichi di lavoro con I/O intensivo. È possibile migrare i dischi delle VM esistenti in SSD Premium per trarre vantaggio dalla velocità e dalle prestazioni di questi dischi. I dischi SSD Premium sono idonei per applicazioni di produzione cruciali. Le unità SSD Premium possono essere usate solo con le serie di VM che sono compatibili con archiviazione Premium.

Per altre informazioni sui singoli tipi e dimensioni delle VM in Azure per Windows o Linux, incluse le dimensioni compatibili con archiviazione Premium, vedere [dimensioni per le macchine virtuali in Azure](sizes.md). Da questo articolo è necessario controllare ogni singolo articolo sulle dimensioni della VM per determinare se è compatibile con archiviazione Premium.

### <a name="disk-size"></a>Dimensioni del disco
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

Quando si effettua il provisioning di un disco di archiviazione Premium, a differenza di Archiviazione Standard, vengono garantiti livelli di capacità, IOPS e velocità effettiva del disco. Se ad esempio si crea un disco P50, Azure effettua il provisioning di 4.095 GB di capacità di archiviazione, 7.500 IOPS e 250 MB/s di velocità effettiva per tale disco. L'applicazione può usare la totalità o una della capacità e delle prestazioni. I dischi SSD Premium sono progettati per offrire basse latenze di pochissimi millisecondi, nonché il numero di operazioni di I/O al secondo e la velocità effettiva di destinazione descritti nella tabella precedente per il 99,9% del tempo.

## <a name="bursting"></a>Espansione nel

SSD Premium dimensioni inferiori a P30 offrono ora l'espansione del disco e possono aumentare le operazioni di i/o al secondo per ogni disco fino a 3.500 e la larghezza di banda fino a 170 Mbps. L'espansione è automatizzata e funziona in base a un sistema di credito. I crediti vengono accumulati automaticamente in un bucket di espansione quando il traffico del disco è inferiore alla destinazione di prestazioni con provisioning e i crediti vengono usati automaticamente quando il traffico supera la soglia, fino al limite massimo di picchi. Il limite massimo di impulsi definisce il limite di IOPS del disco & larghezza di banda anche se sono presenti crediti di espansione da utilizzare. Il provisioning del disco garantisce una maggiore tolleranza sulle modifiche imprevedibili dei modelli di i/o. È possibile sfruttarlo per l'avvio del disco del sistema operativo e le applicazioni con traffico irregolare.    

Il supporto per l'espansione dei dischi verrà abilitato per le nuove distribuzioni di dimensioni del disco applicabili per impostazione predefinita, senza alcuna azione da utente. Per i dischi esistenti con le dimensioni applicabili, è possibile abilitare l'espansione con una delle due opzioni: scollegare e ricollegare il disco oppure arrestare e riavviare la macchina virtuale collegata. Tutte le dimensioni del disco applicabili a impulsi iniziano con un bucket di credito a espansione completa quando il disco è collegato a una macchina virtuale che supporta una durata massima al limite massimo di 30 minuti. Per altre informazioni sul funzionamento del proromping nei dischi di Azure, vedere [SSD Premium](linux/disk-bursting.md)l'espansione. 

### <a name="transactions"></a>Transazioni

Per le unità SSD Premium, ogni operazione di I/O inferiore o uguale a 256 KiB di velocità effettiva viene considerata una singola operazione di I/O. Le operazioni di i/O superiori a 256 KiB di velocità effettiva sono considerate più I/O di dimensioni 256 KiB.

## <a name="standard-ssd"></a>SSD Standard

Le unità SSD Standard di Azure sono un'opzione di archiviazione conveniente, ottimizzata per carichi di lavoro che richiedono prestazioni coerenti a livelli più bassi di I/O al secondo. Le unità SSD Standard offrono una buona esperienza iniziale per coloro che vogliono passare al cloud, soprattutto se riscontrano problemi di varianza dei carichi di lavoro in esecuzione nelle soluzioni HDD locali. Rispetto agli HDD standard, le unità SSD standard offrono disponibilità, coerenza, affidabilità e latenza migliori. Le unità SSD Standard sono idonee per server Web, server applicazioni con un numero ridotto di operazioni di I/O al secondo, applicazioni aziendali con un utilizzo non intensivo e carichi di lavoro di sviluppo/test. Analogamente agli HDD standard, le unità SSD standard sono disponibili in tutte le macchine virtuali di Azure.

### <a name="disk-size"></a>Dimensioni del disco
[!INCLUDE [disk-storage-standard-ssd-sizes](../../includes/disk-storage-standard-ssd-sizes.md)]

Le unità SSD standard sono progettate per fornire latenze di millisecondi a una sola cifra e i valori di IOPS e velocità effettiva fino ai limiti descritti nella tabella precedente 99% del tempo. Gli IOPS e la velocità effettiva possono variare a seconda dei modelli di traffico. Le unità SSD Standard offrono prestazioni più coerenti rispetto ai dischi HDD con latenza più bassa.

### <a name="transactions"></a>Transazioni

Per le unità SSD standard, ogni operazione di I/O inferiore o uguale a 256 KiB di velocità effettiva viene considerata una singola operazione di I/O. Le operazioni di i/O superiori a 256 KiB di velocità effettiva sono considerate più I/O di dimensioni 256 KiB. Queste transazioni hanno un effetto sulla fatturazione.

## <a name="standard-hdd"></a>HDD Standard

Le unità HDD Standard di Azure offrono un supporto dei dischi affidabile e a basso costo per le VM che eseguono carichi di lavoro non sensibili alla latenza. Con l'archiviazione standard, i dati vengono archiviati in unità disco rigido (HDD). Latenza, IOPS e velocità effettiva dei dischi HDD Standard possono variare in modo più ampio rispetto ai dischi basati su unità SSD. HDD Standard dischi sono progettati per fornire latenze di scrittura in 10 ms e le latenze di lettura in 20ms per la maggior parte delle operazioni di i/o, tuttavia le prestazioni effettive possono variare a seconda delle dimensioni di i/o e del carico Quando si lavora con le macchine virtuali, è possibile usare dischi HDD standard per scenari di sviluppo/test e carichi di lavoro meno critici. I dischi rigidi standard sono disponibili in tutte le aree di Azure e possono essere usati con tutte le macchine virtuali di Azure.

### <a name="disk-size"></a>Dimensioni del disco
[!INCLUDE [disk-storage-standard-hdd-sizes](../../includes/disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transazioni

Per i dischi rigidi standard, ogni operazione di I/O viene considerata come una singola transazione, indipendentemente dalle dimensioni di I/O. Queste transazioni hanno un effetto sulla fatturazione.

## <a name="billing"></a>Fatturazione

Quando si usano dischi gestiti, tenere conto delle considerazioni seguenti relative alla fatturazione:

- Tipo di disco
- Dimensioni dei dischi gestiti
- Snapshot
- Trasferimenti di dati in uscita
- Numero di transazioni

**Dimensioni dei dischi gestiti**: la fatturazione dei dischi gestiti è basata sulle dimensioni di cui è stato effettuato il provisioning. Azure associa le dimensioni di cui è stato effettuato il provisioning (arrotondate per eccesso) all'offerta di dimensioni dei dischi più vicina. Per informazioni dettagliate sulle dimensioni dei dischi disponibili, vedere le tabelle precedenti. Viene eseguito il mapping di ogni disco gestito a un'offerta relativa alle dimensioni di provisioning dei dischi supportate e viene eseguita la relativa fatturazione. Se ad esempio è stato effettuato il provisioning di un'unità SSD Standard da 200 GiB, tale unità viene associata all'offerta relativa alle dimensioni dei dischi E15 (256 GiB). La fatturazione per qualsiasi disco di cui è stato effettuato il provisioning viene ripartita con frequenza oraria usando il prezzo mensile dell'offerta di archiviazione. Se ad esempio è stato effettuato il provisioning di un disco E10 e lo si è eliminato dopo 20 ore, verranno fatturate 20 ore per l'offerta E10, indipendentemente dalla quantità di dati effettivamente scritti sul disco.

**Snapshot**: gli snapshot vengono fatturati in base alle dimensioni usate. Ad esempio, se si crea uno snapshot di un disco gestito con una capacità di provisioning di 64 GiB e una dimensione di dati effettivamente usata di 10 GiB, viene addebitato solo lo snapshot relativo alla dimensione di dati usata di 10 GiB.

Per altre informazioni sugli snapshot, vedere la sezione sugli snapshot nella [panoramica dei dischi gestiti](managed-disks-overview.md).

**Trasferimenti di dati in uscita**: i [trasferimenti di dati in uscita](https://azure.microsoft.com/pricing/details/bandwidth/) (dati in uscita dai data center di Azure) vengono fatturati in base all'uso della larghezza di banda.

**Transazioni**: viene addebitato il numero di transazioni eseguite su un disco gestito standard. Per le unità SSD standard, ogni operazione di I/O inferiore o uguale a 256 KiB di velocità effettiva viene considerata una singola operazione di I/O. Le operazioni di i/O superiori a 256 KiB di velocità effettiva sono considerate più I/O di dimensioni 256 KiB. Per i dischi rigidi standard, ogni operazione di I/O viene considerata come una singola transazione, indipendentemente dalle dimensioni di I/O.

Per informazioni dettagliate sui prezzi per Managed Disks, inclusi i costi delle transazioni, vedere [prezzi Managed disks](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Costo prenotazione macchina virtuale ultra disk

Le macchine virtuali di Azure sono in grado di indicare se sono compatibili con dischi Ultra. Una macchina virtuale compatibile con dischi Ultra alloca una capacità di larghezza di banda dedicata tra l'istanza della macchina virtuale di calcolo e l'unità di scala di archiviazione a blocchi per ottimizzare le prestazioni e ridurre la latenza. L'aggiunta di questa capacità alla macchina virtuale comporta un addebito per la prenotazione, che viene applicato solo se si abilita la funzionalità per i dischi Ultra nella macchina virtuale senza collegare un disco Ultra. Quando alla macchina virtuale compatibile con i dischi Ultra viene collegato un disco Ultra, questo addebito non viene applicato. L'addebito viene calcolato per ogni vCPU di cui è stato effettuato il provisioning nella macchina virtuale. 

> [!Note]
> Per le [dimensioni delle VM Core vincolate](constrained-vcpu.md), la tariffa di prenotazione è basata sul numero effettivo di vCPU e non sui core vincolati. Per Standard_E32 8s_v3, la tariffa di prenotazione sarà basata su 32 core. 

Per informazioni sui prezzi di dischi Ultra, vedere la [pagina dei prezzi di Azure disks](https://azure.microsoft.com/pricing/details/managed-disks/) .

### <a name="azure-disk-reservation"></a>Prenotazione dischi di Azure

La prenotazione del disco è la possibilità di acquistare un anno di spazio di archiviazione su disco in anticipo a uno sconto, riducendo i costi totali. Quando si acquista una prenotazione su disco, si seleziona uno SKU del disco specifico in un'area di destinazione, ad esempio 10 unità SSD Premium P30 (1TiB) nell'area Stati Uniti orientali 2 per un periodo di un anno. L'esperienza di prenotazione è simile alle istanze di macchina virtuale (VM) riservate. È possibile aggregare le prenotazioni di macchine virtuali e dischi per ottimizzare il risparmio. Per il momento, la prenotazione dei dischi di Azure offre un piano di impegno annuale per gli SKU SSD Premium da P30 (1TiB) a P80 (32 TiB) in tutte le aree di produzione. Per altri dettagli sui prezzi dei dischi riservati, vedere la [pagina prezzi di dischi di Azure](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="next-steps"></a>Passaggi successivi

Per iniziare, vedere [Managed disks prezzi](https://azure.microsoft.com/pricing/details/managed-disks/) .
