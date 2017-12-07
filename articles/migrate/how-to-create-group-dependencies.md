---
title: Ridefinire un gruppo di valutazione con il mapping delle dipendenze del gruppo in Azure Migrate | Microsoft Docs
description: Descrive come ridefinire una valutazione usando il mapping delle dipendenze del gruppo nel servizio Azure Migrate.
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 0527e34e-a078-405e-aeb9-c91a5808112a
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: b4d6861f147fbb6e65a9d529f17f78b54075eb90
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2017
---
# <a name="refine-a-group-using-group-dependency-mapping"></a>Ridefinire un gruppo usando il mapping delle dipendenze del gruppo

Questo articolo descrive come configurare il mapping delle dipendenze del gruppo per la valutazione di [Azure Migrate](migrate-overview.md). In genere si usa questo metodo quando si vogliono ridefinire le impostazioni di un gruppo esistente, controllando in modo incrociato le dipendenze del gruppo, prima di eseguire una valutazione. I gruppi per i quali si vuole eseguire il mapping delle dipendenze non devono includere più di 10 computer.  

## <a name="modify-a-group"></a>Modificare un gruppo

1. Nel progetto di Azure Migrate, in **Gestisci**, fare clic su  **Gruppi** e selezionare il gruppo.
2. Nella pagina relativa al gruppo fare clic su  **Visualizza dipendenze** per aprire la mappa delle dipendenze del gruppo. 

     ![Visualizzazione del gruppo](./media/how-to-create-group-dependencies/create-group.png)

3. Per visualizzare le dipendenze a un livello più dettagliato, fare clic sull'intervallo di tempo per modificarlo. Per impostazione predefinita, l'intervallo è un'ora. È possibile modificare l'intervallo di tempo oppure specificare le date di inizio e fine e la durata.
4. Usare CTRL+clic per selezionare i computer nella mappa. Aggiungere o rimuovere i computer nella mappa.
    - È possibile aggiungere solo i computer che sono stati individuati.
    - L'aggiunta e la rimozione dei computer di un gruppo invalidano le precedenti valutazioni eseguite per il gruppo.
    - Quando si modifica il gruppo, è possibile creare facoltativamente una nuova valutazione.
5. Fare clic su **OK** per salvare il gruppo.

    ![Aggiunta e rimozione](./media/how-to-create-group-dependencies/add-remove.png)

Se si vogliono verificare le dipendenze di un computer specifico che viene visualizzato nella mappa delle dipendenze del gruppo, [configurare il mapping delle dipendenze del computer](how-to-create-group-machine-dependencies.md).


## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
