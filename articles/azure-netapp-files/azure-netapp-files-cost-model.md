---
title: Modello di costo per i file di Azure NetApp | Microsoft Docs
description: Descrive il modello di costo per i file di NetApp di Azure per la gestione delle spese dal servizio.
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
ms.openlocfilehash: b06e3366224b90899dd3f9f9439edf897de82794
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524221"
---
# <a name="cost-model-for-azure-netapp-files"></a>Modello di costi per Azure NetApp Files 

Informazioni sul modello di costo per i file di NetApp Azure semplifica la gestione delle spese dal servizio.

## <a name="calculation-of-capacity-consumption"></a>Calcolo dell'utilizzo della capacità

File di NetApp Azure viene fatturato su capacità di archiviazione con provisioning.  Capacità con provisioning viene allocata tramite la creazione di pool di capacità.  Pool di capacità sono addebitati in $/ provisioning-GiB/mese in incrementi di un'ora. La dimensione minima per un pool di capacità della singola è 4 TiB e i pool di capacità possono essere successivamente espansi in incrementi di 1 TiB. I volumi vengono creati all'interno dei pool di capacità.  Ogni volume viene assegnata una quota che decrementa dalla capacità con provisioning pool. La quota che può essere assegnata ai volumi intervalli da un minimo di 100 GiB a un massimo di TiB 92.  

Per un volume attivo, il consumo di capacità ai fini della quota si basa sulla logica capacità (effettiva).

Se il consumo di capacità effettiva di un volume supera la quota di archiviazione, il volume può continuare ad aumentare. Operazioni di scrittura ancora saranno dunque consentite fino a quando le dimensioni del volume effettivo sono inferiore al limite di sistema (100 TiB).  

La capacità totale utilizzata in un pool di capacità su relativa quantità sottoposte a provisioning è la somma della maggiore della quota assegnata o effettivo il consumo di tutti i volumi all'interno del pool: 

   ![Calcolo di capacità totale utilizzata](../media/azure-netapp-files/azure-netapp-files-total-used-capacity.png)

Il diagramma seguente illustra questi concetti.  
* È necessario un pool di capacità con 4 TiB di capacità con provisioning.  Il pool contiene tre volumi.  
    * Volume 1 viene assegnato una quota pari a 2 TiB e dispone di 800 GiB di consumo.  
    * Volume 2 viene assegnato a una quota pari a 1 TiB e ha 100 GiB di consumo.  
    * Volume 3 è assegnato una quota di 500 GiB, ma dispone di 800 GiB di consumo (eccedenze di utilizzo).  
* Pool di capacità è a consumo per 4 TiB di capacità (la quantità di provisioning).  
    3,8 viene consumato TiB di capacità (TiB 2 e 1 TiB di quota da1 volumi e 2 e 800 GiB di consumo effettivo per il Volume 3). E 200 GiB di capacità rimanente.

   ![Pool di capacità con tre volumi](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-three-vols.png)

## <a name="overage-in-capacity-consumption"></a>Eccedenza nell'utilizzo della capacità  

