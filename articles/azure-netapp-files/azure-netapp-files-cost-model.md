---
title: Modello di costo per i file NetApp di Azure Documenti Microsoft
description: Descrive il modello dei costi per i file NetApp di Azure per la gestione delle spese dal servizio.
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
ms.openlocfilehash: aea783b818550b8219e1a0498256280f61f678e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70995107"
---
# <a name="cost-model-for-azure-netapp-files"></a>Modello di costi per Azure NetApp Files 

La comprensione del modello dei costi per i file NetApp di Azure consente di gestire le spese dal servizio.

## <a name="calculation-of-capacity-consumption"></a>Calcolo del consumo di capacità

File NetApp di Azure fatturati sulla capacità di archiviazione di cui è stato eseguito il provisioning.  La capacità di provisioning viene allocata creando pool di capacità.  I pool di capacità vengono fatturati in base a un'incremento orario/GiB/mese di cui è stato eseguito il provisioning. La dimensione minima per un singolo pool di capacità è 4 TiB e i pool di capacità possono essere successivamente espansi in incrementi di 1 TiB. I volumi vengono creati all'interno di pool di capacità.  A ogni volume viene assegnata una quota che decrementa dalla capacità di cui è stato eseguito il provisioning del pool. La quota che può essere assegnata ai volumi varia da un minimo di 100 GiB a un massimo di 100 TiB.  

Per un volume attivo, il consumo di capacità rispetto alla quota si basa sulla capacità logica (effettiva).

Se il consumo di capacità effettivo di un volume supera la quota di archiviazione, il volume può continuare a crescere. Le scritture saranno comunque consentite finché la dimensione effettiva del volume è inferiore al limite di sistema (100 TiB).  

