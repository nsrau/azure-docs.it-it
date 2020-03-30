---
title: Aggiungere uno strumento di valutazione/migrazione in Azure MigrateAdd an assessment/migration tool in Azure Migrate
description: Descrive come creare un progetto di Azure Migrate e aggiungere uno strumento di valutazione/migrazione.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 319d97d96bd054aed90079777e2ff83d0e308e5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185944"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Aggiungere uno strumento di valutazione/migrazione per la prima volta

Questo articolo descrive come aggiungere uno strumento di valutazione o migrazione a un progetto [di Azure Migrate](migrate-overview.md) per la prima volta.  
Azure Migrate provides a central hub to track discovery, assessment and migration of your on-premises apps and workloads, and private/public cloud VMs, to Azure. L'hub fornisce strumenti di Azure Migrate per la valutazione e la migrazione, nonché altri strumenti e offerte di [fornitori](migrate-services-overview.md#isv-integration) di software indipendenti (ISV). 

## <a name="create-a-project-and-add-a-tool"></a>Creare un progetto e aggiungere uno strumento

Configurare un nuovo progetto di Azure Migrate in una sottoscrizione di Azure e aggiungere uno strumento.

- Un progetto Azure Migrate viene usato per archiviare i metadati di individuazione, valutazione e migrazione raccolti dall'ambiente che si sta valutando o eseguendo la migrazione. 
- In un progetto è possibile tenere traccia delle risorse individuate e orchestrare la valutazione e la migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi** e cercare **Azure Migrate**.
2. In **Servizi** selezionare **Azure Migrate**.

    ![Configurare Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. In **Panoramica** fare clic su **Valutare ed eseguire la migrazione dei server **.
4. In **Individuare, valutare ed eseguire la migrazione dei server** fare clic su **Valutare ed eseguire la migrazione dei server **.

    ![Individuare e valutare i server](./media/how-to-add-tool-first-time/assess-migrate.png)

1. In **Individuare, valutare ed eseguire la migrazione dei server** fare clic su **Aggiungi strumenti**.
2. In **Progetto di migrazione** selezionare la sottoscrizione di Azure e creare un gruppo di risorse, se non se ne ha già uno.
3. In **Dettagli progetto**specificare il nome del progetto e l'area geografica in cui si desidera creare il progetto. 

    ![Creare un progetto di Azure Migrate](./media/how-to-add-tool-first-time/migrate-project.png)

    È possibile creare un progetto di Azure Migrate in una delle aree geografiche seguenti.

   **Geografia** | **Area della posizione di archiviazione**
    --- | ---
    Asia   | Asia sud-orientale o Asia orientale
    Europa | Europa settentrionale o Europa occidentale
    Giappone  | Giappone Est o Giappone Ovest
    Regno Unito | Regno Unito meridionale o Regno Unito occidentale
    Stati Uniti | Stati Uniti centrali o Stati Uniti occidentali 2
    Canada | Canada centrale
    India  | India Centrale o India Sud
    Australia | Australia Sud-est

    L'area geografica specificata per il progetto viene usato solo per archiviare i metadati raccolti da macchine virtuali locali. Per la migrazione effettiva è possibile selezionare qualsiasi area di destinazione.

    Se si vuole specificare un'area specifica all'interno di un'area geografica per la distribuzione del progetto di migrazione e delle risorse associate (le restrizioni dei criteri nella sottoscrizione possono consentire la distribuzione di risorse di Azure solo in un'area di Azure specifica), è possibile usare l'API seguente per creare un progetto di migrazione. Specificare l'ID sottoscrizione, il nome del gruppo di risorse, Eseguire la migrazione del nome del progetto insieme alla posizione (una delle aree di Azure indicate nella tabella in cui viene distribuita Azure Migrate).

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Fare clic su **Avanti**e aggiungere uno strumento di valutazione o migrazione.

    > [!NOTE]
    > Quando si crea un progetto è necessario aggiungere almeno uno strumento di valutazione o migrazione.

5. In **Seleziona strumento di valutazione,** aggiungere uno strumento di valutazione. Se non è necessario uno strumento di valutazione, selezionare **Ignora l'aggiunta** > di uno strumento di valutazione per ora**Avanti**. 
2. In **Seleziona strumento di migrazione**aggiungere uno strumento di migrazione in base alle esigenze. Se al momento non è necessario uno strumento di migrazione, selezionare **Ignora l'aggiunta** > di uno strumento di migrazione per ora**Avanti**.
3. In **Revisione e aggiunta di strumenti**rivedere le impostazioni e fare clic su Aggiungi **strumenti**.

Dopo aver creato il progetto è possibile selezionare strumenti aggiuntivi per la valutazione e la migrazione di server e carichi di lavoro, database e app Web.

## <a name="create-additional-projects"></a>Creare progetti aggiuntivi

In alcune circostanze, potrebbe essere necessario creare altri progetti di Azure Migrate.In some circumstances, you might need to create additional Azure Migrate projects. Ad esempio, se si dispone di data center in aree geografiche diverse o è necessario archiviare i metadati in un'area geografica diversa. Creare un progetto aggiuntivo come segue:

1. Nel progetto di Azure Migrate corrente fare clic su **Server** o **Database**.
2. Nell'angolo in alto a destra, fai clic su **Cambia** accanto al nome del progetto corrente.
3. In **Impostazioni**selezionare **Fare clic qui per creare un nuovo progetto.**
4. Creare un nuovo progetto e aggiungere un nuovo strumento come descritto nella procedura precedente.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come aggiungere ulteriori strumenti di [valutazione](how-to-assess.md) e [migrazione.](how-to-migrate.md) 
