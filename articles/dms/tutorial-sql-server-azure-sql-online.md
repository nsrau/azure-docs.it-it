---
title: 'Esercitazione: Usare Servizio Migrazione del database di Azure per eseguire la migrazione online da SQL Server a un database singolo o in pool in Database SQL di Azure | Microsoft Docs'
description: Informazioni su come eseguire la migrazione online da SQL Server in locale a un database singolo o in pool in Database SQL di Azure con Servizio Migrazione del database di Azure.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/09/2019
ms.openlocfilehash: e5666a64e4160964e2c1b35707a0f064edb72460
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706898"
---
# <a name="tutorial-migrate-sql-server-to-a-single-database-or-pooled-database-in-azure-sql-database-online-using-dms"></a>Esercitazione: Eseguire la migrazione online di SQL Server a un database singolo o in pool in Database SQL di Azure con Servizio Migrazione del database

È possibile usare il servizio Migrazione del database di Azure per eseguire la migrazione dei database da un'istanza di SQL Server locale al [database SQL di Azure](https://docs.microsoft.com/azure/sql-database/) con tempi di inattività minimi. In questa esercitazione si esegue la migrazione del database **AdventureWorks2012** ripristinato in un'istanza locale di SQL Server 2016 o versione successiva verso un database singolo o in pool in Database SQL di Azure usando Servizio Migrazione del database di Azure.

In questa esercitazione si apprenderà come:
> [!div class="checklist"]
> - Valutare il database locale usando Data Migration Assistant.
> - Eseguire la migrazione dello schema di esempio con Data Migration Assistant.
> - Creare un'istanza del Servizio Migrazione del database di Azure.
> - Creare un progetto di migrazione tramite il Servizio Migrazione del database di Azure.
> - Eseguire la migrazione.
> - Monitorare la migrazione.
> - Scaricare un report di migrazione.

