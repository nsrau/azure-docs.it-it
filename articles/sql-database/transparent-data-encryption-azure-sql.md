---
title: Transparent Data Encryption
description: Panoramica della crittografia dei dati trasparente per il database SQL e l'sql Synapse in Azure Synapse Analytics.An overview of transparent data encryption for SQL Database and Synapse SQL in Azure Synapse Analytics. Il documento illustra i vantaggi e le opzioni per la configurazione, che include crittografia TDE gestita dal servizio e Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/06/2020
ms.openlocfilehash: 982a59f1eb8717e2fe2d86728cdae731c919aaf0
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80476960"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>Crittografia dei dati trasparente per il database SQL e Azure SynapseTransparent data encryption for SQL Database and Azure Synapse

La crittografia trasparente dei dati (TDE) consente di proteggere il database SQL di Azure, l'istanza gestita SQL di Azure e SQL Synapse in Azure Synapse Analytics contro la minaccia di attività offline dannosa crittografando i dati inattivi. Esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni inattivi, senza richiedere modifiche dell'applicazione. Per impostazione predefinita, la tecnologia TDE è abilitata per tutti i database SQL di Azure appena distribuiti. Transparent Data Encryption non può essere usata per crittografare il database **master** logico nel database SQL.  Il database **master** contiene gli oggetti necessari a eseguire le operazioni di Transparent Data Encryption nei database utente.

TDE needs to be manually enabled for older databases of Azure SQL Database, Azure SQL Managed Instance, or Azure Synapse.
I database dell'istanza gestita creati tramite il ripristino ereditano lo stato di crittografia dal database di origine.

Transparent Data Encryption esegue la crittografia dell'archiviazione di un intero database usando una chiave simmetrica denominata "chiave di crittografia del database". Questa chiave di crittografia del database è protetta dalla protezione TDE. La protezione è un certificato gestito dal servizio (TDE gestita dal servizio) o una chiave asimmetrica archiviata in Azure Key Vault (Bring Your Own Key). Impostare la protezione trasparente della crittografia dei dati a livello di server per il database SQL di Azure e Azure Synapse e a livello di istanza per l'istanza gestita SQL di Azure.You set the transparent data encryption protector at the server level for Azure SQL Database and Azure Synapse, and instance level for Azure SQL Managed Instance. Salvo diversa indicazione, in questo documento il termine *server* fa riferimento sia al server che all'istanza.

All'avvio del database, la chiave di crittografia del database crittografato viene decrittografata e quindi usata per decrittografare e ricrittografare i file di database nel processo di motore di database di SQL Server. TDE esegue la crittografia e la decrittografia I/O in tempo reale dei dati a livello di pagina. Ogni pagina viene decrittografata quando letta in memoria e quindi crittografata prima di essere scritta su disco. Per una descrizione generale della crittografia trasparente dei dati, vedere [Crittografia trasparente dei dati](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).

SQL Server eseguito in una macchina virtuale di Azure può anche usare una chiave asimmetrica di Key Vault. I passaggi della configurazione sono diversi rispetto all'uso di una chiave asimmetrica nel database SQL e in Istanza gestita di database SQL di Azure. Per altre informazioni, vedere [Extensible Key Management tramite Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).

## <a name="service-managed-transparent-data-encryption"></a>Transparent Data Encryption gestita dal servizio

