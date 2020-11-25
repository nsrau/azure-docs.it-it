---
title: Integrazione del controllo del codice sorgente
description: Esperienza DevOps di database di livello aziendale per un pool SQL dedicato con integrazione nativa del controllo del codice sorgente tramite repository di Azure (Git e GitHub).
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 766837cacb4ef2acf874c2872b347b6d35633faa
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94700998"
---
# <a name="source-control-integration-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Integrazione del controllo del codice sorgente per il pool SQL dedicato in Azure Synapse Analytics

Questa esercitazione illustra come integrare il progetto di database SQL Server Data Tools (SSDT) con il controllo del codice sorgente.  L'integrazione del controllo del codice sorgente è il primo passaggio per la creazione della pipeline di integrazione e distribuzione continue con la risorsa pool SQL dedicato in Azure Synapse Analytics.

## <a name="before-you-begin"></a>Prima di iniziare

- Iscriversi per ottenere un'[organizzazione di Azure DevOps](https://azure.microsoft.com/services/devops/)
- Completare l'esercitazione [Creare e connettere](create-data-warehouse-portal.md)
- [Installare Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Configurare Azure DevOps e connettersi

1. Nell'organizzazione di Azure DevOps creare un progetto che ospiterà il progetto di database SSDT tramite un repository di Azure.

   ![Creare il progetto](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Crea progetto")

2. Aprire Visual Studio e connettersi all'organizzazione e al progetto di Azure DevOps del passaggio 1 selezionando **Gestisci connessione**.

   ![Gestire le connessioni](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Gestire le connessioni")

3. Connettersi al progetto selezionando **Gestisci connessioni** e quindi **Connetti a un progetto**.
 ![Connect1](./media/sql-data-warehouse-source-control-integration/3-connect-project.png "Connessione")


4. Individuare il progetto creato nel passaggio 1, quindi selezionare **Connetti**.
![Connect2](./media/sql-data-warehouse-source-control-integration/3.5-connect.png "Connessione")


3. Clonare il repository di Azure DevOps del progetto nel computer locale.

   ![Clonare il repository](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Clonare il repository")

Per altre informazioni sulla connessione ai progetti tramite Visual Studio, vedere [Connettersi ai progetti in Team Explorer](https://docs.microsoft.com/visualstudio/ide/connect-team-project?view=vs-2019). Per informazioni sulla clonazione di un repository con Visual Studio, vedere l'articolo [Clonare un repository Git esistente](https://docs.microsoft.com/azure/devops/repos/git/clone?view=azure-devops&tabs=visual-studio). 

## <a name="create-and-connect-your-project"></a>Creare il progetto e connettersi

1. In Visual Studio creare un nuovo progetto di database di SQL Server con una directory e un repository Git locale nel **repository clonato locale**.

   ![Creare un nuovo progetto](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Creare un nuovo progetto")  

2. Fare clic con il pulsante destro del mouse sul progetto sqlproject vuoto e importare il data warehouse nel progetto di database.

   ![Importare il progetto](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Importare il progetto")  

3. In Team Explorer in Visual Studio eseguire il commit delle modifiche apportate al repository Git locale.

   ![Eseguire il commit](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Commit")  

4. A questo punto, dopo aver eseguito il commit delle modifiche in locale nel repository clonato, sincronizzare ed effettuare il push delle modifiche nel repository Azure Repos nel progetto Azure DevOps.

   ![Sincronizzazione e push - staging](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Sincronizzazione e push - staging")

   ![Sincronizzazione e push](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Sincronizzazione e push")  

## <a name="validation"></a>Convalida

1. Verificare che sia stato eseguito il push delle modifiche nel repository di Azure aggiornando una colonna della tabella nel progetto di database da Visual Studio SQL Server Data Tools (SSDT).

   ![Convalidare l'aggiornamento della colonna](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Convalidare l'aggiornamento della colonna")

2. Eseguire il commit e il push della modifica dal repository locale al repository di Azure.

   ![Eseguire il push delle modifiche](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Effettuare il push delle modifiche")

3. Verificare che sia stato eseguito il push della modifica nel repository di Azure.

   ![Verificare](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Verificare le modifiche")

4. (**Facoltativo**) Usare Confronto schemi e aggiornare le modifiche apportate al data warehouse di destinazione tramite SSDT per assicurarsi che le definizioni degli oggetti nel repository di Azure e nel repository locale riflettano il data warehouse.

## <a name="next-steps"></a>Passaggi successivi

- [Sviluppo per il pool SQL dedicato](sql-data-warehouse-overview-develop.md)
