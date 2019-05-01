---
title: Connettersi a SQL Server o al database SQL di Azure - App per la logica di Azure | Microsoft Docs
description: Come accedere e gestire database SQL in locale o nel cloud tramite l'automatizzazione dei flussi di lavoro con App per la logica di Azure
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 05/15/2018
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 998fcba50636cd92b14bdbe1633c2548e84a6bfc
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64696410"
---
# <a name="connect-to-sql-server-or-azure-sql-database-from-azure-logic-apps"></a>Connettersi a SQL Server o al database SQL di Azure - App per la logica di Azure

Questo articolo illustra come accedere ai dati del database SQL da un'app per la logica mediante il connettore SQL Server. In questo modo, è possibile automatizzare le attività, i processi e i flussi di lavoro che gestiscono i dati e le risorse SQL tramite la creazione di app per la logica. Il connettore può essere usato sia per [SQL Server in locale](https://docs.microsoft.com/sql/sql-server/sql-server-technical-documentation) sia per il [database SQL di Azure nel cloud](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview). 

È possibile creare app per la logica che vengono eseguite in seguito all'attivazione da eventi che si verificano nel database SQL o in altri sistemi, ad esempio Dynamics CRM Online. Le app per la logica possono anche recuperare, inserire ed eliminare dati, oltre a eseguire query SQL e stored procedure. È possibile, ad esempio, creare un'app per la logica che verifichi automaticamente la presenza di nuovi record in Dynamics CRM Online, che aggiunga elementi al database SQL per ogni nuovo record e che invii avvisi tramite posta elettronica.

Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>. Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Avvio rapido: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per informazioni tecniche specifiche del connettore, vedere le <a href="https://docs.microsoft.com/connectors/sql/" target="blank">informazioni di riferimento sul connettore SQL Server</a>.

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

   In questo esempio selezionare questo trigger: **SQL Server - quando viene creato un elemento**

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

   Per questo esempio, selezionare questa azione, che ottiene un singolo record: **SQL Server - Ottieni riga**

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

## <a name="handle-bulk-data"></a>Gestire in blocco dei dati

In alcuni casi, potrebbe essere necessario lavorare con set di risultati così grande che il connettore non restituisce tutti i risultati nello stesso momento o si desidera maggiore controllo di dimensioni e della struttura per i set di risultati. Ecco alcuni aspetti che è possibile gestire tali set di risultati di grandi dimensioni:

* Per aiutarti a gestire i risultati come set di dimensioni ridotte, attivare *paginazione*. Per altre informazioni, vedere [ottenere dati per operazioni bulk, i record e gli elementi usando la paginazione](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Creare una stored procedure che consente di organizzare i risultati nel modo desiderato.

  Per ottenere o inserimento di più righe, l'app per la logica può scorrere queste righe utilizzando un [ *ciclo until* ](../logic-apps/logic-apps-control-flow-loops.md#until-loop) all'interno di tali [limiti](../logic-apps/logic-apps-limits-and-config.md). 
  Tuttavia, quando l'app per la logica deve funzionare con set di record di così grandi dimensioni, ad esempio, migliaia o milioni di righe, che si desidera ridurre al minimo i costi derivanti da chiamate al database.

  Per organizzare i risultati nel modo desiderato, è possibile creare un [ *stored procedure* ](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) che viene eseguito nell'istanza di SQL e si usa il **SELECT - ORDER BY** istruzione. 
  Questa soluzione consente di controllare meglio le dimensioni e la struttura dei risultati. 
  L'app per logica chiama la stored procedure usando l'azione **Esegui stored procedure** del connettore SQL Server.

  Per informazioni dettagliate sulla soluzione, vedere gli articoli seguenti:

  * [SQL Pagination for bulk data transfer with Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx) (Paginazione SQL per il trasferimento di dati in bulk con App per la logica)

  * [Clausola SELECT - ORDER BY](https://docs.microsoft.com/sql/t-sql/queries/select-order-by-clause-transact-sql)

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per informazioni tecniche sui trigger, le azioni e i limiti di questo connettore, vedere le [informazioni di riferimento relative al connettore](/connectors/sql/). 

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori di App per la logica](../connectors/apis-list.md)

