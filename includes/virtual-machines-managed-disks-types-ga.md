---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: rockboyfor
ms.service: virtual-machines
ms.topic: include
ms.date: 03/13/2019
ms.author: v-yeche
ms.custom: include file
ms.openlocfilehash: 88a4110d68dc8aa921d647f90de654d2ebb4e17d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60618041"
---
## <a name="premium-ssd"></a>SSD Premium

I dischi SSD Premium di Azure offrono prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali (VM) con carichi di lavoro con I/O intensivo. È possibile migrare i dischi delle VM esistenti in SSD Premium per trarre vantaggio dalla velocità e dalle prestazioni di questi dischi. I dischi SSD Premium sono idonei per applicazioni di produzione cruciali.

### <a name="disk-size"></a>Dimensioni disco
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

## <a name="standard-ssd"></a>SSD Standard

Le unità SSD Standard di Azure sono un'opzione di archiviazione conveniente, ottimizzata per carichi di lavoro che richiedono prestazioni coerenti a livelli più bassi di I/O al secondo. Le unità SSD Standard offrono una buona esperienza iniziale per coloro che vogliono passare al cloud, soprattutto se riscontrano problemi di varianza dei carichi di lavoro in esecuzione nelle soluzioni HDD locali. Le unità SSD Standard offrono disponibilità, coerenza, affidabilità e latenza migliori rispetto ai dischi HDD. Le unità SSD Standard sono idonee per server Web, server applicazioni con un numero ridotto di operazioni di I/O al secondo, applicazioni aziendali con un utilizzo non intensivo e carichi di lavoro di sviluppo/test.

### <a name="disk-size"></a>Dimensioni disco
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

## <a name="standard-hdd"></a>HDD Standard

Le unità HDD Standard di Azure offrono un supporto dei dischi affidabile e a basso costo per le VM che eseguono carichi di lavoro non sensibili alla latenza. Supporta anche BLOB, tabelle, code e file. Con l'archiviazione standard, i dati vengono archiviati in unità disco rigido (HDD). Per le macchine virtuali è possibile usare dischi SSD e HDD Standard per scenari di sviluppo/test e carichi di lavoro meno critici. L'archiviazione standard è disponibile in tutte le aree di Azure.

### <a name="disk-size"></a>Dimensioni disco
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

## <a name="billing"></a>Fatturazione

Quando si usano dischi gestiti, tenere conto delle considerazioni seguenti relative alla fatturazione:

- Tipo di disco
- Dimensioni dei dischi gestiti
- Snapshot
- Trasferimenti di dati in uscita
- Numero di transazioni

**Dimensioni dei dischi gestiti**: la fatturazione dei dischi gestiti è basata sulle dimensioni di cui è stato effettuato il provisioning. Azure associa le dimensioni di cui è stato effettuato il provisioning (arrotondate per eccesso) all'offerta di dimensioni dei dischi più vicina. Per informazioni dettagliate sulle dimensioni dei dischi disponibili, vedere le tabelle precedenti. Viene eseguito il mapping di ogni disco gestito a un'offerta relativa alle dimensioni di provisioning dei dischi supportate e viene eseguita la relativa fatturazione. Se ad esempio è stato effettuato il provisioning di un'unità SSD Standard da 200 GiB, tale unità viene associata all'offerta relativa alle dimensioni dei dischi E15 (256 GiB). La fatturazione per qualsiasi disco di cui sia stato effettuato il provisioning viene ripartita in modo proporzionale in base alle ore usando il prezzo mensile dell'offerta di Archiviazione Premium. Se ad esempio è stato effettuato il provisioning di un disco E10 e lo si è eliminato dopo 20 ore, verranno fatturate 20 ore per l'offerta E10, indipendentemente dalla quantità di dati effettivamente scritti sul disco.

**Snapshot**: Gli snapshot vengono fatturati in base alle dimensioni usate. Ad esempio, se si crea uno snapshot di un disco gestito con una capacità di provisioning di 64 GiB e una dimensione di dati effettivamente usata di 10 GiB, viene addebitato solo lo snapshot relativo alla dimensione di dati usata di 10 GiB.