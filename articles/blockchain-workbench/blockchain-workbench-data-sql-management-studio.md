---
title: Usare i dati di Azure Blockchain Workbench con SQL Server Management Studio
description: Informazioni su come connettersi al database SQL di Azure Blockchain Workbench da SQL Server Management Studio.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/3/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: mmercuri
manager: femila
ms.openlocfilehash: b640aec322d6dc184a428e67d9576cf162c8f394
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
ms.locfileid: "34075256"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Usare i dati di Azure Blockchain Workbench con SQL Server Management Studio

Microsoft SQL Server Management Studio consente di scrivere e testare rapidamente le query nel database SQL di Azure Blockhain Workbench. Questa sezione contiene una procedura dettagliata su come connettersi al database SQL di Azure Blockchain Workbench da SQL Server Management Studio.

## <a name="prerequisites"></a>prerequisiti

* Scaricare [SQL Server Management Studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>Connessione di SQL Server Management Studio ai dati di Azure Blockchain Workbench

1. Aprire SQL Server Management Studio e selezionare **Connetti**.
2. Selezionare **Motore di database**.

    ![Motore di database](media/blockchain-workbench-data-sql-management-studio/database-engine.png)

3. Nella finestra di dialogo **Connect to Server** (Connetti al server) immettere il nome del server e le credenziali del database.

    Se si usano le credenziali create durante il processo di distribuzione di Azure Blockchain Workbench, il nome utente sarà **dbadmin** e la password sarà quella indicata durante la distribuzione.

    ![Immettere le credenziali di SQL](media/blockchain-workbench-data-sql-management-studio/sql-creds.png)

 4. SQL Server Management Studio consente di visualizzare l'elenco di database, viste di database e stored procedure nel database di Azure Blockchain Workbench.

    ![Elenco di database](media/blockchain-workbench-data-sql-management-studio/db-list.png)

5. Per visualizzare i dati associati a una delle viste di database, è possibile generare automaticamente un'istruzione di selezione usando la procedura seguente.
6. Fare clic con il pulsante destro del mouse su una qualsiasi delle viste di database in Esplora oggetti.
7. Selezionare **Script View as** (Visualizza script come).
8. Scegliere **SELECT to** (SELEZIONA per).
9. Selezionare **Nuova finestra editor di query**.
10. È possibile creare una nuova query selezionando **Nuova query**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Database views in Azure Blockchain Workbench](blockchain-workbench-database-views.md) (Viste di database in Azure Blockchain Workbench)