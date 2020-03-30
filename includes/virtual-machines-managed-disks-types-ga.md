---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/28/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6740ea320f2d950386da12eb44726e2c826b60a4
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2020
ms.locfileid: "80386109"
---
## <a name="premium-ssd"></a>SSD Premium

I dischi SSD Premium di Azure offrono prestazioni elevate e supporto per dischi a bassa latenza per le macchine virtuali (VM) con carichi di lavoro con I/O intensivo. È possibile migrare i dischi delle VM esistenti in SSD Premium per trarre vantaggio dalla velocità e dalle prestazioni di questi dischi. I dischi SSD Premium sono idonei per applicazioni di produzione cruciali. Gli SSD Premium possono essere usati solo con serie di macchine virtuali compatibili con l'archiviazione Premium.Premium SSDs can only be used with VM series that are premium storage-compatible.

Per altre informazioni sui singoli tipi e dimensioni di macchine virtuali in Azure per Windows, incluse le dimensioni compatibili con l'archiviazione Premium, vedere Dimensioni di Macchine virtuali Windows.To learn more about individual VM types and sizes in Azure for Windows, including which sizes are premium storage-compatible, see [Windows VM sizes.](../articles/virtual-machines/windows/sizes.md) Per altre informazioni sui singoli tipi e dimensioni di vm in Azure per Linux, incluse le dimensioni compatibili con l'archiviazione Premium, vedere Dimensioni delle macchine virtuali Linux.To learn more about individual VM types and sizes in Azure for Linux, including which sizes are premium storage-compatible, see [Linux VM sizes.](../articles/virtual-machines/linux/sizes.md) Da uno di questi articoli, è necessario controllare ogni singolo articolo di dimensioni della macchina virtuale per determinare se è compatibile con l'archiviazione Premium.From either of those articles, you need to check each individual VM size article to determine if it is premium storage-compatible.

### <a name="disk-size"></a>Dimensioni disco
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Quando si effettua il provisioning di un disco di archiviazione Premium, a differenza di Archiviazione Standard, vengono garantiti livelli di capacità, IOPS e velocità effettiva del disco. Se ad esempio si crea un disco P50, Azure effettua il provisioning di 4.095 GB di capacità di archiviazione, 7.500 IOPS e 250 MB/s di velocità effettiva per tale disco. L'applicazione può usare la totalità o una della capacità e delle prestazioni. I dischi SSD Premium sono progettati per fornire latenze di millisecondo a una cifra bassa e operazioni di I/O al secondo di destinazione e velocità effettiva descritte nella tabella precedente per il 99,9% del tempo.

## <a name="bursting"></a>Scoppio

Le dimensioni degli SSD Premium inferiori a P30 ora offrono il burstdel del disco e possono burst their IOPS per disco fino a 3.500 e la loro larghezza di banda fino a 170 Mbps. Il bursting è automatizzato e funziona sulla base di un sistema di credito. I crediti vengono accumulati automaticamente in un bucket burst quando il traffico su disco è inferiore all'obiettivo di prestazioni di cui è stato eseguito il provisioning e i crediti vengono consumati automaticamente quando il traffico supera l'obiettivo, fino al limite massimo di burst. Il limite massimo di burst definisce il limite di operazioni di I/O al secondo su disco & larghezza di banda anche se si dispone di crediti burst da utilizzare. L'esplosione del disco fornisce una migliore tolleranza sulle modifiche imprevedibili dei modelli di I/O. È possibile sfruttare al meglio per l'avvio del disco del sistema operativo e le applicazioni con traffico appuntito.    

Per impostazione predefinita, il supporto per i dischi bursting verrà abilitato nelle nuove distribuzioni di dimensioni dei dischi applicabili, senza che sia necessaria alcuna azione da parte dell'utente. Per i dischi esistenti di dimensioni applicabili, è possibile abilitare il bursting con una delle due opzioni: scollegare e ricollegare il disco o arrestare e riavviare la macchina virtuale collegata. Tutte le dimensioni del disco applicabili del burst inizieranno con un bucket di credito burst completo quando il disco è collegato a una macchina virtuale che supporta una durata massima al limite di burst di picco di 30 min. Per altre informazioni sul funzionamento del burst ing ai dischi di Azure, vedere [Esplod Premium SSD bursting](../articles/virtual-machines/linux/disk-bursting.md). 

### <a name="transactions"></a>Transazioni

Per gli SSD premium, ogni operazione di I/O minore o uguale a 256 KiB di velocità effettiva viene considerata una singola operazione di I/O.For premium SSDs, each I/O operation less than or equal to 256 KiB of throughput is consider a single I/O operation. Le operazioni di I/O superiori a 256 KiB di velocità effettiva sono considerate operazioni di I/O multiple della dimensione 256 KiB.

## <a name="standard-ssd"></a>SSD Standard

