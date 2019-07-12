---
title: Aggiungere uno strumento di valutazione/migrazione per la prima volta in Azure Migrate | Microsoft Docs
description: Viene descritto come creare un progetto Azure Migrate e aggiungere uno strumento di valutazione/migrazione.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/09/2019
ms.author: raynew
ms.openlocfilehash: b226f7c5879673b573133cde45db78d8d1f2fffa
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812025"
---
# <a name="add-an-assessmentmigration-tool-for-the-first-time"></a>Aggiungere uno strumento di valutazione/migrazione per la prima volta

Questo articolo descrive come aggiungere uno strumento di valutazione o la migrazione a un [Azure Migrate](migrate-overview.md) progetto per la prima volta.  
Azure Migrate offre un hub centrale per tenere traccia di individuazione, valutazione e migrazione delle App in locale e i carichi di lavoro e cloud privati/pubblici macchine virtuali di Azure. L'hub fornisce gli strumenti di Azure Migrate per la valutazione e migrazione, nonché il fornitore di software di terze parti, indipendenti (ISV) [offerte](migrate-services-overview.md#isv-integration) . 

## <a name="create-a-project-and-add-a-tool"></a>Creare un progetto e aggiungere uno strumento

Configurare un nuovo progetto Azure Migrate in una sottoscrizione di Azure e aggiungere uno strumento.

- Consente di archiviare il rilevamento, valutazione e migrazione i metadati raccolti dall'ambiente si è verificata o la migrazione di un progetto Azure Migrate. 
- In un progetto è possibile tenere traccia delle risorse individuate e orchestrare la valutazione e la migrazione.

1. Nel portale di Azure > **tutti i servizi**, cercare **Azure Migrate**.
2. Sotto **Services**, selezionare **Azure Migrate**.

    ![Configurare Azure Migrate](./media/how-to-add-tool-first-time/azure-migrate-search.png)

3. Nella **Overview**, fare clic su **valutazione e la migrazione di server**.
4. Sotto **Discover, valutare ed eseguire la migrazione di server**, fare clic su **valutazione e la migrazione di server**.

    ![Individuare e valutare i server](./media/how-to-add-tool-first-time/assess-migrate.png)

1. Nelle **Discover, valutare ed eseguire la migrazione di server**, fare clic su **aggiungere gli strumenti**.
2. Nelle **progetto di migrazione**, selezionare la sottoscrizione di Azure e creare un gruppo di risorse se non hai uno.
3. Nelle **dettagli progetto**, specificare il nome del progetto e area geografica in cui si desidera creare il progetto. 

    ![Creare un progetto Azure Migrate](./media/how-to-add-tool-first-time/migrate-project.png)

    È possibile creare un progetto Azure Migrate in una qualsiasi di queste aree geografiche.

    **Area geografica** | **Area del percorso di archiviazione**
    --- | ---
    Asia | Asia sud-orientale o Asia orientale
    Europa | Europa meridionale o Europa occidentale
    Regno Unito | Regno Unito meridionale e Regno Unito occidentale
    Stati Uniti | Stati Uniti centrali o Stati Uniti occidentali 2

    L'area geografica specificata per il progetto viene usato solo per archiviare i metadati raccolti da macchine virtuali locali. È possibile selezionare qualsiasi area di destinazione per la migrazione effettiva.

4. Fare clic su **successivo**e aggiungere uno strumento di valutazione o la migrazione.

    > [!NOTE]
    > Quando si crea un progetto è necessario aggiungere almeno uno strumento di valutazione o la migrazione.

5. Nelle **lo strumento di valutazione selezionare**, aggiungere uno strumento di valutazione. Se non è necessario uno strumento di valutazione, selezionare **ignorare l'aggiunta di uno strumento di valutazione per il momento** > **successivo**. 
2. Nelle **lo strumento di migrazione selezionare**, aggiungere uno strumento di migrazione in base alle esigenze. Se attualmente non è necessario uno strumento di migrazione, selezionare **ignorare l'aggiunta di uno strumento di migrazione per il momento** > **successivo**.
3. In **rivedere e aggiungere gli strumenti**, rivedere le impostazioni e fare clic su **aggiungere gli strumenti**.

Dopo aver creato il progetto è possibile selezionare altri strumenti per la valutazione e migrazione di server e carichi di lavoro, i database e le app web.

## <a name="create-additional-projects"></a>Creazione di progetti aggiuntivi

In alcuni casi, potrebbe essere necessario creare ulteriori progetti Azure Migrate. Ad esempio se si utilizzano i Data Center in aree geografiche diverse, è necessario archiviare i metadati in una diversa area geografica. Creare un altro progetto come indicato di seguito:

1. Nel progetto Azure Migrate corrente, fare clic su **i server** oppure **database**.
2. Nell'angolo superiore destro, fare clic su **modifica** accanto al nome del progetto corrente.
3. Nelle **le impostazioni**, selezionare **fare clic qui per creare un nuovo progetto**.
4. Creare un nuovo progetto e aggiungere un nuovo strumento, come descritto nella procedura precedente.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come aggiungere ulteriori [assessment](how-to-assess.md) e [migrazione](how-to-migrate.md) strumenti. 
