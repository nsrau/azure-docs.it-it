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
ms.date: 08/10/2017
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 8bba5a97fdc4bbc15fe996ee68daf5b796d1bfac
ms.contentlocale: it-it
ms.lasthandoff: 08/16/2017

---

# <a name="create-an-azure-database-for-postgresql-in-the-azure-portal"></a>Creare un database di Azure per PostgreSQL nel portale di Azure

Il database di Azure per PostgreSQL è un servizio gestito che consente di eseguire, gestire e ridimensionare database PostgreSQL a disponibilità elevata nel cloud. Questa guida introduttiva illustra come creare un database di Azure per il server PostgreSQL con il portale di Azure in circa cinque minuti.

Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure.
Aprire il Web browser e passare al [portale di Microsoft Azure](https://portal.azure.com/). Immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.

## <a name="create-an-azure-database-for-postgresql"></a>Creare un database di Azure per PostgreSQL

Verrà creato un database di Azure per il server PostgreSQL con un set definito di [risorse di calcolo e di archiviazione](./concepts-compute-unit-and-storage.md). Il server viene creato all'interno di un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md).

Seguire questa procedura per creare un database di Azure per il server PostgreSQL:
1.  Fare clic sul pulsante **Nuovo** (+) nell'angolo superiore sinistro del portale di Azure.
2.  Selezionare **Database** nella pagina **Nuovo** e selezionare **Database di Azure per PostgreSQL** nella pagina **Database**.
 ![Database di Azure per PostgreSQL - Creare il database](./media/quickstart-create-database-portal/1-create-database.png)

3.  Compilare il modulo per i dettagli del nuovo server con le informazioni seguenti, come illustrato nell'immagine precedente:

    Impostazione|Valore consigliato|Descrizione
    ---|---|---
    Nome server |*mypgserver-20170401*|Scegliere un nome univoco per identificare il database di Azure per il server PostgreSQL. Il nome di dominio *postgres.database.azure.com* viene aggiunto al nome del server specificato per la connessione delle applicazioni. Il nome del server può contenere solo lettere minuscole, numeri e il segno meno (-) e deve avere una lunghezza compresa tra 3 e 63 caratteri.
    Sottoscrizione|*Sottoscrizione in uso*|Sottoscrizione di Azure da usare per il server. Se si possiedono più sottoscrizioni, scegliere quella appropriata in cui viene fatturata la risorsa.
    Gruppo di risorse|*myresourcegroup*| È possibile creare un nuovo nome di gruppo di risorse o usarne uno esistente nella sottoscrizione.
    Accesso amministratore server |*mylogin*| Creare un proprio account di accesso da usare per la connessione al server. Il nome di accesso dell'amministratore non può essere "azure_superuser", "azure_pg_admin", "admin", "administrator", "root", "guest" o "public" e non può iniziare con "pg_".
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

1.  Individuare il server al termine della distribuzione. Se necessario, è possibile eseguire una ricerca. Scegliere **Tutte le risorse** dal menu a sinistra e digitare il nome del server, che in questo esempio è *mypgserver-20170401*, per cercare il server appena creato. Fare clic sul nome del server elencato nei risultati della ricerca. Si apre la pagina **Panoramica** del server in cui vengono fornite le opzioni per una configurazione aggiuntiva.
 
    ![Database di Azure per PostgreSQL: cercare il nome del server](./media/quickstart-create-database-portal/4-locate.png)

2.  Nella pagina del server selezionare **Sicurezza connessione**. 
    ![Database di Azure per PostgreSQL: creare una regola del firewall](./media/quickstart-create-database-portal/5-firewall-2.png)

3.  Sotto l'intestazione **Regole del firewall** fare clic nella casella di testo vuota nella colonna **Nome regola** per iniziare a creare la regola del firewall. 

    Per questa guida introduttiva, verranno consentiti tutti gli indirizzi IP nel server inserendo nella casella di testo di ogni colonna i valori seguenti:

    Nome regola | Indirizzo IP iniziale | Indirizzo IP finale 
    ---|---|---
    AllowAllIps |  0.0.0.0 | 255.255.255.255

4. Sulla barra degli strumenti superiore della pagina Sicurezza connessione fare clic su **Salva**. Prima di continuare, attendere alcuni istanti la notifica che segnala che l'aggiornamento della sicurezza della connessione è stato completato.

    > [!NOTE]
    > Le connessioni al database di Azure per il server PostgreSQL comunicano sulla porta 5432. Se si sta cercando di connettersi da una rete aziendale, il traffico in uscita sulla porta 5432 potrebbe non essere consentito dal firewall della rete. In questo caso, non sarà possibile connettersi al server a meno che il reparto IT non apra la porta 5432.
    >

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Quando si crea il database di Azure per il server PostgreSQL, viene creato un database predefinito **postgres**. Per connettersi al server di database, è necessario ricordare il nome del server completo e le credenziali di accesso amministratore. È possibile che si sia preso nota prima di questi valori durante la lettura dell'articolo introduttivo. In caso contrario, è possibile trovare facilmente il nome del server e le informazioni di accesso nella pagina Panoramica del server nel portale di Azure.

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
    --host | *nome del server* | Specificare il valore del nome del server usato prima quando si è creato il database di Azure per PostgreSQL. Il server di esempio visualizzato è mypgserver-20170401.postgres.database.azure.com. Usare il nome di dominio completo (\*.postgres.database.azure.com) come nell'esempio. Se non si ricorda il nome del server, seguire la procedura illustrata nella sezione precedente per ottenere le informazioni di connessione. 
    --port | **5432** | Usare sempre la porta 5432 quando ci si connette al database di Azure per PostgreSQL. 
    --username | *nome di accesso amministratore server* |Digitare il nome utente di accesso amministratore server usato prima quando si è creato il database di Azure per PostgreSQL. Se non si ricorda il nome utente, seguire la procedura illustrata nella sezione precedente per ottenere le informazioni di connessione.  Il formato è *username@servername*.
    --dbname | **postgres** | Usare il nome del database predefinito generato dal sistema *postgres* per la prima connessione. Successivamente viene creato il database personalizzato.

    Dopo l'esecuzione del comando psql con i propri valori dei parametri, verrà richiesto di digitare la password amministratore server. Si tratta della stessa password specificata al momento della creazione del server. 

    Parametro psql |Valore consigliato|Descrizione
    ---|---|---
    password | *password amministratore dell'utente* | Si noti che i caratteri della password digitati non verranno visualizzati nel prompt di Bash. Dopo avere digitato tutti i caratteri, premere INVIO per eseguire l'autenticazione e la connessione.

    Dopo che è stata stabilita la connessione, l'utilità psql visualizza un prompt postgres in cui è possibile digitare comandi sql. Nell'output della connessione iniziale potrebbe essere visualizzato un avviso perché la versione di psql in Azure Cloud Shell potrebbe essere diversa dalla versione del database di Azure per il server PostgreSQL. 
    
    Output di psql esempio:
    ```bash
    psql (9.5.7, server 9.6.2)
    WARNING: psql major version 9.5, server major version 9.6.
        Some psql features might not work.
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
    Type "help" for help.
   
    postgres=> 
    ```

    > [!TIP]
    > Se il firewall non è configurato per consentire l'indirizzo IP di Azure Cloud Shell, verrà visualizzato l'errore seguente:
    > 
    > "psql: FATAL:  no pg_hba.conf entry for host "138.91.195.82", user "mylogin", database "postgres", SSL on FATAL:  SSL connection is required. Please specify SSL options and retry.
    > 
    > Per risolvere l'errore, assicurarsi che la configurazione del server corrisponda alla procedura illustrata nella sezione *Configurare una regola del firewall a livello di server* di questo articolo.

4.  Creare un database vuoto al prompt digitando il comando seguente:
    ```bash
    CREATE DATABASE mypgsqldb;
    ```
    Il completamento del comando potrebbe richiedere alcuni istanti. 

5.  Al prompt eseguire il comando seguente per cambiare la connessione passando al database appena creato **mypgsqldb**.
    ```bash
    \c mypgsqldb
    ```

6.  Digitare \q e quindi premere INVIO per chiudere psql. Al termine, è possibile chiudere Azure Cloud Shell.

Ora che si è connessi al database di Azure per PostgreSQL e si è creato un database utente vuoto, continuare con la sezione successiva per connettersi usando un altro strumento comune, pgAdmin.

## <a name="connect-to-postgresql-database-using-pgadmin"></a>Connettersi al database PostgreSQL tramite pgAdmin

Per connettersi al server PostgreSQL Azure tramite lo strumento dell'interfaccia utente grafica _pgAdmin_
1.  Avviare l'applicazione _pgAdmin_ nel computer client. È possibile installare _pgAdmin_ da http://www.pgadmin.org/.
2.  Fare clic sull'icona **Add New Server** (Aggiungi nuovo server) nella sezione **Quick Links** (Collegamenti rapidi) al centro della pagina del dashboard.
3.  Nella finestra di dialogo **Create - Server** (Creazione server), nella scheda **General** (Generale), immettere un nome descrittivo univoco per il server, ad esempio **Azure PostgreSQL Server**.
![Strumento pgAdmin - Finestra di creazione del server](./media/quickstart-create-database-portal/9-pgadmin-create-server.png)
4.  Nella finestra di dialogo **Create - Server** (Creazione server), nella scheda **Connection** (Connessione), usare le impostazioni specificate e fare clic su **Save** (Salva).
   ![pgAdmin - Finestra di creazione del server](./media/quickstart-create-database-portal/10-pgadmin-create-server.png)

    Parametro di pgAdmin |Valore consigliato|Descrizione
    ---|---|---
    Host Name/Address (Nome host/indirizzo) | *nome del server* | Specificare il valore del nome del server usato prima quando si è creato il database di Azure per PostgreSQL. Il server di esempio visualizzato è mypgserver-20170401.postgres.database.azure.com. Usare il nome di dominio completo (\*.postgres.database.azure.com) come nell'esempio. Se non si ricorda il nome del server, seguire la procedura illustrata nella sezione precedente per ottenere le informazioni di connessione. 
    Porta | **5432** | Usare sempre la porta 5432 quando ci si connette al database di Azure per PostgreSQL.  
    Maintenance Database (Database manutenzione) | **postgres** | Usare il nome del database predefinito generato dal sistema *postgres*.
    User Name | *nome di accesso amministratore server* | Digitare il nome utente di accesso amministratore server specificato in precedenza al momento della creazione del database di Azure per PostgreSQL. Se non si ricorda il nome utente, seguire la procedura illustrata nella sezione precedente per ottenere le informazioni di connessione. Il formato è *username@servername*.
    Password | *password amministratore dell'utente* |  Password scelta in precedenza in questa guida introduttiva durante la creazione del server.
    Ruolo | *lasciare vuoto* | Non è necessario specificare un nome di ruolo in questa fase. Lasciare vuoto questo campo.
    SSL Mode (Modalità SSL) | Valore richiesto | Per impostazione predefinita, tutti i server PostgreSQL Azure vengono creati con l'opzione di applicazione del protocollo SSL attivata. Per disattivare questa opzione, vedere le informazioni dettagliate nell'argomento relativo all'[applicazione di SSL](./concepts-ssl-connection-security.md).
    
5.  Fare clic su **Salva**.
6.  Nel riquadro sinistro Browser espandere il nodo **Servers** (Server). Scegliere il server, ad esempio **Azure PostgreSQL Server**, e fare clic per connettersi.
7. Espandere il nodo del server e quindi il nodo **Databases** (Database) sottostante. L'elenco dovrebbe includere il database *postgres* esistente e qualsiasi database utente appena creato, come il database *mypgsqldb* creato nella sezione precedente. Si noti che con Database di Azure per PostgreSQL è possibile creare più database per server.
8. Fare clic con il pulsante destro del mouse su **Databases** (Database), scegliere il menu **Create** (Crea) e fare clic su **Database**.
9.  Digitare un nome di database a propria scelta nel campo **Database**, ad esempio *mypgsqldb* come illustrato di seguito. 
10. Selezionare il **proprietario** del database nella casella di riepilogo a discesa. Scegliere il nome di accesso amministratore server, che in questo esempio è *mylogin*.
10. Fare clic su **Save** (Salva) per creare un nuovo database vuoto.
11. Il database creato verrà visualizzato nel riquadro **Browser** nell'elenco dei database sotto il nome del server.
 ![pgAdmin - Finestra di creazione del database](./media/quickstart-create-database-portal/11-pgadmin-database.png)


## <a name="clean-up-resources"></a>Pulire le risorse
Eseguire la pulizia delle risorse create nel corso della guida introduttiva eliminando il [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md), in modo da includere tutte le risorse nel gruppo, oppure solo la risorsa server, se si vogliono mantenere intatte le altre.

> [!TIP]
> Altre guide di avvio rapido di questa raccolta si basano sulla presente guida di avvio rapido. Se si prevede di continuare a usare le guide di avvio rapido successive, non pulire le risorse create in questa guida di avvio rapido. Se non si intende continuare, seguire questa procedura per eliminare le risorse create con questa guida introduttiva nel portale di Azure.

Per eliminare l'intero gruppo di risorse, incluso il server appena creato:
1.  Individuare il gruppo di risorse nel portale di Azure. Scegliere **Gruppi di risorse** dal menu a sinistra nel portale di Azure e quindi fare clic sul nome del gruppo di risorse, che in questo esempio è **myresourcegroup**.
2.  Nella pagina del gruppo di risorse fare clic su **Elimina**. Digitare quindi nella casella di testo il nome del gruppo di risorse, che in questo esempio è **myresourcegroup**, per confermare l'eliminazione e quindi fare clic su **Elimina**.

In alternativa, per eliminare il server appena creato:
1.  Individuare il server nel portale di Azure, se non è già aperto. Scegliere **Tutte le risorse** dal menu a sinistra nel portale di Azure e quindi cercare il server creato.
2.  Nella pagina **Panoramica** fare clic sul pulsante **Elimina** nel riquadro superiore.
![Database di Azure per PostgreSQL: eliminare il server](./media/quickstart-create-database-portal/12-delete.png)
3.  Verificare il nome del server che si vuole eliminare e visualizzare i database sottostanti interessati. Digitare nella casella di testo il nome del server, che in questo esempio è **mypgserver-20170401**, e quindi fare clic su **Elimina**.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire la migrazione del database usando le funzionalità di esportazione e importazione](./howto-migrate-using-export-and-import.md)

