---
title: File di inclusione
description: File di inclusione
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/15/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: da03c5247b8ebe0a3305b08a05d661264497663f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60540973"
---
* Se si usa il database SQL di Azure, seguire la procedura descritta in [Connettersi al database SQL di Azure](#connect-azure-sql-db). 

* Se si usa SQL Server, seguire la procedura descritta in [Connettersi a SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Connettersi al database SQL di Azure

1. Quando il trigger o l'azione SQL richiede informazioni di connessione, seguire questa procedura:

   1. Creare un nome per la connessione.

   2. Selezionare il server SQL e quindi selezionare il database. 

      L'elenco dei database viene visualizzatolo solo dopo aver selezionato il server SQL.
 
   3. Specificare nome utente e password per il server.

      È possibile trovare queste informazioni nel portale di Azure tra le proprietà del database SQL o nella stringa di connessione: 
      
      "User ID=<*NomeUtente*>"
      <br>
      "Password=<*Password*>"

   Questo esempio mostra le informazioni di connessione per un trigger, ma questa procedura è valida anche per le azioni.

   ![Creare una connessione al database SQL di Azure](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)
   <br>
   Gli asterischi (*) indicano valori obbligatori.

   | Proprietà | Value | Dettagli | 
   |----------|-------|---------| 
   | Connection Name (Nome connessione) | <*my-sql-connection*> | Nome per la connessione | 
   | Nome server SQL | <*my-sql-server*> | Nome del server SQL |
   | Nome del database SQL | <*my-sql-database*>  | Nome del database SQL | 
   | Username | <*my-sql-username*> | Nome utente per l'accesso al database |
   | Password | <*my-sql-password*> | Password per l'accesso al database | 
   |||| 

2. Al termine dell'operazione, scegliere **Crea**.

3. Dopo aver creato la connessione, continuare con [Aggiungere un trigger SQL](#add-sql-trigger) oppure [Aggiungere un'azione SQL](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Connettersi a SQL Server

Prima di poter selezionare il gateway, assicurarsi di avere già [configurato il gateway dati](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). In questo modo, il gateway viene visualizzato nell'elenco dei gateway quando si crea la connessione.

1. Quando il trigger o l'azione SQL richiede informazioni di connessione, seguire questa procedura:

   1. Nel trigger o nell'azione selezionare **Connetti tramite gateway dati locale** in modo da visualizzare le opzioni del server SQL.

   2. Creare un nome per la connessione.

   3. Specificare l'indirizzo per il server SQL e quindi specificare il nome del database.
   
      È possibile trovare queste informazioni nella stringa di connessione: 
      
      * "Server = <*IndirizzoServer*>"
      * "Database = <*NomeDatabase*>"

   4. Specificare nome utente e password per il server.

      È possibile trovare queste informazioni nella stringa di connessione: 
      
      * "User ID=<*NomeUtente*>"
      * "Password=<*Password*>"

   5. Se il server SQL usa l'autenticazione di base o di Windows, selezionare il tipo di autenticazione.

   6. Selezionare il nome per il gateway dati locale creato in precedenza.
   
      Se il gateway non compare nell'elenco, verificare di avere [configurato il gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection) correttamente.

   Questo esempio mostra le informazioni di connessione per un trigger, ma questa procedura è valida anche per le azioni.

   ![Creare la connessione a SQL Server](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)
   <br>
   Gli asterischi (*) indicano valori obbligatori.

   | Proprietà | Value | Dettagli | 
   |----------|-------|---------| 
   | Connetti tramite gateway dati locale | Selezionare prima di tutto questa opzione per le impostazioni di SQL Server. | | 
   | Connection Name (Nome connessione) | <*my-sql-connection*> | Nome per la connessione | 
   | Nome server SQL | <*my-sql-server*> | Nome del server SQL |
   | Nome del database SQL | <*my-sql-database*>  | Nome del database SQL |
   | Username | <*my-sql-username*> | Nome utente per l'accesso al database |
   | Password | <*my-sql-password*> | Password per l'accesso al database | 
   | Tipo di autenticazione | Windows o Di base | Facoltativo: Il tipo di autenticazione utilizzato da SQL server | 
   | Gateway | <*my-data-gateway*> | Nome del gateway dati locale | 
   |||| 

2. Al termine dell'operazione, scegliere **Crea**. 

3. Dopo aver creato la connessione, continuare con [Aggiungere un trigger SQL](#add-sql-trigger) oppure [Aggiungere un'azione SQL](#add-sql-action).
