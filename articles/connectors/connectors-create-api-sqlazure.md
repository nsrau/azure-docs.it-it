---
title: Connettersi a SQL Server o al database SQL di AzureConnect to SQL Server or Azure SQL Database
description: Automatizzare le attività per i database SQL in locale o nel cloud usando le app per la logica di AzureAutomate tasks for SQL databases on premises or in the cloud by using Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam; logicappspm
ms.topic: conceptual
ms.date: 11/08/2019
tags: connectors
ms.openlocfilehash: 93b63d332f00c31a352c11e483fc3ce5cb45a922
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789197"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Automatizzare i flussi di lavoro per SQL Server o database SQL di Azure usando le app per la logica di AzureAutomate workflows for SQL Server or Azure SQL Database by using Azure Logic Apps

Questo articolo illustra come accedere ai dati del database SQL da un'app per la logica mediante il connettore SQL Server. In questo modo, è possibile automatizzare attività, processi o flussi di lavoro che gestiscono i dati e le risorse SQL creando app per la logica. Il connettore SQL Server funziona sia per [SQL Server locale](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) che per il database SQL di Azure [basato su cloud.](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview)

È possibile creare app per la logica che vengono eseguite in seguito all'attivazione da eventi che si verificano nel database SQL o in altri sistemi, ad esempio Dynamics CRM Online. Le app per la logica possono anche ottenere, inserire ed eliminare dati insieme all'esecuzione di query SQL e stored procedure. Ad esempio, è possibile creare un'app per la logica che controlla automaticamente la presenza di nuovi record in Dynamics CRM Online, aggiunge elementi al database SQL per eventuali nuovi record e quindi invia avvisi tramite posta elettronica sugli elementi aggiunti.

