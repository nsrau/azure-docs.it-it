---
title: Transparent Data Encryption
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Panoramica di Transparent Data Encryption per il database SQL di Azure, Azure SQL Istanza gestita e Azure sinapsi Analytics. Il documento illustra i vantaggi e le opzioni per la configurazione, che include crittografia TDE gestita dal servizio e Bring Your Own Key.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=3
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 06/15/2020
ms.openlocfilehash: 8bf1a19c8756e8c51b79ec63f10822efa7816d32
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84986962"
---
# <a name="transparent-data-encryption-for-sql-database-sql-managed-instance-and-azure-synapse-analytics"></a>Transparent Data Encryption per database SQL, SQL Istanza gestita e Azure sinapsi Analytics
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Transparent Data Encryption (Transparent Data Encryption)](/sql/relational-databases/security/encryption/transparent-data-encryption) consente di proteggere il database SQL di Azure, azure SQL istanza gestita e l'analisi delle sinapsi di Azure contro la minaccia di attività offline dannose mediante la crittografia dei dati inattivi. Esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni inattivi, senza richiedere modifiche dell'applicazione. Per impostazione predefinita, Transparent Data Encryption è abilitato per tutti i nuovi database SQL distribuiti e deve essere abilitato manualmente per i database precedenti del database SQL di Azure, Azure SQL Istanza gestita. È necessario abilitare manualmente Transparent Data Encryption per Azure sinapsi Analytics.

TDE esegue in tempo reale la crittografia e decrittografia dell'I/O dei dati a livello di pagina. Ogni pagina viene decrittografata quando letta in memoria e quindi crittografata prima di essere scritta su disco. Transparent Data Encryption crittografa l'archivio di un intero database utilizzando una chiave simmetrica denominata chiave di crittografia del database. All'avvio del database, la crittografia crittografata viene decrittografata e quindi usata per la decrittografia e la ricrittografia dei file di database nel processo del motore di database SQL Server. La protezione da Transparent Data Encryption è protetta da crittografia. La protezione Transparent Data Encryption è un certificato gestito dal servizio (Transparent Data Encryption gestito dal servizio) o una chiave asimmetrica archiviata nel [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) (Transparent Data Encryption gestita dal cliente).

Per il database SQL di Azure e la sinapsi di Azure, la protezione Transparent Data Encryption è impostata a livello di [Server](logical-servers.md) e viene ereditata da tutti i database associati a tale server. Per Istanza gestita SQL di Azure (funzionalità BYOK in anteprima), la protezione Transparent Data Encryption viene impostata a livello di istanza e viene ereditata da tutti i database crittografati in tale istanza. Salvo diversa indicazione, in questo documento il termine *server* fa riferimento sia al server che all'istanza.

> [!IMPORTANT]
> Per impostazione predefinita, tutti i nuovi database creati nel database SQL vengono crittografati tramite Transparent Data Encryption gestito dal servizio. Per impostazione predefinita, i database SQL esistenti creati prima del 2017 e i database SQL creati tramite il ripristino, la replica geografica e la copia del database non sono crittografati. Per impostazione predefinita, i database di SQL Istanza gestita esistenti creati prima del 2019 febbraio non sono crittografati. I database SQL Istanza gestita creati tramite il ripristino ereditano lo stato di crittografia dall'origine.

> [!NOTE]
> Impossibile utilizzare Transparent Data Encryption per crittografare il database **Master** nel database SQL.  Il database **master** contiene gli oggetti necessari a eseguire le operazioni di Transparent Data Encryption nei database utente.

## <a name="service-managed-transparent-data-encryption"></a>Transparent Data Encryption gestita dal servizio

