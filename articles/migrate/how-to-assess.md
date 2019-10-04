---
title: Componente aggiuntivo Strumenti di valutazione di Azure Migrate | Microsoft Docs
description: Viene descritto come aggiungere gli strumenti di valutazione nell'hub di Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.date: 07/04/2019
ms.author: raynew
ms.openlocfilehash: d176e6276d69cd3465aa4943efa86ea1e6b0736d
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67811648"
---
# <a name="add-assessment-tools"></a>Aggiungere gli strumenti di valutazione

Questo articolo descrive come aggiungere gli strumenti di valutazione nella [Azure Migrate](migrate-overview.md).

Azure Migrate offre un hub di strumenti per la valutazione e migrazione in Azure. Include gli strumenti originali, gli strumenti forniti da altri servizi di Azure, mentre le offerte di fornitori di software indipendenti di terze parti.

Se si desidera aggiungere uno strumento di valutazione e non si ha un progetto Azure Migrate, seguire questo [articolo](how-to-add-tool-first-time.md).

## <a name="selecting-an-isv-tool"></a>Selezione di uno strumento di ISV

Se si sceglie un' [dello strumento di ISV](migrate-services-overview.md#isv-integration) per valutazione, è possibile iniziare da ottenere una licenza o iscriversi per una versione di valutazione gratuita, in conformità con i criteri di ISV. In ogni strumento, è disponibile un'opzione per connettersi a Azure Migrate. Seguire le istruzioni degli strumenti e documentazione per connettere l'area di lavoro dello strumento con Azure Migrate. 


## <a name="select-an-assessment-scenario"></a>Selezionare uno scenario di valutazione

1. Nel progetto Azure Migrate, fare clic su **Panoramica**.
2. Selezionare lo scenario di valutazione che si desidera utilizzare:

    - Per individuare e valutare le macchine e i carichi di lavoro per la migrazione ad Azure, selezionare **fase di valutazione e la migrazione di server**.
    - Per valutare le macchine virtuali SQL in locale, selezionare **fase di valutazione e la migrazione dei database**.
    - Per valutare l'App web in locale, selezionare **fase di valutazione e la migrazione di App web**.

    ![Scenario di valutazione](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Selezionare uno strumento di valutazione di server 

1. Fare clic su **valutare ed eseguire la migrazione di server**.
2. In **Azure Migrate - server**, se è stato aggiunto uno strumento di valutazione, nella sezione **strumenti di valutazione**, selezionare **fare clic qui per aggiungere uno strumento di valutazione**. Se già stato aggiunto agli strumenti di valutazione, nella **aggiungere ulteriori strumenti di valutazione**, selezionare **modifica**.

    > [!NOTE]
    > Se è necessario passare a un altro progetto, in **Azure Migrate - server**, accanto a **vedere i dettagli per un progetto di migrazione diverso**, fare clic su **fare clic qui**.

3. Nelle **Azure Migrate**, selezionare lo strumento di valutazione si desidera utilizzare.

    
    ![Strumenti di valutazione](./media/how-to-assess/assess-tool.png)

    - Se si usa Azure eseguire la migrazione di Server Assessment, è possibile configurare, eseguire e visualizzare le valutazioni direttamente nel progetto Azure Migrate.
    - Se si usa uno strumento di valutazione di terze parti, passare al collegamento fornito per il sito ed eseguire la valutazione in conformità con le istruzioni che offrono.


## <a name="select-a-database-assessment-tool"></a>Selezionare uno strumento di valutazione di database

1. Fare clic su **valuta e la migrazione dei database**
2. Nelle **database**, fare clic su **aggiungere gli strumenti**.
3. In Aggiungi uno strumento > **lo strumento di valutazione selezionare**, selezionare lo strumento da usare per valutare il database.

## <a name="select-a-web-app-assessment-tool"></a>Selezionare uno strumento di valutazione di app web

1. Fare clic su **fase di valutazione e la migrazione di App web**.
2. Seguire il collegamento allo strumento di migrazione per il servizio App di Azure. Usare lo strumento di migrazione per:

    - **Valutare le app online**: È possibile valutare le app con un URL pubblico online, usando Azure App Service Migration Assistant.
    - **.NET/PHP**: Per le app .NET e PHP interne, è possibile scaricare ed eseguire il Migration Assistant.



## <a name="next-steps"></a>Passaggi successivi

Provare una valutazione usando Azure eseguire la migrazione di Server di valutazione per [Hyper-V](tutorial-prepare-hyper-v.md) oppure [VMware](tutorial-prepare-vmware.md) macchine virtuali.
