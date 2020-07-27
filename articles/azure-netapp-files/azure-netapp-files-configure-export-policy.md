---
title: Configurare i criteri di esportazione per il volume Azure NetApp Files NFS
description: Viene descritto come configurare i criteri di esportazione per controllare l'accesso a un volume NFS utilizzando Azure NetApp Files
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/24/2020
ms.openlocfilehash: 6d990b94210383ba4b30569693f4471f43306ed2
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87169820"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Configurare i criteri di esportazione per un volume NFS

È possibile configurare i criteri di esportazione per controllare l'accesso a un volume Azure NetApp Files. Azure NetApp Files i criteri di esportazione supportano i volumi che usano il protocollo NFS (NFSv3 e NFSv 4.1) e il protocollo Dual (NFSv3 e SMB). 

È possibile creare fino a cinque regole dei criteri di esportazione.

## <a name="steps"></a>Passaggi 

1.  Nella pagina volumi selezionare il volume per il quale si desidera configurare i criteri di esportazione, quindi fare clic su **Esporta criterio**. 

    È anche possibile configurare i criteri di esportazione durante la creazione del volume.

2.  Specificare le informazioni nei campi seguenti creare una regola di criteri di esportazione:   
    *  **Indice**   
        Specificare il numero di indice per la regola.  
        I criteri di esportazione possono essere costituiti da un massimo di cinque regole. Le regole vengono valutate in base al relativo ordine nell'elenco dei numeri di indice. Le regole con numeri di indice inferiori vengono valutate per prime. Ad esempio, la regola con numero di indice 1 viene valutata prima della regola con numero di indice 2. 

    * **Client consentiti**   
        Specificare il valore in uno dei formati seguenti:  
        * Indirizzo IPv4, ad esempio, `10.1.12.24` 
        * Indirizzo IPv4 con subnet mask espressa sotto forma di numero di bit, ad esempio, `10.1.12.10/4`

    * **Accesso**  
        Selezionare uno dei tipi di accesso seguenti:  
        * Nessun accesso 
        * Lettura e scrittura
        * Sola lettura

    * **Accesso alla radice**  
        Specificare se l' `root` account può accedere al volume.  Per impostazione predefinita, l'accesso alla radice è impostato **su on**e l' `root` account ha accesso al volume.

        ![Criteri di esportazione](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 



## <a name="next-steps"></a>Passaggi successivi 
* [Gestire i volumi](azure-netapp-files-manage-volumes.md)
* [Montare o smontare un volume per le macchine virtuali](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gestire gli snapshot](azure-netapp-files-manage-snapshots.md)
