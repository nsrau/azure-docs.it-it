---
title: Modello di costo per Azure NetApp Files | Microsoft Docs
description: Viene descritto il modello di costo per Azure NetApp Files per gestire le spese del servizio.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/01/2019
ms.author: b-juche
ms.openlocfilehash: 563416418b3f387f103fddc88b3ba9ad4c93fdd4
ms.sourcegitcommit: 18061d0ea18ce2c2ac10652685323c6728fe8d5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030790"
---
# <a name="cost-model-for-azure-netapp-files"></a>Modello di costi per Azure NetApp Files 

Comprendere il modello di costo per Azure NetApp Files consente di gestire le spese del servizio.

## <a name="calculation-of-capacity-consumption"></a>Calcolo dell'utilizzo della capacità

Azure NetApp Files viene addebitato in base alla capacità di archiviazione con provisioning.  La capacità di provisioning viene allocata creando pool di capacità.  I pool di capacità vengono fatturati in base a $/provisioned-GiB/month con incrementi orari. La dimensione minima per un singolo pool di capacità è 4 TiB e i pool di capacità possono essere successivamente espansi in incrementi di 1 TiB. I volumi vengono creati all'interno di pool di capacità.  A ogni volume viene assegnata una quota che decrementa dalla capacità con provisioning dei pool. La quota che può essere assegnata a volumi è compresa tra un minimo di 100 GiB e un massimo di 100 TiB.  

Per un volume attivo, l'utilizzo della capacità rispetto alla quota è basato sulla capacità logica (efficace).

Se il consumo di capacità effettivo di un volume supera la quota di archiviazione, il volume può continuare ad aumentare. Le scritture saranno ancora consentite purché le dimensioni effettive del volume siano inferiori al limite di sistema (100 TiB).  

