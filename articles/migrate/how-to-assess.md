---
title: Aggiungere strumenti di valutazione in Azure MigrateAdd assessment tools in Azure Migrate
description: Informazioni su come aggiungere strumenti di valutazione in Azure Migrate.Learn how to add assessment tools in Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: article
ms.manager: carmonm
ms.date: 11/19/2019
ms.author: raynew
ms.openlocfilehash: 64af78abd8f82b41d4a03fbb56c96e3038cef5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185903"
---
# <a name="add-assessment-tools"></a>Aggiungere strumenti di valutazione

Questo articolo descrive come aggiungere strumenti di valutazione in [Azure Migrate.](migrate-overview.md)

Azure Migrate provides a hub of tools for assessment and migration to Azure. Include gli strumenti di Azure Migrate, nonché altri strumenti e offerte di fornitori di software indipendenti (ISV).

Se si vuole aggiungere uno strumento di valutazione e non si dispone ancora di un progetto Azure Migrate, seguire questo [articolo.](how-to-add-tool-first-time.md)

## <a name="select-a-tool"></a>Selezionare uno strumento

Se si sceglie uno strumento di migrazione non di Azure per la valutazione, iniziare ottenendo una licenza o iscriversi a una versione di valutazione gratuita, in conformità con i criteri dello strumento. Gli strumenti hanno un'opzione per connettersi ad Azure Migrate.Tools have an option to connect to Azure Migrate. Seguire le istruzioni e la documentazione per connettere lo strumento ad Azure Migrate.Follow the instructions and documentation, to connect the tool to Azure Migrate. [Ulteriori informazioni](migrate-services-overview.md) sugli strumenti.


## <a name="select-an-assessment-scenario"></a>Selezionare uno scenario di valutazione

1. Nel progetto di Azure Migrate fare clic su **Panoramica**.
2. Selezionare lo scenario di valutazione che si desidera utilizzare:

    - Per individuare e valutare computer e carichi di lavoro per la migrazione ad Azure, selezionare **Valuta ed esegui la migrazione dei server.**
    - Per valutare i computer SQL locali, selezionare **Valuta ed esegui la migrazione dei database**.
    - Per valutare le app Web locali, selezionare **Valuta ed esegui la migrazione delle app Web.**

    ![Scenario di valutazione](./media/how-to-assess/assess-scenario.png)

## <a name="select-a-server-assessment-tool"></a>Selezionare uno strumento di valutazione del server 

1. Fare clic su **Valuta ed esegui la migrazione dei server**.
2. In **Azure Migrate - Server**, se non è stato aggiunto uno strumento di valutazione, in Strumenti di **valutazione**selezionare Fare clic qui per aggiungere uno strumento di **valutazione.** Se sono già stati aggiunti strumenti di valutazione, in **Aggiungi altri strumenti**di valutazione selezionare **Cambia**.

    > [!NOTE]
    > Se è necessario passare a un progetto diverso, in **Azure Migrate - Server**, accanto a Vedere i dettagli per un progetto di migrazione **diverso**fare clic su Fare **clic qui**.

3. In **Azure Migrate**selezionare lo strumento di valutazione che si vuole usare.

    - Se si usa Valutazione server di Azure di migrazione, è possibile configurare, eseguire e visualizzare le valutazioni direttamente nel progetto Azure Migrate.If you use Azure Migrate Server Assessment, you can set up, run, and view assessments directly in the Azure Migrate project.
    - Se si utilizza uno strumento di valutazione diverso, passare al collegamento fornito per il sito ed eseguire la valutazione in base alle istruzioni fornite.


## <a name="select-a-database-assessment-tool"></a>Selezionare uno strumento di valutazione del database

1. Fare clic su **Valuta ed esegui la migrazione dei database**
2. In **Database**fare clic su **Aggiungi strumenti**.
3. In Aggiungi uno strumento > **strumento di valutazione Seleziona**selezionare lo strumento che si desidera utilizzare per valutare il database.

## <a name="select-a-web-app-assessment-tool"></a>Selezionare uno strumento di valutazione delle app Web

1. Fare clic su **Valuta ed esegui la migrazione delle app Web**.
2. Seguire il collegamento allo strumento di migrazione per il servizio app di Azure.Follow the link to the Migration tool for the Azure App Service. Utilizzare lo strumento di migrazione per:

    - **Valutare le app online:** è possibile valutare le app con un URL pubblico online usando l'Assistente migrazione del servizio app di Azure.Assess apps online : You can assess apps with a public URL online, using the Azure App Service Migration Assistant.
    - **.NET/PHP**: Per le app interne .NET e PHP, è possibile scaricare ed eseguire l'Assistente migrazione.



## <a name="next-steps"></a>Passaggi successivi

Provare una valutazione usando Azure Migrate Server Assessment for [VMware](tutorial-prepare-vmware.md) VMs, [Hyper-V](tutorial-prepare-hyper-v.md)o [server fisici](tutorial-prepare-physical.md)
