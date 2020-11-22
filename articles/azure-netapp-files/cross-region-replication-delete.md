---
title: Eliminare i volumi o le repliche del volume per Azure NetApp Files la replica tra aree | Microsoft Docs
description: Viene descritto come eliminare una connessione di replica non più necessaria tra i volumi di origine e di destinazione.
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
ms.topic: how-to
ms.date: 11/18/2020
ms.author: b-juche
ms.openlocfilehash: 5ce7a591acd8203775808457219b0ec392cd696e
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2020
ms.locfileid: "95249895"
---
# <a name="delete-volume-replications-or-volumes"></a>Elimina volumi o repliche di volumi

Questo articolo descrive come eliminare le repliche del volume. Viene inoltre descritto come eliminare il volume di origine o di destinazione.

## <a name="delete-volume-replications"></a>Elimina repliche di volumi

È possibile terminare la connessione di replica tra i volumi di origine e di destinazione eliminando la replica del volume. È necessario eliminare la replica dal volume di destinazione. L'operazione Delete rimuove solo l'autorizzazione per la replica. non rimuove il volume di origine o di destinazione. 

1. Verificare che il peering di replica sia stato danneggiato prima di eliminare la replica del volume. Per interrompere il peering di replica: 

    1. Selezionare il volume di *destinazione* . Fare clic su **replica** in servizio di archiviazione.  

    2.  Prima di continuare, controllare i campi seguenti:  
        * Verificare che lo stato del mirror indichi ***Mirrored** _.   
            Non tentare di interrompere il peering della replica se lo stato del mirror indica _Uninitialized *.
        * Verificare che lo stato della relazione sia visualizzato ***inattivo** _.   
            Non tentare di interrompere il peering di replica se lo stato della relazione Mostra _Transferring *.   

        Vedere [visualizzare lo stato di integrità della relazione di replica](cross-region-replication-display-health-status.md). 

    3.  Fare clic su **Interrompi peering**.  

    4.  Digitare **Yes** quando richiesto e fare clic su **Interrompi**. 

        ![Interrompere il peering della replica](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)


1. Per eliminare la replica del volume, selezionare **replica** dal volume di origine o di destinazione.  

2. Fare clic su **Elimina**.    

3. Confermare l'eliminazione digitando **Sì** e facendo clic su **Elimina**.   

    ![Elimina replica](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="delete-source-or-destination-volumes"></a>Elimina volumi di origine o di destinazione

Se si desidera eliminare il volume di origine o di destinazione, è necessario eseguire i passaggi seguenti nell'ordine descritto. In caso contrario, `Volume with replication cannot be deleted` si verifica l'errore.  

1. Dal volume di destinazione, [eliminare la replica del volume](#delete-volume-replications).   

2. Eliminare il volume di destinazione o di origine in base alle esigenze facendo clic con il pulsante destro del mouse sul nome del volume e scegliere **Elimina**.   

    ![Screenshot che mostra il menu di scelta rapida di un volume.](../media/azure-netapp-files/cross-region-replication-delete-volume.png)

## <a name="next-steps"></a>Passaggi successivi  

* [Replica tra più aree](cross-region-replication-introduction.md)
* [Requisiti e considerazioni per l'uso della replica tra aree](cross-region-replication-requirements-considerations.md)
* [Visualizzare lo stato integrità della relazione di replica](cross-region-replication-display-health-status.md)
* [Risolvere i problemi relativi alla replica tra aree](troubleshoot-cross-region-replication.md)

