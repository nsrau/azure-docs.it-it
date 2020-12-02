---
title: Rimuovere le risorse da una raccolta di spostamento in Azure Resource Mover
description: Informazioni su come rimuovere risorse da una raccolta di spostamento in Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 11/30/2020
ms.author: raynew
ms.openlocfilehash: 63548e2bf470c012e0dd8a5f879a51eeb631f453
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2020
ms.locfileid: "96459280"
---
# <a name="manage-move-collections-and-resource-groups"></a>Gestire le raccolte di spostamento e i gruppi di risorse

Questo articolo descrive come rimuovere risorse da una raccolta di spostamento o rimuovere una raccolta/gruppo di risorse di spostamento in [Azure Resource Mover](overview.md). Le raccolte di spostamento vengono usate quando si spostano le risorse di Azure tra aree di Azure.

## <a name="remove-a-resource-portal"></a>Rimuovere una risorsa (portale)

È possibile rimuovere le risorse in una raccolta di spostamento, nel portale di Resource Mover, come indicato di seguito:

1. In **aree diverse** selezionare tutte le risorse che si desidera rimuovere dalla raccolta e selezionare **Rimuovi**. 

    ![Pulsante da rimuovere](./media/remove-move-resources/portal-select-resources.png)

2. In **Rimuovi risorse** fare clic su **Rimuovi**.

    ![Pulsante per selezionare la rimozione delle risorse da una raccolta di spostamento](./media/remove-move-resources/remove-portal.png)

## <a name="remove-a-move-collectionresource-group-portal"></a>Rimuovere una raccolta o un gruppo di risorse di spostamento (portale)

È possibile rimuovere una raccolta o un gruppo di risorse di spostamento nel portale.

1. Per rimuovere le risorse dalla raccolta, seguire le istruzioni riportate nella procedura precedente. Se si sta rimuovendo un gruppo di risorse, assicurarsi che non contenga risorse.
2. Eliminare la raccolta di spostamento o il gruppo di risorse.  

## <a name="remove-a-resource-powershell"></a>Rimuovere una risorsa (PowerShell)

Rimuovere una risorsa (in questo esempio i computer PSDemoVM) da una raccolta usando PowerShell, come indicato di seguito:

```azurepowershell-interactive
# Remove a resource using the resource ID
Remove-AzResourceMoverMoveResource -SubscriptionId  <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus  -MoveCollectionName MoveCollection-centralus-westcentralus -Name PSDemoVM
```
**Output previsto**

![Testo di output dopo la rimozione di una risorsa da una raccolta di spostamento](./media/remove-move-resources/remove-resource.png)

## <a name="remove-a-collection-powershell"></a>Rimuovere una raccolta (PowerShell)

Rimuovere un'intera raccolta di spostamento usando PowerShell, come indicato di seguito:

1. Seguire le istruzioni riportate sopra per rimuovere le risorse nella raccolta usando PowerShell.
2. Eseguire:

    ```azurepowershell-interactive
    # Remove a resource using the resource ID
    Remove-AzResourceMoverMoveCollection -SubscriptionId <subscription-id> -ResourceGroupName RegionMoveRG-centralus-westcentralus -MoveCollectionName MoveCollection-centralus-westcentralus
    ```
    **Output previsto**
    
    ![Testo di output dopo la rimozione di una raccolta di spostamento](./media/remove-move-resources/remove-collection.png)

## <a name="vm-resource-state-after-removing"></a>Stato delle risorse della macchina virtuale dopo la rimozione

Cosa accade quando si rimuove una risorsa della macchina virtuale da una raccolta di spostamento dipende dallo stato della risorsa, come riepilogato nella tabella.

