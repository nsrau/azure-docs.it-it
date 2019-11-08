---
title: Connettersi a SQL Server o al database SQL di Azure-app per la logica di Azure
description: Automatizzare le attività per i database SQL in locale o nel cloud usando le app per la logica di Azure
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam; LADocs
manager: carmonm
ms.topic: conceptual
tags: connectors
ms.date: 11/08/2019
ms.openlocfilehash: a6367e5897e9bd548550b099c0bd2e6186845d6d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826313"
---
# <a name="automate-workflows-for-sql-server-or-azure-sql-database-by-using-azure-logic-apps"></a>Automatizzare i flussi di lavoro per SQL Server o il database SQL di Azure usando app per la logica di Azure

Questo articolo illustra come accedere ai dati del database SQL da un'app per la logica mediante il connettore SQL Server. In questo modo, è possibile automatizzare attività, processi o flussi di lavoro che gestiscono i dati e le risorse SQL creando app per la logica. Il connettore SQL Server funziona sia per la [SQL Server locale](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) sia per il [database SQL di Azure basato sul cloud](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).

È possibile creare app per la logica che vengono eseguite in seguito all'attivazione da eventi che si verificano nel database SQL o in altri sistemi, ad esempio Dynamics CRM Online. Le app per la logica possono anche ottenere, inserire ed eliminare i dati insieme all'esecuzione di query e stored procedure SQL. È ad esempio possibile compilare un'app per la logica che controlla automaticamente la presenza di nuovi record in Dynamics CRM Online, aggiunge elementi al database SQL per tutti i nuovi record e quindi invia avvisi di posta elettronica sugli elementi aggiunti.

