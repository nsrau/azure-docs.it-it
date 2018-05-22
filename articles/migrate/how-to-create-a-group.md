---
title: Raggruppare i computer per la valutazione con Azure Migrate | Microsoft Docs
description: Descrive come raggruppare i computer prima di eseguire una valutazione con il servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: raynew
ms.openlocfilehash: 5c5b5dc97c0faf43c9543422406c4fa1c30ed679
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2018
---
# <a name="group-machines-for-assessment"></a>Raggruppare i computer per la valutazione

Questo articolo descrive come creare un gruppo di computer per la valutazione con [Azure Migrate](migrate-overview.md). Azure Migrate valuta i computer del gruppo per verificare se sono adatti per la migrazione ad Azure e fornisce stime riguardo a dimensioni e costi per l'esecuzione del computer in Azure.


## <a name="create-a-group"></a>Creare un gruppo

1. Nella **Panoramica** del progetto di Azure Migrate, in Gestisci, fare clic su  **Gruppi** > **+Gruppo** e specificare un nome di gruppo.
2. Aggiungere uno o più computer al gruppo e fare clic su  **Crea**. 
3. Facoltativamente è possibile scegliere di eseguire una nuova valutazione per il gruppo. 

    ![Creare un gruppo](./media/how-to-create-a-group/create-group.png)

Dopo aver creato il gruppo, è possibile modificarlo selezionando la voce corrispondente nella pagina **Gruppi** e aggiungendo o rimuovendo computer.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare il [mapping delle dipendenze del computer](how-to-create-group-machine-dependencies.md) per creare gruppi più attendibili.
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
