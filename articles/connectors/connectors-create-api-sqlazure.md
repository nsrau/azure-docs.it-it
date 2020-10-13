---
title: Connettersi a SQL Server, al database SQL di Azure o ad Azure SQL Istanza gestita
description: Automatizzare le attività per i database SQL in locale o nel cloud usando App per la logica di Azure
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, jonfan, logicappspm
ms.topic: conceptual
ms.date: 06/06/2020
tags: connectors
ms.openlocfilehash: a50a171536d7f81de42da415960398d31ec64827
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326780"
---
# <a name="automate-workflows-for-a-sql-database-by-using-azure-logic-apps"></a>Automatizzare i flussi di lavoro per un database SQL usando app per la logica di Azure

Questo articolo illustra come accedere ai dati del database SQL da un'app per la logica mediante il connettore SQL Server. In questo modo, è possibile automatizzare le attività, i processi o i flussi di lavoro che gestiscono i dati e le risorse SQL tramite la creazione di app per la logica. Il connettore SQL Server funziona per [SQL Server](/sql/sql-server/sql-server-technical-documentation) , nonché per il [database SQL di azure](../azure-sql/database/sql-database-paas-overview.md) e il [istanza gestita SQL di Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md).

È possibile creare app per la logica che vengono eseguite in seguito all'attivazione da eventi che si verificano nel database SQL o in altri sistemi, ad esempio Dynamics CRM Online. Le app per la logica possono anche recuperare, inserire ed eliminare dati, oltre a eseguire query SQL e stored procedure. È possibile, ad esempio, creare un'app per la logica che verifichi automaticamente la presenza di nuovi record in Dynamics CRM Online, che aggiunga elementi al database SQL per ogni nuovo record e che invii avvisi tramite posta elettronica sugli elementi aggiunti.

Se non si ha familiarità con le app per la logica, consultare [Informazioni su App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Avvio rapido: Creare la prima app per la logica](../logic-apps/quickstart-create-first-logic-app-workflow.md). Per informazioni tecniche specifiche del connettore, limitazioni e problemi noti, vedere la [pagina di riferimento del connettore SQL Server](/connectors/sql/).

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Un [database SQL Server](/sql/relational-databases/databases/create-a-database), un [database SQL di azure](../azure-sql/database/single-database-create-quickstart.md)o un [istanza gestita SQL di Azure](../azure-sql/managed-instance/instance-create-quickstart.md).

  È necessario che le tabelle contengano dati in modo che l'app per la logica possa restituire un risultato quando si chiama un'operazione. Se si usa il database SQL di Azure, è possibile usare i database di esempio, inclusi.

