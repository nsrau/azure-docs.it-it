---
title: Transparent Data Encryption per il database SQL di Azure e Data Warehouse | Microsoft Docs
description: Una panoramica di Transparent Data Encryption per il database SQL e Data Warehouse. Il documento illustra i vantaggi e le opzioni per la configurazione, che include crittografia TDE gestita dal servizio e Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 10/15/2018
ms.openlocfilehash: 94a3a502d5756d57cfebdf6698a4435dc1e19948
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353031"
---
# <a name="transparent-data-encryption-for-sql-database-and-data-warehouse"></a>Transparent Data Encryption per il database SQL e Data Warehouse

La funzionalità Transparent Data Encryption (TDE) consente di proteggere il database SQL di Azure e Azure Data Warehouse dalla minaccia di attività dannose. Esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni inattivi, senza richiedere modifiche dell'applicazione. Per impostazione predefinita, la tecnologia TDE è abilitata per tutti i database SQL di Azure appena distribuiti. Transparent Data Encryption non può essere usata per crittografare il database **master** logico nel database SQL.  Il database **master** contiene gli oggetti necessari a eseguire le operazioni di Transparent Data Encryption nei database utente.

Transparent Data Encryption dovrà essere attivata manualmente per database precedenti o Azure SQL Data Warehouse.  

Transparent Data Encryption esegue la crittografia dell'archiviazione di un intero database usando una chiave simmetrica denominata "chiave di crittografia del database". Questa chiave di crittografia del database è protetta dalla protezione TDE. La protezione è un certificato gestito dal servizio (TDE gestita dal servizio) o una chiave asimmetrica archiviata in Azure Key Vault (Bring Your Own Key). Impostare la protezione TDE a livello di server.

All'avvio del database, la chiave di crittografia del database crittografato viene decrittografata e quindi usata per decrittografare e ricrittografare i file di database nel processo di motore di database di SQL Server. TDE esegue la crittografia e la decrittografia I/O in tempo reale dei dati a livello di pagina. Ogni pagina viene decrittografata quando letta in memoria e quindi crittografata prima di essere scritta su disco. Per una descrizione generale della funzionalità TDE, vedere [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

SQL Server eseguito in una macchina virtuale di Azure può anche usare una chiave asimmetrica di Key Vault. I passaggi della configurazione sono diversi rispetto all'uso di una chiave asimmetrica nel database SQL. Per altre informazioni, vedere [Extensible Key Management tramite Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Transparent Data Encryption gestita dal servizio

In Azure, l'impostazione predefinita per TDE prevede che la chiave di crittografia del database sia protetta da un certificato server integrato. Il certificato server integrato è univoco per ogni server. Se un database è in una relazione di replica geografica, il database primario e la replica geografica secondaria sono protetti dalla chiave primaria del server padre del database. Se due database sono connessi allo stesso server, condividono lo stesso certificato predefinito. Microsoft ruota automaticamente questi certificati almeno ogni 90 giorni.

Microsoft inoltre sposta e gestisce le chiavi senza problemi in base alle esigenze per la replica geografica e ne esegue il ripristino.

> [!IMPORTANT]
> Tutti i nuovi database SQL vengono crittografati per impostazione predefinita tramite TDE gestita del servizio. I database esistenti prima di maggio 2017 e i database creati tramite il ripristino, la replica geografica e la copia di database non sono crittografati per impostazione predefinita.

## <a name="bring-your-own-key"></a>BYOK (Bring Your Own Key)

Con il supporto Bring Your Own Key, è possibile controllare le proprie chiavi di TDE, chi può accedervi e quando. Key Vault, il sistema di Azure di gestione chiavi esterne basato sul cloud, è il primo servizio di gestione delle chiavi che TDE ha integrato con il supporto Bring Your Own Key. Con il supporto Bring Your Own Key, la chiave di crittografia del database è protetta da una chiave asimmetrica archiviata in Key Vault. La chiave asimmetrica non si discosta mai da Key Vault. Una volta che il server ha le autorizzazioni per un insieme di credenziali delle chiavi, il server gli invia tramite Key Vault le richieste di operazione di base della chiave. Se la chiave asimmetrica è impostata a livello di server tutti i database in tale server la ereditano.

