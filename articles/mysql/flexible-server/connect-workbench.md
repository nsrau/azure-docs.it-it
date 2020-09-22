---
title: 'Avvio rapido: Effettuare la connessione - MySQL Workbench - Database di Azure per MySQL - Server flessibile'
description: Questo argomento di avvio rapido illustra come usare MySQL Workbench per connettersi ed eseguire query sui dati dal server flessibile di Database di Azure per MySQL.
author: ambhatna
ms.author: ambhatna
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 9/21/2020
ms.openlocfilehash: d604ca8c8979ec98b990f8002ce29d0df92ac2af
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90945069"
---
# <a name="quickstart-use-mysql-workbench-to-connect-and-query-data-in-azure-database-for-mysql---flexible-server-preview"></a>Avvio rapido: Usare MySQL Workbench per connettersi ed eseguire query sui dati in Database di Azure per MySQL - Server flessibile (anteprima)


> [!IMPORTANT] 
> Il server flessibile di Database di Azure per MySQL è attualmente disponibile in anteprima pubblica.

Questo argomento di avvio rapido illustra come connettersi a un server flessibile di Database di Azure per MySQL usando l'applicazione MySQL Workbench.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva usa le risorse create in una delle guide seguenti come punto di partenza:

- [Creare un server flessibile di Database di Azure per MySQL tramite il portale di Azure](./quickstart-create-server-portal.md)
- [Creare un server flessibile di Database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure](./quickstart-create-server-cli.md)

