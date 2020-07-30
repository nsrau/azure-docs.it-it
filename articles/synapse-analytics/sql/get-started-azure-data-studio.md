---
title: Connettersi a Synapse SQL con Azure Data Studio (anteprima)
description: Usare Azure Data Studio per connettersi ed eseguire query su Synapse SQL in Azure Synapse Analytics.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 78807db19b413bb642e8dcf61e480d6d954b6178
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87059565"
---
# <a name="connect-to-synapse-sql-with-azure-data-studio-preview"></a>Connettersi a Synapse SQL con Azure Data Studio (anteprima)

> [!div class="op_single_selector"]
>
> * [Azure Data Studio](get-started-azure-data-studio.md)
> * [Power BI](get-started-power-bi-professional.md)
> * [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
> * [sqlcmd](get-started-connect-sqlcmd.md)
> * [SSMS](get-started-ssms.md)

Usare [Azure Data Studio (anteprima)](/sql/azure-data-studio/download-azure-data-studio?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) per connettersi ed eseguire query su Synapse SQL in Azure Synapse Analytics. 

## <a name="connect"></a>Connessione

Per connettersi a Synapse SQL, aprire Azure Data Studio e selezionare **Nuova connessione**.

![Aprire Azure Data Studio](./media/get-started-azure-data-studio/1-start.png)

Scegliere **Microsoft SQL Server** per **Tipo di connessione**.

Per la connessione sono necessari i parametri seguenti:

* **Server:** server nel formato `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net
* **Database:** Nome database

> [!NOTE]
> Se si vuole usare **SQL su richiesta (anteprima)** , l'URL dovrebbe essere simile al seguente:
>
> - `<Azure Synapse workspace name>`-ondemand.sql.azuresynapse.net.
>
> Se si vuole usare il **pool SQL**, l'URL dovrebbe essere simile al seguente:
>
> - `<Azure Synapse workspace name>`.sql.azuresynapse.net

Scegliere **Autenticazione di Windows**, **Azure Active Directory** o **Account di accesso SQL** per **Tipo di autenticazione**.

Per usare **Account di accesso SQL** come tipo di autenticazione, aggiungere i parametri nome utente/password:

* **Utente:** utente del server nel formato `<User>`
* **Password:** password associata all'utente

Per usare Azure Active Directory, è necessario scegliere il tipo di autenticazione necessario.

![Autenticazione AAD](./media/get-started-azure-data-studio/3-aad-auth.png)

Lo screenshot seguente mostra l'opzione **Dettagli connessione** per **Autenticazione di Windows**:

![Autenticazione di Windows](./media/get-started-azure-data-studio/3-windows-auth.png)

Lo screenshot seguente mostra l'opzione **Dettagli connessione** per **Account di accesso SQL**:

![Account di accesso SQL](./media/get-started-azure-data-studio/2-database-details.png)

Dopo aver effettuato l'accesso, verrà visualizzato un dashboard simile a questo: ![Dashboard](./media/get-started-azure-data-studio/4-dashboard.png)

## <a name="query"></a>Query

Dopo aver stabilito la connessione, è possibile eseguire possibile eseguire query su Synapse SQL usando le istruzioni [Transact-SQL](/sql/t-sql/language-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (T-SQL) supportate nell'istanza. Per iniziare, selezionare **Nuova query** nella vista del dashboard.

![Nuova query](./media/get-started-azure-data-studio/5-new-query.png)

È ad esempio possibile usare l'istruzione Transact-SQL seguente per [eseguire query su file Parquet](query-parquet-files.md) con SQL su richiesta:

```sql
SELECT COUNT(*)
FROM  
OPENROWSET(
    BULK 'https://azureopendatastorage.blob.core.windows.net/censusdatacontainer/release/us_population_county/year=20*/*.parquet',
    FORMAT='PARQUET'
)
```
## <a name="next-steps"></a>Passaggi successivi 
Esplorare altri modi per connettersi a Synapse SQL: 

- [SSMS](get-started-ssms.md)
- [Power BI](get-started-power-bi-professional.md)
- [Visual Studio](../sql-data-warehouse/sql-data-warehouse-query-visual-studio.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
- [sqlcmd](get-started-connect-sqlcmd.md)
 
