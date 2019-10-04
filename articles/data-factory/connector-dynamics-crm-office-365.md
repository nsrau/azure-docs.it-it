---
title: Copiare i dati da e in Dynamics CRM o Dynamics 365 (Common Data Service) usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da Microsoft Dynamics CRM o Microsoft Dynamics 365 (Common Data Service) in archivi dati di sink supportati o da archivi dati di origine supportati in Dynamics CRM o Dynamics 365 usando un'attività di copia in una pipeline di data factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: jingwang
ms.openlocfilehash: 18fdb14430eee97ff2780d963abf3e5ceafe1126
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71009388"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Copiare i dati da e in Dynamics 365 (Common Data Service) o Dynamics CRM usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Microsoft Dynamics 365 o Microsoft Dynamics CRM. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta informazioni generali sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da Dynamics 365 (Common Data Service) o Dynamics CRM in qualsiasi archivio dati di sink supportato. È anche possibile copiare dati da qualsiasi archivio dati di origine supportato a Dynamics 365 (Common Data Service) o Dynamics CRM. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Questo connettore Dynamics supporta Dynamics versione 7. x a 9. x sia in linea che in locale. Più in particolare,

- La versione 7. x è mappata a Dynamics CRM 2015
- La versione 8. x è mappata a Dynamics CRM 2016 e alla prima versione di Dynamics 365
- La versione 9. x è mappata alla versione più recente di Dynamics 365

Fare riferimento alla seguente tabella sui tipi di autenticazione supportati e sulle configurazioni per le rispettive versioni/prodotti Dynamics. IFD è l'acronimo di Internet Facing Deployment (distribuzione con connessione Internet).