###  <a name="remove-vm-state"></a>Rimuovere lo stato della macchina virtuale
**Stato risorsa** | **VM** | **Rete**
--- | --- | --- 
**Aggiunta per spostare la raccolta** | Elimina dalla raccolta di spostamento. | Elimina dalla raccolta di spostamento. 
**Dipendenze risolte/preparate in sospeso** | Elimina dalla raccolta di spostamento  | Elimina dalla raccolta di spostamento. 
**Preparazione in corso**<br/> (o qualsiasi altro stato in corso) | L'operazione di eliminazione non riesce con errore.  | L'operazione di eliminazione non riesce con errore.
**Preparazione non riuscita** | Elimina dalla raccolta di spostamento.<br/>Eliminare qualsiasi elemento creato nell'area di destinazione, inclusi i dischi di replica. <br/><br/> Le risorse dell'infrastruttura create durante lo spostamento devono essere eliminate manualmente. | Elimina dalla raccolta di spostamento.  
**Inizio spostamento in sospeso** | Elimina dalla raccolta di spostamento.<br/><br/> Eliminare tutti gli elementi creati nell'area di destinazione, inclusi i dischi della macchina virtuale, di replica e così via.  <br/><br/> Le risorse dell'infrastruttura create durante lo spostamento devono essere eliminate manualmente. | Elimina dalla raccolta di spostamento.
**Spostamento avvio non riuscito** | Elimina dalla raccolta di spostamento.<br/><br/> Eliminare tutti gli elementi creati nell'area di destinazione, inclusi i dischi della macchina virtuale, di replica e così via.  <br/><br/> Le risorse dell'infrastruttura create durante lo spostamento devono essere eliminate manualmente. | Elimina dalla raccolta di spostamento.
**Commit in sospeso** | Si consiglia di annullare lo spostamento in modo che le risorse di destinazione vengano eliminate per prime.<br/><br/> La risorsa torna allo stato di **inizio spostamento in sospeso** ed è possibile continuare da questa posizione. | Si consiglia di annullare lo spostamento in modo che le risorse di destinazione vengano eliminate per prime.<br/><br/> La risorsa torna allo stato di **inizio spostamento in sospeso** ed è possibile continuare da questa posizione. 
**Commit non riuscito** | Si consiglia di eliminare il in modo che le risorse di destinazione vengano eliminate per prime.<br/><br/> La risorsa torna allo stato di **inizio spostamento in sospeso** ed è possibile continuare da questa posizione. | Si consiglia di annullare lo spostamento in modo che le risorse di destinazione vengano eliminate per prime.<br/><br/> La risorsa torna allo stato di **inizio spostamento in sospeso** ed è possibile continuare da questa posizione.
**Eliminazione completata** | La risorsa torna allo stato di **inizio spostamento in sospeso** .<br/><br/> Viene eliminato dalla raccolta di spostamento, insieme a qualsiasi elemento creato nella macchina virtuale di destinazione, nei dischi di replica, nel Vault e così via.  <br/><br/> Le risorse dell'infrastruttura create durante lo spostamento devono essere eliminate manualmente. <br/><br/> Le risorse dell'infrastruttura create durante lo spostamento devono essere eliminate manualmente. |  La risorsa torna allo stato di **inizio spostamento in sospeso** .<br/><br/> Viene eliminato dalla raccolta Move.
**Eliminazione non riuscita** | Si consiglia di eliminare gli spostamenti in modo che le risorse di destinazione vengano eliminate per prime.<br/><br/> Successivamente, la risorsa torna allo stato di **inizio spostamento in sospeso** ed è possibile continuare da questa posizione. | Si consiglia di eliminare gli spostamenti in modo che le risorse di destinazione vengano eliminate per prime.<br/><br/> Successivamente, la risorsa torna allo stato di **inizio spostamento in sospeso** ed è possibile continuare da questa posizione.
**Elimina origine in sospeso** | Eliminato dalla raccolta di spostamento.<br/><br/> Non elimina tutti gli oggetti creati nell'area di destinazione.  | Eliminato dalla raccolta di spostamento.<br/><br/> Non elimina tutti gli oggetti creati nell'area di destinazione.
**Eliminazione origine non riuscita** | Eliminato dalla raccolta di spostamento.<br/><br/> Non elimina tutti gli oggetti creati nell'area di destinazione. | Eliminato dalla raccolta di spostamento.<br/><br/> Non elimina tutti gli oggetti creati nell'area di destinazione.

## <a name="sql-resource-state-after-removing"></a>Stato della risorsa SQL dopo la rimozione

Cosa accade quando si rimuove una risorsa SQL di Azure da una raccolta di spostamento dipende dallo stato della risorsa, come riepilogato nella tabella.

**Stato risorsa** | **SQL** 
--- | --- 
**Aggiunta per spostare la raccolta** | Elimina dalla raccolta di spostamento. 
**Dipendenze risolte/preparate in sospeso** | Elimina dalla raccolta di spostamento 
**Preparazione in corso**<br/> (o qualsiasi altro stato in corso)  | L'operazione di eliminazione non riesce con errore. 
**Preparazione non riuscita** | Elimina dalla raccolta di spostamento<br/><br/>Eliminare qualsiasi elemento creato nell'area di destinazione. 
**Inizio spostamento in sospeso** |  Elimina dalla raccolta di spostamento<br/><br/>Eliminare qualsiasi elemento creato nell'area di destinazione. Il database SQL esiste a questo punto e verrà eliminato. 
**Spostamento avvio non riuscito** | Elimina dalla raccolta di spostamento<br/><br/>Eliminare qualsiasi elemento creato nell'area di destinazione. Il database SQL esiste a questo punto e deve essere eliminato. 
**Commit in sospeso** | Si consiglia di annullare lo spostamento in modo che le risorse di destinazione vengano eliminate per prime.<br/><br/> La risorsa torna allo stato di **inizio spostamento in sospeso** ed è possibile continuare da questa posizione.
**Commit non riuscito** | Si consiglia di annullare lo spostamento in modo che le risorse di destinazione vengano eliminate per prime.<br/><br/> La risorsa torna allo stato di **inizio spostamento in sospeso** ed è possibile continuare da questa posizione. 
**Eliminazione completata** |  La risorsa torna allo stato di **inizio spostamento in sospeso** .<br/><br/> Viene eliminato dalla raccolta di spostamento, insieme a qualsiasi elemento creato nella destinazione, inclusi i database SQL. 
**Eliminazione non riuscita** | Si consiglia di eliminare gli spostamenti in modo che le risorse di destinazione vengano eliminate per prime.<br/><br/> Successivamente, la risorsa torna allo stato di **inizio spostamento in sospeso** ed è possibile continuare da questa posizione. 
**Elimina origine in sospeso** | Eliminato dalla raccolta di spostamento.<br/><br/> Non elimina tutti gli oggetti creati nell'area di destinazione. 
**Eliminazione origine non riuscita** | Eliminato dalla raccolta di spostamento.<br/><br/> Non elimina tutti gli oggetti creati nell'area di destinazione. 

## <a name="next-steps"></a>Passaggi successivi

Provare a [trasferire una macchina virtuale](tutorial-move-region-virtual-machines.md) in un'altra area con il motore di risorse.
