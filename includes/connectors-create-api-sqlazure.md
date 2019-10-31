---
title: file di inclusione
description: file di inclusione
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.author: estfan
ms.custom: include file
ms.date: 05/15/2018
ms.openlocfilehash: d60d7727e0674298fa6da7e7330221318da23efd
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161651"
---
* Se si usa il database SQL di Azure, seguire la procedura descritta in [Connettersi al database SQL di Azure](#connect-azure-sql-db).

* Se si usa SQL Server, seguire la procedura descritta in [Connettersi a SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Connettersi al database SQL di Azure

Quando il trigger SQL o l'azione richiede le informazioni di connessione, attenersi alla procedura seguente, che funziona sia per i trigger che per le azioni.

1. Per **nome connessione**, creare un nome per la connessione.

1. In **nome SQL Server**selezionare il server di Azure SQL. Quando viene visualizzato l'elenco **nome database SQL** , selezionare il database. Fornire il nome utente e la password per il server di Azure SQL.

   È anche possibile trovare queste informazioni nel portale di Azure sotto le proprietà del database SQL o nella stringa di connessione:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Creare una connessione al database SQL di Azure](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Al termine, selezionare **Crea**.

1. Dopo aver creato la connessione, continuare con [aggiungere un trigger SQL](#add-sql-trigger) o [aggiungere un'azione SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Connettersi a SQL Server

Quando il trigger SQL o l'azione richiede le informazioni di connessione, attenersi alla procedura seguente, che funziona sia per i trigger che per le azioni. Tuttavia, prima di iniziare, assicurarsi di aver già [configurato il gateway dati locale](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). In caso contrario, il gateway non verrà visualizzato nell'elenco gateway quando si crea la connessione.

1. Per **nome connessione**, creare un nome per la connessione.

1. Nel trigger o nell'azione selezionare **Connetti tramite gateway dati locale** in modo da visualizzare le opzioni del server SQL.

1. Per nome **SQL Server** e **nome database SQL**, specificare l'indirizzo per il server SQL e il nome del database. Per **nome utente e** **password**specificare il nome utente e la password per il server.

   È anche possibile trovare queste informazioni nella stringa di connessione:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Crea connessione a SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Se SQL Server usa l'autenticazione di Windows o di base, selezionare il **tipo di autenticazione**.

1. In **gateway**selezionare la sottoscrizione di Azure associata al gateway dati locale creato in precedenza e selezionare il nome per il gateway dati locale.

   Se il gateway non compare nell'elenco, verificare di avere [configurato il gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection) correttamente.

   ![Creazione connessione SQL Server completata](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Al termine, selezionare **Crea**.

1. Dopo aver creato la connessione, continuare con [Aggiungere un trigger SQL](#add-sql-trigger) oppure [Aggiungere un'azione SQL](#add-sql-action).
