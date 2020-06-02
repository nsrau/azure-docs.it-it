---
title: Connettersi a SQL Server o al database SQL di Azure
description: Automatizzare le attività per i database SQL in locale o nel cloud usando App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 05/12/2020
tags: connectors
ms.openlocfilehash: c32e17aaf83c233ad77bbbf607c30cc526253352
ms.sourcegitcommit: 90d2d95f2ae972046b1cb13d9956d6668756a02e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2020
ms.locfileid: "83402592"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Automatizzare i flussi di lavoro per SQL Server o il database SQL di Azure usando App per la logica di Azure

Questo articolo illustra come accedere ai dati del database SQL da un'app per la logica mediante il connettore SQL Server. In questo modo, è possibile automatizzare le attività, i processi o i flussi di lavoro che gestiscono i dati e le risorse SQL tramite la creazione di app per la logica. Il connettore SQL Server può essere usato sia per [SQL Server in locale](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) sia per il [database SQL di Azure nel cloud](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).

È possibile creare app per la logica che vengono eseguite in seguito all'attivazione da eventi che si verificano nel database SQL o in altri sistemi, ad esempio Dynamics CRM Online. Le app per la logica possono anche recuperare, inserire ed eliminare dati, oltre a eseguire query SQL e stored procedure. È possibile, ad esempio, creare un'app per la logica che verifichi automaticamente la presenza di nuovi record in Dynamics CRM Online, che aggiunga elementi al database SQL per ogni nuovo record e che invii avvisi tramite posta elettronica sugli elementi aggiunti.

Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Avvio rapido: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per informazioni tecniche specifiche del connettore, limitazioni e problemi noti, vedere la [pagina di riferimento del connettore SQL Server](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un [database SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) o un [database SQL di Azure](../sql-database/sql-database-get-started-portal.md).

  È necessario che le tabelle contengano dati in modo che l'app per la logica possa restituire un risultato quando si chiama un'operazione. Se si crea un database SQL di Azure, è possibile usare i database di esempio inclusi.

* Il nome del server SQL, il nome del database, il nome utente e la password. Queste credenziali sono necessarie per autorizzare la logica ad accedere al server SQL.

  * Per SQL Server, è possibile trovare queste informazioni nella stringa di connessione:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Per il database SQL di Azure, è possibile trovare queste informazioni nella stringa di connessione o nel portale di Azure all'interno delle proprietà del database SQL:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* Il [gateway dati locale](../logic-apps/logic-apps-gateway-install.md) installato in un computer locale e una [risorsa gateway dati di Azure creata nel portale di Azure](../logic-apps/logic-apps-gateway-connection.md) per questi scenari:

  * Le app per la logica non vengono eseguite in un [ambiente del servizio di integrazione (ISE, Integration Service Environment)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

  * Le app per la logica *vengono eseguite* in un ambiente del servizio di integrazione, ma è necessario usare l'autenticazione di Windows per la connessione SQL Server. Per questo scenario, usare la versione non ISE del connettore SQL Server insieme al gateway dati perché la versione ISE non supporta l'autenticazione di Windows.

* L'app per la logica da cui accedere al database SQL. Per avviare l'app per la logica con un trigger SQL, è necessario un'[app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Aggiungere un trigger SQL

In App per la logica di Azure, ogni app per la logica deve essere avviata con un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, il motore di App per la logica crea un'istanza dell'app per la logica e avvia l'esecuzione del flusso di lavoro dell'app per la logica.

1. Nel portale di Azure o in Visual Studio creare un'app per la logica vuota, che apre Progettazione app per la logica. Questo esempio usa il portale di Azure.

1. Nella casella di ricerca della finestra di progettazione immettere "sql server" come filtro. Nell'elenco dei trigger selezionare il trigger SQL desiderato.

   Questo esempio usa il trigger **Quando viene creato un elemento**.

   ![Selezionare il trigger "Quando viene creato un elemento"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Se viene richiesto di creare una connessione, [creare ora la connessione SQL](#create-connection). Se la connessione esiste, selezionare un **nome di tabella**.

   ![Selezionare la tabella desiderata](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Impostare le proprietà **Intervallo** e **Frequenza** per specificare la frequenza con cui l'app per la logica deve controllare la tabella.

   Questo trigger restituisce solo una riga della tabella selezionata. Per eseguire altre attività, aggiungere altre azioni che eseguono le attività desiderate. Ad esempio, per visualizzare i dati in questa riga, è possibile aggiungere altre azioni per la creazione di un file che include i campi dalla riga restituita e quindi inviare avvisi di posta elettronica. Per informazioni sulle altre azioni disponibili per questo connettore, vedere la [pagina di riferimento del connettore](https://docs.microsoft.com/connectors/sql/).

1. Al termine, nella barra degli strumenti della finestra di progettazione selezionare **Salva**.

   Con questo passaggio l'app per la logica viene automaticamente abilitata e pubblicata in Azure.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Aggiungere un'azione SQL

In App per la logica di Azure, un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un passaggio del flusso di lavoro che segue un trigger o un'altra azione. In questo esempio, l'app per la logica inizia con il [trigger di ricorrenza](../connectors/connectors-native-recurrence.md)e chiama un'azione che ottiene una riga da un database SQL.

1. Nel portale di Azure o in Visual Studio aprire l'app per la logica in Progettazione app per la logica. Questo esempio usa il portale di Azure.

1. Nel trigger o nell'azione in cui si vuole aggiungere l'azione SQL selezionare **Nuovo passaggio**.

   ![Aggiungere un nuovo passaggio all'app per la logica](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Per aggiungere un'azione tra due passaggi esistenti, posizionare il puntatore del mouse sulla freccia di connessione. Selezionare il segno più ( **+** ) visualizzato e quindi **Aggiungi un'azione**.

1. Nella casella di ricerca di **Scegliere un'azione** immettere "sql server" come filtro. Nell'elenco delle azioni scegliere l'azione SQL desiderata.

   Questo esempio usa l'azione **Ottieni riga**, che ottiene un singolo record.

   ![Trovare e selezionare l'azione "Ottieni riga" di SQL](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Questa azione restituisce solo una riga della tabella selezionata. Per visualizzare i dati contenuti nella riga è possibile aggiungere altre azioni che consentono di creare un file che include i campi della riga restituita e archiviare il file in un account di archiviazione cloud. Per informazioni sulle altre azioni disponibili per questo connettore, vedere la [pagina di riferimento del connettore](https://docs.microsoft.com/connectors/sql/).

1. Se viene richiesto di creare una connessione, [creare ora la connessione SQL](#create-connection). Se la connessione esiste già, selezionare un **nome tabella** e immettere l'**ID riga** relativo al record desiderato.

   ![Immettere il nome della tabella e l'ID di riga](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. Al termine, nella barra degli strumenti della finestra di progettazione selezionare **Salva**.

   Con questo passaggio l'app per la logica viene automaticamente abilitata e pubblicata in Azure.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Connettersi al database

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Gestire i dati in blocco

A volte, è necessario usare set di risultati di dimensioni tali che il connettore non riesce a restituire tutti i risultati contemporaneamente oppure si vuole un controllo migliore sulle dimensioni e sulla struttura dei set di risultati. Ecco alcuni modi in cui è possibile gestire set di risultati di grandi dimensioni:

* Per gestire i risultati come set più piccoli, attivare la *paginazione*. Per altre informazioni, vedere [Ottenere dati in blocco, record ed elementi usando la paginazione](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Creare una stored procedure per organizzare i risultati nel modo desiderato.

  Se si ottengono o si inseriscono più righe, l'app per la logica può scorrere queste righe usando un [*ciclo Until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) nell'ambito di questi [limiti](../logic-apps/logic-apps-limits-and-config.md). In alcuni casi, tuttavia, l'app per la logica deve interagire con set di record talmente grandi, ad esempio migliaia o milioni di righe, che si vuole ridurre al minimo i costi risultanti dalle chiamate al database.

  Per organizzare i risultati nel modo desiderato, è possibile creare una [*stored procedure*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) che viene eseguita nell'istanza di SQL e usa l'istruzione **SELECT - ORDER BY**. Questa soluzione consente di controllare meglio le dimensioni e la struttura dei risultati. L'app per logica chiama la stored procedure usando l'azione **Esegui stored procedure** del connettore SQL Server.

  Per altre informazioni dettagliate sulla soluzione, vedere questi articoli:

  * [SQL Pagination for bulk data transfer with Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx) (Paginazione SQL per il trasferimento di dati in bulk con App per la logica)

  * [Clausola SELECT - ORDER BY](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

### <a name="handle-dynamic-bulk-data"></a>Gestire i dati in blocco dinamici

In alcuni casi, quando si effettua una chiamata a una stored procedure nel connettore SQL Server, l'output restituito è dinamico. In questo scenario, seguire questi passaggi:

1. Aprire **Progettazione app per la logica**.
1. Eseguire un'esecuzione dei test dell'app per la logica per visualizzare il formato di output. Copiare l'output di esempio.
1. Nella finestra di progettazione, in corrispondenza dell'azione in cui è stata effettuata la chiamata alla stored procedure, selezionare **Nuovo passaggio**.
1. In **Scegliere un'azione** cercare e selezionare l'azione [**Analizza JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action).
1. Nell'azione **Analizza JSON** selezionare **Usare il payload di esempio per generare lo schema**.
1. Nella finestra **Immettere o incollare un payload JSON di esempio** incollare l'output di esempio e quindi selezionare **Chiudi**.
1. Se viene restituito un errore che indica che App per la logica non può generare uno schema, verificare che la sintassi dell'output di esempio sia formattata correttamente. Se non si riesce ancora a generare lo schema, immetterne manualmente uno nella casella **Schema**.
1. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.
1. Per accedere alle proprietà del contenuto JSON, usare i token di dati visualizzati nell'elenco del contenuto dinamico per l'azione [**Analizza JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action).

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per informazioni tecniche sui trigger, le azioni e i limiti di questo connettore, vedere la [pagina di riferimento del connettore](https://docs.microsoft.com/connectors/sql/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori per App per la logica di Azure](../connectors/apis-list.md)
