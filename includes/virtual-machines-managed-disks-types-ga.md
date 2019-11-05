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
ms.openlocfilehash: a355307eef9f5ce1f833cfd7924f5efa234a0cd7
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73523113"
---
## <a name="premium-ssd"></a>SSD Premium

I dischi SSD Premium di Azure offrono prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali (VM) con carichi di lavoro con I/O intensivo. È possibile migrare i dischi delle VM esistenti in SSD Premium per trarre vantaggio dalla velocità e dalle prestazioni di questi dischi. I dischi SSD Premium sono idonei per applicazioni di produzione cruciali. Le unità SSD Premium possono essere usate solo con le serie di VM che sono compatibili con archiviazione Premium.

Per altre informazioni sui singoli tipi e dimensioni delle macchine virtuali in Azure per Windows, incluse le dimensioni compatibili con archiviazione Premium, vedere [dimensioni delle macchine virtuali Windows](../articles/virtual-machines/windows/sizes.md). Per altre informazioni sui singoli tipi e dimensioni delle macchine virtuali in Azure per Linux, incluse le dimensioni compatibili con archiviazione Premium, vedere [dimensioni delle macchine virtuali Linux](../articles/virtual-machines/linux/sizes.md).

### <a name="disk-size"></a>Dimensione disco
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Quando si effettua il provisioning di un disco di archiviazione Premium, a differenza di Archiviazione Standard, vengono garantiti livelli di capacità, IOPS e velocità effettiva del disco. Se ad esempio si crea un disco P50, Azure effettua il provisioning di 4.095 GB di capacità di archiviazione, 7.500 IOPS e 250 MB/s di velocità effettiva per tale disco. L'applicazione può usare la totalità o una della capacità e delle prestazioni. I dischi SSD Premium sono progettati per fornire latenze di millisecondi a singola cifra e la velocità effettiva e la velocità effettiva di destinazione descritti nella tabella precedente 99,9% del tempo.

## <a name="bursting-preview"></a>Picchi (anteprima)

SSD Premium dimensioni inferiori a P30 offrono ora l'espansione del disco (anteprima) e possono aumentare le operazioni di i/o al secondo per ogni disco fino a 3.500 e la larghezza di banda fino a 170 Mbps. L'espansione è automatizzata e funziona in base a un sistema di credito. I crediti vengono accumulati automaticamente in un bucket di espansione quando il traffico del disco è inferiore alla destinazione di prestazioni con provisioning e i crediti vengono usati automaticamente quando il traffico supera la soglia, fino al limite massimo di picchi. Il limite massimo di impulsi definisce il limite di IOPS del disco & larghezza di banda anche se sono presenti crediti di espansione da utilizzare. Il provisioning del disco garantisce una maggiore tolleranza sulle modifiche imprevedibili dei modelli di i/o. È possibile sfruttarlo per l'avvio del disco del sistema operativo e le applicazioni con traffico irregolare.    

Il supporto per l'espansione dei dischi verrà abilitato per le nuove distribuzioni di dimensioni dei dischi applicabili nelle [aree di anteprima](https://docs.microsoft.com/azure/virtual-machines/linux/disk-bursting#regional-availability) per impostazione predefinita, senza che sia necessario alcun intervento da parte dell'utente. Per i dischi esistenti con le dimensioni applicabili, è possibile abilitare l'espansione con una delle due opzioni: scollegare e ricollegare il disco oppure arrestare e riavviare la macchina virtuale collegata. Tutte le dimensioni del disco applicabili a impulsi iniziano con un bucket di credito a espansione completa quando il disco è collegato a una macchina virtuale che supporta una durata massima al limite massimo di 30 minuti. Per altre informazioni sul funzionamento del proromping nei dischi di Azure, vedere [SSD Premium](../articles/virtual-machines/linux/disk-bursting.md)l'espansione. 

### <a name="transactions"></a>Transazioni

Per le unità SSD Premium, ogni operazione di I/O inferiore o uguale a 256 KiB di velocità effettiva viene considerata una singola operazione di I/O. Le operazioni di i/O superiori a 256 KiB di velocità effettiva sono considerate più I/O di dimensioni 256 KiB.

## <a name="standard-ssd"></a>SSD Standard

