---
title: Connettersi a Synapse SQL con SQL Server Management Studio (SSMS)
description: Usare SQL Server Management Studio (SSMS) per connettersi ed eseguire query su Synapse SQL in Azure Synapse Analytics.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 4337d6bb108042a909250b3d87d13ab60357cfec
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311130"
---
# <a name="connect-to-synapse-sql-with-sql-server-management-studio-ssms"></a>Connettersi a Synapse SQL con SQL Server Management Studio (SSMS)
> [!div class="op_single_selector"]
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](../sql/get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)
> 
> 

È possibile usare [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) per connettersi a Synapse SQL ed eseguire query in Azure Synapse Analytics tramite le risorse pool SQL serverless (anteprima) o pool SQL dedicato. 

### <a name="supported-tools-for-serverless-sql-pool-preview"></a>Strumenti supportati per il pool SQL serverless (anteprima)

[Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) è completamente supportato a partire dalla versione 1.18.0. SSMS è parzialmente supportato a partire dalla versione 18.5, è possibile usarlo solo per connettersi ed eseguire query.

> [!NOTE]
> Se l'accesso AAD ha una connessione aperta per più di un'ora al momento dell'esecuzione della query, tutte le query che si basano su AAD avranno esito negativo. È inclusa l'esecuzione di query sull'archiviazione tramite il pass-through di AAD e le istruzioni che interagiscono con AAD (ad esempio CREATE EXTERNAL PROVIDER). Ciò influisce su tutti gli strumenti che mantengono aperta la connessione, ad esempio l'editor di query in SSMS e ADS. Gli strumenti che aprono una nuova connessione per eseguire query, ad esempio Synapse Studio, non sono interessati.
> Per attenuare questo problema, è possibile riavviare SSMS o connettersi e disconnettersi in ADS. .
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, verificare di aver soddisfatto i prerequisiti seguenti:  

* [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms). 
* Per il pool SQL dedicato, è necessario un data warehouse esistente. Per crearne uno, vedere [Creare un pool SQL dedicato](../quickstart-create-sql-pool-portal.md). Per il pool SQL serverless, è già stato effettuato il provisioning di uno predefinito nell'area di lavoro al momento della creazione. 
* Il nome completo dell'istanza di SQL Server. Per trovare questo nome, vedere [Connettersi a Synapse SQL](connect-overview.md).

## <a name="connect"></a>Connessione

### <a name="dedicated-sql-pool"></a>Pool SQL dedicato

Per connettersi a Synapse SQL tramite il pool SQL dedicato, seguire questa procedura: 

1. Aprire SQL Server Management Studio (SSMS). 
1. Nella finestra di dialogo **Connetti al server** compilare i campi e quindi selezionare **Connetti** : 
  
    ![Connettersi al server 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/connect-object-explorer1.png)
   
   * **Nome server** : Immettere il **nome server** identificato in precedenza.
   * **Autenticazione** :  scegliere un tipo di autenticazione, ad esempio **Autenticazione di SQL Server** o **Autenticazione integrata di Active Directory**.
   * **Nome utente** e **Password** : se è stata selezionata l'autenticazione di SQL Server, immettere il nome utente e la password.

1. Espandere Azure SQL Server in **Esplora oggetti**. È possibile visualizzare i database associati al server, come il database di esempio AdventureWorksDW. Per vedere le tabelle, espandere il database:
   
    ![Esplorare AdventureWorksDW 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/explore-tables.png)


### <a name="serverless-sql-pool-preview"></a>Pool SQL serverless (anteprima)

Per connettersi a Synapse SQL tramite il pool SQL serverless, seguire questa procedura: 

1. Aprire SQL Server Management Studio (SSMS).
1. Nella finestra di dialogo **Connetti al server** compilare i campi e quindi selezionare **Connetti** : 
   
    ![Connettersi al server 2](./media/get-started-ssms/connect-object-explorer1.png)
   
   * **Nome server** : Immettere il **nome server** identificato in precedenza.
   * **Autenticazione** : scegliere un tipo di autenticazione, ad esempio **Autenticazione di SQL Server** o **Autenticazione integrata di Active Directory** :
   * **Nome utente** e **Password** : se è stata selezionata l'autenticazione di SQL Server, immettere il nome utente e la password.
   * Selezionare **Connetti**.

4. Per l'esplorazione, espandere il server SQL Azure. È possibile visualizzare i database associati al server. Espandere *demo* per visualizzare il contenuto nel database di esempio.
   
    ![Esplorare AdventureWorksDW 2](./media/get-started-ssms/explore-tables.png)


## <a name="run-a-sample-query"></a>Eseguire una query di esempio

### <a name="dedicated-sql-pool"></a>Pool SQL dedicato

Ora che è stata stabilita una connessione al database, è possibile eseguire una query sui dati.

1. Fare clic con il pulsante destro del mouse sul database in Esplora oggetti di SQL Server.
2. Selezionare **Nuova query**. Viene visualizzata una nuova finestra di query.
   
    ![Nuova query 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/new-query.png)
3. Copiare la query T-SQL seguente nella finestra di query:
   
    ```sql
    SELECT COUNT(*) FROM dbo.FactInternetSales;
    ```
4. Eseguire la query selezionando `Execute` oppure usare il collegamento seguente: `F5`.
   
    ![Eseguire la query 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/execute-query.png)
5. Osservare i risultati della query. Nell'esempio seguente la tabella FactInternetSales include 60398 righe.
   
    ![Risultati della query 1](../sql-data-warehouse/media/sql-data-warehouse-query-ssms/results.png)

### <a name="serverless-sql-pool"></a>Pool SQL serverless

Ora che è stata stabilita una connessione al database, è possibile eseguire una query sui dati.

1. Fare clic con il pulsante destro del mouse sul database in Esplora oggetti di SQL Server.
2. Selezionare **Nuova query**. Viene visualizzata una nuova finestra di query.
   
    ![Nuova query 2](./media/get-started-ssms/new-query.png)
3. Copiare la query T-SQL seguente nella finestra di query:
   
    ```sql
    SELECT COUNT(*) FROM demo.dbo.usPopulationView
    ```
4. Eseguire la query selezionando `Execute` oppure usare il collegamento seguente: `F5`.
   
    ![Eseguire la query 2](./media/get-started-ssms/execute-query.png)
5. Osservare i risultati della query. In questo esempio, la vista usPopulationView contiene 3664512 righe.
   
    ![Risultati della query 2](./media/get-started-ssms/results.png)

## <a name="next-steps"></a>Passaggi successivi
Ora che è possibile connettersi ed eseguire una query, provare a [visualizzare i dati con Power BI](get-started-power-bi-professional.md).

Per configurare l'ambiente per l'autenticazione di Azure Active Directory, vedere [Eseguire l'autenticazione in Synapse SQL](../sql-data-warehouse/sql-data-warehouse-authentication.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

