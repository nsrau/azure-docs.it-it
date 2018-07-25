---
title: Configurare i criteri di esportazione per un volume di Azure NetApp Files | Microsoft Docs
description: Descrive come configurare i criteri di esportazione per controllare l'accesso a un volume di Azure NetApp Files
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
ms.openlocfilehash: 08de7e2ca8cd993a0931f5b16cb9d6c9a04e53dc
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/13/2018
ms.locfileid: "39010957"
---
# <a name="configure-export-policy-for-a-volume-optional"></a>Configurare i criteri di esportazione per un volume (facoltativo)

È facoltativamente possibile configurare i criteri di esportazione per controllare l'accesso a un volume di Azure NetApp Files. 

## <a name="steps"></a>Passaggi 

1.  Fare clic sul pannello **Create Export Policy** (Crea criteri di esportazione) dal pannello Manage Volume (Gestisci volume). 

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

    * **Protocols**  (Protocolli)  
        Specificare il protocollo da usare per i criteri di esportazione.   
        Attualmente Azure NetApp Files supporta solo NFSv3.

    ![Criteri di esportazione](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 


## <a name="next-steps"></a>Passaggi successivi 
* [Gestire i volumi](azure-netapp-files-manage-volumes.md)
* [Montare o smontare un volume per le macchine virtuali](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Gestire gli snapshot](azure-netapp-files-manage-snapshots.md)
