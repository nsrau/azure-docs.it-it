---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/14/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 75b3934a7329b4e83a0f36f79bbc8365eaf8a086
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2018
ms.locfileid: "51572300"
---
# <a name="standard-ssd-managed-disks-for-azure-virtual-machine-workloads"></a>Managed Disks SSD Standard per carichi di lavoro delle macchine virtuali di Azure

Managed Disks SSD (Solid State Drive) Standard di Azure è un'opzione di archiviazione conveniente, ottimizzata per carichi di lavoro che richiedono prestazioni coerenti a livelli più bassi di I/O al secondo. Le unità SSD Standard offrono una buona esperienza iniziale per coloro che vogliono passare al cloud, soprattutto se riscontrano problemi di varianza dei carichi di lavoro in esecuzione nelle soluzioni HDD locali. Le unità SSD Standard garantiscono disponibilità, coerenza, affidabilità e latenza migliori rispetto ai dischi HDD e sono adatte per server Web, server applicazioni con un numero limitato di operazioni di I/O al secondo, applicazioni aziendali poco usate e carichi di lavoro di Sviluppo/test.

## <a name="standard-ssd-features"></a>Caratteristiche delle unità SSD Standard

**Dischi gestiti**: le unità SSD Standard sono disponibili solo come dischi gestiti. I dischi non gestiti e i BLOB di pagine non sono supportati nelle unità SSD Standard. Quando si crea il disco gestito, si specifica SSD Standard come tipo di disco e si indicano le dimensioni del disco necessarie. Azure creerà e gestirà automaticamente il disco.
Le unità SSD Standard supportano tutte le operazioni del modello di distribuzione classico offerte da Managed Disks. È ad esempio possibile creare o copiare dischi gestiti SSD Standard oppure eseguirne snapshot proprio come per i dischi gestiti.

**Macchine virtuali**: le unità SSD Standard possono essere usate con tutte le macchine virtuali di Azure, inclusi i tipi che non supportano i dischi Premium. Se ad esempio si usa una macchina virtuale della serie A, N o DS, o di qualsiasi altra serie di macchine virtuali di Azure, è possibile usare unità SSD Standard con tale macchina virtuale. Con l'introduzione delle unità SSD Standard, si consente la transizione di una vasta gamma di carichi di lavoro che in precedenza usavano dischi basati su unità HDD a dischi basati su unità SSD che garantiranno prestazioni coerenti, maggiore disponibilità, migliore latenza e un'esperienza di qualità complessivamente superiore.

**Durabilità e disponibilità elevate**: le unità SSD Standard sono basate sulla stessa piattaforma Dischi di Azure, che offre in modo coerente durabilità e disponibilità elevate per i dischi. I dischi di Azure sono stati progettati in modo da garantire una disponibilità del 99,999%. Come tutti i dischi gestiti, le unità SSD Standard offrono archiviazione con ridondanza locale. Grazie a questo tipo di archiviazione, la piattaforma gestisce più repliche dei dati per ogni disco e offre in modo coerente una durabilità di livello aziendale per i dischi IaaS, con una percentuale di frequenza di errori annua pari a zero, ovvero la migliore del settore.

**Snapshot**: come tutti i dischi gestiti, anche le unità SSD Standard supportano la creazione di snapshot. Il tipo di snapshot può essere Standard (HDD) o Premium (SSD). Per un risparmio sui costi, si consiglia il tipo di snapshot Standard (HDD) per tutti i tipi di dischi di Azure. Quando infatti si crea un disco gestito da uno snapshot, è sempre possibile scegliere un livello superiore, ad esempio SSD Standard o SSD Premium.

## <a name="scalability-and-performance-targets"></a>Obiettivi di scalabilità e prestazioni

La tabella seguente contiene le dimensioni dei dischi attualmente offerte per i dischi SSD Standard. Le dimensioni indicate con un asterisco sono attualmente in anteprima.

|Tipo di disco SSD Standard  |Dimensione disco  |Operazioni di I/O al secondo per disco  |Velocità effettiva per disco  |
|---------|---------|---------|---------|
|E10     |128 GiB         |Fino a 500         |Fino a 60 MiB al secondo         |
|E15     |256 GiB         |Fino a 500         |Fino a 60 MiB al secondo         |
|E20     |512 GiB         |Fino a 500         |Fino a 60 MiB al secondo         |
|E30     |1.024 GiB       |Fino a 500         |Fino a 60 MiB al secondo         |
|E40     |2.048 GiB       |Fino a 500         |Fino a 60 MiB al secondo         |
|E50     |4.095 GiB       |Fino a 500         |Fino a 60 MiB al secondo         |
|E60 *     |8.192 GiB       |Fino a 1.300       |Fino a 300 MiB al secondo        |
|E70 *    |16.384 GiB      |Fino a 2.000       |Fino a 500 MiB al secondo        |
|E80 *    |32.767 GiB      |Fino a 2.000       |Fino a 500 MiB al secondo        |

