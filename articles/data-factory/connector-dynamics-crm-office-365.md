---
title: Copiare i dati in Dynamics (Common Data Service)
description: Informazioni su come copiare dati da Microsoft Dynamics CRM o Microsoft Dynamics 365 (Common Data Service) in archivi dati di sink supportati o da archivi dati di origine supportati in Dynamics CRM o Dynamics 365 usando un'attività di copia in una pipeline di data factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 06/10/2020
ms.openlocfilehash: 54aac9fda42a867ab66d631279efbca4f812b01a
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497617"
---
# <a name="copy-data-from-and-to-dynamics-365-common-data-service-or-dynamics-crm-by-using-azure-data-factory"></a>Copiare i dati da e in Dynamics 365 (Common Data Service) o Dynamics CRM usando Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare un'attività di copia in Azure Data Factory per copiare dati da e in Microsoft Dynamics 365 e Microsoft Dynamics CRM. Si basa sull'articolo [Panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale di un'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine e sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da Dynamics 365 (Common Data Service) o Dynamics CRM in qualsiasi archivio dati di sink supportato. È anche possibile copiare dati da qualsiasi archivio dati di origine supportato a Dynamics 365 (Common Data Service) o Dynamics CRM. Per un elenco di archivi dati supportati da un'attività di copia come origini e sink, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats) .

Questo connettore Dynamics supporta le versioni di Dynamics da 7 a 9 sia per l'ambiente online che per l'ambiente locale. Più in particolare:

- La versione 7 esegue il mapping a Dynamics CRM 2015.
- La versione 8 esegue il mapping a Dynamics CRM 2016 e la versione precedente di Dynamics 365.
- La versione 9 è mappata alla versione più recente di Dynamics 365.

Fare riferimento alla seguente tabella dei tipi di autenticazione e delle configurazioni supportate per le versioni e i prodotti Dynamics.

