---
title: Limiti delle risorse per Azure NetApp Files | Microsoft Docs
description: Descrive i limiti per le risorse di Azure NetApp Files, inclusi i limiti per account NetApp, pool di capacità, volumi, snapshot e la subnet delegata.
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
ms.date: 05/02/2019
ms.author: b-juche
ms.openlocfilehash: b55467d77beb8f97b8e392b72682268ae0407e54
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65826368"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limiti delle risorse per Azure NetApp Files

Conoscere i limiti delle risorse per Azure NetApp Files è utile per gestire i volumi.

## <a name="resource-limits"></a>Limiti delle risorse

La tabella seguente descrive i limiti delle risorse per i file di Azure NetApp:

|  Resource  |  Limite predefinito  |  Modificabile tramite richiesta di supporto  |
|----------------|---------------------|--------------------------------------|
|  Numero di account NetApp per ogni sottoscrizione di Azure   |  10    |  Yes   |
|  Numero di pool di capacità per ogni account NetApp   |    25     |   Yes   |
|  Numero di volumi per ogni pool di capacità     |    500   |    Yes     |
|  Numero di snapshot per volume       |    255     |    No        |
|  Numero di subnet delegate NetApp in file di Azure (Microsoft.NetApp/volumes) per ogni rete virtuale di Azure    |   1   |    No    |
|  Numero massimo di macchine virtuali (che include le reti virtuali con peering) per connettersi a un volume     |    1000   |    No   |
|  Dimensioni minime di un pool di capacità della singola   |  4 TiB     |    No  |
|  Dimensioni massime di un pool di capacità della singola    |  500 TiB   |   No   |
|  Dimensione minima di un singolo volume    |    100 GiB    |    No    |
|  Valore massimo assegnato quota di un singolo volume *   |   92 TiB   |    No   |
|  Dimensioni massime di un singolo volume *     |    100 TiB    |    No       |

\* Un volume può essere creato manualmente o ridimensionato a quello ottenuto 92 TiB. Tuttavia, un volume può aumentare fino a 100 TiB in uno scenario in eccedenza. Visualizzare [modello di costo per i file di Azure NetApp](azure-netapp-files-cost-model.md) per informazioni dettagliate sulle eccedenze di utilizzo della capacità. 

## <a name="request-limit-increase"></a>Richiedere l'aumento del limite 

È possibile creare una richiesta di supporto tecnico di Azure per aumentare i limiti regolabili nella tabella precedente. 

Rispetto al piano di navigazione del portale di Azure: 

1. Fare clic su **Guida e supporto**.
2. Fare clic su **+ nuova richiesta di supporto**.
3. Nella scheda elementi di base, fornire le informazioni seguenti: 
    1. Tipo di problema: Selezionare **limiti di servizio e sottoscrizione (quote)** .
    2. Sottoscrizioni: Selezionare la sottoscrizione per la risorsa che è necessario aumentare la quota.
    3. Tipo di quota: Selezionare **archiviazione: File di Azure NetApp limita**.
    4. Fare clic su **Avanti: Soluzioni**.
4. Nella scheda Dettagli:
    1. Nella finestra di descrizione, fornire le informazioni seguenti per il tipo di risorsa corrispondente:

        |  Resource  |    Risorse padre      |    Nuovi limiti richiesti     |    Motivo di aumento della quota       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Account |  *ID sottoscrizione*   |  *Richiesta nuovo massimo **account** numero*    |  *Quale caso di scenario o l'utilizzo richiesto della richiesta?*  |
        |  Pool    |  *ID sottoscrizione, URI dell'Account*  |  *Richiesta nuovo massimo **pool** numero*   |  *Quale caso di scenario o l'utilizzo richiesto della richiesta?*  |
        |  Volume  |  *ID sottoscrizione, URI, Account del Pool URI*   |  *Richiesta nuovo massimo **volume** numero*     |  *Quale caso di scenario o l'utilizzo richiesto della richiesta?*  |

    2. Specificare appropriato il metodo di supporto e fornisce le informazioni di contratto.

    3. Fare clic su **Avanti: Rivedi e crea** per creare la richiesta. 


## <a name="next-steps"></a>Passaggi successivi  

- [Informazioni sulla gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modello di costo per i file di Azure NetApp](azure-netapp-files-cost-model.md)
