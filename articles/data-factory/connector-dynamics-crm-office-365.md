---
title: Copiare dati da/in Dynamics CRM e 365 usando Azure Data Factory | Microsoft Docs
description: "Informazioni su come copiare dati da Dynamics CRM e 365 in archivi dati di sink supportati o da archivi dati di origine supportati in Dynamics CRM e 365 usando un'attività di copia in una pipeline di Azure Data Factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/21/2017
ms.author: jingwang
ms.openlocfilehash: b0906ef180359cef2f83042d9aa5a0f8296bac8a
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2017
---
# <a name="copy-data-fromto-dynamics-365dynamics-crm-using-azure-data-factory"></a>Copiare dati da/in Dynamics 365/Dynamics CRM usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Dynamics 365/Dynamics CRM. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da Dynamics 365/Dynamics CRM in qualsiasi archivio dati di sink supportato o da qualsiasi archivio dati di origine supportato in Dynamics 365/Dynamics CRM. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Questo connettore Dynamics supporta le versioni di Dynamics e i tipi di autenticazione seguenti. *IFD (Internet Facing Deployment) indica una distribuzione con connessione Internet*:

| Versioni di Dynamics | Tipi di autenticazione | Esempi di servizi collegati |
|:--- |:--- |:--- |
| Dynamics 365 Online <br> Dynamics CRM Online | Office365 | [Dynamics Online + autenticazione di Office365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 locale con IFD <br> Dynamics CRM 2016 locale con IFD <br> Dynamics CRM 2015 locale con IFD | IFD | [Dynamics locale IFD + autenticazione IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Per Dynamics 365 in particolare, sono supportati i tipi di applicazioni seguenti:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

> [!NOTE]
> Per usare un connettore di Dynamics, archiviare la password nell'insieme di credenziali chiave di Azure e consentire il pull di attività di copia da qui quando si esegue una copia dei dati. Per informazioni sulle procedure di configurazione, vedere la sezione [Proprietà del servizio collegato](#linked-service-properties).

## <a name="getting-started"></a>Introduzione

È possibile creare una pipeline con l'attività di copia usando .NET SDK, Python SDK, Azure PowerShell, l'API REST o il modello Azure Resource Manager. Vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per Dynamics.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Dynamics sono supportate le proprietà seguenti:

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 e Dynamics CRM Online

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Dynamics**. | Sì |
| deploymentType | Il tipo di distribuzione dell'istanza di Dynamics. Deve essere **"Online"** per Dynamics Online. | Sì |
| organizationName | Il nome organizzazione dell'istanza di Dynamics. | No, deve essere specificato solo se sono presenti più istanze di Dynamics associate all'utente. |
| authenticationType | Il tipo di autenticazione per la connessione al server Dynamics. Specificare **"Office365"** per Dynamics Online. | Sì |
| username | Specificare il nome utente per la connessione a Dynamics. | Sì |
| password | Specificare la password per l'account utente specificato per il nome utente. È necessario inserire la password in Azure Key Vault e configurarla come "AzureKeyVaultSecret". Per altre informazioni, vedere [Archiviare le credenziali nell'insieme di credenziali delle chiavi](store-credentials-in-key-vault.md). | Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No per l'origine, Sì per il sink |

>[!IMPORTANT]
>Per copiare i dati in Dynamics, [creare un runtime di integrazione di Azure](create-azure-integration-runtime.md#create-azure-ir) in modo esplicito con una posizione prossima a Dynamics e associarlo al servizio collegato come illustrato nell'esempio seguente.

**Esempio: Dynamics Online usando l'autenticazione di Office365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "organizationName": "orga02d9c75",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 e Dynamics CRM locale con IFD

*Proprietà aggiuntive rispetto a Dynamics Online sono "hostName" e "port".*

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Dynamics**. | Sì |
| deploymentType | Il tipo di distribuzione dell'istanza di Dynamics. Deve essere **"OnPremisesWithIfd"** per Dynamics locale con IFD.| Sì |
| **hostName** | Il nome host del server Dynamics locale. | Sì |
| **port** | La porta del server Dynamics locale. | No, il valore predefinito è 443 |
| organizationName | Il nome organizzazione dell'istanza di Dynamics. | Sì |
| authenticationType | Il tipo di autenticazione per la connessione al server Dynamics. Specificare **"Ifd"** per Dynamics locale con IFD. | Sì |
| username | Specificare il nome utente per la connessione a Dynamics. | Sì |
| password | Specificare la password per l'account utente specificato per il nome utente. È necessario inserire la password in Azure Key Vault e configurarla come "AzureKeyVaultSecret". Per altre informazioni, vedere [Archiviare le credenziali nell'insieme di credenziali delle chiavi](store-credentials-in-key-vault.md). | Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No per l'origine, Sì per il sink |

>[!IMPORTANT]
>Per copiare i dati in Dynamics, [creare un runtime di integrazione di Azure](create-azure-integration-runtime.md#create-azure-ir) in modo esplicito con una posizione prossima a Dynamics e associarlo al servizio collegato come illustrato nell'esempio seguente.

**Esempio: Dynamics locale con IFD usando l'autenticazione IFD**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics on-premises with IFD linked service using IFD authentication",
        "typeProperties": {
            "deploymentType": "OnPremisesWithIFD",
            "hostName": "contosodynamicsserver.contoso.com",
            "port": 443,
            "organizationName": "admsDynamicsTest",
            "authenticationType": "Ifd",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Dynamics.

Per copiare dati da/in Dynamics, impostare la proprietà type del set di dati su **DynamicsEntity**. Sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **DynamicsEntity** |Sì |
| entityName | Il nome logico dell'entità da recuperare. | No per l'origine (se nell'origine dell'attività è specificato "query"), Sì per il sink |

> [!IMPORTANT]
>- **Quando si copiano dati da Dynamics, la sezione "struttura" è necessaria** nel set di dati di Dynamics, che definisce il nome di colonna e il tipo di dati per i dati di Dynamics che si vuole copiare. Altre informazioni sono disponibili nelle sezioni [Struttura del set di dati](concepts-datasets-linked-services.md#dataset-structure) e [Mapping dei tipi di dati per Dynamics](#data-type-mapping-for-dynamics).
>- **Quando si copiano dati da Dynamics, la sezione "struttura" è facoltativa** nel set di dati di Dynamics. Le colonne in cui eseguire la copia verranno determinate dallo schema dei dati di origine. Se l'origine è un file con estensione csv senza intestazione, nel set di dati di input specificare la "struttura" con il nome e il tipo di dati della colonna associati singolarmente mediante mapping ai campi nel file con estensione csv.

**Esempio:**

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "structure": [
            {
                "name": "accountid",
                "type": "Guid"
            },
            {
                "name": "name",
                "type": "String"
            },
            {
                "name": "marketingonly",
                "type": "Boolean"
            },
            {
                "name": "modifiedon",
                "type": "Datetime"
            }
        ],
        "typePoperties": {
            "entityName": "account"
        },
        "linkedServiceName": {
            "referenceName": "<Dynamics linked service name>",
            "type": "linkedservicereference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Dynamics.

### <a name="dynamics-as-source"></a>Dynamics come origine

Per copiare dati da Dynamics, impostare il tipo di origine nell'attività di copia su **DynamicsSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **DynamicsSource**  | Sì |
| query  | FetchXML è un linguaggio di query proprietario usato in Microsoft Dynamics (Online e locale). Per altre informazioni, vedere l'esempio seguente e l'articolo [Creare query con FetchXML](https://msdn.microsoft.com/en-us/library/gg328332.aspx). | No (se nel set di dati è specificato "entityName")  |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Dynamics input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DynamicsSource",
                "query": "<FetchXML Query>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="sample-fetchxml-query"></a>Esempio di query FetchXML

```xml
<fetch>
  <entity name="account">
    <attribute name="accountid" />
    <attribute name="name" />
    <attribute name="marketingonly" />
    <attribute name="modifiedon" />
    <order attribute="modifiedon" descending="false" />
    <filter type="and">
      <condition attribute ="modifiedon" operator="between">
        <value>2017-03-10 18:40:00z</value>
        <value>2017-03-12 20:40:00z</value>
      </condition>
    </filter>
  </entity>
</fetch>
```

### <a name="dynamics-as-sink"></a>Dynamics come sink

Per copiare dati in Dynamics, impostare il tipo di sink nell'attività di copia su **DynamicsSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su: **DynamicsSink**  | Sì |
| writebehavior | Comportamento dell'azione di scrittura dell'operazione.<br/>Il valore consentito è: **"Upsert"**. | Sì |
| writeBatchSize | Conteggio delle righe di dati scritti da Dynamics in ogni batch. | No (il valore predefinito è 10) |
| ignoreNullValues | Indica se ignorare i valori null dai dati di input (tranne i campi chiave) durante l'operazione di scrittura.<br/>I valori consentiti sono: **true** e **false**.<br>- true: lascia invariati i dati nell'oggetto di destinazione durante l'operazione di upsert/aggiornamento e inserisce il valore predefinito durante l'operazione di inserimento.<br/>- false: aggiorna i dati nell'oggetto di destinazione impostandoli su NULL durante l'operazione di upsert/aggiornamento e inserisce il valore NULL durante l'operazione di inserimento.  | No (il valore predefinito è false) |

>[!NOTE]
>Il valore predefinito della proprietà [parallelCopies](copy-activity-performance.md#parallel-copy) dell'attività di copia e della proprietà writeBatchSize per il sink di Dynamics è in entrambi i casi 10, ovvero 100 record inviati contemporaneamente a Dynamics.

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToDynamics",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Dynamics output dataset>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "DynamicsSink",
                "writeBehavior": "Upsert",
                "writeBatchSize": 10,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="data-type-mapping-for-dynamics"></a>Mapping dei tipi di dati per Dynamics

Quando si copiano dati da Dynamics, vengono usati i mapping seguenti tra i tipi di dati di Dynamics e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

Configurare il tipo di dati di Data Factory corrispondente nella struttura di set di dati in base all'origine dati di Dynamics digitare con tabella di mapping seguente:

| Tipo di dati di Dynamics | Tipo di dati provvisori di Data Factory | Supportato come origine | Supportato come sink |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | boolean | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ |  |
| AttributeType.DateTime | DateTime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | string | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ |  |
| AttributeType.ManagedProperty | boolean | ✓ |  |
| AttributeType.Memo | string | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | string | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |


> [!NOTE]
> Non sono supportati i tipi di dati di Dynamics AttributeType.CalendarRules e AttributeType.PartyList.

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).