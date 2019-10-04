---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/14/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 2bc5602011ed64b11b1b8c96b7e69a8d5ee9bf32
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67132943"
---
## <a name="premium-ssd"></a>SSD Premium

I dischi SSD Premium di Azure offrono prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali (VM) con carichi di lavoro con I/O intensivo. È possibile migrare i dischi delle VM esistenti in SSD Premium per trarre vantaggio dalla velocità e dalle prestazioni di questi dischi. I dischi SSD Premium sono idonei per applicazioni di produzione cruciali. Unità SSD Premium può essere utilizzato solo con serie di macchine Virtuali che sono compatibili con archiviazione premium.

Per altre informazioni sui singoli tipi di macchine Virtuali e le dimensioni in Azure per Windows, tra cui le dimensioni sono premium compatibile con archiviazione, vedere [dimensioni delle macchine Virtuali Windows](../articles/virtual-machines/windows/sizes.md). Per altre informazioni sui singoli tipi di macchine Virtuali e di dimensioni in Azure per Linux e include le dimensioni sono premium compatibile con archiviazione, vedere [dimensioni delle VM Linux](../articles/virtual-machines/linux/sizes.md).

### <a name="disk-size"></a>Dimensioni disco
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Quando si effettua il provisioning di un disco di archiviazione Premium, a differenza di Archiviazione Standard, vengono garantiti livelli di capacità, IOPS e velocità effettiva del disco. Se ad esempio si crea un disco P50, Azure effettua il provisioning di 4.095 GB di capacità di archiviazione, 7.500 IOPS e 250 MB/s di velocità effettiva per tale disco. L'applicazione può usare la totalità o una della capacità e delle prestazioni. I dischi SSD Premium sono progettati per offrire latenze pari a singole bassa e IOPS e velocità effettiva descritto nella precedente tabella pari al 99,9% del tempo di destinazione.

### <a name="transactions"></a>Transazioni

Per le unità SSD premium, ogni operazione dei / o minore o uguale a 256 KB di velocità effettiva viene considerato una singola operazione dei / o. Operazioni dei / o superiore a 256 KB di velocità effettiva sono considerate diversi i/o di dimensione 256 KB.

## <a name="standard-ssd"></a>SSD Standard

Le unità SSD Standard di Azure sono un'opzione di archiviazione conveniente, ottimizzata per carichi di lavoro che richiedono prestazioni coerenti a livelli più bassi di I/O al secondo. Le unità SSD Standard offrono una buona esperienza iniziale per coloro che vogliono passare al cloud, soprattutto se riscontrano problemi di varianza dei carichi di lavoro in esecuzione nelle soluzioni HDD locali. Rispetto alle unità disco rigido standard, gli SSD standard forniscono una migliore disponibilità, coerenza, affidabilità e la latenza. Le unità SSD Standard sono idonee per server Web, server applicazioni con un numero ridotto di operazioni di I/O al secondo, applicazioni aziendali con un utilizzo non intensivo e carichi di lavoro di sviluppo/test. Come standard HDD, SSD standard sono disponibili in tutte le VM di Azure.

### <a name="disk-size"></a>Dimensioni disco
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Unità SSD standard sono progettati per offrire latenze di pochi millisecondi a cifra singola e di IOPS e velocità effettiva fino ai limiti di descritto nella precedente tabella 99% del tempo. IOPS e velocità effettiva effettivi possono variare in alcuni casi a seconda di modelli di traffico. Le unità SSD Standard offrono prestazioni più coerenti rispetto ai dischi HDD con latenza più bassa.

### <a name="transactions"></a>Transazioni

Per le unità SSD standard, ogni operazione dei / o minore o uguale a 256 KB di velocità effettiva viene considerato una singola operazione dei / o. Operazioni dei / o superiore a 256 KB di velocità effettiva sono considerate diversi i/o di dimensione 256 KB. Le transazioni hanno un impatto sulla fatturazione.

## <a name="standard-hdd"></a>HDD Standard

Le unità HDD Standard di Azure offrono un supporto dei dischi affidabile e a basso costo per le VM che eseguono carichi di lavoro non sensibili alla latenza. Con l'archiviazione standard, i dati vengono archiviati in unità disco rigido (HDD). Latenza, IOPS e velocità effettiva di Standard HDD dischi possono variare in modo più esteso rispetto ai dischi basati su unità SSD. I dischi HDD standard sono progettati per offrire le latenze di scrittura in 10 ms e latenze in 20 ms per la maggior parte delle operazioni dei / o, di lettura ma le prestazioni effettive possono variare a seconda il modello di carico di lavoro e le dimensioni dei / o. Quando si lavora con le macchine virtuali, è possibile usare i dischi HDD standard per scenari di sviluppo/test e ridurre i carichi di lavoro critici. Unità disco rigido standard è disponibili in tutte le aree di Azure e può essere usato con tutte le macchine virtuali di Azure.

### <a name="disk-size"></a>Dimensioni disco
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transazioni

Per unità disco rigido Standard, ogni operazione dei / o viene considerato come una singola transazione, indipendentemente dalle dimensioni i/o. Le transazioni hanno un impatto sulla fatturazione.

## <a name="billing"></a>Fatturazione

Quando si usano dischi gestiti, tenere conto delle considerazioni seguenti relative alla fatturazione:

- Tipo di disco
- Dimensioni dei dischi gestiti
- Snapshot
- Trasferimenti di dati in uscita
- Numero di transazioni

**Dimensioni dei dischi gestiti**: la fatturazione dei dischi gestiti è basata sulle dimensioni di cui è stato effettuato il provisioning. Azure associa le dimensioni di cui è stato effettuato il provisioning (arrotondate per eccesso) all'offerta di dimensioni dei dischi più vicina. Per informazioni dettagliate sulle dimensioni dei dischi disponibili, vedere le tabelle precedenti. Viene eseguito il mapping di ogni disco gestito a un'offerta relativa alle dimensioni di provisioning dei dischi supportate e viene eseguita la relativa fatturazione. Se ad esempio è stato effettuato il provisioning di un'unità SSD Standard da 200 GiB, tale unità viene associata all'offerta relativa alle dimensioni dei dischi E15 (256 GiB). La fatturazione per qualsiasi disco di cui sia stato effettuato il provisioning viene ripartita in modo proporzionale in base alle ore usando il prezzo mensile dell'offerta di Archiviazione Premium. Se ad esempio è stato effettuato il provisioning di un disco E10 e lo si è eliminato dopo 20 ore, verranno fatturate 20 ore per l'offerta E10, indipendentemente dalla quantità di dati effettivamente scritti sul disco.

**Snapshot**: Gli snapshot vengono fatturati in base alle dimensioni usate. Ad esempio, se si crea uno snapshot di un disco gestito con una capacità di provisioning di 64 GiB e una dimensione di dati effettivamente usata di 10 GiB, viene addebitato solo lo snapshot relativo alla dimensione di dati usata di 10 GiB.