Le unità SSD Standard di Azure sono un'opzione di archiviazione conveniente, ottimizzata per carichi di lavoro che richiedono prestazioni coerenti a livelli più bassi di I/O al secondo. Le unità SSD Standard offrono una buona esperienza iniziale per coloro che vogliono passare al cloud, soprattutto se riscontrano problemi di varianza dei carichi di lavoro in esecuzione nelle soluzioni HDD locali. Rispetto agli HDD standard, gli SSD standard offrono una migliore disponibilità, coerenza, affidabilità e latenza. Le unità SSD Standard sono idonee per server Web, server applicazioni con un numero ridotto di operazioni di I/O al secondo, applicazioni aziendali con un utilizzo non intensivo e carichi di lavoro di sviluppo/test. Come gli HDD standard, gli SSD standard sono disponibili in tutte le macchine virtuali di Azure.As standard HDDs, standard SSD s/available on all Azure VMs.

### <a name="disk-size"></a>Dimensioni disco
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Gli SSD standard sono progettati per fornire latenze di millisecondi a una cifra e le operazioni di I/O al secondo e la velocità effettiva fino ai limiti descritti nella tabella precedente, il 99% dei casi. Le operazioni di I/O al secondo e la velocità effettiva possono variare a volte a seconda dei modelli di traffico. Le unità SSD Standard offrono prestazioni più coerenti rispetto ai dischi HDD con latenza più bassa.

### <a name="transactions"></a>Transazioni

Per gli SSD standard, ogni operazione di I/O minore o uguale a 256 KiB di velocità effettiva viene considerata una singola operazione di I/O.For standard SSDs, each I/O operation less than or equal to 256 KiB of throughput is considered a single I/O operation. Le operazioni di I/O superiori a 256 KiB di velocità effettiva sono considerate operazioni di I/O multiple della dimensione 256 KiB. Queste transazioni hanno un impatto sulla fatturazione.

## <a name="standard-hdd"></a>HDD Standard

Le unità HDD Standard di Azure offrono un supporto dei dischi affidabile e a basso costo per le VM che eseguono carichi di lavoro non sensibili alla latenza. Con l'archiviazione standard, i dati vengono archiviati in unità disco rigido (HDD). Latenza, IOPS e velocità effettiva dei dischi HDD standard possono variare in modo più ampio rispetto ai dischi basati su SSD. I dischi HDD standard sono progettati per fornire latenze di scrittura inferiori a 10 ms e latenze di lettura inferiori a 20 ms per la maggior parte delle operazioni di I/O, tuttavia le prestazioni effettive possono variare a seconda delle dimensioni di I/O e del modello di carico di lavoro. Quando si usa le macchine virtuali, è possibile usare dischi HDD standard per scenari di sviluppo/test e carichi di lavoro meno critici. Standard HDDs are available in all Azure regions and can be used with all Azure VMs.

### <a name="disk-size"></a>Dimensioni disco
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transazioni

Per i dischi rigidi standard, ogni operazione di I/O viene considerata come una singola transazione, indipendentemente dalle dimensioni dei / o. Queste transazioni hanno un impatto sulla fatturazione.

## <a name="billing"></a>Fatturazione

Quando si usano dischi gestiti, tenere conto delle considerazioni seguenti relative alla fatturazione:

- Tipo di disco
- Dimensioni dei dischi gestiti
- Snapshot
- Trasferimenti di dati in uscita
- Numero di transazioni

**Dimensioni dei dischi gestiti**: la fatturazione dei dischi gestiti è basata sulle dimensioni di cui è stato effettuato il provisioning. Azure associa le dimensioni di cui è stato effettuato il provisioning (arrotondate per eccesso) all'offerta di dimensioni dei dischi più vicina. Per informazioni dettagliate sulle dimensioni dei dischi disponibili, vedere le tabelle precedenti. Viene eseguito il mapping di ogni disco gestito a un'offerta relativa alle dimensioni di provisioning dei dischi supportate e viene eseguita la relativa fatturazione. Se ad esempio è stato effettuato il provisioning di un'unità SSD Standard da 200 GiB, tale unità viene associata all'offerta relativa alle dimensioni dei dischi E15 (256 GiB). La fatturazione per qualsiasi disco di cui sia stato effettuato il provisioning viene ripartita in modo proporzionale in base alle ore usando il prezzo mensile dell'offerta di Archiviazione Premium. Se ad esempio è stato effettuato il provisioning di un disco E10 e lo si è eliminato dopo 20 ore, verranno fatturate 20 ore per l'offerta E10, indipendentemente dalla quantità di dati effettivamente scritti sul disco.

**Istantanee**: Le istantanee vengono fatturate in base alle dimensioni utilizzate. Ad esempio, se si crea uno snapshot di un disco gestito con una capacità di provisioning di 64 GiB e una dimensione di dati effettivamente usata di 10 GiB, viene addebitato solo lo snapshot relativo alla dimensione di dati usata di 10 GiB.
