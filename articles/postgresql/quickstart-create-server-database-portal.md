---
title: 'Avvio rapido: Creare un server - Portale di Azure - Database di Azure per PostgreSQL - server singolo'
description: Questo argomento di avvio rapido illustra come creare e gestire un server di Database di Azure per PostgreSQL con il portale di Azure.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/27/2020
ms.openlocfilehash: 0c390c64db8c5833f5bc5bf2be3367cb63127a0f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902643"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Avvio rapido: Creare un server di Database di Azure per PostgreSQL con il portale di Azure

Il database di Azure per PostgreSQL è un servizio gestito usato per eseguire, gestire e ridimensionare database PostgreSQL a disponibilità elevata nel cloud. Questo argomento di avvio rapido illustra come creare un server singolo di Database di Azure per PostgreSQL in circa cinque minuti con il portale di Azure.

Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure
Aprire il Web browser e passare al [portale](https://portal.azure.com/). Immettere le credenziali per accedere al portale. La visualizzazione predefinita è il dashboard del servizio.

## <a name="create-an-azure-database-for-postgresql-server"></a>Creare un database di Azure per il server PostgreSQL

Un server Database di Azure per PostgreSQL viene creato con un set definito di [risorse di calcolo e di archiviazione](./concepts-pricing-tiers.md). Il server viene creato all'interno di un [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md).

Per creare un server di Database di Azure per PostgreSQL:

1. Selezionare **Crea una risorsa** nell'angolo in alto a sinistra del portale.

2. Selezionare **Database** > **Database di Azure per PostgreSQL**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/1-create-database.png" alt-text="Screenshot che mostra l'opzione Database di Azure per PostgreSQL nel menu.":::

3. Selezionare l'opzione di distribuzione **Server singolo**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/select-deployment-option.png" alt-text="Screenshot della selezione dell'opzione di distribuzione del server singolo per Database di Azure per PostgreSQL.":::

4. Compilare il modulo **Informazioni di base** con le informazioni seguenti.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-database-portal/create-basics.png" alt-text="Screenshot che mostra la scheda Informazioni di base per la creazione di un server singolo.":::

   Impostazione|Valore consigliato|Descrizione
   ---|---|---
   Subscription|Nome della sottoscrizione utente|Sottoscrizione di Azure da usare per il server. Se si hanno più sottoscrizioni, scegliere quella in cui viene fatturata la risorsa.
   Resource group|*myresourcegroup*| Nuovo nome di gruppo di risorse o uno esistente nella sottoscrizione.
   Nome server |*mydemoserver*|Nome univoco per identificare il database di Azure per il server PostgreSQL. Il nome di dominio *postgres.database.azure.com* viene aggiunto al nome del server specificato. Il server può contenere solo lettere minuscole, numeri e il segno meno (-). Deve contenere da 3 a 63 caratteri.
   Origine dati | **Nessuno** | Selezionare **Nessuno** per creare un nuovo server da zero. Si selezionerebbe **Backup** se si creasse un server da un backup geografico di un server di Database di Azure per PostgreSQL esistente.
   Nome utente amministratore |*myadmin*| Account di accesso da usare per la connessione al server. Il nome di accesso dell'amministratore non può essere **azure_superuser**, **azure_pg_admin**, **admin**, **administrator**, **root**, **guest** o **public**. Non può iniziare con **pg_** .
   Password |Password| Nuova password per l'account amministratore del server. Deve contenere da 8 a 128 caratteri di tre categorie seguenti: lettere maiuscole, lettere minuscole, numeri (da 0 a 9) e caratteri non alfanumerici, ad esempio !, $, #, %.
   Location|Area più vicina ai propri utenti| Località più vicina agli utenti.
   Versione|La versione principale più recente| La versione principale più recente di PostgreSQL, a meno che non si abbiano requisiti specifici diversi.
   Calcolo e archiviazione | **Utilizzo generico**, **Generazione 5**, **2 vCore**, **5 GB**, **7 giorni**, **Con ridondanza geografica** | Configurazioni di calcolo, archiviazione e backup per il nuovo server. Selezionare **Configura server**. Selezionare quindi il piano tariffario appropriato. Per altre informazioni, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/postgresql/server/). Per abilitare l'archiviazione con ridondanza geografica dei backup del server, selezionare **Con ridondanza geografica** in **Opzioni di ridondanza per il backup**. Selezionare **OK**.

   > [!NOTE]
   > È consigliabile usare il piano tariffario Basic se le esigenze di calcolo e di prestazioni I/O sono adeguate per il carico di lavoro. Si noti che per i server creati nel piano tariffario Basic non è possibile passare in un secondo momento ai piani per utilizzo generico o ottimizzati per la memoria. 
   
