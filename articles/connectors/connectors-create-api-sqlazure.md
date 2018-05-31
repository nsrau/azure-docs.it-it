---
title: Connettersi a SQL Server o al database SQL di Azure - App per la logica di Azure | Microsoft Docs
description: Creare connessioni a SQL Server in locale e al database SQL di Azure nel cloud da App per la logica di Azure
services: logic-apps
documentationcenter: ''
author: ecfan
manager: cfowler
editor: ''
tags: connectors
ms.assetid: d8a319d0-e4df-40cf-88f0-29a6158c898c
ms.service: logic-apps
ms.workload: logic-apps
ms.devlang: ''
ms.tgt_pltfrm: ''
ms.topic: article
ms.date: 05/15/2018
ms.author: estfan
ms.openlocfilehash: 4917f784c07919155e006711026899ce7712fecb
ms.sourcegitcommit: d78bcecd983ca2a7473fff23371c8cfed0d89627
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/14/2018
ms.locfileid: "34164799"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Connettersi a SQL Server o al database SQL di Azure - App per la logica di Azure

Questo articolo illustra come accedere ai dati del database SQL da un'app per la logica mediante il connettore SQL Server. In questo modo, infatti, è possibile creare app per la logica che automatizzano le attività e i flussi di lavoro per la gestione dei dati. Il connettore può essere usato sia per [SQL Server in locale](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) sia per il [database SQL di Azure nel cloud](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

