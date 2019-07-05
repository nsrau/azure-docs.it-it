---
title: Copiare dati da o verso il database SQL di Azure usando Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da archivi dati di origine supportati al database SQL di Azure o dal database SQL ad archivi dati sink supportati usando Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: jingwang
ms.openlocfilehash: 5dbd739070b1f66fe5ff04f319a3818269d0bdaa
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449598"
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Copiare dati da o nel database SQL di Azure tramite Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare la versione di Azure Data Factory in cui in uso:"]
> * [Versione 1](v1/data-factory-azure-sql-connector.md)
> * [Versione corrente](connector-azure-sql-database.md)

Questo articolo illustra come copiare dati da e verso Database SQL di Azure. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Database SQL di Azure è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [supportata matrice di origine/sink](copy-activity-overview.md) tabella
- [Mapping di flusso di dati](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

In particolare, il connettore del database SQL di Azure supporta queste funzioni:

- Copia dei dati usando l'autenticazione SQL e autenticazione token dell'applicazione di Azure Active Directory (Azure AD) con un'identità del servizio dell'entità o gestito per le risorse di Azure.
- Come origine, il recupero dei dati usando una query SQL o una stored procedure.
- Come sink, Accodamento dei dati in una tabella di destinazione o la chiamata di una stored procedure con logica personalizzata durante la copia.

>[!NOTE]
>Database SQL di Azure [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) non è attualmente supportata da questo connettore. Soluzione alternativa, è possibile usare una [connettore ODBC generico](connector-odbc.md) e un driver ODBC di SQL Server tramite un runtime di integrazione self-hosted. Seguire [questo materiale sussidiario](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) con configurazioni stringa download e connessione ODBC driver.

> [!IMPORTANT]
> Se si copiano dati tramite il runtime di integrazione di Azure Data Factory, configurare un [firewall del Server SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) in modo che servizi di Azure possono accedere al server.
> Se si copiano i dati usando un runtime di integrazione self-hosted, configurare il firewall del Server SQL di Azure per consentire l'intervallo di IP appropriato. Questo intervallo include l'indirizzo IP del computer utilizzato per connettersi al Database SQL di Azure.

## <a name="get-started"></a>Attività iniziali

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Azure Data Factory specifiche per un connettore Database SQL di Azure.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per un servizio collegato al database SQL di Azure sono supportate queste proprietà:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **AzureSqlDatabase**. | Sì |
| connectionString | Specifica le informazioni necessarie per connettersi all'istanza del database SQL di Azure per la proprietà **connectionString**. <br/>Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Azure Data Factory. È anche possibile inserire una chiave dell'entità servizio o la password in Azure Key Vault. Se si tratta di autenticazione di SQL Server, eseguire il pull di `password` configurazione all'esterno della stringa di connessione. Per altre informazioni, vedere l'esempio JSON seguente nella tabella e [Store le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì, quando si usa l'autenticazione di Azure AD con un'entità servizio |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Azure Data Factory oppure [fanno riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì, quando si usa l'autenticazione di Azure AD con un'entità servizio |
| tenant | Specificare le informazioni sul tenant, ad esempio il nome di dominio o ID, in cui risiede l'applicazione del tenant. Recuperarlo passando il mouse nell'angolo superiore destro del portale di Azure. | Sì, quando si usa l'autenticazione di Azure AD con un'entità servizio |
| connectVia | Questo [runtime di integrazione](concepts-integration-runtime.md) viene usato per connettersi all'archivio dati. È possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted se l'archivio dati si trova in una rete privata. Se non specificato, viene utilizzato il runtime di integrazione di Azure predefinito. | No |

Per altri tipi di autenticazione, fare riferimento alle sezioni seguenti relative, rispettivamente, ai prerequisiti e agli esempi JSON:

- [Autenticazione SQL](#sql-authentication)
- [Autenticazione del token dell'applicazione Azure AD: Entità servizio](#service-principal-authentication)
- [Autenticazione del token dell'applicazione Azure AD: Identità gestite per le risorse di Azure](#managed-identity)

>[!TIP]
>Se si riscontra un errore con codice di errore "UserErrorFailedToConnectToSqlServer" e un messaggio, ad esempio "il limite di sessioni per il database è XXX ed è stato raggiunto", aggiungere `Pooling=false` alla stringa di connessione e riprovare.

### <a name="sql-authentication"></a>Autenticazione in SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Esempio di servizio collegato tramite l'autenticazione SQL

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Password in Azure Key Vault** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Autenticazione di un'entità servizio

Per usare l'autenticazione token dell'applicazione di Azure AD basata sull'entità servizio, seguire questa procedura:

1. [Creare un'applicazione di Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) dal portale di Azure. Prendere nota del nome dell'applicazione e dei valori seguenti che definiscono il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. [Effettuare il provisioning di un amministratore di Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) per il Server SQL di Azure nel portale di Azure se non è già fatto. L'amministratore di Azure AD deve essere un utente o un gruppo di Azure AD, ma non può essere un'entità servizio. Questo passaggio viene eseguito in modo che, nel passaggio successivo, sia possibile usare un'identità di Azure AD per creare un utente di database indipendente per l'entità servizio.

3. [Creare utenti del database indipendente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) per l'entità servizio. Connettersi al database da o a cui si desidera copiare i dati usando strumenti come SQL Server Management Studio, con un'identità di Azure AD che abbia almeno l'autorizzazione ALTER ANY USER. Eseguire il comando in T-SQL seguente: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Concedere che all'entità servizio le autorizzazioni necessarie, come si farebbe normalmente per gli utenti SQL o ad altri utenti. Eseguire il codice seguente. Per altre opzioni, vedere [questo documento](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

5. Configurare un servizio collegato Database SQL di Azure in Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Esempio di servizio collegato tramite l'autenticazione basata su entità servizio

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identity"></a>Autenticazione di identità gestite per le risorse di Azure

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare questa identità gestita per l'autenticazione del Database SQL di Azure. La factory designata può accedere ai dati e copiarli da o verso il database tramite questa identità.

Per usare l'autenticazione identità gestita, seguire questa procedura.

1. [Effettuare il provisioning di un amministratore di Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) per il Server SQL di Azure nel portale di Azure se non è già fatto. L'amministratore di Azure AD può essere un utente di Azure AD o un gruppo di Azure AD. Se si concede al gruppo con identità gestita un ruolo di amministratore, ignorare i passaggi 3 e 4. L'amministratore ha accesso completo al database.

2. [Creare utenti del database indipendente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) per Azure Data Factory identità gestita. Connettersi al database da o a cui si desidera copiare i dati usando strumenti come SQL Server Management Studio, con un'identità di Azure AD che abbia almeno l'autorizzazione ALTER ANY USER. Eseguire il comando in T-SQL seguente: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Concedere che alla Data Factory gestiti identità necessarie autorizzazioni durante la consueta per gli utenti SQL e altri. Eseguire il codice seguente. Per altre opzioni, vedere [questo documento](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    EXEC sp_addrolemember [role name], [your Data Factory name];
    ```

4. Configurare un servizio collegato Database SQL di Azure in Azure Data Factory.

**Esempio**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per definire i set di dati, vedere [set di dati](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). Questa sezione presenta un elenco delle proprietà supportate dal set di dati del database SQL di Azure.

Per copiare dati da o in Database SQL di Azure, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del set di dati deve essere impostata su **AzureSqlTable**. | Sì |
| tableName | Il nome della tabella o della vista nell'istanza del database SQL di Azure a cui fa riferimento il servizio collegato. | No per l'origine, Sì per il sink |

#### <a name="dataset-properties-example"></a>Esempio di proprietà dei set di dati

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink del database SQL di Azure.

### <a name="azure-sql-database-as-the-source"></a>Database SQL di Azure come origine

Per copiare dati da Database SQL di Azure, impostare il **tipo** proprietà nell'origine dell'attività di copia per **SqlSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | Il **tipo** proprietà di origine dell'attività di copia deve essere impostata su **SqlSource**. | Yes |
| sqlReaderQuery | Questa proprietà usa la query SQL personalizzata per leggere i dati. Un esempio è `select * from MyTable`. | No |
| sqlReaderStoredProcedureName | Nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. | No |
| storedProcedureParameters | Parametri per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere i nomi e maiuscole e minuscole dei parametri della stored procedure. | No |

**Punti da notare:**

- Se **sqlReaderQuery** è specificato per **SqlSource**, l'attività di copia esegue questa query nell'origine Database SQL di Azure per ottenere i dati. In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters**, se la stored procedure accetta parametri.
- Se non si specifica uno **sqlReaderQuery** oppure **sqlReaderStoredProcedureName**, le colonne definite nella sezione "struttura" del set di dati JSON vengono usate per costruire una query. La query `select column1, column2 from mytable` viene eseguita sul Database SQL di Azure. Se la definizione del set di dati non include "structure", vengono selezionate tutte le colonne della tabella.

#### <a name="sql-query-example"></a>Esempio di query SQL

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Esempio di stored procedure

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="stored-procedure-definition"></a>Definizione della stored procedure

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-database-as-the-sink"></a>Database SQL di Azure come sink

> [!TIP]
> Altre informazioni su comportamenti di scrittura supportati, configurazioni e le procedure consigliate dagli [procedure consigliate per il caricamento dei dati nel Database SQL di Azure](#best-practice-for-loading-data-into-azure-sql-database).

Per copiare dati in Database SQL di Azure, impostare il **tipo** sink proprietà nell'attività di copia per il **SqlSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | Il **tipo** proprietà del sink dell'attività di copia deve essere impostata su **SqlSink**. | Yes |
| writeBatchSize | Numero di righe da inserire nella tabella SQL *per ogni batch*.<br/> Il valore consentito è **integer** (numero di righe). Per impostazione predefinita, Azure Data Factory determina in modo dinamico le dimensioni del batch appropriato in base alla dimensione di riga. | No |
| writeBatchTimeout | Tempo di attesa per il completamento dell'operazione di inserimento batch prima del timeout.<br/> Il valore consentito è **timespan**. Un esempio è "00:30:00" (30 minuti). | No |
| preCopyScript | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere dati nel Database SQL di Azure. Viene richiamata solo una volta per ogni esecuzione della copia. Usare questa proprietà per pulire i dati precaricati. | No |
| sqlWriterStoredProcedureName | Il nome della stored procedure che definisce come applicare i dati di origine in una tabella di destinazione. <br/>Questa stored procedure viene *richiamata per batch*. Per le operazioni che vengono eseguiti una sola volta e non hanno niente a un'origine dati, ad esempio, delete o truncate, usare il `preCopyScript` proprietà. | No |
| storedProcedureParameters |Parametri per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. | No |
| sqlWriterTableType | Specificare il nome di un tipo di tabella da usare nella stored procedure. L'attività di copia rende i dati spostati disponibili in una tabella temporanea con questo tipo di tabella. Il codice della stored procedure può quindi unire i dati di cui è in corso la copia con i dati esistenti. | No |

**Esempio 1: Accodare dati**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Esempio 2: Richiamare una stored procedure durante la copia**

Per altre informazioni, vedere [Richiamare una stored procedure da un sink SQL](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "sqlWriterTableType": "CopyTestTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Procedure consigliate per il caricamento dei dati nel Database SQL di Azure

Quando si copiano dati in Database SQL di Azure, si potrebbe richiedere diverso comportamento:

- [Accodare](#append-data): I dati di origine contengono solo i nuovi record.
- [Upsert](#upsert-data): I dati di origine contengono sia istruzioni inserts e updates.
- [Sovrascrivere](#overwrite-the-entire-table): Si vuole ricaricare ogni volta che una tabella di tutta la dimensione.
- [Scrivere con logica personalizzata](#write-data-with-custom-logic): Ho bisogno di ulteriore elaborazione prima dell'inserimento finale nella tabella di destinazione.

Fare riferimento alle rispettive sezioni sulla configurazione in Azure Data Factory e le procedure consigliate.

### <a name="append-data"></a>Aggiungere i dati

Aggiunta di dati è il comportamento predefinito di questo connettore di sink di Database SQL di Azure. Azure Data Factory esegue un inserimento bulk per scrivere in modo efficiente alla tabella. È possibile configurare l'origine e di conseguenza di sink nell'attività di copia.

### <a name="upsert-data"></a>Eseguire l'upsert dei dati

**Opzione 1:** Se si ha una grande quantità di dati da copiare, utilizzare l'approccio seguente per eseguire un'operazione di upsert: 

- In primo luogo, utilizzare un [tabelle temporanee con ambito database](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) per il caricamento bulk, tutti i record usando l'attività di copia. Perché non vengono registrate operazioni su tabelle temporanee con ambito database, è possibile caricare milioni di record in pochi secondi.
- Eseguire un'attività stored procedure in Azure Data Factory per applicare una [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) o un'istruzione di inserimento/aggiornamento. Usare la tabella temporanea come origine per eseguire tutte le aggiorna o inserisce una singola transazione. In questo modo, viene ridotto il numero di round trip e operazioni del log. Al termine dell'attività stored procedure, la tabella temporanea può essere troncata per la preparazione per il successivo ciclo di upsert.

Ad esempio, in Azure Data Factory, è possibile creare una pipeline con un' **attività di copia** concatenato con un **attività di Stored Procedure**. La prima copia i dati dall'archivio di origine in una tabella temporanea di Database SQL di Azure, ad esempio, **##UpsertTempTable**, come il nome della tabella nel set di dati. Quest'ultimo richiama quindi una stored procedure per unire i dati di origine dalla stessa tabella nella tabella di destinazione e pulire la tabella temporanea.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Nel database, definire una stored procedure con logica di unione, come nell'esempio seguente, che viene fatto riferimento dall'attività di stored procedure precedente. Si supponga che la destinazione è il **Marketing** tabella con tre colonne: **ProfileID**, **State** e **Category**. Eseguire l'operazione upsert in base il **ProfileID** colonna.

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Opzione 2:** È anche possibile effettuare [richiamare una stored procedure all'interno dell'attività di copia](#invoke-a-stored-procedure-from-a-sql-sink). Questo approccio viene eseguito ogni riga nella tabella di origine invece di uso di bulk insert come l'approccio predefinito nell'attività di copia, non è appropriato per l'operazione upsert su larga scala.

### <a name="overwrite-the-entire-table"></a>Sovrascrivere l'intera tabella

È possibile configurare il **preCopyScript** proprietà nel sink dell'attività di copia. In questo caso, per ogni attività di copia che viene eseguito, Azure Data Factory esegue lo script prima di tutto. Viene quindi eseguita la copia per inserire i dati. Ad esempio, per sovrascrivere l'intera tabella con i dati più recenti, specificare uno script per eliminare innanzitutto tutti i record prima bulk caricare nuovi dati dall'origine.

### <a name="write-data-with-custom-logic"></a>Scrivere i dati con logica personalizzata

I passaggi per scrivere i dati con logica personalizzata sono simili a quelli descritti nel [Upsert dati](#upsert-data) sezione. Quando è necessario applicare aggiuntivi di elaborazione prima dell'inserimento finale dei dati di origine nella tabella di destinazione, per la scalabilità di grandi dimensioni, è possibile eseguire una delle seguenti operazioni:

- Carica in una tabella temporanea con ambito database e quindi richiamare una stored procedure. 
- Richiamare una stored procedure durante la copia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Richiamare una stored procedure da un sink SQL

Quando si copiano dati in Database SQL di Azure, è anche possibile configurare e richiamare una specificato dall'utente stored procedure con parametri aggiuntivi.

> [!TIP]
> Richiamare una stored procedure elabora i dati riga per riga, anziché mediante un'operazione bulk, che non è consigliabile per la copia su larga scala. Altre informazioni, vedere [procedure consigliate per il caricamento dei dati nel Database SQL di Azure](#best-practice-for-loading-data-into-azure-sql-database).

È possibile usare una stored procedure quando non si possono usare i meccanismi di copia predefiniti. Un esempio è quando si desidera applicare ulteriori operazioni di elaborazione prima dell'inserimento finale dei dati di origine nella tabella di destinazione. Alcuni esempi di operazioni di elaborazione aggiuntive sono per le colonne di tipo merge, cercare valori aggiuntivi e inserire in più di una tabella.

L'esempio seguente illustra come usare una stored procedure per eseguire un'operazione di upsert in una tabella del database SQL di Azure. Si supponga che i dati di input e il sink **Marketing** ogni tabella dispone di tre colonne: **ProfileID**, **State** e **Category**. Eseguire l'operazione di upsert nella colonna **ProfileID** e applicarla solo a una categoria specifica.

**Set di dati di output:** Il valore "tableName" è lo stesso nome parametro di tipo tabella della stored procedure, come illustrato nello script di stored procedure seguente:

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "Marketing"
        }
    }
}
```

Definire le **sink SQL** sezione nell'attività di copia come indicato di seguito:

```json
"sink": {
    "type": "SqlSink",
    "SqlWriterTableType": "MarketingType",
    "SqlWriterStoredProcedureName": "spOverwriteMarketing",
    "storedProcedureParameters": {
        "category": {
            "value": "ProductA"
        }
    }
}
```

Nel database definire la stored procedure con lo stesso nome di **SqlWriterStoredProcedureName**. che gestisce i dati di input dell'origine specificata e li unisce nella tabella di output. Il nome del parametro del tipo di tabella nella stored procedure è identico **tableName** definiti nel set di dati.

```sql
CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
AS
BEGIN
  MERGE [dbo].[Marketing] AS target
  USING @Marketing AS source
  ON (target.ProfileID = source.ProfileID and target.Category = @category)
  WHEN MATCHED THEN
      UPDATE SET State = source.State
  WHEN NOT MATCHED THEN
      INSERT (ProfileID, State, Category)
      VALUES (source.ProfileID, source.State, source.Category);
END
```

Nel database definire il tipo di tabella con lo stesso nome **sqlWriterTableType**. Lo schema del tipo di tabella è identico allo schema restituito dai dati di input.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL,
    [Category] [varchar](256) NOT NULL
)
```

La funzionalità di stored procedure sfrutta i [parametri valutati a livello di tabella](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Informazioni dettagliate dal [trasformazione sorgente](data-flow-source.md) e [sink trasformazione](data-flow-sink.md) nell'eseguire il mapping di flusso di dati.

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapping dei tipi di dati per il database SQL di Azure

Quando si copiano dati da o in Database SQL di Azure, vengono usati i mapping seguenti dai tipi di dati di Database SQL di Azure per i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipi di dati del database SQL di Azure | Tipo di dati provvisorio di Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| date |DateTime |
| Datetime |DateTime |
| datetime2 |DateTime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |DateTime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object |
| text |String, Char[] |
| time |TimeSpan |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |Xml |

>[!NOTE]
> Per i tipi di dati associati al tipo provvisorio Decimal, Azure Data Factory supporta attualmente la precisione fino a 28. Se si dispone di dati con una precisione maggiore di 28, è consigliabile convertire in una stringa di query SQL.

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [archivi dati e formati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