* Il nome del server SQL, il nome del database, il nome utente e la password. Queste credenziali sono necessarie per autorizzare la logica ad accedere al server SQL.

  * Per SQL Server locali, è possibile trovare questi dettagli nella stringa di connessione:

    `Server={your-server-address};Database={your-database-name};User Id={your-user-name};Password={your-password};`

  * Per il database SQL di Azure, è possibile trovare questi dettagli nella stringa di connessione.
  
    Ad esempio, per trovare questa stringa nella portale di Azure, aprire il database. Scegliere **stringhe di connessione** o **Proprietà**dal menu database:

    `Server=tcp:{your-server-name}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

<a name="multi-tenant-or-ise"></a>

* A seconda che le app per la logica vengano eseguite in Azure globale multi-tenant o in un [ambiente di Integration Services (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), di seguito sono riportati altri requisiti per la connessione a SQL Server locali:

  * Per le app per la logica in Azure multi-tenant globali che si connettono a SQL Server locali, è necessario che il [gateway dati](../logic-apps/logic-apps-gateway-install.md) locale sia installato in un computer locale e [in una risorsa del gateway dati già creata in Azure](../logic-apps/logic-apps-gateway-connection.md).

  * Per le app per la logica in un ISE che si connettono a SQL Server locali e usano l'autenticazione di Windows, il connettore SQL Server con versione ISE non supporta l'autenticazione di Windows. Quindi, è ancora necessario usare il gateway dati e il connettore SQL Server non ISE. Per altri tipi di autenticazione, non è necessario usare il gateway dati e può usare il connettore con controllo delle versioni di ISE.

* L'app per la logica da cui accedere al database SQL. Per avviare l'app per la logica con un trigger SQL, è necessario un'[app per la logica vuota](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-connection"></a>

## <a name="connect-to-your-database"></a>Connettersi al database

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

A questo punto, continuare con i passaggi seguenti:

* [Connettersi a un database SQL di Azure basato sul cloud o Istanza gestita](#connect-azure-sql-db)
* [Connettersi a SQL Server locali](#connect-sql-server)

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database-or-managed-instance"></a>Connettersi al database SQL di Azure o Istanza gestita

La prima volta che si aggiunge un [trigger SQL](#add-sql-trigger) o un' [azione SQL](#add-sql-action)e in precedenza non è stata creata una connessione al database, viene richiesto di completare i passaggi seguenti:

1. Per **tipo di autenticazione**selezionare l'autenticazione richiesta e abilitata nel database nel database SQL di Azure o in azure SQL istanza gestita:

   | Authentication | Descrizione |
   |----------------|-------------|
   | [**Azure AD integrata**](../azure-sql/database/authentication-aad-overview.md) | : Supporta sia il connettore SQL Server non ISE che ISE. <p><p>-Richiede un'identità valida in Azure Active Directory (Azure AD) che può accedere al database. <p>Per altre informazioni, vedere gli argomenti seguenti: <p>- [Panoramica della sicurezza di Azure SQL-autenticazione](../azure-sql/database/security-overview.md#authentication) <br>- [Autorizzare l'accesso al database a SQL di Azure: autenticazione e autorizzazione](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) <br>- [Azure SQL-Azure AD autenticazione integrata](../azure-sql/database/authentication-aad-overview.md) |
   | [**Autenticazione di SQL Server**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | : Supporta sia il connettore SQL Server non ISE che ISE. <p><p>-Richiede un nome utente e una password complesse validi che vengono creati e archiviati nel database. <p>Per altre informazioni, vedere gli argomenti seguenti: <p>- [Panoramica della sicurezza di Azure SQL-autenticazione](../azure-sql/database/security-overview.md#authentication) <br>- [Autorizzare l'accesso al database a SQL di Azure: autenticazione e autorizzazione](../azure-sql/database/logins-create-manage.md#authentication-and-authorization) |
   |||

   Questo esempio continua con **Azure ad Integrated**:

   ![Screenshot che mostra la finestra di connessione "SQL Server" con l'elenco "tipo di autenticazione" aperto e "Azure AD Integrated" selezionato.](./media/connectors-create-api-sqlazure/select-azure-ad-authentication.png)

1. Dopo aver selezionato **Azure ad Integrated**, selezionare **Sign in (accedi**). A seconda che si usi il database SQL di Azure o il Istanza gestita SQL di Azure, selezionare le credenziali utente per l'autenticazione.

1. Selezionare i valori seguenti per il database:

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Nome server** | Sì | Indirizzo per SQL Server, ad esempio `Fabrikam-Azure-SQL.database.windows.net` |
   | **Nome database** | Sì | Nome del database SQL, ad esempio `Fabrikam-Azure-SQL-DB` |
   | **Nome tabella** | Sì | Tabella che si desidera utilizzare, ad esempio `SalesLT.Customer` |
   ||||

   > [!TIP]
   > È possibile trovare queste informazioni nella stringa di connessione del database. Ad esempio, nella portale di Azure individuare e aprire il database. Nel menu database selezionare le stringhe di **connessione** o le **Proprietà** in cui è possibile trovare la stringa seguente:
   >
   > `Server=tcp:{your-server-address}.database.windows.net,1433;Initial Catalog={your-database-name};Persist Security Info=False;User ID={your-user-name};Password={your-password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;`

   Questo esempio illustra come possono apparire questi valori:

   ![Creare una connessione al database SQL](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. A questo punto, continuare con i passaggi non ancora completati in [aggiungere un trigger SQL](#add-sql-trigger) o [aggiungere un'azione SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-on-premises-sql-server"></a>Connettersi a SQL Server locali

La prima volta che si aggiunge un [trigger SQL](#add-sql-trigger) o un' [azione SQL](#add-sql-action)e in precedenza non è stata creata una connessione al database, viene richiesto di completare i passaggi seguenti:

1. Per le connessioni all'istanza di SQL Server locale che richiede il gateway dati locale, assicurarsi di avere [completato questi prerequisiti](#multi-tenant-or-ise).

   In caso contrario, la risorsa del gateway dati non verrà visualizzata nell'elenco **gateway di connessione** quando si crea la connessione.

1. Per **tipo di autenticazione**selezionare l'autenticazione richiesta e abilitata nel SQL Server:

   | Authentication | Descrizione |
   |----------------|-------------|
   | [**Autenticazione di Windows**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) | -Supporta solo il connettore SQL Server non ISE, che richiede una risorsa del gateway dati creata in precedenza in Azure per la connessione, indipendentemente dal fatto che si usi Azure multi-tenant o ISE. <p><p>-Richiede un nome utente e una password validi di Windows per confermare l'identità tramite l'account di Windows. <p>Per ulteriori informazioni, vedere [autenticazione di Windows](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-windows-authentication) . |
   | [**Autenticazione di SQL Server**](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication) | : Supporta sia il connettore SQL Server non ISE che ISE. <p><p>-Richiede un nome utente e una password complesse validi che vengono creati e archiviati nella SQL Server. <p>Per ulteriori informazioni, vedere [SQL Server Authentication](/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication). |
   |||

   Questo esempio continua con **l'autenticazione di Windows**:

   ![Selezionare il tipo di autenticazione da usare](./media/connectors-create-api-sqlazure/select-windows-authentication.png)

1. Selezionare o specificare i valori seguenti per il database SQL:

   | Proprietà | Obbligatoria | Descrizione |
   |----------|----------|-------------|
   | **Nome server SQL** | Sì | Indirizzo per SQL Server, ad esempio `Fabrikam-Azure-SQL.database.windows.net` |
   | **Nome database SQL** | Sì | Nome del database di SQL Server, ad esempio `Fabrikam-Azure-SQL-DB` |
   | **Nome utente** | Sì | Nome utente per SQL Server e database |
   | **Password** | Sì | Password per SQL Server e il database |
   | **Sottoscrizione** |  Sì, per l'autenticazione di Windows | Sottoscrizione di Azure per la risorsa del gateway dati creata in precedenza in Azure |
   | **Gateway di connessione** | Sì, per l'autenticazione di Windows | Nome della risorsa del gateway dati creata in precedenza in Azure <p><p>**Suggerimento**: se il gateway non viene visualizzato nell'elenco, verificare di aver [configurato correttamente il gateway](../logic-apps/logic-apps-gateway-connection.md). |
   |||

   > [!TIP]
   > È possibile trovare queste informazioni nella stringa di connessione del database:
   > 
   > * `Server={your-server-address}`
   > * `Database={your-database-name}`
   > * `User ID={your-user-name}`
   > * `Password={your-password}`

   Questo esempio illustra come possono apparire questi valori:

   ![Creazione connessione SQL Server completata](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Al termine, selezionare **Crea**.

1. A questo punto, continuare con i passaggi non ancora completati in [aggiungere un trigger SQL](#add-sql-trigger) o [aggiungere un'azione SQL](#add-sql-action).

<a name="add-sql-trigger"></a>

## <a name="add-a-sql-trigger"></a>Aggiungere un trigger SQL

1. Nel [portale di Azure](https://portal.azure.com) o in Visual Studio creare un'app per la logica vuota, che apre la finestra di progettazione dell'app per la logica. Questo esempio continua con la portale di Azure.

1. Nella finestra di progettazione, nella casella di ricerca, immettere `sql server` . Nell'elenco dei trigger selezionare il trigger SQL desiderato. In questo esempio viene usato il trigger **quando viene creato un elemento** .

   ![Selezionare il trigger "Quando viene creato un elemento"](./media/connectors-create-api-sqlazure/select-sql-server-trigger.png)

1. Se ci si connette al database SQL per la prima volta, viene richiesto di [creare subito la connessione al database SQL](#create-connection). Dopo aver creato la connessione, è possibile continuare con il passaggio successivo.

1. Nel trigger specificare l'intervallo e la frequenza per la frequenza con cui il trigger controlla la tabella.

1. Per aggiungere altre proprietà disponibili per questo trigger, aprire l'elenco **Aggiungi nuovo parametro** .

   Questo trigger restituisce solo una riga della tabella selezionata e nient'altro. Per eseguire altre attività, continuare aggiungendo un'azione del [connettore SQL](#add-sql-action) o [un'altra azione](../connectors/apis-list.md) che esegue l'attività successiva desiderata nel flusso di lavoro dell'app per la logica.
   
   Ad esempio, per visualizzare i dati in questa riga, è possibile aggiungere altre azioni per la creazione di un file che include i campi dalla riga restituita e quindi inviare avvisi di posta elettronica. Per informazioni sulle altre azioni disponibili per questo connettore, vedere la [pagina di riferimento del connettore](/connectors/sql/).

1. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

   Sebbene questo passaggio consenta e pubblichi automaticamente l'app per la logica in Azure, l'unica azione attualmente accettata dall'app per la logica consiste nel controllare il database in base all'intervallo e alla frequenza specificati.

<a name="add-sql-action"></a>

## <a name="add-a-sql-action"></a>Aggiungere un'azione SQL

In questo esempio, l'app per la logica inizia con il [trigger di ricorrenza](../connectors/connectors-native-recurrence.md)e chiama un'azione che ottiene una riga da un database SQL.

1. Nel [portale di Azure](https://portal.azure.com) o in Visual Studio aprire l'app per la logica in progettazione app per la logica. Questo esempio continua il portale di Azure.

1. Nel trigger o nell'azione in cui si vuole aggiungere l'azione SQL selezionare **Nuovo passaggio**.

   ![Aggiungere un'azione all'app per la logica](./media/connectors-create-api-sqlazure/select-new-step-logic-app.png)

   In alternativa, per aggiungere un'azione tra i passaggi esistenti, spostare il mouse sulla freccia di connessione. Selezionare il segno più ( **+** ) visualizzato e quindi **Aggiungi un'azione**.

1. In **Scegliere un'azione** immettere `sql server` nella casella di ricerca. Nell'elenco delle azioni scegliere l'azione SQL desiderata. Questo esempio usa l'azione **Ottieni riga**, che ottiene un singolo record.

   ![Selezionare l'azione "Ottieni riga" di SQL](./media/connectors-create-api-sqlazure/select-sql-get-row-action.png)

1. Se ci si connette al database SQL per la prima volta, viene richiesto di [creare subito la connessione al database SQL](#create-connection). Dopo aver creato la connessione, è possibile continuare con il passaggio successivo.

1. Selezionare il **nome della tabella**, che `SalesLT.Customer` in questo esempio è. Immettere l' **ID di riga** per il record desiderato.

   ![Selezionare il nome della tabella e specificare l'ID di riga](./media/connectors-create-api-sqlazure/specify-table-row-id.png)

   Questa azione restituisce solo una riga della tabella selezionata. Quindi, per visualizzare i dati in questa riga, è possibile aggiungere altre azioni che consentono di creare un file che include i campi dalla riga restituita e archiviare il file in un account di archiviazione cloud. Per informazioni sulle altre azioni disponibili per questo connettore, vedere la [pagina di riferimento del connettore](/connectors/sql/).

1. Al termine, nella barra degli strumenti della finestra di progettazione selezionare **Salva**.

   Con questo passaggio l'app per la logica viene automaticamente abilitata e pubblicata in Azure.

## <a name="handle-bulk-data"></a>Gestire i dati in blocco

A volte, è necessario usare set di risultati di dimensioni tali che il connettore non riesce a restituire tutti i risultati contemporaneamente oppure si vuole un controllo migliore sulle dimensioni e sulla struttura dei set di risultati. Ecco alcuni modi in cui è possibile gestire set di risultati di grandi dimensioni:

* Per gestire i risultati come set più piccoli, attivare la *paginazione*. Per altre informazioni, vedere [Ottenere dati in blocco, record ed elementi usando la paginazione](../logic-apps/logic-apps-exceed-default-page-size-with-pagination.md).

* Creare una stored procedure per organizzare i risultati nel modo desiderato.

  Se si ottengono o si inseriscono più righe, l'app per la logica può scorrere queste righe usando un [*ciclo Until*](../logic-apps/logic-apps-control-flow-loops.md#until-loop) nell'ambito di questi [limiti](../logic-apps/logic-apps-limits-and-config.md). In alcuni casi, tuttavia, l'app per la logica deve interagire con set di record talmente grandi, ad esempio migliaia o milioni di righe, che si vuole ridurre al minimo i costi risultanti dalle chiamate al database.

  Per organizzare i risultati nel modo desiderato, è possibile creare una [*stored procedure*](/sql/relational-databases/stored-procedures/stored-procedures-database-engine) che viene eseguita nell'istanza di SQL e usa l'istruzione **SELECT - ORDER BY**. Questa soluzione consente di controllare meglio le dimensioni e la struttura dei risultati. L'app per logica chiama la stored procedure usando l'azione **Esegui stored procedure** del connettore SQL Server.

  Per altre informazioni dettagliate sulla soluzione, vedere questi articoli:

  * [SQL Pagination for bulk data transfer with Logic Apps](https://social.technet.microsoft.com/wiki/contents/articles/40060.sql-pagination-for-bulk-data-transfer-with-logic-apps.aspx) (Paginazione SQL per il trasferimento di dati in bulk con App per la logica)

  * [Clausola SELECT - ORDER BY](/sql/t-sql/queries/select-order-by-clause-transact-sql)

### <a name="handle-dynamic-bulk-data"></a>Gestire i dati in blocco dinamici

Quando si chiama un stored procedure usando il connettore SQL Server, l'output restituito è talvolta dinamico. In questo scenario, seguire questi passaggi:

1. Nel [portale di Azure](https://portal.azure.com) aprire l'app per la logica in Progettazione app per la logica.

1. Visualizzare il formato di output eseguendo un'esecuzione dei test. Copiare e salvare l'output di esempio.

1. Nella finestra di progettazione, in corrispondenza dell'azione in cui è stata effettuata la chiamata alla stored procedure, selezionare **Nuovo passaggio**.

1. In **scegliere un'azione**trovare e selezionare l'azione [**analizza JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) .

1. Nell'azione **Analizza JSON** selezionare **Usare il payload di esempio per generare lo schema**.

1. Nella casella **immettere o incollare un payload JSON di esempio** incollare l'output di esempio e selezionare **fine**.

   > [!NOTE]
   > Se viene restituito un errore che indica che App per la logica non può generare uno schema, verificare che la sintassi dell'output di esempio sia formattata correttamente. Se non si riesce ancora a generare lo schema, nella casella **schema** immettere manualmente lo schema.

1. Sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

1. Per fare riferimento alle proprietà di contenuto JSON, fare clic all'interno delle caselle di modifica in cui si desidera fare riferimento a tali proprietà in modo che venga visualizzato l'elenco contenuto dinamico. Nell'elenco, sotto l'intestazione [**Parse JSON**](../logic-apps/logic-apps-perform-data-operations.md#parse-json-action) , selezionare i token di dati per le proprietà di contenuto JSON desiderate.

## <a name="connector-specific-details"></a>Dettagli specifici del connettore

Per informazioni tecniche sui trigger, le azioni e i limiti di questo connettore, vedere la [pagina di riferimento del connettore](/connectors/sql/), generata dalla descrizione di spavalderia.

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su altri [connettori per App per la logica di Azure](../connectors/apis-list.md)

