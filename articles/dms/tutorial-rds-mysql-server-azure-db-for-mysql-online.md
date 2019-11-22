---
title: 'Esercitazione: Usare il Servizio Migrazione del database di Azure per una migrazione online di RDS MySQL in Database di Azure per MySQL | Microsoft Docs'
description: Informazioni su come eseguire la migrazione online da RDS MySQL in Database di Azure per MySQL con il Servizio Migrazione del database di Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 10/28/2019
ms.openlocfilehash: 2df76c5906037fc5ce35e0c3a6558b0240c4b2be
ms.sourcegitcommit: 38251963cf3b8c9373929e071b50fd9049942b37
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73043313"
---
# <a name="tutorial-migrate-rds-mysql-to-azure-database-for-mysql-online-using-dms"></a>Esercitazione: Eseguire la migrazione di RDS MySQL a Database di Azure per MySQL online con il Servizio Migrazione del database

È possibile usare il Servizio Migrazione del database di Azure per eseguire la migrazione di database da un'istanza di RDS MySQL a [Database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/), mantenendo al tempo stesso online il database di origine durante la migrazione. In altre parole, la migrazione può essere eseguita con tempi di inattività minimi per l'applicazione. In questa esercitazione si esegue la migrazione del database di esempio **Employees** da un'istanza di RDS MySQL a Database di Azure per MySQL usando l'attività di migrazione online nel Servizio Migrazione del database di Azure.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
>
> * Eseguire la migrazione dello schema di esempio usando le utilità mysqldump e mysql.
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione tramite il Servizio Migrazione del database di Azure.
> * Eseguire la migrazione.
> * Monitorare la migrazione.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium. Per altre informazioni, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/database-migration/) di Servizio Migrazione del database di Azure.

> [!IMPORTANT]
> Per un'esperienza di migrazione ottimale, Microsoft consiglia di creare un'istanza del Servizio Migrazione del database di Azure nella stessa area di Azure del database di destinazione. Lo spostamento dei dati tra regioni o aree geografiche può rallentare il processo di migrazione e causare errori.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Questo articolo descrive come eseguire una migrazione online da un'istanza di RDS MySQL a Database di Azure per MySQL.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

* Assicurarsi che il server MySQL di origine esegua una versione di MySQL Community Edition supportata. Per determinare la versione dell'istanza di MySQL, nell'utilità mysql o in MySQL Workbench eseguire il comando:

    ```
    SELECT @@version;
    ```

    Per altre informazioni, vedere l'articolo [Versioni supportate di Database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/concepts-supported-versions).