## <a name="preparing-your-client-workstation"></a>Preparazione della workstation client
- Se il server flessibile è stato creato con l'opzione per l'*accesso privato (integrazione rete virtuale)* , sarà necessario connettersi al server da una risorsa all'interno della stessa rete virtuale usata dal server. È possibile creare una macchina virtuale e aggiungerla alla rete virtuale creata con il server flessibile. Vedere [Creare e gestire la rete virtuale del server flessibile di Database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure](./how-to-manage-virtual-network-cli.md).
- Se il server flessibile è stato creato con l'opzione per l'*accesso pubblico (indirizzi IP consentiti)* , è possibile aggiungere l'indirizzo IP locale all'elenco di regole del firewall nel server. Vedere [Creare e gestire le regole del firewall del server flessibile di Database di Azure per MySQL tramite l'interfaccia della riga di comando di Azure](./how-to-manage-firewall-cli.md).

- Scaricare e installare MySQL Workbench nel computer dal [sito Web di MySQL](https://dev.mysql.com/downloads/workbench/).

## <a name="get-connection-information"></a>Ottenere informazioni di connessione

Ottenere le informazioni di connessione necessarie per connettersi al server flessibile. Sono necessari il nome server completo e le credenziali di accesso.

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Dal menu a sinistra nel portale di Azure scegliere **Tutte le risorse** e quindi cercare il server creato, ad esempio **mydemoserver**.
3. Selezionare il nome del server.
4. Nel pannello **Panoramica** del server prendere nota dei valori riportati in **Nome server** e **Nome di accesso dell'amministratore server**. Se si dimentica la password, in questo pannello è anche possibile reimpostarla.
<!--- :::image type="content" source="./media/connect-php/1_server-overview-name-login.png" alt-text="Azure Database for MySQL Flexible Server name":::--->

## <a name="connect-to-the-server-using-mysql-workbench"></a>Connettersi al server con MySQL Workbench

Per connettersi al server flessibile di Database di Azure per MySQL tramite MySQL Workbench:

1. Avviare l'applicazione MySQL Workbench nel computer.

2. Nella finestra di dialogo **Setup New Connection** (Configura nuova connessione) immettere le informazioni seguenti nella scheda **Parameters** (Parametri):

    :::image type="content" source="./media/connect-workbench/2-setup-new-connection.png" alt-text="Setup New Connection (Configura nuova connessione)":::

    | **Parameters** | **Valore consigliato** | **Descrizione campo** |
    |---|---|---|
    |    Connection Name (Nome connessione) | Demo Connection | Specificare un'etichetta per la connessione. |
    | Connection Method (Metodo di connessione) | Standard (TCP/IP) | Standard (TCP/IP) è sufficiente. |
    | nomehost | *nome del server* | Specificare il valore del nome del server usato in precedenza al momento della creazione del database di Azure per MySQL. Il server di esempio visualizzato è mydemoserver.mysql.database.azure.com. Usare il nome di dominio completo (\*.mysql.database.azure.com) come nell'esempio. Se non si ricorda il nome del server, seguire la procedura illustrata nella sezione precedente per ottenere le informazioni di connessione.  |
    | Porta | 3306 | Usare sempre la porta 3306 per la connessione al database di Azure per MySQL. |
    | Username |  *nome di accesso amministratore server* | Digitare il nome utente di accesso amministratore server specificato in precedenza al momento della creazione del database di Azure per MySQL. Il nome utente dell'esempio è myadmin. Se non si ricorda il nome utente, seguire la procedura illustrata nella sezione precedente per ottenere le informazioni di connessione.
    | Password | Immettere la password. | Fare clic sul pulsante **Store in Vault...** (Archivia nell'insieme di credenziali) per salvare la password. |

3. Fare clic su **Test Connection** (Test connessione) per verificare che tutti i parametri siano configurati correttamente.

4. Fare clic su **OK** per salvare la connessione.

5. Nell'elenco delle **connessioni MySQL** fare clic sul riquadro corrispondente al server e quindi attendere che venga stabilita la connessione.

    Verrà visualizzata una nuova scheda SQL con un editor vuoto in cui è possibile digitare le query.

> [!NOTE]
> La connessione crittografata con TLS 1.2 è richiesta e applicata al server flessibile di Database di Azure per MySQL. Anche se in genere non sono necessarie configurazioni aggiuntive con certificati TLS/SSL per la connessione di MySQL Workbench al server, è consigliabile associare la certificazione TLS/SSL della CA a MySQL Workbench. Per altre informazioni, vedere [Connettersi tramite TLS/SSL](./how-to-connect-tls-ssl.md)

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>Creare una tabella e inserire, leggere, aggiornare ed eliminare dati

1. Copiare e incollare il codice SQL di esempio in una scheda SQL vuota per illustrare alcuni dati di esempio.

    Il codice crea un database vuoto denominato quickstartdb, quindi crea una tabella di esempio denominata inventory. Inserisce alcune righe e quindi le legge. Modifica i dati con un'istruzione update e legge nuovamente le righe. Infine, elimina una riga e quindi legge nuovamente le righe.

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

    :::image type="content" source="./media/connect-workbench/3-workbench-sql-tab.png" alt-text="Scheda SQL in MySQL Workbench per l'esecuzione del codice SQL di esempio":::

2. Per eseguire l'esempio di codice SQL, fare clic sull'icona saetta nella barra degli strumenti della scheda **File SQL**.
3. Si notino i tre risultati a schede nella sezione **Griglia risultati** nella parte centrale della pagina.
4. Si noti l'elenco **Output** nella parte inferiore della pagina. Viene visualizzato lo stato di ogni comando.

A questo punto è stata effettuata la connessione al server flessibile di Database di Azure per MySQL tramite MySQL Workbench ed è stata eseguita una query sui dati con il linguaggio SQL.

## <a name="next-steps"></a>Passaggi successivi
- [Connettività crittografata con Transport Layer Security (TLS 1.2) in Database di Azure per MySQL - Server flessibile](./how-to-connect-tls-ssl.md).
- Vedere le informazioni sulle [funzionalità di rete nel server flessibile di Database di Azure per MySQL](./concepts-networking.md).
- [Creare e gestire le regole del firewall del server flessibile di Database di Azure per MySQL tramite il portale di Azure](./how-to-manage-firewall-portal.md).
- [Creare e gestire la rete virtuale del server flessibile di Database di Azure per MySQL tramite il portale di Azure](./how-to-manage-virtual-network-portal.md).
