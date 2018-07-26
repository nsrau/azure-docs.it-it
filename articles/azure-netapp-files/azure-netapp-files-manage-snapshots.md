---
title: Gestire gli snapshot tramite Azure NetApp Files | Microsoft Docs
description: Questo articolo descrive come creare uno snapshot su richiesta per un volume o come eseguire il ripristino da uno snapshot a un nuovo volume tramite Azure NetApp Files.
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
ms.topic: how-to-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 48cb88b9815ba723d93c18caf63f33b50eea850c
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011979"
---
# <a name="manage-snapshots-by-using-azure-netapp-files"></a>Gestire gli snapshot tramite Azure NetApp Files
È possibile usare Azure NetApp Files per creare uno snapshot su richiesta per un volume o eseguire il ripristino da uno snapshot a un nuovo volume.

## <a name="create-an-on-demand-snapshot-for-a-volume"></a>Creare uno snapshot su richiesta per un volume
Gli snapshot possono essere creati solo su richiesta.  Al momento non sono supportati i criteri degli snapshot.  
1.  Nel pannello Manage Volume (Gestisci volume), fare clic su **Snapshot**, quindi fare clic su **+ Aggiungi snapshot** per creare uno snapshot su richiesta per un volume.

2.  Nella finestra Nuovo snapshot, specificare un nome per il nuovo snapshot che si sta creando.   

3. Fare clic su **OK**. 


## <a name="restore-a-snapshot-to-a-new-volume"></a>Ripristinare uno snapshot in un nuovo volume
Attualmente, è possibile ripristinare uno snapshot solo in un nuovo volume. 
1. Andare al pannello **Manage Snapshots** (Gestisci snapshot) nel pannello Volume per visualizzare l'elenco degli snapshot. 
2. Selezionare lo snapshot da ripristinare.  
3. Fare clic con il destro del mouse sul nome dello snapshot e selezionare **Ripristina in nuovo volume** dall'opzione di menu.  

    ![Ripristinare snapshot in un nuovo volume](../media/azure-netapp-files/azure-netapp-files-snapshot-restore-to-new-volume.png)

4. Nella finestra Nuovo volume, fornire le informazioni relative al nuovo volume:  
    * **Nome**   
        Specificare il nome per il volume che si sta creando.  
        
        Il nome deve essere univoco all'interno di un gruppo di risorse. Deve essere composto da almeno 3 caratteri.  È possibile usare qualsiasi carattere alfanumerico.

    * **Percorso file**     
        Specificare il percorso del file che verrà usato per creare il percorso di esportazione per il nuovo volume. Il percorso di esportazione viene usato per montare il volume e accedervi.   
        
        Una destinazione di montaggio è l'endpoint dell'indirizzo IP del servizio NFS. Viene generata automaticamente.   
        
        Il nome di percorso di file può contenere solo lettere, numeri e trattini ("-"). Il nome deve avere una lunghezza compresa tra 16 e 40 caratteri. 

    * **Quota**  
        Specificare la quantità di spazio di archiviazione logico allocato al volume.  

        Il campo **Quota disponibile** mostra la quantità di spazio inutilizzato nel pool di capacità scelto che è possibile usare per la creazione di un nuovo volume. Le dimensioni del nuovo volume non devono superare la quota disponibile.

    *   **Rete virtuale**  
        Specificare la rete virtuale di Azure da cui si vuole accedere al volume. 
        
        Per la rete virtuale specificata, Azure NetApp Files deve essere configurato. Il servizio Azure NetApp Files è accessibile solo da una rete virtuale presente nella stessa posizione del volume.  

    ![Nuovo volume ripristinato](../media/azure-netapp-files/azure-netapp-files-snapshot-new-volume.png) 
    
5. Fare clic su **OK**.   
    Il nuovo volume in cui viene ripristinato lo snapshot viene visualizzato nel pannello Volumi.

