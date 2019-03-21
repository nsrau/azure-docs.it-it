---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/13/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0201776914610ddaca50a670fc500156a65cd734
ms.sourcegitcommit: f596d88d776a3699f8c8cf98415eb874187e2a48
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2019
ms.locfileid: "58115083"
---
## <a name="premium-ssd"></a>SSD Premium

I dischi SSD Premium di Azure offrono prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali (VM) con carichi di lavoro con I/O intensivo. È possibile migrare i dischi delle VM esistenti in SSD Premium per trarre vantaggio dalla velocità e dalle prestazioni di questi dischi. I dischi SSD Premium sono idonei per applicazioni di produzione cruciali.

### <a name="disk-size"></a>Dimensioni disco

Le dimensioni contrassegnate con un asterisco sono attualmente in anteprima.

| Dimensioni SSD Premium | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60* | P70* | P80* |
|-------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Dimensioni disco in GiB | 32 | 64 | 128 | 256 | 512 | 1.024 | 2.048 | 4.095 | 8,192 | 16.384 | 32.767 |
| IOPS per disco | Fino a 120 | Fino a 240 | Fino a 500 | Fino a 1.100 | Fino a 2.300 | Fino a 5.000 | Fino a 7.500 | Fino a 7.500 | Fino a 12.500 | Fino a 15.000 | Fino a 20.000 |
| Velocità effettiva per disco | Fino a 25 MiB/sec | Fino a 50 MiB/sec | Fino a 100 MiB/sec | Fino a 125 MiB/sec | Fino a 150 MiB/sec | Fino a 200 MiB/sec | Fino a 250 MiB/sec | Fino a 250 MiB/sec| Fino a 480 MiB/sec | Fino a 750 MiB/sec | Fino a 750 MiB/sec |

## <a name="standard-ssd"></a>SSD Standard

Le unità SSD Standard di Azure sono un'opzione di archiviazione conveniente, ottimizzata per carichi di lavoro che richiedono prestazioni coerenti a livelli più bassi di I/O al secondo. Le unità SSD Standard offrono una buona esperienza iniziale per coloro che vogliono passare al cloud, soprattutto se riscontrano problemi di varianza dei carichi di lavoro in esecuzione nelle soluzioni HDD locali. Le unità SSD Standard offrono disponibilità, coerenza, affidabilità e latenza migliori rispetto ai dischi HDD. Le unità SSD Standard sono idonee per server Web, server applicazioni con un numero ridotto di operazioni di I/O al secondo, applicazioni aziendali con un utilizzo non intensivo e carichi di lavoro di sviluppo/test.

### <a name="disk-size"></a>Dimensioni disco

Le dimensioni contrassegnate con un asterisco sono attualmente in anteprima.

| Dimensioni SSD Standard | E4 | E6 | E10 | E15 | E20 | E30 | E40 | E50 | E60* | E70* | E80* |
|--------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Dimensioni disco in GiB | 32 | 64 | 128 | 256 | 512 | 1.024 | 2.048 | 4.095 | 8,192 | 16.384 | 32.767 |
| IOPS per disco | Fino a 120 | Fino a 240 | Fino a 500 | Fino a 500 | Fino a 500 | Fino a 500 | Fino a 500 | Fino a 500 | Fino a 1.300 | Fino a 2.000 | Fino a 2.000 |
| Velocità effettiva per disco |  Fino a 25 MiB/sec |  Fino a 50 MiB/sec  |  Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec| Fino a 300 MiB/sec |  Fino a 500 MiB/sec | Fino a 500 MiB/sec |

## <a name="standard-hdd"></a>HDD Standard

Le unità HDD Standard di Azure offrono un supporto dei dischi affidabile e a basso costo per le VM che eseguono carichi di lavoro non sensibili alla latenza. Supporta anche BLOB, tabelle, code e file. Con l'archiviazione standard, i dati vengono archiviati in unità disco rigido (HDD). Per le macchine virtuali è possibile usare dischi SSD e HDD Standard per scenari di sviluppo/test e carichi di lavoro meno critici. L'archiviazione standard è disponibile in tutte le aree di Azure.

### <a name="disk-size"></a>Dimensioni disco

Le dimensioni contrassegnate con un asterisco sono attualmente in anteprima.

| Tipo di disco Standard | S4 | S6 | S10 | S15 | S20 | S30 | S40 | S50 | S60* | S70* | S80* |
|--------------------|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Dimensioni disco in GiB | 32 | 64 | 128 | 256 | 512 | 1.024 | 2.048 | 4.095 | 8,192 | 16.384 | 32.767 |
| IOPS per disco | Fino a 500 | Fino a 500 | Fino a 500 | Fino a 500 | Fino a 500 | Fino a 500 | Fino a 500 | Fino a 500 | Fino a 1.300 | Fino a 2.000 | Fino a 2.000 |
| Velocità effettiva per disco | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec | Fino a 60 MiB/sec| Fino a 300 MiB/sec | Fino a 500 MiB/sec | Fino a 500 MiB/sec |

## <a name="billing"></a>Fatturazione

Quando si usano dischi gestiti, tenere conto delle considerazioni seguenti relative alla fatturazione:

- Tipo di disco
- Dimensioni dei dischi gestiti
- Snapshot
- Trasferimenti di dati in uscita
- Numero di transazioni

**Dimensioni dei dischi gestiti**: la fatturazione dei dischi gestiti è basata sulle dimensioni di cui è stato effettuato il provisioning. Azure associa le dimensioni di cui è stato effettuato il provisioning (arrotondate per eccesso) all'offerta di dimensioni dei dischi più vicina. Per informazioni dettagliate sulle dimensioni dei dischi disponibili, vedere le tabelle precedenti. Viene eseguito il mapping di ogni disco gestito a un'offerta relativa alle dimensioni di provisioning dei dischi supportate e viene eseguita la relativa fatturazione. Se ad esempio è stato effettuato il provisioning di un'unità SSD Standard da 200 GiB, tale unità viene associata all'offerta relativa alle dimensioni dei dischi E15 (256 GiB). La fatturazione per qualsiasi disco di cui sia stato effettuato il provisioning viene ripartita in modo proporzionale in base alle ore usando il prezzo mensile dell'offerta di Archiviazione Premium. Se ad esempio è stato effettuato il provisioning di un disco E10 e lo si è eliminato dopo 20 ore, verranno fatturate 20 ore per l'offerta E10, indipendentemente dalla quantità di dati effettivamente scritti sul disco.

**Snapshot**: Gli snapshot vengono fatturati in base alle dimensioni usate. Ad esempio, se si crea uno snapshot di un disco gestito con una capacità di provisioning di 64 GiB e una dimensione di dati effettivamente usata di 10 GiB, viene addebitato solo lo snapshot relativo alla dimensione di dati usata di 10 GiB.