> [!NOTE]
> L'uso del Servizio Migrazione del database di Azure per eseguire una migrazione online richiede la creazione di un'istanza basata sul piano tariffario Premium. Per altre informazioni, vedere la pagina dei [prezzi](https://azure.microsoft.com/pricing/details/database-migration/) di Servizio Migrazione del database di Azure.

> [!IMPORTANT]
> Per un'esperienza di migrazione ottimale, Microsoft consiglia di creare un'istanza del Servizio Migrazione del database di Azure nella stessa area di Azure del database di destinazione. Lo spostamento dei dati tra regioni o aree geografiche può rallentare il processo di migrazione e causare errori.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Questo articolo descrive una migrazione online da SQL Server a un database singolo o in pool in Database SQL di Azure. Per una migrazione offline, vedere [Eseguire la migrazione di SQL Server al database SQL di Azure offline con Servizio Migrazione del database](tutorial-sql-server-to-azure-sql.md).

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

- Scaricare e installare [SQL Server 2012 o versione successiva](https://www.microsoft.com/sql-server/sql-server-downloads).
- Abilitare il protocollo TCP/IP, che viene disabilitato per impostazione predefinita durante l'installazione di SQL Server Express, seguendo le istruzioni riportate nell'articolo [Abilitare o disabilitare un protocollo di rete del server](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-or-disable-a-server-network-protocol#SSMSProcedure).
- Creare un database singolo o in pool in Database SQL di Azure seguendo le istruzioni dettagliate riportate nell'articolo [Creare un database singolo in Database SQL di Azure usando il portale di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-get-started).

    > [!NOTE]
    > Se si usa SQL Server Integration Services (SSIS) e si intende eseguire la migrazione del database di catalogo per i progetti/pacchetti SSIS (SSISDB) da SQL Server al database SQL di Azure, il database SSISDB di destinazione verrà creato e gestito automaticamente per conto dell'utente quando si esegue il provisioning di SSIS in Azure Data Factory (ADF). Per altre informazioni sulla migrazione dei pacchetti SSIS, vedere l'articolo [Eseguire la migrazione di pacchetti SQL Server Integration Services in Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

- Scaricare e installare [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) 3.3 o versione successiva.
- Creare una rete virtuale di Azure per Servizio Migrazione del database di Azure usando il modello di distribuzione Azure Resource Manager, che offre la connettività da sito a sito per i server di origine locali con [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) o [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Per altre informazioni sulla creazione di una rete virtuale, vedere [Documentazione sulla rete virtuale](https://docs.microsoft.com/azure/virtual-network/), in particolare gli articoli di avvio rapido con istruzioni dettagliate.

    > [!NOTE]
    > Durante la configurazione della rete virtuale, se si usa ExpressRoute con peering di rete per Microsoft, aggiungere gli [endpoint](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) di servizio seguenti alla subnet in cui verrà effettuato il provisioning del servizio:
    > - Endpoint del database di destinazione (ad esempio endpoint SQL, endpoint Cosmos DB e così via)
    > - Endpoint di archiviazione
    > - Endpoint bus di servizio
    >
    > Questa configurazione è necessaria perché Servizio Migrazione del database di Azure non dispone di connettività Internet.

- Verificare che le regole del gruppo di sicurezza di rete per la rete virtuale non blocchino le porte di comunicazione in ingresso nel Servizio Migrazione del database di Azure: 443, 53, 9354, 445, 12000. Per informazioni dettagliate sui filtri del traffico dei gruppi di sicurezza di rete relativi alla rete virtuale di Azure, vedere l'articolo [Filtrare il traffico di rete con gruppi di sicurezza di rete](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Configurare [Windows Firewall per l'accesso al motore di database](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
- Aprire Windows Firewall per consentire al Servizio Migrazione del database di Azure di accedere a SQL Server di origine (per impostazione predefinita attraverso la porta TCP 1433).
- Se si eseguono più istanze di SQL Server denominate usando le porte dinamiche, è consigliabile abilitare il Servizio browser SQL e consentire l'accesso alla porta UDP 1434 attraverso i firewall, in modo che il Servizio Migrazione del database di Azure possa connettersi a un'istanza denominata nel server di origine.
- Quando si usa un'appliance firewall all'ingresso dei database di origine, potrebbe essere necessario aggiungere regole del firewall per consentire al Servizio Migrazione del database di Azure di accedere ai database di origine per la migrazione.
- Creare una [regola del firewall](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) a livello di server per il server di database SQL di Azure per consentire al Servizio Migrazione del database di Azure di accedere ai database di destinazione. Specificare l'intervallo di subnet della rete virtuale usato per il Servizio Migrazione del database di Azure.
- Assicurarsi che le credenziali usate per connettersi all'istanza di origine di SQL Server abbiano le autorizzazioni [CONTROL SERVER](https://docs.microsoft.com/sql/t-sql/statements/grant-server-permissions-transact-sql).
- Assicurarsi che le credenziali usate per connettersi all'istanza del database SQL di Azure di destinazione abbiano l'autorizzazione CONTROL DATABASE nei database SQL di Azure di destinazione.
- La versione dell'istanza di SQL Server di origine deve essere SQL Server 2005 o superiore. Per determinare la versione eseguita dall'istanza di SQL Server, vedere l'articolo [Come determinare la versione, l'edizione e il livello di aggiornamento di SQL Server e dei relativi componenti](https://support.microsoft.com/help/321185/how-to-determine-the-version-edition-and-update-level-of-sql-server-an).
- I database devono essere in modalità di recupero con registrazione minima delle operazioni bulk o in modalità di recupero con registrazione completa. Per determinare il modello di recupero configurato per l'istanza di SQL Server, vedere l'articolo [Visualizzare o modificare il modello di recupero di un database (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/view-or-change-the-recovery-model-of-a-database-sql-server?view=sql-server-2017).
- Assicurarsi di eseguire i backup completi dei database. Per creare un backup completo del database, vedere l'articolo [Procedura: Creazione di un backup completo del database (Transact-SQL)](https://docs.microsoft.com/previous-versions/sql/sql-server-2008-r2/ms191304(v=sql.105)).
- Se una o più tabelle non dispongono di una chiave primaria, abilitare Change Data Capture (CDC) nel database e nelle tabelle specifiche.
    > [!NOTE]
    > È possibile usare lo script seguente per trovare tutte le tabelle che non dispongono di chiavi primarie.

    ```sql
    USE <DBName>;
    go
    SELECT is_tracked_by_cdc, name AS TableName
    FROM sys.tables WHERE type = 'U' and is_ms_shipped = 0 AND
    OBJECTPROPERTY(OBJECT_ID, 'TableHasPrimaryKey') = 0;
     ```

    >Se i risultati mostrano una o più tabelle con 'is_tracked_by_cdc' a '0', abilitare l'acquisizione delle modifiche per il database e per le tabelle specifiche tramite la procedura descritta nell'articolo [Abilitare e disabilitare Change Data Capture (SQL Server)](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server?view=sql-server-2017).

- Configurare il ruolo del database di distribuzione per SQL Server di origine.

    >[!NOTE]
    > È possibile determinare se i componenti di replica sono installati con la query seguente.

    ```sql
    USE master;
    DECLARE @installed int;
    EXEC @installed = sys.sp_MS_replication_installed;
    SELECT @installed as installed;
    ```

    Se il risultato restituisce un messaggio di errore che consiglia di installare i componenti di replica, installare i componenti di replica di SQL Server con il processo indicato nell'articolo [Installare la replica di SQL Server](https://docs.microsoft.com/sql/database-engine/install-windows/install-sql-server-replication?view=sql-server-2017).

    Se la replica è già installata, controllare se il ruolo di distribuzione è configurato nell'istanza di SQL Server di origine con il comando T-SQL seguente.

    ```sql
    EXEC sp_get_distributor;
    ```

    Se la distribuzione non è configurata, se il server di distribuzione mostra NULL per l'output del comando precedente, configurare la distribuzione tramite la procedura descritta nell'articolo [Configurazione della pubblicazione e della distribuzione](https://docs.microsoft.com/sql/relational-databases/replication/configure-publishing-and-distribution?view=sql-server-2017).

- Disabilitare i trigger del database nel database SQL di Azure di destinazione.
    >[!NOTE]
    > È possibile trovare i trigger del database nel database SQL di Azure di destinazione usando la query seguente:

    ```sql
    Use <Database name>
    select * from sys.triggers
    DISABLE TRIGGER (Transact-SQL)
    ```

    Per altre informazioni, vedere l'articolo [DISABLE TRIGGER (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017). 

## <a name="assess-your-on-premises-database"></a>Valutare il database locale

Prima di eseguire la migrazione dei dati da un'istanza locale di SQL Server a un database singolo o in pool in Database SQL di Azure, è necessario valutare il database di SQL Server per rilevare eventuali problemi che potrebbero causare un blocco e impedire la migrazione. Usando Data Migration Assistant v3.3 o versione successiva, seguire la procedura descritta nell'articolo [Eseguire una valutazione della migrazione a SQL Server](https://docs.microsoft.com/sql/dma/dma-assesssqlonprem) per completare la valutazione del database locale.

Per valutare un database locale, seguire i passaggi seguenti:

1. In DMA selezionare l'icona Nuovo (+) e quindi selezionare il tipo di progetto **Valutazione**.
2. Specificare il nome del progetto, nella casella di testo **Tipo del server di origine** selezionare **SQL Server**, nella casella di testo **Tipo del server di destinazione** selezionare **Database SQL di Azure** e quindi selezionare **Crea** per creare il progetto.

    Quando si valuta il database di SQL Server di origine per la migrazione a un database singolo o in pool in Database SQL di Azure, si può scegliere uno o entrambi i tipi di report di valutazione seguenti:

   - Check database compatibility (Verificare la compatibilità del database)
   - Check feature parity (Verificare la parità di funzionalità)

     Entrambi i tipi di report sono selezionati per impostazione predefinita.

3. In DMA, nella schermata **Opzioni**, selezionare **Avanti**.
4. Nella schermata **Seleziona origini** della finestra di dialogo **Connetti a un server** indicare i dettagli della connessione a SQL Server e quindi selezionare **Connetti**.
5. Nella finestra di dialogo **Aggiungi origini** selezionare **AdventureWorks2012**, selezionare **Aggiungi** e quindi selezionare **Start Assessment** (Avvia valutazione).

    > [!NOTE]
    > Se si usa SSIS, DMA non supporta al momento la valutazione del database SSISDB di origine. I progetti/pacchetti SSIS verranno tuttavia valutati/convalidati man mano che vengono ridistribuiti nel database SSISDB di destinazione ospitato dal database SQL di Azure. Per altre informazioni sulla migrazione dei pacchetti SSIS, vedere l'articolo [Eseguire la migrazione di pacchetti SQL Server Integration Services in Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

    Al termine della valutazione, i risultati vengono visualizzati come mostrato nella figura seguente:

    ![Valutare la migrazione dei dati](media/tutorial-sql-server-to-azure-sql-online/dma-assessments.png)

    Per i database singoli o in pool in Database SQL di Azure, le valutazioni identificano i problemi di parità delle funzionalità e i problemi che causano un blocco della migrazione per la distribuzione in un database singolo o in pool.

    - La categoria di **parità delle funzionalità di SQL Server** offre un set completo di indicazioni, approcci alternativi disponibili in Azure e procedure di mitigazione che consentono di pianificare le attività nei progetti di migrazione.
    - La categoria relativa ai **problemi di compatibilità** identifica funzionalità parzialmente supportate o non supportate che riflettono i problemi di compatibilità che potrebbero bloccare la migrazione dei database di SQL Server locali aL database SQL di Azure. Vengono anche fornite raccomandazioni che consentono di risolvere tali problemi.

6. Esaminare i risultati della valutazione per identificare problemi di blocco della migrazione e problemi di parità della funzionalità selezionando le opzioni specifiche.

## <a name="migrate-the-sample-schema"></a>Eseguire la migrazione dello schema di esempio

Dopo aver acquisito familiarità con la valutazione e aver verificato che il database selezionato è un candidato idoneo per la migrazione a un database singolo o in pool in Database SQL di Azure, usare DMA per eseguire la migrazione dello schema al database SQL di Azure.

> [!NOTE]
> Prima di creare un progetto di migrazione in DMA, assicurarsi di aver già eseguito il provisioning di un database SQL di Azure come indicato nei prerequisiti. Ai fini di questa esercitazione, si presuppone che il nome del database SQL di Azure sia **AdventureWorksAzure**, ma è possibile specificare il nome che si preferisce.

> [!IMPORTANT]
> Se si usa SSIS, DMA non supporta al momento la migrazione del database SSISDB di origine, ma è possibile ridistribuire i propri progetti/pacchetti SSIS nel database SSISDB di destinazione ospitato dal database SQL di Azure. Per altre informazioni sulla migrazione dei pacchetti SSIS, vedere l'articolo [Eseguire la migrazione di pacchetti SQL Server Integration Services in Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

Per eseguire la migrazione dello schema di **AdventureWorks2012** a un database singolo o in pool in Database SQL di Azure, seguire questa procedura:

1. In Data Migration Assistant selezionare l'icona Nuovo (+) e quindi in **Tipo di progetto** selezionare **Migrazione**.
2. Specificare il nome del progetto, nella casella di testo **Source server type** (Tipo di server di origine) selezionare **SQL Server** e quindi nella casella di testo **Target server type** (Tipo di server di destinazione) selezionare **Database SQL di Azure**.
3. In **Migration Scope** (Ambito della migrazione) selezionare **Schema only** (Solo schema).

    Dopo aver eseguito i passaggi precedenti, viene visualizzata l'interfaccia di DMA come illustrato nella figura seguente:

    ![Creare un progetto di Data Migration Assistant](media/tutorial-sql-server-to-azure-sql-online/dma-create-project.png)

4. Selezionare **Crea** per creare il progetto.
5. In DMA specificare i dettagli della connessione di origine per Server SQL, selezionare **Connetti**, quindi selezionare il database **AdventureWorks2012**.

    ![Dettagli della connessione di origine Data Migration Assistant](media/tutorial-sql-server-to-azure-sql-online/dma-source-connect.png)

6. Selezionare **Avanti** in **Connetti al server di destinazione** specificare i dettagli della connessione di destinazione per il database SQL di Azure, selezionare **Connetti**, quindi selezionare il database **AdventureWorksAzure** di cui è già stato eseguito il provisioning nel database SQL di Azure.

    ![Dettagli della connessione di destinazione Data Migration Assistant](media/tutorial-sql-server-to-azure-sql-online/dma-target-connect.png)

7. Selezionare **Avanti** per passare alla schermata **Seleziona oggetti**, in cui è possibile specificare gli oggetti dello schema nel database **AdventureWorks2012** che devono essere distribuiti in database SQL di Azure.

    Per impostazione predefinita, sono selezionati tutti gli oggetti.

    ![Generare script SQL](media/tutorial-sql-server-to-azure-sql-online/dma-assessment-source.png)

8. Selezionare **Genera script SQL** per creare gli script SQL e quindi esaminare gli script per individuare eventuali errori.

    ![Script dello schema](media/tutorial-sql-server-to-azure-sql-online/dma-schema-script.png)

9. Selezionare **Deploy schema** (Distribuisci schema) per distribuire lo schema nel database SQL di Azure e in seguito alla distribuzione dello schema, verificare che il server di destinazione non presenti anomalie.

    ![Distribuire lo schema](media/tutorial-sql-server-to-azure-sql-online/dma-schema-deploy.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registrare il provider di risorse Microsoft.DataMigration

1. Accedere al portale di Azure, selezionare **Tutti i servizi**, quindi selezionare **Sottoscrizioni**.

   ![Mostra le sottoscrizioni del portale](media/tutorial-sql-server-to-azure-sql-online/portal-select-subscription1.png)

2. Selezionare la sottoscrizione in cui si desidera creare l'istanza del Servizio Migrazione del database di Azure e quindi selezionare **Provider di risorse**.

    ![Visualizzare i provider di risorse](media/tutorial-sql-server-to-azure-sql-online/portal-select-resource-provider.png)

3. Ricercare la migrazione e quindi a destra del **Microsoft.DataMigration** selezionare **Registro**.

    ![Registrare il provider di risorse](media/tutorial-sql-server-to-azure-sql-online/portal-register-resource-provider.png)

## <a name="create-an-instance"></a>Creare un'istanza

1. Nel portale di Azure selezionare **+ Crea una risorsa,** cercare Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure** dall'elenco a discesa.

    ![Azure Marketplace](media/tutorial-sql-server-to-azure-sql-online/portal-marketplace.png)

2. Nella schermata **Servizio Migrazione del database di Azure** selezionare **Crea**.

    ![Creare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-azure-sql-online/dms-create1.png)
  
3. Nella schermata **Crea servizio Migrazione** specificare un nome per il servizio, la sottoscrizione e un gruppo di risorse nuovo o esistente.

4. Selezionare la località in cui si vuole creare l'istanza di Servizio Migrazione del database di Azure. 

5. Selezionare una rete virtuale esistente o crearne una nuova.

    La VNet consente al Servizio Migrazione del database di Azure di accedere all'istanza di SQL Server di origine e all'istanza del database SQL di Azure di destinazione.

    Per altre informazioni su come creare una rete virtuale nel portale di Azure, vedere l'articolo [Creare una rete virtuale usando il portale di Azure](https://aka.ms/DMSVnet).

6. Selezione di un piano tariffario.

    Per altre informazioni sui costi e i piani tariffari, vedere la [pagina relativa ai prezzi](https://aka.ms/dms-pricing).

    ![Configurare le impostazioni dell'istanza del Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-azure-sql-online/dms-settings2.png)

7. Selezionare **Crea** per creare il servizio.

## <a name="create-a-migration-project"></a>Creare un progetto di migrazione

Dopo aver creato il servizio, individuarlo nel portale di Azure, aprirlo e creare un nuovo progetto di migrazione.

1. Nel portale di Azure selezionare **Tutti i servizi**, eseguire la ricerca di Servizio Migrazione del database di Azure e quindi selezionare **Servizio Migrazione del database di Azure**.

    ![Individuare tutte le istanze del Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-azure-sql-online/dms-search.png)

2. Nella schermata **Servizi Migrazione del database di Azure** cercare il nome dell'istanza Servizio Migrazione del database di Azure appena creata e quindi selezionare l'istanza.

    ![Individuare l'istanza del Servizio Migrazione del database di Azure](media/tutorial-sql-server-to-azure-sql-online/dms-instance-search.png)

3. Selezionare **+ Nuovo progetto di migrazione**.
4. Nella schermata **Nuovo progetto di migrazione** specificare il nome del progetto, nella casella di testo **Tipo del server di origine** selezionare **SQL Server** e nella casella di testo **Tipo del server di destinazione** selezionare **Database SQL di Azure**.
5. Nella sezione **Scegli il tipo di attività** selezionare **Migrazione dei dati online**.

    ![Creare il progetto del Servizio Migrazione del database](media/tutorial-sql-server-to-azure-sql-online/dms-create-project3.png)

    > [!NOTE]
    > In alternativa, è possibile scegliere **Crea solo il progetto** per creare subito il progetto di migrazione ed eseguire la migrazione in un secondo momento.

6. Selezionare **Salva**.

7. Selezionare **Crea ed esegui attività** per creare il progetto ed eseguire l'attività di migrazione.

    ![Creare ed eseguire un'attività di Servizio Migrazione del database](media/tutorial-sql-server-to-azure-sql-online/dms-create-and-run-activity.png)

## <a name="specify-source-details"></a>Specificare le informazioni di origine

1. Nella schermata **Dettagli origine della migrazione** specificare i dettagli di connessione per l'istanza di SQL Server di origine.

    Accertarsi di usare un nome di dominio completo (FQDN) per il nome dell'istanza di SQL Server di origine. Nelle situazioni in cui la risoluzione del nome DNS non è possibile, si può usare l'indirizzo IP.

2. Se nel server di origine non è installato un certificato attendibile, selezionare la casella di controllo **Considera attendibile certificato server**.

    Quando non è installato un certificato attendibile, SQL Server genera un certificato autofirmato all'avvio dell'istanza. Questo certificato viene usato per crittografare le credenziali per le connessioni client.

    > [!CAUTION]
    > Le connessioni SSL crittografate con un certificato autofirmato non offrono sicurezza avanzata. Sono infatti suscettibili ad attacchi man-in-the-middle. È consigliabile non usare SSL con i certificati autofirmati in un ambiente di produzione o in server connessi a Internet.

   ![Dettagli origine](media/tutorial-sql-server-to-azure-sql-online/dms-source-details3.png)

    > [!IMPORTANT]
    > Se si usa SSIS, il Servizio Migrazione del database non supporta al momento la migrazione del database SSISDB di origine, ma è possibile ridistribuire i propri progetti/pacchetti SSIS nel database SSISDB di destinazione ospitato dal database SQL di Azure. Per altre informazioni sulla migrazione dei pacchetti SSIS, vedere l'articolo [Eseguire la migrazione di pacchetti SQL Server Integration Services in Azure](https://docs.microsoft.com/azure/dms/how-to-migrate-ssis-packages).

## <a name="specify-target-details"></a>Specificare i dettagli della destinazione

1. Selezionare **Salva**, quindi nella schermata **Dettagli destinazione della migrazione** specificare i dettagli di connessione per il server di database SQL di Azure di destinazione, ovvero il database SQL di Azure di cui è già stato eseguito il provisioning e in cui lo schema **AdventureWorks2012** è stato distribuito tramite DMA.

    ![Selezionare la destinazione](media/tutorial-sql-server-to-azure-sql-online/dms-select-target3.png)

2. Selezionare **Salva** e quindi nella schermata **Mappa ai database di destinazione** eseguire il mapping del database di origine e di quello di destinazione per la migrazione.

    Se il database di destinazione contiene lo stesso nome di database del database di origine, Servizio Migrazione del database di Azure seleziona il database di destinazione per impostazione predefinita.

    ![Eseguire il mapping nei database di destinazione](media/tutorial-sql-server-to-azure-sql-online/dms-map-targets-activity3.png)

3. Selezionare **Salva**, quindi nella schermata **Seleziona tabelle** espandere l'elenco delle tabelle ed esaminare l'elenco dei campi interessati.

    Il servizio Migrazione del database di Azure seleziona automaticamente tutte le tabelle di origine vuote esistenti nell'istanza di database SQL di Azure di destinazione. Se si vuole eseguire di nuovo la migrazione delle tabelle che includono già dati, occorre selezionarle esplicitamente in questo pannello.

    ![Selezionare le tabelle](media/tutorial-sql-server-to-azure-sql-online/dms-configure-setting-activity3.png)

4. Selezionare **Salva**, nella schermata **Riepilogo della migrazione**, nella casella di testo **Nome attività**, specificare un nome per l'attività di migrazione, quindi rivedere il riepilogo per verificare che i dettagli dell'origine e della destinazione corrispondano a quanto specificato in precedenza.

    ![Riepilogo della migrazione](media/tutorial-sql-server-to-azure-sql-online/dms-migration-summary.png)

## <a name="run-the-migration"></a>Eseguire la migrazione

- Selezionare **Esegui migrazione**.

    Verrà visualizzata la finestra dell'attività di migrazione con il campo **Stato** dell'attività impostato su **Inizializzazione in corso**.

    ![Stato attività - Inizializzazione in corso](media/tutorial-sql-server-to-azure-sql-online/dms-activity-status2.png)

## <a name="monitor-the-migration"></a>Monitorare la migrazione

1. Nella schermata dell'attività di migrazione selezionare **Aggiorna** per aggiornare la visualizzazione finché nel campo **Stato** della migrazione non viene indicato **In esecuzione**.

2. Fare clic su un database specifico per ottenere lo stato di migrazione per le operazioni **Caricamento completo** e **Sincronizzazione dei dati incrementale**.

    ![Stato attività - In corso](media/tutorial-sql-server-to-azure-sql-online/dms-activity-in-progress.png)

## <a name="perform-migration-cutover"></a>Eseguire il cutover della migrazione

Al termine del caricamento completo iniziale, i database vengono contrassegnati con **Pronto per il cutover**.

1. Quando si è pronti per completare la migrazione del database, selezionare **Avvia cutover**.

    ![Avvia cutover](media/tutorial-sql-server-to-azure-sql-online/dms-start-cutover.png)

2. Assicurarsi di arrestare tutte le transazioni in ingresso nel database di origine. Attendere finché il contatore **Modifiche in sospeso** mostra **0**.
3. Selezionare **Conferma** e quindi **Applica**.
4. Quando lo stato della migrazione del database è **Completata**, connettere le applicazioni al nuovo database SQL di Azure di destinazione.

    ![Stato attività - Completata](media/tutorial-sql-server-to-azure-sql-online/dms-activity-completed.png)

## <a name="next-steps"></a>Passaggi successivi

- Laboratorio pratico sulla [migrazione SQL con il servizio Migrazione del database di Azure](https://www.microsoft.com/handsonlabs/SelfPacedLabs/?storyGuid=3b671509-c3cd-4495-8e8f-354acfa09587).
- Per informazioni sulle limitazioni e i problemi noti, vedere l'articolo relativo a [problemi noti e soluzioni alternative per le migrazioni online al database SQL di Azure](known-issues-azure-sql-online.md).
- Per informazioni sul Servizio Migrazione del database di Azure, vedere l'articolo [Definizione del Servizio Migrazione del database di Azure](https://docs.microsoft.com/azure/dms/dms-overview).
- Per informazioni sul database SQL di Azure, vedere l'articolo [Servizio database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-technical-overview).
