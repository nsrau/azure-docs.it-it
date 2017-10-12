---
title: Trasformare dati usando lo script U-SQL - Azure | Documentazione Microsoft
description: Informazioni su come elaborare o trasformare i dati eseguendo gli script U-SQL nel servizio di calcolo di Azure Data Lake Analytics.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: e17c1255-62c2-4e2e-bb60-d25274903e80
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: spelluru
robots: noindex
ms.openlocfilehash: 3a0a097afa0ef5efe11cb5044bf9ea5d399e463f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Trasformare i dati eseguendo script U-SQL in Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](data-factory-usql-activity.md)
> * [Versione 2 - Anteprima](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere le informazioni sull'[attività U-SQL nella versione 2](../transform-data-using-data-lake-analytics.md).

Una pipeline in un'istanza di Data factory di Azure elabora i dati nei servizi di archiviazione collegati usando i servizi di calcolo collegati. Contiene una sequenza di attività in cui ogni attività esegue una specifica operazione di elaborazione. Questo articolo descrive l'**attività U-SQL di Data Lake Analytics** che esegue uno script **U-SQL** in un servizio di calcolo collegato di **Azure Data Lake Analytics**. 

Creare un account di Azure Data Lake Analytics prima di creare una pipeline con un'attività U-SQL di Data Lake Analytics. Per altre informazioni su Azure Data Lake Analytics, vedere [Introduzione ad Azure Data Lake con l'SDK .NET](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Per la procedura dettagliata per la creazione di una data factory, dei servizi collegati, dei set di dati e di una pipeline, vedere l' [Esercitazione: Creare la prima pipeline](data-factory-build-your-first-pipeline.md) . Usare i frammenti JSON con l'editor di Data Factory, Visual Studio o Azure PowerShell per creare le entità di Data Factory.

## <a name="supported-authentication-types"></a>Tipi di autenticazione supportati
L'attività U-SQL supporta i tipi di autenticazione in Data Lake Analytics indicati di seguito:
* Autenticazione di un'entità servizio
* Autenticazione basata su credenziali dell'utente (OAuth) 

È consigliabile usare l'autenticazione basata sull'entità servizio, in particolare per un'esecuzione U-SQL pianificata. Con l'autenticazione basata sulle credenziali dell'utente può verificarsi la scadenza del token. Per i dettagli di configurazione, vedere la sezione [Proprietà del servizio collegato](#azure-data-lake-analytics-linked-service).

## <a name="azure-data-lake-analytics-linked-service"></a>Servizio collegato di Azure Data Lake Analytics
Creare un servizio collegato di **Azure Data Lake Analytics** per collegare un servizio di calcolo di Azure Data Lake Analytics a una Data Factory di Azure. L'attività U-SQL di Data Lake Analytics nella pipeline fa riferimento a questo servizio collegato. 

La tabella seguente fornisce le descrizioni delle proprietà generiche usate nella definizione JSON. È possibile scegliere anche tra l'autenticazione basata sull'entità servizio e l'autenticazione basata sulle credenziali utente.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| **type** |La proprietà type deve essere impostata su **AzureDataLakeAnalytics**. |Sì |
| **accountName** |Nome dell'account di Azure Data Lake Analytics. |Sì |
| **dataLakeAnalyticsUri** |URI di Azure Data Lake Analytics. |No |
| **subscriptionId** |ID sottoscrizione di Azure |No (se non specificata, viene usata la sottoscrizione della Data factory). |
| **resourceGroupName** |Nome del gruppo di risorse di Azure |No (se non specificata, viene usato il gruppo di risorse di Data Factory). |

### <a name="service-principal-authentication-recommended"></a>Autenticazione basata su entità servizio (opzione consigliata)
Per usare l'autenticazione basata su entità servizio, registrare un'entità applicazione in Azure Active Directory (Azure AD) e concedere a tale entità l'accesso a Data Lake Store. Per la procedura dettaglia, vedere [Autenticazione da servizio a servizio](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:
* ID applicazione
* Chiave applicazione 
* ID tenant

Usare l'autenticazione basata su entità servizio specificando le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| **servicePrincipalId** | Specificare l'ID client dell'applicazione. | Sì |
| **servicePrincipalKey** | Specificare la chiave dell'applicazione. | Sì |
| **tenant** | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì |

**Esempio: autenticazione basata su entità servizio**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Autenticazione basata su credenziali utente
In alternativa, è possibile usare l'autenticazione delle credenziali dell'utente per Data Lake Analytics specificando le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| **authorization** | Fare clic sul pulsante **Autorizza** nell'editor di Data Factory e immettere le credenziali per assegnare l'URL di autorizzazione generato automaticamente a questa proprietà. | Sì |
| **sessionId** | ID sessione OAuth dalla sessione di autorizzazione oauth. Ogni ID di sessione è univoco e può essere usato solo una volta. Questa impostazione viene generata automaticamente quando si usa l'editor di Data Factory. | Sì |

**Esempio: autenticazione basata su credenziali utente**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Scadenza del token
Il codice di autorizzazione generato con il pulsante **Autorizza** ha una scadenza. Per le scadenze dei diversi tipi di account utente, vedere la tabella seguente. Alla **scadenza del token** di autenticazione potrebbe essere visualizzato un messaggio di errore simile al seguente: Errore dell'operazione relativa alle credenziali: invalid_grant - AADSTS70002: Errore di convalida delle credenziali. AADSTS70008: La concessione dell'accesso specificata è scaduta o è stata revocata. ID traccia: d18629e8-af88-43c5-88e3-d8419eb1fca1 ID correlazione: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31Z.

| Tipo di utente | Scade dopo |
|:--- |:--- |
| Account utente NON gestiti da Azure Active Directory (@hotmail.com, @live.com e così via). |12 ore |
| Account utente gestiti da Azure Active Directory (AAD) |14 giorni dopo l'esecuzione dell'ultima sezione. <br/><br/>90 giorni, se viene eseguita una sezione basata sul servizio collegato OAuth almeno una volta ogni 14 giorni. |

Per evitare/risolvere questo problema, alla **scadenza del token** ripetere l'autorizzazione usando il pulsante **Autorizza** e ridistribuire il servizio collegato. È anche possibile generare valori per le proprietà **sessionId** e **authorization** a livello di codice usando il codice seguente:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Per informazioni dettagliate sulle classi di Data Factory usate nel codice, vedere gli argomenti [AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) e [AuthorizationSessionGetResponse Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Aggiungere un riferimento a: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll per la classe WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Attività U-SQL di Data Lake Analytics
Il frammento JSON seguente definisce una pipeline con un'attività U-SQL di Data Lake Analytics. La definizione dell'attività contiene un riferimento al servizio collegato di Azure Data Lake Analytics creato in precedenza.   

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

Nella tabella seguente vengono descritti i nomi e le descrizioni delle proprietà specifiche per questa attività. 

| Proprietà            | Descrizione                              | Obbligatorio                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| Tipo                | La proprietà type deve essere impostata su **DataLakeAnalyticsU-SQL**. | Sì                                      |
| linkedServiceName   | Riferimento all'istanza di Azure Data Lake Analytics registrata come servizio collegato in Data Factory | Sì                                      |
| scriptPath          | Percorso della cartella contenente lo script U-SQL. Il nome del file distingue tra maiuscole e minuscole. | No (se si usa uno script)                   |
| scriptLinkedService | Servizi collegati che collegano la risorsa di archiviazione contenente lo script alla Data factory | No (se si usa uno script)                   |
| script              | Specificare lo script inline anziché scriptPath e scriptLinkedService. Ad esempio: `"script": "CREATE DATABASE test"`. | No (se si usano le proprietà scriptPath e scriptLinkedService) |
| degreeOfParallelism | Il numero massimo di nodi usati contemporaneamente per eseguire il processo. | No                                       |
| priority            | Determina quali processi rispetto a tutti gli altri disponibili nella coda devono essere selezionati per essere eseguiti per primi. Più è basso il numero, maggiore sarà la priorità. | No                                       |
| parameters          | Parametri per lo script U-SQL          | No                                       |
| runtimeVersion      | Versione di runtime del motore di U-SQL da usare | No                                       |
| compilationMode     | <p>Modalità di compilazione di U-SQL. Deve essere uno dei valori seguenti:</p> <ul><li>**Semantic:** esegue solo controlli semantici e i controlli di integrità necessari.</li><li>**Full:** esegue la compilazione completa, inclusi il controllo della sintassi, l'ottimizzazione, la generazione di codice e così via.</li><li>**SingleBox:** esegue la compilazione completa, con TargetType impostato su SingleBox.</li></ul><p>Se per questa proprietà non si specifica alcun valore, il server determina la modalità di compilazione ottimale. </p> | No                                       |

Per la definizione dello script, vedere la sezione [Definizione dello script SearchLogProcessing.txt](#sample-u-sql-script) . 

## <a name="sample-input-and-output-datasets"></a>Set di dati di input e output di esempio
### <a name="input-dataset"></a>Set di dati di input
In questo esempio i dati di input si trovano in un archivio di Azure Data Lake (file SearchLog.tsv nella cartella datalake/input). 

```json
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}    
```

### <a name="output-dataset"></a>Set di dati di output
In questo esempio i dati di output generati dallo script U-SQL sono memorizzati in un archivio di Azure Data Lake (cartella datalake/output). 

```json
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>Servizio collegato di Data Lake Store di esempio
Ecco la definizione del servizio collegato di Azure Data Lake Store di esempio usato dai set di dati di input/output. 

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
        }
    }
}
```

Per la descrizione delle proprietà JSON, vedere l'articolo [Move data to and from Azure Data Lake Store](data-factory-azure-datalake-connector.md) (Spostare dati da e in Azure Data Lake Store). 

## <a name="sample-u-sql-script"></a>Script U-SQL di esempio

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

I valori dei parametri **@in** e **@out** nello script U-SQL vengono passati in modo dinamico da ADF usando la sezione "parameters". Vedere la sezione "parameters" nella definizione della pipeline.

È possibile specificare anche altre proprietà come degreeOfParallelism e priorità nella definizione della pipeline per i processi in esecuzione sul servizio Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Parametri dinamici
Nell'esempio di definizione di pipeline i parametri in e out vengono assegnati con valori hardcoded. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

È anche possibile usare parametri dinamici. ad esempio: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

In questo caso, i file di input vengono prelevati dalla cartella /datalake/input e i file di output vengono generati nella cartella /datalake/output. I nomi dei file sono dinamici e si basano sull'ora di inizio della sezione.  


