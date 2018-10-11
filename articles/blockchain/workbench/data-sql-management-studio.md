---
title: Usare i dati di Azure Blockchain Workbench con SQL Server Management Studio
description: Informazioni su come connettersi al database SQL di Azure Blockchain Workbench da SQL Server Management Studio.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 10/1/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: 405104554b4ee8e773b6d2e39966a262f98beb6d
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48242096"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Usare i dati di Azure Blockchain Workbench con SQL Server Management Studio

Microsoft SQL Server Management Studio consente di scrivere e testare rapidamente le query nel database SQL di Azure Blockhain Workbench. Questa sezione contiene una procedura dettagliata su come connettersi al database SQL di Azure Blockchain Workbench da SQL Server Management Studio.

## <a name="prerequisites"></a>Prerequisiti

* Scaricare [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>Connessione di SQL Server Management Studio ai dati di Azure Blockchain Workbench

1. Aprire SQL Server Management Studio e selezionare **Connetti**.
2. Selezionare **Motore di database**.

    ![Motore di database](./media/data-sql-management-studio/database-engine.png)

3. Nella finestra di dialogo **Connect to Server** (Connetti al server) immettere il nome del server e le credenziali del database.

    Se si usano le credenziali create durante il processo di distribuzione di Azure Blockchain Workbench, il nome utente sarà **dbadmin** e la password sarà quella indicata durante la distribuzione.

    ![Immettere le credenziali di SQL](./media/data-sql-management-studio/sql-creds.png)

 4. SQL Server Management Studio consente di visualizzare l'elenco di database, viste di database e stored procedure nel database di Azure Blockchain Workbench.

    ![Elenco di database](./media/data-sql-management-studio/db-list.png)

5. Per visualizzare i dati associati a una delle viste di database, è possibile generare automaticamente un'istruzione di selezione usando la procedura seguente.
6. Fare clic con il pulsante destro del mouse su una qualsiasi delle viste di database in Esplora oggetti.
7. Selezionare **Script View as** (Visualizza script come).
8. Scegliere **SELECT to** (SELEZIONA per).
9. Selezionare **Nuova finestra editor di query**.
10. È possibile creare una nuova query selezionando **Nuova query**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Database views in Azure Blockchain Workbench](database-views.md) (Viste di database in Azure Blockchain Workbench)