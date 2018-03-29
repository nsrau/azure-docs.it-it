---
title: Copiare dati dal/nel database SQL di Azure usando Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da archivi dati di origine supportati nel database SQL di Azure o dal database SQL in archivi dati sink supportati usando Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2018
ms.author: jingwang
ms.openlocfilehash: 12f673a8d3ca9c0bb03b9cd2d8c33ae866039289
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
# <a name="copy-data-to-or-from-azure-sql-database-by-using-azure-data-factory"></a>Copiare dati da o nel database SQL di Azure tramite Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-azure-sql-connector.md)
> * [Versione 2 - Anteprima](connector-azure-sql-database.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database SQL di Azure. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Connettore database SQL di Azure in V1](v1/data-factory-azure-sql-connector.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare i dati da o verso un database SQL di Azure in qualsiasi archivio dati sink supportato oppure copiare i dati da un archivio dati di origine supportato in un database SQL di Azure. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore database SQL di Azure supporta:

- La copia di dati tramite l'**autenticazione SQL** e l'**autenticazione token dell'applicazione di Azure Active Directory** con entità servizio o identità del servizio gestito.
- Come origine, il recupero di dati tramite query SQL o stored procedure.
- Come sink, l'aggiunta di dati alla tabella di destinazione o la chiamata a una stored procedure con logica personalizzata durante la copia.

> [!IMPORTANT]
> Se si copiano i dati tramite il runtime di integrazione di Azure, configurare il [firewall del server SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) per [consentire ai servizi di Azure di accedere al server](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Se si copiano i dati tramite il runtime di integrazione self-hosted, configurare il firewall del server SQL di Azure per consentire un intervallo di indirizzi IP appropriato, incluso l'indirizzo IP del computer usato per la connessione al database SQL di Azure.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di data factory specifiche in un connettore del database SQL di Azure.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato database SQL di Azure sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà del tipo deve essere impostata su: **AzureSqlDatabase** | Sì |
| connectionString |Specificare le informazioni necessarie per connettersi all'istanza di database SQL di Azure per la proprietà connectionString. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì, quando si usa l'autenticazione AAD con entità servizio. |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì, quando si usa l'autenticazione AAD con entità servizio. |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì, quando si usa l'autenticazione AAD con entità servizio. |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No  |

Per altri tipi di autenticazione, fare riferimento alle sezioni seguenti relative, rispettivamente, ai prerequisiti e agli esempi JSON:

- [Uso dell'autenticazione SQL](#using-sql-authentication)
- [Uso dell'autenticazione token dell'applicazione di AAD: entità servizio](#using-service-principal-authentication)
- [Uso dell'autenticazione token dell'applicazione di AAD: identità del servizio gestito](#using-managed-service-identity-authentication)

### <a name="using-sql-authentication"></a>Uso dell'autenticazione SQL

**Esempio di servizio collegato tramite l'autenticazione SQL:**

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

### <a name="using-service-principal-authentication"></a>Uso dell'autenticazione basata sull'entità servizio

Per usare l'autenticazione token dell'applicazione di AAD basata sull'entità servizio, seguire questa procedura:

1. **[Creare un'applicazione di Azure Active Directory nel portale di Azure](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).**  Prendere nota del nome dell'applicazione e dei valori seguenti da usare per definire il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. **[Effettuare il provisioning di un amministratore di Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#create-an-azure-ad-administrator-for-azure-sql-server)** per il server SQL di Azure nel portale di Azure, se l'operazione non è già stata eseguita. L'amministratore di AAD deve essere un utente o un gruppo di AAD, ma non può essere un'entità servizio. Questo passaggio viene eseguito in modo che, nel passaggio successivo, sia possibile usare un'identità di AAD per creare un utente di database indipendente per l'entità servizio.

3. **Creare un utente di database indipendente per l'entità servizio** tramite la connessione al database da/verso cui si vogliono copiare i dati usando strumenti come SQL Server Management Studio, con un'identità di AAD che abbia almeno l'autorizzazione ALTER ANY USER, e l'esecuzione del comando in T-SQL seguente. Per altre informazioni sull'utente di database indipendente, consultare [questo articolo](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Concedere all'entità servizio le autorizzazioni necessarie**, come si fa di norma per gli utenti SQL, ad esempio tramite l'esecuzione del codice seguente:

    ```sql
    EXEC sp_addrolemember '[your application name]', 'readonlyuser';
    ```

5. Nel file di definizione dell'applicazione (ADF) configurare un servizio collegato al database SQL di Azure.


**Esempio di servizio collegato tramite l'autenticazione basata su entità servizio:**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
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

### <a name="using-managed-service-identity-authentication"></a>Uso dell'autenticazione basata sull'identità del servizio gestito

Una data factory può essere associata a un'[identità del servizio gestito](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare questa identità del servizio per l'autenticazione del database SQL di Azure, che consente a questa factory designata di accedere e copiare i dati dal/nel database.

Per usare l'autenticazione token dell'applicazione di AAD basata sull'identità del servizio gestito, seguire questa procedura:

1. **Creare un gruppo in Azure AD e aggiungere l'identità del servizio gestito della factory come membro del gruppo**.

    a. Trovare l'identità del servizio della data factory nel portale di Azure. Passare alla data factory -> Proprietà -> copiare il valore di **ID IDENTITÀ DEL SERVIZIO**.

    b. Installare il modulo [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2), eseguire l'accesso usando il comando `Connect-AzureAD` ed eseguire i comandi seguenti per creare un gruppo e aggiungere l'identità del servizio gestito della data factory come membro.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

2. **[Effettuare il provisioning di un amministratore di Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#create-an-azure-ad-administrator-for-azure-sql-server)** per il server SQL di Azure nel portale di Azure, se l'operazione non è già stata eseguita. L'amministratore di AAD può essere un utente o un gruppo di AAD. Se si concede al gruppo con identità del servizio gestito un ruolo di amministratore, ignorare i passaggi 3 e 4 riportati di seguito, in quanto l'amministratore avrà l'accesso completo al database.

3. **Creare un utente di database indipendente per il gruppo di AAD** tramite la connessione al database da/verso cui si vogliono copiare i dati usando strumenti come SQL Server Management Studio, con un'identità di AAD che abbia almeno l'autorizzazione ALTER ANY USER, e l'esecuzione del comando in T-SQL seguente. Per altre informazioni sull'utente di database indipendente, consultare [questo articolo](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities).
    
    ```sql
    CREATE USER [your AAD group name] FROM EXTERNAL PROVIDER;
    ```

4. **Concedere al gruppo di AAD le autorizzazioni necessarie**, come si fa di norma per gli utenti SQL, ad esempio tramite l'esecuzione del codice seguente:

    ```sql
    EXEC sp_addrolemember '[your AAD group name]', 'readonlyuser';
    ```

5. Nel file di definizione dell'applicazione (ADF) configurare un servizio collegato al database SQL di Azure.

**Esempio di servizio collegato tramite l'autenticazione basata sull'identità del servizio gestito:**

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

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati del database SQL di Azure.

Per copiare dati da/verso il database SQL di Azure, impostare la proprietà type del set di dati su **AzureSqlTable**. Sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **AzureSqlTable** | Sì |
| tableName |Nome della tabella o vista nell'istanza di database SQL di Azure a cui fa riferimento il servizio collegato. | Sì |

**Esempio:**

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
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink del database SQL di Azure.

### <a name="azure-sql-database-as-source"></a>Database SQL di Azure come origine

Per copiare i dati da un database SQL di Azure, impostare il tipo di origine nell'attività di copia su **SqlSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **SqlSource** | Sì |
| SqlReaderQuery |Usare la query SQL personalizzata per leggere i dati. Esempio: `select * from MyTable`. |No  |
| sqlReaderStoredProcedureName |Nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. |No  |
| storedProcedureParameters |Parametri per la stored procedure.<br/>I valori consentiti sono: coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No  |

**Punti da notare:**

- Se la proprietà **sqlReaderQuery** è specificata per SqlSource, l'attività di copia esegue questa query nell'origine del database SQL di Azure per ottenere i dati. In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri).
- Se non si specifica "sqlReaderQuery" o "sqlReaderStoredProcedureName", le colonne definite nella sezione "struttura" del set di dati JSON vengono usate per creare una query, `select column1, column2 from mytable`, da eseguire sul database SQL di Azure. Se la definizione del set di dati non dispone della "struttura", vengono selezionate tutte le colonne della tabella.
- Quando si usa **sqlReaderStoredProcedureName** è necessario specificare un valore fittizio per la proprietà **tableName** nel set di dati JSON.

**Esempio: uso della query SQL**

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

**Esempio: uso della stored procedure**

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

**Definizione della stored procedure:**

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

### <a name="azure-sql-database-as-sink"></a>Database SQL di Azure come sink

Per copiare i dati da un database SQL di Azure, impostare il tipo di sink nell'attività di copia su **SqlSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su: **SqlSink** | Sì |
| writeBatchSize |Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge writeBatchSize.<br/>I valori consentiti sono integer, ovvero il numero di righe. |No (il valore predefinito è 10.000) |
| writeBatchTimeout |Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br/>I valori consentiti sono: intervallo di tempo. Ad esempio: "00:30:00" (30 minuti). |No  |
| preCopyScript |Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati nel database SQL di Azure. Può essere richiamata solo una volta per esecuzione della copia. È possibile usare questa proprietà per pulire i dati precaricati. |No  |
| sqlWriterStoredProcedureName |Nome della stored procedure che definisce come applicare i dati di origine nella tabella di destinazione, ad esempio per eseguire upsert o trasformazioni usando logica di business personalizzata. <br/><br/>Si noti che questa stored procedure verrà **richiamata per batch**. Se si vuole eseguire una sola volta un'operazione che non ha nulla a che fare con i dati di origine, ad esempio un'eliminazione o un troncamento, usare la proprietà `preCopyScript`. |No  |
| storedProcedureParameters |Parametri per la stored procedure.<br/>I valori consentiti sono: coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No  |
| sqlWriterTableType |Specificare il nome di un tipo di tabella da usare nella stored procedure. L'attività di copia rende i dati spostati disponibili in una tabella temporanea con questo tipo di tabella. Il codice della stored procedure può quindi unire i dati copiati con i dati esistenti. |No  |

> [!TIP]
> Quando si copiano dati in un database SQL di Azure, per impostazione predefinita l'attività di copia aggiunge i dati alla tabella di sink. Per eseguire un'operazione UPSERT o una logica di business aggiuntiva, usare la stored procedure in SqlSink. Altre informazioni sono contenute in [Richiamo delle stored procedure per SQL Sink](#invoking-stored-procedure-for-sql-sink).

**Esempio 1: aggiunta di dati**

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

**Esempio 2: richiamare una stored procedure durante la copia per l'operazione upsert**

Altre informazioni sono contenute in [Richiamo delle stored procedure per SQL Sink](#invoking-stored-procedure-for-sql-sink).

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

## <a name="identity-columns-in-the-target-database"></a>Colonne Identity nel database di destinazione

In questa sezione viene fornito un esempio per la copia di dati da una tabella di origine senza una colonna identity in una tabella di destinazione con una colonna identity.

**Tabella di origine:**

```sql
create table dbo.SourceTbl
(
       name varchar(100),
       age int
)
```

**Tabella di destinazione:**

```sql
create table dbo.TargetTbl
(
       identifier int identity(1,1),
       name varchar(100),
       age int
)
```

Si noti che la tabella di destinazione contiene una colonna identity.

**Definizione JSON del set di dati di origine**

```json
{
    "name": "SampleSource",
    "properties": {
        "type": " AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "SourceTbl"
        }
    }
}
```

**Definizione JSON del set di dati di destinazione**

```json
{
    "name": "SampleTarget",
    "properties": {
        "structure": [
            { "name": "name" },
            { "name": "age" }
        ],
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "TestIdentitySQL",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "TargetTbl"
        }
    }
}
```

Si noti che la tabella di origine e la tabella di destinazione hanno schemi diversi (la destinazione include una colonna aggiuntiva identity). In questo scenario è necessario specificare la proprietà **structure** nella definizione del set di dati di destinazione che non include la colonna identity.

## <a name="invoke-stored-procedure-from-sql-sink"></a>Chiamare una stored procedure da un sink SQL

Quando si copiano dati in un database SQL di Azure, è possibile configurare e richiamare una stored procedure specificata da un utente con parametri aggiuntivi.

È possibile usare una stored procedure quando non è possibile usare i meccanismi di copia predefiniti. In genere viene usata quando un'elaborazione upsert, ovvero di inserimento e aggiornamento, o un'operazione supplementare, ovvero di unione colonne, ricerca di valori aggiuntivi, inserimento in più tabelle e così via, deve essere eseguita prima dell'inserimento finale dei dati di origine nella tabella di destinazione.

Nell'esempio seguente viene illustrato come usare una stored procedure per eseguire un'operazione upsert in una tabella del database SQL di Azure. Si presuppone che la tabella "Marketing" dei dati inseriti e dei sink abbia tre colonne: ProfileID, Stato e Categoria. Eseguire quindi l'operazione upsert nella colonna "ProfileID" e applicarla solo a una categoria specifica.

**Set di dati di output**

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

Definire la sezione "SqlSink" nell'attività di copia come indicato di seguito.

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

Nel database definire la stored procedure con lo stesso nome di "SqlWriterStoredProcedureName", che gestisce i dati di input dell'origine specificata e li inserisce nella tabella di output. Il nome del parametro della stored procedure deve essere identico al valore "tableName" definito nel set di dati.

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
      VALUES (source.ProfileID, source.State, source.Category)
END
```

Nel database definire il tipo di tabella con lo stesso nome di sqlWriterTableType. Si noti che lo schema del tipo di tabella deve essere identico allo schema restituito dai dati di input.

```sql
CREATE TYPE [dbo].[MarketingType] AS TABLE(
    [ProfileID] [varchar](256) NOT NULL,
    [State] [varchar](256) NOT NULL，
    [Category] [varchar](256) NOT NULL，
)
```

La funzionalità di stored procedure sfrutta i [parametri valutati a livello di tabella](https://msdn.microsoft.com/library/bb675163.aspx).

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapping dei tipi di dati per il database SQL di Azure

Quando si copiano i dati da o verso il database SQL di Azure, vengono usati i mapping seguenti tra i tipi di dati del database SQL di Azure e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipi di dati del database SQL di Azure | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| bigint |Int64 |
| binary |Byte[] |
| bit |boolean |
| char |String, Char[] |
| date |Datetime |
| DateTime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| immagine |Byte[] |
| int |Int32 |
| money |Decimal |
| nchar |String, Char[] |
| ntext |String, Char[] |
| numeric |Decimal |
| nvarchar |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| smallint |Int16 |
| smallmoney |Decimal |
| sql_variant |Object * |
| text |String, Char[] |
| time |Intervallo di tempo |
| timestamp |Byte[] |
| tinyint |Byte |
| uniqueidentifier |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| xml |xml |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).