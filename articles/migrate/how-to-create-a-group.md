---
title: Raggruppare i computer per la valutazione con Azure Migrate | Microsoft Docs
description: Descrive come raggruppare i computer prima di eseguire una valutazione con il servizio Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/28/2018
ms.author: raynew
ms.openlocfilehash: c11d2f22fa08417107b0eecdd902b4521410b358
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60544934"
---
# <a name="group-machines-for-assessment"></a>Raggruppare i computer per la valutazione

Questo articolo descrive come creare un gruppo di computer per la valutazione con [Azure Migrate](migrate-overview.md). Azure Migrate valuta i computer del gruppo per verificare se sono adatti per la migrazione ad Azure e fornisce stime riguardo a dimensioni e costi per l'esecuzione del computer in Azure. Se si sa quali macchine devono essere migrate insieme, è possibile creare manualmente il gruppo in Azure Migrate usando il metodo seguente. Se invece non si sa con certezza quali macchine devono essere raggruppate, è possibile usare la funzionalità di visualizzazione delle dipendenze in Azure Migrate per creare i gruppi. [Altre informazioni.](how-to-create-group-machine-dependencies.md)

> [!NOTE]
> La funzionalità di visualizzazione delle dipendenze non è disponibile in Azure per enti pubblici.

## <a name="create-a-group"></a>Creare un gruppo

1. Nella **Panoramica** del progetto di Azure Migrate, in Gestisci, fare clic su  **Gruppi** > **+Gruppo** e specificare un nome di gruppo.
2. Aggiungere uno o più computer al gruppo e fare clic su  **Crea**.
3. Facoltativamente è possibile scegliere di eseguire una nuova valutazione per il gruppo.

    ![Creare un gruppo](./media/how-to-create-a-group/create-group.png)

Dopo aver creato il gruppo, è possibile modificarlo selezionando la voce corrispondente nella pagina **Gruppi** e aggiungendo o rimuovendo i computer desiderati.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come usare il [mapping delle dipendenze del computer](how-to-create-group-machine-dependencies.md) per creare gruppi più attendibili.
- [Altre informazioni](concepts-assessment-calculation.md) sul modo in cui vengono calcolate le valutazioni.
