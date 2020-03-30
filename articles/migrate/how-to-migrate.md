---
title: Aggiungere strumenti di migrazione in Azure MigrateAdd migration tools in Azure Migrate
description: Informazioni su come aggiungere strumenti di migrazione in Azure Migrate.Learn how to add migration tools in Azure Migrate.
author: rayne-wiselman
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 11/18/2019
ms.author: raynew
ms.openlocfilehash: 6bcb65912627356215769ccc8380ee1a7404f6f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74185970"
---
# <a name="add-migration-tools"></a>Aggiungere strumenti di migrazione

Questo articolo descrive come aggiungere strumenti di migrazione in [Azure Migrate.](migrate-overview.md)

Azure Migrate provides a hub of tools for assessment and migration to Azure. Include strumenti nativi, strumenti forniti da altri servizi di Azure e offerte di fornitori di software indipendenti di terze parti (ISV).

Se si vuole aggiungere uno strumento di migrazione e non è ancora stato configurato un progetto Azure Migrate, seguire questo [articolo.](how-to-add-tool-first-time.md)



## <a name="selecting-an-isv-tool"></a>Selezione di uno strumento ISV

Se si sceglie [uno strumento ISV](migrate-services-overview.md#isv-integration) per la migrazione, è possibile iniziare ottenendo una licenza o iscrivervi per una prova gratuita, in conformità con i criteri ISV. In ogni strumento è disponibile un'opzione per connettersi ad Azure Migrate. Distribuire lo strumento e seguire le istruzioni e la documentazione dello strumento per connettere l'area di lavoro degli strumenti con Azure Migrate.Deploy the tool, and follow the tool instructions and documentation to connect the tool workspace with Azure Migrate. 

## <a name="select-a-migration-scenario"></a>Selezionare uno scenario di migrazione

1. Nel progetto di Azure Migrate fare clic su **Panoramica**.
2. Selezionare lo scenario di migrazione che si desidera utilizzare:

    - Per eseguire la migrazione di computer e carichi di lavoro in Azure, selezionare **Valuta ed esegui la migrazione dei server**.
    - Per eseguire la migrazione di computer SQL locali, selezionare **Valuta ed eseguire la migrazione dei database**.
    - Per eseguire la migrazione delle app Web locali, selezionare **Valuta ed esegui la migrazione delle app Web.**
    - Per eseguire la migrazione di grandi quantità di dati locali in Azure in modalità offline, selezionare **Ordina una casella dati**.

    ![Scenario di valutazione](./media/how-to-migrate/assess-scenario.png)

## <a name="select-a-server-migration-tool"></a>Selezionare uno strumento di migrazione server

1. Fare clic su **Valuta ed esegui la migrazione dei server**.
2. In **Azure Migrate - Server**, se non sono ancora stati aggiunti strumenti di migrazione, in Strumenti di **migrazione**selezionare Fare clic qui per aggiungere uno strumento di **migrazione.** Se sono già stati aggiunti strumenti di migrazione, in **Aggiungi altri strumenti**di migrazione selezionare **Cambia**.

    > [!NOTE]
    > Se è necessario passare a un progetto diverso, in **Azure Migrate - Server**, accanto a Vedere i dettagli per un progetto di migrazione **diverso**fare clic su Fare **clic qui**.

3. In **Azure Migrate**selezionare lo strumento di migrazione che si vuole usare.
    - Se si usa migrazione del server di Azure Migrate, è possibile configurare ed eseguire le migrazioni direttamente nel progetto Azure Migrate.If you use Azure Migrate Server Migration, you can set up and run migrations directly in the Azure Migrate project.
    - Se si utilizza uno strumento di valutazione di terze parti, passare al collegamento fornito per l'ISV ed eseguire la migrazione in base alle istruzioni fornite.

## <a name="select-a-database-migration-tool"></a>Selezionare uno strumento di migrazione del database

1. Fare clic su **Valuta ed esegui la migrazione dei database**
2. In **Database**fare clic su **Aggiungi strumenti**.
3. In Aggiungi uno strumento > **Strumento di migrazione selezionare**lo strumento che si desidera utilizzare per eseguire la migrazione del database.

## <a name="select-a-web-app-migration-tool"></a>Selezionare uno strumento di migrazione di app Web

1. Fare clic su **Valuta ed esegui la migrazione delle app Web**.
2. Seguire il collegamento allo strumento di migrazione per il servizio app di Azure.Follow the link to the Migration tool for the Azure App Service. Utilizzare lo strumento di migrazione per:

    - **Valutazione delle app online:** è possibile valutare ed eseguire la migrazione delle app con un URL pubblico online usando l'Assistente migrazione del servizio app di Azure.Assess apps online : You can assess and migrate apps with a public URL online, using the Azure App Service Migration Assistant.
    - **.NET/PHP**: Per le app interne .NET e PHP, è possibile scaricare ed eseguire l'Assistente migrazione.

## <a name="order-an-azure-data-box"></a>Ordinare una casella di dati di AzureOrder an Azure Data Box

Per eseguire la migrazione di grandi quantità di dati in Azure, è possibile ordinare una casella DAta di Azure per il trasferimento di dati offline.

1. Fare clic su **Ordina una casella dati**.
2. In **Selezionare la casella di dati di Azure**specificare la sottoscrizione. 
3. Il trasferimento sarà un'importazione in Azure.The transfer will be an import to Azure. Specificare l'origine dati e la destinazione dell'area di Azure per i dati.

## <a name="next-steps"></a>Passaggi successivi

Provare una migrazione usando Migrazione server di Azure Migrate per macchine virtuali Hyper-V o [VMware.Try](tutorial-migrate-vmware.md) out a migration using Azure Migrate Server Migration for [Hyper-V](tutorial-migrate-hyper-v.md) or VMware VMs.
