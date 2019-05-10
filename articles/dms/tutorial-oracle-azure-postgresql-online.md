---
title: 'Esercitazione: Usare il Servizio Migrazione del database di Azure per eseguire la migrazione online di Oracle in Database di Azure per PostgreSQL | Microsoft Docs'
description: Informazioni su come eseguire la migrazione online da Oracle in locale o su macchine virtuali in Database di Azure per PostgreSQL con il Servizio Migrazione del database di Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 6f94fa8b5c0d972d9cdbe86c480a712f7e44c29f
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65157209"
---
# <a name="tutorial-migrate-oracle-to-azure-database-for-postgresql-online-using-dms-preview"></a>Esercitazione: Eseguire la migrazione online di Oracle a Database di Azure per PostgreSQL usando il Servizio Migrazione del database (anteprima)

È possibile usare il Servizio Migrazione del database di Azure per eseguire la migrazione dei database dai database Oracle ospitati in locale o su macchine virtuali a [Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/) con tempi di inattività minimi. In altre parole, è possibile completare la migrazione con tempi di inattività minimi per l'applicazione. In questa esercitazione si esegue la migrazione del database di esempio **HR** da un'istanza locale o su macchina virtuale di Oracle 11g a Database di Azure per PostgreSQL usando l'attività di migrazione online nel Servizio Migrazione del database di Azure.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> * Valutare il lavoro richiesto per la migrazione usando lo strumento ora2pg.
> * Eseguire la migrazione dello schema di esempio con lo strumento ora2pg.
> * Creare un'istanza del Servizio Migrazione del database di Azure.
> * Creare un progetto di migrazione tramite il Servizio Migrazione del database di Azure.
> * Eseguire la migrazione.
> * Monitorare la migrazione.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium.

> [!IMPORTANT]
> Per un'esperienza di migrazione ottimale, Microsoft consiglia di creare un'istanza del Servizio Migrazione del database di Azure nella stessa area di Azure del database di destinazione. Lo spostamento dei dati tra regioni o aree geografiche può rallentare il processo di migrazione e causare errori.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Questo articolo descrive come eseguire una migrazione online da Oracle a Database di Azure per PostgreSQL.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

