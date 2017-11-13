---
title: 'Avvio rapido: Creare un database di Azure per il server MySQL - Portale di Azure | Microsoft Docs'
description: Questo articolo contiene informazioni dettagliate su come usare il portale di Azure per creare rapidamente un database di Azure di esempio per il server MySQL in circa cinque minuti.
services: mysql
author: v-chenyh
ms.author: v-chenyh
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 11/01/2017
ms.openlocfilehash: a8cea099103bd6a306e41770e47b1279f5dbcdea
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="create-an-azure-database-for-mysql-server-by-using-the-azure-portal"></a>Creare un database di Azure per il server MySQL con il portale di Azure
Database di Azure per MySQL è un servizio gestito che consente di eseguire, gestire e ridimensionare database MySQL a disponibilità elevata nel cloud. Questa guida introduttiva illustra come creare un database di Azure per il server MySQL in circa cinque minuti usando il portale di Azure.  

Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure
Aprire il Web browser e quindi passare al [portale di Azure](https://portal.azure.com/). Immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.

## <a name="create-an-azure-database-for-mysql-server"></a>Creare un database di Azure per il server MySQL
Si crea un database di Azure per il server MySQL con un set definito di [risorse di calcolo e di archiviazione](./concepts-compute-unit-and-storage.md). Il server viene creato all'interno di un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md).

Seguire questa procedura per creare un database di Azure per il server MySQL:

1. Selezionare il pulsante **Nuovo** (+) nell'angolo superiore sinistro del portale.

2. Selezionare **Database** > **Database di Azure per MySQL**. È anche possibile digitare **MySQL** nella casella di ricerca per trovare il servizio.

    ![Opzione Database di Azure per MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

4. Compilare il modulo dei dettagli del nuovo server con le informazioni seguenti, come illustrato nell'immagine precedente:

    **Impostazione** | **Valore consigliato** | **Descrizione campo** 
    ---|---|---
    Nome server | Nome server univoco | Scegliere un nome univoco per identificare il database di Azure per il server MySQL. For example, myserver4demo. Al nome del server specificato viene aggiunto il nome di dominio *mysql.database.azure.com*. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) e deve avere una lunghezza compresa tra 3 e 63 caratteri.
    Sottoscrizione | Sottoscrizione in uso | Selezionare la sottoscrizione di Azure da usare per il server. Se si hanno più sottoscrizioni, scegliere quella in cui viene fatturata la risorsa.
    Gruppo di risorse | myresourcegroup | Specificare il nome di un gruppo di risorse nuovo o esistente.
    Accesso amministratore server | myadmin | Account di accesso da usare per la connessione al server. Il nome di accesso dell'amministratore non può essere **azure_superuser**, **admin**, **administrator**, **root**, **guest** o **public**.
    Password | *A scelta dell'utente* | Specificare una nuova password per l'account dell'amministratore del server. Deve avere una lunghezza compresa tra 8 e 128 caratteri. La password deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri (0-9) e caratteri non alfanumerici (!, $, #, % e così via).
    Conferma password | *A scelta dell'utente*| Confermare la password dell'account amministratore.
    Percorso | *Area più vicina ai propri utenti*| Scegliere la località più vicina agli utenti o alle altre applicazioni Azure.
    Versione | *Ultima versione*| Ultima versione, a meno che non si abbiano requisiti specifici per cui deve esserne usata un'altra.
    Piano tariffario  | **Basic**, **50 unità di calcolo**, **50 GB** | Scegliere il livello di servizio e il livello di prestazioni per il nuovo database. Selezionare **Piano Basic** nella parte superiore della scheda. Selezionare il lato sinistro del dispositivo di scorrimento **Unità di calcolo** per impostare il valore sulla quantità minima disponibile per questa guida introduttiva. Selezionare **OK** per salvare la selezione del piano tariffario. Per altre informazioni, vedere lo screenshot seguente.
    Aggiungi al dashboard | Controllo | Selezionare questa opzione per tenere facilmente traccia del server nella pagina iniziale del dashboard del portale di Azure.

    > [!IMPORTANT]
    > L'account di accesso amministratore del server e la password qui specificati sono necessari per accedere al server e ai relativi database più avanti in questa guida introduttiva. Prendere nota di queste informazioni per usarle in seguito.
    > 

    ![Portale di Azure: creare MySQL specificando l'input del modulo necessario](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

4.  Selezionare **Crea** per effettuare il provisioning del server. Il provisioning può richiedere fino a 20 minuti.
   
5.  Selezionare **Notifiche** sulla barra degli strumenti (icona a forma di campana) per monitorare il processo di distribuzione.

## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server

Il servizio Database di Azure per MySQL crea un firewall a livello di server, che impedisce alle applicazioni e agli strumenti esterni di connettersi al server e ai database nel server, a meno che non venga creata una regola del firewall per aprire il firewall per indirizzi IP specifici. 

1.   Al termine della distribuzione, individuare il server. Se necessario, è possibile eseguire una ricerca. Scegliere **Tutte le risorse** dal menu a sinistra e quindi digitare il nome del server, che in questo esempio è **myserver4demo**, per cercare il server appena creato. Selezionare il nome del server nell'elenco dei risultati della ricerca. Si apre la pagina **Panoramica** del server in cui vengono fornite le opzioni per una configurazione aggiuntiva.

2. Nella pagina del server selezionare **Sicurezza connessione**.

3.  Sotto l'intestazione **Regole del firewall** selezionare la casella di testo vuota nella colonna **Nome regola** per iniziare a creare la regola del firewall. 

   ![Sicurezza delle connessioni: regole del firewall](./media/quickstart-create-mysql-server-database-using-azure-portal/5_firewall-settings.png)

   Per questa guida introduttiva verranno consentiti tutti gli indirizzi IP nel server inserendo nelle caselle di ogni colonna i valori seguenti:

   Nome regola | Indirizzo IP iniziale | Indirizzo IP finale 
   ---|---|---
   AllowAllIps |  0.0.0.0 | 255.255.255.255
   
   Consentire tutti gli indirizzi IP non è un'opzione sicura. Questo esempio viene fornito per motivi di semplicità, ma in uno scenario reale è necessario conoscere gli intervalli di indirizzi IP precisi da aggiungere per applicazioni e utenti. 

4. Sulla barra degli strumenti superiore della pagina **Sicurezza connessione** selezionare **Salva**. Prima di continuare, attendere che venga visualizzata la notifica che informa che l'aggiornamento è stato completato. 

   > [!NOTE]
   > Le connessioni al database di Azure per MySQL comunicano sulla porta 3306. Se si tenta di connettersi da una rete aziendale, il traffico in uscita sulla porta 3306 potrebbe non essere consentito. In questo caso, non è possibile connettersi al server a meno che il reparto IT non apra la porta 3306.
   > 

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione
Per connettersi al server di database, è necessario il nome del server completo e le credenziali di accesso amministratore. È possibile che si sia preso nota prima di questi valori durante la lettura dell'articolo introduttivo. In caso contrario, è possibile trovare facilmente il nome del server e le informazioni di accesso nella pagina **Proprietà** o nella pagina **Panoramica** del server nel portale di Azure.

A tale scopo, seguire questa procedura: 

1. Aprire la pagina **Panoramica** del server. Annotare il **Nome server** e il **nome di accesso dell'amministratore del server**. 

2. Passare il puntatore del mouse su ogni campo per visualizzare l'icona di copia a destra del testo. Selezionare l'icona di copia necessaria per copiare i valori.

In questo esempio, il nome del server è **myserver4demo.mysql.database.azure.com** e l'account di accesso amministratore del server è **myadmin@myserver4demo**.

## <a name="connect-to-mysql-by-using-the-mysql-command-line-tool"></a>Connettersi a MySQL con lo strumento da riga di comando mysql
Per connettersi al database di Azure per il server MySQL è possibile usare diverse applicazioni. 

Prima di tutto verrà usato lo strumento da riga di comando [mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) per illustrare la connessione al server. È anche possibile usare un Web browser e Azure Cloud Shell, come descritto qui, senza installare software aggiuntivo. È possibile connettersi anche dall'utilità mysql eventualmente installata in locale.

1. Avviare Azure Cloud Shell tramite l'icona del terminale (**>_**) nella parte superiore destra del portale di Azure.

2.  Azure Cloud Shell si apre nel browser, in cui è quindi possibile digitare i comandi della shell Bash.

    ![Prompt dei comandi: esempio di riga di comando mysql](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

3. Al prompt di Cloud Shell connettersi al database di Azure per il server MySQL digitando la riga di comando mysql.

    Per connettersi a un database di Azure per il server MySQL con l'utilità mysql, usare il formato seguente:

    ```bash
    mysql --host <fully qualified servername> --user <serveradminlogin@servername> -p
    ```

    Ad esempio, il comando seguente stabilisce la connessione al server di esempio:

    ```azurecli-interactive
    mysql --host myserver4demo.mysql.database.azure.com --user myadmin@myserver4demo -p
    ```

    Parametro di mysql |Valore consigliato|Descrizione
    ---|---|---
    --host | *Server name* (Nome server) | Valore del nome del server usato in precedenza al momento della creazione del database di Azure per il server MySQL. Il server dell'esempio è **myserver4demo.mysql.database.azure.com**. Usare il nome di dominio completo (**\*.mysql.database.azure.com**) come illustrato nell'esempio. Se non si ricorda il nome del server, seguire la procedura descritta nella sezione precedente per ottenere le informazioni di connessione. 
    --user | *Nome di accesso amministratore del server* |Nome utente di accesso amministratore del server specificato in precedenza al momento della creazione del database di Azure per il server MySQL. Se non si ricorda il nome utente, seguire la procedura descritta nella sezione precedente per ottenere le informazioni di connessione. Il formato è *username@servername*.
    -p | *Attendere finché non viene richiesta* |Quando richiesto, digitare la stessa password specificata al momento della creazione del server. Si noti che i caratteri della password digitati non vengono visualizzati nel prompt di Bash durante la digitazione. Dopo aver immesso la password, premere **INVIO**.

   Dopo che è stata stabilita la connessione, l'utilità mysql visualizza un prompt `mysql>` in cui è possibile digitare comandi. 

   Di seguito è riportato un output di mysql di esempio:

    ```bash
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 65505
    Server version: 5.6.26.0 MySQL Community Server (GPL)
    
    Copyright (c) 2000, 2017, Oracle and/or its affiliates. All rights reserved.
    
    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
    
    mysql>
    ```
    > [!TIP]
    > Se il firewall non è configurato per consentire l'indirizzo IP di Azure Cloud Shell, verrà visualizzato l'errore seguente:
    >
    > ERROR 2003 (28000): Client with IP address 123.456.789.0 is not allowed to access the server.
    >
    > Per risolvere l'errore, assicurarsi che la configurazione del server corrisponda alla procedura illustrata nella sezione "Configurare una regola del firewall a livello di server" di questo articolo.

4. Per verificare che la connessione funzioni, digitare `status` al prompt mysql> per visualizzare lo stato del server.

    ```sql
    status
    ```

   > [!TIP]
   > Per altri comandi, vedere il [capitolo 4.5.1 del manuale di riferimento di MySQL 5.7](https://dev.mysql.com/doc/refman/5.7/en/mysql.html).

5.  Creare un database vuoto al prompt **mysql>** digitando il comando seguente:
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    Il completamento del comando potrebbe richiedere alcuni istanti. 

    In un database di Azure per il server MySQL è possibile creare uno o più database. È possibile scegliere di creare un singolo database per ogni server per usare tutte le risorse o creare più database per condividere le risorse. Il numero di database che è possibile creare è illimitato, ma più database condividono le stesse risorse del server. 

6. Elencare i database al prompt **mysql>** digitando il comando seguente:

    ```sql
    SHOW DATABASES;
    ```

7.  Digitare `\q` e quindi premere **INVIO** per uscire dallo strumento mysql. Al termine, è possibile chiudere Azure Cloud Shell.

Ora che si è connessi al database di Azure per il server MySQL e si è creato un database utente vuoto, passare alla sezione successiva per un esercizio simile. Nel prossimo esercizio viene stabilita la connessione allo stesso server usando un altro strumento comune, MySQL Workbench.

## <a name="connect-to-the-server-by-using-the-mysql-workbench-gui-tool"></a>Connettersi al server con lo strumento con interfaccia utente grafica MySQL Workbench
Per connettersi al server con lo strumento con interfaccia utente grafica MySQL Workbench, seguire questa procedura:

1.  Aprire l'applicazione MySQL Workbench nel computer client. È possibile scaricare e installare MySQL Workbench dalla pagina [Download MySQL Workbench](https://dev.mysql.com/downloads/workbench/) (Scaricare MySQL Workbench).

2. Crea una nuova connessione. Fare clic sull'icona del segno più (+) accanto all'intestazione **MySQL Connections** (Connessioni MySQL).

3. Nella finestra di dialogo **Setup New Connection** (Configura nuova connessione) immettere le informazioni di connessione server nella scheda **Parameters** (Parametri). I valori segnaposto sono visualizzati a titolo di esempio. Sostituire Hostname (Nome host), Username (Nome utente) e Password con valori personalizzati.

    ![Configurare una nuova connessione](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

    |Impostazione |Valore consigliato|Descrizione campo|
    |---|---|---|
     Nome connessione | Demo Connection | Etichetta per la connessione. |
    Connection Method (Metodo di connessione) | Standard (TCP/IP) | Standard (TCP/IP) è sufficiente. |
    Nome host | *Server name* (Nome server) | Nome del server usato in precedenza al momento della creazione del database di Azure per il server MySQL. Il server dell'esempio è **myserver4demo.mysql.database.azure.com**. Usare il nome di dominio completo (**\*.mysql.database.azure.com**) come illustrato nell'esempio. Se non si ricorda il nome del server, seguire la procedura descritta nella sezione precedente per ottenere le informazioni di connessione.|
     Porta | 3306 | Porta da usare per la connessione al database di Azure per il server MySQL. |
    Username |  *Nome di accesso amministratore del server* | Informazioni di accesso amministratore del server specificate in precedenza al momento della creazione del database di Azure per il server MySQL. Il nome utente dell'esempio è **myadmin@myserver4demo**. Se non si ricorda il nome utente, seguire la procedura descritta nella sezione precedente per ottenere le informazioni di connessione. Il formato è *username@servername*.
    Password | *Password* | Selezionare il pulsante **Store in Vault** (Archivia nell'insieme di credenziali) per salvare la password. |

4. Selezionare **Test Connection** (Test connessione) per verificare che tutti i parametri siano configurati correttamente. Selezionare quindi **OK** per salvare la connessione. 

    > [!NOTE]
    > SSL viene applicato per impostazione predefinita nel server e richiede una configurazione aggiuntiva per completare la connessione. Per altre informazioni, vedere [Configurare la connettività SSL nell'applicazione per la connessione sicura a Database di Azure per MySQL](./howto-configure-ssl.md). Per disabilitare SSL per questa guida introduttiva, passare al portale di Azure. Selezionare quindi la pagina Sicurezza connessione per disabilitare l'interruttore **Imponi connessione SSL**.

## <a name="clean-up-resources"></a>Pulire le risorse
È possibile eseguire la pulizia delle risorse create nel corso della guida introduttiva in due modi. Si può eliminare il [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md), in modo da includere tutte le risorse del gruppo. Se invece si vogliono mantenere intatte le altre risorse, eliminare solo la risorsa server.

> [!TIP]
> Altre guide introduttive della raccolta si basano su questa. Se si intende continuare a usare le guide introduttive, non eseguire la pulizia delle risorse create in questa. Se non si intende continuare, usare la procedura seguente per eliminare tutte le risorse create con questa guida introduttiva.
>

Per eliminare l'intero gruppo di risorse, incluso il server appena creato, seguire questa procedura:

1.  Individuare il gruppo di risorse nel portale di Azure. Scegliere **Gruppi di risorse** dal menu a sinistra e quindi selezionare il nome del gruppo di risorse, che in questo esempio è **myresourcegroup**.

2.  Nella pagina del gruppo di risorse selezionare **Elimina**. Digitare quindi nella casella il nome del gruppo di risorse, che in questo esempio è **myresourcegroup**, per confermare l'eliminazione e selezionare **Elimina**.

Per eliminare solo il server appena creato, seguire questa procedura:

1.  Individuare il server nel portale di Azure, se non è già aperto. Scegliere **Tutte le risorse** dal menu a sinistra nel portale di Azure. Cercare quindi il server creato.

2.  Nella pagina **Panoramica** selezionare **Elimina**. 

    ![Database di Azure per MySQL: eliminare il server](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)

3.  Verificare il nome del server che si vuole eliminare e visualizzare i database sottostanti interessati. Digitare nella casella il nome del server, che in questo esempio è **myserver4demo**. Selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Progettare il primo database di Azure per il database MySQL](./tutorial-design-database-using-portal.md)

