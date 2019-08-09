---
author: yashar
ms.author: banders
ms.service: virtual-machines-windows
ms.topic: include
ms.date: 11-20-2018
ms.openlocfilehash: 790fb39c3d8964c053ffe1d7ee04418fcbc0913c
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68857411"
---
# <a name="virtual-machine-size-flexibility-with-reserved-vm-instances"></a>Flessibilità di dimensioni delle macchine virtuali con le istanze di macchina virtuale riservate

Con un'istanza di macchina virtuale riservata ottimizzata per la flessibilità di dimensioni, la prenotazione acquistata è applicabile alle dimensioni di macchine virtuali (VM) nello stesso gruppo di serie di dimensioni. Ad esempio, se si acquista una prenotazione per una dimensione di macchina virtuale inclusa nella tabella della serie DSv2, ad esempio Standard_DS5_v2, lo sconto per la prenotazione è applicabile alle altre quattro dimensioni elencate nella stessa tabella:

- Standard_DS1_v2
- Standard_DS2_v2
- Standard_DS3_v2
- Standard_DS4_v2

Tale sconto per la prenotazione non si applica però alle dimensioni di macchine virtuali incluse in tabelle diverse, ad esempio quelle della tabella della serie DSv2 con memoria elevata: Standard_DS11_v2, Standard_DS12_v2 e così via.

All'interno del gruppo di serie di dimensioni, il numero di macchine virtuali a cui si applica lo sconto per la prenotazione dipende dalle dimensioni della macchina virtuale prescelte al momento dell'acquisto della prenotazione. Dipende anche dalle dimensioni delle macchine virtuali in esecuzione. La colonna Rapporto nelle tabelle seguenti indica il footprint relativo per ogni dimensione di macchina virtuale nel gruppo. Usare il valore di rapporto per calcolare come viene applicato lo sconto per la prenotazione alle macchine virtuali in esecuzione.

## <a name="examples"></a>Esempi

Gli esempi seguenti usano le dimensioni e i rapporti nella tabella della serie DSv2.

 Si acquista un'istanza di macchina virtuale riservata con le dimensioni Standard_DS4_v2, con il rapporto (o footprint relativo) pari a 8 rispetto alle altre dimensioni nella serie.

- Scenario 1: Eseguire otto macchine virtuali con dimensioni Standard_DS1_v2 con un rapporto pari a 1. Lo sconto per la prenotazione si applica a tutte e otto le macchine virtuali.
- Scenario 2: Eseguire due macchine virtuali con dimensioni Standard_DS2_v2 ciascuna con un rapporto pari a 2. Eseguire anche una macchina virtuale con dimensioni Standard_DS3_v2 con un rapporto pari a 4. Il footprint complessivo è 2+2+4=8. Pertanto, lo sconto per la prenotazione si applica a tutte e tre le macchine virtuali.
- Scenario 3: Eseguire una macchina virtuale Standard_DS5_v2 con un rapporto di 16. Lo sconto per la prenotazione si applica a metà del costo di calcolo di tale macchina virtuale.

Le sezioni seguenti mostrano quali dimensioni sono incluse nello stesso gruppo di serie di dimensioni quando si acquista un'istanza di macchina virtuale riservata ottimizzata per la flessibilità di dimensioni dell'istanza.

## <a name="b-series"></a>Serie B

| Dimensione | Rapporto|
|---|---|
| Standard_B1s | 1 |
|Standard_B2s|4|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali della serie B con supporto per burst](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="b-series-high-memory"></a>Serie B con memoria elevata

| Dimensione | Rapporto|
|---|---|
| Standard_B1ms |1|
|Standard_B2ms|4|
|Standard_B4ms|8|
|Standard_B8ms|16|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali della serie B con supporto per burst](../articles/virtual-machines/windows/b-series-burstable.md).

## <a name="d-series"></a>Serie D

| Dimensione | Rapporto|
|---|---|
| Standard_D1|1|
|Standard_D2|2|
|Standard_D3|4|
|Standard_D4|8|

