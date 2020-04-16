---
title: Copiare dati da un'origine REST usando Azure Data FactoryCopy data from a REST source by using Azure Data Factory
description: Informazioni su come copiare dati da un'origine REST locale o cloud in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: jingwang
ms.openlocfilehash: 2657f1998e3ca908bc52166154ac3353e1e5a66b
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415036"
---
# <a name="copy-data-from-a-rest-endpoint-by-using-azure-data-factory"></a>Copiare dati da un endpoint REST tramite Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo descrive come usare l'attività di copia in Azure Data Factory per copiare dati da un endpoint REST. L'articolo è basato su [Attività di copia in Azure Data Factory](copy-activity-overview.md), dove viene presentata una panoramica generale dell'attività di copia.

La differenza tra questo connettore REST, il [connettore HTTP](connector-http.md) e il [connettore Tabella Web](connector-web-table.md) è la seguente:

- **Il connettore REST** supporta in modo specifico la copia dei dati dalle API RESTful; 
- Il **connettore HTTP** è un connettore generico per recuperare i dati da qualsiasi endpoint HTTP, ad esempio per scaricare file. Prima che il connettore REST diventi disponibile, può capitare di usare il connettore HTTP per copiare dati dall'API RESTful, operazione supportata ma meno funzionale rispetto all'uso del connettore REST.
- Il **connettore Tabella Web** estrae il contenuto della tabella da una pagina Web HTML.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un'origine REST in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati dall'attività di copia come origini e sink, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, questo connettore REST generico supporta:

