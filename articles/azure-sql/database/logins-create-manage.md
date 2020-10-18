---
title: Autorizzare l'accesso al server e al database tramite account di accesso e account utente
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Informazioni sul modo in cui il database SQL di Azure, SQL Istanza gestita e la sinapsi di Azure autenticano gli utenti per l'accesso tramite account utente e account di accesso. Viene inoltre illustrato come concedere ai ruoli del database e le autorizzazioni esplicite per autorizzare gli account di accesso e gli utenti a eseguire azioni ed eseguire query sui dati.
keywords: sicurezza del database sql, gestione della sicurezza del database, sicurezza degli account di accesso, sicurezza del database, accesso al database
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 03/23/2020
ms.openlocfilehash: ca458bebf75f8e77774236166704794b817b7c3f
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167135"
---
# <a name="authorize-database-access-to-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Autorizzare l'accesso al database SQL, SQL Istanza gestita e Azure sinapsi Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

In questo articolo vengono fornite informazioni su:

- Opzioni per la configurazione di database SQL di Azure, Azure SQL Istanza gestita e Azure sinapsi Analytics (in precedenza SQL Data Warehouse) per consentire agli utenti di eseguire attività amministrative e di accedere ai dati archiviati in questi database.
- Configurazione dell'accesso e dell'autorizzazione dopo la creazione iniziale di un nuovo server.
- Come aggiungere account di accesso e account utente nel database master e negli account utente, quindi concedere a tali account le autorizzazioni amministrative.
- Come aggiungere gli account utente nei database utente, associati ad account di accesso o come account utente indipendenti.
- Configurare gli account utente con le autorizzazioni nei database utente usando i ruoli del database e le autorizzazioni esplicite.

> [!IMPORTANT]
> I database nel database SQL di Azure, Azure SQL Istanza gestita e la sinapsi di Azure vengono definiti collettivamente nel resto di questo articolo come database e il server fa riferimento al [Server](logical-servers.md) che gestisce i database per il database SQL di Azure e la sinapsi di Azure.

## <a name="authentication-and-authorization"></a>Autenticazione e autorizzazione

L' [**autenticazione**](security-overview.md#authentication) è il processo che consente di dimostrare che l'utente è quello che afferma. Un utente si connette a un database utilizzando un account utente.
Quando un utente tenta di connettersi a un database, fornisce un account utente e le informazioni di autenticazione. L'utente viene autenticato mediante uno dei due metodi di autenticazione seguenti:

