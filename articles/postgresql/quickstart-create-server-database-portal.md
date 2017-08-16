---
title: 'Portale di Azure: Creare un database di Azure per PostgreSQL | Microsoft Docs'
description: Guida di avvio rapido alla creazione e alla gestione di un database di Azure per il server PostgreSQL tramite l'interfaccia utente del portale di Azure.
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql-database
ms.custom: mvc
ms.topic: hero-article
ms.date: 08/04/2017
ms.translationtype: HT
ms.sourcegitcommit: 0aae2acfbf30a77f57ddfbaabdb17f51b6938fd6
ms.openlocfilehash: e89058a500aeb542ae4c7dc6bb4a9b9ae54db7f2
ms.contentlocale: it-it
ms.lasthandoff: 08/09/2017

---

# <a name="create-an-azure-database-for-postgresql-in-the-azure-portal"></a>Creare un database di Azure per PostgreSQL nel portale di Azure

Il database di Azure per PostgreSQL è un servizio gestito che consente di eseguire, gestire e ridimensionare database PostgreSQL a disponibilità elevata nel cloud. Questa guida di avvio rapido mostra come creare un database di Azure per il server PostgreSQL usando il portale di Azure.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure.

Accedere al [Portale di Azure](https://portal.azure.com).

## <a name="create-an-azure-database-for-postgresql"></a>Creare un database di Azure per PostgreSQL

Verrà creato un database di Azure per il server PostgreSQL con un set definito di [risorse di calcolo e di archiviazione](./concepts-compute-unit-and-storage.md). Il server viene creato all'interno di un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md).

Seguire questa procedura per creare un database di Azure per il server PostgreSQL:
1.  Fare clic sul pulsante **Nuovo** (+) nell'angolo superiore sinistro del portale di Azure.
2.  Selezionare **Database** nella pagina **Nuovo** e selezionare **Database di Azure per PostgreSQL** nella pagina **Database**.
 ![Database di Azure per PostgreSQL - Creare il database](./media/quickstart-create-database-portal/1-create-database.png)

3.  Compilare il modulo per i dettagli del nuovo server con le informazioni seguenti, come illustrato nell'immagine precedente:

    Impostazione|Valore consigliato|Descrizione
    ---|---|---
    Nome server |*mypgserver-20170401*|Scegliere un nome univoco per identificare il database di Azure per il server PostgreSQL. Il nome di dominio *postgres.database.azure.com* viene aggiunto al nome del server specificato a cui le applicazioni devono connettersi. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) e deve avere una lunghezza compresa tra 3 e 63 caratteri.
    Sottoscrizione|*Sottoscrizione in uso*|Sottoscrizione di Azure da usare per il server. Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui viene fatturata la risorsa.
    Gruppo di risorse|*myresourcegroup*| È possibile creare un nuovo nome di gruppo di risorse o usarne uno esistente nella sottoscrizione.
    Accesso amministratore server |*mylogin*| Creare un proprio account di accesso da usare quando ci si connette al server. Il nome di accesso dell'amministratore non può essere "azure_superuser", "azure_pg_admin", "admin", "administrator", "root", "guest" o "public" e non può iniziare con "pg_".
    Password |*A scelta dell'utente* | Creare una nuova password per l'account dell'amministratore del server. Deve avere una lunghezza compresa tra 8 e 128 caratteri. La password deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri (0-9) e caratteri non alfanumerici (!, $, #, % e così via).
    Percorso|*Area più vicina ai propri utenti*| Scegliere la località più vicina agli utenti.
    Versione di PostgreSQL|*Scegliere la versione più recente*| Scegliere la versione più recente a meno che non si abbiano requisiti specifici.
    Piano tariffario | **Basic**, **50 unità di calcolo** **50 GB** | Fare clic su **Piano tariffario** per specificare il livello di servizio e il livello delle prestazioni per il nuovo database. Scegliere il piano Basic nella parte superiore della scheda. Fare clic sul lato sinistro del dispositivo di scorrimento Unità di calcolo per impostare il valore sulla quantità minima disponibile per questa guida introduttiva. Fare clic su **OK** per salvare la selezione del piano tariffario. Vedere lo screenshot seguente.
    | Aggiungi al dashboard | Controllo | Selezionare l'opzione **Aggiungi al dashboard** per tenere facilmente traccia del server nella pagina iniziale del dashboard del portale di Azure.

  > [!IMPORTANT]
  > L'account di accesso amministratore server e la password qui specificati sono necessari per accedere al server e ai relativi database più avanti in questa guida introduttiva. Prendere nota di queste informazioni per usarle in seguito.

    ![Database di Azure per PostgreSQL: selezionare il piano tariffario](./media/quickstart-create-database-portal/2-service-tier.png)

