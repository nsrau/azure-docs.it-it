---
title: 'Avvio rapido: Creare un server - Portale di Azure - Database di Azure per PostgreSQL - server singolo'
description: Questo argomento di avvio rapido illustra come creare e gestire un server di Database di Azure per PostgreSQL con il portale di Azure.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 10/18/2020
ms.openlocfilehash: 000ab3e3911c65554622a48d34abda79d60411df
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96492387"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Avvio rapido: Creare un server di Database di Azure per PostgreSQL con il portale di Azure

Il database di Azure per PostgreSQL è un servizio gestito usato per eseguire, gestire e ridimensionare database PostgreSQL a disponibilità elevata nel cloud. Questa guida di avvio rapido illustra come creare un server singolo di Database di Azure per PostgreSQL a cui connettersi.

## <a name="prerequisites"></a>Prerequisiti
È necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="create-an-azure-database-for-postgresql-server"></a>Creare un database di Azure per il server PostgreSQL
Passare al [portale di Azure](https://portal.azure.com/) per creare un database del server singolo di Database di Azure per PostgreSQL. Cercare e selezionare *Server di Database di Azure per PostgreSQL*.

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/search-postgres.png" alt-text="Trovare Database di Azure per PostgreSQL.":::

1. Selezionare **Aggiungi**.

2. Nella pagina Creare un database di Azure per il server PostgreSQL selezionare **Server singolo**.

    >[!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/select-single-server.png" alt-text="Selezionare Server singolo":::

3. Immettere ora le informazioni seguenti nel modulo **Informazioni di base**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="Screenshot che mostra la scheda Informazioni di base per la creazione di un server singolo.":::

   |Impostazione|Valore consigliato|Descrizione|
   |:---|:---|:---|
   |Subscription|il nome della sottoscrizione|Selezionare la sottoscrizione di Azure da usare.|
   |Resource group|*myresourcegroup*| Un gruppo di risorse nuovo o esistente nella sottoscrizione.|
   |Nome server |*mydemoserver*|Nome univoco per identificare il database di Azure per il server PostgreSQL. Il nome di dominio *postgres.database.azure.com* viene aggiunto al nome del server specificato. Il server può contenere solo lettere minuscole, numeri e il segno meno (-). Deve contenere da 3 a 63 caratteri.|
   |Origine dati | Nessuno | Selezionare **Nessuno** per creare un nuovo server da zero. Selezionare **Backup** solo se viene eseguito il ripristino di un backup geografico di un server esistente.|
   |Nome utente amministratore |*myadmin*| Immettere il nome utente dell'amministratore del server. Non può iniziare con **pg_** e non è consentito usare questi valori: **azure_superuser**, **azure_pg_admin**, **admin**, **administrator**, **root**, **guest** o **public**.|
   |Password |Immettere la password.| Una nuova password per l'utente amministratore del server. Deve contenere da 8 a 128 caratteri di tre categorie seguenti: lettere maiuscole, lettere minuscole, numeri (da 0 a 9) e caratteri non alfanumerici, ad esempio !, $, #, %.|
   |Location|la località desiderata| selezionare una località dall'elenco a discesa.|
   |Versione|La versione principale più recente| La versione principale più recente di PostgreSQL, a meno che non si abbiano requisiti specifici diversi.|
   |Calcolo e archiviazione | *usare i valori predefiniti*| Il piano tariffario predefinito è **Utilizzo generico** con **4 vCore** e **100 GB** di spazio di archiviazione. La conservazione dei backup è impostata su **7 giorni** con l'opzione di backup **Con ridondanza geografica**.<br/>Vedere informazioni sui [prezzi](https://azure.microsoft.com/pricing/details/postgresql/server/) e aggiornare i valori predefiniti, se necessario.|


   > [!NOTE]
   > È consigliabile usare il piano tariffario Basic se le esigenze di calcolo e di prestazioni I/O sono adeguate per il carico di lavoro. Si noti che per i server creati nel piano tariffario Basic non è possibile passare in un secondo momento ai piani per utilizzo generico o ottimizzati per la memoria.

5. Selezionare **Revisione e creazione** per rivedere le selezioni effettuate. Selezionare **Crea** per effettuare il provisioning del server. L'operazione potrebbe richiedere alcuni minuti.
    > [!NOTE]
    > Viene creato un database vuoto, **postgres**. Si troverà anche un database, **azure_maintenance**, usato per separare i processi del servizio gestito dalle azioni degli utenti. Non è possibile accedere al database **azure_maintenance**.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/deployment-success.png" alt-text="Distribuzione riuscita.":::

[Problemi? Segnalarli](https://aka.ms/postgres-doc-feedback).

## <a name="configure-a-firewall-rule"></a>Configurare una regola del firewall
Per impostazione predefinita, il server creato non è accessibile pubblicamente. È necessario concedere le autorizzazioni all'indirizzo IP. Passare alla risorsa server nel portale di Azure e scegliere **Sicurezza delle connessioni** dal menu a sinistra. In caso di dubbi su come trovare la risorsa, vedere [Aprire le risorse](../azure-resource-manager/management/manage-resources-portal.md#open-resources).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="Screenshot che mostra le regole del firewall per la sicurezza della connessione.":::

Selezionare **Aggiungi indirizzo IP client corrente** e quindi **Salva**. È possibile aggiungere altri indirizzi IP o fornire un intervallo di indirizzi IP per la connessione al server. Per altre informazioni, vedere [Regole del firewall in Database di Azure per PostgreSQL](./concepts-firewall-rules.md).

> [!NOTE]
> Per evitare problemi di connettività, verificare se la rete consente il traffico in uscita sulla porta 5432. Database di Azure per PostgreSQL usa tale porta.

[Problemi? Segnalarli](https://aka.ms/postgres-doc-feedback).

## <a name="connect-to-the-server-with-psql"></a>Connettersi al server con psql

È possibile usare [psql](http://postgresguide.com/utilities/psql.html) o [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html), che sono i client PostgreSQL più diffusi. Per questo argomento di avvio rapido si effettuerà la connessione usando psql in [Azure Cloud Shell](../cloud-shell/overview.md) all'interno del portale di Azure.

1. Annotare il nome del server, il nome dell'account di accesso dell'amministratore del server, la password e l'ID sottoscrizione per il server appena creato nella sezione **Panoramica** del server.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/quickstart-create-database-portal/overview-new.png" alt-text="Ottenere le informazioni di connessione.":::


2. Aprire Azure Cloud Shell nel portale selezionando l'icona in alto a sinistra.

   > [!NOTE]
   > Se si apre Cloud Shell per la prima volta, verrà chiesto di creare un gruppo di risorse e un account di archiviazione. Questo passaggio è occasionale e verrà automaticamente collegato per tutte le sessioni.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="media/quickstart-create-database-portal/use-in-cloud-shell.png" alt-text="Screenshot che mostra le informazioni sul server e l'icona per l'apertura di Azure Cloud Shell.":::

3. Eseguire il comando seguente nel terminale di Azure Cloud Shell. Sostituire i valori con il nome effettivo del server e il nome di accesso dell'utente amministratore. Usare il database **postgres** vuoto con l'utente amministratore nel formato seguente: `<admin-username>@<servername>`.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```

   Ecco come si presenta l'esperienza nel terminale di Cloud Shell:

   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell

    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. Nello stesso terminale di Azure Cloud Shell creare un database denominato **guest**.

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Cambiare ora le connessioni passando al database **guest** appena creato.

   ```bash
   \c guest
   ```
6. Digitare `\q` e quindi premere INVIO per chiudere psql.

[Problemi? Segnalarli](https://aka.ms/postgres-doc-feedback).

## <a name="clean-up-resources"></a>Pulire le risorse
La creazione di un server di Database di Azure per PostgreSQL in un gruppo di risorse è stata completata. Se non si prevede di aver bisogno di queste risorse in futuro, è possibile eliminarle eliminando il gruppo di risorse o il server PostgreSQL.

Per eliminare il gruppo di risorse:

1. Accedere al portale di Azure e selezionare **Gruppi di risorse**.
2. Nell'elenco dei gruppi di risorse scegliere il nome del gruppo di risorse.
3. Nella pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
4. Nella finestra di dialogo di conferma immettere il nome del gruppo di risorse, quindi selezionare **Elimina**.

Per eliminare il server, selezionare il pulsante **Elimina** nella pagina **Panoramica** del server:

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="Screenshot che mostra il pulsante per l'eliminazione di un server.":::

[Problemi? Segnalarli](https://aka.ms/postgres-doc-feedback).

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire la migrazione del database usando le funzionalità di esportazione e importazione](./howto-migrate-using-export-and-import.md) <br/>

> [!div class="nextstepaction"]
> [Progettare un database](./tutorial-design-database-using-azure-portal.md#create-tables-in-the-database)

[Ci sono problemi a trovare le informazioni giuste? Segnalarli](https://aka.ms/postgres-doc-feedback).