- [Autenticazione SQL](https://docs.microsoft.com/sql/relational-databases/security/choose-an-authentication-mode#connecting-through-sql-server-authentication).

  Con questo metodo di autenticazione, l'utente invia un nome di account utente e la password associata per stabilire una connessione. Questa password viene archiviata nel database master per gli account utente collegati a un account di accesso o archiviati nel database contenente gli account utente *non* collegati a un account di accesso.
- [Autenticazione di Azure Active Directory](authentication-aad-overview.md)

  Con questo metodo di autenticazione, l'utente invia un nome di account utente e richiede che il servizio utilizzi le informazioni sulle credenziali archiviate nel Azure Active Directory (Azure AD).

Account di **accesso e utenti**: un account utente in un database può essere associato a un account di accesso archiviato nel database master oppure può essere un nome utente archiviato in un singolo database.

- Un account di **accesso** è un account singolo nel database master, al quale è possibile collegare un account utente in uno o più database. Con un account di accesso, le informazioni sulle credenziali per l'account utente vengono archiviate con l'account di accesso.
- Un **account utente** è un account singolo in qualsiasi database che può essere, ma non deve essere collegato a un account di accesso. Con un account utente non collegato a un account di accesso, le informazioni sulle credenziali vengono archiviate con l'account utente.

L' [**autorizzazione**](security-overview.md#authorization) per accedere ai dati ed eseguire varie azioni viene gestita tramite ruoli del database e autorizzazioni esplicite. L'autorizzazione si riferisce alle autorizzazioni assegnate a un utente e determina le operazioni che l'utente è autorizzato a eseguire. L'autorizzazione viene controllata dalle [appartenenze ai ruoli](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles) del database e dalle [autorizzazioni a livello di oggetto](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine)dell'account utente. È consigliabile concedere agli utenti i privilegi minimi necessari.

## <a name="existing-logins-and-user-accounts-after-creating-a-new-database"></a>Account di accesso e account utente esistenti dopo la creazione di un nuovo database

Quando si distribuisce per la prima volta SQL di Azure, è necessario specificare un account di accesso amministratore e una password associata per tale account di accesso. Questo account amministrativo è denominato **amministratore del server**. La seguente configurazione di account di accesso e utenti nei database master e utente si verifica durante la distribuzione:

- Viene creato un account di accesso SQL con privilegi amministrativi usando il nome dell'account di accesso specificato. Un account di [accesso](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine#sa-login) è un singolo account utente per l'accesso al database SQL, a SQL istanza gestita e a sinapsi di Azure.
- A questo account di accesso vengono concesse autorizzazioni amministrative complete per tutti i database come [entità di livello server](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine). L'account di accesso dispone di tutte le autorizzazioni disponibili e non può essere limitato. In un Istanza gestita SQL, questo account di accesso viene aggiunto al ruolo predefinito del [server sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) (questo ruolo non esiste nel database SQL di Azure).
- Viene creato un [account utente](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/getting-started-with-database-engine-permissions#database-users) denominato `dbo` per questo account di accesso in ogni database utente. L'utente [dbo](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/principals-database-engine) dispone di tutte le autorizzazioni di database nel database e viene mappato al `db_owner` ruolo predefinito del database. Ulteriori ruoli predefiniti del database sono descritti più avanti in questo articolo.

Per identificare gli account amministratore per un database, aprire il portale di Azure e passare alla scheda **Proprietà** del server o dell'istanza gestita.

![Amministratori del server SQL](./media/logins-create-manage/sql-admins.png)

![Schermata che evidenzia l'opzione del menu proprietà.](./media/logins-create-manage/sql-admins2.png)

> [!IMPORTANT]
> Il nome dell'account di accesso dell'amministratore non può essere modificato dopo che è stato creato. Per reimpostare la password per l'amministratore del servizio, aprire il [portale di Azure](https://portal.azure.com), fare clic su **SQL Server**, selezionare il server dall'elenco e quindi fare clic su **Reimposta password**. Per reimpostare la password per il Istanza gestita SQL, passare al portale di Azure, fare clic sull'istanza e quindi fare clic su **Reimposta password**. È anche possibile usare PowerShell o l'interfaccia della riga di comando di Azure.

## <a name="create-additional-logins-and-users-having-administrative-permissions"></a>Creare altri account di accesso e utenti con autorizzazioni amministrative

A questo punto, il server o l'istanza gestita viene configurata per l'accesso solo usando un unico account di accesso SQL e un account utente. Per creare account di accesso aggiuntivi con autorizzazioni amministrative complete o parziali, sono disponibili le opzioni seguenti (a seconda della modalità di distribuzione):

- **Creare un account amministratore Azure Active Directory con autorizzazioni amministrative complete**

  Abilitare l'autenticazione Azure Active Directory e creare un account di accesso Azure AD amministratore. Un account di Azure Active Directory può essere configurato come amministratore della distribuzione SQL di Azure con autorizzazioni amministrative complete. Questo account può essere un account utente singolo o di gruppo di sicurezza. È **necessario** configurare un amministratore Azure ad se si vuole usare account Azure ad per connettersi al database SQL, a SQL istanza gestita o a sinapsi di Azure. Per informazioni dettagliate sull'abilitazione dell'autenticazione Azure AD per tutti i tipi di distribuzione SQL di Azure, vedere gli articoli seguenti:

  - [Usare l'autenticazione Azure Active Directory per l'autenticazione con SQL](authentication-aad-overview.md)
  - [Configurare e gestire l'autenticazione di Azure Active Directory con SQL](authentication-aad-configure.md)

- **In SQL Istanza gestita creare account di accesso SQL con autorizzazioni amministrative complete**

  - Creare un account di accesso SQL aggiuntivo nel database master.
  - Aggiungere l'account di accesso al ruolo predefinito del [server sysadmin](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/server-level-roles) utilizzando l'istruzione [ALTER Server Role](https://docs.microsoft.com/sql/t-sql/statements/alter-server-role-transact-sql) . Questo account di accesso avrà autorizzazioni amministrative complete.
  - In alternativa, creare un [account di accesso Azure ad](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance) usando la sintassi [Create Login](/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) .

- **Nel database SQL creare account di accesso SQL con autorizzazioni amministrative limitate**

  - Creare un account di accesso SQL aggiuntivo nel database master.
  - Creare un account utente nel database master associato a questo nuovo account di accesso.
  - Aggiungere l'account utente a `dbmanager` , il `loginmanager` ruolo o entrambi nel `master` database usando l'istruzione [ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) (per la sinapsi di Azure, usare l'istruzione [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) ).

  > [!NOTE]
  > `dbmanager` i `loginmanager` ruoli e **non** riguardano le distribuzioni di SQL istanza gestita.

  I membri di questi [speciali ruoli del database master](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles#special-roles-for--and-) per il database SQL di Azure hanno l'autorità per creare e gestire i database o per creare e gestire gli account di accesso. Nei database creati da un utente membro del `dbmanager` ruolo, il membro viene mappato al `db_owner` ruolo predefinito del database e può accedere a tale database e gestirlo utilizzando l' `dbo` account utente. Questi ruoli non dispongono di autorizzazioni esplicite al di fuori del database master.

  > [!IMPORTANT]
  > Non è possibile creare un account di accesso SQL aggiuntivo con autorizzazioni amministrative complete nel database SQL.

## <a name="create-accounts-for-non-administrator-users"></a>Creare account per utenti non amministratori

È possibile creare account per utenti non amministrativi usando uno dei due metodi seguenti:

- **Crea un accesso**

  Creare un account di accesso SQL nel database master. Creare quindi un account utente in ogni database a cui l'utente deve accedere e associare l'account utente a tale account. Questo approccio è preferibile quando l'utente deve accedere a più database e si desidera che le password vengano sincronizzate. Questo approccio, tuttavia, presenta complessità quando viene usato con la replica geografica perché è necessario creare l'account di accesso sia nel server primario che nel server secondario. Per altre informazioni, vedere [configurare e gestire la sicurezza del database SQL di Azure per il ripristino geografico o il failover](active-geo-replication-security-configure.md).
- **Creare un account utente**

  Creare un account utente nel database a cui un utente deve accedere (detto anche [utente indipendente](/sql/relational-databases/security/contained-database-users-making-your-database-portable)).

  - Con il database SQL, è sempre possibile creare questo tipo di account utente.
  - Con SQL Istanza gestita che supporta [Azure ad entità server](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities), è possibile creare account utente per eseguire l'autenticazione al istanza gestita SQL senza richiedere la creazione di utenti del database come utente di database indipendente.

  Con questo approccio, le informazioni di autenticazione dell'utente vengono archiviate in ogni database e replicate automaticamente nei database con replica geografica. Tuttavia, se lo stesso account è presente in più database e si usa l'autenticazione SQL di Azure, è necessario tenere sincronizzate manualmente le password. Inoltre, se un utente dispone di un account in database diversi con password diverse, la memorizzazione di tali password può costituire un problema.

> [!IMPORTANT]
> Per creare utenti indipendenti con mapping a Azure AD identità, è necessario eseguire l'accesso con un account di Azure AD che sia un amministratore del database nel database SQL di Azure. In SQL Istanza gestita un account di accesso SQL con `sysadmin` autorizzazioni può anche creare un account di accesso o un utente Azure ad.

Per esempi che illustrano come creare account di accesso e utenti, vedere:

- [Creare un account di accesso per il database SQL di Azure](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-current#examples-1)
- [Creare un account di accesso per Azure SQL Istanza gestita](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current#examples-2)
- [Creare l'account di accesso per la sinapsi di Azure](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azure-sqldw-latest#examples-3)
- [Creare un utente](https://docs.microsoft.com/sql/t-sql/statements/create-user-transact-sql#examples)
- [Creazione di Azure AD utenti indipendenti](authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)

> [!TIP]
> Per un'esercitazione sulla sicurezza che include la creazione di utenti nel database SQL di Azure, vedere [esercitazione: proteggere il database SQL di Azure](secure-database-tutorial.md).

## <a name="using-fixed-and-custom-database-roles"></a>Utilizzo di ruoli di database fissi e personalizzati

Dopo aver creato un account utente in un database, in base a un account di accesso o come utente indipendente, è possibile autorizzare l'utente a eseguire varie azioni e ad accedere ai dati in un database specifico. Per autorizzare l'accesso, è possibile usare i metodi seguenti:

- **Ruoli predefiniti del database**

  Aggiungere l'account utente a un [ruolo predefinito del database](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/database-level-roles). Sono disponibili 9 ruoli predefiniti del database, ognuno con un set di autorizzazioni definito. I ruoli predefiniti del database più comuni sono: **db_owner**, **db_ddladmin**, **db_datawriter**, **db_datareader**, **db_denydatawriter**e **db_denydatareader**. Per concedere autorizzazioni complete a un numero limitato di utenti viene usato comunemente **db_owner**. Gli altri ruoli predefiniti del database sono utili per ottenere rapidamente un database semplice nello sviluppo, ma non sono consigliabili per la maggior parte dei database di produzione. Ad esempio, il ruolo predefinito del database **db_datareader** concede l'accesso in lettura a ogni tabella del database, che è più che strettamente necessario.

  - Per aggiungere un utente a un ruolo predefinito del database:

    - Nel database SQL di Azure usare l'istruzione [ALTER ROLE](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql) . Per esempi, vedere [ALTER ROLE examples](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql#examples) .
    - Sinapsi di Azure, usare l'istruzione [sp_addrolemember](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql) . Per esempi, vedere [sp_addrolemember esempi](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql).

- **Ruolo del database personalizzato**

  Creare un ruolo del database personalizzato utilizzando l'istruzione [create Role](https://docs.microsoft.com/sql/t-sql/statements/create-role-transact-sql) . Un ruolo personalizzato consente di creare ruoli del database definiti dall'utente e concedere con attenzione a ogni ruolo le autorizzazioni minime necessarie per le esigenze aziendali. È quindi possibile aggiungere utenti al ruolo personalizzato. Quando un utente è membro di più ruoli, vengono aggregate le autorizzazioni di tutti.
- **Concedi direttamente le autorizzazioni**

  Concedere direttamente le [autorizzazioni](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) per l'account utente. Nel database SQL possono essere concesse o negate singolarmente oltre 100 autorizzazioni. Molte di queste autorizzazioni sono annidate. L'autorizzazione `UPDATE` per uno schema, ad esempio, include l'autorizzazione `UPDATE` per ogni tabella all'interno di tale schema. Come nella maggior parte dei sistemi di autorizzazioni, la negazione di un'autorizzazione determina l'override di una concessione. A causa dell'annidamento e del numero delle autorizzazioni, progettare un sistema di autorizzazioni appropriato per proteggere correttamente il database può richiedere un attento studio. Per iniziare, vedere l'elenco di autorizzazioni in [Autorizzazioni (Motore di database)](https://docs.microsoft.com/sql/relational-databases/security/permissions-database-engine) e la [grafica in formato di poster](https://docs.microsoft.com/sql/relational-databases/security/media/database-engine-permissions.png) relativa alle autorizzazioni.

## <a name="using-groups"></a>Uso di gruppi

Una gestione efficiente degli accessi usa le autorizzazioni assegnate a Active Directory i gruppi di sicurezza e i ruoli predefiniti o personalizzati anziché ai singoli utenti.

- Quando si usa l'autenticazione Azure Active Directory, inserire gli utenti Azure Active Directory in un gruppo di sicurezza di Azure Active Directory. Creare un utente di database indipendente per il gruppo. Aggiungere uno o più utenti del database come membri di ruoli del database personalizzati o predefiniti con le autorizzazioni specifiche appropriate per il gruppo di utenti.

- Quando si usa l'autenticazione SQL, creare utenti di database indipendente nel database. Inserire uno o più utenti di database in un ruolo del database personalizzato con autorizzazioni specifiche appropriate per il gruppo di utenti.

  > [!NOTE]
  > È anche possibile usare i gruppi per gli utenti di database non indipendenti.

È consigliabile acquisire familiarità con le funzionalità seguenti, utili per limitare o elevare le autorizzazioni:

- È possibile usare la [rappresentazione](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/customizing-permissions-with-impersonation-in-sql-server) e la [firma del modulo](https://docs.microsoft.com/dotnet/framework/data/adonet/sql/signing-stored-procedures-in-sql-server) per elevare temporaneamente le autorizzazioni in modo sicuro.
- [Sicurezza a livello di riga](https://docs.microsoft.com/sql/relational-databases/security/row-level-security) può essere utilizzato come limite alle cui righe un utente può accedere.
- [Mascheramento dei dati](dynamic-data-masking-overview.md) per limitare l'esposizione dei dati sensibili
- È possibile usare [stored procedure](https://docs.microsoft.com/sql/relational-databases/stored-procedures/stored-procedures-database-engine) per limitare le operazioni che possono essere eseguite nel database.

## <a name="next-steps"></a>Passaggi successivi

Per una panoramica di tutte le funzionalità di sicurezza del database SQL di Azure e di SQL Istanza gestita, vedere [Panoramica della sicurezza](security-overview.md).