La capacità totale utilizzata in un pool di capacità rispetto alla relativa quantità di cui è stato effettuato il provisioning è la somma del valore maggiore tra la quota assegnata o il consumo effettivo di tutti i volumi all'interno del pool: 

   ![Calcolo capacità totale utilizzato](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Il diagramma seguente illustra questi concetti.  
* È disponibile un pool di capacità con 4 TiB di capacità di cui è stato effettuato il provisioning.  Il pool contiene tre volumi.  
    * Al volume 1 viene assegnata una quota di 2 TiB e 800 GiB di consumo.  
    * Al volume 2 viene assegnata una quota di 1 TiB con 100 GiB di consumo.  
    * Al volume 3 viene assegnata una quota di 500 GiB, ma con 800 GiB di consumo (eccedenza).  
* Il pool di capacità viene calcolato a consumo per 4 TiB di capacità (importo con provisioning).  
    3,8 TiB di capacità viene utilizzato (2 TiB e 1 TiB di quota dai volumi 1 e 2 e 800 GiB del consumo effettivo per il volume 3). E 200 GiB di capacità rimanenti.

   ![Pool di capacità con tre volumi](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Eccedenza nell'utilizzo della capacità  

Quando la capacità totale utilizzata di un pool supera la capacità di provisioning, le scritture dei dati sono ancora consentite.  Dopo il periodo di tolleranza (un'ora), se la capacità utilizzata del pool supera ancora la capacità di cui è stato effettuato il provisioning, le dimensioni del pool verranno automaticamente aumentate con incrementi di 1 TiB fino a quando la capacità di cui è stato effettuato il provisioning è maggiore della capacità totale utilizzata.  Nell'illustrazione precedente, ad esempio, se il volume 3 continua ad aumentare e il consumo effettivo raggiunge 1,2 TiB, dopo il periodo di tolleranza il pool verrà automaticamente ridimensionato a 5 TiB.  Il risultato è che la capacità del pool di cui è stato effettuato il provisioning (5 TiB) supera la capacità utilizzata (4,2 TiB).  

## <a name="manual-changes-of-the-pool-size"></a>Modifiche manuali delle dimensioni del pool  

È possibile aumentare o ridurre manualmente le dimensioni del pool. Tuttavia, si applicano i vincoli seguenti:
* Limiti minimi e massimi del servizio  
    Vedere l'articolo sui [limiti delle risorse](azure-netapp-files-resource-limits.md).
* Incremento a 1 TiB dopo l'acquisto iniziale di 4 TiB minimo
* Incremento di fatturazione minimo di un'ora
* È possibile che le dimensioni del pool di cui è stato effettuato il provisioning non siano diminuite fino alla capacità totale utilizzata nel pool.

## <a name="behavior-of-maximum-size-pool-overage"></a>Comportamento dell'eccedenza del pool di dimensioni massime   

La dimensione massima di un pool di capacità che è possibile creare o ridimensionare è 500 TiB.  Quando la capacità totale utilizzata in un pool di capacità supera 500 TiB, si verificheranno le situazioni seguenti:
* Le scritture dei dati saranno comunque consentite (se il volume è inferiore al massimo del sistema 100 TiB).
* Dopo il periodo di tolleranza di un'ora, il pool verrà ridimensionato automaticamente in incrementi di 1 TiB, fino a quando la capacità di provisioning del pool supera la capacità totale utilizzata.
* Non è possibile usare la capacità aggiuntiva del pool con provisioning e fatturazione superiore a 500 TiB per assegnare la quota del volume. Non è inoltre possibile utilizzare per espandere i limiti QoS delle prestazioni.  
    Vedere [livelli di servizio](azure-netapp-files-service-levels.md) relativi ai limiti delle prestazioni e al dimensionamento QoS.

Il diagramma seguente illustra questi concetti:
* È presente un pool di capacità con un livello di archiviazione Premium e una capacità di 500-TiB. Il pool contiene nove volumi.
    * Ai volumi da 1 a 8 viene assegnata una quota di 60 TiB ciascuna.  La capacità totale utilizzata è 480 TiB.  
        Ogni volume ha un limite QoS di 3,75 GiB/s di velocità effettiva (60 TiB * 64 MiB/s).  
    * Al volume 9 viene assegnata una quota di 20 TiB.  
        Volume 9 ha un limite QoS di 1,25 GiB/s di velocità effettiva (60 TiB * 64 MiB/s).
* Volume 9 è uno scenario in eccedenza. Ha 25 TiB di consumo effettivo.  
    * Dopo il periodo di tolleranza di un'ora, il pool di capacità verrà ridimensionato a 505 TiB.  
        Ovvero la capacità totale usata = 8 * 60-la quota TiB per i volumi da 1 a 8 e 25 TiB del consumo effettivo per il volume 9.
    * La capacità fatturata è 505 TiB.
    * Non è possibile aumentare la quota volume per il volume 9 (perché la quota totale assegnata per il pool non può superare 500 TiB).
    * La velocità effettiva QoS aggiuntiva potrebbe non essere assegnata (poiché la QoS totale per il pool è ancora basata su 500 TiB).

   ![Pool di capacità con nove volumi](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Consumo di capacità degli snapshot 

Il consumo di capacità degli snapshot in Azure NetApp Files viene addebitato in base alla quota del volume padre.  Di conseguenza, condivide la stessa tariffa di fatturazione del pool di capacità a cui appartiene il volume.  Tuttavia, a differenza del volume attivo, il consumo di snapshot viene misurato in base alla capacità incrementale utilizzata.  Gli snapshot Azure NetApp Files sono di natura differenziale. A seconda della frequenza di modifica dei dati, gli snapshot spesso utilizzano una capacità molto inferiore rispetto alla capacità logica del volume attivo. Si supponga, ad esempio, di avere uno snapshot di un volume 500-GiB che contiene solo 10 GiB di dati differenziali. La capacità addebitata in base alla quota del volume per lo snapshot è 10 GiB, non 500 GiB. 

## <a name="next-steps"></a>Passaggi successivi

* [Pagina dei prezzi di Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Livelli di servizio per Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Limiti delle risorse per Azure NetApp Files](azure-netapp-files-resource-limits.md)