Se non si ha familiarità con le app per la logica, leggere [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per informazioni tecniche specifiche del connettore, limitazioni e problemi noti, vedere la [pagina di riferimento del connettore SQL Server](https://docs.microsoft.com/connectors/sql/).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* [Database SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) o [database SQL di Azure](../sql-database/sql-database-get-started-portal.md)

  È necessario che le tabelle contengano dati in modo che l'app per la logica possa restituire un risultato quando si chiama un'operazione. Se si crea un database SQL di Azure, è possibile usare i database di esempio inclusi.

* Il nome del server SQL, il nome del database, il nome utente e la password. Queste credenziali sono necessarie per autorizzare la logica ad accedere al server SQL.

  * Per SQL Server, è possibile trovare queste informazioni nella stringa di connessione:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Per il database SQL di Azure, è possibile trovare queste informazioni nella stringa di connessione o nel portale di Azure all'interno delle proprietà del database SQL:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

* Il [gateway dati](../logic-apps/logic-apps-gateway-install.md) locale installato in un computer locale e una risorsa del [gateway dati di Azure creata nel portale di Azure](../logic-apps/logic-apps-gateway-connection.md) per questi scenari:

  * Le app per la logica non vengono eseguite in un [ambiente Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md).

  * Le *app per la logica vengono eseguite* in un ambiente del servizio di integrazione, ma è necessario usare l'autenticazione di Windows per la connessione SQL Server. Per questo scenario, usare la versione non ISE del connettore SQL Server insieme al gateway dati perché la versione di ISE non supporta l'autenticazione di Windows.

* L'app per la logica da cui accedere al database SQL. Per avviare l'app per la logica con un trigger SQL, è necessario un'[app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Aggiungere un trigger SQL

In App per la logica di Azure, ogni app per la logica deve essere avviata con un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, il motore app per la logica crea un'istanza dell'app per la logica e avvia l'esecuzione del flusso di lavoro dell'app per la logica.

1. Nel portale di Azure o in Visual Studio creare un'app per la logica vuota, che apre Progettazione app per la logica. Questo esempio usa il portale di Azure.

1. Nella casella di ricerca della finestra di progettazione immettere "SQL Server" come filtro. Nell'elenco dei trigger selezionare il trigger SQL desiderato.

   In questo esempio viene usato il trigger **quando viene creato un elemento** .

   ![Selezionare il trigger "quando viene creato un elemento"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Se viene richiesto di creare una connessione, [creare ora la connessione SQL](#create-connection). Se la connessione esiste, selezionare un **nome di tabella**.

   ![Selezionare la tabella desiderata](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

1. Impostare le proprietà **Intervallo** e **Frequenza** per specificare la frequenza con cui l'app per la logica deve controllare la tabella.

   Questo trigger restituisce solo una riga della tabella selezionata, nient'altro. Per eseguire altre attività, aggiungere altre azioni che eseguono le attività desiderate. Ad esempio, per visualizzare i dati in questa riga, è possibile aggiungere altre azioni per la creazione di un file che include i campi dalla riga restituita, quindi inviare avvisi di posta elettronica. Per informazioni sulle altre azioni disponibili per questo connettore, vedere la [pagina di riferimento del connettore](https://docs.microsoft.com/connectors/sql/).

1. Al termine, fare clic su **Salva**nella barra degli strumenti della finestra di progettazione.

   Con questo passaggio l'app per la logica viene automaticamente abilitata e pubblicata in Azure.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Aggiungi un'azione SQL

In App per la logica di Azure, un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un passaggio del flusso di lavoro che segue un trigger o un'altra azione. In questo esempio, l'app per la logica inizia con il [trigger di ricorrenza](../connectors/connectors-native-recurrence.md)e chiama un'azione che ottiene una riga da un database SQL.

1. Nel portale di Azure o in Visual Studio aprire l'app per la logica in Progettazione app per la logica. Questo esempio usa il portale di Azure.

1. Nel trigger o nell'azione in cui si vuole aggiungere l'azione SQL selezionare **nuovo passaggio**.

   ![Aggiungere un nuovo passaggio all'app per la logica](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   Per aggiungere un'azione tra due passaggi esistenti, posizionare il puntatore del mouse sulla freccia di connessione. Selezionare il segno più ( **+** ) visualizzato, quindi selezionare **Aggiungi un'azione**.

1. In **scegliere un'azione**, nella casella di ricerca, immettere "SQL Server" come filtro. Nell'elenco azioni selezionare l'azione SQL desiderata.

   Questo esempio usa l'azione **Ottieni riga** , che ottiene un singolo record.

   ![Trovare e selezionare l'azione "Ottieni riga" di SQL](./media/connectors-create-api-sqlazure/find-select-sql-get-row-action.png)

   Questa azione restituisce solo una riga della tabella selezionata, nient'altro. Per visualizzare i dati in questa riga, è possibile aggiungere altre azioni che consentono di creare un file che include i campi dalla riga restituita e archiviare il file in un account di archiviazione cloud. Per informazioni sulle altre azioni disponibili per questo connettore, vedere la [pagina di riferimento del connettore](https://docs.microsoft.com/connectors/sql/).

1. Se viene richiesto di creare una connessione, [creare ora la connessione SQL](#create-connection). Se la connessione esiste, selezionare un **nome di tabella**e immettere l' **ID di riga** per il record desiderato.

   ![Immettere il nome della tabella e l'ID di riga](./media/connectors-create-api-sqlazure/specify-table-row-id-property-value.png)

1. Al termine, fare clic su **Salva**nella barra degli strumenti della finestra di progettazione.

   Con questo passaggio l'app per la logica viene automaticamente abilitata e pubblicata in Azure.

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Connettersi al database

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="handle-bulk-data"></a>Gestire i dati in blocco

In alcuni casi, è necessario utilizzare i set di risultati in modo tale che il connettore non restituisca tutti i risultati contemporaneamente oppure si desidera un controllo migliore sulle dimensioni e sulla struttura per i set di risultati. Ecco alcuni modi in cui è possibile gestire set di risultati di grandi dimensioni:

* Per semplificare la gestione dei risultati come set più piccoli, attivare l' *impaginazione*. Per ulteriori informazioni, vedere [Get bulk data, Records and items by using impaginazione](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Creare una stored procedure per organizzare i risultati nel modo desiderato.

  Quando si recuperano o si inseriscono più righe, l'app per la logica può scorrere queste righe usando un [*ciclo until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) entro questi [limiti](../logic-apps/logic-apps-limits-and-config.md). Tuttavia, quando l'app per la logica deve usare set di record di grandi dimensioni, ad esempio migliaia o milioni di righe, si vuole ridurre al minimo i costi derivanti dalle chiamate al database.

  Per organizzare i risultati nel modo desiderato, è possibile creare un [*stored procedure*](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) eseguito nell'istanza di SQL e utilizza l'istruzione **SELECT-ORDER BY** . Questa soluzione consente di controllare meglio le dimensioni e la struttura dei risultati. L'app per logica chiama la stored procedure usando l'azione **Esegui stored procedure** del connettore SQL Server.

  Per altri dettagli sulla soluzione, vedere questi articoli:

  * [SQL Pagination for bulk data transfer with Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx) (Paginazione SQL per il trasferimento di dati in bulk con App per la logica)

  * [Clausola SELECT - ORDER BY](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per informazioni tecniche sui trigger, le azioni e i limiti di questo connettore, vedere la [pagina di riferimento del connettore](https://docs.microsoft.com/connectors/sql/).

## <a name="next-steps"></a>Passaggi successivi

* Informazioni sugli altri [connettori per app per la logica di Azure](../connectors/apis-list.md)