Con il supporto Bring Your Own Key, è ora possibile controllare le attività di gestione delle chiavi, ad esempio le rotazioni delle chiavi e le autorizzazioni dell'insieme di credenziali delle chiavi. È anche possibile eliminare le chiavi e abilitare il controllo o il reporting di tutte le chiavi di crittografia. Key Vault offre una gestione centrale delle chiavi e usa moduli di protezione hardware accuratamente monitorati. Key Vault porta a un nuovo livello la separazione della gestione delle chiavi e dei dati per consentire il rispetto della conformità alle normative. Per altre informazioni sugli insiemi di credenziali delle chiavi, vedere [Pagina di documentazione di Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).

Per altre informazioni su TDE con supporto Bring Your Own Key per database SQL e Data Warehouse, vedere [TDE con supporto Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).

Per iniziare a usare TDE con supporto Bring Your Own Key, vedere la guida [Abilitare Transparent Data Encryption usando la propria chiave di Key Vault con PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Spostare un database protetto con TDE

Non è necessario decrittografare i database per le operazioni all'interno di Azure. Le impostazioni di TDE nel database di origine o nel database primario vengono ereditate in modo trasparente nella destinazione. Le operazioni incluse fanno riferimento a:

- Ripristino geografico
- Ripristino temporizzato self-service
- Ripristino di un database eliminato
- Replica geografica attiva
- Creazione di una copia del database

Quando si esporta un database protetto con TDE, il contenuto esportato del database non viene crittografato. Questo contenuto esportato viene archiviato in file BACPAC non crittografati. Assicurarsi di proteggere i file BACPAC nel modo appropriato e abilitare TDE al termine dell'importazione del nuovo database.

Ad esempio, se il file BACPAC viene esportato da un'istanza di SQL Server in locale, non viene crittografato automaticamente il contenuto importato del nuovo database. Allo stesso modo, se il file BACPAC viene esportato da un'istanza di SQL Server in locale, non viene crittografato automaticamente neppure il nuovo database.

L'unica eccezione riguarda l'esportazione da e verso un database SQL. TDE è abilitata nel nuovo database, ma il file BACPAC non viene comunque crittografato.

## <a name="manage-transparent-data-encryption-in-the-azure-portal"></a>Gestire TDE nel portale di Azure

Per configurare TDE usando il portale di Azure, è necessario essere connessi come il proprietario, collaboratore o gestore della sicurezza SQL di Azure.

Impostare TDE a livello di database. Per abilitare TDE in un database, andare al [portale di Azure](https://portal.azure.com) e accedere con l'account amministratore o di collaboratore di Azure. Trovare le impostazioni di TDE nel database utente. Per impostazione predefinita, viene usata TDE gestita dal servizio. Un certificato TDE viene generato automaticamente per il server che contiene il database.

![Transparent Data Encryption gestita dal servizio](./media/transparent-data-encryption-azure-sql/service-managed-tde.png)  

Impostare la chiave master di TDE, nota anche come protezione TDE, a livello di server. Per usare TDE con supporto Bring Your Own Key e proteggere i database con una chiave di Key Vault, vedere le impostazioni di TDE nel server.

![TDE con supporto Bring Your Own Key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

## <a name="manage-transparent-data-encryption-by-using-powershell"></a>Gestire TDE usando PowerShell

Per configurare TDE usando PowerShell, è necessario essere connessi come il proprietario, collaboratore o gestore della sicurezza SQL di Azure.

| Cmdlet | DESCRIZIONE |
| --- | --- |
| [Set-AzureRmSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) |Abilita o disabilita TDE per un database|
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) |Ottiene lo stato di TDE per un database |
| [Get-Azure-Rm-Sql-Database-Transparent-Data-Encryption-Activity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) |Controlla l'avanzamento della crittografia per un database |
| [Add-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) |Aggiunge una chiave di Key Vault a un'istanza di SQL Server |
| [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) |Ottiene una chiave di Key Vault per un'istanza di SQL Server  |
| [Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) |Imposta la protezione TDE per un istanza di SQL Server |
| [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) |Ottiene la protezione TDE |
| [Remove-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) |Rimuove una chiave di Key Vault da un'istanza di SQL Server |
|  | |

## <a name="manage-transparent-data-encryption-by-using-transact-sql"></a>Gestire TDE usando Transact-SQL

Connettere il database usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master.

| Comando | DESCRIZIONE |
| --- | --- |
| [ALTER DATABASE (database SQL di Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF consente di crittografare o decrittografare un database |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Restituisce informazioni sullo stato di crittografia di un database e delle chiavi di crittografia associate al database |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Restituisce informazioni sullo stato di crittografia di un nodo di data warehouse e delle chiavi di crittografia associate al database |
|  | |

È possibile trasmettere la protezione TDE a una chiave di Key Vault usando Transact-SQL. Usare PowerShell o il portale Azure.

## <a name="manage-transparent-data-encryption-by-using-the-rest-api"></a>Gestire TDE usando API REST

Per configurare TDE usando API REST, è necessario essere connessi come il proprietario, collaboratore o gestore della sicurezza SQL di Azure.

| Comando | DESCRIZIONE |
| --- | --- |
|[Crea o aggiorna il server](https://docs.microsoft.com/rest/api/sql/servers/servers_createorupdate)|Aggiunge un'identità di Azure Active Directory a un'istanza di SQL Server (usata per concedere l'accesso a Key Vault)|
|[Crea o aggiorna la chiave del server](https://docs.microsoft.com/rest/api/sql/serverkeys/serverkeys_createorupdate)|Aggiunge una chiave di Key Vault a un'istanza di SQL Server|
|[Elimina la chiave del server](https://docs.microsoft.com/rest/api/sql/serverkeys/serverkeys_delete)|Rimuove una chiave di Key Vault da un'istanza di SQL Server|
|[Ottieni la chiave del server](https://docs.microsoft.com/rest/api/sql/serverkeys/serverkeys_get)|Ottiene una chiave di Key Vault da un'istanza di SQL Server|
|[Elenca le chiavi del server dal server](https://docs.microsoft.com/rest/api/sql/serverkeys/serverkeys_listbyserver)|Ottiene una chiave di Key Vault per un'istanza di SQL Server |
|[Crea o aggiorna la protezione della crittografia](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/encryptionprotectors_createorupdate)|Imposta la protezione TDE per un istanza di SQL Server|
|[Ottieni la protezione della crittografia](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/encryptionprotectors_get)|Ottiene la protezione TDE per un istanza di SQL Server|
|[Elenca le protezioni di crittografia dal server](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/encryptionprotectors_listbyserver)|Ottiene le protezioni TDE per un istanza di SQL Server |
|[Crea o aggiorna la configurazione di Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/transparentdataencryptions_createorupdate)|Abilita o disabilita TDE per un database|
|[Ottieni la configurazione di Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/transparentdataencryptions_get)|Ottiene la configurazione di TDE per un database|
|[Elenca i risultati della configurazione di Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/transparentdataencryptionactivities_listbyconfiguration)|Ottiene il risultato della crittografia per un database|

## <a name="next-steps"></a>Passaggi successivi

- Per una descrizione generale della funzionalità TDE, vedere [Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Per altre informazioni su TDE con supporto Bring Your Own Key per database SQL e Data Warehouse, vedere [TDE con supporto Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).
- Per iniziare a usare TDE con supporto Bring Your Own Key, vedere la guida [Abilitare Transparent Data Encryption usando la propria chiave di Key Vault con PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
- Per altre informazioni sugli insiemi di credenziali delle chiavi, vedere [Pagina di documentazione di Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
