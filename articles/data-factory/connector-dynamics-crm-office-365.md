---
title: Copiare dati da Dynamics CRM e 365 usando Azure Data Factory | Microsoft Docs
description: "Informazioni su come copiare dati da Dynamics CRM e 365 in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory."
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
ms.date: 11/02/2017
ms.author: jingwang
ms.openlocfilehash: 1af330596052a92237469aba4729474e7fe417aa
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-dynamics-365dynamics-crm-using-azure-data-factory"></a>Copiare dati da Dynamics 365/Dynamics CRM usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da Dynamics 365/Dynamics CRM. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da Dynamics 365/Dynamics CRM in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Dynamics supporta le versioni di Dynamics e i tipi di autenticazione seguenti:

| Versioni di Dynamics | Tipi di autenticazione | Esempi di servizi collegati |
|:--- |:--- |:--- |
| Dynamics 365 Online <br> Dynamics CRM Online | Office365 | [Dynamics Online + autenticazione di Office365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 locale con IFD <br> Dynamics CRM 2016 locale con IFD <br> Dynamics CRM 2015 locale con IFD | IFD | [Dynamics locale IFD + autenticazione IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

*IFD è l'acronimo di Internet Facing Deployment (distribuzione con connessione Internet).*

> [!NOTE]
> Per usare il connettore Dynamics, archiviare la password in Azure Key Vault e consentire all'attività di copia di Azure Data Factory di effettuare il pull da qui quando esegue la copia dei dati. Per informazioni sulle procedure di configurazione, vedere la sezione [Proprietà del servizio collegato](#linked-service-properties).

## <a name="getting-started"></a>introduttiva

È possibile creare una pipeline con l'attività di copia usando .NET SDK, Python SDK, Azure PowerShell, l'API REST o il modello Azure Resource Manager. Vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per Dynamics.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Dynamics sono supportate le proprietà seguenti:

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 e Dynamics CRM Online

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Dynamics**. | Sì |
| deploymentType | Il tipo di distribuzione dell'istanza di Dynamics. Deve essere **"Online"** per Dynamics Online. | Sì |
| organizationName | Il nome organizzazione dell'istanza di Dynamics. | No, deve essere specificato solo se sono presenti più istanze di Dynamics associate all'utente. |
| authenticationType | Il tipo di autenticazione per la connessione al server Dynamics. Specificare **"Office365"** per Dynamics Online. | Sì |
| username | Specificare il nome utente per la connessione a Dynamics. | Sì |
| password | Specificare la password per l'account utente specificato per il nome utente. È necessario inserire la password in Azure Key Vault e configurarla come "AzureKeyVaultSecret". Per altre informazioni, vedere l'articolo [Store credentials in Key Vault](store-credentials-in-key-vault.md) (Archiviare credenziali in Key Vault). | Sì |

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
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

### <a name="dynamics-365-and-dynamics-crm-on-premises-with-ifd"></a>Dynamics 365 e Dynamics CRM locale con IFD

*Proprietà aggiuntive rispetto a Dynamics Online sono "hostName" e "port".*

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Dynamics**. | Sì |
| deploymentType | Il tipo di distribuzione dell'istanza di Dynamics. Deve essere **"OnPremisesWithIfd"** per Dynamics locale con IFD.| Sì |
| **hostName** | Il nome host del server Dynamics locale. | Sì |
| **port** | La porta del server Dynamics locale. | No, il valore predefinito è 443 |
| organizationName | Il nome organizzazione dell'istanza di Dynamics. | Sì |
| authenticationType | Il tipo di autenticazione per la connessione al server Dynamics. Specificare **"Ifd"** per Dynamics locale con IFD. | Sì |
| username | Specificare il nome utente per la connessione a Dynamics. | Sì |
| password | Specificare la password per l'account utente specificato per il nome utente. È necessario inserire la password in Azure Key Vault e configurarla come "AzureKeyVaultSecret". Per altre informazioni, vedere l'articolo [Store credentials in Key Vault](store-credentials-in-key-vault.md) (Archiviare credenziali in Key Vault). | Sì |

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
                "secretName": "mySecret",
                "store":{
                    "linkedServiceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Dynamics.

Per copiare dati da Dynamics, impostare la proprietà type del set di dati su **DynamicsEntity**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **DynamicsEntity** |Sì |
| entityName | Il nome logico dell'entità da recuperare. | No (se nell'origine dell'attività è specificato "query") |

> [!IMPORTANT]
> **Per Dynamics è necessaria la sezione "struttura" nel set di dati**, che definisce il nome di colonna e il tipo di dati per i dati di Dynamics che si vuole copiare. Altre informazioni sono disponibili nelle sezioni [Struttura del set di dati](concepts-datasets-linked-services.md#dataset-structure) e [Mapping dei tipi di dati per Dynamics](#data-type-mapping-for-dynamics).

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Dynamics.

### <a name="dynamics-as-source"></a>Dynamics come origine

Per copiare dati da Dynamics, impostare il tipo di origine nell'attività di copia su **DynamicsSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **DynamicsSource**  | Sì |
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

## <a name="data-type-mapping-for-dynamics"></a>Mapping dei tipi di dati per Dynamics

Quando si copiano dati da Dynamics, vengono usati i mapping seguenti tra i tipi di dati di Dynamics e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

Nella struttura del set di dati, configurare il tipo di dati di Azure Data Factory corrispondente in base al tipo di dati di Dynamics di origine usando la tabella di mapping seguente:

| Tipo di dati di Dynamics | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| AttributeTypeCode.BigInt | long |
| AttributeTypeCode.Boolean | Boolean |
| AttributeType.Customer | Guid |
| AttributeType.DateTime | Datetime |
| AttributeType.Decimal | Decimal |
| AttributeType.Double | Double |
| AttributeType.EntityName | String |
| AttributeType.Integer | Int32 |
| AttributeType.Lookup | Guid |
| AttributeType.ManagedProperty | Boolean |
| AttributeType.Memo | String |
| AttributeType.Money | Decimal |
| AttributeType.Owner | Guid |
| AttributeType.Picklist | Int32 |
| AttributeType.Uniqueidentifier | Guid |
| AttributeType.String | String |
| AttributeType.State | Int32 |
| AttributeType.Status | Int32 |

> [!NOTE]
> Non sono supportati i tipi di dati di Dynamics AttributeType.CalendarRules e AttributeType.PartyList.


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).