---
title: Transparent Data Encryption
description: Panoramica di Transparent Data Encryption per il database SQL e la sinapsi SQL in Azure sinapsi Analytics. Il documento illustra i vantaggi e le opzioni per la configurazione, che include crittografia TDE gestita dal servizio e Bring Your Own Key.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113541"
---
# <a name="transparent-data-encryption-for-sql-database-and-azure-synapse"></a>Transparent Data Encryption per il database SQL e la sinapsi di Azure

[Transparent Data Encryption (Transparent Data Encryption)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) consente di proteggere il database SQL di Azure, azure SQL istanza gestita e sinapsi SQL in Azure sinapsi Analytics contro la minaccia di attività offline dannose crittografando i dati inattivi. Esegue in tempo reale la crittografia e la decrittografia del database, dei backup associati e dei file di log delle transazioni inattivi, senza richiedere modifiche dell'applicazione. Per impostazione predefinita, la funzionalità Transparent Data Encryption è abilitata per tutti i database SQL di Azure appena distribuiti e deve essere abilitata manualmente per i database precedenti del database SQL di Azure, Azure SQL Istanza gestita o la sinapsi di Azure.

TDE esegue in tempo reale la crittografia e decrittografia dell'I/O dei dati a livello di pagina. Ogni pagina viene decrittografata quando letta in memoria e quindi crittografata prima di essere scritta su disco. Transparent Data Encryption crittografa l'archivio di un intero database utilizzando una chiave simmetrica denominata chiave di crittografia del database. All'avvio del database, la chiave di crittografia crittografata viene decrittografata e quindi usata per la decrittografia e la ricrittografia dei file di database nel processo di motore di database SQL Server. La protezione da Transparent Data Encryption è protetta da crittografia. La protezione Transparent Data Encryption è un certificato gestito dal servizio (Transparent Data Encryption gestito dal servizio) o una chiave asimmetrica archiviata nel [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault) (Transparent Data Encryption gestita dal cliente). 

Per il database SQL di Azure e la sinapsi di Azure, la protezione Transparent Data Encryption è impostata a livello di server SQL logico e viene ereditata da tutti i database associati a tale server. Per Istanza gestita SQL di Azure (funzionalità BYOK in anteprima), la protezione Transparent Data Encryption viene impostata a livello di istanza e viene ereditata da tutti i database crittografati in tale istanza. Salvo diversa indicazione, in questo documento il termine *server* fa riferimento sia al server che all'istanza.

> [!IMPORTANT]
> Tutti i database SQL di Azure appena creati vengono crittografati per impostazione predefinita usando Transparent Data Encryption gestito dal servizio. Per impostazione predefinita, i database SQL esistenti creati prima del 2017 e i database SQL creati tramite il ripristino, la replica geografica e la copia del database non sono crittografati. I database di Istanza gestita esistenti creati prima del 2019 febbraio non vengono crittografati per impostazione predefinita. Istanza gestita database creati tramite il ripristino ereditano lo stato di crittografia dall'origine.

> [!NOTE]
> Transparent Data Encryption non può essere usata per crittografare il database **master** logico nel database SQL.  Il database **master** contiene gli oggetti necessari a eseguire le operazioni di Transparent Data Encryption nei database utente.


## <a name="service-managed-transparent-data-encryption"></a>Transparent Data Encryption gestita dal servizio

