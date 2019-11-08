---
title: Integrazione del controllo del codice sorgente
description: Esperienza DevOps di database di livello aziendale per SQL Data Warehouse con integrazione nativa del controllo del codice sorgente tramite Azure Repos (Git e GitHub).
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 82d366b3f807e21e3531da00da78520fed90bc66
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73645254"
---
# <a name="source-control-integration-for-azure-sql-data-warehouse"></a>Integrazione del controllo del codice sorgente per Azure SQL Data Warehouse

Questa esercitazione illustra come integrare il progetto di database SQL Server Data Tools (SSDT) con il controllo del codice sorgente.  L'integrazione del controllo del codice sorgente è il primo passaggio per la creazione della pipeline di integrazione e distribuzione continua con SQL Data Warehouse. 

## <a name="before-you-begin"></a>Prima di iniziare

- Iscriversi per ottenere un'[organizzazione di Azure DevOps](https://azure.microsoft.com/services/devops/)
- Completare l'esercitazione [Creare e connettere](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)
-  [Installare Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/) 

## <a name="set-up-and-connect-to-azure-devops"></a>Configurare Azure DevOps e connettersi

1. Nell'organizzazione di Azure DevOps creare un progetto che ospiterà il progetto di database SSDT tramite un repository di Azure

   ![Creare il progetto](media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Crea progetto")

2. Aprire Visual Studio e connettersi all'organizzazione e al progetto di Azure DevOps del passaggio 1 selezionando "Gestisci connessioni"

   ![Gestire le connessioni](media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Gestire le connessioni")

   ![Connettere](media/sql-data-warehouse-source-control-integration/3-connect.png "Connettere")

3. Clonare il repository Azure Repos del progetto nel computer locale

   ![Clonare il repository](media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Clonare il repository")

## <a name="create-and-connect-your-project"></a>Creare il progetto e connettersi

1. In Visual Studio creare un nuovo progetto di database SQL Server con una directory e un repository Git locale nel **repository clonato locale**

   ![Creare un nuovo progetto](media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Creare un nuovo progetto")  

2. Fare clic con il pulsante destro del mouse sul progetto SQL vuoto e importarvi il data warehouse

   ![Importare il progetto](media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Importare il progetto")  

3. In Team Explorer di Visual Studio eseguire il commit di tutte le modifiche apportate al repository Git locale 

   ![Eseguire il commit](media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Commit")  

4. A questo punto, dopo aver eseguito il commit delle modifiche in locale nel repository clonato, sincronizzare ed effettuare il push delle modifiche nel repository Azure Repos nel progetto Azure DevOps.

   ![Sincronizzazione e push - staging](media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Sincronizzazione e push - staging")

   ![Sincronizzazione e push](media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Sincronizzazione e push")  

## <a name="validation"></a>Convalida

1. Verificare che il push delle modifiche sia stato eseguito nel repository Azure Repos aggiornando una colonna della tabella nel progetto di database da Visual Studio SQL Server Data Tools (SSDT)

   ![Convalidare l'aggiornamento della colonna](media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Convalidare l'aggiornamento della colonna")

2. Eseguire il commit e il push della modifica dal repository locale al repository Azure Repos

   ![Eseguire il push delle modifiche](media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Effettuare il push delle modifiche")

3. Verificare che il push della modifica sia stato eseguito nel repository Azure Repos

   ![Verificare](media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Verificare le modifiche")

4. (**Facoltativo**) Usare Confronto schema e aggiornare le modifiche apportate al data warehouse di destinazione tramite SSDT per assicurarsi che le definizioni degli oggetti nel repository Azure Repos e nel repository locale riflettano il data warehouse

## <a name="next-steps"></a>Passaggi successivi

- [Sviluppo per Azure SQL Data Warehouse](sql-data-warehouse-overview-develop.md)

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->