- Il recupero dei dati da un endpoint REST tramite il metodo **GET** o **POST**.
- Recupero dei dati utilizzando una delle seguenti autenticazioni: **Anonymous**, **Basic**, **AAD service principal**e **managed identities for Azure resources**.
- La **[paginazione](#pagination-support)** nelle API REST.
- La copia della risposta JSON REST [così com'è](#export-json-response-as-is) o la relativa analisi tramite [mapping dello schema](copy-activity-schema-and-type-mapping.md#schema-mapping). È supportato solo il payload della risposta in **JSON**.

> [!TIP]
> Per testare una richiesta di recupero dei dati prima di configurare il connettore REST in Data Factory, fare riferimento alla specifica dell'API per i requisiti relativi a intestazione e corpo. È possibile usare strumenti come Postman o un Web browser per la convalida.

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti offrono informazioni dettagliate sulle proprietà che è possibile usare per definire entità di Data Factory specifiche del connettore REST.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato REST sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **RestService**. | Sì |
| url | URL di base del servizio REST. | Sì |
| enableServerCertificateValidation | Se convalidare il certificato TLS/SSL lato server durante la connessione all'endpoint. | No<br /> (il valore predefinito è **true**) |
| authenticationType | Tipo di autenticazione usato per connettersi al servizio REST. I valori consentiti sono **Anonymous**, **Basic**, **AadServicePrincipal** e **ManagedServiceIdentity**. Per altre proprietà ed esempi su ogni valore, vedere le sezioni corrispondenti di seguito. | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Per ulteriori informazioni, vedere la sezione [Prerequisiti.](#prerequisites) Se non è specificata, questa proprietà usa il tipo Azure Integration Runtime predefinito. |No |

### <a name="use-basic-authentication"></a>Usare l'autenticazione di base

Impostare la proprietà **authenticationType** su **Basic**. Oltre alle proprietà generiche descritte nella sezione precedente, specificare le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| userName | Nome utente da usare per accedere all'endpoint REST. | Sì |
| password | Password per l'utente (valore di **userName**). Contrassegnare questo campo come di tipo **SecureString** per l'archiviazione sicura in Data Factory. È anche possibile [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |

**Esempio**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<REST endpoint>",
            "userName": "<user name>",
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

### <a name="use-aad-service-principal-authentication"></a>Usare l'autenticazione basata sull'entità servizio AAD

Impostare la proprietà **authenticationType** su **AadServicePrincipal**. Oltre alle proprietà generiche descritte nella sezione precedente, specificare le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| servicePrincipalId | Specificare l'ID client. dell'applicazione Azure Active Directory. | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione Azure Active Directory. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. Recuperarle passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì |
| aadResourceId | Specificare la risorsa AAD per cui si sta richiedendo l'autorizzazione, ad esempio `https://management.core.windows.net`.| Sì |

**Esempio**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "AadServicePrincipal",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="use-managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a> Usare le entità gestite per l'autenticazione delle risorse di Azure

Impostare la proprietà **authenticationType** su **ManagedServiceIdentity**. Oltre alle proprietà generiche descritte nella sezione precedente, specificare le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| aadResourceId | Specificare la risorsa AAD per cui si sta richiedendo l'autorizzazione, ad esempio `https://management.core.windows.net`.| Sì |

**Esempio**

```json
{
    "name": "RESTLinkedService",
    "properties": {
        "type": "RestService",
        "typeProperties": {
            "url": "<REST endpoint e.g. https://www.example.com/>",
            "authenticationType": "ManagedServiceIdentity",
            "aadResourceId": "<AAD resource URL e.g. https://management.core.windows.net>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Questa sezione contiene un elenco delle proprietà supportate dal set di dati REST. 

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere [Set di dati e servizi collegati](concepts-datasets-linked-services.md). 

Per copiare dati da REST, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del set di dati deve essere impostata su **RestResource**. | Sì |
| relativeUrl | URL relativo della risorsa che contiene i dati. Quando questa proprietà non è specificata, viene usato solo l'URL indicato nella definizione del servizio collegato. Il connettore HTTP copia i `[URL specified in linked service]/[relative URL specified in dataset]`dati dall'URL combinato: . | No |

Se si `requestMethod`imposta `additionalHeaders` `requestBody` , `paginationRules` , e nel dataset, è ancora supportato così com'è, mentre si consiglia di utilizzare il nuovo modello nell'origine attività in futuro.

**Esempio:**

```json
{
    "name": "RESTDataset",
    "properties": {
        "type": "RestResource",
        "typeProperties": {
            "relativeUrl": "<relative url>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<REST linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Questa sezione contiene un elenco delle proprietà supportate dall'origine REST.

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Pipeline](concepts-pipelines-activities.md). 

### <a name="rest-as-source"></a>REST come origine

Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** dell'origine dell'attività di copia deve essere impostata su **RestSource**. | Sì |
| requestMethod | Metodo HTTP. I valori consentiti sono **Get** (predefinito) e **Post**. | No |
| additionalHeaders | Intestazioni richiesta HTTP aggiuntive. | No |
| requestBody | Corpo della richiesta HTTP. | No |
| paginationRules | Regole di paginazione per comporre le richieste di pagina successive. Per informazioni dettagliate, vedere la sezione [Supporto della paginazione](#pagination-support). | No |
| httpRequestTimeout | Timeout (valore di **TimeSpan**) durante il quale la richiesta HTTP attende una risposta. Si tratta del timeout per ottenere una risposta, non per leggere i dati della risposta. Il valore predefinito è **00:01:40**.  | No |
| requestInterval | Periodo di attesa prima di inviare la richiesta per la pagina successiva. Il valore predefinito è **00:00:01** |  No |

>[!NOTE]
>Il connettore REST ignora qualsiasi `additionalHeaders`intestazione "Accetta" specificata in . Poiché il connettore REST supporta solo la `Accept: application/json`risposta in JSON, genererà automaticamente un'intestazione di .

**Esempio 1: Utilizzo del metodo Get con impaginazioneExample 1: Using the Get method with pagination**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "additionalHeaders": {
                    "x-user-defined": "helloworld"
                },
                "paginationRules": {
                    "AbsoluteUrl": "$.paging.next"
                },
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Esempio 2: Uso del metodo POST**

```json
"activities":[
    {
        "name": "CopyFromREST",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<REST input dataset name>",
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
                "type": "RestSource",
                "requestMethod": "Post",
                "requestBody": "<body for POST REST request>",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="pagination-support"></a>Supporto della paginazione

In genere, l'API REST limita la dimensione del payload di risposta di una singola richiesta con un numero ragionevole. mentre per restituire grandi quantità di dati, suddivide il risultato in più pagine e richiede ai chiamanti di inviare richieste consecutive per ottenere la pagina successiva del risultato. In genere la richiesta di una sola pagina è dinamica ed è composta dalle informazioni restituite dalla risposta della pagina precedente.

Questo connettore REST generico supporta i modelli di paginazione seguenti: 

* URL assoluto o relativo della richiesta successiva: valore della proprietà nel corpo della risposta corrente
* URL assoluto o relativo della richiesta successiva: valore dell'intestazione nelle intestazioni di risposta correnti
* Parametro di query della richiesta successiva = valore della proprietà nel corpo della risposta corrente
* Parametro di query della richiesta successiva = valore dell'intestazione nelle intestazioni della risposta corrente
* Intestazione della richiesta successiva = valore della proprietà nel corpo della risposta corrente
* Intestazione della richiesta successiva = valore dell'intestazione nelle intestazioni della risposta corrente

Le regole di **impaginazione** sono definite come dizionario nel set di dati che contiene una o più coppie chiave-valore con distinzione tra maiuscole e minuscole. La configurazione verrà usata per generare la richiesta a partire dalla seconda pagina. Il connettore interromperà l'iterazione quando ottiene il codice di stato HTTP 204 (Nessun contenuto) o una qualsiasi delle espressioni JSONPath in "paginationRules" restituisce null.

**Chiavi supportate** nelle regole di paginazione:

| Chiave | Descrizione |
|:--- |:--- |
| AbsoluteUrl | Indica l'URL per l'invio della richiesta successiva. Può essere **un URL assoluto o relativo.** |
| QueryParameters.*parametro_query_richiesta* o QueryParameters['parametro_query_richiesta'] | Il valore di "parametro_query_richiesta" è definito dall'utente e fa riferimento al nome di un parametro di query nell'URL della richiesta HTTP successiva. |
| Headers.*intestazione_richiesta* o Headers['intestazione_richiesta'] | Il valore di "intestazione_richiesta" è definito dall'utente e fa riferimento a un nome di intestazione nella richiesta HTTP successiva. |

**Valori supportati** nelle regole di paginazione:

| valore | Descrizione |
|:--- |:--- |
| Headers.*intestazione_risposta* o Headers['intestazione_risposta'] | Il valore di "intestazione_risposta" è definito dall'utente e fa riferimento a un nome di intestazione nella risposta HTTP corrente, il cui valore verrà usato per inviare la richiesta successiva. |
| Espressione JSONPath che inizia con "$" (che rappresenta la radice del corpo della risposta) | Il corpo della risposta deve contenere un solo oggetto JSON. L'espressione JSONPath deve restituire un singolo valore primitivo, che verrà usato per inviare la richiesta successiva. |

**Esempio:**

L'API Viso di Facebook restituisce la risposta nella struttura seguente, nel qual caso l'URL della pagina successiva è rappresentato in ***paging.next***:

```json
{
    "data": [
        {
            "created_time": "2017-12-12T14:12:20+0000",
            "name": "album1",
            "id": "1809938745705498_1809939942372045"
        },
        {
            "created_time": "2017-12-12T14:14:03+0000",
            "name": "album2",
            "id": "1809938745705498_1809941802371859"
        },
        {
            "created_time": "2017-12-12T14:14:11+0000",
            "name": "album3",
            "id": "1809938745705498_1809941879038518"
        }
    ],
    "paging": {
        "cursors": {
            "after": "MTAxNTExOTQ1MjAwNzI5NDE=",
            "before": "NDMyNzQyODI3OTQw"
        },
        "previous": "https://graph.facebook.com/me/albums?limit=25&before=NDMyNzQyODI3OTQw",
        "next": "https://graph.facebook.com/me/albums?limit=25&after=MTAxNTExOTQ1MjAwNzI5NDE="
    }
}
```

La configurazione di origine dell'attività di copia REST corrispondente, in particolare è `paginationRules` la seguente:

```json
"typeProperties": {
    "source": {
        "type": "RestSource",
        "paginationRules": {
            "AbsoluteUrl": "$.paging.next"
        },
        ...
    },
    "sink": {
        "type": "<sink type>"
    }
}
```

## <a name="use-oauth"></a>Usare OAuth
Questa sezione descrive come usare un modello di soluzione per copiare i dati dal connettore REST in Archiviazione di Azure Data Lake in formato JSON usando OAuth.This section describes how to use a solution template to copy data from REST connector into Azure Data Lake Storage in JSON format using OAuth. 

### <a name="about-the-solution-template"></a>Informazioni sul modello di soluzione

Il modello contiene due attività:
- **L'attività Web** recupera il token di connessione e quindi lo passa all'attività Copy successiva come autorizzazione.
- **L'attività di copia** copia i dati da REST ad Archiviazione dati di Azure.Copy activity copies data from REST to Azure Data Lake Storage.

Il modello definisce due parametri:
- **SinkContainer** è il percorso della cartella radice in cui vengono copiati i dati in Archiviazione dati di Azure.SinkContainer is the root folder path where the data are copied to in your Azure Data Lake Storage. 
- **SinkDirectory** è il percorso della directory nella radice in cui vengono copiati i dati in Archiviazione dati di Azure.SinkDirectory is the directory path under the root where the data are copied to in your Azure Data Lake Storage. 

### <a name="how-to-use-this-solution-template"></a>Come usare questo modello di soluzione

1. Passare al modello **Copia da REST o HTTP usando OAuth.** Creare una nuova connessione per Connessione origine. 
    ![Creare nuove connessioni](media/solution-template-copy-from-rest-or-http-using-oauth/source-connection.png)

    Di seguito sono riportati i passaggi chiave per le nuove impostazioni del servizio collegato (REST):Below are key steps for new linked service (REST) settings:
    
     1. In **URL di base**specificare il parametro url per il proprio servizio REST di origine. 
     2. Per **Tipo di autenticazione**scegliere *Anonimo*.
        ![Nuova connessione REST](media/solution-template-copy-from-rest-or-http-using-oauth/new-rest-connection.png)

2. Creare una nuova connessione per Connessione di destinazione.  
    ![Nuova connessione Gen2](media/solution-template-copy-from-rest-or-http-using-oauth/destination-connection.png)

3. Selezionare **Usa questo modello**.
    ![Usa questo modello](media/solution-template-copy-from-rest-or-http-using-oauth/use-this-template.png)

4. Si vedrebbe la pipeline creata come ![illustrato nell'esempio seguente: Pipeline](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline.png)

5. Selezionare Attività **Web.** In **Impostazioni**specificare **l'URL**, **il metodo**, le **intestazioni**e il **corpo** corrispondenti per recuperare il token di connessione OAuth dall'API di accesso del servizio da cui si desidera copiare i dati. Il segnaposto nel modello mostra un esempio di OAuth di Azure Active Directory (AAD). Nota: L'autenticazione AAD è supportata in modo nativo dal connettore REST, qui è solo un esempio per il flusso OAuth. 

    | Proprietà | Descrizione |
    |:--- |:--- |:--- |
    | URL |Specificare l'URL da cui recuperare il token di connessione OAuth. ad esempio nel campione qui èhttps://login.microsoftonline.com/microsoft.onmicrosoft.com/oauth2/token |. 
    | Metodo | Metodo HTTP. I valori consentiti sono **Post** e **Get**. | 
    | Headers | Intestazione è definita dall'utente, che fa riferimento a un nome di intestazione nella richiesta HTTP. | 
    | Corpo | Corpo della richiesta HTTP. | 

    ![Pipeline](media/solution-template-copy-from-rest-or-http-using-oauth/web-settings.png)

6. In Copia attività **dati** selezionare la scheda *Origine,* è possibile vedere che il token di connessione (access_token) recuperato dal passaggio precedente verrebbe passato a Copia attività dati come **autorizzazione** in Intestazioni aggiuntive. Verificare le impostazioni per le proprietà seguenti prima di avviare un'esecuzione della pipeline.

    | Proprietà | Descrizione |
    |:--- |:--- |:--- | 
    | Metodo richiesta | Metodo HTTP. I valori consentiti sono **Get** (predefinito) e **Post**. | 
    | Intestazioni aggiuntive | Intestazioni richiesta HTTP aggiuntive.| 

   ![Copia autenticazione di origine](media/solution-template-copy-from-rest-or-http-using-oauth/copy-data-settings.png)

7. Selezionare **Debug**, immettere i **parametri**, quindi scegliere **Fine**.
   ![Esecuzione della pipeline](media/solution-template-copy-from-rest-or-http-using-oauth/pipeline-run.png) 

8. Quando l'esecuzione della pipeline viene completata correttamente, ![il risultato viene visualizzato in modo simile all'esempio seguente: Risultato dell'esecuzione della pipelineWhen the pipeline run completes successfully, you would see the result similar to the following example: Pipeline run result](media/solution-template-copy-from-rest-or-http-using-oauth/run-result.png) 

9. Fare clic sull'icona "Output" di WebActivity nella colonna **Azioni,** verrà visualizzato il access_token restituito dal servizio.

   ![Uscita token](media/solution-template-copy-from-rest-or-http-using-oauth/token-output.png) 

10. Fare clic sull'icona "Input" di CopyActivity nella colonna **Azioni,** è necessario visualizzare il access_token recuperato da WebActivity viene passato a CopyActivity per l'autenticazione. 

    ![Ingresso token](media/solution-template-copy-from-rest-or-http-using-oauth/token-input.png)
        
    >[!CAUTION] 
    >Per evitare che il token venga registrato in testo normale, abilitare "Output sicuro" nell'attività Web e "Input sicuro" nell'attività di copia.


## <a name="export-json-response-as-is"></a>Esportare la risposta JSON così com'è

È possibile usare questo connettore REST per esportare la risposta JSON dell'API REST in vari archivi basati su file. Per ottenere una copia priva di schema, ignorare la sezione "structure" (chiamata anche *schema*) nel set di dati e il mapping dello schema nell'attività di copia.

## <a name="schema-mapping"></a>Mapping dello schema

Per copiare dati dall'endpoint REST al sink in formato tabulare, vedere [Mapping dello schema](copy-activity-schema-and-type-mapping.md#schema-mapping).

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
