---
title: Migrare un database SQL Server nel database SQL di Azure | Documentazione Microsoft
description: Informazioni sulla migrazione di un database SQL Server a un database SQL di Azure.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,migrate
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 09/01/2017
ms.author: carlrab
ms.openlocfilehash: b62003fb2758393d5ed8fddad8bd4d028424a2bb
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2017
---
# <a name="migrate-your-sql-server-database-to-azure-sql-database"></a>Migrare un database SQL Server in un database SQL di Azure

La migrazione di un database SQL Server a un database SQL di Azure è un'operazione semplice che prevede la creazione di un database SQL vuoto in Azure e quindi l'uso di [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) per l'importazione del database in Azure. In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un database SQL di Azure vuoto nel portale di Azure (usando un server di database SQL di Azure nuovo o esistente)
> * Creare un firewall a livello di server nel portale di Azure (se non è stato ancora fatto)
> * Usare [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) per importare il database SQL Server nel database SQL di Azure vuoto 
> * Usare [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) per modificare le proprietà del database.

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, verificare che siano soddisfatti i prerequisiti seguenti:

- Installato la versione più recente di [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS).  
- È stata installata la versione più recente di [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA).
- È avvenuta l'identificazione e si ha avuto accesso a un database per eseguire la migrazione. Questa esercitazione usa il [database OLTP SQL Server 2008R2 AdventureWorks](https://msftdbprodsamples.codeplex.com/releases/view/59211) in un'istanza di SQL Server 2008 R2 o versione più recenti, ma è possibile usare qualsiasi database di propria scelta. Per risolvere i problemi di compatibilità, usare [SQL Server Data Tools](https://docs.microsoft.com/sql/ssdt/download-sql-server-data-tools-ssdt).

## <a name="log-in-to-the-azure-portal"></a>Accedere al Portale di Azure.

Accedere al [Portale di Azure](https://portal.azure.com/).

## <a name="create-a-blank-sql-database"></a>Creare un database SQL vuoto

Un database SQL di Azure viene creato con un set definito di [risorse di calcolo e di archiviazione](sql-database-service-tiers.md). Il database viene creato in un [gruppo di risorse di Azure](../azure-resource-manager/resource-group-overview.md) e in un [server logico di database SQL di Azure](sql-database-features.md). 

Per creare un database SQL vuoto, attenersi alla procedura seguente. 

1. Fare clic sul pulsante **Nuovo** nell'angolo superiore sinistro del portale di Azure.

2. Selezionare **Database** dalla pagina **Nuovo**, quindi selezionare **Crea** in **Database SQL** nella pagina **Nuovo**.

   ![creare database vuoto](./media/sql-database-design-first-database/create-empty-database.png)

3. Compilare il modulo Database SQL con le informazioni seguenti, come illustrato nell'immagine precedente:   

   | Impostazione       | Valore consigliato | Descrizione | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Database name** (Nome database) | mySampleDatabase | Per i nomi di database validi, vedere [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificatori di database). | 
   | **Sottoscrizione** | Sottoscrizione in uso  | Per informazioni dettagliate sulle sottoscrizioni, vedere [Subscriptions](https://account.windowsazure.com/Subscriptions) (Sottoscrizioni). |
   | **Gruppo di risorse** | myResourceGroup | Per i nomi di gruppi di risorse validi, vedere [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Regole di denominazione e restrizioni). |
   | **Select source** (Seleziona origine) | Database vuoto | Indica che deve essere creato un database vuoto. |

4. Fare clic su **Server** per creare e configurare un nuovo server per il nuovo database. Compilare il **modulo del nuovo server** con le informazioni seguenti: 

   | Impostazione       | Valore consigliato | Descrizione | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Server name** (Nome server) | Qualsiasi nome globalmente univoco | Per i nomi di server validi, vedere [Naming rules and restrictions](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions) (Regole di denominazione e restrizioni). | 
   | **Nome di accesso amministratore server** | Qualsiasi nome valido | Per i nomi di accesso validi, vedere [Database Identifiers](https://docs.microsoft.com/sql/relational-databases/databases/database-identifiers) (Identificatori di database).|
   | **Password** | Qualsiasi password valida | La password deve contenere almeno otto caratteri delle tre categorie seguenti: maiuscole, minuscole, numeri e caratteri non alfanumerici. |
   | **Posizione** | Qualsiasi località valida | Per informazioni sulle aree, vedere [Aree di Azure](https://azure.microsoft.com/regions/). |

   ![Creare il server di database](./media/sql-database-design-first-database/create-database-server.png)

5. Fare clic su **Seleziona**.

6. Fare clic su **Piano tariffario** per specificare il livello di servizio, il numero di DTU e la quantità di risorse di archiviazione. Esplorare le opzioni relative al numero di DTU e di risorse di archiviazione disponibile per ogni livello di servizio. 

7. Per questa esercitazione selezionare il livello di servizio **Standard** e quindi usare il dispositivo di scorrimento per selezionare **100 DTU (S3)** e **400** GB di archiviazione.

   ![Creare il database s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

8. Accettare le condizioni dell'anteprima per usare l'opzione **Spazio di archiviazione aggiuntivo**. 

   > [!IMPORTANT]
   > \* Le dimensioni di archiviazione superiori alla quantità di risorse di archiviazione incluse sono disponibili in anteprima e vengono applicati costi aggiuntivi. Per ulteriori informazioni, vedere [Database SQL Prezzi](https://azure.microsoft.com/pricing/details/sql-database/). 
   >
   >\* Nel livello Premium sono attualmente disponibili più di 1 TB di risorse di archiviazione nelle aree seguenti: Stati Uniti orientali 2, Stati Uniti occidentali, US Gov Virginia, Europa occidentale, Germania centrale, Asia sud-orientale, Giappone orientale, Australia orientale, Canada centrale e Canada orientale. Vedere [Limitazioni correnti per P11-P15](sql-database-resource-limits.md#single-database-limitations-of-p11-and-p15-when-the-maximum-size-greater-than-1-tb).  
   > 

9. Dopo la selezione del livello di servizio, del numero di DTU e della quantità di risorse di archiviazione, fare clic su **Applica**.  

10. Selezionare **regole di confronto** per il database vuoto. Per questa esercitazione usare il valore predefinito. Per altre informazioni sulle regole di confronto, vedere [Collations](https://docs.microsoft.com/sql/t-sql/statements/collations) (Regole di confronto)

11. Dopo aver completato il modulo del database SQL, fare clic su **Crea** per effettuare il provisioning del database. Il provisioning richiede alcuni minuti. 

12. Sulla barra degli strumenti fare clic su **Notifiche** per monitorare il processo di distribuzione.
    
     ![notifica](./media/sql-database-get-started-portal/notification.png)

## <a name="create-a-server-level-firewall-rule"></a>Creare una regola del firewall a livello di server

Il servizio di database SQL crea un firewall a livello di server che impedisce alle applicazioni e agli strumenti esterni di connettersi al server o ai database sul server a meno che non venga creata una regola del firewall per aprire il firewall per indirizzi IP specifici. Seguire questi passaggi per creare una [regola del firewall a livello di server di database SQL](sql-database-firewall-configure.md) per l'indirizzo IP del client e abilitare la connettività esterna tramite il firewall del database SQL solo per il proprio indirizzo IP. 

> [!NOTE]
> Il database SQL comunica attraverso la porta 1433. Se si sta tentando di connettersi da una rete aziendale, il traffico in uscita attraverso la porta 1433 potrebbe non essere autorizzato dal firewall della rete. In questo caso, non è possibile connettersi al server del database SQL di Azure, a meno che il reparto IT non apra la porta 1433.
>

1. Al termine della distribuzione, scegliere **Database SQL** dal menu a sinistra e fare clic su **mySampleDatabase** nella pagina **Database SQL**. Si apre la pagina di panoramica per il database che visualizza il nome completo del server, ad esempio **mynewserver-20170824.database.windows.net**, e offre altre opzioni di configurazione. 

2. Copiare il nome completo del server per connettersi al server e ai relativi database nelle guide introduttive successive. 

   ![Nome del server](./media/sql-database-get-started-portal/server-name.png) 

3. Fare clic su **Imposta firewall server** sulla barra degli strumenti. Si apre la pagina **Impostazioni del firewall** per il server del database SQL. 

   ![Regola del firewall del server](./media/sql-database-get-started-portal/server-firewall-rule.png) 

4. Fare clic su **Aggiungi IP client** sulla barra degli strumenti per aggiungere l'indirizzo IP corrente a una nuova regola del firewall. Una regola del firewall può aprire la porta 1433 per un indirizzo IP singolo o un intervallo di indirizzi IP.

5. Fare clic su **Salva**. Viene creata una regola del firewall a livello di server per l'indirizzo IP corrente, che apre la porta 1433 nel server logico.

6. Fare clic su **OK** e quindi chiudere la pagina **Impostazioni del firewall**.

È ora possibile connettersi al server del database SQL e ai relativi database usando SQL Server Management Studio, Data Migration Assistant o un altro strumento di propria scelta da questo indirizzo IP, con l'account amministratore del server creato nella procedura precedente.

> [!IMPORTANT]
> Per impostazione predefinita, l'accesso attraverso il firewall del database SQL è abilitato per tutti i servizi di Azure. Selezionando **NO** in questa pagina permette di disabilitare tutti i servizi di Azure.

## <a name="sql-server-connection-information"></a>Informazioni di connessione SQL Server

Ottenere il nome completo del server per il server del database SQL di Azure nel portale di Azure. Usare il nome completo del server per connettersi al server SQL di Azure usando strumenti client, tra cui Data Migration Assistant e SQL Server Management Studio.

1. Accedere al [Portale di Azure](https://portal.azure.com/).
2. Scegliere **Database SQL** dal menu a sinistra, quindi fare clic sul database nella pagina **Database SQL**. 
3. Nel riquadro **Informazioni di base** della pagina del portale di Azure per il database individuare e quindi copiare il **Nome server**.

   ![informazioni di connessione](./media/sql-database-get-started-portal/server-name.png)

## <a name="migrate-your-database"></a>Eseguire la migrazione del database

Seguire questi passaggi per usare **[Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595)** per valutare l'idoneità del database alla migrazione al database SQL di Azure e completare la migrazione.

1. Aprire **Data Migration Assistant**. È possibile eseguire DMA in qualsiasi computer con connettività all'istanza di SQL Server contenente il database che si intende migrare e connettività a Internet. Non è necessario installarlo nel computer che ospita l'istanza di SQL Server di cui si sta eseguendo la migrazione. La regola del firewall creata in una procedura precedente deve essere destinata al computer in cui viene eseguito Data Migration Assistant.

     ![aprire Data Migration Assistant](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-open.png)

2. Nel menu a sinistra fare clic su **+ Nuovo** per creare un progetto **Valutazione**. Specificare i valori richiesti e quindi fare clic su **Create** (Crea):

   | Impostazione      | Valore consigliato | Descrizione | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Tipo di progetto | Migrazione | Scegliere di valutare il database per la migrazione oppure scegliere di valutare ed eseguire la migrazione nell'ambito dello stesso flusso di lavoro |
   |Project name (Nome progetto)|Migration tutorial (Esercitazione per la migrazione)| Nome descrittivo |
   |Source server type (Tipo server di origine)| SQL Server | È l'unica origine attualmente supportata |
   |Target server type (Tipo server di destinazione)| Database SQL di Azure| Le opzioni includono: Azure SQL Database (Database SQL di Azure), SQL Server, SQL Server on Azure virtual machines (SQL Server in macchine virtuali di Azure) |
   |Migration Scope (Ambito migrazione)| Schema and data (Schema e dati)| Le opzioni includono: Schema and data (Schema e dati), schema only (solo schema), data only (solo dati) |
   
   ![nuovo progetto di Data Migration Assistant](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-new-project.png)

3.  Nella pagina **Select source** (Seleziona origine) immettere i valori richiesti e quindi fare clic su **Connect** (Connetti):

    | Impostazione      | Valore consigliato | Descrizione | 
    | ------------ | ------------------ | ------------------------------------------------- | 
    | Nome server | Nome del server o indirizzo IP | Nome del server o indirizzo IP |
    | Tipo di autenticazione | Tipo di autenticazione preferito| Opzioni: Windows Authentication (Autenticazione di Windows), SQL Server Authentication (Autenticazione di SQL Server), Active Directory Integrated Authentication (Autenticazione integrata di Active Directory), Active Directory Password Authentication (Autenticazione della password di Active Directory) |
    | Username | Nome di accesso | L'account di accesso deve disporre di autorizzazioni **CONTROL SERVER** |
    | Password| Password | Password |
    | Connection properties (Proprietà connessione)| Selezionare **Encrypt connection** (Crittografia connessione) e **Trust server certificate** (Considera attendibile il certificato del server) in base al proprio ambiente. | Scegliere le proprietà appropriate per la connessione al server |

    ![nuova migrazione di dati - selezione dell'origine](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-source.png)

5. Selezionare un singolo database dal server di origine per eseguire la migrazione al database SQL di Azure e quindi fare clic su **Next** (Avanti). Per questa esercitazione, è disponibile solo un singolo database.

6. Nella pagina **Select target** (Seleziona destinazione) immettere i valori richiesti e quindi fare clic su **Connect** (Connetti):

    | Impostazione      | Valore consigliato | Descrizione | 
    | ------------ | ------------------ | ------------------------------------------------- | 
    | Nome server | Nome completo del server di database di Azure | Nome completo del server di database di Azure dalla procedura precedente |
    | Tipo di autenticazione | Autenticazione di SQL Server | SQL Server authentication (Autenticazione di SQL Server) è l'unica opzione al momento della realizzazione di questa esercitazione, ma il database SQL di Azure supporta anche le opzioni Active Directory Integrated Authentication (Autenticazione integrata di Active Directory) e Active Directory Password Authentication (Autenticazione della password di Active Directory) |
    | Username | Nome di accesso | L'account di accesso deve disporre di autorizzazioni **CONTROL DATABASE** per il database di origine |
    | Password| Password | Password |
    | Connection properties (Proprietà connessione)| Selezionare **Encrypt connection** (Crittografia connessione) e **Trust server certificate** (Considera attendibile il certificato del server) in base al proprio ambiente. | Scegliere le proprietà appropriate per la connessione al server |

    ![nuova migrazione di dati - selezione della destinazione](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-target.png)

7. Selezionare il database dal server di destinazione creato in una procedura precedente e quindi fare clic su **Next** (Avanti) per avviare il processo di valutazione dello schema del database di origine. Per questa esercitazione, è disponibile solo un singolo database. Si noti che il livello di compatibilità per il database è impostato su 140, ovvero il livello di compatibilità predefinito per tutti i nuovi database nel database SQL di Azure.

   > [!IMPORTANT] 
   > Dopo la migrazione del database al database SQL di Azure, è possibile scegliere di far funzionare il database al livello di compatibilità specificato per motivi di compatibilità con le versioni precedenti. Per altre informazioni sulle implicazioni e le opzioni per il funzionamento di un database a un livello di compatibilità specifico, vedere [ALTER DATABASE Compatibility Level](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-compatibility-level) (Livello di compatibilità ALTER DATABASE). Vedere anche [ALTER DATABASE SCOPED CONFIGURATION](https://docs.microsoft.com/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql) per informazioni sulle impostazioni a livello di database aggiuntive relative ai livelli di compatibilità.
   >

8. Nella pagina **Select objects** (Seleziona oggetti), al termine del processo di valutazione dello schema del database di origine, esaminare gli oggetti selezionati per la migrazione e quelli contenenti problemi. Ad esempio, esaminare l'oggetto **dbo.uspSearchCandidateResumes** per le modifiche del comportamento di **SERVERPROPERTY('LCID')** e l'oggetto **HumanResourcesJobCandidate** per le modifiche della ricerca full-text. 

   > [!IMPORTANT] 
   > A seconda della progettazione del database e dell'applicazione, quando si esegue la migrazione del database di origine, potrebbe essere necessario modificare il database o l'applicazione oppure entrambi dopo la migrazione (e, in alcuni casi, prima della migrazione). Per informazioni sulle differenze di Transact-SQL che possono influire sulla migrazione, vedere [Risoluzione delle differenze di Transact-SQL durante la migrazione al database SQL](sql-database-transact-sql-information.md).

     ![nuova migrazione di dati - valutazione e selezione degli oggetti](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-assessment-results.png)

9. Fare clic su **Generate SQL script** (Genera script SQL) per creare script degli oggetti dello schema nel database di origine. 
10. Rivedere lo script generato e quindi fare clic su **Next issue** (Problema successivo) in base alle esigenze per esaminare i problemi di valutazione identificati e le indicazioni proposte. Ad esempio, per la ricerca full-text l'indicazione per l'aggiornamento è di testare le applicazioni sfruttando le funzionalità full-text. Se si vuole, è anche possibile salvare o copiare lo script.

     ![nuova migrazione di dati - script generato](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-generated-script.png)

11. Fare clic su **Deploy schema** (Distribuisci schema) e attendere il completamento del processo di migrazione dello schema.

     ![nuova migrazione di dati - migrazione dello schema](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-schema-migration.png)

12. Al termine della migrazione dello schema, esaminare i risultati per trovare eventuali errori e quindi, se non ce ne sono, fare clic su **Migrate data** (Esegui migrazione dati).
13. Nella pagina **Select tables** (Seleziona tabelle) rivedere le tabelle selezionate per la migrazione e quindi fare clic su **Start data migration** (Avvia migrazione dati).

     ![nuova migrazione di dati - migrazione dei dati](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-data-migration.png)

14. Attendere il completamento del processo di migrazione.

     ![nuova migrazione di dati - processo di migrazione dei dati](./media/sql-database-migrate-your-sql-server-database/data-migration-assistant-data-migration-process.png)

## <a name="connect-to-the-database-with-ssms"></a>Connettersi al database con SSMS

Usare [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) per stabilire una connessione al server del database SQL di Azure.

1. Aprire SQL Server Management Studio.

2. Nella finestra di dialogo **Connetti al server** immettere le informazioni seguenti:

   | Impostazione       | Valore consigliato | Descrizione | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | Tipo di server | Motore di database | Questo valore è obbligatorio |
   | Nome server | Nome completo del server | Il nome sarà simile a: **mynewserver20170824.database.windows.net**. |
   | Autenticazione | Autenticazione di SQL Server | L'autenticazione SQL è il solo tipo di autenticazione configurato in questa esercitazione. |
   | Login | Account amministratore del server | Si tratta dell'account specificato quando è stato creato il server. |
   | Password | Password per l'account amministratore del server | Si tratta della password specificata quando è stato creato il server. |

   ![connetti al server](./media/sql-database-connect-query-ssms/connect.png)

3. Fare clic su **Opzioni** nella finestra di dialogo **Connetti al server**. Nella sezione **Connetti al database** immettere **mySampleDatabase** per connettersi a tale database.

   ![connettersi al database nel server](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Fare clic su **Connetti**. La finestra Esplora oggetti viene visualizzata in SSMS. 

5. In Esplora oggetti espandere **Database** e quindi espandere **mySampleDatabase** per visualizzare gli oggetti disponibili nel database di esempio.

   ![oggetti di database](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="change-database-properties"></a>Cambiare le proprietà del database

Con SQL Server Management Studio è possibile cambiare il livello di servizio, il livello di prestazioni e il livello di compatibilità. Durante la fase di importazione, è consigliabile importare in un database con livello di prestazioni superiore per ottenere prestazioni ottimali, ma ridurlo dopo il completamento dell'importazione per risparmiare denaro fino a quando non si è pronti a usare attivamente il database importato. La modifica del livello di compatibilità può comportare prestazioni migliori e l'accesso alle funzionalità più recenti del servizio Database SQL di Azure. Quando si esegue la migrazione di un database meno recente, il livello di compatibilità del database viene mantenuto al livello più basso supportato e compatibile con il database importato. Per altre informazioni, vedere [Miglioramento delle prestazioni delle query con il livello di compatibilità 130 nel database SQL di Azure](sql-database-compatibility-level-query-performance-130.md).

1. In Esplora oggetti fare clic con il pulsante destro del mouse su **mySampleDatabase** e quindi scegliere **Nuova query**. Viene visualizzata una finestra di query connessa al database.

2. Eseguire il comando seguente per impostare il livello di servizio **Standard** e il livello di prestazioni **S1**.

    ```sql
    ALTER DATABASE mySampleDatabase 
    MODIFY 
        (
        EDITION = 'Standard'
        , MAXSIZE = 250 GB
        , SERVICE_OBJECTIVE = 'S1'
    );
    ```

## <a name="next-steps"></a>Passaggi successivi 
Questa esercitazione illustra come:

> * Creare un database SQL di Azure vuoto nel portale di Azure 
> * Creare un firewall a livello di server nel portale di Azure 
> * Usare [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) (DMA) per importare il database SQL Server nel database SQL di Azure vuoto 
> * Usare [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SSMS) per modificare le proprietà del database.

Passare all'esercitazione successiva per informazioni su come proteggere il database.

> [!div class="nextstepaction"]
> [Proteggere il database SQL di Azure](sql-database-security-tutorial.md)