4.  Fare clic su **Crea** per eseguire il provisioning del server. Il provisioning richiede qualche minuto, fino a un massimo di 20.

5.  Sulla barra degli strumenti fare clic su **Notifiche** per monitorare il processo di distribuzione.
 ![Database di Azure per PostgreSQL - Vedere le notifiche](./media/quickstart-create-database-portal/3-notifications.png)
   
  Per impostazione predefinita, il database **postgres** viene creato nel server. Il database [postgres](https://www.postgresql.org/docs/9.6/static/app-initdb.html) è un database predefinito che può essere usato da utenti, utilità e applicazioni di terze parti. 

## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server

Il database di Azure per il servizio PostgreSQL crea un firewall a livello di server. Questo firewall impedisce alle applicazioni e agli strumenti esterni di connettersi al server e ai database nel server, a meno che non venga creata una regola del firewall per aprire il firewall per indirizzi IP specifici. 

1.  Individuare il server al termine della distribuzione. Se necessario, è possibile cercarlo. Scegliere, ad esempio, **Tutte le risorse** dal menu a sinistra e digitare il nome del server (ad esempio, mypgserver-20170401), per cercare il server appena creato. Fare clic sul nome del server elencato nei risultati della ricerca. Verrà visualizzata la pagina Panoramica per il server, con le opzioni per un'ulteriore configurazione.
 
    ![Database di Azure per PostgreSQL - Cercare il server ](./media/quickstart-create-database-portal/4-locate.png)

2.  Nella pagina del server selezionare **Sicurezza connessione**. 
    ![Database di Azure per PostgreSQL: creare una regola del firewall](./media/quickstart-create-database-portal/5-firewall-2.png)

3.  Nell'intestazione **Regole del firewall** fare clic nella casella di testo vuota nella colonna **Nome regola** per iniziare la creazione della regola del firewall. 

    Per questa guida introduttiva, verranno consentiti tutti gli indirizzi IP nel server inserendo nella casella di testo di ogni colonna i valori seguenti:

    Nome regola | Indirizzo IP iniziale | Indirizzo IP finale 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. Nella barra degli strumenti superiore della pagina Sicurezza connessione fare clic su **Salva**. Fare quindi clic su **X** per chiudere la pagina Sicurezza connessione.

    > [!NOTE]
    > Il server PostgreSQL Azure comunica sulla porta 5432. Se si sta cercando di connettersi da una rete aziendale, il traffico in uscita sulla porta 5432 potrebbe non essere consentito dal firewall della rete. In tal caso, non sarà possibile connettersi al server del database SQL di Azure, a meno che il reparto IT non apra la porta 5432.
  >

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Quando si crea il database di Azure per il server PostgreSQL, viene creato un database predefinito **postgres**. Per connettersi al server di database, è necessario richiamare il nome del server completo e le credenziali di accesso amministratore. È possibile che si sia preso nota prima di questi valori durante la lettura dell'articolo introduttivo. Se non è stato fatto, è possibile trovare facilmente il nome del server e le informazioni di accesso nella pagina Panoramica del portale di Azure.

1. Aprire la pagina **Panoramica** del server. Annotare il **Nome server** e il **nome di accesso dell'amministratore del server**.
    Passare il puntatore del mouse su ogni campo per visualizzare l'icona di copia a destra del testo. Fare clic sull'icona di copia necessaria per copiare i valori desiderati.

 ![Database di Azure per PostgreSQL - Accesso dell'amministratore del server](./media/quickstart-create-database-portal/6-server-name.png)

## <a name="connect-to-postgresql-database-using-psql-in-cloud-shell"></a>Connettersi al database PostgreSQL tramite psql in Cloud Shell

Sono diverse le applicazioni che è possibile usare per connettere il database di Azure per il server PostgreSQL. Verrà usata prima l'utilità della riga di comando psql per illustrare la connessione al server.  È possibile usare un Web browser e Azure Cloud Shell, come descritto qui, senza dover installare software aggiuntivi. È anche possibile connettersi dall'utilità psql, se è già installata nel computer.

1. Avviare Azure Cloud Shell tramite l'icona del terminale nel riquadro di spostamento in alto.

   ![Database di Azure per PostgreSQL - Icona del terminale di Azure Cloud Shell](./media/quickstart-create-database-portal/7-cloud-console.png)

2. Azure Cloud Shell si apre nel browser, consentendo di digitare i comandi della shell Bash.

   ![Database di Azure per PostgreSQL - Prompt Bash di Azure Cloud Shell](./media/quickstart-create-database-portal/8-bash.png)

3. Al prompt di Cloud Shell connettersi a un database nel database di Azure per il server PostgreSQL digitando la riga di comandi psql al prompt verde.

    Il formato seguente è usato per connettersi a un Database di Azure per il server PostgreSQL con l'utilità [psql](https://www.postgresql.org/docs/9.6/static/app-psql.html):
    ```bash
    psql --host=<yourserver> --port=<port> --username=<server admin login> --dbname=<database name>
    ```

    Il comando seguente, ad esempio, stabilisce la connessione a un server di esempio:

    ```bash
    psql --host=mypgserver-20170401.postgres.database.azure.com --port=5432 --username=mylogin@mypgserver-20170401 --dbname=postgres
    ```

    Parametro psql |Valore consigliato|Descrizione
    ---|---|---
    --host | *nome del server* | Specificare il valore del nome del server usato prima quando si è creato il database di Azure per PostgreSQL. Il server di esempio visualizzato è mypgserver-20170401.postgres.database.azure.com. Usare il nome di dominio completo (\*.postgres.database.azure.com) come nell'esempio. Se non si ricorda il nome del server, seguire la sezione precedente per ottenere le informazioni di connessione. 
    --port | **5432** | Usare sempre la porta 5432 quando ci si connette al database di Azure per PostgreSQL. 
    --username | *nome di accesso amministratore server* |Digitare il nome utente di accesso amministratore server usato prima quando si è creato il database di Azure per PostgreSQL. Se non si ricorda il nome utente, seguire la sezione precedente per ottenere le informazioni di connessione.  Il formato è *username@servername*.
    --dbname | **postgres** | Usare il nome del database predefinito generato dal sistema *postgres* per la prima connessione. Successivamente viene creato il database personalizzato.

    Dopo l'esecuzione del comando psql, con i propri valori dei parametri, verrà chiesto di digitare la password amministratore server. Si tratta della stessa password specificata quando è stato creato il server. 

    Parametro psql |Valore consigliato|Descrizione
    ---|---|---
    password | *password amministratore dell'utente* | Tenere presente che i caratteri della password digitati non verranno visualizzati nel prompt di Bash. Dopo avere digitato tutti i caratteri, premere INVIO per eseguire l'autenticazione e la connessione.

4.  Dopo avere eseguito la connessione al server, creare un database vuoto al prompt digitando il comando seguente:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```

5.  Al prompt eseguire il comando seguente per cambiare la connessione passando al database appena creato **mypgsqldb**.
    ```bash
    \c mypgsqldb
    ```

6.  Digitare \q e quindi premere INVIO per chiudere psql. È possibile chiudere Azure Cloud Shell.

Ora che si è connessi al database di Azure per PostgreSQL e si è creato un database utente vuoto, continuare con la sezione successiva per connettersi usando un altro strumento comune, pgAdmin.

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Connettersi al database PostgreSQL tramite pgAdmin

Per connettersi al server PostgreSQL Azure tramite lo strumento dell'interfaccia utente grafica _pgAdmin_
1.  Avviare l'applicazione _pgAdmin_ nel computer client. È possibile installare _pgAdmin_ da http://www.pgadmin.org/.
2.  Scegliere **Add New Server** (Aggiungi nuovo server) dal menu **Quick Links** (Collegamenti rapidi).
3.  Nella finestra di dialogo **Create - Server** (Creazione server), nella scheda **General** (Generale), immettere un nome descrittivo univoco per il server, ad esempio **Azure PostgreSQL Server**.
![Strumento pgAdmin - Finestra di creazione del server](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)
4.  Nella finestra di dialogo **Create - Server** (Creazione server), nella scheda **Connection** (Connessione), usare le impostazioni specificate e fare clic su **Save** (Salva).
   ![pgAdmin - Finestra di creazione del server](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)
    - **Host Name/Address** (Nome host/Indirizzo): mypgserver-20170401.postgres.database.azure.com 
        - Nome completo del server.
    - **Port** (Porta): 5432
        - Il numero di porta usato dal server di database è 5432.
    - **Maintenance Database** (Database manutenzione): postgres 
        - Nome del database predefinito generato dal sistema.
    - **User Name** (Nome utente): mylogin@mypgserver-20170401 
        - L'account di accesso amministratore server (user@mypgserver) ottenuto in precedenza in questa guida di avvio rapido.
    - **Password**: password scelta al momento della creazione del server in precedenza in questa guida di avvio rapido.
    - **SSL Mode** (Modalità SSL): Require (Richiesta)
        - Per impostazione predefinita, tutti i server PostgreSQL Azure vengono creati con l'opzione di applicazione del protocollo SSL attivata. Per disattivare questa opzione, vedere le informazioni dettagliate nell'argomento relativo all'[applicazione di SSL](./concepts-ssl-connection-security.md).
5.  Fare clic su **Salva**.
6.  Nel riquadro sinistro Browser espandere i gruppi **Server**. Scegliere il server **Azure PostgreSQL Server**.
7.  In **Server** scegliere il server a cui si è connessi e quindi scegliere **Databases** (Database) sotto il server. 
8.  Fare clic con il pulsante destro del mouse su **Databases** (Database) per creare un database.
9.  Scegliere il nome di database **mypgsqldb** e il proprietario, rappresentato dall'account di accesso dell'amministratore del server **mylogin**.
10. Fare clic su **Save** (Salva) per creare un database vuoto.
11. In **Browser**, espandere **Server**. Espandere il server creato e visualizzare il database **mypgsqldb** sotto di esso.
 ![pgAdmin - Finestra di creazione del database](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>Pulire le risorse
Pulire tutte le risorse create nella guida di avvio rapido eliminando il [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md).

> [!TIP]
> Altre guide di avvio rapido di questa raccolta si basano sulla presente guida di avvio rapido. Se si prevede di continuare a usare le guide di avvio rapido successive, non pulire le risorse create in questa guida di avvio rapido. Se non si prevede di continuare, seguire questa procedura per eliminare tutte le risorse create da questa guida di avvio rapido nel portale di Azure.

1.  Scegliere **Gruppi di risorse** dal menu a sinistra nel portale di Azure e quindi fare clic su **myresourcegroup**.
2.  Nella pagina del gruppo di risorse fare clic su **Elimina**, digitare **myresourcegroup** nella casella di testo e quindi fare clic su Elimina.

Se si vuole eliminare solo il server appena creato:
1.  Nel menu a sinistra nel portale di Azure fare clic su Server PostgreSQL e quindi cercare il server appena creato
2.  Nella pagina Panoramica fare clic sul pulsante Elimina nel riquadro superiore ![Database di Azure per PostgreSQL - Eliminare il server](./media/quickstart-create-database-portal/12-delete.png)
3.  Verificare il nome del server che si vuole eliminare e visualizzare i database interessati sotto al server. Digitare **mypgserver-20170401** nella casella di testo e quindi fare clic su Elimina.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire la migrazione del database usando le funzionalità di esportazione e importazione](./howto-migrate-using-export-and-import.md)

