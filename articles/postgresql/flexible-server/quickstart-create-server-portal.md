---
title: 'Avvio rapido: Creare un server - Portale di Azure - Database di Azure per PostgreSQL - Server flessibile'
description: Guida di avvio rapido alla creazione e alla gestione di un server flessibile di Database di Azure per PostgreSQL tramite l'interfaccia utente del portale di Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/22/2020
ms.openlocfilehash: 25c3f875717c9c064af0ce27bdab735db67d2f5d
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "90944111"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---flexible-server-in-the-azure-portal"></a>Avvio rapido: Creare un server flessibile di Database di Azure per PostgreSQL nel portale di Azure

> [!IMPORTANT]
> Il server flessibile di Database di Azure per PostgreSQL è disponibile in anteprima

Il database di Azure per PostgreSQL è un servizio gestito usato per eseguire, gestire e ridimensionare database PostgreSQL a disponibilità elevata nel cloud. Questa guida di avvio rapido illustra come creare un server flessibile di Database di Azure per PostgreSQL in circa cinque minuti con il portale di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Aprire il Web browser e passare al [portale](https://portal.azure.com/). Immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.

## <a name="create-an-azure-database-for-postgresql-server"></a>Creare un database di Azure per il server PostgreSQL

Un server Database di Azure per PostgreSQL viene creato con un set definito di [risorse di calcolo e di archiviazione](./concepts-compute-storage.md). Il server viene creato all'interno di un [gruppo di risorse di Azure](https://docs.microsoft.com/azure/azure-resource-manager/management/overview).

Per creare un database di Azure per il server PostgreSQL, seguire questa procedura:

1. Nell'angolo in alto a sinistra del portale selezionare **Crea una risorsa** (+).

2. Selezionare **Database** > **Database di Azure per PostgreSQL**.

    :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="Database di Azure per PostgreSQL nel menu":::

3. Selezionare l'opzione di distribuzione **Server flessibile**.

   :::image type="content" source="./media/quickstart-create-database-portal/2-select-deployment-option.png" alt-text="Database di Azure per PostgreSQL nel menu":::

4. Compilare il modulo **Informazioni di base** con le informazioni seguenti:

    :::image type="content" source="./media/quickstart-create-database-portal/3-create-basics.png" alt-text="Database di Azure per PostgreSQL nel menu":::

    Impostazione|Valore consigliato|Descrizione
    ---|---|---
    Subscription|Nome della sottoscrizione utente|Sottoscrizione di Azure da usare per il server. Se si hanno più sottoscrizioni, scegliere quella in cui si desidera che venga fatturata la risorsa.
    Resource group|*myresourcegroup*| Nuovo nome di gruppo di risorse o uno esistente nella sottoscrizione.
    Nome server |*mydemoserver*|Nome univoco per identificare il database di Azure per il server PostgreSQL. Al nome del server specificato viene aggiunto il nome di dominio *postgres.database.azure.com*. Il server può contenere solo lettere minuscole, numeri e il segno meno (-). Deve contenere almeno da 3 a 63 caratteri.
    Nome utente amministratore |*myadmin*| Account di accesso da usare per la connessione al server. Il nome di accesso dell'amministratore non può essere **azure_superuser**, **azure_pg_admin**, **admin**, **administrator**, **root**, **guest** o **public**. Non può iniziare con **pg_** .
    Password |Password| Nuova password per l'account amministratore del server. Deve contenere tra 8 e 128 caratteri. La password deve contenere caratteri di tre delle categorie seguenti: lettere maiuscole, lettere minuscole, numeri (da 0 a 9) e caratteri non alfanumerici (!, $, #, % e così via).
    Location|Area più vicina ai propri utenti| Località più vicina agli utenti.
    Versione|La versione principale più recente| La versione principale più recente di PostgreSQL, a meno che non si abbiano requisiti specifici diversi.
    Calcolo e archiviazione | **Utilizzo generico**, **4 vCore**, **512 GB**, **7 giorni** | Configurazioni di calcolo, archiviazione e backup per il nuovo server. Selezionare **Configura server**. *Utilizzo generico*, *4 vCore*, *512 GB* e *7 giorni* sono i valori predefiniti per **Livello di calcolo**, **vCore**, **Archiviazione** e **Periodo di conservazione backup**. È possibile lasciare questi dispositivi di scorrimento nella posizione in cui si trovano oppure regolarli. Per salvare la selezione del piano tariffario, selezionare **OK**. Lo screenshot successivo mostra queste selezioni.

    :::image type="content" source="./media/quickstart-create-database-portal/4-pricing-tier.png" alt-text="Database di Azure per PostgreSQL nel menu":::
    
5. Configurazione delle opzioni di rete

    Nella scheda Rete è possibile scegliere come deve essere raggiunto il server. Database di Azure per PostgreSQL crea un firewall a livello di server, che impedisce alle applicazioni e agli strumenti esterni di connettersi al server e ai database nel server, a meno che non si crei una regola per aprire il firewall per indirizzi IP specifici. È consigliabile rendere il server accessibile pubblicamente:

    :::image type="content" source="./media/quickstart-create-database-portal/5-networking.png" alt-text="Database di Azure per PostgreSQL nel menu":::

    E quindi limitarlo al proprio indirizzo IP client:

    :::image type="content" source="./media/quickstart-create-database-portal/6-add-client-ip.png" alt-text="Database di Azure per PostgreSQL nel menu":::

6. Selezionare **Revisione e creazione** per rivedere le selezioni effettuate. Selezionare **Crea** per effettuare il provisioning del server. Questa operazione può richiedere qualche minuto.

7. Sulla barra degli strumenti selezionare l'icona **Notifiche** a forma di campana per monitorare il processo di distribuzione. Al termine della distribuzione è possibile selezionare **Aggiungi al dashboard** per creare un riquadro di questo server nel dashboard del portale di Azure come collegamento alla pagina **Panoramica** del server. Selezionare **Vai alla risorsa** per aprire la pagina **Panoramica** del server.

    :::image type="content" source="./media/quickstart-create-database-portal/7-notifications.png" alt-text="Database di Azure per PostgreSQL nel menu":::

   Per impostazione predefinita, un database **postgres** viene creato nel server. Il database [postgres](https://www.postgresql.org/docs/12/static/app-initdb.html) è un database predefinito che può essere usato da utenti, utilità e applicazioni di terze parti. (L'altro database predefinito è **azure_maintenance**. La sua funzione è quella di separare i processi del servizio gestito dalle azioni dell'utente. Non è possibile accedere a questo database.)

    > [!NOTE]
    > Le connessioni al database di Azure per il server PostgreSQL comunicano sulla porta 5432. Quando si cerca di connettersi da una rete aziendale, il traffico in uscita sulla porta 5432 potrebbe non essere consentito dal firewall della rete. In questo caso, non è possibile connettersi al server a meno che il reparto IT non apra la porta 5432.
    >

## <a name="get-the-connection-information"></a>Ottenere le informazioni di connessione

Quando si crea il database di Azure per il server PostgreSQL, viene creato un database predefinito **postgres**. Per connettersi al server di database, sono necessari il nome del server completo e le credenziali di accesso amministratore. È possibile che si sia preso nota prima di questi valori durante la lettura dell'articolo introduttivo. In caso contrario, è possibile trovare facilmente il nome del server e le informazioni di accesso nella pagina **Panoramica** del server nel portale.

Aprire la pagina **Panoramica** del server. Annotare il **nome del server** e il **nome di accesso dell'amministratore del server**. Passare il puntatore del mouse su ogni campo per visualizzare il simbolo di copia a destra del testo. Selezionare il simbolo di copia necessario per copiare i valori.

 :::image type="content" source="./media/quickstart-create-database-portal/8-server-name.png" alt-text="Database di Azure per PostgreSQL nel menu":::

## <a name="connect-to-the-postgresql-database-using-psql"></a>Connettersi al database PostgreSQL tramite psql

Sono diverse le applicazioni che è possibile usare per connettere il database di Azure per il server PostgreSQL. Se nel computer client è installato PostgreSQL, è possibile usare un'istanza locale di [psql](https://www.postgresql.org/docs/current/static/app-psql.html) per connettersi a un server PostgreSQL Azure. È ora possibile usare l'utilità della riga di comando psql per connettersi al server PostgreSQL Azure.

1. Eseguire il comando psql seguente per connettersi a un database di Azure per il server PostgreSQL

   ```bash
   psql --host=<servername> --port=<port> --username=<user> --dbname=<dbname>
   ```

   Ad esempio, il comando seguente consente di connettersi al database predefinito denominato **postgres** nel server PostgreSQL **mydemoserver.postgres.database.azure.com** usando le credenziali di accesso. Immettere il valore di `<server_admin_password>` scelto quando viene chiesta la password.
  
   ```bash
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin --dbname=postgres
   ```

   Dopo che è stata stabilita la connessione, l'utilità psql visualizza un prompt postgres in cui è possibile digitare comandi sql. Nell'output della connessione iniziale potrebbe essere visualizzato un avviso perché la versione di psql in uso potrebbe essere diversa dalla versione del server Database di Azure per PostgreSQL.

   Output di psql esempio:

   ```bash
   psql (11.3, server 12.1)
   WARNING: psql major version 11, server major version 12.
            Some psql features might not work.
   SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
   Type "help" for help.

   postgres=>
   ```

   > [!TIP]
   > Se il firewall non è configurato per accettare l'indirizzo IP del client, verrà visualizzato l'errore seguente:
   >
   > "psql: FATAL:  no pg_hba.conf entry for host `<IP address>`, user "myadmin", database "postgres", SSL on FATAL: SSL connection is required. Specify SSL options and retry.
   >
   > Verificare che l'IP del cliente sia consentito nella fase precedente delle regole del firewall.

2. Creare un database vuoto denominato "mypgsqldb" al prompt digitando il comando seguente:

    ```bash
    CREATE DATABASE mypgsqldb;
    ```

3. Al prompt eseguire il comando seguente per cambiare le connessioni passando al database appena creato **mypgsqldb**:

    ```bash
    \c mypgsqldb
    ```

4. Digitare `\q` e quindi premere INVIO per uscire da psql.

Ora che si è connessi al server Database di Azure per PostgreSQL tramite psql e si è creato un database utente vuoto,

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile eseguire la pulizia delle risorse create nel corso della guida introduttiva in due modi. Si può eliminare il gruppo di risorse di Azure, in modo da includere tutte le risorse del gruppo. Se invece si vogliono mantenere intatte le altre risorse, eliminare solo la risorsa server.

> [!TIP]
> Altre guide introduttive della raccolta si basano su questa. Se si intende continuare a usare le guide introduttive, non eseguire la pulizia delle risorse create in questa. Se non si intende continuare, seguire questa procedura per eliminare le risorse create con questa guida introduttiva nel portale.

Per eliminare l'intero gruppo di risorse, incluso il server appena creato:

1. Individuare il gruppo di risorse nel portale. Scegliere **Gruppi di risorse** dal menu a sinistra. Selezionare quindi il nome del gruppo di risorse, ad esempio **myresourcegroup**.

2. Nella pagina del gruppo di risorse selezionare **Elimina**. Immettere nella casella di testo il nome del gruppo di risorse, in questo esempio **myresourcegroup**, per confermare l'eliminazione. Selezionare **Elimina**.

Per eliminare solo il server appena creato:

1. Individuare il server nel portale, se non è già aperto. Scegliere **Tutte le risorse** dal menu a sinistra. Cercare quindi il server creato.

2. Nella pagina **Panoramica** selezionare **Elimina**.

    :::image type="content" source="./media/quickstart-create-database-portal/9-delete.png" alt-text="Database di Azure per PostgreSQL nel menu":::

3. Verificare il nome del server che si vuole eliminare e visualizzare i database sottostanti interessati. Immettere il nome del server nella casella di testo, in questo esempio **mydemoserver**. Selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Distribuire un'app Django con il servizio app e PostgreSQL](tutorial-django-app-service-postgres.md)
