---
title: Configurare i criteri di esportazione per il volume Azure NetApp Files NFS
description: Viene descritto come configurare i criteri di esportazione per controllare l'accesso a un volume NFS utilizzando Azure NetApp Files
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 4a20a223932f82c80ad5831ef3a02bad803e26e6
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533207"
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

    * Sola **lettura** e **lettura/scrittura**  
        Se si usa la crittografia Kerberos con NFSv 4.1, seguire le istruzioni in [configurare la crittografia Kerberos NFSv 4.1](configure-kerberos-encryption.md).  Per l'effetto sulle prestazioni di Kerberos, vedere l' [effetto sulle prestazioni di Kerberos in NFSv 4.1](configure-kerberos-encryption.md#kerberos_performance). 

        ![Opzioni di sicurezza Kerberos](../media/azure-netapp-files/kerberos-security-options.png) 

    * **Accesso alla radice**  
        Specificare se l' `root` account può accedere al volume.  Per impostazione predefinita, l'accesso alla radice è impostato **su on**e l' `root` account ha accesso al volume.

![Criteri di esportazione](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 



## <a name="next-steps"></a>Passaggi successivi 
* [Montare o smontare un volume per le macchine virtuali](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gestire gli snapshot](azure-netapp-files-manage-snapshots.md)