In Azure, l'impostazione predefinita per Transparent Data Encryption è che la chiave di crittografia è protetta da un certificato server incorporato. Il certificato server predefinito è univoco per ogni server e l'algoritmo di crittografia usato è AES 256. Se un database si trova in una relazione di replica geografica, il database primario e quello secondario sono protetti dalla chiave del server padre del database primario. Se due database sono connessi allo stesso server, condividono anche lo stesso certificato predefinito. Microsoft ruota automaticamente questi certificati in conformità con i criteri di sicurezza interni e la chiave radice è protetta da un archivio segreti interno di Microsoft. I clienti possono verificare il database SQL e la conformità SQL Istanza gestita con i criteri di sicurezza interni in report di controllo di terze parti indipendenti disponibili nel [Centro protezione Microsoft](https://servicetrust.microsoft.com/).

Microsoft inoltre sposta e gestisce le chiavi senza problemi in base alle esigenze per la replica geografica e ne esegue il ripristino.

## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Transparent Data Encryption gestita dal cliente: Bring Your Own Key

Transparent Data Encryption gestito dal cliente è noto anche come supporto Bring Your Own Key (BYOK) per Transparent Data Encryption. In questo scenario, la protezione Transparent Data Encryption che crittografa la chiave di crittografia è una chiave asimmetrica gestita dal cliente, archiviata in un Azure Key Vault gestito e di proprietà del cliente (sistema di gestione delle chiavi esterne basato sul cloud di Azure) senza mai lasciare l'insieme di credenziali delle chiavi. La protezione Transparent Data Encryption può essere generata dall'insieme di credenziali [delle chiavi o trasferita](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) nell'insieme di credenziali delle chiavi da un dispositivo del modulo di protezione hardware (HSM) locale. È necessario concedere le autorizzazioni per il database SQL, il Istanza gestita SQL e la sinapsi di Azure all'insieme di credenziali delle chiavi di proprietà del cliente per decrittografare e crittografare la chiave di crittografia. Se le autorizzazioni del server per l'insieme di credenziali delle chiavi vengono revocate, un database non sarà accessibile e tutti i dati vengono crittografati

Grazie all'integrazione di TDE con Azure Key Vault, gli utenti possono controllare le attività di gestione delle chiavi, tra cui le rotazioni, i backup e le autorizzazioni dell'insieme di credenziali, nonché abilitare il controllo o il reporting per tutte le protezioni TDE usando Azure Key Vault. Key Vault fornisce una gestione centralizzata delle chiavi, sfrutta HSM strettamente monitorati e consente la separazione dei compiti tra la gestione delle chiavi e dei dati per consentire la conformità ai criteri di sicurezza.
Per altre informazioni su BYOK per il database SQL di Azure e la sinapsi di Azure, vedere [Transparent Data Encryption with Azure Key Vault Integration](transparent-data-encryption-byok-overview.md).

Per iniziare a usare Transparent Data Encryption con l'integrazione Azure Key Vault, vedere la Guida alle procedure per [attivare la crittografia Transparent Data Encryption usando una chiave personalizzata da Key Vault](transparent-data-encryption-byok-configure.md).

## <a name="move-a-transparent-data-encryption-protected-database"></a>Spostare un database protetto con TDE

Non è necessario decrittografare i database per le operazioni all'interno di Azure. Le impostazioni di Transparent Data Encryption nel database di origine o nel database primario vengono ereditate in modo trasparente nel database di destinazione. Le operazioni incluse fanno riferimento a:

- Ripristino geografico
- Ripristino temporizzato self-service
- Ripristino di un database eliminato
- Replica geografica attiva
- Creazione di una copia del database
- Ripristino di un file di backup per Istanza gestita di database SQL di Azure

> [!IMPORTANT]
> L'esecuzione del backup manuale di sola copia di un database crittografato tramite Transparent Data Services gestita dal servizio non è supportata nel Istanza gestita SQL di Azure, perché il certificato usato per la crittografia non è accessibile. Utilizzare la funzionalità di ripristino temporizzato per spostare questo tipo di database in un altro Istanza gestita SQL oppure passare alla chiave gestita dal cliente.

Quando si esporta un database protetto con Transparent Data Encryption, il contenuto esportato del database non è crittografato. Questo contenuto esportato viene archiviato in file BACPAC non crittografati. Assicurarsi di proteggere i file BACPAC in modo appropriato e di abilitare Transparent Data Encryption al termine dell'importazione del nuovo database.

Se, ad esempio, il file BACPAC viene esportato da un'istanza di SQL Server, il contenuto importato del nuovo database non viene crittografato automaticamente. Analogamente, se il file BACPAC viene importato in un'istanza di SQL Server, anche il nuovo database non viene crittografato automaticamente.

L'unica eccezione si verifica quando si esporta un database da e verso il database SQL. Transparent Data Encryption è abilitato nel nuovo database, ma il file BACPAC stesso non è ancora crittografato.

## <a name="manage-transparent-data-encryption"></a>Gestire Transparent Data Encryption

# <a name="the-azure-portal"></a>[Il portale di Azure](#tab/azure-portal)

Gestire Transparent Data Encryption nel portale di Azure.

Per configurare Transparent Data Encryption tramite il portale di Azure, è necessario essere connessi come proprietario, collaboratore o gestore sicurezza SQL di Azure.

Abilitare e disabilitare Transparent Data Encryption a livello di database. Per Azure SQL Istanza gestita usare Transact-SQL (T-SQL) per attivare e disattivare la funzionalità Transparent Data Encryption in un database. Per il database SQL di Azure e la sinapsi di Azure, è possibile gestire Transparent Data Encryption per il database nel [portale di Azure](https://portal.azure.com) dopo aver eseguito l'accesso con l'account di amministratore o collaboratore di Azure. Le impostazioni di TDE si trovano nel database utente. Per impostazione predefinita, viene usata TDE gestita dal servizio. Viene generato automaticamente un certificato Transparent Data Encryption per il server che contiene il database.

![Transparent Data Encryption gestita dal servizio](./media/transparent-data-encryption-tde-overview/service-managed-transparent-data-encryption.png)  

Si imposta la chiave master di Data Encryption, nota come protezione Transparent Data Encryption, a livello di server o di istanza. Per usare Transparent Data Encryption con il supporto di BYOK e proteggere i database con una chiave da Key Vault, aprire le impostazioni di Transparent Data Encryption nel server.

![TDE con supporto Bring Your Own Key](./media/transparent-data-encryption-tde-overview/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Gestire Transparent Data Encryption tramite PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato, ma tutto lo sviluppo futuro riguarda il modulo AZ. SQL. Per informazioni su questi cmdlet, vedere [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nei moduli Az e AzureRm sono sostanzialmente identici.

Per configurare TDE tramite PowerShell è necessario essere connessi come proprietario, collaboratore o Gestore Sicurezza SQL di Azure.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Cmdlet per il database SQL di Azure e la sinapsi di Azure

Usare i cmdlet seguenti per il database SQL di Azure e la sinapsi di Azure:

| Cmdlet | Descrizione |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Abilita o Disabilita la crittografia Transparent Data Encryption per un database.|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Ottiene lo stato Transparent Data Encryption per un database. |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Controlla lo stato di crittografia per un database. |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Aggiunge una chiave di Key Vault a un server. |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Ottiene le chiavi di Key Vault per un server  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Imposta la protezione Transparent Data Encryption per un server. |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Ottiene la protezione TDE |
| [Remove-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Rimuove una chiave di Key Vault da un server. |
|  | |

> [!IMPORTANT]
> Per il Istanza gestita SQL di Azure, usare il comando T-SQL [ALTER database](/sql/t-sql/statements/alter-database-azure-sql-database) per attivare e disattivare la crittografia a livello di database e controllare [lo script di PowerShell di esempio](transparent-data-encryption-byok-configure.md) per gestire TRANSparent Data Encryption a livello di istanza.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)

Gestire Transparent Data Encryption tramite Transact-SQL.

Connettere il database usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master.

| Comando | Descrizione |
| --- | --- |
| [ALTER DATABASE (database SQL di Azure)](/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF consente di crittografare o decrittografare un database |
| [sys.dm_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Restituisce informazioni sullo stato di crittografia di un database e delle chiavi di crittografia associate al database |
| [sys.dm_pdw_nodes_database_encryption_keys](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Restituisce informazioni sullo stato di crittografia di ogni nodo della sinapsi di Azure e delle chiavi di crittografia del database associate |
|  | |

Non è possibile passare la protezione Transparent Data Encryption a una chiave da Key Vault usando Transact-SQL. Usare PowerShell o il portale Azure.

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)

Gestire Transparent Data Encryption usando l'API REST.

Per configurare Transparent Data Encryption tramite l'API REST, è necessario essere connessi come proprietario, collaboratore o gestore sicurezza SQL di Azure.
Usare il set di comandi seguente per il database SQL di Azure e la sinapsi di Azure:

| Comando | Descrizione |
| --- | --- |
|[Crea o aggiorna il server](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Aggiunge un'identità Azure Active Directory a un server. (utilizzato per concedere l'accesso a Key Vault)|
|[Crea o aggiorna la chiave del server](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Aggiunge una chiave di Key Vault a un server.|
|[Elimina la chiave del server](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Rimuove una chiave di Key Vault da un server. |
|[Ottieni la chiave del server](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Ottiene una chiave di Key Vault specifica da un server.|
|[Elenca le chiavi del server dal server](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Ottiene le chiavi Key Vault per un server. |
|[Crea o aggiorna la protezione della crittografia](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Imposta la protezione Transparent Data Encryption per un server.|
|[Ottieni la protezione della crittografia](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Ottiene la protezione Transparent Data Encryption per un server.|
|[Elenca le protezioni di crittografia dal server](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Ottiene le protezioni Transparent Data Encryption per un server. |
|[Crea o aggiorna la configurazione di Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Abilita o disabilita TDE per un database.|
|[Ottieni la configurazione di Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Ottiene la configurazione di TDE per un database.|
|[Elenca i risultati della configurazione di Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Ottiene il risultato della crittografia per un database.|

## <a name="see-also"></a>Vedere anche

- SQL Server eseguito in una macchina virtuale di Azure può anche usare una chiave asimmetrica di Key Vault. I passaggi della configurazione sono diversi rispetto all'uso di una chiave asimmetrica nel database SQL e in Istanza gestita di database SQL di Azure. Per altre informazioni, vedere [Extensible Key Management tramite Azure Key Vault (SQL Server)](/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- Per una descrizione generale di [Transparent Data Encryption, vedere Transparent Data Encryption](/sql/relational-databases/security/encryption/transparent-data-encryption).
- Per altre informazioni su Transparent Data Encryption con il supporto di BYOK per il database SQL di Azure, Azure SQL Istanza gestita e la sinapsi di Azure, vedere [Transparent Data Encryption con Bring your own key supporto](transparent-data-encryption-byok-overview.md).
- Per iniziare a usare Transparent Data Encryption con il supporto Bring Your Own Key, vedere la Guida alle procedure, [abilitare Transparent Data Encryption usando una chiave personalizzata da Key Vault](transparent-data-encryption-byok-configure.md).
- Per altre informazioni su Key Vault, vedere [proteggere l'accesso a un insieme di](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)credenziali delle chiavi.
