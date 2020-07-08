---
title: Configurare i criteri di esportazione per il volume Azure NetApp Files NFS
description: Viene descritto come configurare i criteri di esportazione per controllare l'accesso a un volume NFS utilizzando Azure NetApp Files
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 10/18/2019
ms.openlocfilehash: e59648ee76b6715029c690329cbf8f4f1eee7243
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85483653"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Configurare i criteri di esportazione per un volume NFS

È facoltativamente possibile configurare i criteri di esportazione per controllare l'accesso a un volume di Azure NetApp Files. Azure NetApp Files i criteri di esportazione supportano solo i volumi NFS.  Sono supportati sia NFSv3 che NFSv4. 

## <a name="steps"></a>Passaggi 

1.  Fare clic su **Esporta criterio** nel riquadro di spostamento Azure NetApp files. 

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

    ![Criteri di esportazione](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Passaggi successivi 
* [Gestire i volumi](azure-netapp-files-manage-volumes.md)
* [Montare o smontare un volume per le macchine virtuali](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gestire gli snapshot](azure-netapp-files-manage-snapshots.md)
