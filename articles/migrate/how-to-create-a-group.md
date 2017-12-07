---
title: Raggruppare i computer per la valutazione con Azure Migrate | Microsoft Docs
description: Descrive come raggruppare i computer prima di eseguire una valutazione con il servizio Azure Migrate.
services: migrate
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 5c279804-aa30-4946-a222-6b77c7aac508
ms.service: migrate
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/21/2017
ms.author: raynew
ms.openlocfilehash: 966e0aebf56ab049e898d1787bfdfbb2ef23635e
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2017
---
# <a name="group-machines-for-assessment"></a>Raggruppare i computer per la valutazione

Questo articolo descrive come creare un gruppo di computer per la valutazione con [Azure Migrate](migrate-overview.md). Azure Migrate valuta i computer del gruppo per verificare se sono adatti per la migrazione ad Azure e fornisce stime riguardo a dimensioni e costi per l'esecuzione del computer in Azure.


## <a name="create-a-group"></a>Creare un gruppo

1. Nel **Dashboard** del progetto di Azure Migrate fare clic su  **Gruppi** > **+Gruppo** e specificare un nome di gruppo.
2. Aggiungere uno o più computer al gruppo e fare clic su  **Crea**. 
3. Facoltativamente è possibile scegliere di eseguire una nuova valutazione per il gruppo. 

    ![Creare un gruppo](./media/how-to-create-a-group/create-group.png)

Dopo aver creato il gruppo, è possibile modificarlo selezionando la voce corrispondente nella pagina **Gruppi** e aggiungendo o rimuovendo computer.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare il [mapping delle dipendenze del computer](how-to-create-group-machine-dependencies.md) per creare gruppi più dettagliati.
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