Quando il totale usato capacità di un pool supera la capacità con provisioning, le scritture dati sono ancora consentite.  Dopo il periodo di tolleranza (un'ora), se la capacità utilizzata del pool supera ancora la capacità con provisioning, quindi le dimensioni del pool verranno automaticamente aumentata in base a incrementi di 1 TiB fino a quando la capacità fornita è supera alla capacità totale utilizzata.  Ad esempio, nella figura precedente, se 3 Volume continua a crescere e il consumo effettivo raggiunge TiB 1,2, quindi dopo il periodo di tolleranza, il pool verrà automaticamente ridimensionato da 5 TiB.  Il risultato è che la capacità del pool con provisioning (5 TiB) supera la capacità usata (4.2 TiB).  

## <a name="manual-changes-of-the-pool-size"></a>Modifiche manuali delle dimensioni del pool  

Manualmente, è possibile aumentare o diminuire le dimensioni del pool. Tuttavia, si applicano i vincoli seguenti:
* Limiti minimi e massimi di servizio  
    Vedere l'articolo [dei limiti delle risorse](azure-netapp-files-resource-limits.md).
* Un incremento di 1 TiB dopo l'acquisto minimo iniziale 4 TiB
* Un incremento fatturabile minima di un'ora
* Le dimensioni del pool con provisioning non possono essere ridotte a minore capacità totale utilizzata nel pool.

## <a name="behavior-of-maximum-size-pool-overage"></a>Comportamento di eccedenza di dimensioni massime pool   

Le dimensioni massime di un pool di capacità che è possibile creare o ridimensionare a sono 500 TiB.  Quando il totale di capacità usata in un pool di capacità supera 500 TiB, si verificherà nelle situazioni seguenti:
* Le scritture dati ancora saranno dunque consentite (se il volume è di sotto il valore massimo del sistema di 100 TiB).
* Dopo il periodo di tolleranza di un'ora, il pool verrà ridimensionato automaticamente in incrementi di 1 TiB, fino a quando la capacità del pool provisioning supera capacità totale utilizzata.
* Le altre il provisioning e fatturato capacità del pool che superano 500 TiB non può essere usato per assegnare la quota del volume. Non può essere usato anche per espandere i limiti di QoS di prestazioni.  
    Visualizzare [livelli di servizio](azure-netapp-files-service-levels.md) sui limiti delle prestazioni e ridimensionamento di QoS.

Il diagramma seguente illustra questi concetti:
* È necessario un pool di capacità con un livello di archiviazione Premium e una capacità di 500 TiB. Il pool contiene nove volumi.
    * I volumi da 1 a 8 sono assegnati una quota pari a 60 TiB.  La capacità totale utilizzata è 480 TiB.  
        Ogni volume ha un limite di QoS di 3,75 GiB/s di velocità effettiva (TiB 60 * 64 MiB/s).  
    * Volume 9 viene assegnata una quota pari a 20 TiB.  
        Volume 9 ha un limite di QoS di 1,25 GiB/s di velocità effettiva (TiB 60 * 64 MiB/s).
* Volume 9 è uno scenario in eccedenza. Include 25 TiB di consumo effettivo.  
    * Dopo il periodo di tolleranza di un'ora, verrà ridimensionato il pool di capacità a TiB 505.  
        Capacità utilizzata del totale, ovvero 8 * 60 =-quota TiB per i volumi da 1 a 8 e 25 TiB di consumo effettivo 9 nel Volume.
    * La capacità fatturata è TiB 505.
    * Quota del volume per Volume 9 non può essere aumentata (perché la quota totale assegnata per il pool non può superare i 500 TiB).
    * Velocità effettiva aggiuntiva QoS non è possibile assegnare (perché i criteri QoS totale per il pool si basa sul 500 TiB).

   ![Pool di capacità con nove volumi](../media/azure-netapp-files/azure-netapp-files-capacity-pool-with-nine-vols.png)

## <a name="capacity-consumption-of-snapshots"></a>Utilizzo della capacità di snapshot 

Il consumo di capacità di snapshot in file di NetApp Azure viene addebitato ai fini della quota del volume padre.  Di conseguenza, condivide la stessa tariffa di fatturazione del pool di capacità a cui appartiene il volume.  Tuttavia, a differenza del volume attivo, il consumo di snapshot viene misurato dipende dalla capacità incrementale utilizzata.  Gli snapshot di file NetApp Azure sono invece differenziali in natura. A seconda della frequenza di modifica dei dati, gli snapshot usano spesso molto meno capacità rispetto alla capacità del volume attivo logica. Si supponga, ad esempio, che è presente uno snapshot di un volume di 500 GiB contenente solo 10 GiB di dati differenziali. La capacità che viene addebitata ai fini della quota di volume per tale snapshot sarebbe di 10 GiB, non di 500 GiB. 

## <a name="next-steps"></a>Passaggi successivi

* [File di Azure NetApp pagina dei prezzi](https://azure.microsoft.com/pricing/details/storage/netapp/)
* [Livelli di servizio per Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Limiti delle risorse per Azure NetApp Files](azure-netapp-files-resource-limits.md)