Per altre informazioni, vedere [Generazioni precedenti delle dimensioni delle macchine virtuali](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="d-series-high-memory"></a>Serie D con memoria elevata

| Dimensione | Rapporto|
|---|---|
| Standard_D11|1|
|Standard_D12|2|
|Standard_D13|4|
|Standard_D14|8|

Per altre informazioni, vedere [Generazioni precedenti delle dimensioni delle macchine virtuali](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series"></a>Serie DS

| Dimensione | Rapporto|
|---|---|
|Standard_DS1|1|
|Standard_DS2|2|
|Standard_DS3|4|
|Standard_DS4|8|

Per altre informazioni, vedere [Generazioni precedenti delle dimensioni delle macchine virtuali](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="ds-series-high-memory"></a>Serie DS con memoria elevata

| Dimensione | Rapporto|
|---|---|
|Standard_DS11|1|
|Standard_DS12|2|
|Standard_DS13|4|
|Standard_DS14|8|

Per altre informazioni, vedere [Generazioni precedenti delle dimensioni delle macchine virtuali](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series"></a>Serie DSv2

| Dimensione | Rapporto|
|---|---|
|Standard_DS1_v2|1|
|Standard_DS2_v2|2|
|Standard_DS3_v2|4|
|Standard_DS4_v2|8|
|Standard_DS5_v2|16|

Per altre informazioni, vedere [Generazioni precedenti delle dimensioni delle macchine virtuali](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-series-high-memory"></a>Serie DSv2 con memoria elevata

| Dimensione | Rapporto|
|---|---|
|Standard_DS11_v2|1|
|Standard_DS11-1_v2|1|
|Standard_DS12_v2|2|
|Standard_DS12-1_v2|2|
|Standard_DS12-2_v2|2|
|Standard_DS13_v2|4|
|Standard_DS13-2_v2|4|
|Standard_DS13-4_v2|4|
|Standard_DS14_v2|8|
|Standard_DS14-4_v2|8|
|Standard_DS14-8_v2|8|
|Standard_DS15_v2|10|

Per altre informazioni, vedere [Generazioni precedenti delle dimensioni delle macchine virtuali](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv2-high-memory-isolated-series"></a>Serie isolata con memoria elevata DSv2

| Dimensione | Rapporto|
|---|---|
|Standard_DS15i_v2|1|

Per altre informazioni, vedere [Generazioni precedenti delle dimensioni delle macchine virtuali](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dsv3-series"></a>Serie DSv3

| Dimensione | Rapporto|
|---|---|
|Standard_D2s_v3|1|
|Standard_D4s_v3|2|
|Standard_D8s_v3|4|
|Standard_D16s_v3|8|
|Standard_D32s_v3|16|
|Standard_D64s_v3|32|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali di utilizzo generico](../articles/virtual-machines/windows/sizes-general.md#dsv3-series-1).

## <a name="dv2-series"></a>Serie Dv2

| Dimensione | Rapporto|
|---|---|
|Standard_D1_v2|1|
|Standard_D2_v2|2|
|Standard_D3_v2|4|
|Standard_D4_v2|8|
|Standard_D5_v2|16|

Per altre informazioni, vedere [Generazioni precedenti delle dimensioni delle macchine virtuali](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv2-series-high-memory"></a>Serie Dv2 con memoria elevata

| Dimensione | Rapporto|
|---|---|
|Standard_D11_v2|1|
|Standard_D12_v2|2|
|Standard_D13_v2|4|
|Standard_D14_v2|8|
|Standard_D15_v2|10|

Per altre informazioni, vedere [Generazioni precedenti delle dimensioni delle macchine virtuali](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="dv2--high-memory-isolated-series"></a>Serie isolata con memoria elevata dv2

| Dimensione | Rapporto|
|---|---|
|Standard_D15i_v2|1|

Per altre informazioni, vedere [Generazioni precedenti delle dimensioni delle macchine virtuali](../articles/virtual-machines/windows/sizes-previous-gen.md).


## <a name="dv3-series"></a>Serie Dv3

| Dimensione | Rapporto|
|---|---|
| Standard_D2_v3|1|
|Standard_D4_v3|2|
|Standard_D8_v3|4|
|Standard_D16_v3|8|
|Standard_D32_v3|16|
|Standard_D64_v3|32|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali di utilizzo generico](../articles/virtual-machines/windows/sizes-general.md#dv3-series-1).

## <a name="esv3-series"></a>Serie ESv3

| Dimensione | Rapporto|
|---|---|
|Standard_E2s_v3|1|
|Standard_E4s_v3|2|
|Standard_E4-2s_v3|2|
|Standard_E8s_v3|4|
|Standard_E8-2s_v3|4|
|Standard_E8-4s_v3|4|
|Standard_E16s_v3|8|
|Standard_E16-4s_v3|8|
|Standard_E16-8s_v3|8|
|Standard_E20s_v3|10|
|Standard_E32s_v3|16|
|Standard_E32-8s_v3|16|
|Standard_E32-16s_v3|16|
|Standard_E64s_v3|28,8|
|Standard_E64-16s_v3|28,8|
|Standard_E64-32s_v3|28,8|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per la memoria](../articles/virtual-machines/windows/sizes-memory.md#esv3-series).

## <a name="esv3-series-isolated-series"></a>Serie ESv3 serie isolata

| Dimensione | Rapporto|
|---|---|
|Standard_E64is_v3|1|

## <a name="ev3-series"></a>Serie Ev3

| Dimensione | Rapporto|
|---|---|
|Standard_E2_v3|1|
|Standard_E4_v3|2|
|Standard_E8_v3|4|
|Standard_E16_v3|8|
|Standard_E20_v3|10|
|Standard_E32_v3|16|
|Standard_E64_v3|32|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per la memoria](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="ev3-series-isolated-series"></a>Serie EV3 serie isolata

| Dimensione | Rapporto|
|---|---|
|Standard_E64i_v3|1|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per la memoria](../articles/virtual-machines/windows/sizes-memory.md#ev3-series).

## <a name="f-series"></a>Serie F

| Dimensione | Rapporto|
|---|---|
| Standard_F1|1|
|Standard_F2|2|
|Standard_F4|4|
|Standard_F8|8|
Standard_F16|16|

Per altre informazioni, vedere [Generazioni precedenti delle dimensioni delle macchine virtuali](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fs-series"></a>Serie FS

| Dimensione | Rapporto|
|---|---|
| Standard_F1s|1|
|Standard_F2s|2|
|Standard_F4s|4|
|Standard_F8s|8|
|Standard_F16s|16|

Per altre informazioni, vedere [Generazioni precedenti delle dimensioni delle macchine virtuali](../articles/virtual-machines/windows/sizes-previous-gen.md).

## <a name="fsv2-series"></a>Serie Fsv2

| Dimensione | Rapporto|
|---|---|
|Standard_F2s_v2|1|
|Standard_F4s_v2|2|
|Standard_F8s_v2|4|
|Standard_F16s_v2|8|
|Standard_F32s_v2|16|
|Standard_F64s_v2|32|
|Standard_F72s_v2|36|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per il calcolo](../articles/virtual-machines/windows/sizes-compute.md#fsv2-series-1).

## <a name="h-series"></a>Serie H

| Dimensione | Rapporto|
|---|---|
| Standard_H8|1|
|Standard_H16|2|

Per altre informazioni, vedere [Dimensioni delle VM High Performance Computing (HPC)](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-high-memory"></a>Serie H con memoria elevata

| Dimensione | Rapporto|
|---|---|
| Standard_H8m|1|
|Standard_H16m|2|

Per altre informazioni, vedere [Dimensioni delle VM High Performance Computing (HPC)](../articles/virtual-machines/windows/sizes-hpc.md).


## <a name="hcs-series"></a>Serie HCS

| Dimensione | Rapporto|
|---|---|
|Standard_HC44rs|1|

Per altre informazioni, vedere [Dimensioni delle VM High Performance Computing (HPC)](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="hbs-series"></a>Serie HBS

| Dimensione | Rapporto|
|---|---|
|Standard_HB60rs|1|

Per altre informazioni, vedere [Dimensioni delle VM High Performance Computing (HPC)](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="hbs-series"></a>Serie HBS

| Dimensione | Rapporto|
|---|---|
|Standard_HB60rs|1|

Per altre informazioni, vedere [Dimensioni delle VM High Performance Computing (HPC)](../articles/virtual-machines/windows/sizes-hpc.md).

## <a name="h-series-low-latency-series"></a>Serie H bassa latenza serie

| Dimensione | Rapporto|
|---|---|
|Standard_H16r|1|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per l'archiviazione](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="h-series-high-memory-low-latency-series"></a>Serie H a bassa latenza di memoria alta serie

| Dimensione | Rapporto|
|---|---|
|Standard_H16mr|1|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per l'archiviazione](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="h-series"></a>Serie H

| Dimensione | Rapporto|
|---|---|
|Standard_H8|1|
|Standard_H16|2|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per l'archiviazione](../articles/virtual-machines/windows/sizes-storage.md).

## <a name="lsv2-series-series"></a>Serie LSv2

| Dimensione | Rapporto|
|---|---|
|Standard_L8s_v2|1|
|Standard_L16s_v2|2|
|Standard_L32s_v2|4|
|Standard_L48s_v2|6|
|Standard_L64s_v2|8|
|Standard_L80s_v2|10|
|Standard_L96s_v2|12|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per l'archiviazione](../articles/virtual-machines/windows//sizes-storage.md#lsv2-series).

## <a name="m-series"></a>Serie M

| Dimensione | Rapporto|
|---|---|
| Standard_M64s|1|
|Standard_M128s|2|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per la memoria](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional"></a>Serie M frazionaria

| Dimensione | Rapporto|
|---|---|
| Standard_M16s|1|
|Standard_M32s|2|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per la memoria](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-high-memory"></a>Serie M frazionaria cin memoria elevata

| Dimensione | Rapporto|
|---|---|
|Standard_M8ms|1|
|Standard_M8-2ms|1|
|Standard_M8-4ms|1|
|Standard_M16ms|2|
|Standard_M16-4ms|2|
|Standard_M16-8ms|2|
|Standard_M32ms|4|
|Standard_M32-8ms|4|
|Standard_M32-16ms|4|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per la memoria](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-fractional-large"></a>Serie M frazionaria grandi dimensioni

| Dimensione | Rapporto|
|---|---|
|Standard_M32ls|1|
|Standard_M64ls|2|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per la memoria](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="m-series-high-memory"></a>Serie M con memoria elevata

| Dimensione | Rapporto|
|---|---|
|Standard_M64ms|1|
|Standard_M64-16ms|1|
|Standard_M64-32ms|1|
|Standard_M128ms|2|
|Standard_M128-32ms|2|
|Standard_M128-64ms|2|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per la memoria](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="ms-series-fractional-tiny"></a>Serie MS - Parziale Tiny

| Dimensione | Rapporto|
|---|---|
|Standard_M32ls|1|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per la memoria](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="msv2-series-high-memory-series"></a>Serie MSv2 serie di memoria elevata

| Dimensione | Rapporto|
|---|---|
|Standard_M208ms_v2|1|
|Standard_M416ms_v2|2|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per la memoria](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="msv2-series"></a>Serie MSv2

| Dimensione | Rapporto|
|---|---|
|Standard_M208s_v2|1|
|Standard_M416s_v2|2|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per la memoria](../articles/virtual-machines/windows/sizes-memory.md#m-series).

## <a name="nc-series"></a>Serie NC

| Dimensione | Rapporto|
|---|---|
|Standard_NC6|1|
|Standard_NC12|2|
|Standard_NC24|4|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per la GPU](../articles/virtual-machines/windows/sizes-gpu.md).

## <a name="ncv2-series"></a>Serie NCv2

| Dimensione | Rapporto|
|---|---|
| Standard_NC6s_v2|1|
|Standard_NC12s_v2|2|
|Standard_NC24s_v2|4|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per la GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="ncv3-series"></a>Serie NCv3

| Dimensione | Rapporto|
|---|---|
| Standard_NC6s_v3|1|
|Standard_NC12s_v3|2|
|Standard_NC24s_v3|4|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per la GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="nd-series"></a>Serie ND

| Dimensione | Rapporto|
|---|---|
| Standard_ND6s|1|
|Standard_ND12s|2|
|Standard_ND24s|4|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per la GPU](../articles/virtual-machines/windows//sizes-gpu.md#nd-series).

## <a name="nv-series"></a>Serie NV

| Dimensione | Rapporto|
|---|---|
| Standard_NV6|1|
|Standard_NV12|2|
|Standard_NV24|4|

## <a name="nvsv3-series"></a>Serie NVSv3

| Dimensione | Rapporto|
|---|---|
|Standard_NV12s_v3|1|
|Standard_NV24s_v3|2|
|Standard_NV48s_v3|4|

## <a name="nds-series-low-latency-series"></a>Serie NDS bassa latenza serie

| Dimensione | Rapporto|
|---|---|
|Standard_ND24rs|1|

## <a name="ncsv3-series-low-latency-series"></a>Serie NCSv3 a bassa latenza

| Dimensione | Rapporto|
|---|---|
|Standard_NC24rs_v3|1|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per la GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv3-series).

## <a name="ncsv2-series-low-latency-series"></a>Serie NCSv2 a bassa latenza

| Dimensione | Rapporto|
|---|---|
|Standard_NC24rs_v2|1|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per la GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).

## <a name="nc-series-low-latency-series"></a>Serie NC bassa latenza serie

| Dimensione | Rapporto|
|---|---|
|Standard_NC24r|1|

Per altre informazioni, vedere [Dimensioni delle macchine virtuali ottimizzate per la GPU](../articles/virtual-machines/windows//sizes-gpu.md#ncv2-series).