| Versioni di Dynamics | Tipi di autenticazione | Esempi di servizi collegati |
|:--- |:--- |:--- |
| Common Data Service <br/><br/> Dynamics 365 Online <br/><br/> Dynamics CRM Online | Entità servizio Azure Active Directory (Azure AD) <br/><br/> Office 365 | [Autenticazione di Dynamics online e Azure AD Service-Principal o Office 365](#dynamics-365-and-dynamics-crm-online) |
| Dynamics 365 locale con distribuzione con connessione Internet (IFD) <br/><br/> Dynamics CRM 2016 locale con IFD <br/><br/> Dynamics CRM 2015 locale con IFD | IFD | [Dynamics locale con autenticazione IFD e IFD](#dynamics-365-and-dynamics-crm-on-premises-with-ifd) |

Per Dynamics 365 in particolare, sono supportati i tipi di applicazioni seguenti:

- Dynamics 365 for Sales
- Dynamics 365 for Customer Service
- Dynamics 365 for Field Service
- Dynamics 365 for Project Service Automation
- Dynamics 365 for Marketing

Questo connettore non supporta altri tipi di applicazioni, ad esempio finanza, operazioni e talento.

Questo connettore Dynamics si basa sugli strumenti di [Dynamics XRM](https://docs.microsoft.com/dynamics365/customer-engagement/developer/build-windows-client-applications-xrm-tools).

>[!TIP]
>Per copiare dati da Dynamics 365 Finance and Operations, è possibile usare il [connettore Dynamics AX](connector-dynamics-ax.md).

## <a name="prerequisites"></a>Prerequisiti

Per usare questo connettore con Azure AD l'autenticazione basata su entità servizio, è necessario configurare l'autenticazione da server a server (S2S) in Common Data Service o Dynamics. Per la procedura dettagliata, fare riferimento a [questo articolo](https://docs.microsoft.com/powerapps/developer/common-data-service/build-web-applications-server-server-s2s-authentication) .

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per Dynamics.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Dynamics sono supportate le proprietà seguenti.

### <a name="dynamics-365-and-dynamics-crm-online"></a>Dynamics 365 e Dynamics CRM Online

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type deve essere impostata su "Dynamics", "DynamicsCrm" o "CommonDataServiceForApps". | Sì |
| deploymentType | Il tipo di distribuzione dell'istanza di Dynamics. Il valore deve essere "online" per Dynamics online. | Sì |
| serviceUri | URL del servizio dell'istanza di Dynamics. Un esempio è https://www.crmdynamics.com. | Sì |
| authenticationType | Il tipo di autenticazione per la connessione a un server Dynamics. I valori validi sono "AADServicePrincipal" e "Office365". | Sì |
| servicePrincipalId | ID client dell'applicazione Azure AD. | Sì quando l'autenticazione è "AADServicePrincipal" |
| servicePrincipalCredentialType | Tipo di credenziale da usare per l'autenticazione dell'entità servizio. I valori validi sono "ServicePrincipalKey" e "ServicePrincipalCert". | Sì quando l'autenticazione è "AADServicePrincipal" |
| servicePrincipalCredential | Credenziale dell'entità servizio. <br/><br/>Quando si usa "ServicePrincipalKey" come tipo di credenziale, `servicePrincipalCredential` può essere una stringa che Azure Data Factory crittografata al momento della distribuzione del servizio collegato. In alternativa, può trattarsi di un riferimento a un segreto in Azure Key Vault. <br/><br/>Quando si usa "ServicePrincipalCert" come credenziale, `servicePrincipalCredential` deve essere un riferimento a un certificato in Azure Key Vault. | Sì quando l'autenticazione è "AADServicePrincipal" |
| username | Nome utente per la connessione a Dynamics. | Sì quando l'autenticazione è "Office365" |
| password | Password per l'account utente specificato come nome utente. Contrassegnare questo campo con "SecureString" per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato nel Azure Key Vault](store-credentials-in-key-vault.md). | Sì quando l'autenticazione è "Office365" |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Se non viene specificato alcun valore, la proprietà usa il runtime di integrazione di Azure predefinito. | No per l'origine e Sì per il sink se il servizio collegato di origine non dispone di un runtime di integrazione |

>[!NOTE]
>Dynamics Connector usava in precedenza la proprietà facoltativa **OrganizationName** per identificare l'istanza di Dynamics CRM o Dynamics 365 online. Sebbene la proprietà funzioni ancora, è consigliabile specificare invece la nuova proprietà **ServiceUri** per ottenere prestazioni migliori per l'individuazione dell'istanza.

#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-key-authentication"></a>Esempio: Dynamics online con Azure AD autenticazione basata su entità servizio e chiave

```json
{  
    "name": "DynamicsLinkedService",  
    "properties": {  
        "type": "Dynamics",  
        "typeProperties": {  
            "deploymentType": "Online",  
            "serviceUri": "https://www.crmdynamics.com",  
            "authenticationType": "AADServicePrincipal",  
            "servicePrincipalId": "<service principal id>",  
            "servicePrincipalCredentialType": "ServicePrincipalKey",  
            "servicePrincipalCredential": "<service principal key>"
        },  
        "connectVia": {  
            "referenceName": "<name of Integration Runtime>",  
            "type": "IntegrationRuntimeReference"  
        }  
    }  
}  
```
#### <a name="example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication"></a>Esempio: Dynamics online con Azure AD autenticazione basata su entità servizio e certificato

```json
{ 
    "name": "DynamicsLinkedService", 
    "properties": { 
        "type": "Dynamics", 
        "typeProperties": { 
            "deploymentType": "Online", 
            "serviceUri": "https://www.crmdynamics.com", 
            "authenticationType": "AADServicePrincipal", 
            "servicePrincipalId": "<service principal id>", 
            "servicePrincipalCredentialType": "ServicePrincipalCert", 
            "servicePrincipalCredential": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<AKV reference>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<certificate name in AKV>" 
            } 
        }, 
        "connectVia": { 
            "referenceName": "<name of Integration Runtime>", 
            "type": "IntegrationRuntimeReference" 
        } 
    } 
} 
```

#### <a name="example-dynamics-online-using-office-365-authentication"></a>Esempio: Dynamics online con l'autenticazione di Office 365

```json
{
    "name": "DynamicsLinkedService",
    "properties": {
        "type": "Dynamics",
        "typeProperties": {
            "deploymentType": "Online",
            "serviceUri": "https://www.crmdynamics.com",
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

Proprietà aggiuntive che vengono confrontate con Dynamics Online sono **nome host** e **porta**.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type deve essere impostata su "Dynamics", "DynamicsCrm" o "CommonDataServiceForApps". | Sì. |
| deploymentType | Il tipo di distribuzione dell'istanza di Dynamics. Il valore deve essere "OnPremisesWithIfd" per Dynamics locale con IFD.| Sì. |
| hostName | Nome host del server Dynamics locale. | Sì. |
| port | Porta del server Dynamics locale. | No. Il valore predefinito è 443. |
| organizationName | Il nome organizzazione dell'istanza di Dynamics. | Sì. |
| authenticationType | Tipo di autenticazione per la connessione al server Dynamics. Specificare "Ifd" per Dynamics locale con IFD. | Sì. |
| username | Nome utente per la connessione a Dynamics. | Sì. |
| password | Password per l'account utente specificato per il nome utente. È possibile contrassegnare questo campo con "SecureString" per archiviarlo in modo sicuro in Data Factory. In alternativa, è possibile archiviare una password in Key Vault e consentire all'attività di copia di effettuare il pull da tale posizione quando esegue la copia dei dati. Per altre informazioni, vedere [Archiviare le credenziali nell'insieme di credenziali delle chiavi](store-credentials-in-key-vault.md). | Sì. |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Se non viene specificato alcun valore, la proprietà usa il runtime di integrazione di Azure predefinito. | No per l'origine e Sì per il sink. |

#### <a name="example-dynamics-on-premises-with-ifd-using-ifd-authentication"></a>Esempio: Dynamics locale con IFD usando l'autenticazione IFD

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

Per copiare dati da e in Dynamics, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su "DynamicsEntity", "DynamicsCrmEntity" o "CommonDataServiceForAppsEntity". |Sì |
| entityName | Il nome logico dell'entità da recuperare. | No per l'origine se l'origine dell'attività è specificata come "query" e Sì per il sink |

#### <a name="example"></a>Esempio

```json
{
    "name": "DynamicsDataset",
    "properties": {
        "type": "DynamicsEntity",
        "schema": [],
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

Per copiare dati da Dynamics, la sezione **origine** dell'attività di copia supporta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type dell'origine dell'attività di copia deve essere impostata su "DynamicsSource", "DynamicsCrmSource" o "CommonDataServiceForAppsSource". | Sì |
| query | Un'FetchXML è un linguaggio di query proprietario usato in Dynamics online e in locale. Vedere l'esempio seguente. Per altre informazioni, vedere [compilare query con un'FetchXML](https://msdn.microsoft.com/library/gg328332.aspx). | No se `entityName` nel set di dati è specificato |

>[!NOTE]
>La colonna della chiave primaria verrà sempre copiata anche se la proiezione della colonna configurata nella query FetchXML non la contiene.

> [!IMPORTANT]
>- Quando si copiano dati da Dynamics, il mapping esplicito di colonne da Dynamics a sink è facoltativo. È tuttavia consigliabile eseguire il mapping per garantire un risultato di copia deterministica.
>- Quando Data Factory importa uno schema nell'interfaccia utente di creazione, deduce lo schema. Esegue il campionamento delle prime righe del risultato della query Dynamics per inizializzare l'elenco delle colonne di origine. In tal caso, le colonne senza valori nelle prime righe vengono omesse. Lo stesso comportamento si applica alle esecuzioni di copia se non esiste alcun mapping esplicito. È possibile esaminare e aggiungere altre colonne nel mapping, che vengono rispettate durante la copia del runtime.

#### <a name="example"></a>Esempio

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

Per copiare i dati in Dynamics, la sezione **sink** dell'attività di copia supporta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del sink dell'attività di copia deve essere impostata su "DynamicsSink", "DynamicsCrmSink" o "CommonDataServiceForAppsSink". | Sì. |
| writeBehavior | Comportamento dell'azione di scrittura dell'operazione. Il valore deve essere "upsert". | Sì |
| alternateKeyName | Nome della chiave alternativa definito nell'entità per eseguire un Upsert. | No. |
| writeBatchSize | Conteggio delle righe di dati scritti da Dynamics in ogni batch. | No. Il valore predefinito è 10. |
| ignoreNullValues | Indica se ignorare i valori null dai dati di input diversi dai campi chiave durante un'operazione di scrittura.<br/><br/>I valori validi sono **true** e **false**:<ul><li>**True**: lasciare invariati i dati nell'oggetto di destinazione quando si esegue un'operazione Upsert o Update. Inserire un valore predefinito definito quando si esegue un'operazione di inserimento.</li><li>**False**: aggiornare i dati nell'oggetto di destinazione in un valore null quando si esegue un'operazione Upsert o Update. Inserire un valore null quando si esegue un'operazione di inserimento.</li></ul> | No. Il valore predefinito è **false**. |

>[!NOTE]
>Il valore predefinito sia per il sink **writeBatchSize** che per l'attività di copia **[parallelCopies](copy-activity-performance-features.md#parallel-copy)** per il sink Dynamics è 10. Pertanto, per impostazione predefinita, i record 100 vengono inviati simultaneamente a Dynamics.

Per Dynamics 365 online, è previsto un limite di [due chiamate batch simultanee per organizzazione](https://msdn.microsoft.com/library/jj863631.aspx#Run-time%20limitations). Se tale limite viene superato, viene generata un'eccezione "Server occupato" prima che venga mai eseguita la prima richiesta. Per evitare la limitazione delle chiamate simultanee, è necessario usare un numero di **writeBatchSize** pari o inferiore a 10.

La combinazione ottimale di **writeBatchSize** e **parallelCopies** dipende dallo schema dell'entità. Gli elementi dello schema includono il numero di colonne, le dimensioni delle righe e il numero di plug-in, flussi di lavoro o attività del flusso di lavoro collegati a tali chiamate. L'impostazione predefinita di **writeBatchSize** (10) &times; **parallelCopies** (10) è la raccomandazione in base al servizio Dynamics. Questo valore funziona per la maggior parte delle entità Dynamics, anche se potrebbe non offrire prestazioni ottimali. È possibile ottimizzare le prestazioni regolando la combinazione nelle impostazioni di attività di copia.

#### <a name="example"></a>Esempio

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

Quando si copiano dati da Dynamics, nella tabella seguente vengono illustrati i mapping dei tipi di dati di Dynamics a Data Factory tipi di dati provvisori. Per informazioni sul mapping di un'attività di copia a uno schema di origine e il mapping di un tipo di dati a un sink, vedere [mapping di schemi e tipi di dati](copy-activity-schema-and-type-mapping.md).

Configurare il tipo di dati Data Factory corrispondente in una struttura di DataSet basata sul tipo di dati di Dynamics di origine usando la tabella di mapping seguente:

| Tipo di dati di Dynamics | Tipo di dati provvisorio di Data Factory | Supportato come origine | Supportato come sink |
|:--- |:--- |:--- |:--- |
| AttributeTypeCode.BigInt | long | ✓ | ✓ |
| AttributeTypeCode.Boolean | Booleano | ✓ | ✓ |
| AttributeType.Customer | GUID | ✓ | ✓ (Vedere le [linee guida](#writing-data-to-a-lookup-field)) |
| AttributeType.DateTime | Datetime | ✓ | ✓ |
| AttributeType.Decimal | Decimal | ✓ | ✓ |
| AttributeType.Double | Double | ✓ | ✓ |
| AttributeType.EntityName | Stringa | ✓ | ✓ |
| AttributeType.Integer | Int32 | ✓ | ✓ |
| AttributeType.Lookup | GUID | ✓ | ✓ (Vedere le [linee guida](#writing-data-to-a-lookup-field)) |
| AttributeType.ManagedProperty | Booleano | ✓ | |
| AttributeType.Memo | Stringa | ✓ | ✓ |
| AttributeType.Money | Decimal | ✓ | ✓ |
| AttributeType.Owner | GUID | ✓ | ✓ (Vedere le [linee guida](#writing-data-to-a-lookup-field)) |
| AttributeType.Picklist | Int32 | ✓ | ✓ |
| AttributeType.Uniqueidentifier | GUID | ✓ | ✓ |
| AttributeType.String | Stringa | ✓ | ✓ |
| AttributeType.State | Int32 | ✓ | ✓ |
| AttributeType.Status | Int32 | ✓ | ✓ |

> [!NOTE]
> I tipi di dati di Dynamics **attributeType. CalendarRules**, **attributeType. MultiSelectPicklist**e **attributeType. partity** non sono supportati.

## <a name="writing-data-to-a-lookup-field"></a>Scrittura di dati in un campo di ricerca

Per scrivere i dati in un campo di ricerca con più destinazioni come Customer e Owner, seguire questa guida ed esempio:

1. Fare in modo che l'origine contenga sia il valore del campo che il nome dell'entità di destinazione corrispondente.
   - Se tutti i record vengono mappati alla stessa entità di destinazione, verificare una delle condizioni seguenti:
      - I dati di origine hanno una colonna in cui è archiviato il nome dell'entità di destinazione.
      - È stata aggiunta una colonna aggiuntiva nell'origine dell'attività di copia per definire l'entità di destinazione.
   - Se i record diversi vengono mappati a entità di destinazione diverse, assicurarsi che i dati di origine includano una colonna in cui è archiviato il nome dell'entità di destinazione corrispondente.

1. Eseguire il mapping delle colonne valore e riferimento entità dall'origine al sink. È necessario eseguire il mapping della colonna entità-riferimento a una colonna virtuale con il modello di denominazione speciale `{lookup_field_name}@EntityReference` . La colonna non esiste effettivamente in Dynamics. Viene usato per indicare che questa colonna è la colonna di metadati del campo di ricerca multitarget specificato.

Si supponga, ad esempio, che l'origine includa le due colonne seguenti:

- Colonna **CustomerField** di tipo **GUID**, che rappresenta il valore di chiave primaria dell'entità di destinazione in Dynamics.
- Colonna di **destinazione** di tipo **String**, ovvero il nome logico dell'entità di destinazione.

Si supponga inoltre di voler copiare tali dati nel campo dell'entità di Dynamics sink **CustomerField** del tipo **Customer**.

Nel mapping delle colonne di copia-attività eseguire il mapping delle due colonne nel modo seguente:

- Da **CustomerField** a **CustomerField**. Questo mapping è il normale mapping dei campi.
- **Destinazione** per **CustomerField \@ EntityReference**. La colonna sink è una colonna virtuale che rappresenta il riferimento all'entità. Immettere i nomi dei campi in un mapping, perché non vengono visualizzati importando gli schemi.

![Ricerca dinamica-mapping colonna campo](./media/connector-dynamics-crm-office-365/connector-dynamics-lookup-field-column-mapping.png)

Se tutti i record di origine sono mappati alla stessa entità di destinazione e i dati di origine non contengono il nome dell'entità di destinazione, di seguito è riportato un collegamento: nell'origine dell'attività di copia aggiungere una colonna aggiuntiva. Assegnare un nome alla nuova colonna usando il criterio `{lookup_field_name}@EntityReference` , impostare il valore sul nome dell'entità di destinazione, quindi procedere con il mapping delle colonne come di consueto. Se i nomi delle colonne di origine e sink sono identici, è anche possibile ignorare il mapping esplicito della colonna perché l'attività di copia esegue il mapping delle colonne in base al nome.

![Ricerca dinamica-campo aggiunta di una colonna di riferimento entità](./media/connector-dynamics-crm-office-365/connector-dynamics-add-entity-reference-column.png)

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per informazioni dettagliate sulle proprietà, vedere [attività Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di archivi dati, l'attività di copia in Data Factory supporta come origini e sink, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