La capacità totale utilizzata in un pool di capacità rispetto all'importo di cui è stato eseguito il provisioning è la somma della quota assegnata o del consumo effettivo di tutti i volumi all'interno del pool: 

   ![Calcolo della capacità totale utilizzata](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Il diagramma seguente illustra questi concetti.  
* Abbiamo un pool di capacità con 4 TiB di capacità di provisioning.  Il pool contiene tre volumi.  
    * Al Volume 1 viene assegnata una quota di 2 TiB e 800 GiB di consumo.  
    * Al Volume 2 viene assegnata una quota di 1 TiB e ha 100 GiB di consumo.  
    * Al Volume 3 viene assegnata una quota di 500 GiB ma 800 GiB di consumo (overage).  
* Il pool di capacità viene misurato per 4 TiB di capacità (l'importo di cui è stato eseguito il provisioning).  
    3.8 Viene consumato il TIB della capacità (2 TiB e 1 TiB di quota dai volumi 1 e 2 e 800 GiB di consumo effettivo per il Volume 3). E rimangono 200 GiB di capacità.

   ![Pool di capacità con tre volumi](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Sovraccarico nel consumo di capacità  

Quando la capacità totale utilizzata di un pool supera la capacità di cui è stato eseguito il provisioning, le scritture di dati sono ancora consentite.  Dopo il periodo di prova (un'ora), se la capacità utilizzata del pool supera ancora la capacità di cui è stato eseguito il provisioning, le dimensioni del pool verranno aumentate automaticamente con incrementi di 1 TiB fino a quando la capacità di cui è stato eseguito il provisioning non sarà superiore alla capacità totale utilizzata.  Ad esempio, nell'illustrazione precedente, se il Volume 3 continua a crescere e il consumo effettivo raggiunge 1,2 TiB, dopo il periodo di tolleranza, il pool verrà automaticamente ridimensionato a 5 TiB.  Il risultato è che la capacità del pool di cui è stato eseguito il provisioning (5 TiB) supera la capacità utilizzata (4.2 TiB).  

## <a name="manual-changes-of-the-pool-size"></a>Modifiche manuali delle dimensioni del pool  

È possibile aumentare o ridurre manualmente le dimensioni del pool. Tuttavia, si applicano i vincoli seguenti:However, the following constraints apply:
* Limiti minimi e massimi del servizio  
    Vedere l'articolo sui [limiti delle risorse](azure-netapp-files-resource-limits.md).
* Un incremento di 1-TiB dopo l'acquisto minimo iniziale di 4 TiB
* Un incremento di fatturazione minimo di un'ora
* Le dimensioni del pool di cui è stato eseguito il provisioning non possono essere ridotte a un valore inferiore alla capacità totale utilizzata nel pool.

## <a name="behavior-of-maximum-size-pool-overage"></a>Comportamento dell'overage del pool di dimensioni massime   

La dimensione massima di un pool di capacità che è possibile creare o ridimensionare è 500 TiB.  Quando la capacità totale utilizzata in un pool di capacità supera 500 TiB, si verificheranno le seguenti situazioni:
* Le scritture dati saranno comunque consentite (se il volume è al di sotto del massimo di 100 TiB).
* Dopo il periodo di tolleranza di un'ora, il pool verrà ridimensionato automaticamente con incrementi di 1-TiB, fino a quando la capacità di provisioning del pool non supera la capacità totale utilizzata.
* La capacità aggiuntiva del pool con provisioning e fatturazione superiore a 500 TiB non può essere utilizzata per assegnare la quota di volume. Inoltre, non può essere utilizzato per espandere i limiti QoS delle prestazioni.  
    Visualizzare i [livelli di servizio](azure-netapp-files-service-levels.md) sui limiti di prestazioni e sul dimensionamento QoS.See service levels about performance limits and QoS sizing.

Il diagramma seguente illustra questi concetti:The diagram below illustrates these concepts:
* Abbiamo un pool di capacità con un livello di archiviazione Premium e una capacità di 500-TiB. Il pool contiene nove volumi.
    * Ai volumi da 1 a 8 viene assegnata una quota di 60 TiB ciascuno.  La capacità totale utilizzata è 480 TiB.  
        Ogni volume ha un limite di QoS di 3,75 GiB/s di velocità effettiva (60 TiB e 64 MiB/s).  
    * Al Volume 9 viene assegnata una quota di 20 TiB.  
        Il volume 9 ha un limite di QoS di 1,25 GiB/s di velocità effettiva (20 TiB e 64 MiB/s).
* Il volume 9 è uno scenario di esadio. Ha 25 TiB di consumo effettivo.  
    * Dopo il periodo di tolleranza di un'ora, il pool di capacità verrà ridimensionato a 505 TiB.  
        Ovvero, la capacità totale utilizzata è pari a 8 x 60-TiB per i volumi da 1 a 8 e 25 TiB di consumo effettivo per il Volume 9.
    * La capacità fatturata è 505 TiB.
    * La quota di volume per il Volume 9 non può essere aumentata (perché la quota totale assegnata per il pool non può superare 500 TiB).
    * È possibile che la velocità effettiva QoS aggiuntiva non venga assegnata (perché il QoS totale per il pool è ancora basato su 500 TiB).

   ![Pool di capacità con nove volumi](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Consumo di capacità delle istantanee 

Il consumo di capacità degli snapshot nei file NetApp di Azure viene addebitato rispetto alla quota del volume padre.  Di conseguenza, condivide la stessa tariffa di fatturazione del pool di capacità a cui appartiene il volume.  Tuttavia, a differenza del volume attivo, il consumo di snapshot viene misurato in base alla capacità incrementale utilizzata.  Gli snapshot dei file NetApp di Azure sono di natura differenziale. A seconda della frequenza di modifica dei dati, gli snapshot spesso consumano una capacità molto inferiore rispetto alla capacità logica del volume attivo. Si supponga, ad esempio, di disporre di uno snapshot di un volume 500-GiB che contiene solo 10 GiB di dati differenziali. La capacità addebitata rispetto alla quota di volume per lo snapshot sarebbe 10 GiB, non 500 GiB. 

## <a name="next-steps"></a>Passaggi successivi

* [Pagina dei prezzi file NetApp di AzureAzure NetApp Files pricing page](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Livelli di servizio per Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Limiti delle risorse per Azure NetApp Files](azure-netapp-files-resource-limits.md)