5. Selezionare **Revisione e creazione** per rivedere le selezioni effettuate. Selezionare **Crea** per effettuare il provisioning del server. L'operazione potrebbe richiedere alcuni minuti.

6. Sulla barra degli strumenti selezionare l'icona **Notifiche** a forma di campana per monitorare il processo di distribuzione. Una volta completata la distribuzione, selezionare **Vai alla risorsa** per aprire la pagina **Panoramica** del server.

Viene creato un database vuoto, **postgres**. Si troverà anche un database, **azure_maintenance**, usato per separare i processi del servizio gestito dalle azioni degli utenti. Non è possibile accedere al database **azure_maintenance**.

## <a name="configure-a-server-level-firewall-rule"></a>Configurare una regola del firewall a livello di server
Per impostazione predefinita, il server creato non è accessibile pubblicamente. È necessario concedere le autorizzazioni all'indirizzo IP. Passare alla risorsa server nel portale di Azure e scegliere **Sicurezza delle connessioni** dal menu a sinistra. In caso di dubbi su come trovare la risorsa, vedere [Aprire le risorse](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources).

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-database-portal/add-current-ip-firewall.png" alt-text="Screenshot che mostra le regole del firewall per la sicurezza della connessione.":::
  
Selezionare **Aggiungi indirizzo IP client corrente** e quindi **Salva**. È possibile aggiungere altri indirizzi IP o fornire un intervallo di indirizzi IP per la connessione al server. Per altre informazioni, vedere [Regole del firewall in Database di Azure per PostgreSQL](./concepts-firewall-rules.md).
   
> [!NOTE]
> Per evitare problemi di connettività, verificare se la rete consente il traffico in uscita sulla porta 5432. Database di Azure per PostgreSQL usa tale porta.  

## <a name="connect-to-azure-database-for-postgresql-server-by-using-psql"></a>Connettersi al server di Database di Azure per PostgreSQL tramite psql

È possibile usare [psql](http://postgresguide.com/utilities/psql.html) o [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html), che sono i client PostgreSQL più diffusi. Per questo argomento di avvio rapido si effettuerà la connessione usando psql in [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) all'interno del portale di Azure.

1. Annotare il nome del server, il nome dell'account di accesso dell'amministratore del server, la password e l'ID sottoscrizione per il server appena creato nella sezione **Panoramica** del server.

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

## <a name="clean-up-resources"></a>Pulizia delle risorse
La creazione di un server di Database di Azure per PostgreSQL in un gruppo di risorse è stata completata. Se non si prevede di aver bisogno di queste risorse in futuro, è possibile eliminarle eliminando il gruppo di risorse o il server PostgreSQL. 

Per eliminare il gruppo di risorse:

1. Accedere al portale di Azure e selezionare **Gruppi di risorse**. 
2. Nell'elenco dei gruppi di risorse scegliere il nome del gruppo di risorse.
3. Nella pagina **Panoramica** del gruppo di risorse selezionare **Elimina gruppo di risorse**.
4. Nella finestra di dialogo di conferma immettere il nome del gruppo di risorse, quindi selezionare **Elimina**.

Per eliminare il server, selezionare il pulsante **Elimina** nella pagina **Panoramica** del server:

> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-database-portal/12-delete.png" alt-text="Screenshot che mostra il pulsante per l'eliminazione di un server.":::

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"]
> [Eseguire la migrazione del database usando le funzionalità di esportazione e importazione](./howto-migrate-using-export-and-import.md)
