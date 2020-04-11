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
ms.date: 04/10/2020
ms.openlocfilehash: 87abdb37ff7773b0205a2ce3ee21689a10c459d7
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113541"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>Crittografia dei dati trasparente per il database SQL e Azure SynapseTransparent data encryption for SQL Database and Azure Synapse

La crittografia trasparente dei [dati (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) consente di proteggere il database SQL di Azure, l'istanza gestita SQL di Azure e SQL Synapse in Azure Synapse Analytics contro la minaccia di attività offline dannosa crittografando i dati inattivi. Esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni inattivi, senza richiedere modifiche dell'applicazione. Per impostazione predefinita, TDE è abilitato per tutti i database SQL di Azure appena distribuiti e deve essere abilitato manualmente per i database meno recenti del database SQL di Azure, dell'istanza gestita SQL di Azure o di Azure Synapse.By default, TDE is enabled for all newly deployed Azure SQL databases and needs to be manually enabled for older SQL Database, Azure SQL Managed Instance, or Azure Synapse.

TDE esegue in tempo reale la crittografia e decrittografia dell'I/O dei dati a livello di pagina. Ogni pagina viene decrittografata quando letta in memoria e quindi crittografata prima di essere scritta su disco. TDE crittografa l'archiviazione di un intero database utilizzando una chiave simmetrica denominata chiave DEK (Database Encryption Key). All'avvio del database, la chiave DEK crittografata viene decrittografata e quindi utilizzata per la decrittografia e la ricrittografia dei file di database nel processo del Motore di database di SQL ServerSQL Server Database Engine. DEK è protetto dal protettore TDE. La protezione TDE è un certificato gestito dal servizio (crittografia dei dati trasparente gestita dal servizio) o una chiave asimmetrica archiviata in [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) (crittografia dei dati trasparente gestita dal cliente). 

Per il database SQL di Azure e Azure Synapse, la protezione TDE viene impostata a livello di server SQL logico ed è ereditata da tutti i database associati a tale server. Per l'istanza gestita SQL di Azure (funzionalità BYOK in anteprima), la protezione TDE viene impostata a livello di istanza e viene ereditata da tutti i database crittografati in tale istanza. Salvo diversa indicazione, in questo documento il termine *server* fa riferimento sia al server che all'istanza.

> [!IMPORTANT]
> Tutti i database SQL di Azure appena creati vengono crittografati per impostazione predefinita usando la crittografia dei dati trasparente gestita dal servizio. I database SQL esistenti creati prima di maggio 2017 e i database SQL creati tramite ripristino, replica geografica e copia del database non vengono crittografati per impostazione predefinita. I database dell'istanza gestita esistenti creati prima di febbraio 2019 non vengono crittografati per impostazione predefinita. I database dell'istanza gestita creati tramite il ripristino ereditano lo stato di crittografia dall'origine.

> [!NOTE]
> Transparent Data Encryption non può essere usata per crittografare il database **master** logico nel database SQL.  Il database **master** contiene gli oggetti necessari a eseguire le operazioni di Transparent Data Encryption nei database utente.


## <a name="service-managed-transparent-data-encryption"></a>Transparent Data Encryption gestita dal servizio

In Azure, l'impostazione predefinita per TDE è che la proprietà DEK è protetta da un certificato server predefinito. Il certificato server incorporato è univoco per ogni server e l'algoritmo di crittografia utilizzato è AES 256. Se un database è in una relazione di replica geografica, i database primario e quello secondario geografico sono protetti dalla chiave del server padre del database primario. Se due database sono connessi allo stesso server, condividono anche lo stesso certificato predefinito.  Microsoft ruota automaticamente questi certificati in conformità con i criteri di sicurezza interni e la chiave radice è protetta da un archivio segreti interno di Microsoft.  I clienti possono verificare la conformità del database SQL ai criteri di sicurezza interni nei report di controllo di terze parti indipendenti disponibili nel [Centro protezione Microsoft](https://servicetrust.microsoft.com/).

Microsoft inoltre sposta e gestisce le chiavi senza problemi in base alle esigenze per la replica geografica e ne esegue il ripristino.


## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Transparent Data Encryption gestita dal cliente: Bring Your Own Key

TDE gestito dal cliente è anche indicato come supporto Bring Your Own Key (BYOK) per TDE. In questo scenario, il Protettore TDE che crittografa la chiave DEK è una chiave asimmetrica gestita dal cliente, archiviata in un insieme di credenziali delle chiavi di Azure gestito e di proprietà del cliente (sistema di gestione delle chiavi esterne basato su cloud di Azure) e non lascia mai l'insieme di credenziali delle chiavi. Il protettore TDE può essere generato dall'insieme di credenziali delle [chiavi o trasferito all'insieme](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) di credenziali delle chiavi da un dispositivo HSM (Hardware Security Module) locale. Per decrittografare e crittografare la chiave DEK, il database SQL deve possedere le autorizzazioni di accesso all'insieme di credenziali delle chiavi del cliente. Se le autorizzazioni del server SQL logico per l'insieme di credenziali delle chiavi vengono revocate, un database sarà inaccessibile e tutti i dati vengono crittografati

Grazie all'integrazione di TDE con Azure Key Vault, gli utenti possono controllare le attività di gestione delle chiavi, tra cui le rotazioni, i backup e le autorizzazioni dell'insieme di credenziali, nonché abilitare il controllo o il reporting per tutte le protezioni TDE usando Azure Key Vault. Key Vault fornisce una gestione centralizzata delle chiavi, sfrutta gli HSM strettamente monitorati e consente la separazione dei compiti tra la gestione delle chiavi e dei dati per garantire la conformità ai criteri di sicurezza.
Per altre informazioni su BYOK per il database SQL di Azure e Azure Synapse, vedere [Transparent data encryption with Azure Key Vault integration](transparent-data-encryption-byok-azure-sql.md)..

Per iniziare a usare TDE con l'integrazione di Azure Key Vault, vedere la guida alle procedure [Attivare](transparent-data-encryption-byok-azure-sql-configure.md)la crittografia dei dati trasparente usando la propria chiave da Key Vault .

## <a name="move-a-transparent-data-encryption-protected-database"></a>Spostare un database protetto con TDE

Non è necessario decrittografare i database per le operazioni all'interno di Azure. Le impostazioni di Transparent Data Encryption nel database di origine o nel database primario vengono ereditate in modo trasparente nel database di destinazione. Le operazioni incluse fanno riferimento a:

- Ripristino geografico
- Ripristino temporizzato self-service
- Ripristino di un database eliminato
- Replica geografica attiva
- Creazione di una copia del database
- Ripristino di un file di backup per Istanza gestita di database SQL di Azure

> [!IMPORTANT]
> L'esecuzione di un backup manuale di sola copia di un database crittografato con tecnologia TDE gestita dal servizio non è consentita in Istanza gestita di database SQL di Azure perché il certificato usato per la crittografia non è accessibile. Usare la funzionalità di ripristino temporizzato per spostare i database di questo tipo in un'altra istanza gestita.

Quando si esporta un database protetto da TDE, il contenuto esportato del database non viene crittografato. Questo contenuto esportato viene archiviato in file BACPAC non crittografati. Assicurarsi di proteggere i file BACPAC in modo appropriato e abilitare TDE al termine dell'importazione del nuovo database.

Ad esempio, se il file BACPAC viene esportato da un'istanza di SQL Server in locale, non viene crittografato automaticamente il contenuto importato del nuovo database. Allo stesso modo, se il file BACPAC viene esportato da un'istanza di SQL Server in locale, non viene crittografato automaticamente neppure il nuovo database.

L'unica eccezione riguarda l'esportazione da e verso un database SQL. TDE è abilitato nel nuovo database, ma il file BACPAC non è ancora crittografato.


## <a name="manage-transparent-data-encryption"></a>Gestire la crittografia dei dati trasparente
# <a name="portal"></a>[Portale](#tab/azure-portal)
Gestire TDE nel portale di Azure.Manage TDE in the Azure portal.

Per configurare TDE tramite il portale di Azure, è necessario essere connessi come proprietario di Azure, collaboratore o gestione sicurezza SQL.

Attivare e disattivare TDE a livello di database. Per abilitare TDE in un database, passare al portale di Azure e accedere con l'account di amministratore o collaboratore di Azure.To enable TDE on a database, go to the [Azure portal](https://portal.azure.com) and sign in with your Azure Administrator or Contributor account. Le impostazioni di TDE si trovano nel database utente. Per impostazione predefinita, viene usata TDE gestita dal servizio. Viene generato automaticamente un certificato TDE per il server che contiene il database. Per l'istanza gestita SQL di Azure usare T-SQL per attivare e disattivare TDE in un database.

![Transparent Data Encryption gestita dal servizio](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)  

Impostare la chiave master TDE, noto come la protezione TDE, a livello di server. Per utilizzare TDE con il supporto BYOK e proteggere i database con una chiave dall'insieme di credenziali delle chiavi, aprire le impostazioni TDE nel server.

![TDE con supporto Bring Your Own Key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Gestire TDE tramite PowerShell.Manage TDE by using PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo di PowerShell Azure Resource Manager è ancora supportato dal database SQL di Azure, ma tutto lo sviluppo futuro è per il modulo Az.Sql.The PowerShell Azure Resource Manager module is still supported by Azure SQL Database, but all future development is for the Az.Sql module. Per questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo Az e nei moduli di AzureRm sono sostanzialmente identici.

Per configurare TDE tramite PowerShell è necessario essere connessi come proprietario, collaboratore o Gestore Sicurezza SQL di Azure.

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
> Per l'istanza gestita SQL di Azure, usare il comando T-SQL [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) per attivare e disattivare TDE a livello di database e controllare lo script di [PowerShell di esempio](transparent-data-encryption-byok-azure-sql-configure.md) per gestire TDE a livello di istanza.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Gestire TDE tramite Transact-SQLTransact-SQL.

Connettere il database usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master.

| Comando | Descrizione |
| --- | --- |
| [ALTER DATABASE (database SQL di Azure)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF consente di crittografare o decrittografare un database |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Restituisce informazioni sullo stato di crittografia di un database e delle chiavi di crittografia associate al database |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Restituisce informazioni sullo stato di crittografia di ogni nodo Synapse di Azure e sulle chiavi di crittografia del database associate |
|  | |

Non è possibile passare la protezione TDE a una chiave dall'insieme di credenziali delle chiavi utilizzando Transact-SQLTransact-SQL.You can't switch the TDE protector to a key from Key Vault by using Transact-SQLTransact-SQL. Usare PowerShell o il portale Azure.

# <a name="rest-api"></a>[API REST](#tab/azure-RESTAPI)
Gestire TDE utilizzando l'API REST.

Per configurare TDE tramite l'API REST, è necessario essere connessi come proprietario di Azure, collaboratore o sql Security Manager.To configure TDE through the REST API, you must be connected as the Azure Owner, Contributor, or SQL Security Manager.
Usare il set di comandi seguente per Il database SQL di Azure e Azure Synapse:Use the following set of commands for Azure SQL Database and Azure Synapse:

| Comando | Descrizione |
| --- | --- |
|[Crea o aggiorna il server](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Aggiunge un'identità di Azure Active Directory a un'istanza di SQL Server (usata per concedere l'accesso a Key Vault)|
|[Crea o aggiorna la chiave del server](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Aggiunge una chiave di Key Vault a un'istanza di SQL Server|
|[Elimina la chiave del server](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Rimuove una chiave di Key Vault da un'istanza di SQL Server|
|[Ottieni la chiave del server](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Ottiene una chiave di Key Vault da un'istanza di SQL Server|
|[Elenca le chiavi del server dal server](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Ottiene una chiave di Key Vault per un'istanza di SQL Server |
|[Crea o aggiorna la protezione della crittografia](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Imposta la protezione TDE per un'istanza di SQL ServerSets the TDE protector for a SQL Server instance|
|[Ottieni la protezione della crittografia](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Ottiene la protezione TDE per un'istanza di SQL Server|
|[Elenca le protezioni di crittografia dal server](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Ottiene le protezioni TDE per un'istanza di SQL Server |
|[Crea o aggiorna la configurazione di Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Abilita o disabilita TDE per un database|
|[Ottieni la configurazione di Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Ottiene la configurazione TDE per un database|
|[Elenca i risultati della configurazione di Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Ottiene il risultato della crittografia per un database|

## <a name="see-also"></a>Vedere anche
- SQL Server eseguito in una macchina virtuale di Azure può anche usare una chiave asimmetrica di Key Vault. I passaggi della configurazione sono diversi rispetto all'uso di una chiave asimmetrica nel database SQL e in Istanza gestita di database SQL di Azure. Per altre informazioni, vedere [Extensible Key Management tramite Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- Per una descrizione generale di TDE, vedere [Transparent data encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Per altre informazioni sul supporto TDE con BYOK per il database SQL di Azure, l'istanza gestita SQL di Azure e Lo synapse di Azure, vedere [Crittografia dei dati trasparente con supporto Bring Your Own Key](transparent-data-encryption-byok-azure-sql.md).
- Per iniziare a utilizzare TDE con il supporto Bring Your Own Key, vedere la guida alle procedure [attiva Attiva crittografia dei dati trasparente utilizzando la propria chiave da Key Vault](transparent-data-encryption-byok-azure-sql-configure.md).
- Per ulteriori informazioni sull'insieme di credenziali delle chiavi, vedere [Proteggere l'accesso a un insieme di credenziali delle chiavi](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault).
