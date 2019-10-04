---
title: Aggiungere uno strumento di valutazione/migrazione per la prima volta in Azure Migrate | Microsoft Docs
description: Viene descritto come creare un progetto Azure Migrate e aggiungere uno strumento di valutazione/migrazione.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: bd119956ced79b73b0376fe4530c9eafaf870238
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70934169"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Aggiungere uno strumento di valutazione/migrazione per la prima volta

Questo articolo descrive come aggiungere uno strumento di valutazione o migrazione a un progetto di [Azure migrate](migrate-overview.md) per la prima volta.  
Azure Migrate offre un hub centrale per tenere traccia dell'individuazione, della valutazione e della migrazione delle app e dei carichi di lavoro locali e delle macchine virtuali del cloud privato/pubblico in Azure. L'hub fornisce strumenti Azure Migrate per la valutazione e la migrazione, oltre a [offerte](migrate-services-overview.md#isv-integration) di fornitori di software indipendenti (ISV) di terze parti. 

## <a name="create-a-project-and-add-a-tool"></a>Creare un progetto e aggiungere uno strumento

Configurare un nuovo progetto di Azure Migrate in una sottoscrizione di Azure e aggiungere uno strumento.

- Un progetto Azure Migrate viene usato per archiviare i metadati di individuazione, valutazione e migrazione raccolti dall'ambiente che si sta valutando o migrando. 
- In un progetto è possibile tenere traccia delle risorse individuate e orchestrare la valutazione e la migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi** e cercare **Azure Migrate**.
2. In **Servizi** selezionare **Azure Migrate**.

    ![Configurare Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. In **Panoramica** fare clic su **Valutare ed eseguire la migrazione dei server** .
4. In **Individuare, valutare ed eseguire la migrazione dei server** fare clic su **Valutare ed eseguire la migrazione dei server** .

    ![Individuare e valutare i server](./media/how-to-add-tool-first-time/assess-migrate.png)

1. In **Individuare, valutare ed eseguire la migrazione dei server** fare clic su **Aggiungi strumenti**.
2. In **Progetto di migrazione** selezionare la sottoscrizione di Azure e creare un gruppo di risorse, se non se ne ha già uno.
3. In **Dettagli progetto**specificare il nome del progetto e l'area geografica in cui si desidera creare il progetto. 

    ![Creare un progetto di Azure Migrate](./media/how-to-add-tool-first-time/migrate-project.png)

    È possibile creare un progetto di Azure Migrate in una delle aree geografiche seguenti.

   **Area geografica** | **Area località di archiviazione**
    --- | ---
    Asia   | Asia sud-orientale o Asia orientale
    Europa | Europa settentrionale o Europa occidentale
    Giappone  | Giappone orientale o Giappone occidentale
    Regno Unito | Regno Unito meridionale o Regno Unito occidentale
    Stati Uniti | Stati Uniti centrali o Stati Uniti occidentali 2
    Canada | Canada centrale
    India  | India centrale o India meridionale
    Australia | Australia sudorientale

    L'area geografica specificata per il progetto viene usato solo per archiviare i metadati raccolti da macchine virtuali locali. Per la migrazione effettiva è possibile selezionare qualsiasi area di destinazione.

    Se si vuole specificare un'area specifica in una geografia per la distribuzione del progetto di migrazione e delle risorse associate (le restrizioni dei criteri nella sottoscrizione possono consentire la distribuzione di risorse di Azure solo in un'area specifica di Azure), è possibile usare l'API seguente per creare un progetto di migrazione. Specificare l'ID sottoscrizione, il nome del gruppo di risorse, eseguire la migrazione del nome del progetto insieme a location (tutte le aree di Azure indicate nella tabella in cui viene distribuito Azure Migrate).

    `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Fare clic su **Avanti**e aggiungere uno strumento di valutazione o migrazione.

    > [!NOTE]
    > Quando si crea un progetto, è necessario aggiungere almeno uno strumento di valutazione o migrazione.

5. In **Seleziona strumento di valutazione**aggiungere uno strumento di valutazione. Se non è necessario uno strumento di valutazione, selezionare **Ignora l'aggiunta di uno strumento di valutazione per il momento** > **successivo**. 
2. In **selezione strumento di migrazione**aggiungere uno strumento di migrazione come richiesto. Se non è necessario uno strumento di migrazione in questo momento, selezionare **Ignora l'aggiunta di uno strumento di migrazione per il momento** > **successivo**.
3. In **Verifica + Aggiungi strumenti**, rivedere le impostazioni e fare clic su **Aggiungi strumenti**.

Dopo aver creato il progetto, è possibile selezionare strumenti aggiuntivi per la valutazione e la migrazione di server e carichi di lavoro, database e app Web.

## <a name="create-additional-projects"></a>Creazione di progetti aggiuntivi

In alcuni casi, potrebbe essere necessario creare progetti di Azure Migrate aggiuntivi. Se, ad esempio, si dispone di data center in aree geografiche diverse o se è necessario archiviare i metadati in un'area geografica diversa. Creare un progetto aggiuntivo come segue:

1. Nel progetto Azure Migrate corrente fare clic su **Server** o **database**.
2. Nell'angolo superiore destro fare clic su **modifica** accanto al nome del progetto corrente.
3. In **Impostazioni**selezionare **fare clic qui per creare un nuovo progetto**.
4. Creare un nuovo progetto e aggiungere un nuovo strumento come descritto nella procedura precedente.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come aggiungere altri strumenti di [valutazione](how-to-assess.md) e [migrazione](how-to-migrate.md) . 
