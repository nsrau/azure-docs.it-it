---
title: Aggiungere uno strumento di valutazione/migrazione in Azure Migrate
description: Viene descritto come creare un progetto Azure Migrate e aggiungere uno strumento di valutazione/migrazione.
ms.topic: how-to
ms.date: 04/16/2020
ms.openlocfilehash: a94e3cc18f46c457d6ed54ef88c62adefb07c5b9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86102532"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Aggiungere uno strumento di valutazione/migrazione per la prima volta

Questo articolo descrive come aggiungere uno strumento di valutazione o migrazione a un progetto di [Azure migrate](./migrate-services-overview.md) per la prima volta.  
Azure Migrate offre un hub centrale per tenere traccia dell'individuazione, della valutazione e della migrazione delle app e dei carichi di lavoro locali e delle macchine virtuali del cloud privato/pubblico in Azure. L'hub fornisce strumenti di Azure Migrate per la valutazione e la migrazione, nonché altri strumenti e [offerte](migrate-services-overview.md#isv-integration) di fornitori di software indipendenti (ISV). 

## <a name="check-permissions-to-create-project"></a>Controllare le autorizzazioni per creare il progetto

Se non è ancora stato creato un progetto di Azure Migrate, verificare di avere le autorizzazioni corrette.

1. Nel portale di Azure aprire la sottoscrizione e selezionare **Controllo di accesso (IAM)** .
2. In Verifica l'accesso trovare l'account pertinente e fare clic su di esso per visualizzare le autorizzazioni. È necessario avere le autorizzazioni di Collaboratore o Proprietario.
    - Se è appena stato creato un account Azure gratuito, si è proprietari della propria sottoscrizione.
    - Se non si ha il ruolo di proprietario della sottoscrizione, collaborare con il proprietario per assegnare il ruolo.

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
3. In **Dettagli progetto**specificare il nome del progetto e l'area geografica in cui si desidera creare il progetto.  Esaminare le aree geografiche supportate per i cloud [pubblico](migrate-support-matrix.md#supported-geographies-public-cloud) e per [enti pubblici](migrate-support-matrix.md#supported-geographies-azure-government).

    ![Creare un progetto di Azure Migrate](./media/how-to-add-tool-first-time/migrate-project.png)

    - L'area geografica specificata per il progetto viene usato solo per archiviare i metadati raccolti da macchine virtuali locali. Per la migrazione effettiva è possibile selezionare qualsiasi area di destinazione.
    - Se è necessario distribuire un progetto all'interno di un'area specifica in una geografia, usare l'API seguente per creare un progetto. Specificare l'ID sottoscrizione, il nome del gruppo di risorse e il nome del progetto, insieme al percorso. Esaminare le aree geografiche/aree per i cloud [pubblici](migrate-support-matrix.md#supported-geographies-public-cloud) e [governativi](migrate-support-matrix.md#supported-geographies-azure-government).

        `PUT /subscriptions/<subid>/resourceGroups/<rg>/providers/Microsoft.Migrate/MigrateProjects/<mymigrateprojectname>?api-version=2018-09-01-preview "{location: 'centralus', properties: {}}"`   


4. Fare clic su **Avanti**e aggiungere uno strumento di valutazione o migrazione.

    > [!NOTE]
    > Quando si crea un progetto, è necessario aggiungere almeno uno strumento di valutazione o migrazione.

5. In **Seleziona strumento di valutazione**aggiungere uno strumento di valutazione. Se non è necessario uno strumento di valutazione, selezionare **Ignora l'aggiunta di uno strumento di valutazione per il momento**  >  **successivo**. 
2. In **selezione strumento di migrazione**aggiungere uno strumento di migrazione come richiesto. Se non è necessario uno strumento di migrazione in questo momento, selezionare **Ignora l'aggiunta di uno strumento di migrazione per il momento**  >  **successivo**.
3. In **Rivedi e aggiungi strumenti** rivedere le impostazioni e fare clic su **Aggiungi strumenti**.

Dopo aver creato il progetto, è possibile selezionare strumenti aggiuntivi per la valutazione e la migrazione di server e carichi di lavoro, database e app Web.

## <a name="create-additional-projects"></a>Creazione di progetti aggiuntivi

In alcuni casi, potrebbe essere necessario creare progetti di Azure Migrate aggiuntivi. Se, ad esempio, si dispone di data center in aree geografiche diverse o se è necessario archiviare i metadati in un'area geografica diversa. Creare un progetto aggiuntivo come segue:

1. Nel progetto Azure Migrate corrente fare clic su **Server** o **database**.
2. Nell'angolo superiore destro fare clic su **modifica** accanto al nome del progetto corrente.
3. In **Impostazioni**selezionare **fare clic qui per creare un nuovo progetto**.
4. Creare un nuovo progetto e aggiungere un nuovo strumento come descritto nella procedura precedente.

## <a name="next-steps"></a>Passaggi successivi

- Introduzione a [Azure migrate: server Assessment](migrate-services-overview.md#azure-migrate-server-assessment-tool)o [Azure migrate: migrazione del server](migrate-services-overview.md#azure-migrate-server-migration-tool).
- Se è stato aggiunto uno strumento ISV o Mover, [esaminare i passaggi](prepare-isv-movere.md) per preparare il collegamento dello strumento a Azure migrate.
- Informazioni su come aggiungere altri strumenti di [valutazione](how-to-assess.md) e [migrazione](how-to-migrate.md) . 
