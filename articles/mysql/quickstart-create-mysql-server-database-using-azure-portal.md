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
ms.date: 08/15/2017
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 829c7e73cbf22d866bbd6fd54edc7a954ad7174c
ms.contentlocale: it-it
ms.lasthandoff: 08/16/2017

---

# <a name="create-an-azure-database-for-mysql-server-using-azure-portal"></a>Creare un database di Azure per il server MySQL tramite il portale di Azure
Il database di Azure per MySQL è un servizio gestito che consente di eseguire, gestire e scalare dei database MySQL a disponibilità elevata nel cloud. Questa guida introduttiva illustra come creare un database di Azure per il server MySQL con il portale di Azure in circa cinque minuti. 

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="log-in-to-azure"></a>Accedere ad Azure
Aprire il Web browser e passare al [portale di Microsoft Azure](https://portal.azure.com/). Immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.

## <a name="create-azure-database-for-mysql-server"></a>Creare un database di Azure per il server MySQL
Verrà creato un database di Azure per MySQL con un set definito di [risorse di calcolo e di archiviazione](./concepts-compute-unit-and-storage.md). Il server viene creato all'interno di un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md).

Seguire questa procedura per creare un database di Azure per il server MySQL:

1. Fare clic sul pulsante **Nuovo** (+) nell'angolo superiore sinistro del portale di Azure.

2. Selezionare **Database** nella pagina **Nuovo** e quindi **Database di Azure per MySQL** nella pagina **Database**. È anche possibile digitare **MySQL** nella casella di ricerca della pagina Nuovo per trovare il servizio.
![Portale di Azure - Nuovo - Database - MySQL](./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png)

3. Compilare il modulo per i dettagli del nuovo server con le informazioni seguenti, come illustrato nell'immagine precedente:

    **Impostazione** | **Valore consigliato** | **Descrizione campo** 
    ---|---|---
    Nome server | myserver4demo | Scegliere un nome univoco per identificare il database di Azure per il server MySQL. Il nome di dominio *mysql.database.azure.com* viene aggiunto al nome del server specificato per la connessione delle applicazioni. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) e deve avere una lunghezza compresa tra 3 e 63 caratteri.
    Sottoscrizione | Sottoscrizione in uso | Sottoscrizione di Azure da usare per il server. Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui viene fatturata la risorsa.
    Gruppo di risorse | myresourcegroup | È possibile creare un nuovo nome di gruppo di risorse o usarne uno esistente nella sottoscrizione.
    Accesso amministratore server | myadmin | Creare un proprio account di accesso da usare per la connessione al server. Il nome di accesso dell'amministratore non può essere "azure_superuser", "admin", "administrator", "root", "guest" o "public".
    Password | *A scelta dell'utente* | Creare una nuova password per l'account dell'amministratore del server. Deve avere una lunghezza compresa tra 8 e 128 caratteri. La password deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri (0-9) e caratteri non alfanumerici (!, $, #, % e così via).
    Conferma password | *A scelta dell'utente*| Confermare la password dell'account amministratore.
    Percorso | *Area più vicina ai propri utenti*| Scegliere la località più vicina agli utenti o alle altre applicazioni Azure.
    Versione | *Scegliere la versione più recente*| Scegliere la versione più recente a meno che non si abbiano requisiti specifici.
    Piano tariffario | **Basic**, **50 unità di calcolo** **50 GB** | Fare clic su **Piano tariffario** per specificare il livello di servizio e il livello delle prestazioni per il nuovo database. Scegliere **Piano Basic** nella parte superiore della scheda. Fare clic sul lato sinistro del dispositivo di scorrimento **Unità di calcolo** per impostare il valore sulla quantità minima disponibile per questa guida introduttiva. Fare clic su **OK** per salvare la selezione del piano tariffario. Vedere lo screenshot seguente.
    Aggiungi al dashboard | Controllo | Selezionare l'opzione **Aggiungi al dashboard** per tenere facilmente traccia del server nella pagina iniziale del dashboard del portale di Azure.

    > [!IMPORTANT]
    > L'account di accesso amministratore server e la password qui specificati sono necessari per accedere al server e ai relativi database più avanti in questa guida introduttiva. Prendere nota di queste informazioni per usarle in seguito.
    > 

    ![Portale di Azure - Creazione del database per MySQL specificando l'input del modulo necessario](./media/quickstart-create-mysql-server-database-using-azure-portal/3_create-server.png)

4.  Fare clic su **Crea** per eseguire il provisioning del server. Il provisioning richiede qualche minuto, fino a un massimo di 20.
   
5.  Sulla barra degli strumenti fare clic su **Notifiche** (icona a forma di campana) per monitorare il processo di distribuzione.

## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server

Il database di Azure per il servizio MySQL crea un firewall a livello di server. Questo firewall impedisce alle applicazioni e agli strumenti esterni di connettersi al server e ai database nel server, a meno che non venga creata una regola del firewall per aprire il firewall per indirizzi IP specifici. 

1.  Individuare il server al termine della distribuzione. Se necessario, è possibile eseguire una ricerca. Scegliere **Tutte le risorse** dal menu a sinistra e digitare il nome del server, che in questo esempio è *myserver4demo*, per cercare il server appena creato. Fare clic sul nome del server elencato nei risultati della ricerca. Si apre la pagina **Panoramica** del server in cui vengono fornite le opzioni per una configurazione aggiuntiva.

2. Nella pagina del server selezionare **Sicurezza connessione**.

3.  Sotto l'intestazione **Regole del firewall** fare clic nella casella di testo vuota nella colonna **Nome regola** per iniziare a creare la regola del firewall. 

    Per questa guida introduttiva verranno consentiti tutti gli indirizzi IP nel server inserendo nella casella di testo di ogni colonna i valori seguenti:

    Nome regola | Indirizzo IP iniziale | Indirizzo IP finale 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. Sulla barra degli strumenti superiore della pagina **Sicurezza connessione** fare clic su **Salva**. Prima di continuare, attendere alcuni istanti la notifica che segnala che l'aggiornamento della sicurezza della connessione è stato completato.

    > [!NOTE]
    > Le connessioni al database di Azure per MySQL comunicano sulla porta 3306. Se si sta tentando di connettersi da una rete aziendale, il traffico in uscita attraverso la porta 3306 potrebbe non essere autorizzato dal firewall della rete. In questo caso, non sarà possibile connettersi al server a meno che il reparto IT non apra la porta 3306.
    > 

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione
Per connettersi al server di database, è necessario ricordare il nome del server completo e le credenziali di accesso amministratore. È possibile che si sia preso nota prima di questi valori durante la lettura dell'articolo introduttivo. In caso contrario, è possibile trovare facilmente il nome del server e le informazioni di accesso nella pagina **Proprietà** o nella pagina **Panoramica** del server nel portale di Azure.

1. Aprire la pagina **Panoramica** del server. Annotare il **Nome server** e il **nome di accesso dell'amministratore del server**. 
    Passare il puntatore del mouse su ogni campo per visualizzare l'icona di copia a destra del testo. Fare clic sull'icona di copia necessaria per copiare i valori desiderati.

    In questo esempio il nome del server è *myserver4demo.mysql.database.azure.com* e l'account di accesso dell'amministratore del server è *myadmin@myserver4demo*.

## <a name="connect-to-mysql-using-mysql-command-line-tool"></a>Connettersi a MySQL usando lo strumento da riga di comando mysql
Per connettersi al database di Azure per il server MySQL è possibile usare diverse applicazioni. Prima di tutto verrà usato lo strumento da riga di comando [mysql](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) per illustrare la connessione al server.  È possibile usare un Web browser e Azure Cloud Shell, come descritto qui, senza dover installare software aggiuntivi. È possibile connettersi anche dall'utilità mysql eventualmente installata in locale nel computer in uso.

1. Avviare Azure Cloud Shell tramite l'icona del terminale (>_) nella parte superiore destra della pagina Web del portale di Azure.

2. Azure Cloud Shell si apre nel browser, consentendo di digitare i comandi della shell Bash.

    ![Prompt dei comandi - Esempio di riga di comando mysql](./media/quickstart-create-mysql-server-database-using-azure-portal/7_connect-to-server.png)

3. Al prompt di Cloud Shell connettersi al database di Azure per il server MySQL digitando la riga di comando mysql al prompt verde.

    Per connettersi a un database di Azure per il server MySQL con l'utilità mysql viene usato il formato seguente:
    ```bash
    mysql --host <yourserver> --user <server admin login> --password
    ```

    Ad esempio, il comando seguente stabilisce la connessione al server di esempio:
    ```azurecli-interactive
    mysql --host myserver4demo.mysql.database.azure.com --user myadmin@myserver4demo --password
    ```

    Parametro di mysql |Valore consigliato|Descrizione
    ---|---|---
    --host | *nome del server* | Specificare il valore del nome del server usato in precedenza al momento della creazione del database di Azure per MySQL. Il server di esempio visualizzato è myserver4demo.mysql.database.azure.com. Usare il nome di dominio completo (\*.mysql.database.azure.com) come nell'esempio. Se non si ricorda il nome del server, seguire la procedura illustrata nella sezione precedente per ottenere le informazioni di connessione. 
    --user | *nome di accesso amministratore server* |Digitare il nome utente di accesso amministratore server specificato in precedenza al momento della creazione del database di Azure per MySQL. Se non si ricorda il nome utente, seguire la procedura illustrata nella sezione precedente per ottenere le informazioni di connessione.  Il formato è *username@servername*.
    --password | *attendere finché non viene richiesta* | Dopo l'esecuzione del comando verrà richiesto di immettere la password. Quando viene visualizzata la richiesta, digitare la stessa password specificata al momento della creazione del server.  Si noti che i caratteri della password digitati non verranno visualizzati nel prompt di Bash durante la digitazione. Dopo avere digitato tutti i caratteri, premere INVIO per eseguire l'autenticazione e la connessione.

   Dopo che è stata stabilita la connessione, l'utilità mysql visualizza un prompt `mysql>` per la digitazione dei comandi. 

    Output di mysql di esempio:
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
    > Per risolvere l'errore, assicurarsi che la configurazione del server corrisponda alla procedura illustrata nella sezione *Configurare una regola del firewall a livello di server* di questo articolo.

4. Visualizzare lo stato del server per verificare che la connessione sia funzionale. Digitare `status` al prompt mysql> dopo che è stata eseguita la connessione.
    ```sql
    status
    ```

   > [!TIP]
   > Per altri comandi, vedere il capitolo 4.5.1 di [MySQL 5.7 Reference Manual](https://dev.mysql.com/doc/refman/5.7/en/mysql.html) (Manuale di riferimento di MySQL 5.7).

5.  Creare un database vuoto al prompt mysql> digitando il comando seguente:
    ```sql
    CREATE DATABASE quickstartdb;
    ```
    Il completamento del comando potrebbe richiedere alcuni istanti. 

    In un database di Azure per il server MySQL è possibile creare uno o più database. È possibile scegliere di creare un singolo database per ogni server per usare tutte le risorse o creare più database per condividere le risorse. Il numero di database che è possibile creare è illimitato, ma più database condividono le stesse risorse del server. 

6. Elencare i database al prompt mysql> digitando il comando seguente:

    ```sql
    SHOW DATABASES;
    ```

7.  Digitare `\q` e quindi premere INVIO per uscire dallo strumento mysql. Al termine, è possibile chiudere Azure Cloud Shell.

Ora che si è connessi al database di Azure per MySQL e si è creato un database utente vuoto, continuare con la sezione successiva per ripetere un esercizio simile per connettersi allo stesso server usando un altro strumento comune, MySQL Workbench.

## <a name="connect-to-the-server-using-the-mysql-workbench-gui-tool"></a>Connettersi al server tramite lo strumento dell'interfaccia utente grafica MySQL Workbench
Per connettersi al server MySQL di Azure con lo strumento dell'interfaccia utente grafica MySQL Workbench:

1.  Avviare l'applicazione MySQL Workbench nel computer client. È possibile scaricare e installare MySQL Workbench da [qui](https://dev.mysql.com/downloads/workbench/).

2.  Nella finestra di dialogo **Setup New Connection** (Configura nuova connessione) immettere le informazioni seguenti nella scheda **Parameters** (Parametri):

    ![Setup New Connection (Configura nuova connessione)](./media/quickstart-create-mysql-server-database-using-azure-portal/setup-new-connection.png)

    | **Impostazione** | **Valore consigliato** | **Descrizione campo** |
    |---|---|---|
    |   Connection Name (Nome connessione) | Demo Connection | Specificare un'etichetta per la connessione. |
    | Connection Method (Metodo di connessione) | Standard (TCP/IP) | Standard (TCP/IP) è sufficiente. |
    | Nome host | *nome del server* | Specificare il valore del nome del server usato in precedenza al momento della creazione del database di Azure per MySQL. Il server di esempio visualizzato è myserver4demo.mysql.database.azure.com. Usare il nome di dominio completo (\*.mysql.database.azure.com) come nell'esempio. Se non si ricorda il nome del server, seguire la procedura illustrata nella sezione precedente per ottenere le informazioni di connessione.  |
    | Porta | 3306 | Usare sempre la porta 3306 per la connessione al database di Azure per MySQL. |
    | Username |  *nome di accesso amministratore server* | Digitare il nome utente di accesso amministratore server specificato in precedenza al momento della creazione del database di Azure per MySQL. Il nome utente dell'esempio è myadmin@myserver4demo. Se non si ricorda il nome utente, seguire la procedura illustrata nella sezione precedente per ottenere le informazioni di connessione. Il formato è *username@servername*.
    | Password | Immettere la password. | Fare clic sul pulsante Store in Vault... (Archivia nell'insieme di credenziali) per salvare la password. |

    Fare clic su **Test Connection** (Test connessione) per verificare che tutti i parametri siano configurati correttamente. Fare clic su OK per salvare la connessione. 

    > [!NOTE]
    > SSL viene applicato per impostazione predefinita nel server e richiede una configurazione aggiuntiva per la corretta connessione. Vedere [Configure SSL connectivity in your application to securely connect to Azure Database for MySQL](./howto-configure-ssl.md) (Configurare la connettività SSL nell'applicazione per connettersi in modo sicuro al database di Azure per MySQL).  Se si vuole disabilitare SSL per questa guida introduttiva, visitare il portale di Azure e fare clic sulla pagina Sicurezza connessione per disabilitare l'interruttore Imponi connessione SSL.

## <a name="clean-up-resources"></a>Pulire le risorse
Eseguire la pulizia delle risorse create nel corso della guida introduttiva eliminando il [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md), in modo da includere tutte le risorse nel gruppo, oppure solo la risorsa server, se si vogliono mantenere intatte le altre.

> [!TIP]
> Altre guide introduttive della raccolta si basano su questa. Se si prevede di continuare a usare le guide di avvio rapido successive, non pulire le risorse create in questa guida di avvio rapido. Se non si prevede di continuare, seguire questa procedura per eliminare tutte le risorse create da questa guida di avvio rapido nel portale di Azure.
>

Per eliminare l'intero gruppo di risorse, incluso il server appena creato:
1.  Individuare il gruppo di risorse nel portale di Azure. Scegliere **Gruppi di risorse** dal menu a sinistra nel portale di Azure e quindi fare clic sul nome del gruppo di risorse, che in questo esempio è **myresourcegroup**.
2.  Nella pagina del gruppo di risorse fare clic su **Elimina**. Digitare quindi nella casella di testo il nome del gruppo di risorse, che in questo esempio è **myresourcegroup**, per confermare l'eliminazione e quindi fare clic su **Elimina**.

In alternativa, per eliminare il server appena creato:
1.  Individuare il server nel portale di Azure, se non è già aperto. Scegliere **Tutte le risorse** dal menu a sinistra nel portale di Azure e quindi cercare il server creato.
2.  Nella pagina **Panoramica** fare clic sul pulsante **Elimina** nel riquadro superiore.
![Database di Azure per MySQL: eliminare il server](./media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png)
3.  Verificare il nome del server che si vuole eliminare e visualizzare i database sottostanti interessati. Digitare nella casella di testo il nome del server, che in questo esempio è **myserver4demo**, e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Progettare il primo database di Azure per il database MySQL](./tutorial-design-database-using-portal.md)