| Versioni di Dynamics | Tipi di autenticazione | Esempi di servizi collegati |
|:--- |:--- |:--- |
| Dynamics 365 Online <br> Dynamics CRM Online | Office365 | [Dynamics online + autenticazione di Office365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 locale con IFD <br> Dynamics CRM 2016 locale con IFD <br> Dynamics CRM 2015 locale con IFD | IFD | [Dynamics locale con IFD + autenticazione IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Per Dynamics 365 in particolare, sono supportati i tipi di applicazioni seguenti:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Altri tipi di applicazioni, ad esempio Finance and Operations, Talent e così via non sono supportati da questo connettore.

Questo connettore Dynamics si basa sugli strumenti di [Dynamics XRM](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Per copiare dati da **Dynamics 365 for Finance and Operations**, è possibile usare il [connettore di Dynamics AX](connector-dynamics-ax.md).

## <a name="get-started"></a>Attività iniziali

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per Dynamics.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Dynamics sono supportate le proprietà seguenti.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 e Dynamics CRM Online

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **Dynamics**. | Yes |
| deploymentType | Il tipo di distribuzione dell'istanza di Dynamics. Deve essere **"Online"** per Dynamics online. | Sì |
| serviceUri | URL del servizio dell'istanza di Dynamics, ad esempio `https://adfdynamics.crm.dynamics.com`. | Sì |
| authenticationType | Il tipo di autenticazione per la connessione a un server Dynamics. Specificare **"Office365"** per Dynamics online. | Yes |
| userName | Specificare il nome utente per la connessione a Dynamics. | Sì |
| password | Specificare la password dell'account utente specificato per il nome utente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No per l'origine, Sì per il sink se il servizio collegato all'origine non dispone di un runtime di integrazione |

>[!NOTE]
>Connettore di Dynamics che permette di usare la proprietà facoltativa "organizationName" per identificare l'istanza di Dynamics CRM/365 Online. Durante l'esecuzione, viene consigliato di specificare invece la nuova proprietà "serviceUri" per ottenere prestazioni migliori per l'individuazione dell'istanza.

**Esempio: Dynamics Online con l'autenticazione di Office365**

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "description": "Dynamics online linked service using Office365 authentication",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://adfdynamics.crm.dynamics.com",
            "authenticationType": "Office365",
            "username": "test@contoso.onmicrosoft.com",
            "password": {
                "type": "SecureString",
                "value": "<password>"
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

*Proprietà aggiuntive rispetto a Dynamics online sono "hostName" e "port".*

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **Dynamics**. | Yes |
| deploymentType | Il tipo di distribuzione dell'istanza di Dynamics. Deve essere **"OnPremisesWithIfd"** per Dynamics locale con IFD.| Yes |
| hostName | Nome host del server Dynamics locale. | Sì |
| port | Porta del server Dynamics locale. | No, il valore predefinito è 443 |
| organizationName | Il nome organizzazione dell'istanza di Dynamics. | Sì |
| authenticationType | Tipo di autenticazione per la connessione al server Dynamics. Specificare **"Ifd"** per Dynamics locale con IFD. | Yes |
| userName | Specificare il nome utente per la connessione a Dynamics. | Yes |
| password | Specificare la password dell'account utente specificato per il nome utente. È possibile scegliere di contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory o archiviare la password in Azure Key Vault e consentire all'attività di copia di eseguire il pull da tale posizione durante l'esecuzione della copia dei dati. Per altre informazioni, vedere [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No per l'origine, Sì per il sink |

**Esempio: Dynamics locale con IFD e autenticazione IFD**

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
                "type": "SecureString",
                "value": "<password>"
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Dynamics.

Per copiare dati da e in Dynamics, impostare la proprietà type del set di dati su **DynamicsEntity**. Sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **DynamicsEntity**. |Sì |
| entityName | Il nome logico dell'entità da recuperare. | No per l'origine (se nell'origine dell'attività è specificato "query"), Sì per il sink |

> [!IMPORTANT]
>- Quando si copiano dati da Dynamics, la sezione "Structure" è facoltativa ma altamente riordinata nel set di dati di Dynamics per garantire un risultato di copia deterministica. Definisce il nome di colonna e il tipo di dati per i dati di Dynamics da copiare. Per altre informazioni, vedere [Struttura del set di dati](concepts-datasets-linked-services.md#dataset-structure-or-schema) e [Mapping dei tipi di dati per Dynamics](#data-type-mapping-for-dynamics).
>- Durante l'importazione dello schema nell'interfaccia utente di creazione, Azure Data Factory deduce lo schema tramite il campionamento delle prime righe dal risultato della query Dynamics per inizializzare la costruzione della struttura, nel qual caso le colonne senza valori verranno omesse. Lo stesso comportamento si applica alle esecuzioni di copia se non esiste alcuna definizione esplicita della struttura. È possibile rivedere e aggiungere altre colonne nello schema/nella struttura del set di dati Dynamics, come necessario, che verranno rispettate durante il runtime di copia.
>- Quando si copiano dati in Dynamics, la sezione "structure" è facoltativa nel set di dati di Dynamics. Le colonne in cui eseguire la copia sono determinate dallo schema dei dati di origine. Se l'origine è un file CSV senza intestazione, nel set di dati di input specificare "structure" con il nome della colonna e il tipo di dati. Le colonne vengono mappate ai campi nel file CSV una alla volta in ordine.

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
        "typeProperties": {
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dai tipi di origine e di sink Dynamics.

### <a name="dynamics-as-a-source-type"></a>Dynamics come tipo di origine

Per copiare dati da Dynamics, impostare il tipo di origine nell'attività di copia su **DynamicsSource**. Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **DynamicsSource**. | Sì |
| query | FetchXML è un linguaggio di query proprietario usato in Dynamics (online e locale). Vedere l'esempio seguente. Per altre informazioni, vedere [compilare query con un'FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | No (se nel set di dati è specificato "entityName") |

>[!NOTE]
>La colonna della chiave primaria verrà sempre copiata anche se la proiezione della colonna configurata nella query FetchXML non la contiene.

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

### <a name="dynamics-as-a-sink-type"></a>Dynamics come tipo di sink

Per copiare dati in Dynamics, impostare il tipo di sink nell'attività di copia su **DynamicsSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su **DynamicsSink**. | Sì |
| writebehavior | Comportamento dell'azione di scrittura dell'operazione.<br/>Il valore consentito è **"Upsert"** . | Sì |
| writeBatchSize | Conteggio delle righe di dati scritti da Dynamics in ogni batch. | No (il valore predefinito è 10) |
| ignoreNullValues | Indica se ignorare i valori null dai dati di input (tranne i campi chiave) durante un'operazione di scrittura.<br/>I valori consentiti sono **true** e **false**.<br>- **True**: i dati nell'oggetto di destinazione rimangono invariati quando si esegue un'operazione di upsert/aggiornamento. Inserire un valore predefinito definito quando si esegue un'operazione di inserimento.<br/>- **False**: i dati nell'oggetto di destinazione vengono aggiornati a NULL quando si esegue un'operazione di upsert/aggiornamento. Inserire un valore NULL quando si esegue un'operazione di inserimento. | No (il valore predefinito è false) |

>[!NOTE]
>Il valore predefinito di "**writeBatchSize**" del sink e di " **[parallelCopies](copy-activity-performance.md#parallel-copy)** " per l'attività di copia per il sink di Dynamics è in entrambi i casi 10. Pertanto, vengono inviati simultaneamente 100 record a Dynamics.

Per Dynamics 365 online, è previsto un limite di [2 chiamate di batch simultanee per ogni organizzazione](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Se tale limite viene superato, viene generato un errore "Server occupato" prima che venga eseguita la prima richiesta. Mantenendo "writeBatchSize" minore o uguale a 10 si evita la limitazione delle richieste di chiamate simultanee.

La combinazione ottimale di "**writeBatchSize**" e di "**parallelCopies**" dipende dallo schema dell'entità, ad esempio numero di colonne, le dimensioni delle righe, il numero di attività di plug-in/flussi di lavoro/flusso di lavoro collegate a tali chiamate e così via. L'impostazione predefinita 10 writeBatchSize * 10 parallelCopies è quella consigliata in base al servizio di Dynamics e che funzionerà per la maggior parte delle entità di Dynamics benché potrebbe non garantire prestazioni ottimali. È possibile ottimizzare le prestazioni regolando la combinazione nelle impostazioni di attività di copia.

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

Quando si copiano dati da Dynamics, vengono usati i mapping seguenti tra i tipi di dati di Dynamics e i tipi di dati provvisori di Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

Nella struttura del set di dati, configurare il tipo di dati corrispondente di Data Factory in base al tipo di dati di origine di Dynamics usando la tabella di mapping seguente.

| Tipo di dati di Dynamics | Tipo di dati provvisorio di Data Factory | Supportato come origine | Supportato come sink |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | Long | ✓ | ✓ |
| AttributeTypeCode.Boolean | Boolean | ✓ | ✓ |
| AttributeType.Customer | Guid | ✓ | |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | String | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | Guid | ✓ | ✓ (associata a destinazione singola) |
| AttributeType.ManagedProperty | Boolean | ✓ | |
| AttributeType.Memo | String | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | Guid | ✓ | |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | Guid | ✓ | ✓ |
| AttributeType.String | String | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> I tipi di dati di Dynamics AttributeType. CalendarRules, AttributeType. MultiSelectPicklist e AttributeType. party non sono supportati.

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
