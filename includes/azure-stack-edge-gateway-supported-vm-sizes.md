---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/03/2020
ms.author: alkohli
ms.openlocfilehash: 7ce49873b4e59bcf474deaea4420f56a72c9c589
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2020
ms.locfileid: "89085745"
---
La dimensione di VM determina la quantità di risorse di calcolo, come CPU, GPU e memoria, che viene resa disponibile per la VM. Le macchine virtuali dovrebbero essere create usando una dimensione di VM adeguata per il carico di lavoro. Anche se tutti i computer verranno eseguiti sullo stesso hardware, le dimensioni dei computer hanno limiti diversi per l'accesso al disco, che consentono di gestire l'accesso globale al disco tra le macchine virtuali. Se un carico di lavoro aumenta, è possibile ridimensionare anche una macchina virtuale esistente.

Le macchine virtuali serie dv2 standard seguenti sono supportate per la creazione sul dispositivo Azure Stack Edge.

### <a name="dv2-series"></a>Serie Dv2
|Dimensione     |vCPU     |Memoria (GiB) | Spazio di archiviazione temp (GiB)  | Velocità effettiva massima del disco del sistema operativo (IOPS) | Velocità effettiva massima di archiviazione temporanea (IOPS) | Numero massimo di dischi dati/velocità effettiva (IOPS) | Schede di interfaccia di rete max |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3,5 |50   |500 |3000  |4/4 x 500   |2 |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8/8 x 500   |2 |
|**Standard_D3_v2** |4   |14  |200  |500 |12000 |16/16 x 500 |4 |
|**Standard_D4_v2** |8   |28  |400  |500 |24000 |32/32 x 500 |8 |
|**Standard_D5_v2** |16  |56  |800  |500 |48000 |64/64x500 |8 |

### <a name="dsv2-series"></a>Serie DSv2
|Dimensione     |vCPU     |Memoria (GiB) | Spazio di archiviazione temp (GiB)  | Velocità effettiva massima del disco del sistema operativo (IOPS) | Velocità effettiva massima di archiviazione temporanea (IOPS) | Numero massimo di dischi dati/velocità effettiva (IOPS) | Schede di interfaccia di rete max |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3,5 |7   |1000 |4000  |4/4x2300   |2 |
|**Standard_DS2_v2** |2   |7   |14  |1000 |8000  |8/8x2300   |2 |
|**Standard_DS3_v2** |4   |14  |28  |1000 |16000 |16/16x2300 |4 |
|**Standard_DS4_v2** |8   |28  |56  |1000 |32000 |32/32x2300 |8 |
|**Standard_DS5_v2** |16  |56  |112 |1000 |64000 |64/64x2300 |8 |

### <a name="dv2-series"></a>Serie Dv2
|Dimensione     |vCPU     |Memoria (GiB) | Spazio di archiviazione temp (GiB)  | Velocità effettiva massima del disco del sistema operativo (IOPS) | Velocità effettiva massima di archiviazione temporanea (IOPS) | Numero massimo di dischi dati/velocità effettiva (IOPS) | Schede di interfaccia di rete max |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8/8 x 500    |2 |
|**Standard_D12_v2** |4   |28  |200  |500 |12000  |16/16 x 500  |4 |
|**Standard_D13_v2** |8   |56  |400  |500 |24000  |32/32 x 500  |8 |
|**Standard_D14_v2** |16  |112 |800  |500 |48000  |64/64x500  |8 |


### <a name="dsv2-series"></a>Serie DSv2
|Dimensione     |vCPU     |Memoria (GiB) | Spazio di archiviazione temp (GiB)  | Velocità effettiva massima del disco del sistema operativo (IOPS) | Velocità effettiva massima di archiviazione temporanea (IOPS) | Numero massimo di dischi dati/velocità effettiva (IOPS) | Schede di interfaccia di rete max |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1000 |8000   |4/4x2300    |2 |
|**Standard_DS12_v2** |4   |28  |56   |1000 |16000  |8/8x2300    |4 |
|**Standard_DS13_v2** |8   |56  |112  |1000 |32000  |16/16x2300  |8 |
|**Standard_DS14_v2** |16  |112 |224  |1000 |64000  |32/32x2300  |8 |

Per altre informazioni, vedere la [serie dv2 in per utilizzo generico dimensioni delle macchine virtuali](../articles/virtual-machines/dv2-dsv2-series.md#dv2-series).
