---
title: Creare un volume per Azure NetApp Files | Microsoft Docs
description: Descrive come creare un volume per Azure NetApp Files.
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
ms.topic: get-started-article
ms.date: 03/28/2018
ms.author: b-juche
ms.openlocfilehash: 42e475f4da2102bb8b010daec6e6451ba7f13848
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39011093"
---
# <a name="create-a-volume-for-azure-netapp-files"></a>Creare un volume per Azure NetApp Files

L'utilizzo della capacità di un volume concorre al calcolo della capacità di cui è stato effettuato il provisioning del pool.  È possibile creare più volumi in un pool di capacità, ma l'utilizzo totale della capacità dei volumi non deve superare le dimensioni del pool. 

## <a name="before-you-begin"></a>Prima di iniziare 
È necessario avere già configurato un pool di capacità.  

[Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)

## <a name="steps"></a>Passaggi 
1.  Scegliere il pannello **Volumi** nel pannello Manage Capacity Pools (Gestisci pool di capacità). 
2.  Fare clic su **+ Aggiungi volume** per creare un volume.  
    Verrà visualizzata la finestra Nuovo volume.

3.  Nella finestra Nuovo volume fare clic su **Crea** e specificare le informazioni per i campi seguenti:   
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

    * **Rete virtuale**  
        Specificare la rete virtuale di Azure da cui si vuole accedere al volume. Per la rete virtuale specificata, Azure NetApp Files deve essere configurato. Il servizio Azure NetApp Files è accessibile solo da una rete virtuale presente nella stessa posizione del volume.   

    ![Nuovo volume](../media/azure-netapp-files/azure-netapp-files-new-volume.png)

4.  Fare clic su **OK**. 
 
Un volume eredita sottoscrizione, gruppo di risorse e attributi di posizione dal relativo pool di capacità. Per monitorare lo stato di distribuzione del volume, è possibile usare la scheda Notifiche.

## <a name="next-steps"></a>Passaggi successivi  
[Configurare i criteri di esportazione per un volume (facoltativo)](azure-netapp-files-configure-export-policy.md)

