---
title: Usare i dati di Azure Blockchain Workbench in Microsoft Excel
description: Learn how to load and view Azure Blockchain Workbench Preview SQL DB data in Microsoft Excel.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: a84858ead83782cc9b6ef1b1d7f905172600fb8a
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326057"
---
# <a name="view-azure-blockchain-workbench-data-with-microsoft-excel"></a>Visualizzare i dati di Azure Blockchain Workbench con Microsoft Excel

È possibile usare Microsoft Excel per visualizzare i dati nel database SQL di Azure Blockchain Workbench. Questo articolo descrive la procedura necessaria per:

* Connettersi al database di Blockchain Workbench da Microsoft Excel
* Esaminare le tabelle e le viste del database di Blockchain Workbench
* Caricare i dati della vista di Blockchain Workbench in Excel

## <a name="connect-to-the-blockchain-workbench-database"></a>Connettersi al database di Blockchain Workbench

Per connettersi al database di Blockchain Workbench:

1. Aprire Microsoft Excel.
2. Nella scheda **Dati** scegliere **Scarica i dati**.
3. Selezionare **Da Azure** e quindi **Dal database SQL di Azure**.

   ![Connettersi al database SQL di Azure](./media/data-excel/connect-sql-db.png)

4. Nella finestra di dialogo **Database SQL Server**:

    * Per **Server** immettere il nome del server di Blockchain Workbench.
    * Per **Database (facoltativo)** immettere il nome del database.

   ![Indicare il server di database e il database](./media/data-excel/provide-server-db.png)

5. Nella finestra di dialogo **Database SQL Server** sulla barra di spostamento selezionare **Database**. Immettere il **nome utente** e la **password**, quindi selezionare **Connetti**.

    > [!NOTE]
    > Se si usano le credenziali create durante il processo di distribuzione di Azure Blockchain Workbench, il **nome utente** è `dbadmin`. La **password** è quella creata per la distribuzione di Blockchain Workbench.
    
   ![Indicare le credenziali per accedere al database](./media/data-excel/provide-credentials.png)

## <a name="look-at-database-tables-and-views"></a>Esaminare le tabelle e le viste del database

Dopo aver eseguito la connessione al database, si apre la finestra di dialogo Strumento di spostamento di Excel. È possibile usare lo Strumento di spostamento per esaminare le tabelle e viste nel database. Le viste sono progettate per creare report e i relativi nomi hanno il prefisso **vw**.

   ![Anteprima dello Strumento di spostamento di una vista](./media/data-excel/excel-navigator.png)

## <a name="load-view-data-into-an-excel-workbook"></a>Caricare i dati della vista in una cartella di lavoro di Excel

L'esempio successivo mostra come caricare i dati da una vista in una cartella di lavoro di Excel.

1. Nella barra di scorrimento dello **Strumento di spostamento** selezionare la vista **vwContractAction**. L'anteprima di **vwContractAction** mostra tutte le azioni relative a un contratto nel database di Blockchain Workbench.
2. Selezionare **Carica** per recuperare tutti i dati nella vista e inserirli nella cartella di lavoro di Excel.

   ![Dati caricati da una vista](./media/data-excel/view-data.png)

Dopo aver caricato i dati, è possibile usare le caratteristiche di Excel per creare report personalizzati usando i metadati e i dati delle transazioni dal database di Azure Blockchain Workbench.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Database views in Azure Blockchain Workbench](database-views.md) (Viste di database in Azure Blockchain Workbench)