È possibile creare app per la logica che vengano attivate da eventi che si verificano nel database SQL o in altri sistemi, ad esempio Dynamics CRM Online. Le app per la logica possono anche ottenere, inserire o eliminare dati ed eseguire query SQL o stored procedure. È possibile, ad esempio, creare un'app per la logica che verifichi automaticamente la presenza di nuovi record in Dynamics CRM Online, che aggiunga elementi al database SQL per ogni nuovo record e che invii avvisi tramite posta elettronica.

Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. Se non si ha familiarità con le app per la logica, leggere [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Guida introduttiva: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per informazioni tecniche specifiche del connettore, vedere le <a href="https://docs.microsoft.com/connectors/sql/" target="blank">informazioni di riferimento sul connettore SQL Server</a>.

## <a name="prerequisites"></a>Prerequisiti

* L'app per la logica da cui accedere al database SQL. Per avviare l'app per la logica con un trigger SQL, è necessario un'[app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md). 

* Un [database SQL di Azure](../sql-database/sql-database-get-started-portal.md) o un [database SQL Server](https://docs.microsoft.com/sql/relational-databases/databases/create-a-database) 

  È necessario che le tabelle contengano dati in modo che l'app per la logica possa restituire un risultato quando si chiama un'operazione. Se si crea un database SQL di Azure, è possibile usare i database di esempio inclusi. 

* Il nome del server SQL, il nome del database, il nome utente e la password. Queste credenziali sono necessarie per autorizzare la logica ad accedere al server SQL. 

  * Per il database SQL di Azure, è possibile trovare queste informazioni nella stringa di connessione o nel portale di Azure all'interno delle proprietà del database SQL:

    "Server=tcp:<*yourServerName*>.database.windows.net,1433;Initial Catalog=<*yourDatabaseName*>;Persist Security Info=False;User ID=<*yourUserName*>;Password=<*yourPassword*>;MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"

  * Per SQL Server, è possibile trovare queste informazioni nella stringa di connessione: 

    "Server=<*yourServerAddress*>;Database=<*yourDatabaseName*>;User Id=<*yourUserName*>;Password=<*yourPassword*>;"

* Prima di poter connettere le app per la logica a sistemi locali, ad esempio a SQL Server, è necessario [configurare un gateway dati locale](../logic-apps/logic-apps-gateway-install.md). In questo modo, è possibile selezionare il gateway quando si crea la connessione SQL per l'app per la logica.

<a name="add-sql-trigger"></a>

## <a name="add-sql-trigger"></a>Aggiungere un trigger SQL

In App per la logica di Azure, ogni app per la logica deve essere avviata con un [trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts), che viene attivato quando si verifica un determinato evento o quando viene soddisfatta una condizione specifica. Ogni volta che il trigger viene attivato, il motore di App per la logica crea un'istanza dell'app per la logica e avvia l'esecuzione del flusso di lavoro dell'app.

1. Nel portale di Azure o in Visual Studio creare un'app per la logica vuota, che apre Progettazione app per la logica. Questo esempio usa il portale di Azure.

2. Nella casella di ricerca immettere "sql server" come filtro. Nell'elenco dei trigger selezionare il trigger SQL desiderato. 

   Per questo esempio, selezionare il trigger: **SQL Server - When an item is created** (SQL Server - Quando viene creato un elemento)

   ![Selezionare il trigger "SQL Server - When an item is created" (SQL Server - Quando viene creato un elemento)](./media/connectors-create-api-sqlazure/sql-server-trigger.png)

3. Se vengono richiesti i dettagli della connessione, [creare ora la connessione SQL](#create-connection). 
   Se la connessione esiste già, selezionare il **nome tabella** desiderato nell'elenco.

   ![Selezionare una tabella](./media/connectors-create-api-sqlazure/azure-sql-database-table.png)

4. Impostare le proprietà **Intervallo** e **Frequenza** per specificare la frequenza con cui l'app per la logica deve controllare la tabella.

   In questo esempio viene controllata solo la tabella selezionata. 
   Per effettuare operazioni più interessanti, aggiungere azioni che eseguano le attività desiderate. 
   
   Per visualizzare il nuovo elemento nella tabella, ad esempio, è possibile aggiungere altre azioni, come creare un file con i campi della tabella, e quindi inviare avvisi tramite posta elettronica. 
   Per informazioni sulle altre azioni disponibili per questo e per altri connettori, vedere [Connettori di App per la logica](../connectors/apis-list.md).

5. Al termine, nella barra degli strumenti della finestra di progettazione scegliere **Salva**. 

   Con questo passaggio l'app per la logica viene automaticamente abilitata e pubblicata in Azure. 

<a name="add-sql-action"></a>

## <a name="add-sql-action"></a>Aggiungere un'azione SQL

In App per la logica di Azure, un'[azione](../logic-apps/logic-apps-overview.md#logic-app-concepts) è un passaggio del flusso di lavoro che segue un trigger o un'altra azione. In questo esempio, l'app per la logica inizia con il [trigger di ricorrenza](../connectors/connectors-native-recurrence.md)e chiama un'azione che ottiene una riga da un database SQL.

1. Nel portale di Azure o in Visual Studio aprire l'app per la logica in Progettazione app per la logica. Questo esempio usa il portale di Azure.

2. In Progettazione app per la logica, sotto il trigger o l'azione, scegliere **Nuovo passaggio** > **Aggiungi un'azione**.

   ![Scegliere "Nuovo passaggio", "Aggiungi un'azione"](./media/connectors-create-api-sqlazure/add-action.png)
   
   Per aggiungere un'azione tra due passaggi esistenti, posizionare il puntatore del mouse sulla freccia di connessione. 
   Fare clic sul segno più (**+**) visualizzato e quindi scegliere **Aggiungi un'azione**.

2. Nella casella di ricerca immettere "sql server" come filtro. Nell'elenco delle azioni scegliere l'azione SQL desiderata. 

   Per questo esempio, selezionare questa azione in modo da ottenere un singolo record: **SQL Server - Get row** (SQL Server - Ottieni riga)

   ![Immettere "sql server", selezionare "SQL Server - Get row" (SQL Server - Ottieni riga)](./media/connectors-create-api-sqlazure/select-sql-get-row.png) 

3. Se vengono richiesti i dettagli della connessione, [creare ora la connessione SQL](#create-connection). 
   Se la connessione esiste già, selezionare un **nome tabella** e immettere l'**ID riga** relativo al record desiderato.

   ![Immettere il nome della tabella e l'ID di riga](./media/connectors-create-api-sqlazure/table-row-id.png)
   
   In questo esempio viene restituita solo una riga della tabella selezionata. 
   Per visualizzare i dati contenuti nella riga è possibile aggiungere altre azioni che consentono di creare un file con i campi della riga, da rivedere in seguito, e archiviare il file in un account di archiviazione cloud. Per informazioni sulle altre azioni disponibili in questo o in altri connettori, vedere [Connettori di App per la logica](../connectors/apis-list.md).

4. Al termine, nella barra degli strumenti della finestra di progettazione scegliere **Salva**. 

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Connettersi al database

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

[!INCLUDE [Create a connection to SQL Server or Azure SQL Database](../../includes/connectors-create-api-sqlazure.md)]

## <a name="process-data-in-bulk"></a>Elaborare i dati in bulk

Se si usano set di risultati di dimensioni tali che il connettore non riesce a restituire tutti i risultati contemporaneamente o se si vuole un controllo migliore sulle dimensioni e sulla struttura dei set di risultati, è possibile usare la *paginazione*, che consente di gestire i risultati in set più piccoli. 

[!INCLUDE [Set up pagination for results exceeding default page size](../../includes/connectors-pagination-bulk-data-transfer.md)]

### <a name="create-a-stored-procedure"></a>Creare una stored procedure

Se si ottengono o si inseriscono più righe, l'app per la logica può scorrere questi elementi usando un [*ciclo Until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) nell'ambito di questi [limiti](../logic-apps/logic-apps-limits-and-config.md). In alcuni casi, tuttavia, l'app per la logica deve interagire con set di record talmente grandi, ad esempio migliaia o milioni di righe, che si vuole ridurre al minimo i costi delle chiamate al database. 

In questi casi, è possibile creare una <a href="https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine" target="blank">*stored procedure*</a> in modo che venga eseguita nell'istanza di SQL e usi l'istruzione **SELECT - ORDER BY** per organizzare i risultati nel modo desiderato. Questa soluzione consente di controllare meglio le dimensioni e la struttura dei risultati. L'app per logica chiama la stored procedure usando l'azione **Esegui stored procedure** del connettore SQL Server. 

Per informazioni dettagliate sulla soluzione, vedere gli articoli seguenti:

* <a href="https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx" target="_blank">SQL Pagination for bulk data transfer with Logic Apps</a> (Paginazione SQL per il trasferimento di dati in bulk con App per la logica)

* <a href="https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql" target="_blank">Clausola SELECT - ORDER BY</a>

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per informazioni tecniche sui trigger, le azioni e i limiti di questo connettore, vedere le [informazioni di riferimento relative al connettore](/connectors/sql/). 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)

