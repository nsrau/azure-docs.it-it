---
title: Creare e gestire i progetto di Azure Migrate
description: Trovare, creare, gestire ed eliminare progetti in Azure Migrate.Find, create, manage, and delete projects in Azure Migrate.
ms.topic: how-to
ms.date: 04/19/2020
ms.openlocfilehash: f5079ed979d98f2c6f0c654c860c6f176f366497
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676387"
---
# <a name="create-and-manage-azure-migrate-projects"></a>Creare e gestire i progetto di Azure Migrate

Questo articolo descrive come creare, gestire ed eliminare progetti di [Azure Migrate.This](migrate-services-overview.md) article describes how to create, manage, and delete Azure Migrate projects.


## <a name="create-a-project-for-the-first-time"></a>Creare un progetto per la prima volta

La prima volta che si configura Azure Migrate, si crea un progetto e si aggiunge uno strumento di valutazione o migrazione. [Seguire queste istruzioni](how-to-add-tool-first-time.md) per configurare per la prima volta.

## <a name="create-additional-projects"></a>Creare progetti aggiuntivi

Se si dispone già di un progetto di Azure Migrate e si vuole creare un progetto aggiuntivo, eseguire le operazioni seguenti:If you already have an Azure Migrate project and you want to create an additional project, do the following:  

1. Nel [portale pubblico](https://portal.azure.com) di Azure o [per enti pubblici](https://portal.azure.us)di Azure cercare **Azure Migrate**.
2. Nel dashboard di Azure Migrate > **server**selezionare **modifica** nell'angolo superiore destro.

   ![Modificare il progetto Azure MigrateChange Azure Migrate project](./media/create-manage-projects/switch-project.png)

3. Per creare un nuovo progetto, selezionare **fare clic qui**.

   ![Creare un secondo progetto di Azure MigrateCreate a second Azure Migrate project](./media/create-manage-projects/create-new-project.png)


## <a name="find-a-project"></a>Trovare un progetto

Trovare un progetto come segue:

1. Nel [portale](https://portal.azure.com)di Azure cercare **Azure Migrate**.
2. Nel dashboard di Azure Migrate > **server**selezionare **modifica** nell'angolo superiore destro.

    ![Passare a un progetto di Azure Migrate esistente](./media/create-manage-projects/switch-project.png)

3. Selezionare la sottoscrizione appropriata e il progetto di Azure Migrate.Select the appropriate subscription and Azure Migrate project.


Se il progetto è stato creato nella [versione precedente](migrate-services-overview.md#azure-migrate-versions) di Azure Migrate, individuarlo come segue:

1. Nel [portale](https://portal.azure.com)di Azure cercare **Azure Migrate**.
2. Nel dashboard di Azure Migrate, se è stato creato un progetto nella versione precedente, viene visualizzato un banner che fa riferimento a progetti precedenti. Selezionare il banner.

    ![Accedere a progetti esistenti](./media/create-manage-projects/access-existing-projects.png)

3. Esaminare l'elenco dei vecchi progetti.


## <a name="delete-a-project"></a>Eliminare un progetto

Eliminare come segue:

1. Aprire il gruppo di risorse di Azure in cui è stato creato il progetto.
2. Nella pagina Del gruppo di risorse selezionare **Mostra tipi nascosti.**
3. Selezionare il progetto di migrazione che si desidera eliminare e le risorse associate.
    - Il tipo di risorsa è **Microsoft.Migrate/migrateprojects**.
    - Se il gruppo di risorse viene usato esclusivamente dal progetto Azure Migrate, è possibile eliminare l'intero gruppo di risorse.


Tenere presente quanto segue:

- Quando si elimina, vengono eliminati sia il progetto che i metadati relativi ai computer individuati.
- Se si usa la versione precedente di Azure Migrate, aprire il gruppo di risorse di Azure in cui è stato creato il progetto. Selezionare il progetto di migrazione che si desidera eliminare (il tipo di risorsa è **Progetto di migrazione**).
- Se si usa l'analisi delle dipendenze con un'area di lavoro di Azure Log Analytics:If you're using dependency analysis with an Azure Log Analytics workspace:
    - Se è stata collegata un'area di lavoro di Log Analytics allo strumento di valutazione del server, l'area di lavoro non viene eliminata automaticamente. La stessa area di lavoro di Log Analytics può essere utilizzata per più scenari.
    - Se si desidera eliminare l'area di lavoro di Log Analytics, eseguire questa operazione manualmente.

### <a name="delete-a-workspace-manually"></a>Eliminare manualmente un'area di lavoro

1. Passare all'area di lavoro Log Analytics collegata al progetto.

    - Se il progetto Azure Migrate non è stato eliminato, è possibile trovare il collegamento all'area di lavoro in**Valutazione del server** **Essentials** > .
       ![Area](./media/create-manage-projects/loganalytics-workspace.png)di lavoro LA .
       
    - Se il progetto Azure Migrate è già stato eliminato, selezionare Gruppi di **risorse** nel riquadro sinistro del portale di Azure e individuare l'area di lavoro.
       
2. [Seguire le istruzioni](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace) per eliminare l'area di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Aggiungere strumenti di valutazione o migrazione ai progetti di Azure Migrate.Add [assessment](how-to-assess.md) or [migration](how-to-migrate.md) tools to Azure Migrate projects.
