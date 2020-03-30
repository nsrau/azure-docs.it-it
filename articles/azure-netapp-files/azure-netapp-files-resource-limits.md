---
title: Limiti delle risorse per Azure NetApp Files | Microsoft Docs
description: Descrive i limiti per le risorse dei file NetApp di Azure e come richiedere l'aumento del limite di risorse.
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
ms.date: 3/13/2020
ms.author: b-juche
ms.openlocfilehash: 36b2d50722a1840e461d6907f440d859c7c82117
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408828"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Limiti delle risorse per Azure NetApp Files

Conoscere i limiti delle risorse per Azure NetApp Files è utile per gestire i volumi.

## <a name="resource-limits"></a>Limiti delle risorse

Nella tabella seguente vengono descritti i limiti delle risorse per i file NetApp di Azure:The following table describes resource limits for Azure NetApp Files:

|  Risorsa  |  Limite predefinito  |  Regolabile tramite richiesta di supporto  |
|----------------|---------------------|--------------------------------------|
|  Numero di account NetApp per area di AzureNumber of NetApp accounts per Azure region   |  10    |  Sì   |
|  Numero di pool di capacità per account NetApp   |    25     |   Sì   |
|  Numero di volumi per pool di capacità     |    500   |    Sì     |
|  Numero di snapshot per volume       |    255     |    No        |
|  Numero di subnet delegate ai file NetApp di Azure (Microsoft.NetApp/volumi) per ogni rete virtuale di AzureNumber of subnets delegated to Azure NetApp Files (Microsoft.NetApp/volumes) per Azure Virtual Network    |   1   |    No    |
|  Numero di indirizzi IP usati in una rete virtuale (incluse le reti virtuali con peering immediato) con i file NetApp di AzureNumber of used Ip in a VNet (including immediately peered VNets) with Azure NetApp Files   |    1000   |    Sì   |
|  Dimensioni minime di un singolo pool di capacità   |  4 TiB     |    No  |
|  Dimensioni massime di un pool a capacità singolaMaximum size of a single capacity pool    |  500 TiB   |   No   |
|  Dimensione minima di un singolo volume    |    100 GiB    |    No    |
|  Dimensione massima di un singolo volume     |    100 TiB    |    No    |
|  Dimensione massima di un singolo file     |    16 TiB    |    No    |    
|  Dimensione massima di una singola directory      |    320 MB    |    No    |    
|  Numero massimo di file ([maxfiles](#maxfiles)) per volume     |    100 milioni    |    Sì    |    

Per ulteriori informazioni, vedere [Domande frequenti sulla gestione](azure-netapp-files-faqs.md#capacity-management-faqs)della capacità .

## <a name="maxfiles-limits"></a>Limiti di Maxfiles<a name="maxfiles"></a> 

I volumi dei file NetApp di Azure hanno un limite denominato *maxfiles*. Il limite maxfiles è il numero di file che un volume può contenere. Il limite massimo di file massimi per un volume file NetApp di Azure viene indicizzato in base alle dimensioni (quota) del volume. Il limite massimo di file per un volume aumenta o diminuisce alla velocità di 20 milioni di file per TiB delle dimensioni del volume di cui è stato eseguito il provisioning. 

Il servizio regola dinamicamente il limite maxfiles per un volume in base alle dimensioni di cui è stato eseguito il provisioning. Ad esempio, un volume configurato inizialmente con una dimensione di 1 TiB avrebbe un limite massimo di 20 milioni. Le successive modifiche alle dimensioni del volume comporterebbero una riregolazione automatica del limite maxfiles in base alle seguenti regole: 

|    Dimensione del volume (quota)     |  Regolazione automatica del limite maxfiles    |
|----------------------------|-------------------|
|    < 1 TiB                 |    20 milioni di abitanti     |
|    >1 TiB ma < 2 TiB    |    40 milioni di abitanti     |
|    >2 TiB ma < 3 TiB    |    60 milioni     |
|    >3 TiB ma < 4 TiB    |    80 milioni di abitanti     |
|    >4 TiB                |    100 milioni    |

Per qualsiasi dimensione del volume, è possibile avviare una richiesta di [supporto](#limit_increase) per aumentare il limite massimo di file oltre 100 milioni.

## <a name="request-limit-increase"></a>Aumento limite richieste<a name="limit_increase"></a> 

È possibile creare una richiesta di supporto di Azure per aumentare i limiti regolabili dalla tabella precedente. 

Dal piano di navigazione del portale di Azure:From Azure portal navigation plane: 

1. Fare clic su **Guida e supporto**tecnico .
2. Fare clic su **Nuova richiesta di supporto**.
3. Nella scheda Nozioni di base specificare le informazioni seguenti: 
    1. Tipo di problema: selezionare **Limiti di servizio e sottoscrizione (quote)**.
    2. Sottoscrizioni: selezionare la sottoscrizione per la risorsa per cui è necessario aumentare la quota.
    3. Tipo di quota: selezionare **Archiviazione: Limiti file NetApp**di Azure .
    4. Fare clic su **Avanti: Soluzioni**.
4. Nella scheda Dettagli:
    1. Nella casella Descrizione specificare le informazioni seguenti per il tipo di risorsa corrispondente:

        |  Risorsa  |    Risorse padre      |    Nuovi limiti richiesti     |    Motivo dell'aumento delle quote       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Account |  *ID sottoscrizione*   |  *Nuovo numero di **conto** massimo richiesto*    |  *Quale scenario o caso di utilizzo ha richiesto la richiesta?*  |
        |  Pool    |  *ID sottoscrizione, URI account*  |  *Nuovo numero **massimo di pool** richiesto*   |  *Quale scenario o caso di utilizzo ha richiesto la richiesta?*  |
        |  Volume  |  *ID sottoscrizione, URI account, URI pool*   |  *Nuovo numero di **volume** massimo richiesto*     |  *Quale scenario o caso di utilizzo ha richiesto la richiesta?*  |
        |  Maxfile  |  *ID sottoscrizione, URI account, URI pool, URI volume*   |  *Nuovo numero **massimo maxfiles** richiesto*     |  *Quale scenario o caso di utilizzo ha richiesto la richiesta?*  |    

    2. Specificare il metodo di supporto appropriato e fornire le informazioni sul contratto.

    3. Fare clic su **Successivo: Revisione e creazione** per creare la richiesta. 


## <a name="next-steps"></a>Passaggi successivi  

- [Informazioni sulla gerarchia di archiviazione di Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
- [Modello di costi per Azure NetApp Files](azure-netapp-files-cost-model.md)
