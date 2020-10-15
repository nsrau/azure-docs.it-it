---
title: Copiare dati da Elenco di SharePoint Online usando Azure Data Factory
description: Informazioni su come copiare dati da Elenco di SharePoint Online in archivi dati sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: jingwang
ms.openlocfilehash: f560a01c4ec00649157a9c43aedf0ed6cfc2e050
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "83871924"
---
# <a name="copy-data-from-sharepoint-online-list-by-using-azure-data-factory"></a>Copiare dati da Elenco di SharePoint Online usando Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo descrive come usare l'attività di copia in Azure Data Factory per copiare dati da Elenco di SharePoint Online. L'articolo è basato su [Attività di copia in Azure Data Factory](copy-activity-overview.md), dove viene presentata una panoramica generale dell'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Il connettore Elenco di SharePoint Online è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da Elenco di SharePoint Online a qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati dall'attività di copia come origini e sink, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Elenco di SharePoint Online usa l'autenticazione basata su entità servizio e recupera i dati tramite il protocollo OData.

> [!TIP]
> Questo connettore supporta la copia di dati da **Elenco** di SharePoint Online ma non da un file. Informazioni su come copiare file dalla sezione [Copiare file da SharePoint Online](#copy-file-from-sharepoint-online).

## <a name="prerequisites"></a>Prerequisiti

Il connettore Elenco di SharePoint Online usa l'autenticazione basata su entità servizio per connettersi a SharePoint. Seguire questi passaggi per configurarlo:

1. Registrare un'entità applicazione in Azure Active Directory (Azure AD) seguendo le indicazioni fornite in [Registrare l'applicazione con un tenant di Azure AD](../storage/common/storage-auth-aad-app.md#register-your-application-with-an-azure-ad-tenant). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. Concedere all'applicazione registrata l'autorizzazione a livello di sito per SharePoint Online: 

    > [!NOTE]
    > Questa operazione richiede l'autorizzazione del proprietario del sito di SharePoint Online. È possibile trovare il proprietario visitando la home page del sito -> fare clic su "Membri di X" nell'angolo destro -> verificare chi ha il ruolo di "Proprietario".

    1. Aprire il collegamento di sito di SharePoint Online, ad esempio `https://[your_site_url]/_layouts/15/appinv.aspx` (sostituire l'URL del sito).
    2. Cercare l'ID applicazione registrato, compilare i campi vuoti e fare clic su "Crea".

        - Dominio applicazione: `localhost.com`
        - URL di reindirizzamento: `https://www.localhost.com`
        - XML richiesta autorizzazione:

        ```xml
        <AppPermissionRequests AllowAppOnlyPolicy="true">
            <AppPermissionRequest Scope="http://sharepoint/content/sitecollection/web" Right="Read"/>
        </AppPermissionRequests>
        ```

        ![autorizzazione concessione SharePoint](media/connector-sharepoint-online-list/sharepoint-online-grant-permission.png)

    3. Fare clic su "Attendibile" per l'app.

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti offrono informazioni dettagliate sulle proprietà che è possibile usare per definire entità di Data Factory specifiche del connettore Elenco di SharePoint Online.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Elenco di SharePoint Online sono supportate le proprietà seguenti:

| **Proprietà**        | **Descrizione**                                              | **Obbligatorio** |
| ------------------- | ------------------------------------------------------------ | ------------ |
| type                | La proprietà type deve essere impostata su:  **SharePointOnlineList**.  | Sì          |
| siteUrl             | URL del sito di SharePoint Online, ad esempio `https://contoso.sharepoint.com/sites/siteName`. | Sì          |
| servicePrincipalId  | ID applicazione (client) dell'applicazione registrata in Azure Active Directory. | Sì          |
| servicePrincipalKey | Chiave dell'applicazione. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì          |
| TenantId            | ID del tenant in cui risiede l'applicazione.          | Sì          |
| connectVia          | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites) più indietro in questo articolo. Se questa proprietà non è specificata, viene usato il tipo Azure Integration Runtime predefinito. | No           |

**Esempio:**

```json
{
    "name": "SharePointOnlineList",
    "properties": {
        "type": "SharePointOnlineList",
        "typeProperties": {
            "siteUrl": "<site URL>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenantId": "<tenant ID>"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere [Set di dati e servizi collegati](concepts-datasets-linked-services.md). La sezione seguente presenta un elenco delle proprietà supportate dal set di dati della tabella SAP.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del set di dati deve essere impostata su **SharePointOnlineLResource**. | Sì |
| listName | Nome di Elenco di SharePoint Online. | Sì |

**Esempio**

```json
{
    "name": "SharePointOnlineListDataset",
    "properties":
    {
        "type": "SharePointOnlineListResource",
        "linkedServiceName": {
            "referenceName": "<SharePoint Online List linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties":
        {
            "listName": "<name of the list>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Pipeline](concepts-pipelines-activities.md).  Nella sezione seguente viene fornito un elenco delle proprietà supportate dall'origine Elenco di SharePoint Online.

### <a name="sharepoint-online-list-as-source"></a>Elenco di SharePoint Online come origine

Per copiare i dati da Elenco di SharePoint Online, nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** dell'origine dell'attività di copia deve essere impostata su **SharePointOnlineListSource**. | Sì |
| query | Opzioni di query OData personalizzate per filtrare i dati. Esempio: `"$top=10&$select=Title,Number"`. | No |
| httpRequestTimeout | Il timeout (in secondi) durante il quale la richiesta HTTP attende una risposta. Il valore predefinito è 300 (5 minuti). | No |

**Esempio**

```json
"activities":[
    {
        "name": "CopyFromSharePointOnlineList",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SharePoint Online List input dataset name>",
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
            "source": {
                "type": "SharePointOnlineListSource",
                "query": "<OData query e.g. $top=10&$select=Title,Number>"
            }, 
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sharepoint-online-list"></a>Mapping dei tipi di dati per Elenco di SharePoint Online

Quando si copiano dati da Elenco di SharePoint Online, vengono usati i mapping seguenti tra i tipi di dati di Elenco di SharePoint Online e i tipi di dati provvisori di Azure Data Factory. 

| **Tipo di dati di SharePoint Online**                 | **Tipo di dati di OData**                                  | **Tipo di dati provvisorio di Azure Data Factory** |
| ----------------------------------------------- | ---------------------------------------------------- | ---------------------------------------- |
| Riga di testo singola                             | Edm.String                                           | string                                   |
| Più righe di testo                          | Edm.String                                           | string                                   |
| Scelta (menu da cui scegliere)                    | Edm.String                                           | string                                   |
| Numero (1, 1.0, 100)                            | Edm.Double                                           | Double                                   |
| Valuta ($, ¥, €)                              | Edm.Double                                           | Double                                   |
| Data e ora                                   | Edm.DateTime                                         | Datetime                                 |
| Ricerca (informazioni già presenti nel sito)       | Edm.Int32                                            | Int32                                    |
| Sì/No (casella di controllo)                              | Edm.Boolean                                          | Boolean                                  |
| Persona o gruppo                                 | Edm.Int32                                            | Int32                                    |
| Collegamento ipertestuale o immagine                            | Edm.String                                           | string                                   |
| Calcolato (calcolo basato su altre colonne) | Edm.String / Edm.Double / Edm.DateTime / Edm.Boolean | String / Double / DateTime / Boolean     |
| Attachment                                      | Non supportate                                        |                                          |
| Risultato dell'attività                                    | Non supportate                                        |                                          |
| Dati esterni                                   | Non supportate                                        |                                          |
| Metadati gestiti                                | Non supportate                                        |                                          |

## <a name="copy-file-from-sharepoint-online"></a>Copia file da SharePoint Online

È possibile copiare i file da SharePoint Online usando **attività Web** per autenticare e recuperare il token di accesso da SharePoint Online, quindi passare alla successiva **attività di copia** per copiare i dati con **connettore HTTP come origine**.

![flusso file copia sharepoint](media/connector-sharepoint-online-list/sharepoint-online-copy-file-flow.png)

1. Seguire la sezione [Prerequisiti](#prerequisites) per creare un'applicazione AAD e concedere l'autorizzazione a SharePoint Online. 

2. Creare un'**attività Web** per ottenere il token di accesso da SharePoint Online:

    - **URL**: `https://accounts.accesscontrol.windows.net/[Tenant-ID]/tokens/OAuth/2`. Sostituire l'ID tenant.
    - **Metodo**: POST
    - **Intestazioni**:
        - Content-Type: application/x-www-form-urlencoded
    - **Corpo**: `grant_type=client_credentials&client_id=[Client-ID]@[Tenant-ID]&client_secret=[Client-Secret]&resource=00000003-0000-0ff1-ce00-000000000000/[Tenant-Name].sharepoint.com@[Tenant-ID]`. Sostituire l'ID client, il segreto client, l'ID tenant e il nome del tenant.

    > [!CAUTION]
    > Impostare l'opzione Output sicuro su true nell'attività Web per impedire che il valore del token venga registrato come testo normale. In qualsiasi ulteriore attività in cui viene usato questo valore, l'opzione Input sicuro deve essere impostata su true.

3. Catena con un'**attività di copia** con connettore HTTP come origine per copiare il contenuto dei file di SharePoint Online:

    - Servizio collegato HTTP:
        - **URL di base**: `https://[site-url]/_api/web/GetFileByServerRelativeUrl('[relative-path-to-file]')/$value`. Sostituire l'URL del sito e il percorso relativo del file. Percorso relativo di esempio del file come `/sites/site2/Shared Documents/TestBook.xlsx`.
        - **Tipo di autenticazione:** Anonimo *(per usare il token di connessione configurato nell'origine dell'attività di copia in un secondo momento)*
    - Set di dati: scegliere il formato desiderato. Per copiare il file così com'è, selezionare il tipo "Binary".
    - Origine dell'attività di copia:
        - **Metodo di richiesta**: GET
        - **Intestazione aggiuntiva**: usare l'espressione seguente `@{concat('Authorization: Bearer ', activity('<Web-activity-name>').output.access_token)}`, che impiega il token di connessione generato dall'attività Web upstream come intestazione di autorizzazione. Sostituire il nome dell'attività Web.
    - Configurare il sink dell'attività di copia come di consueto.

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