Se non si ha familiarità con le app per la logica, leggere [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per informazioni tecniche specifiche del connettore, limitazioni e problemi noti, vedere la pagina di riferimento del [connettore SQL Server.](https://docs.microsoft.com/connectors/sql/)

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un database di SQL Server o un database SQL di [AzureAn](../sql-database/sql-database-get-started-portal.md) SQL Server [database](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) or Azure SQL database

  È necessario che le tabelle contengano dati in modo che l'app per la logica possa restituire un risultato quando si chiama un'operazione. Se si crea un database SQL di Azure, è possibile usare i database di esempio inclusi.

* Il nome del server SQL, il nome del database, il nome utente e la password. Queste credenziali sono necessarie per autorizzare la logica ad accedere al server SQL.

  * Per SQL Server, è possibile trovare queste informazioni nella stringa di connessione:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Per il database SQL di Azure, è possibile trovare queste informazioni nella stringa di connessione o nel portale di Azure all'interno delle proprietà del database SQL:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* Il gateway dati locale installato in un computer locale e una risorsa gateway dati di Azure creata nel portale di Azure per questi scenari:The [on-premises](../logic-apps/logic-apps-gateway-install.md) data gateway installed on a local computer and an [Azure data gateway resource created in the Azure portal](../logic-apps/logic-apps-gateway-connection.md) for these scenarios:

  * Le app per la logica non vengono eseguite in un ambiente del [servizio di integrazione (ISE).](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)

  * Le app per la logica *vengono* eseguite in un ambiente del servizio di integrazione, ma è necessario usare l'autenticazione di Windows per la connessione a SQL ServerSQL Server . Per questo scenario, utilizzare la versione non ISE del connettore SQL Server insieme al gateway dati perché la versione ISE non supporta l'autenticazione di Windows.

* L'app per la logica da cui accedere al database SQL. Per avviare l'app per la logica con un trigger SQL, è necessario un'[app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Aggiungere un trigger SQLAdd a SQL trigger

In App per la logica di Azure, ogni app per la logica deve essere avviata con un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che viene attivato il trigger, il motore App per la logica crea un'istanza dell'app per la logica e avvia l'esecuzione del flusso di lavoro dell'app per la logica.

1. Nel portale di Azure o in Visual Studio creare un'app per la logica vuota, che apre Progettazione app per la logica. Questo esempio usa il portale di Azure.

1. Nella finestra di progettazione, nella casella di ricerca, immettere "server sql" come filtro. Nell'elenco dei trigger selezionare il trigger SQL desiderato.

   In questo esempio viene utilizzato il trigger **When an item viene creato.**

   ![Selezionare il trigger "Quando viene creato un elemento"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Se viene richiesto di creare una connessione, [creare la connessione SQL ora](#create-connection). Se la connessione esiste, selezionare un **Nome tabella**.

   ![Selezionare la tabella desiderata](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Impostare le proprietà **Intervallo** e **Frequenza** per specificare la frequenza con cui l'app per la logica deve controllare la tabella.

   Questo trigger restituisce solo una riga dalla tabella selezionata, nient'altro. Per eseguire altre attività, aggiungere altre azioni che eseguono le attività desiderate. Ad esempio, per visualizzare i dati in questa riga, è possibile aggiungere altre azioni che creano un file che include i campi della riga restituita e quindi inviano avvisi tramite posta elettronica. Per informazioni sulle altre azioni disponibili per questo connettore, vedere la [pagina di riferimento del connettore](https://docs.microsoft.com/connectors/sql/).

1. Al termine, nella barra degli strumenti della finestra di progettazione selezionare **Salva**.

   Con questo passaggio l'app per la logica viene automaticamente abilitata e pubblicata in Azure.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Aggiungere un'azione SQLAdd a SQL action

In App per la logica di Azure, un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un passaggio del flusso di lavoro che segue un trigger o un'altra azione. In questo esempio, l'app per la logica inizia con il [trigger di ricorrenza](../connectors/connectors-native-recurrence.md)e chiama un'azione che ottiene una riga da un database SQL.

1. Nel portale di Azure o in Visual Studio aprire l'app per la logica in Progettazione app per la logica. Questo esempio usa il portale di Azure.

1. Nel trigger o nell'azione in cui si desidera aggiungere l'azione SQL selezionare **Nuovo passaggio**.

   ![Aggiungere un nuovo passaggio all'app per la logica](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Per aggiungere un'azione tra due passaggi esistenti, posizionare il puntatore del mouse sulla freccia di connessione. Selezionare il**+** segno più ( ) visualizzato e quindi selezionare **Aggiungi un'azione**.

1. In **Scegliere un'azione**immettere "server sql" nella casella di ricerca. Nell'elenco delle azioni selezionare l'azione SQL desiderata.

   In questo esempio viene utilizzata l'azione **Ottieni riga,** che ottiene un singolo record.

   ![Trovare e selezionare l'azione SQL "Ottieni riga"](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Questa azione restituisce solo una riga della tabella selezionata, nient'altro. Per visualizzare i dati in questa riga, è possibile aggiungere altre azioni che creano un file che include i campi della riga restituita e archiviano il file in un account di archiviazione cloud. Per informazioni sulle altre azioni disponibili per questo connettore, vedere la [pagina di riferimento del connettore](https://docs.microsoft.com/connectors/sql/).

1. Se viene richiesto di creare una connessione, [creare la connessione SQL ora](#create-connection). Se la connessione esiste, selezionare un **Nome tabella**e immettere l'ID **riga** per il record desiderato.

   ![Immettere il nome della tabella e l'ID di riga](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. Al termine, nella barra degli strumenti della finestra di progettazione selezionare **Salva**.

   Con questo passaggio l'app per la logica viene automaticamente abilitata e pubblicata in Azure.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Connettersi al database

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Gestire i dati in blocco

In alcuni confronti, è necessario utilizzare set di risultati così grandi che il connettore non restituisce tutti i risultati contemporaneamente o si desidera un migliore controllo sulle dimensioni e la struttura per i set di risultati. Ecco alcuni modi in cui è possibile gestire set di risultati di grandi dimensioni:Here's some ways that you can handle such large result sets:

* Per semplificare la gestione dei risultati come insiemi più piccoli, attivare *l'impaginazione*. Per ulteriori informazioni, consultate [Ottenere dati, record ed elementi in blocco utilizzando l'impaginazione.](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md)

* Creare una stored procedure che organizza i risultati nel modo desiderato.

  Quando si ottengono o si inseriscono più righe, l'app per la logica può scorrere queste righe usando un [*ciclo until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) entro questi [limiti.](../logic-apps/logic-apps-limits-and-config.md) Tuttavia, quando l'app per la logica deve usare set di record di dimensioni così grandi, ad esempio migliaia o milioni di righe, che vuoi ridurre al minimo i costi derivanti dalle chiamate al database.

  Per organizzare i risultati nel modo desiderato, è possibile creare una [*stored procedure*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) che viene eseguita nell'istanza SQL e utilizza l'istruzione SELECT - **ORDER BY.** Questa soluzione consente di controllare meglio le dimensioni e la struttura dei risultati. L'app per logica chiama la stored procedure usando l'azione **Esegui stored procedure** del connettore SQL Server.

  Per altre informazioni sulla soluzione, vedere gli articoli seguenti:For more solution details, see these articles:

  * [SQL Pagination for bulk data transfer with Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx) (Paginazione SQL per il trasferimento di dati in bulk con App per la logica)

  * [Clausola SELECT - ORDER BY](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per informazioni tecniche sui trigger, le azioni e i limiti del connettore, vedere la pagina di [riferimento del connettore.](https://docs.microsoft.com/connectors/sql/)

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori per App per la logica](../connectors/apis-list.md) di AzureLearn about other connectors for Azure Logic Apps