* Scaricare e installare [Oracle 11g Release 2 (Standard Edition, Standard Edition One o Enterprise Edition)](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/index.html).
* Scaricare il database di esempio **HR** da [qui](https://docs.oracle.com/database/121/COMSC/installation.htm#COMSC00002).
* Scaricare e installare ora2pg in [Windows](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Windows.pdf) o [Linux](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Steps%20to%20Install%20ora2pg%20on%20Linux.pdf).
* [Creare un'istanza nel Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/quickstart-create-server-database-portal).
* Connettersi all'istanza e creare un database usando le istruzioni contenute in questo [documento](https://docs.microsoft.com/azure/postgresql/tutorial-design-database-using-azure-portal).
* Creare una rete virtuale di Azure per il Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che offre la connettività da sito a sito per i server di origine locali con [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Per altre informazioni sulla creazione di una rete virtuale, vedere [Documentazione sulla rete virtuale](https://docs.microsoft.com/azure/virtual-network/), in particolare gli articoli di avvio rapido, che forniscono istruzioni dettagliate.

  > [!NOTE]
  > Durante la configurazione della rete virtuale, se si usa ExpressRoute con peering di rete per Microsoft, aggiungere gli [endpoint](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) di servizio seguenti alla subnet in cui verrà effettuato il provisioning del servizio:
  > * Endpoint del database di destinazione (ad esempio endpoint SQL, endpoint Cosmos DB e così via)
  > * Endpoint di archiviazione
  > * Endpoint bus di servizio
  >
  > Questa configurazione è necessaria perché il Servizio Migrazione del database di Azure non ha connettività Internet.

* Verificare che le regole del gruppo di sicurezza di rete (NSG) per la rete virtuale non blocchino le porte di comunicazione in ingresso nel Servizio Migrazione del database di Azure: 443, 53, 9354, 445, 12000. Per informazioni dettagliate sui filtri del traffico dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).
* Configurare [Windows Firewall per l'accesso al motore di database](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Aprire Windows Firewall per consentire al Servizio Migrazione del database di Azure di accedere al server Oracle di origine (per impostazione predefinita attraverso la porta TCP 1521).
* Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.
* Creare una [regola del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) a livello di server per Database di Azure per PostgreSQL per consentire al Servizio Migrazione del database di Azure di accedere ai database di destinazione. Specificare l'intervallo di subnet della rete virtuale usato per il Servizio Migrazione del database di Azure.
* Abilitare l'accesso ai database Oracle di origine.

  > [!NOTE]
  > Un utente, per connettersi al database di origine Oracle, deve avere il ruolo di amministratore del database.

  * I log di ripristino archiviazione sono necessari perché la sincronizzazione incrementale nel Servizio Migrazione del database di Azure possa acquisire la modifica ai dati. Seguire questa procedura per configurare il database di origine Oracle:
    * Accedere con il privilegio SYSDBA eseguendo il comando seguente:

      ```
      sqlplus (user)/(password) as sysdba
      ```

    * Arrestare l'istanza del database eseguendo il comando seguente.

      ```
      SHUTDOWN IMMEDIATE;
      ```

      Attendere la conferma `'ORACLE instance shut down'`.

    * Avviare la nuova istanza e montare (ma non aprire) il database per abilitare o disabilitare l'archiviazione eseguendo il comando seguente:

      ```
      STARTUP MOUNT;
      ```

      Il database deve essere montato. Attendere la conferma "Oracle instance started".

    * Modificare la modalità di archiviazione del database eseguendo il comando seguente:

      ```
      ALTER DATABASE ARCHIVELOG;
      ```

    * Aprire il database per le normali operazioni eseguendo il comando seguente:

      ```
      ALTER DATABASE OPEN;
      ```

      Potrebbe essere necessario riavviare per poter visualizzare il file ARC.

    * Per la verifica eseguire il comando seguente:

      ```
      SELECT log_mode FROM v$database;
      ```

      Si riceverà una risposta `'ARCHIVELOG'`. Se la risposta è `'NOARCHIVELOG'`, il requisito non è soddisfatto.

  * Abilitare la registrazione supplementare per la replica usando una delle opzioni seguenti.

    * **Opzione 1**.
      Modificare la registrazione supplementare a livello di database per coprire tutte le tabelle con indice univoco e chiave primaria. La query di rilevamento restituirà `'IMPLICIT'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY, UNIQUE) COLUMNS;
      ```

      Modificare la registrazione supplementare a livello di tabella. Eseguirla solo per le tabelle con manipolazione dei dati e senza chiavi primarie o indici univoci.

      ```
      ALTER TABLE [TABLENAME] ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    * **Opzione 2**.
      Modificare la registrazione supplementare a livello di database per coprire tutte le tabelle. La query di rilevamento restituisce `'YES'`.

      ```
      ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
      ```

      Modificare la registrazione supplementare a livello di tabella. Seguire la logica indicata sotto per eseguire un'unica istruzione per ogni tabella.

      Se la tabella contiene una chiave primaria:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (PRIMARY KEY) COLUMNS;
      ```

      Se la tabella contiene un indice univoco:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG GROUP (first unique index columns) ALWAYS;
      ```

      In caso contrario, eseguire il comando seguente:

      ```
      ALTER TABLE xxx ADD SUPPLEMENTAL LOG DATA (ALL) COLUMNS;
      ```

    Per la verifica eseguire il comando seguente:

      ```
      SELECT supplemental_log_data_min FROM v$database;
      ```

    Si riceverà una risposta `'YES'`.

> [!IMPORTANT]
> Per la versione di anteprima pubblica di questo scenario, il Servizio Migrazione del database di Azure supporta Oracle versione 10g o 11g. I clienti che eseguono Oracle versione 12c o successiva devono tenere presente che il protocollo di autenticazione minimo consentito perché il driver ODBC possa connettersi a Oracle deve essere 8. Per un database di origine Oracle versione 12c o successiva, è necessario configurare il protocollo di autenticazione come indicato di seguito:
>
> * Aggiornare SQLNET.ORA:
>
>    ```
>    SQLNET.ALLOWED_LOGON_VERSION_CLIENT = 8
>    SQLNET.ALLOWED_LOGON_VERSION_SERVER = 8
>    ```
>
> * Riavviare il computer per rendere effettive le nuove impostazioni.
> * Modificare la password per gli utenti esistenti:
>
>    ```
>    ALTER USER system IDENTIFIED BY {pswd}
>    ```
>
>   Per altre informazioni, vedere [questa](http://www.dba-oracle.com/t_allowed_login_version_server.htm) pagina.
>
> Tenere infine presente che la modifica del protocollo di autenticazione può compromettere l'autenticazione client.

## <a name="assess-the-effort-for-an-oracle-to-azure-database-for-postgresql-migration"></a>Valutare il lavoro richiesto per una migrazione di Oracle a Database di Azure per PostgreSQL

È consigliabile usare ora2pg per valutare il lavoro richiesto per eseguire la migrazione da Oracle a Database di Azure per PostgreSQL. Usare la direttiva `ora2pg -t SHOW_REPORT` per creare un report con l'elenco di tutti gli oggetti Oracle, il costo stimato della migrazione (in giorni sviluppatori) e di determinati oggetti di database che potrebbero richiedere particolare attenzione durante la conversione.

La maggior parte dei clienti impiegherà una notevole quantità di tempo a esaminare il report di valutazione e a prendere in considerazione il lavoro richiesto per la conversione automatica e manuale.

Per configurare ed eseguire ora2pg per creare un report di valutazione, vedere la sezione **Premigration: Assessment** (Pre-migrazione: valutazione) in [Oracle to Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) (Guida di riferimento dettagliata sulla migrazione da Oracle a Database di Azure per PostgreSQL). Un report di valutazione ora2pg di esempio è disponibile [qui](http://ora2pg.darold.net/report.html) come riferimento.

## <a name="export-the-oracle-schema"></a>Esportare lo schema Oracle

È consigliabile usare ora2pg per convertire lo schema Oracle e altri oggetti Oracle (tipi, procedure, funzioni e così via) in uno schema compatibile con Database di Azure per PostgreSQL. ora2pg include molte direttive che consentono di definire in anticipo determinati tipi di dati. È ad esempio possibile usare la direttiva `DATA_TYPE` per sostituire tutte le occorrenze di NUMBER(*,0) con bigint invece che con NUMERIC(38).

È possibile eseguire ora2pg per esportare ogni oggetto di database in file SQL. È quindi possibile esaminare i file SQL prima di importarli in Database di Azure per PostgreSQL usando psql oppure è possibile eseguire lo script SQL in PgAdmin.

```
psql -f [FILENAME] -h [AzurePostgreConnection] -p 5432 -U [AzurePostgreUser] -d database 
```

Ad esempio: 

```
psql -f %namespace%\schema\sequences\sequence.sql -h server1-server.postgres.database.azure.com -p 5432 -U username@server1-server -d database
```

Per configurare ed eseguire ora2pg per la conversione dello schema, vedere la sezione **Migration: Schema and data** (Migrazione: schema e dati) in [Oracle to Azure Database for PostgreSQL Cookbook](https://github.com/Microsoft/DataMigrationTeam/blob/master/Whitepapers/Oracle%20to%20Azure%20PostgreSQL%20Migration%20Cookbook.pdf) (Guida di riferimento dettagliata sulla migrazione da Oracle a Database di Azure per PostgreSQL).

## <a name="set-up-the-schema-in-azure-database-for-postgresql"></a>Configurare lo schema in Database di Azure per PostgreSQL

Per impostazione predefinita, in Oracle schema.table.column è in lettere tutte maiuscole, mentre in PostgreSQL schema.table.column è in lettere minuscole. Perché il Servizio Migrazione del database di Azure possa avviare lo spostamento dati da Oracle a Database di Azure per PostgreSQL, le lettere maiuscole/minuscole in schema.table.column devono avere lo stesso formato del database di origine Oracle.

Se ad esempio il database di origine Oracle ha come schema "HR"."EMPLOYEES"."EMPLOYEE_ID", lo schema di PostgreSQL deve usare lo stesso formato.

Per assicurarsi che il formato delle lettere in schema.table.column sia lo stesso sia per Oracle che per Database di Azure per PostgreSQL, è consigliabile usare la procedura seguente.

> [!NOTE]
> È possibile usare un approccio diverso per derivare lo schema in lettere maiuscole. Questo passaggio verrà migliorato e automatizzato a breve.

1. Esportare gli schemi con ora2pg in lettere minuscole. Nello script sql di creazione della tabella creare manualmente uno schema con "SCHEMA" in lettere maiuscole.
2. Importare gli altri oggetti Oracle, ad esempio trigger, sequenze, procedure, tipi e funzioni, in Database di Azure per PostgreSQL.
3. Per formattare TABLE e COLUMN in lettere MAIUSCOLE, eseguire lo script seguente:

   ```
   -- INPUT: schema name
   set schema.var = “HR”;

   -- Generate statements to rename tables and columns
   SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
   UNION ALL 
   SELECT 2, 'alter table "'||c.relname||'" rename '||a.attname||' to "'||upper(a.attname)||'";'
   FROM pg_class c
   JOIN pg_attribute a ON a.attrelid = c.oid
   JOIN pg_type t ON a.atttypid = t.oid
   LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
    AND r.conname = a.attname
   WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
   UNION ALL
   SELECT 3, 'alter table '||c.relname||' rename to "'||upper(c.relname)||'";'
   FROM pg_catalog.pg_class c
    LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
   WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
   ORDER BY 1;
   ```

* Eliminare la chiave esterna nel database di destinazione per eseguire il caricamento completo. Per uno script da usare per eliminare la chiave esterna, vedere la sezione **Eseguire la migrazione dello schema di esempio** di [questo](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online) articolo.
* Usare Il Servizio Migrazione del database di Azure per eseguire il caricamento completo e la sincronizzazione.
* Quando i dati nell'istanza di Database di Azure per PostgreSQL di destinazione vengono aggiornati con il database di origine, eseguire il cutover del database nel Servizio Migrazione del database di Azure.
* Per formattare SCHEMA, TABLE e COLUMN in lettere minuscole (se lo schema per Database di Azure per PostgreSQL deve avere questo formato per la query dell'applicazione), eseguire lo script seguente:

  ```
  -- INPUT: schema name
  set schema.var = hr;
  
  -- Generate statements to rename tables and columns
  SELECT 1, 'SET search_path = "' ||current_setting('schema.var')||'";'
  UNION ALL
  SELECT 2, 'alter table "'||c.relname||'" rename "'||a.attname||'" to '||lower(a.attname)||';'
  FROM pg_class c
  JOIN pg_attribute a ON a.attrelid = c.oid
  JOIN pg_type t ON a.atttypid = t.oid
  LEFT JOIN pg_catalog.pg_constraint r ON c.oid = r.conrelid
     AND r.conname = a.attname
  WHERE c.relnamespace = (select oid from pg_namespace where nspname=current_setting('schema.var')) AND a.attnum > 0 AND c.relkind ='r'
  UNION ALL
  SELECT 3, 'alter table "'||c.relname||'" rename to '||lower(c.relname)||';'
  FROM pg_catalog.pg_class c
     LEFT JOIN pg_catalog.pg_namespace n ON n.oid = c.relnamespace
  WHERE c.relkind ='r' AND n.nspname=current_setting('schema.var')
  ORDER BY 1;
  ```

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

   ![Mostra le sottoscrizioni del portale](media/tutorial-oracle-azure-postgresql-online/portal-select-subscriptions.png)

2. Selezionare la sottoscrizione in cui si desidera creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

    ![Visualizzare i provider di risorse](media/tutorial-oracle-azure-postgresql-online/portal-select-resource-provider.png)

3. Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.

    ![Registrare il provider di risorse](media/tutorial-oracle-azure-postgresql-online/portal-register-resource-provider.png)

## <a name="create-a-dms-instance"></a>Creare un'istanza del servizio Migrazione del database

1. Nel portale di Azure selezionare **+ Crea una risorsa,** cercare Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

    ![Azure Marketplace](media/tutorial-oracle-azure-postgresql-online/portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-oracle-azure-postgresql-online/dms-create2.png)
  
3. Nella schermata **Crea servizio Migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare una rete virtuale esistente o crearne una nuova.

    La rete virtuale consente al Servizio Migrazione del database di Azure di accedere all'istanza di Oracle di origine e all'istanza di Database di Azure per PostgreSQL di destinazione.

    Per altre informazioni su come creare una rete virtuale nel portale di Azure, vedere l'articolo [Creare una rete virtuale usando il portale di Azure](https://aka.ms/DMSVnet).

5. Selezione di un piano tariffario.

    Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing).

    ![Configurare le impostazioni dell'istanza del Servizio Migrazione del database di Azure](media/tutorial-oracle-azure-postgresql-online/dms-settings5.png)

6. Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato il servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

    ![Individuare tutte le istanze del Servizio Migrazione del database di Azure](media/tutorial-oracle-azure-postgresql-online/dms-search.png)

2. Nella schermata **Servizi Migrazione del database di Azure** cercare il nome dell'istanza Servizio Migrazione del database di Azure appena creata e quindi selezionare l'istanza.

    ![Individuare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-oracle-azure-postgresql-online/dms-instance-search.png)

3. Selezionare **+ Nuovo progetto di migrazione**.
4. Nella schermata **Nuovo progetto di migrazione** specificare il nome del progetto, nella casella di testo **Tipo del server di origine** selezionare **Oracle** e nella casella di testo **Tipo del server di destinazione** selezionare **Database di Azure per PostgreSQL**.
5. Nella sezione **Scegli il tipo di attività** selezionare **Migrazione dei dati online**.

   ![Creare il progetto del Servizio Migrazione del database](media/tutorial-oracle-azure-postgresql-online/dms-create-project5.png)

   > [!NOTE]
   > In alternativa, è possibile scegliere **Crea solo il progetto** per creare ora il progetto di migrazione ed eseguire la migrazione in un secondo momento.

6. Selezionare **Salva**. Prendere nota dei requisiti per usare correttamente il Servizio Migrazione del database di Azure per eseguire una migrazione online e quindi selezionare **Crea ed esegui attività**.

## <a name="specify-source-details"></a>Specificare le informazioni di origine

* Nella schermata **Aggiungi dettagli origine** specificare i dettagli di connessione per l'istanza di Oracle di origine.

  ![Schermata Aggiungi dettagli origine](media/tutorial-oracle-azure-postgresql-online/dms-add-source-details.png)

## <a name="upload-oracle-oci-driver"></a>Caricare il driver Oracle OCI

1. Selezionare **Salva** e quindi nella schermata **Installare il driver OCI** accedere all'account Oracle e scaricare il driver **instantclient-basiclite-windows.x64-12.2.0.1.0.zip** (37.128.586 byte(s)) (checksum SHA1: 865082268) da [qui](https://www.oracle.com/technetwork/topics/winx64soft-089540.html#ic_winx64_inst).
2. Scaricare il driver in una cartella condivisa.

   Verificare che la cartella sia condivisa con il nome utente specificato con accesso minimo di sola lettura. Il Servizio Migrazione del database di Azure accede e legge dalla condivisione per caricare il driver OCI in Azure impersonando il nome utente specificato.

   Il nome utente specificato deve essere un account utente Windows.

   ![Installazione del driver OCI](media/tutorial-oracle-azure-postgresql-online/dms-oci-driver-install.png)

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Selezionare **Salva** e quindi nella schermata **Dettagli destinazione** specificare i dettagli di connessione per il server di Database di Azure per PostgreSQL di destinazione, ovvero l'istanza di Database di Azure per PostgreSQL di cui è già stato effettuato il provisioning e in cui è stato distribuito lo schema **HR**.

    ![Schermata Dettagli destinazione](media/tutorial-oracle-azure-postgresql-online/dms-add-target-details1.png)

2. Selezionare **Salva** e quindi nella schermata **Mappa ai database di destinazione** eseguire il mapping del database di origine e di quello di destinazione per la migrazione.

    Se il database di destinazione contiene lo stesso nome di database del database di origine, il Servizio Migrazione del database di Azure seleziona il database di destinazione per impostazione predefinita.

    ![Eseguire il mapping nei database di destinazione](media/tutorial-oracle-azure-postgresql-online/dms-map-target-details.png)

3. Selezionare **Salva**, nella schermata **Riepilogo della migrazione**, nella casella di testo **Nome attività**, specificare un nome per l'attività di migrazione, quindi rivedere il riepilogo per verificare che i dettagli dell'origine e della destinazione corrispondano a quanto specificato in precedenza.

    ![Riepilogo della migrazione](media/tutorial-oracle-azure-postgresql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Eseguire la migrazione

* Selezionare **Esegui migrazione**.

  Verrà visualizzata la finestra dell'attività di migrazione con il campo **Stato** dell'attività impostato su **Inizializzazione in corso**.

## <a name="monitor-the-migration"></a>Monitorare la migrazione

1. Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione finché nel campo **Stato** della migrazione non viene indicato **In esecuzione**.

     ![Stato attività: in esecuzione](media/tutorial-oracle-azure-postgresql-online/dms-activity-running.png)

2. In **Nome database** selezionare un database specifico per ottenere lo stato di migrazione per le operazioni **Caricamento completo** e **Sincronizzazione dei dati incrementale**.

    Caricamento completo indica lo stato di migrazione del carico iniziale, mentre Sincronizzazione dei dati incrementale indica lo stato di Change Data Capture (CDC).

     ![Stato attività: caricamento completo completato](media/tutorial-oracle-azure-postgresql-online/dms-activity-full-load-completed.png)

     ![Stato attività: sincronizzazione dei dati incrementale](media/tutorial-oracle-azure-postgresql-online/dms-activity-incremental-data-sync.png)

## <a name="perform-migration-cutover"></a>Eseguire il cutover della migrazione

Al termine del caricamento completo iniziale, i database vengono contrassegnati con **Pronto per il cutover**.

1. Quando si è pronti per completare la migrazione del database, selezionare **Avvia cutover**.

2. Assicurarsi di arrestare tutte le transazioni in ingresso nel database di origine. Attendere finché il contatore **Modifiche in sospeso** mostra **0**.

   ![Avvia cutover](media/tutorial-oracle-azure-postgresql-online/dms-start-cutover.png)

3. Selezionare **Conferma** e quindi **Applica**.
4. Quando lo stato della migrazione del database è **Completata**, connettere le applicazioni alla nuova istanza di Database di Azure per PostgreSQL di destinazione.

 > [!NOTE]
 > Poiché per impostazione predefinita schema.table.column in PostgreSQL è in lettere minuscole, è possibile passare nuovamente dal formato in lettere maiuscole a quello in lettere minuscole usando lo script contenuto nella sezione **Configurare lo schema in Database di Azure per PostgreSQL** di questo articolo.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sulle limitazioni e i problemi noti delle migrazioni online verso il Database di Azure per PostgreSQL, vedere l'articolo sui [problemi noti e soluzioni alternative per le migrazioni online in Database di Azure per PostgreSQL](known-issues-azure-postgresql-online.md).
* Per informazioni sul Servizio Migrazione del database di Azure, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview).
* Per informazioni sul Database di Azure per PostgreSQL, vedere l'articolo [Database di Azure per PostgreSQL](https://docs.microsoft.com/azure/postgresql/overview).
