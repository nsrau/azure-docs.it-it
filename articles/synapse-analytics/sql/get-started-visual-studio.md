---
title: Connettersi ed eseguire query su sinapsi SQL con Visual Studio e SSDT
description: Usare Visual Studio per eseguire query nel pool SQL usando Azure sinapsi Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 5e725df5c875f23d82da1fda19bcdf28db548fc8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428563"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Connettersi a sinapsi SQL con Visual Studio e SSDT
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Usare Visual Studio per eseguire query nel pool SQL usando Azure sinapsi Analytics. Questo metodo usa l'estensione SQL Server Data Tools (SSDT) in Visual Studio 2019. 

### <a name="supported-tools-for-sql-on-demand-preview"></a>Strumenti supportati per SQL su richiesta (anteprima)

Visual Studio non è attualmente supportato per SQL su richiesta (anteprima). Tuttavia, Azure Data studi (anteprima) o è uno strumento completamente supportato. SQL Server Management Studio è parzialmente supportato dalla versione 18,4 e presenta funzionalità limitate, ad esempio la connessione e l'esecuzione di query.

## <a name="prerequisites"></a>Prerequisiti
Per usare questa esercitazione, è necessario disporre dei componenti seguenti:

* Un pool SQL esistente. Se non si dispone di uno, vedere [creare un pool SQL](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) per completare questo prerequisito.
* SSDT per Visual Studio. Se si dispone di Visual Studio, probabilmente si dispone già di questo componente. Per istruzioni sull'installazione e sulle opzioni, vedere [Installazione di Visual Studio e SSDT](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Il nome completo dell'istanza di SQL Server. Per trovarlo, vedere [connettersi al pool SQL](connect-overview.md).

## <a name="1-connect-to-sql-pool"></a>1. connessione al pool SQL
1. Aprire Visual Studio 2019.
2. Aprire il Esplora oggetti di SQL Server. A tale scopo, selezionare **Visualizza** > **Esplora oggetti di SQL Server**.
   
    ![Esplora oggetti di SQL Server](./media/get-started-visual-studio/open-ssdt.png)
3. Fare clic sull'icona **Aggiungi SQL Server** .
   
    ![Aggiungi SQL Server](./media/get-started-visual-studio/add-server.png)
4. Compilare i campi nella finestra Connetti al server.
   
    ![Connetti al server](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Nome server**: Immettere il **nome server** identificato in precedenza.
   * **Autenticazione**: selezionare **l'autenticazione SQL Server** o **Active Directory l'autenticazione integrata**:
   * **Nome utente** e **Password**: se è stata selezionata l'autenticazione di SQL Server, immettere il nome utente e la password.
   * Fare clic su **Connetti**.
5. Per l'esplorazione, espandere il server SQL Azure. È possibile visualizzare i database associati al server. Espandere AdventureWorksDW per visualizzare le tabelle nel database di esempio.
   
    ![Esplorare AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. eseguire una query di esempio
Ora che è stata stabilita una connessione al database, si scriverà una query.

1. Fare clic con il pulsante destro del mouse sul database in Esplora oggetti di SQL Server.
2. Selezionare **Nuova query**. Viene visualizzata una nuova finestra di query.
   
    ![Nuova query](./media/get-started-visual-studio/new-query2.png)
3. Copiare la query T-SQL seguente nella finestra di query:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Consente di eseguire la query. A questo scopo, fare clic sulla freccia verde oppure usare la combinazione di tasti seguente: `CTRL`+`SHIFT`+`E`.
   
    ![Esegui query](./media/get-started-visual-studio/run-query.png)
5. Osservare i risultati della query. In questo esempio la tabella FactInternetSales include 60398 righe.
   
    ![Risultati query](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Passaggi successivi
Ora che è possibile connettersi ed eseguire una query, provare a [visualizzare i dati con Power BI](get-started-power-bi-professional.md).
Per configurare l'ambiente per l'autenticazione Azure Active Directory, vedere eseguire l'autenticazione [al pool SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
 