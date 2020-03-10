---
title: Creazione ed esecuzione di query in un data warehouse (portale di Azure)
description: Creare ed eseguire query in un pool SQL di Azure sinapsi Analytics usando il portale di Azure
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: development
ms.date: 05/28/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7a3dbe5d74dc1e88d0615937b8c6e6d2a77b64a7
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78381106"
---
# <a name="quickstart-create-and-query-an-azure-synapse-analytics-sql-pool-using-the-azure-portal"></a>Guida introduttiva: creare ed eseguire query in un pool SQL di Azure sinapsi Analytics usando il portale di Azure

Creare rapidamente ed eseguire query su un data warehouse eseguendo il provisioning del pool SQL in Azure sinapsi Analytics (in precedenza SQL DW) usando il portale di Azure.

## <a name="prerequisites"></a>Prerequisites

1. Se non si ha una sottoscrizione di Azure, creare un account [gratuito](https://azure.microsoft.com/free/) prima di iniziare.

   > [!NOTE]
   > La creazione di un pool SQL in una sinapsi di Azure può comportare un nuovo servizio fatturabile. Per altre informazioni, vedere [prezzi di analisi di sinapsi di Azure](https://azure.microsoft.com/pricing/details/synapse-analytics/).

2. Scaricare e installare la versione più recente di [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).

## <a name="sign-in-to-the-azure-portal"></a>Accedere al portale di Azure

Accedere al [portale di Azure](https://portal.azure.com/).

## <a name="create-a-sql-pool"></a>Creare un pool SQL

I data warehouse vengono creati usando il pool SQL in Azure sinapsi Analytics. Viene creato un pool SQL con un set definito di [risorse di calcolo](memory-concurrency-limits.md). Il database viene creato in un [gruppo di risorse di Azure](../azure-resource-manager/management/overview.md) e in un [server logico di Azure SQL](../sql-database/sql-database-servers.md).

Attenersi alla procedura seguente per creare un data warehouse contenente i dati di esempio **AdventureWorksDW** .

1. Selezionare **Crea una risorsa** nell'angolo superiore sinistro della portale di Azure.

   ![creare una risorsa in portale di Azure](media/create-data-warehouse-portal/create-a-resource.png)

2. Selezionare **database** nella pagina **nuovo** e selezionare **Azure sinapsi Analytics (in precedenza SQL DW)** nell'elenco in **primo piano** .

   ![creare un data warehouse vuoto](media/create-data-warehouse-portal/create-a-data-warehouse.png)

3. In **nozioni di base**specificare la sottoscrizione, il gruppo di risorse, il nome del pool SQL e il nome del server:

   | Impostazione | Valore consigliato | Descrizione |
   | :------ | :-------------- | :---------- |
   | **Sottoscrizione** | Sottoscrizione in uso | Per informazioni dettagliate sulle sottoscrizioni, vedere [Sottoscrizioni](https://account.windowsazure.com/Subscriptions). |
   | **Gruppo di risorse** | myResourceGroup | Per i nomi di gruppi di risorse validi, vedere [Regole di denominazione e restrizioni](/azure/architecture/best-practices/resource-naming). |
   | **Nome data warehouse** | Qualsiasi nome univoco globale (ad esempio *mySampleDataWarehouse*) | Per i nomi di database validi, vedere [Identificatori del database](/sql/relational-databases/databases/database-identifiers). Si noti che un data warehouse è un tipo di database. |
   | **Server** | Qualsiasi nome globalmente univoco | Selezionare Server esistente o creare un nuovo nome server e selezionare **Crea nuovo**. Per i nomi di server validi, vedere [Regole di denominazione e restrizioni](/azure/architecture/best-practices/resource-naming). |

   ![creare un data warehouse dettagli di base](media/create-data-warehouse-portal/create-sql-pool-basics.png)

4. In **livello di prestazioni**selezionare **Seleziona livello di prestazioni** per modificare facoltativamente la configurazione con un dispositivo di scorrimento.

   ![modificare il livello di prestazioni data warehouse](media/create-data-warehouse-portal/create-sql-pool-performance-level.png)  

   Per ulteriori informazioni sui livelli di prestazioni, vedere [Manage COMPUTE in Azure SQL data warehouse](sql-data-warehouse-manage-compute-overview.md).

5. Ora che è stata completata la scheda nozioni di base del modulo Azure sinapsi Analytics, selezionare **Verifica + crea** e quindi **Crea** per creare il data warehouse nel pool SQL. Il provisioning richiede alcuni minuti.

   ![Selezionare Verifica + crea](media/create-data-warehouse-portal/create-sql-pool-review-create.png)

   ![Selezionare Crea](media/create-data-warehouse-portal/create-sql-pool-create.png)

6. Sulla barra degli strumenti selezionare **notifiche** per monitorare il processo di distribuzione.

   ![notifica](media/create-data-warehouse-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Creare una regola del firewall a livello di server

Il servizio sinapsi di Azure crea un firewall a livello di server. Questo firewall impedisce ad applicazioni e strumenti esterni di connettersi al server o a qualsiasi database nel server. Per abilitare la connettività, è possibile aggiungere regole del firewall per aprire il firewall a indirizzi IP specifici. Seguire questa procedura per creare una [regola del firewall a livello di server](../sql-database/sql-database-firewall-configure.md) per l'indirizzo IP del client.

> [!NOTE]
> La sinapsi di Azure comunica sulla porta 1433. Se si sta provando a connettersi da una rete aziendale, il traffico in uscita sulla porta 1433 potrebbe non essere consentito dal firewall della rete. In questo caso, non è possibile connettersi al server di database SQL di Azure, a meno che il reparto IT non apra la porta 1433.

1. Al termine della distribuzione, selezionare **Tutti i servizi** dal menu a sinistra. Selezionare **database**, selezionare la stella accanto ad **Azure sinapsi Analytics** per aggiungere Azure sinapsi Analytics ai Preferiti.

2. Selezionare **Azure sinapsi Analytics** dal menu a sinistra e quindi selezionare **MySampleDataWarehouse** nella pagina **Azure sinapsi Analytics** . Verrà visualizzata la pagina Panoramica per il database, in cui viene visualizzato il nome completo del server (ad esempio **sqlpoolservername.database.Windows.NET**) e vengono fornite le opzioni per un'ulteriore configurazione.

3. Copiare il nome completo del server da usare per connettersi al server e ai relativi database in questa e in altre guide di avvio rapido. Per aprire Impostazioni server, selezionare il nome del server.

   ![trovare il nome del server](media/create-data-warehouse-portal/find-server-name.png)

4. Selezionare **Mostra impostazioni firewall**.

   ![impostazioni del server](media/create-data-warehouse-portal/server-settings.png)

5. Si apre la pagina **Impostazioni del firewall** per il server di database SQL.

   ![Regola del firewall del server](media/create-data-warehouse-portal/server-firewall-rule.png)

6. Per aggiungere l'indirizzo IP corrente a una nuova regola del firewall, selezionare **Aggiungi IP client** sulla barra degli strumenti. Una regola del firewall può aprire la porta 1433 per un indirizzo IP singolo o un intervallo di indirizzi IP.

7. Selezionare **Salva**. Viene creata una regola del firewall a livello di server per l'indirizzo IP corrente, che apre la porta 1433 nel server logico.

8. Selezionare **OK** e quindi chiudere la pagina **impostazioni del firewall** .

È ora possibile connettersi al server SQL e ai relativi data warehouse usando questo indirizzo IP. La connessione funziona da SQL Server Management Studio o un altro strumento di propria scelta. Quando ci si connette, usare l'account ServerAdmin creato in precedenza.

> [!IMPORTANT]
> Per impostazione predefinita, l'accesso attraverso il firewall del database SQL è abilitato per tutti i servizi di Azure. Selezionare **disattivato** in questa pagina e quindi fare clic su **Salva** per disabilitare il firewall per tutti i servizi di Azure.

## <a name="get-the-fully-qualified-server-name"></a>Ottenere il nome completo del server

Ottenere il nome completo del server SQL nel portale di Azure. Questo nome verrà usato nei passaggi successivi per la connessione al server.

1. Accedere al [portale di Azure](https://portal.azure.com/).

2. Selezionare **Azure sinapsi Analytics** dal menu a sinistra e selezionare il data warehouse nella pagina **analisi di sinapsi di Azure** .

3. Nel riquadro **Informazioni di base** della pagina del portale di Azure per il database individuare e quindi copiare il **Nome server**. In questo esempio il nome completo è sqlpoolservername.database.windows.net.

    ![informazioni di connessione](media/create-data-warehouse-portal/find-server-name-copy.png)

## <a name="connect-to-the-server-as-server-admin"></a>Connettersi al server come amministratore del server

In questa sezione si usa [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) per stabilire una connessione al server SQL di Azure.

1. Aprire SQL Server Management Studio.

2. Immettere le informazioni seguenti nella finestra di dialogo **Connetti al server**:

   | Impostazione | Valore consigliato | Descrizione |
   | :------ | :-------------- | :---------- |
   | Tipo di server | Motore di database | Questo valore è obbligatorio |
   | Nome server | Nome completo del server | Ecco un esempio: **sqlpoolservername.database.Windows.NET**. |
   | Authentication | Autenticazione di SQL Server | L'autenticazione SQL è il solo tipo di autenticazione configurato in questa esercitazione. |
   | Login | Account amministratore del server | Account specificato quando è stato creato il server. |
   | Password | Password per l'account amministratore del server | Password specificata quando è stato creato il server. |
   ||||

   ![Connetti al server](media/create-data-warehouse-portal/connect-to-server-ssms.png)

3. Selezionare **Connetti**. In SSMS si apre la finestra Esplora oggetti. 

4. In Esplora oggetti espandere **Database**. Espandere quindi **mySampleDatabase** per visualizzare gli oggetti nel nuovo database.

   ![oggetti di database](media/create-data-warehouse-portal/connected-ssms.png) 

## <a name="run-some-queries"></a>Eseguire alcune query

SQL Data Warehouse usa T-SQL come linguaggio di query. Per aprire una finestra di query ed eseguire alcune query T-SQL, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse su **mySampleDataWarehouse** e scegliere **nuova query**. Viene visualizzata una nuova finestra di query.

2. Nella finestra di query immettere il comando seguente per visualizzare un elenco di database.

    ```sql
    SELECT * FROM sys.databases
    ```

3. Selezionare **Esegui**. I risultati della query mostrano due database: **master** e **mySampleDataWarehouse**.

   ![Eseguire query sui database](media/create-data-warehouse-portal/query-databases.png)

4. Per esaminare alcuni dati, usare il comando seguente per visualizzare il numero di clienti con il cognome Adams che hanno tre figli a casa. L'elenco dei risultati include sei clienti. 

    ```sql
    SELECT LastName, FirstName FROM dbo.dimCustomer
    WHERE LastName = 'Adams' AND NumberChildrenAtHome = 3;
    ```

   ![Eseguire query su dbo.dimCustomer](media/create-data-warehouse-portal/query-customer.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Per le unità del data warehouse e i dati archiviati vengono addebitati dei costi. Le risorse di calcolo e archiviazione vengono fatturate separatamente.

- Se si vogliono mantenere i dati nelle risorse di archiviazione, è possibile sospendere il calcolo quando il data warehouse non è in uso. In questo modo, vengono addebitati solo i costi per l'archiviazione dei dati. È possibile riprendere il calcolo ogni volta che si è pronti a lavorare con i dati.

- Per evitare di ricevere addebiti in futuro, è possibile eliminare il data warehouse.

Seguire questa procedura per pulire le risorse non più necessarie.

1. Accedere al [portale di Azure](https://portal.azure.com) e selezionare il proprio data warehouse.

   ![Pulire le risorse](media/create-data-warehouse-portal/clean-up-resources.png)

2. Per sospendere il calcolo, selezionare il pulsante **Pausa**. Quando si sospende il data warehouse, viene visualizzato il pulsante **Riprendi**. Per riprendere il calcolo, scegliere **Riprendi**.

3. Per rimuovere il data warehouse in modo che non venga addebitato il calcolo o l'archiviazione, selezionare **Elimina**.

4. Per rimuovere il server SQL creato, selezionare **sqlpoolservername.database.Windows.NET** nell'immagine precedente e quindi selezionare **Elimina**. Fare attenzione quando si esegue questa operazione perché l'eliminazione del server comporta anche quella di tutti i database assegnati al server.

5. Per rimuovere il gruppo di risorse, selezionare **myResourceGroup** e quindi **Elimina gruppo di risorse**.

## <a name="next-steps"></a>Passaggi successivi

Sono stati creati un data warehouse e una regola del firewall, è stata stabilita una connessione al data warehouse e sono state eseguite alcune query. Per altre informazioni su Azure SQL Data Warehouse, continuare con l'esercitazione per il caricamento dei dati.

> [!div class="nextstepaction"]
> [Caricare i dati in SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md)
