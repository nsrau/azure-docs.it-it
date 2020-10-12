---
title: Eseguire query sui dati di Azure blockchain Workbench usando SQL Server Management Studio
description: Informazioni su come connettersi al database SQL di Azure Blockchain Workbench da SQL Server Management Studio.
ms.date: 11/20/2019
ms.topic: how-to
ms.service: azure-blockchain
ms.reviewer: mmercuri
ms.openlocfilehash: 16e7f9a6c36ea42e1d0a4144e680baebee5a6c21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87073100"
---
# <a name="using-azure-blockchain-workbench-data-with-sql-server-management-studio"></a>Usare i dati di Azure Blockchain Workbench con SQL Server Management Studio

Microsoft SQL Server Management Studio consente di scrivere e testare rapidamente le query nel database SQL di Azure Blockchain Workbench. Questa sezione contiene una procedura dettagliata per la connessione al database SQL di Azure blockchain Workbench dall'interno SQL Server Management Studio.

## <a name="prerequisites"></a>Prerequisiti

* Scaricare [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017).

## <a name="connecting-sql-server-management-studio-to-data-in-azure-blockchain-workbench"></a>Connessione di SQL Server Management Studio ai dati di Azure Blockchain Workbench

1. Aprire SQL Server Management Studio e selezionare **Connetti**.
2. Selezionare **Motore di database**.

    ![Motore di database](./media/data-sql-management-studio/database-engine.png)

3. Nella finestra di dialogo **Connect to Server** (Connetti al server) immettere il nome del server e le credenziali del database.

    Se si usano le credenziali create durante il processo di distribuzione di Azure Blockchain Workbench, il nome utente è **dbadmin** e la password è quella indicata durante la distribuzione.

    ![Immettere le credenziali di SQL](./media/data-sql-management-studio/sql-creds.png)

   1. SQL Server Management Studio consente di visualizzare l'elenco di database, viste di database e stored procedure nel database di Azure Blockchain Workbench.

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
