---
title: Connettersi ed eseguire query su sinapsi SQL con Visual Studio e SSDT
description: Usare Visual Studio per eseguire query in un pool SQL dedicato usando le analisi delle sinapsi di Azure.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: stefanazaric
ms.reviewer: jrasnick
ms.openlocfilehash: a284e453a621860c0aebf68bef13d30e77971f88
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94686158"
---
# <a name="connect-to-synapse-sql-with-visual-studio-and-ssdt"></a>Connettersi a Synapse SQL con Visual Studio e SSDT

> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](get-started-visual-studio.md)
> * [sqlcmd](get-started-connect-sqlcmd.md) 
> * [SSMS](get-started-ssms.md)
> 
> 

Usare Visual Studio per eseguire query in un pool SQL dedicato usando le analisi delle sinapsi di Azure. Questo metodo usa l'estensione di SQL Server Data Tools (SSDT) in Visual Studio 2019. 

> [!NOTE]
> Il pool SQL senza server (anteprima) non è supportato da SSDT.

## <a name="prerequisites"></a>Prerequisiti

Per usare l'esercitazione è necessario disporre dei componenti seguenti:

* Un pool SQL dedicato esistente. Se non si dispone di uno, vedere [creare un pool SQL dedicato](../sql-data-warehouse/create-data-warehouse-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) per completare questo prerequisito.
* SSDT per Visual Studio. Se Visual Studio è già installato, probabilmente questo componente è già disponibile. Per istruzioni sull'installazione e sulle opzioni, vedere [Installare Visual Studio 2015 e SSDT per SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-install-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
* Il nome completo dell'istanza di SQL Server. Per trovare il nome del server, vedere [connettersi a un pool SQL dedicato](connect-overview.md).

## <a name="1-connect-to-a-dedicated-sql-pool"></a>1. connettersi a un pool SQL dedicato
1. Aprire Visual Studio 2019.
2. Aprire Esplora oggetti di SQL Server selezionando **Vista** > **Esplora oggetti di SQL Server**.
   
    ![Esplora oggetti di SQL Server](./media/get-started-visual-studio/open-ssdt.png)
3. Fare clic sull'icona **Aggiungi SQL Server** .
   
    ![Aggiungi SQL Server](./media/get-started-visual-studio/add-server.png)
4. Compilare i campi nella finestra Connetti al server.
   
    ![Connetti al server](./media/get-started-visual-studio/connection-dialog.png)
   
   * **Nome server**: Immettere il **nome server** identificato in precedenza.
   * **Autenticazione**: Selezionare **Autenticazione di SQL Server** o **Autenticazione integrata di Active Directory**:
   * **Nome utente** e **Password**: se è stata selezionata l'autenticazione di SQL Server, immettere il nome utente e la password.
   * Fare clic su **Connetti**.
5. Per l'esplorazione, espandere il server SQL Azure. È possibile visualizzare i database associati al server. Espandere AdventureWorksDW per visualizzare le tabelle nel database di esempio.
   
    ![Esplorare AdventureWorksDW](./media/get-started-visual-studio/explore-sample.png)

## <a name="2-run-a-sample-query"></a>2. Eseguire una query di esempio
Ora che è stata stabilita una connessione al database, è possibile scrivere una query.

1. Fare clic con il pulsante destro del mouse sul database in Esplora oggetti di SQL Server.
2. Selezionare **Nuova query**. Viene visualizzata una nuova finestra di query.
   
    ![Nuova query](./media/get-started-visual-studio/new-query2.png)
3. Copiare la query T-SQL seguente nella finestra di query:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Per eseguire la query, fare clic sulla freccia verde oppure usare il collegamento seguente: `CTRL`+`SHIFT`+`E`.
   
    ![Esegui query](./media/get-started-visual-studio/run-query.png)
5. Osservare i risultati della query. In questo esempio la tabella FactInternetSales include 60398 righe.
   
    ![Risultati query](./media/get-started-visual-studio/query-results.png)

## <a name="next-steps"></a>Passaggi successivi
Ora che è possibile connettersi ed eseguire una query, provare a [visualizzare i dati con Power BI](get-started-power-bi-professional.md).
Per configurare l'ambiente per l'autenticazione Azure Active Directory, vedere eseguire l'autenticazione [a un pool SQL dedicato](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
 