In Azure, l'impostazione predefinita per Transparent Data Encryption è che la chiave di crittografia è protetta da un certificato server incorporato. Il certificato server predefinito è univoco per ogni server e l'algoritmo di crittografia usato è AES 256. Se un database si trova in una relazione di replica geografica, il database primario e quello secondario sono protetti dalla chiave del server padre del database primario. Se due database sono connessi allo stesso server, condividono anche lo stesso certificato predefinito.  Microsoft ruota automaticamente questi certificati in conformità con i criteri di sicurezza interni e la chiave radice è protetta da un archivio segreti interno di Microsoft.  I clienti possono verificare la conformità del database SQL ai criteri di sicurezza interni nei report di controllo di terze parti indipendenti disponibili nel [Centro protezione Microsoft](https://servicetrust.microsoft.com/).

Microsoft inoltre sposta e gestisce le chiavi senza problemi in base alle esigenze per la replica geografica e ne esegue il ripristino.


## <a name="customer-managed-transparent-data-encryption---bring-your-own-key"></a>Transparent Data Encryption gestita dal cliente: Bring Your Own Key

Transparent Data Encryption gestito dal cliente è noto anche come supporto Bring Your Own Key (BYOK) per Transparent Data Encryption. In questo scenario, la protezione Transparent Data Encryption che crittografa la chiave di crittografia è una chiave asimmetrica gestita dal cliente, archiviata in un Azure Key Vault gestito e di proprietà del cliente (sistema di gestione delle chiavi esterne basato sul cloud di Azure) senza mai lasciare l'insieme di credenziali delle chiavi. La protezione Transparent Data Encryption può essere generata dall'insieme di credenziali [delle chiavi o trasferita](https://docs.microsoft.com/azure/key-vault/key-vault-hsm-protected-keys) nell'insieme di credenziali delle chiavi da un dispositivo del modulo di protezione hardware (HSM) locale. Per decrittografare e crittografare la chiave DEK, il database SQL deve possedere le autorizzazioni di accesso all'insieme di credenziali delle chiavi del cliente. Se le autorizzazioni del server SQL logico all'insieme di credenziali delle chiavi vengono revocate, un database non sarà accessibile e tutti i dati vengono crittografati

Grazie all'integrazione di TDE con Azure Key Vault, gli utenti possono controllare le attività di gestione delle chiavi, tra cui le rotazioni, i backup e le autorizzazioni dell'insieme di credenziali, nonché abilitare il controllo o il reporting per tutte le protezioni TDE usando Azure Key Vault. Key Vault fornisce una gestione centralizzata delle chiavi, sfrutta HSM strettamente monitorati e consente la separazione dei compiti tra la gestione delle chiavi e dei dati per consentire la conformità ai criteri di sicurezza.
Per altre informazioni su BYOK per il database SQL di Azure e la sinapsi di Azure, vedere [Transparent Data Encryption with Azure Key Vault Integration](transparent-data-encryption-byok-azure-sql.md).

Per iniziare a usare Transparent Data Encryption con l'integrazione Azure Key Vault, vedere la Guida alle procedure per [attivare la crittografia Transparent Data Encryption usando una chiave personalizzata da Key Vault](transparent-data-encryption-byok-azure-sql-configure.md).

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

Quando si esporta un database protetto con Transparent Data Encryption, il contenuto esportato del database non è crittografato. Questo contenuto esportato viene archiviato in file BACPAC non crittografati. Assicurarsi di proteggere i file BACPAC in modo appropriato e di abilitare Transparent Data Encryption al termine dell'importazione del nuovo database.

Ad esempio, se il file BACPAC viene esportato da un'istanza di SQL Server in locale, non viene crittografato automaticamente il contenuto importato del nuovo database. Allo stesso modo, se il file BACPAC viene esportato da un'istanza di SQL Server in locale, non viene crittografato automaticamente neppure il nuovo database.

L'unica eccezione riguarda l'esportazione da e verso un database SQL. Transparent Data Encryption è abilitato nel nuovo database, ma il file BACPAC stesso non è ancora crittografato.


## <a name="manage-transparent-data-encryption"></a>Gestire Transparent Data Encryption
# <a name="portal"></a>[Portale](#tab/azure-portal)
Gestire Transparent Data Encryption nel portale di Azure.

Per configurare Transparent Data Encryption tramite il portale di Azure, è necessario essere connessi come proprietario, collaboratore o gestore sicurezza SQL di Azure.

È possibile attivare e disattivare la crittografia a livello di database. Per abilitare Transparent Data Encryption in un database, passare alla [portale di Azure](https://portal.azure.com) e accedere con l'account di amministratore o collaboratore di Azure. Le impostazioni di TDE si trovano nel database utente. Per impostazione predefinita, viene usata TDE gestita dal servizio. Viene generato automaticamente un certificato Transparent Data Encryption per il server che contiene il database. Per Azure SQL Istanza gestita usare T-SQL per attivare e disattivare Transparent Data Encryption in un database.

![Transparent Data Encryption gestita dal servizio](./media/transparent-data-encryption-azure-sql/service-managed-transparent-data-encryption.png)  

Si imposta la chiave master di Data Encryption, nota come protezione Transparent Data Encryption, a livello di server. Per usare Transparent Data Encryption con il supporto di BYOK e proteggere i database con una chiave da Key Vault, aprire le impostazioni di Transparent Data Encryption nel server.

![TDE con supporto Bring Your Own Key](./media/transparent-data-encryption-azure-sql/tde-byok-support.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Gestire Transparent Data Encryption tramite PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Il modulo Azure Resource Manager di PowerShell è ancora supportato dal database SQL di Azure, ma tutte le attività di sviluppo future sono per il modulo AZ. SQL. Per questi cmdlet, vedere [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Gli argomenti per i comandi nel modulo AZ e nei moduli AzureRm sono sostanzialmente identici.

Per configurare TDE tramite PowerShell è necessario essere connessi come proprietario, collaboratore o Gestore Sicurezza SQL di Azure.

### <a name="cmdlets-for-azure-sql-database-and-azure-synapse"></a>Cmdlet per il database SQL di Azure e la sinapsi di Azure

Usare i cmdlet seguenti per il database SQL di Azure e la sinapsi di Azure:

| Cmdlet | Descrizione |
| --- | --- |
| [Set-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) |Abilita o disabilita TDE per un database|
| [Get-AzSqlDatabaseTransparentDataEncryption](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) |Ottiene lo stato di TDE per un database |
| [Get-AzSqlDatabaseTransparentDataEncryptionActivity](https://docs.microsoft.com/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) |Controlla l'avanzamento della crittografia per un database |
| [Add-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/add-azsqlserverkeyvaultkey) |Aggiunge una chiave di Key Vault a un'istanza di SQL Server |
| [Get-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlserverkeyvaultkey) |Fornisce le chiavi dell’insieme di credenziali delle chiavi per un server di database SQL di Azure  |
| [Set-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) |Imposta la protezione TDE per un istanza di SQL Server |
| [Get-AzSqlServerTransparentDataEncryptionProtector](https://docs.microsoft.com/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) |Ottiene la protezione TDE |
| [Remove-AzSqlServerKeyVaultKey](https://docs.microsoft.com/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) |Rimuove una chiave di Key Vault da un'istanza di SQL Server |
|  | |

> [!IMPORTANT]
> Per il Istanza gestita SQL di Azure, usare il comando T-SQL [ALTER database](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) per attivare e disattivare la crittografia a livello di database e controllare [lo script di PowerShell di esempio](transparent-data-encryption-byok-azure-sql-configure.md) per gestire TRANSparent Data Encryption a livello di istanza.

# <a name="transact-sql"></a>[Transact-SQL](#tab/azure-TransactSQL)
Gestire Transparent Data Encryption tramite Transact-SQL.

Connettere il database usando un account di accesso di un amministratore o di un membro del ruolo **dbmanager** nel database master.

| Comando | Descrizione |
| --- | --- |
| [ALTER DATABASE (database SQL di Azure)](https://docs.microsoft.com/sql/t-sql/statements/alter-database-azure-sql-database) | SET ENCRYPTION ON/OFF consente di crittografare o decrittografare un database |
| [sys.dm_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-database-encryption-keys-transact-sql) |Restituisce informazioni sullo stato di crittografia di un database e delle chiavi di crittografia associate al database |
| [sys.dm_pdw_nodes_database_encryption_keys](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-nodes-database-encryption-keys-transact-sql) |Restituisce informazioni sullo stato di crittografia di ogni nodo della sinapsi di Azure e delle chiavi di crittografia del database associate |
|  | |

Non è possibile passare la protezione Transparent Data Encryption a una chiave da Key Vault usando Transact-SQL. Usare PowerShell o il portale Azure.

# <a name="rest-api"></a>[REST API](#tab/azure-RESTAPI)
Gestire Transparent Data Encryption usando l'API REST.

Per configurare Transparent Data Encryption tramite l'API REST, è necessario essere connessi come proprietario, collaboratore o gestore sicurezza SQL di Azure.
Usare il set di comandi seguente per il database SQL di Azure e la sinapsi di Azure:

| Comando | Descrizione |
| --- | --- |
|[Crea o aggiorna il server](https://docs.microsoft.com/rest/api/sql/servers/createorupdate)|Aggiunge un'identità di Azure Active Directory a un'istanza di SQL Server (usata per concedere l'accesso a Key Vault)|
|[Crea o aggiorna la chiave del server](https://docs.microsoft.com/rest/api/sql/serverkeys/createorupdate)|Aggiunge una chiave di Key Vault a un'istanza di SQL Server|
|[Elimina la chiave del server](https://docs.microsoft.com/rest/api/sql/serverkeys/delete)|Rimuove una chiave di Key Vault da un'istanza di SQL Server|
|[Ottieni la chiave del server](https://docs.microsoft.com/rest/api/sql/serverkeys/get)|Ottiene una chiave di Key Vault da un'istanza di SQL Server|
|[Elenca le chiavi del server dal server](https://docs.microsoft.com/rest/api/sql/serverkeys/listbyserver)|Ottiene una chiave di Key Vault per un'istanza di SQL Server |
|[Crea o aggiorna la protezione della crittografia](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/createorupdate)|Imposta la protezione Transparent Data Encryption per un'istanza di SQL Server|
|[Ottieni la protezione della crittografia](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/get)|Ottiene la protezione Transparent Data Encryption per un'istanza di SQL Server|
|[Elenca le protezioni di crittografia dal server](https://docs.microsoft.com/rest/api/sql/encryptionprotectors/listbyserver)|Ottiene le protezioni Transparent Data Encryption per un'istanza di SQL Server |
|[Crea o aggiorna la configurazione di Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/createorupdate)|Abilita o Disabilita Transparent Data Encryption per un database|
|[Ottieni la configurazione di Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptions/get)|Ottiene la configurazione di Transparent Data Encryption per un database|
|[Elenca i risultati della configurazione di Transparent Data Encryption](https://docs.microsoft.com/rest/api/sql/transparentdataencryptionactivities/listbyconfiguration)|Ottiene il risultato della crittografia per un database|

## <a name="see-also"></a>Vedere anche
- SQL Server eseguito in una macchina virtuale di Azure può anche usare una chiave asimmetrica di Key Vault. I passaggi della configurazione sono diversi rispetto all'uso di una chiave asimmetrica nel database SQL e in Istanza gestita di database SQL di Azure. Per altre informazioni, vedere [Extensible Key Management tramite Azure Key Vault (SQL Server)](https://docs.microsoft.com/sql/relational-databases/security/encryption/extensible-key-management-using-azure-key-vault-sql-server).
- Per una descrizione generale di [Transparent Data Encryption, vedere Transparent Data Encryption](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption).
- Per altre informazioni su Transparent Data Encryption con il supporto di BYOK per il database SQL di Azure, Azure SQL Istanza gestita e la sinapsi di Azure, vedere [Transparent Data Encryption con Bring your own key supporto](transparent-data-encryption-byok-azure-sql.md).
- Per iniziare a usare Transparent Data Encryption con il supporto Bring Your Own Key, vedere la Guida alle procedure, [abilitare Transparent Data Encryption usando una chiave personalizzata da Key Vault](transparent-data-encryption-byok-azure-sql-configure.md).
- Per altre informazioni su Key Vault, vedere [proteggere l'accesso a un insieme di](https://docs.microsoft.com/azure/key-vault/key-vault-secure-your-key-vault)credenziali delle chiavi.