Le unità SSD Standard sono progettate per offrire latenze di pochi millisecondi a cifra singola per la maggior parte delle operazioni di I/O e per garantire operazioni di I/O al secondo e velocità effettiva fino ai limiti indicati nella tabella precedente nel 99% dei casi. I valori reali delle operazioni di I/O al secondo e della velocità effettiva possono talvolta variare a seconda dei modelli di traffico. Le unità SSD Standard offrono prestazioni più coerenti rispetto ai dischi HDD con latenza più bassa.

D'altro canto, le unità SSD Premium hanno prestazioni migliori rispetto alle unità SSD Standard, con latenze basse, valori elevati di operazioni di I/O al secondo o velocità effettiva e una coerenza ancora migliore grazie alle prestazioni dei dischi di cui è stato effettuato il provisioning. Le unità SSD Premium sono il tipo di disco consigliato per carichi di lavoro di produzione critici. Se il carico di lavoro richiede un supporto dei dischi a bassa latenza e prestazioni elevate, è consigliabile valutare la possibilità di usare Archiviazione Premium.

Analogamente alle unità SSD Premium, le unità SSD Standard usano unità di I/O da 256 KiB. Se il volume dei dati trasferiti è inferiore a 256 KiB, viene considerata una singola unità di I/O. Le dimensioni di I/O superiori vengono calcolate come più unità di I/O da 256 KiB. Ad esempio, 1.100 KiB di I/O corrispondono a cinque unità di I/O.

## <a name="pricing-and-billing"></a>Prezzi e fatturazione

Quando si usano le unità SSD Standard, tenere conto delle considerazioni seguenti relative alla fatturazione:

- Dimensioni dei dischi gestiti
- Snapshot
- Trasferimenti di dati in uscita
- Transazioni

**Dimensioni dei dischi gestiti**: la fatturazione dei dischi gestiti è basata sulle dimensioni di cui è stato effettuato il provisioning. Azure associa le dimensioni di cui è stato effettuato il provisioning (arrotondate per eccesso) all'offerta di dimensioni dei dischi più vicina. Per informazioni dettagliate sulle dimensioni dei dischi disponibili, vedere la tabella Obiettivi di scalabilità e prestazioni nella sezione precedente. Ogni disco è associato a una delle dimensioni supportate di cui è stato effettuato il provisioning e la fatturazione viene eseguita di conseguenza. Se ad esempio è stato effettuato il provisioning di un'unità SSD Standard da 200 GiB, tale unità viene associata all'offerta relativa alle dimensioni dei dischi E15 (256 GiB). La fatturazione per qualsiasi disco di cui sia stato effettuato il provisioning viene ripartita in modo proporzionale in base alle ore usando il prezzo mensile dell'offerta di Archiviazione Premium. Se ad esempio è stato effettuato il provisioning di un disco E10 e lo si è eliminato dopo 20 ore, verranno fatturate 20 ore per l'offerta E10, indipendentemente dalla quantità di dati effettivamente scritti sul disco.

**Snapshot**: gli snapshot dei dischi gestiti vengono fatturati in base alla capacità usata dagli snapshot, alla destinazione e all'origine, se presenti. Per altre informazioni sugli snapshot, vedere [Snapshot dei dischi gestiti](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview#managed-disk-snapshots).

**Trasferimenti di dati in uscita**: i [trasferimenti di dati in uscita](https://azure.microsoft.com/pricing/details/bandwidth/) (dati in uscita dai data center di Azure) vengono fatturati in base all'uso della larghezza di banda.

**Transazioni**: analogamente alle unità HDD Standard, anche le transazioni su unità SSD Standard sono soggette a fatturazione. Le transazioni includono le operazioni di lettura e scrittura sul disco. Le dimensioni dell'unità di I/O usata per il calcolo delle transazioni come unità SSD Standard sono di 256 KiB. Le dimensioni di I/O superiori vengono calcolate come più unità di I/O da 256 KiB.

Per altre informazioni sui prezzi di Macchine virtuali e Managed Disks, vedere:

- [Prezzi di Macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)
- [Prezzi di Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla creazione di unità SSD Standard, vedere l'esempio che illustra come creare una macchina virtuale con più unità SSD Standard.

> [!div class="nextstepaction"]
> [Create a VM with multiple standard SSDs](https://github.com/azure/azure-quickstart-templates/tree/master/101-vm-with-standardssd-disk/) (Creare una macchina virtuale con più unità SSD Standard)
