---
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 11/08/2019
ms.openlocfilehash: ea0ae1b1527aa1f527c8ac8fbcd3b4e4f6b6fe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789198"
---
* Se si usa il database SQL di Azure, seguire la procedura descritta in [Connettersi al database SQL di Azure](#connect-azure-sql-db).

* Se si usa SQL Server, seguire la procedura descritta in [Connettersi a SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Connettersi al database SQL di Azure

Quando il trigger o l'azione SQL richiede informazioni sulla connessione, attenersi alla seguente procedura, che funziona noto sia per i trigger che per le azioni.

1. In **Nome connessione**, creare un nome per la connessione.

1. In Nome server SQL selezionare il server SQL di Azure.Under **SQL Server Name**, select your Azure SQL server. Quando viene visualizzato l'elenco **Nome database SQL,** selezionare il database. Specificare il nome utente e la password per il server SQL di Azure.Provide the user name and password for your Azure SQL server.

   È inoltre possibile trovare queste informazioni nel portale di Azure nelle proprietà del database SQL o nella stringa di connessione:You can also find this information either in the Azure portal under your SQL database properties or in your connection string:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Creare una connessione al database SQL di AzureCreate connection to Azure SQL Database](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Al termine, selezionare **Crea**.

1. Dopo aver creato la connessione, continuare con [Aggiungi un trigger SQL](#add-sql-trigger) o Aggiungi [un'azione SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Connessione a SQL Server

Quando il trigger o l'azione SQL richiede informazioni sulla connessione, attenersi alla seguente procedura, che funziona noto sia per i trigger che per le azioni. Per gli scenari che richiedono l'installazione del [gateway dati locale](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install) in un computer locale e la creazione della risorsa gateway dati di [Azure,](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)assicurarsi di completare prima questi requisiti. In caso contrario, la risorsa gateway non verrà visualizzata nell'elenco dei gateway quando si crea la connessione.

Inoltre, per utilizzare l'autenticazione di Windows con il connettore SQL Server in un ambiente del servizio di [integrazione (ISE),](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview)utilizzare la versione non ISE del connettore e il gateway dati locale. La versione con etichetta ISE non supporta l'autenticazione di Windows.

1. In **Nome connessione**, creare un nome per la connessione.

1. Nel trigger o nell'azione selezionare **Connetti tramite gateway dati locale** in modo da visualizzare le opzioni del server SQL.

1. Per **Nome server SQL** e Nome database **SQL**, specificare l'indirizzo per il server SQL e il nome del database. Per **Nome utente** e **Password**, specificare il nome utente e la password per il server.

   È inoltre possibile trovare queste informazioni nella stringa di connessione:You can also find this information in your connection string:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Creare una connessione a SQL ServerCreate connection to SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Se il server SQL utilizza l'autenticazione di Windows o di base, selezionare **il tipo di autenticazione**.

1. In **Gateway**selezionare la sottoscrizione di Azure associata al gateway dati locale creato in precedenza e selezionare il nome del gateway dati locale.

   Se il gateway non compare nell'elenco, verificare di avere [configurato il gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection) correttamente.

   ![Creare la connessione a SQL Server completataCreate SQL Server connection completed](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Al termine, selezionare **Crea**.

1. Dopo aver creato la connessione, continuare con [Aggiungere un trigger SQL](#add-sql-trigger) oppure [Aggiungere un'azione SQL](#add-sql-action).
