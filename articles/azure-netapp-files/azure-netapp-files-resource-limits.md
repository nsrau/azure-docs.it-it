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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 15d0a584d88045f6020162a88124cd9d6a4735bf
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984012"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limiti delle risorse per Azure NetApp Files

Conoscere i limiti delle risorse per Azure NetApp Files è utile per gestire i volumi.

## <a name="resource-limits"></a>Limiti delle risorse

La tabella seguente descrive i limiti delle risorse per Azure NetApp Files:

|  Risorsa  |  Limite predefinito  |  Modificabile tramite richiesta di supporto  |
|----------------|---------------------|--------------------------------------|
|  Numero di account NetApp per sottoscrizione di Azure   |  10    |  Sì   |
|  Numero di pool di capacità per account NetApp   |    25     |   Sì   |
|  Numero di volumi per pool di capacità     |    500   |    Sì     |
|  Numero di snapshot per volume       |    255     |    No        |
|  Numero di subnet delegate a Azure NetApp Files (Microsoft. NetApp/volumes) per rete virtuale di Azure    |   1   |    No    |
|  Numero di indirizzi IP in un VNet (incluso reti virtuali con peering) che possono accedere Azure NetApp Files   |    1000   |    Sì   |
|  Dimensioni minime di un singolo pool di capacità   |  4 TiB     |    No  |
|  Dimensioni massime di un singolo pool di capacità    |  500 TiB   |   No   |
|  Dimensioni minime di un singolo volume    |    100 GiB    |    No    |
|  Dimensioni massime di un singolo volume     |    100 TiB    |    No       |
|  Numero massimo di file (inode) per volume     |    50 milioni    |    No    |    

## <a name="request-limit-increase"></a>Incremento limite richieste 

È possibile creare una richiesta di supporto di Azure per aumentare i limiti regolabili della tabella precedente. 

Dal piano di navigazione portale di Azure: 

1. Fare clic su **Guida e supporto**.
2. Fare clic su **+ nuova richiesta di supporto**.
3. Nella scheda informazioni di base specificare le informazioni seguenti: 
    1. Tipo di problema: Selezionare i **limiti del servizio e della sottoscrizione (quote)** .
    2. Sottoscrizioni Selezionare la sottoscrizione per la risorsa necessaria per aumentare la quota.
    3. Tipo di quota: Selezionare **archiviazione: Azure NetApp Files limiti**.
    4. Fare clic su **Avanti: Soluzioni**.
4. Nella scheda Dettagli:
    1. Nella casella Descrizione specificare le informazioni seguenti per il tipo di risorsa corrispondente:

        |  Risorsa  |    Risorse padre      |    Nuovi limiti richiesti     |    Motivo dell'aumento della quota       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Account |  *ID sottoscrizione*   |  *Nuovo numero di **conto** massimo richiesto*    |  *Quale scenario o caso di utilizzo ha richiesto la richiesta?*  |
        |  Pool    |  *ID sottoscrizione, URI account*  |  *Numero massimo di **pool** richiesti*   |  *Quale scenario o caso di utilizzo ha richiesto la richiesta?*  |
        |  Volume  |  *ID sottoscrizione, URI dell'account, URI del pool*   |  *Numero massimo di **volumi** massimo richiesto*     |  *Quale scenario o caso di utilizzo ha richiesto la richiesta?*  |

    2. Specificare il metodo di supporto appropriato e fornire le informazioni sul contratto.

    3. Fare clic su **Avanti: Per creare la** richiesta, vedere + Crea. 


## <a name="next-steps"></a>Passaggi successivi  

- [Informazioni sulla gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modello di costo per Azure NetApp Files](azure-netapp-files-cost-model.md)