Le unità SSD Standard di Azure sono un'opzione di archiviazione conveniente, ottimizzata per carichi di lavoro che richiedono prestazioni coerenti a livelli più bassi di I/O al secondo. Le unità SSD Standard offrono una buona esperienza iniziale per coloro che vogliono passare al cloud, soprattutto se riscontrano problemi di varianza dei carichi di lavoro in esecuzione nelle soluzioni HDD locali. Rispetto agli HDD standard, le unità SSD standard offrono disponibilità, coerenza, affidabilità e latenza migliori. Le unità SSD Standard sono idonee per server Web, server applicazioni con un numero ridotto di operazioni di I/O al secondo, applicazioni aziendali con un utilizzo non intensivo e carichi di lavoro di sviluppo/test. Analogamente agli HDD standard, le unità SSD standard sono disponibili in tutte le macchine virtuali di Azure.

### <a name="disk-size"></a>Dimensione disco
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Le unità SSD standard sono progettate per fornire latenze di millisecondi a una sola cifra e i valori di IOPS e velocità effettiva fino ai limiti descritti nella tabella precedente 99% del tempo. Gli IOPS e la velocità effettiva possono variare a seconda dei modelli di traffico. Le unità SSD Standard offrono prestazioni più coerenti rispetto ai dischi HDD con latenza più bassa.

### <a name="transactions"></a>Transazioni

Per le unità SSD standard, ogni operazione di I/O inferiore o uguale a 256 KiB di velocità effettiva viene considerata una singola operazione di I/O. Le operazioni di i/O superiori a 256 KiB di velocità effettiva sono considerate più I/O di dimensioni 256 KiB. Queste transazioni hanno un effetto sulla fatturazione.

## <a name="standard-hdd"></a>HDD Standard

Le unità HDD Standard di Azure offrono un supporto dei dischi affidabile e a basso costo per le VM che eseguono carichi di lavoro non sensibili alla latenza. Con l'archiviazione standard, i dati vengono archiviati in unità disco rigido (HDD). Latenza, IOPS e velocità effettiva dei dischi HDD Standard possono variare in modo più ampio rispetto ai dischi basati su unità SSD. HDD Standard dischi sono progettati per fornire latenze di scrittura in 10 ms e le latenze di lettura in 20ms per la maggior parte delle operazioni di i/o, tuttavia le prestazioni effettive possono variare a seconda delle dimensioni di i/o e del carico Quando si lavora con le macchine virtuali, è possibile usare dischi HDD standard per scenari di sviluppo/test e carichi di lavoro meno critici. I dischi rigidi standard sono disponibili in tutte le aree di Azure e possono essere usati con tutte le macchine virtuali di Azure.

### <a name="disk-size"></a>Dimensione disco
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transazioni

Per i dischi rigidi standard, ogni operazione di I/O viene considerata come una singola transazione, indipendentemente dalle dimensioni di I/O. Queste transazioni hanno un effetto sulla fatturazione.

## <a name="billing"></a>Fatturazione

Quando si usano dischi gestiti, tenere conto delle considerazioni seguenti relative alla fatturazione:

- Tipo di disco
- Dimensioni dei dischi gestiti
- Snapshot
- Trasferimenti di dati in uscita
- Numero di transazioni

**Dimensioni dei dischi gestiti**: la fatturazione dei dischi gestiti è basata sulle dimensioni di cui è stato effettuato il provisioning. Azure associa le dimensioni di cui è stato effettuato il provisioning (arrotondate per eccesso) all'offerta di dimensioni dei dischi più vicina. Per informazioni dettagliate sulle dimensioni dei dischi disponibili, vedere le tabelle precedenti. Viene eseguito il mapping di ogni disco gestito a un'offerta relativa alle dimensioni di provisioning dei dischi supportate e viene eseguita la relativa fatturazione. Se ad esempio è stato effettuato il provisioning di un'unità SSD Standard da 200 GiB, tale unità viene associata all'offerta relativa alle dimensioni dei dischi E15 (256 GiB). La fatturazione per qualsiasi disco di cui sia stato effettuato il provisioning viene ripartita in modo proporzionale in base alle ore usando il prezzo mensile dell'offerta di Archiviazione Premium. Se ad esempio è stato effettuato il provisioning di un disco E10 e lo si è eliminato dopo 20 ore, verranno fatturate 20 ore per l'offerta E10, indipendentemente dalla quantità di dati effettivamente scritti sul disco.

**Snapshot**: gli snapshot vengono fatturati in base alle dimensioni usate. Ad esempio, se si crea uno snapshot di un disco gestito con una capacità di provisioning di 64 GiB e una dimensione di dati effettivamente usata di 10 GiB, viene addebitato solo lo snapshot relativo alla dimensione di dati usata di 10 GiB.
