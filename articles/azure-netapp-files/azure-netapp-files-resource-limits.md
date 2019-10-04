---
title: Limiti delle risorse per Azure NetApp Files | Microsoft Docs
description: Vengono descritti i limiti per le risorse Azure NetApp Files e come richiedere un aumento del limite di risorse.
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
ms.date: 09/20/2019
ms.author: b-juche
ms.openlocfilehash: f7213ddee5d7bdfd41508f5fee66de63cde5b7c4
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/20/2019
ms.locfileid: "71170031"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limiti delle risorse per Azure NetApp Files

Conoscere i limiti delle risorse per Azure NetApp Files è utile per gestire i volumi.

## <a name="resource-limits"></a>Limiti delle risorse

La tabella seguente descrive i limiti delle risorse per Azure NetApp Files:

|  Risorsa  |  Limite predefinito  |  Modificabile tramite richiesta di supporto  |
|----------------|---------------------|--------------------------------------|
|  Numero di account NetApp per sottoscrizione di Azure   |  10    |  Yes   |
|  Numero di pool di capacità per account NetApp   |    25     |   Yes   |
|  Numero di volumi per pool di capacità     |    500   |    Yes     |
|  Numero di snapshot per volume       |    255     |    No        |
|  Numero di subnet delegate a Azure NetApp Files (Microsoft. NetApp/volumes) per rete virtuale di Azure    |   1   |    No    |
|  Numero di indirizzi IP in un VNet (incluso reti virtuali con peering) che possono accedere Azure NetApp Files   |    1000   |    Yes   |
|  Dimensioni minime di un singolo pool di capacità   |  4 TiB     |    No  |
|  Dimensioni massime di un singolo pool di capacità    |  500 TiB   |   No   |
|  Dimensioni minime di un singolo volume    |    100 GiB    |    No    |
|  Dimensioni massime di un singolo volume     |    100 TiB    |    No    |
|  Numero massimo di file ([maxfiles](#maxfiles)) per volume     |    100 milioni    |    Yes    |    
|  Dimensioni massime di un singolo file     |    16 TiB    |    No    |    

## Limiti di maxfiles<a name="maxfiles"></a> 

I volumi Azure NetApp Files hanno un limite denominato *maxfiles*. Il limite maxfiles è il numero di file che possono essere contenuti in un volume. Il limite di maxfiles per un volume Azure NetApp Files viene indicizzato in base alla dimensione (quota) del volume. Il limite di maxfiles per un volume aumenta o diminuisce alla velocità di 20 milioni file per TiB delle dimensioni del volume di cui è stato effettuato il provisioning. 

Il servizio regola dinamicamente il limite di maxfiles per un volume in base alle dimensioni di cui è stato effettuato il provisioning. Ad esempio, un volume configurato inizialmente con una dimensione di 1 TiB avrà un limite di maxfiles pari a 20 milioni. Le successive modifiche alle dimensioni del volume comporteranno una riregolazione automatica del limite di maxfiles in base alle regole seguenti: 

|    Dimensioni del volume (quota)     |  Riregolazione automatica del limite maxfiles    |
|----------------------------|-------------------|
|    < 1 TiB                 |    20 milioni     |
|    > = 1 TiB ma < 2 TiB    |    40 milioni     |
|    > = 2 TiB ma < 3 TiB    |    60 milioni     |
|    > = 3 TiB ma < 4 TiB    |    80 milioni     |
|    > = 4 TiB                |    100 milioni    |

Per qualsiasi dimensione del volume, è possibile avviare una [richiesta di supporto](#limit_increase) per aumentare il limite di maxfiles oltre 100 milioni.

## Incremento limite richieste<a name="limit_increase"></a> 

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
        |  Maxfiles  |  *ID sottoscrizione, URI dell'account, URI del pool, URI del volume*   |  *Numero massimo di **maxfiles** richiesto*     |  *Quale scenario o caso di utilizzo ha richiesto la richiesta?*  |    

    2. Specificare il metodo di supporto appropriato e fornire le informazioni sul contratto.

    3. Fare clic su **Avanti: Per creare la** richiesta, vedere + Crea. 


## <a name="next-steps"></a>Passaggi successivi  

- [Informazioni sulla gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modello di costo per Azure NetApp Files](azure-netapp-files-cost-model.md)
