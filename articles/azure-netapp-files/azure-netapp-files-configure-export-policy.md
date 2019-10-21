---
title: Configurare i criteri di esportazione per un volume NFS usando Azure NetApp Files | Microsoft Docs
description: Viene descritto come configurare i criteri di esportazione per controllare l'accesso a un volume NFS utilizzando Azure NetApp Files
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
ms.date: 10/18/2019
ms.author: b-juche
ms.openlocfilehash: d323bd0b9684cfe4930d8c779a6728fcfd3836fb
ms.sourcegitcommit: 9a4296c56beca63430fcc8f92e453b2ab068cc62
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2019
ms.locfileid: "72674919"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Configurare i criteri di esportazione per un volume NFS

È facoltativamente possibile configurare i criteri di esportazione per controllare l'accesso a un volume di Azure NetApp Files. Azure NetApp Files i criteri di esportazione supportano solo i volumi NFS.  Sono supportati sia NFSv3 che NFSv4. 

## <a name="steps"></a>Procedure 

1.  Fare clic su **Esporta criterio** nel riquadro di spostamento Azure NetApp files. 

2.  Specificare le informazioni nei campi seguenti creare una regola di criteri di esportazione:   
    *  **Index**  (Indice)  
        Specificare il numero di indice per la regola.  
        I criteri di esportazione possono essere costituiti da un massimo di cinque regole. Le regole vengono valutate in base al relativo ordine nell'elenco dei numeri di indice. Le regole con numeri di indice inferiori vengono valutate per prime. Ad esempio, la regola con numero di indice 1 viene valutata prima della regola con numero di indice 2. 

    * **Allowed Clients**  (Client consentiti)  
        Specificare il valore in uno dei formati seguenti:  
        * Indirizzo IPv4, ad esempio, `10.1.12.24` 
        * Indirizzo IPv4 con subnet mask espressa sotto forma di numero di bit, ad esempio, `10.1.12.10/4`

    * **Accedere**  
        Selezionare uno dei tipi di accesso seguenti:  
        * Nessun accesso 
        * Lettura e scrittura
        * Sola lettura

    ![Criteri di esportazione](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Passaggi successivi 
* [Gestire i volumi](azure-netapp-files-manage-volumes.md)
* [Montare o smontare un volume per le macchine virtuali](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gestire gli snapshot](azure-netapp-files-manage-snapshots.md)