* Scaricare e installare il [database di esempio MySQL **Employees**](https://dev.mysql.com/doc/employee/en/employees-installation.html).
* Creare un'istanza di [Database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/quickstart-create-mysql-server-database-using-azure-portal).
* Creare una rete virtuale di Azure per il Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che offre la connettività da sito a sito per i server di origine locali con [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Per altre informazioni sulla creazione di una rete virtuale, vedere [Documentazione sulla rete virtuale](https://docs.microsoft.com/azure/virtual-network/) e in particolare gli articoli di avvio rapido con istruzioni dettagliate.
* Verificare che le regole del gruppo di sicurezza di rete per la rete virtuale non blocchino le porte di comunicazione in ingresso nel Servizio Migrazione del database di Azure: 443, 53, 9354, 445 e 12000. Per informazioni dettagliate sui filtri del traffico dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Configurare [Windows Firewall](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) (o il firewall Linux) per consentire l'accesso al motore di database. Per il server MySQL consentire la connettività della porta 3306.

> [!NOTE]
> Database di Azure per MySQL supporta solo le tabelle InnoDB. Per convertire le tabelle MyISAM in InnoDB, vedere l'articolo [Converting Tables from MyISAM to InnoDB](https://dev.mysql.com/doc/refman/5.7/en/converting-tables-to-innodb.html) (Conversione di tabelle da MyISAM a InnoDB).

### <a name="set-up-aws-rds-mysql-for-replication"></a>Impostare AWS RDS MySQL per la replica

1. Per creare un nuovo gruppo di parametri, seguire le istruzioni fornite da AWS nell'articolo [MySQL Database Log Files](https://docs.aws.amazon.com/AmazonRDS/latest/UserGuide/USER_LogAccess.Concepts.MySQL.html) (File di log di Database MySQL), nella sezione **F**.
2. Creare un nuovo gruppo di parametri con la configurazione seguente:
    * binlog_format = row
    * binlog_checksum = NONE
3. Salvare il nuovo gruppo di parametri.
4. Associare il nuovo gruppo di parametri all'istanza di RDS MySQL. Potrebbe essere necessario riavviare il computer.

## <a name="migrate-the-schema"></a>Eseguire la migrazione dello schema

1. Estrarre lo schema dal database di origine e applicarlo al database di destinazione per completare la migrazione di tutti gli oggetti di database, ad esempio schemi di tabella, indici e stored procedure.

    Il modo più semplice per eseguire la migrazione solo dello schema è usare mysqldump con il parametro --no-data. Il comando per eseguire la migrazione dello schema è:

    ```
    mysqldump -h [servername] -u [username] -p[password] --databases [db name] --no-data > [schema file path]
    ```
    
    Ad esempio, per eseguire il dump di un file dello schema per il database **Employees**, usare il comando seguente:
    
    ```
    mysqldump -h 10.10.123.123 -u root -p --databases employees --no-data > d:\employees.sql
    ```

2. Importare lo schema nel servizio di destinazione, ovvero Database di Azure per MySQL. Per ripristinare il file di dump dello schema, eseguire il comando seguente:

    ```
    mysql.exe -h [servername] -u [username] -p[password] [database]< [schema file path]
    ```

    Ad esempio, per importare lo schema per il database **Employees**:

    ```
    mysql.exe -h shausample.mysql.database.azure.com -u dms@shausample -p employees < d:\employees.sql
    ```

3. Se nello schema sono presenti chiavi esterne, il caricamento iniziale e la sincronizzazione continua della migrazione avrà esito negativo. Per estrarre lo script di eliminazione della chiave esterna e aggiungere lo script della chiave esterna nel database di destinazione (Database di Azure per MySQL), eseguire lo script seguente in MySQL Workbench:

    ```
    SET group_concat_max_len = 8192;
        SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
        FROM
        (SELECT
        KCU.REFERENCED_TABLE_SCHEMA as SchemaName,
                    KCU.TABLE_NAME,
                    KCU.COLUMN_NAME,
                    CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
                    FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
                    WHERE
                      KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
                      AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries
      GROUP BY SchemaName;
    ```

4. Eseguire drop foreign key, ovvero la seconda colonna, sul risultato della query per eliminare la chiave esterna.

5. Se nei dati sono presenti trigger (di inserimento o di aggiornamento), prima di replicare i dati dall'origine nel database di destinazione verrà applicata l'integrità dei dati. È consigliabile disabilitare i trigger in tutte le tabelle *del database di destinazione* durante la migrazione e quindi riabilitarli al termine della migrazione.

    Per disabilitare i trigger nel database di destinazione:

    ```
    select concat ('alter table ', event_object_table, ' disable trigger ', trigger_name)
    from information_schema.triggers;
    ```

6. Se sono presenti istanze del tipo di dati ENUM nelle tabelle, è consigliabile aggiornarli temporaneamente al tipo di dati "character varying" nella tabella di destinazione. Una volta completata la replica dei dati, ripristinare il tipo di dati ENUM.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

   ![Mostra le sottoscrizioni del portale](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-subscription1.png)

2. Selezionare la sottoscrizione in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

    ![Visualizzare i provider di risorse](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-select-resource-provider.png)

3. Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.

    ![Registrare il provider di risorse](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-register-resource-provider.png)

## <a name="create-an-instance-of-azure-database-migration-service"></a>Creare un'istanza del Servizio Migrazione del database di Azure

1. Nel portale di Azure selezionare **+ Crea una risorsa,** cercare Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

    ![Azure Marketplace](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create1.png)
  
3. Nella schermata **Crea servizio Migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare la località in cui si vuole creare l'istanza del Servizio Migrazione del database di Azure.

5. Selezionare una rete virtuale esistente o crearne una nuova.

    La rete virtuale consente al Servizio Migrazione del database di Azure di accedere all'istanza di MySQL di origine e all'istanza di Database di Azure per MySQL di destinazione.

    Per altre informazioni su come creare una rete virtuale nel portale di Azure, vedere l'articolo [Creare una rete virtuale usando il portale di Azure](https://aka.ms/DMSVnet).

6. Selezionare un piano tariffario. Per questa migrazione online assicurarsi di selezionare il piano tariffario Premium: 4 vCore.

    ![Configurare le impostazioni dell'istanza del Servizio Migrazione del database di Azure](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-settings3.png)

7. Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato il servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

      ![Individuare tutte le istanze del Servizio Migrazione del database di Azure](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-search.png)

2. Nella schermata **Servizi Migrazione del database di Azure** cercare il nome dell'istanza Servizio Migrazione del database di Azure appena creata e quindi selezionare l'istanza.

     ![Individuare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-instance-search.png)

3. Selezionare **+ Nuovo progetto di migrazione**.
4. Nella schermata **Nuovo progetto di migrazione** specificare il nome del progetto, nella casella di testo **Tipo del server di origine** selezionare **MySQL** e quindi nella casella di testo **Tipo del server di destinazione** selezionare **AzureDbForMySQL**.
5. Nella sezione **Scegli il tipo di attività** selezionare **Migrazione dei dati online**.

    > [!IMPORTANT]
    > Assicurarsi di selezionare **Migrazione dei dati online**; le migrazioni offline non sono supportate per questo scenario.

    ![Creare il progetto del Servizio Migrazione del database](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-create-project6.png)

    > [!NOTE]
    > In alternativa, è possibile scegliere **Crea solo il progetto** per creare subito il progetto di migrazione ed eseguire la migrazione in un secondo momento.

6. Selezionare **Salva**.

7. Selezionare **Crea ed esegui attività** per creare il progetto ed eseguire l'attività di migrazione.

    > [!NOTE]
    > Annotare i prerequisiti necessari per configurare la migrazione online nel pannello di creazione del progetto.

## <a name="specify-source-details"></a>Specificare le informazioni di origine

* Nella schermata **Dettagli origine della migrazione** specificare i dettagli di connessione per l'istanza di MySQL di origine.

   ![Dettagli origine](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-source-details5.png)

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Selezionare **Salva** e quindi nella schermata **Dettagli destinazione** specificare i dettagli di connessione per il server di Database di Azure per MySQL di destinazione, di cui è già stato eseguito il provisioning e con lo schema **Employees** distribuito tramite MySQLDump.

    ![Selezionare la destinazione](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-select-target5.png)

2. Selezionare **Salva** e quindi nella schermata **Mappa ai database di destinazione** eseguire il mapping del database di origine e di quello di destinazione per la migrazione.

    Se il database di destinazione contiene lo stesso nome del database di origine, il Servizio Migrazione del database di Azure seleziona il database di destinazione per impostazione predefinita.

    ![Eseguire il mapping nei database di destinazione](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-map-targets-activity5.png)

3. Selezionare **Salva**, nella schermata **Riepilogo della migrazione**, nella casella di testo **Nome attività**, specificare un nome per l'attività di migrazione, quindi rivedere il riepilogo per verificare che i dettagli dell'origine e della destinazione corrispondano a quanto specificato in precedenza.

    ![Riepilogo della migrazione](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-migration-summary2.png)

## <a name="run-the-migration"></a>Eseguire la migrazione

* Selezionare **Esegui migrazione**.

    Verrà visualizzata la finestra dell'attività di migrazione con il campo **Stato** dell'attività impostato su **Inizializzazione in corso**.

## <a name="monitor-the-migration"></a>Monitorare la migrazione

1. Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione finché nel campo **Stato** della migrazione non viene indicato **In esecuzione**.

    ![Stato attività: in esecuzione](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-activity-status4.png)

2. In **NOME DATABASE** selezionare un database specifico per ottenere lo stato di migrazione per le operazioni **Caricamento completo** e **Sincronizzazione dei dati incrementale**.

    **Caricamento completo** indica lo stato di migrazione del carico iniziale, mentre **Sincronizzazione dei dati incrementale** indica lo stato di Change Data Capture (CDC).

    ![Schermata dell'inventario: caricamento dei dati completo](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-full-load.png)

    ![Schermata dell'inventario: sincronizzazione dei dati incrementale](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-incremental.png)

## <a name="perform-migration-cutover"></a>Eseguire il cutover della migrazione

Al termine del caricamento completo iniziale, i database vengono contrassegnati con **Pronto per il cutover**.

1. Quando si è pronti per completare la migrazione del database, selezionare **Avvia cutover**.

    ![Avviare il cutover](media/tutorial-rds-mysql-server-azure-db-for-mysql-online/dms-inventory-start-cutover.png)

2. Assicurarsi di arrestare tutte le transazioni in ingresso nel database di origine. Attendere finché il contatore **Modifiche in sospeso** mostra **0**.
3. Selezionare **Conferma** e quindi **Applica**.
4. Quando lo stato della migrazione del database è **Completata**, connettere le applicazioni al nuovo database Database di Azure per MySQL di destinazione.

La migrazione online di un'istanza locale di MySQL a Database di Azure per MySQL è stata completata.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sul Servizio Migrazione del database di Azure, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview).
* Per informazioni sul Database di Azure per MySQL, vedere l'articolo [Database di Azure per MySQL](https://docs.microsoft.com/azure/mysql/overview).
* Per altre domande, inviare un messaggio di posta elettronica all'alias [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).
