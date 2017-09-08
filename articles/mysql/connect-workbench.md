---
title: Connettersi al database di Azure per MySQL da MySQL Workbench | Microsoft Docs
description: Questa guida introduttiva illustra come usare MySQL Workbench per connettersi ed eseguire query sui dati dal database di Azure per MySQL.
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: seanli1988
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 08/23/2017
ms.translationtype: HT
ms.sourcegitcommit: 646886ad82d47162a62835e343fcaa7dadfaa311
ms.openlocfilehash: 20a1f31ce42abb924504c4008f85420fc49aec89
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---

# <a name="azure-database-for-mysql-use-mysql-workbench-to-connect-and-query-data"></a>Database di Azure per MySQL: usare MySQL Workbench per connettersi ed eseguire query sui dati
Questa guida introduttiva illustra come connettersi a un database di Azure per MySQL usando un'applicazione MySQL Workbench. 

## <a name="prerequisites"></a>Prerequisiti
Questa guida introduttiva usa le risorse create in una delle guide seguenti come punto di partenza:
- [Creare un database di Azure per il server MySQL tramite il portale di Azure](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Creare un database di Azure per il server MySQL tramite l'interfaccia della riga di comando di Azure](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-mysql-workbench"></a>Installare MySQL Workbench
Scaricare e installare MySQL Workbench nel computer dal [sito Web di MySQL](https://dev.mysql.com/downloads/workbench/).

## <a name="get-connection-information"></a>Ottenere informazioni di connessione
Ottenere le informazioni di connessione necessarie per connettersi al database di Azure per MySQL. Sono necessari il nome del server completo e le credenziali di accesso.

1. Accedere al [Portale di Azure](https://portal.azure.com/).

2. Nel menu a sinistra nel portale di Azure fare clic su **Tutte le risorse** e cercare il server creato, ad esempio **myserver4demo**.

3. Fare clic sul nome del server.

4. Selezionare la pagina **Proprietà** del server. Annotare il **Nome server** e il **nome di accesso dell'amministratore del server**.

 ![Nome del server del database di Azure per MySQL](./media/connect-workbench/1-server-properties-name-login.png)
 
5. Se si dimenticano le informazioni di accesso per il server, passare alla pagina **Panoramica** per visualizzare il nome di accesso dell'amministratore del server e, se necessario, reimpostare la password.

## <a name="connect-to-the-server-using-mysql-workbench"></a>Connettersi al server con MySQL Workbench 
Per connettersi al server MySQL di Azure con lo strumento dell'interfaccia utente grafica MySQL Workbench:

1.  Avviare l'applicazione MySQL Workbench nel computer. 

2.  Nella finestra di dialogo **Setup New Connection** (Configura nuova connessione) immettere le informazioni seguenti nella scheda **Parameters** (Parametri):

    ![Setup New Connection (Configura nuova connessione)](./media/connect-workbench/2-setup-new-connection.png)

    | **Impostazione** | **Valore consigliato** | **Descrizione campo** |
    |---|---|---|
    |   Connection Name (Nome connessione) | Demo Connection | Specificare un'etichetta per la connessione. |
    | Connection Method (Metodo di connessione) | Standard (TCP/IP) | Standard (TCP/IP) è sufficiente. |
    | Nome host | *nome del server* | Specificare il valore del nome del server usato in precedenza al momento della creazione del database di Azure per MySQL. Il server di esempio visualizzato è myserver4demo.mysql.database.azure.com. Usare il nome di dominio completo (\*.mysql.database.azure.com) come nell'esempio. Se non si ricorda il nome del server, seguire la procedura illustrata nella sezione precedente per ottenere le informazioni di connessione.  |
    | Porta | 3306 | Usare sempre la porta 3306 per la connessione al database di Azure per MySQL. |
    | Username |  *nome di accesso amministratore server* | Digitare il nome utente di accesso amministratore server specificato in precedenza al momento della creazione del database di Azure per MySQL. Il nome utente dell'esempio è myadmin@myserver4demo. Se non si ricorda il nome utente, seguire la procedura illustrata nella sezione precedente per ottenere le informazioni di connessione. Il formato è *username@servername*.
    | Password | Immettere la password. | Fare clic sul pulsante **Store in Vault...** (Archivia nell'insieme di credenziali) per salvare la password. |

3.   Fare clic su **Test Connection** (Test connessione) per verificare che tutti i parametri siano configurati correttamente. 

4.   Fare clic su **OK** per salvare la connessione. 

5.   Nell'elenco delle **connessioni MySQL** fare clic sul riquadro corrispondente al server e attendere che venga stabilita la connessione.

6.   Verrà visualizzata una nuova scheda SQL con un editor vuoto in cui è possibile digitare le query.

    > [!NOTE]
    > Per impostazione predefinita, in Database di Azure per il server MySQL è necessaria e viene applicata la sicurezza della connessione SSL. Non sono in genere necessarie configurazioni aggiuntive con certificati SSL per la connessione di MySQL Workbench al server. Per altre informazioni su SSL, vedere [Configurare la connettività SSL nell'applicazione per la connessione sicura a Database di Azure per MySQL](./howto-configure-ssl.md).  Se è necessario disabilitare SSL, visitare il portale di Azure e fare clic sulla pagina Sicurezza connessione per disabilitare l'interruttore Imponi connessione SSL.

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>Creare una tabella e inserire, leggere, aggiornare ed eliminare dati
1. Copiare e incollare il codice SQL di esempio in una scheda SQL vuota per illustrare alcuni dati di esempio.

    Il codice crea un database vuoto denominato quickstartdb, quindi crea una tabella di esempio denominata inventory. Inserisce alcune righe e quindi le legge. Modifica i dati con un'istruzione update e legge nuovamente le righe. Infine, elimina una riga e legge nuovamente le righe.
    
    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;
    
    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    
    -- Read
    SELECT * FROM inventory;
    
    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;
    
    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    Lo screenshot mostra un esempio di codice SQL in MySQL Workbench e l'output dopo l'esecuzione.
    
    ![Scheda SQL in MySQL Workbench per l'esecuzione del codice SQL di esempio](media/connect-workbench/3-workbench-sql-tab.png)

2. Per eseguire l'esempio di codice SQL, fare clic sull'icona saetta nella barra degli strumenti della scheda **File SQL**.
3. Si notino i tre risultati a schede nella sezione **Griglia risultati** nella parte centrale della pagina. 
4. Si noti l'elenco **Output** nella parte inferiore della pagina. Viene visualizzato lo stato di ogni comando. 

A questo punto è stata stabilita la connessione al database di Azure per MySQL tramite MySQL Workbench ed è stata eseguita una query sui dati con il linguaggio SQL.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire la migrazione del database usando le funzionalità di esportazione e importazione](./concepts-migrate-import-export.md)

