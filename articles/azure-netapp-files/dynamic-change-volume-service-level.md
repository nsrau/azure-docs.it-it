---
title: Modificare dinamicamente il livello di servizio di un volume per Azure NetApp Files | Microsoft Docs
description: Viene descritto come modificare dinamicamente il livello di servizio di un volume.
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
ms.date: 07/24/2020
ms.author: b-juche
ms.openlocfilehash: bd28f949d35d38c9e64af7ff4196aa1754fbc37a
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2020
ms.locfileid: "87172663"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Modificare dinamicamente il livello di servizio di un volume

È possibile modificare il livello di servizio di un volume esistente spostando il volume in un altro pool di capacità che utilizza il [livello di servizio](azure-netapp-files-service-levels.md) desiderato per il volume. Questa modifica sul posto a livello di servizio per il volume non richiede la migrazione dei dati. Non influisca inoltre sull'accesso al volume.  

> [!IMPORTANT] 
> Per utilizzare questa funzionalità è necessario aggiungere l'elenco elementi Consenti Inviare un messaggio di posta elettronica a anffeedback@microsoft.com con l'ID sottoscrizione per richiedere questa funzionalità.

Questa funzionalità consente di soddisfare le esigenze del carico di lavoro su richiesta.  È possibile modificare un volume esistente per usare un livello di servizio superiore per ottenere prestazioni migliori o per usare un livello di servizio inferiore per l'ottimizzazione dei costi. Ad esempio, se il volume si trova attualmente in un pool di capacità che usa il livello di servizio *standard* e si vuole che il volume usi il livello di servizio *Premium* , è possibile spostare il volume in modo dinamico in un pool di capacità che usa il livello di servizio *Premium* .  

Il pool di capacità in cui si desidera spostare il volume deve essere già esistente. Il pool di capacità può contenere altri volumi.  Se si vuole spostare il volume in un nuovo pool di capacità, è necessario [creare il pool di capacità](azure-netapp-files-set-up-capacity-pool.md) prima di spostare il volume.  

## <a name="considerations"></a>Considerazioni

* Dopo che il volume è stato spostato in un altro pool di capacità, non sarà più possibile accedere ai log attività e alle metriche del volume precedenti. Il volume inizierà con i nuovi log attività e le metriche nel nuovo pool di capacità.

* Se si sposta un volume in un pool di capacità di un livello di servizio superiore (ad esempio, passando dal livello *standard* al livello *Premium* o *ultra* ), è necessario attendere almeno sette giorni prima che sia possibile spostare di nuovo il volume in un pool di capacità di un livello di servizio inferiore (ad esempio, passando da *ultra* a *Premium* o *standard*).  
Questo periodo di attesa non si applica se si sposta il volume in un pool di capacità con lo stesso livello di servizio o un livello di servizio inferiore.

## <a name="steps"></a>Passaggi

1.  Nella pagina volumi, fare clic con il pulsante destro del mouse sul volume di cui si desidera modificare il livello di servizio. Selezionare **Cambia pool**.

    ![Pulsante destro del mouse su volume](../media/azure-netapp-files/right-click-volume.png)

2. Nella finestra Cambia pool selezionare il pool di capacità in cui si desidera spostare il volume. 

    ![Cambia pool](../media/azure-netapp-files/change-pool.png)

3.  Fare clic su **OK**.


## <a name="next-steps"></a>Passaggi successivi  

* [Livelli di servizio per Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Configurare un pool di capacità](azure-netapp-files-set-up-capacity-pool.md)