In Azure, l'impostazione predefinita per TDE prevede che la chiave di crittografia del database sia protetta da un certificato server integrato. Il certificato server incorporato è univoco per ogni server e l'algoritmo di crittografia utilizzato è AES 256. Se un database è in una relazione di replica geografica, il database primario e la replica geografica secondaria sono protetti dalla chiave primaria del server padre del database. Se due database sono connessi allo stesso server, condividono anche lo stesso certificato predefinito.  Microsoft ruota automaticamente questi certificati in conformità con i criteri di sicurezza interni e la chiave radice è protetta da un archivio segreti interno di Microsoft.  I clienti possono verificare la conformità del database SQL ai criteri di sicurezza interni nei report di controllo di terze parti indipendenti disponibili nel [Centro protezione Microsoft](https://servicetrust.microsoft.com/).

Microsoft inoltre sposta e gestisce le chiavi senza problemi in base alle esigenze per la replica geografica e ne esegue il ripristino.

> [!IMPORTANT]
> Tutti i database SQL appena creati e i database dell'istanza gestita vengono crittografati per impostazione predefinita utilizzando la crittografia dei dati trasparente gestita dal servizio. I database SQL esistenti creati prima di maggio 2017 e i database SQL creati tramite ripristino, replica geografica e copia del database non vengono crittografati per impostazione predefinita. I database dell'istanza gestita esistenti creati prima di febbraio 2019 non vengono crittografati per impostazione predefinita. I database dell'istanza gestita creati tramite il ripristino ereditano lo stato di crittografia dall'origine.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Transparent Data Encryption gestita dal cliente: Bring Your Own Key

La [crittografia TDE con chiavi gestite dal cliente in Azure Key Vault](transparent-data-encryption-byok-azure-sql.md) consente di crittografare la chiave DEK (Database Encryption Key) con una chiave asimmetrica gestita dal cliente chiamata protezione TDE.  Questo è anche comunemente noto come supporto Bring Your Own Key (BYOK) per Transparent Data Encryption. Nello scenario BYOK, Protezione TDE viene archiviato in un insieme di credenziali delle chiavi di Azure gestito e di proprietà del cliente, il sistema di gestione delle chiavi esterne basato su cloud di Azure.In the BYOK scenario, the TDE Protector is stored in a customer-owned and managed [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault), the Azure's cloud-based external key management system. Il protettore TDE può essere [generato dall'insieme di credenziali delle chiavi o trasferito all'insieme](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys) di credenziali delle chiavi da un dispositivo HSM locale. La chiave DEK della crittografia TDE, archiviata nella pagina di avvio di un database, viene crittografata e decrittografata dalla protezione TDE, che rimane archiviata in modo permanente nell'insieme di credenziali delle chiavi in Azure Key Vault.  Per decrittografare e crittografare la chiave DEK, il database SQL deve possedere le autorizzazioni di accesso all'insieme di credenziali delle chiavi del cliente. Se le autorizzazioni del server SQL per l'insieme di credenziali delle chiavi vengono revocate, un database non è accessibile e tutti i dati rimangono crittografati. Per il database SQL di Azure, la protezione TDE è impostata a livello di server logico SQL e viene ereditata da tutti i database associati al server. Per l'[istanza gestita di database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-howto-managed-instance), la protezione TDE è impostata a livello di istanza e viene ereditata da tutti i database *crittografati* nell'istanza. Salvo diversa indicazione, in questo documento il termine *server* fa riferimento sia al server che all'istanza.

Grazie all'integrazione di TDE con Azure Key Vault, gli utenti possono controllare le attività di gestione delle chiavi, tra cui le rotazioni, i backup e le autorizzazioni dell'insieme di credenziali, nonché abilitare il controllo o il reporting per tutte le protezioni TDE usando Azure Key Vault. Questa funzionalità offre la gestione centralizzata delle chiavi, sfrutta il vantaggio dei moduli di protezione hardware (HSM) accuratamente monitorati e consente di separare i compiti di gestione delle chiavi e dei dati per contribuire a rispettare la conformità ai criteri di sicurezza.
Per altre informazioni sulla crittografia dei dati trasparente con l'integrazione di Azure Key Vault (supporto Bring Your Own Key) per il database SQL di Azure, l'istanza gestita SQL e lo synapse di Azure, vedere Crittografia dei dati trasparente [con l'integrazione](transparent-data-encryption-byok-azure-sql.md)di Azure Key Vault.To learn more about transparent data encryption with Azure Key Vault integration (Bring Your Own Key support) for Azure SQL Database, SQL Managed Instance, and Azure Synapse, see Transparent data encryption with Azure Key Vault integration .

Per iniziare a usare la crittografia TDE con integrazione di Azure Key Vault (supporto Bring Your Own Key), vedere la procedura dettagliata [Abilitare Transparent Data Encryption usando la propria chiave di Key Vault con PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Spostare un database protetto con TDE

Non è necessario decrittografare i database per le operazioni all'interno di Azure. Le impostazioni di TDE nel database di origine o nel database primario vengono ereditate in modo trasparente nella destinazione. Le operazioni incluse fanno riferimento a:

- Ripristino geografico
- Ripristino temporizzato self-service
- Ripristino di un database eliminato
- Replica geografica attiva
- Creazione di una copia del database
- Ripristino di un file di backup per Istanza gestita di database SQL di Azure

> [!IMPORTANT]
> L'esecuzione di un backup manuale di sola copia di un database crittografato con tecnologia TDE gestita dal servizio non è consentita in Istanza gestita di database SQL di Azure perché il certificato usato per la crittografia non è accessibile. Usare la funzionalità di ripristino temporizzato per spostare i database di questo tipo in un'altra istanza gestita.

Quando si esporta un database protetto con TDE, il contenuto esportato del database non viene crittografato. Questo contenuto esportato viene archiviato in file BACPAC non crittografati. Assicurarsi di proteggere i file BACPAC nel modo appropriato e abilitare TDE al termine dell'importazione del nuovo database.

Ad esempio, se il file BACPAC viene esportato da un'istanza di SQL Server in locale, non viene crittografato automaticamente il contenuto importato del nuovo database. Allo stesso modo, se il file BACPAC viene esportato da un'istanza di SQL Server in locale, non viene crittografato automaticamente neppure il nuovo database.

L'unica eccezione riguarda l'esportazione da e verso un database SQL. TDE è abilitata nel nuovo database, ma il file BACPAC non viene comunque crittografato.


## <a name="manage-transparent-data-encryption"></a>Gestire la crittografia dei dati trasparente
# <a name="portal"></a>[Portale](#tab/azure-portal)
Gestire la crittografia dei dati trasparente nel portale di Azure.Manage transparent data encryption in the Azure portal.

Per configurare TDE usando il portale di Azure, è necessario essere connessi come il proprietario, collaboratore o gestore della sicurezza SQL di Azure.

Attivare e disattivare TDE a livello di database. Per abilitare TDE in un database, andare al [portale di Azure](https://portal.azure.com) e accedere con l'account amministratore o di collaboratore di Azure. Trovare le impostazioni di TDE nel database utente. Per impostazione predefinita, viene usata TDE gestita dal servizio. Un certificato TDE viene generato automaticamente per il server che contiene il database. Per Istanza gestita di database SQL di Azure, usare T-SQL per attivare e disattivare TDE in un database.

![Transparent Data Encryption gestita dal servizio](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)

Impostare la chiave master di TDE, nota anche come protezione TDE, a livello di server. Per usare TDE con supporto Bring Your Own Key e proteggere i database con una chiave dall’insieme di credenziali delle chiavi, aprire le impostazioni di TDE nel server.

![TDE con supporto Bring Your Own Key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Gestire la crittografia dei dati trasparente tramite PowerShell.Manage transparent data encryption by using PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo di PowerShell Azure Resource Manager è ancora supportato dal database SQL di Azure, ma tutto lo sviluppo futuro è per il modulo Az.Sql.The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. Per questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli di AzureRm sono sostanzialmente identici.

Per configurare TDE usando PowerShell, è necessario essere connessi come il proprietario, collaboratore o gestore della sicurezza SQL di Azure.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Cmdlet per il database SQL di Azure e Azure Synapse

Usare i cmdlet seguenti per Il database SQL di Azure e Azure Synapse:Use the following cmdlets for Azure SQL Database and Azure Synapse:

| Cmdlet | Descrizione |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Abilita o disabilita TDE per un database|
| [Get-AzSqlDatabaseTransparentDataEncryptionGet-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Ottiene lo stato di TDE per un database |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Controlla l'avanzamento della crittografia per un database |
| [Aggiunta-AzSqlServerKeyVault](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Aggiunge una chiave di Key Vault a un'istanza di SQL Server |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Fornisce le chiavi dell’insieme di credenziali delle chiavi per un server di database SQL di Azure  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Imposta la protezione TDE per un istanza di SQL Server |
| [Get-AzSqlServerTransparentDataEncryptionProtectorGet-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Ottiene la protezione TDE |
| [Rimuovi-AzSqlServerKeyVault](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Rimuove una chiave di Key Vault da un'istanza di SQL Server |
|  | |

> [!IMPORTANT]
> Per Istanza gestita di database SQL di Azure, utilizzare il comando T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) per attivare e disattivare TDE a livello di database e controllare lo [script PowerShell di esempio](transparent-data-encryption-byok-azure-sql-configure.md) per gestire TDE a livello di istanza.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Gestire la crittografia dei dati trasparente tramite Transact-SQLTransact-SQL.Manage transparent data encryption by using Transact-SQLTransact-SQL.

Connettere il database usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master.

| Comando | Descrizione |
| --- | --- |
| [ALTER DATABASE (database SQL di Azure)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF consente di crittografare o decrittografare un database |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Restituisce informazioni sullo stato di crittografia di un database e delle chiavi di crittografia associate al database |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Restituisce informazioni sullo stato di crittografia di un nodo di data warehouse e delle chiavi di crittografia associate al database |
|  | |

È possibile trasmettere la protezione TDE a una chiave di Key Vault usando Transact-SQL. Usare PowerShell o il portale Azure.

# <a name="rest-api"></a>[API REST](#tab/azure-RESTAPI)
Gestire la crittografia dei dati trasparente tramite l'API REST.

Per configurare TDE usando API REST, è necessario essere connessi come il proprietario, collaboratore o gestore della sicurezza SQL di Azure.
Usare il set di comandi seguente per Il database SQL di Azure e Azure Synapse:Use the following set of commands for Azure SQL Database and Azure Synapse:

| Comando | Descrizione |
| --- | --- |
|[Crea o aggiorna il server](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Aggiunge un'identità di Azure Active Directory a un'istanza di SQL Server (usata per concedere l'accesso a Key Vault)|
|[Crea o aggiorna la chiave del server](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Aggiunge una chiave di Key Vault a un'istanza di SQL Server|
|[Elimina la chiave del server](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Rimuove una chiave di Key Vault da un'istanza di SQL Server|
|[Ottieni la chiave del server](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Ottiene una chiave di Key Vault da un'istanza di SQL Server|
|[Elenca le chiavi del server dal server](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Ottiene una chiave di Key Vault per un'istanza di SQL Server |
|[Crea o aggiorna la protezione della crittografia](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Imposta la protezione TDE per un istanza di SQL Server|
|[Ottieni la protezione della crittografia](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Ottiene la protezione TDE per un istanza di SQL Server|
|[Elenca le protezioni di crittografia dal server](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Ottiene le protezioni TDE per un istanza di SQL Server |
|[Crea o aggiorna la configurazione di Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Abilita o disabilita TDE per un database|
|[Ottieni la configurazione di Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Ottiene la configurazione di TDE per un database|
|[Elenca i risultati della configurazione di Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Ottiene il risultato della crittografia per un database|

## <a name="next-steps"></a>Passaggi successivi

- Per una descrizione generale della crittografia trasparente dei dati, vedere [Crittografia trasparente dei dati](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Per altre informazioni sulla crittografia dei dati trasparente con il supporto Bring Your Own Key per il database SQL di Azure, l'istanza gestita di SQL Azure e Azure Synapse, vedere [Transparent data encryption with Bring Your Own Key support](transparent-data-encryption-byok-azure-sql.md).
- Per iniziare a usare TDE con supporto Bring Your Own Key, vedere la guida [Abilitare Transparent Data Encryption usando la propria chiave di Key Vault con PowerShell](transparent-data-encryption-byok-azure-sql-configure.md).
- Per altre informazioni sugli insiemi di credenziali delle chiavi, vedere [Pagina di documentazione di Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
