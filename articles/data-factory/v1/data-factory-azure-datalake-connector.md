---
title: Copiare dati da e in Azure Data Lake Store | Microsoft Docs
description: Informazioni su come copiare dati da e in Data Lake Store usando Azure Data Factory
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: f4ba8288c1efd443310b4efc305c6f397c8163a0
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-to-and-from-data-lake-store-by-using-data-factory"></a>Copiare dati da e in Data Lake Store usando Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](data-factory-azure-datalake-connector.md)
> * [Versione 2 - Anteprima](../connector-azure-data-lake-store.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere [Connettore Azure Data Lake Store nella versione 2](../connector-azure-data-lake-store.md).

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare i dati da e in Azure Data Lake Store. Si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), una panoramica sullo spostamento dei dati con l'attività di copia.

## <a name="supported-scenarios"></a>Scenari supportati
È possibile copiare i dati **da Azure Data Lake Store** agli archivi di dati seguenti:

[!INCLUDE [data-factory-supported-sinks](../../../includes/data-factory-supported-sinks.md)]

È possibile copiare i dati dagli archivi dati seguenti **a Azure Data Lake Store**:

[!INCLUDE [data-factory-supported-sources](../../../includes/data-factory-supported-sources.md)]

> [!NOTE]
> Creare un account Data Lake Store prima della creazione di una pipeline con l'attività di copia. Per altre informazioni, vedere l'[introduzione ad Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md).

## <a name="supported-authentication-types"></a>Tipi di autenticazione supportati
Il connettore Data Lake Store supporta questi tipi di autenticazione:
* Autenticazione di un'entità servizio
* Autenticazione basata su credenziali dell'utente (OAuth) 

È consigliabile usare l'autenticazione basata su entità servizio, in particolare per una copia di dati pianificata. Con l'autenticazione basata sulle credenziali dell'utente può verificarsi la scadenza del token. Per i dettagli di configurazione, vedere la sezione [Proprietà del servizio collegato](#linked-service-properties).

## <a name="get-started"></a>Attività iniziali
È possibile creare una pipeline con l'attività di copia che sposta i dati da e verso Azure Data Lake Store usando diversi strumenti/API.

Il modo più semplice per creare una pipeline per copiare dati è usare la **Copia guidata**. Per un'esercitazione sulla creazione di una pipeline usando la Copia guidata, vedere [Esercitazione: creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md).

È possibile anche usare gli strumenti seguenti per creare una pipeline: **portale di Azure**, **Visual Studio**, **Azure PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink:

1. Creare una **data factory**. Una data factory può contenere una o più pipeline. 
2. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory. Ad esempio, se si copiano i dati da un archivio BLOB di Azure in un Azure Data Lake Store, si creano due servizi collegati per collegare l'account di archiviazione di Azure e l' Azure Data Lake Store alla data factory. Per le proprietà del servizio collegato specifiche dell'Azure Data Lake Store, vedere la sezione sulle [proprietà del servizio collegato](#linked-service-properties). 
2. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia. Nell'esempio citato nel passaggio precedente, si crea un set di dati per specificare un contenitore BLOB e la cartella che contiene i dati di input. Si crea anche un altro set di dati per specificare il percorso di cartella e file nel Data Lake Store che contiene i dati copiati dall'archivio BLOB. Per le proprietà del set di dati specifiche per Azure Data Lake Store, vedere la sezione sulle [proprietà del set di dati](#dataset-properties).
3. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output. Nell'esempio indicato in precedenza si usa BlobSource come origine e AzureDataLakeStoreSink come sink per l'attività di copia. Analogamente, se si effettua la copia da Azure Data Lake Store all'archivio BLOB di Azure, si usa AzureDataLakeStoreSource e BlobSink nell'attività di copia. Per le proprietà dell'attività di copia specifiche di Azure Data Lake Store, vedere la sezione sulle [proprietà dell'attività di copia](#copy-activity-properties). Per informazioni dettagliate su come usare un archivio dati come origine o come sink, fare clic sul collegamento nella sezione precedente per l'archivio dati.  

Quando si usa la procedura guidata, le definizioni JSON per queste entità di data factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di data factory.  Per esempi con definizioni JSON per entità di data factory utilizzate per copiare i dati da e verso Azure Data Lake Store, vedere la sezione degli [esempi JSON](#json-examples-for-copying-data-to-and-from-data-lake-store) in questo articolo.

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità di data factory specifiche di Data Lake Store.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
Un servizio collegato collega un archivio dati a una data factory. Si crea un servizio collegato di tipo **AzureDataLakeStore** per collegare di dati del Data Lake Store alla data factory. La tabella seguente descrive gli elementi JSON specifici dei servizi collegati del Data Lake Store. È possibile scegliere tra l'autenticazione basata su entità servizio e l'autenticazione basata su credenziali utente.

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| **type** | La proprietà type deve essere impostata su **AzureDataLakeStore**. | Sì |
| **dataLakeStoreUri** | Informazioni sull'account Azure Data Lake Store. Queste informazioni accettano uno dei seguenti formati: `https://[accountname].azuredatalakestore.net/webhdfs/v1` o `adl://[accountname].azuredatalakestore.net/`. | Sì |
| **subscriptionId** | ID sottoscrizione di Azure a cui l'account Data Lake Store appartiene. | Richiesto per il sink |
| **resourceGroupName** | Nome del gruppo di risorse di Azure a cui l'account Data Lake Store appartiene. | Richiesto per il sink |

### <a name="service-principal-authentication-recommended"></a>Autenticazione basata su entità servizio (opzione consigliata)
Per usare l'autenticazione basata su entità servizio, registrare un'entità applicazione in Azure Active Directory (Azure AD) e concedere a tale entità l'accesso a Data Lake Store. Per la procedura dettaglia, vedere [Autenticazione da servizio a servizio](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Prendere nota dei valori seguenti che si usano per definire il servizio collegato:
* ID applicazione
* Chiave applicazione 
* ID tenant

> [!IMPORTANT]
> Assicurarsi di concedere all'entità servizio un'autorizzazione appropriata in Azure Data Lake Store:
>- **Per usare Data Lake Store come origine**, concedere almeno l'autorizzazione di accesso ai dati **Lettura ed esecuzione** per l'elenco e la copia del contenuto di una cartella oppure l'autorizzazione **Lettura** per la copia di un file singolo. Nessun requisito per il controllo di accesso a livello di account.
>- **Per usare Data Lake Store come sink**, concedere almeno l'autorizzazione di accesso ai dati **Scrittura ed esecuzione** per consentire la creazione di elementi figlio nella cartella. E se si consente la copia tramite runtime di integrazione di Azure (sia origine che sink sono nel cloud) per permettere a Data Factory di rilevare l'area di Data Lake Store, concedere almeno il ruolo **Lettore** nel controllo di accesso dell'account (IAM). Se si vuole evitare questo ruolo IAM, nell'attività di copia [specificare executionLocation](data-factory-data-movement-activities.md#global) con la posizione di Data Lake Store.
>- Se si usa la **copia guidata per creare pipeline**, concedere almeno il ruolo **Lettore** nel controllo di accesso dell'account (IAM). Concedere poi almeno l'autorizzazione **Lettura ed Esecuzione** alla radice di Data Lake Store ("/") e ai relativi elementi figlio. In caso contrario, potrebbe venire visualizzato il messaggio "Fornite credenziali non valide".

Usare l'autenticazione basata su entità servizio specificando le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| **servicePrincipalId** | Specificare l'ID client dell'applicazione. | Sì |
| **servicePrincipalKey** | Specificare la chiave dell'applicazione. | Sì |
| **tenant** | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì |

**Esempio: autenticazione basata su entità servizio**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Autenticazione basata su credenziali utente
In alternativa, è possibile usare l'autenticazione delle credenziali dell'utente per copiare dati da o in Data Lake Store specificando le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| **authorization** | Fare clic sul pulsante **Autorizza** nell'editor di Data Factory e immettere le credenziali per assegnare l'URL di autorizzazione generato automaticamente a questa proprietà. | Sì |
| **sessionId** | ID sessione OAuth dalla sessione di autorizzazione oauth. Ogni ID di sessione è univoco e può essere usato solo una volta. Questa impostazione viene generata automaticamente quando si usa l'editor di Data Factory. | Sì |

> [!IMPORTANT]
> Assicurarsi di concedere all'utente un'autorizzazione appropriata in Azure Data Lake Store:
>- **Per usare Data Lake Store come origine**, concedere almeno l'autorizzazione di accesso ai dati **Lettura ed esecuzione** per l'elenco e la copia del contenuto di una cartella oppure l'autorizzazione **Lettura** per la copia di un file singolo. Nessun requisito per il controllo di accesso a livello di account.
>- **Per usare Data Lake Store come sink**, concedere almeno l'autorizzazione di accesso ai dati **Scrittura ed esecuzione** per consentire la creazione di elementi figlio nella cartella. E se si consente la copia tramite runtime di integrazione di Azure (sia origine che sink sono nel cloud) per permettere a Data Factory di rilevare l'area di Data Lake Store, concedere almeno il ruolo **Lettore** nel controllo di accesso dell'account (IAM). Se si vuole evitare questo ruolo IAM, nell'attività di copia [specificare executionLocation](data-factory-data-movement-activities.md#global) con la posizione di Data Lake Store.
>- Se si usa la **copia guidata per creare pipeline**, concedere almeno il ruolo **Lettore** nel controllo di accesso dell'account (IAM). Concedere poi almeno l'autorizzazione **Lettura ed Esecuzione** alla radice di Data Lake Store ("/") e ai relativi elementi figlio. In caso contrario, potrebbe venire visualizzato il messaggio "Fornite credenziali non valide".

**Esempio: autenticazione basata su credenziali utente**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Scadenza del token
Il codice di autorizzazione generato con il pulsante **Autorizza** ha una scadenza. Il messaggio seguente indica che il token di autenticazione è scaduto:

Errore dell'operazione relativa alle credenziali: invalid_grant - AADSTS70002: Errore di convalida delle credenziali. AADSTS70008: La concessione dell'accesso specificata è scaduta o è stata revocata. ID traccia: d18629e8-af88-43c5-88e3-d8419eb1fca1 ID correlazione: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21-09-31Z.

La tabella seguente mostra le scadenze di diversi tipi di account utente:

| Tipo di utente | Scade dopo |
|:--- |:--- |
| Account utente *non* gestiti da Azure Active Directory (ad esempio, @hotmail.com or @live.com) |12 ore |
| Account utente gestiti da Azure Active Directory |14 giorni dopo l'esecuzione dell'ultima sezione <br/><br/>90 giorni, se viene eseguita una sezione basata su un servizio collegato OAuth almeno una volta ogni 14 giorni |

Se si modifica la password prima dell'ora di scadenza del token, il token scade immediatamente. Verrà visualizzato il messaggio indicato in precedenza in questa sezione.

Alla scadenza del token è possibile ripetere l'autorizzazione dell'account usando il pulsante **Autorizza** per ridistribuire il servizio collegato. È anche possibile generare valori per le proprietà **sessionId** e **authorization** a livello di codice usando il codice seguente:


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
Per informazioni dettagliate sulle classi di Data Factory usate nel codice, vedere gli argomenti [AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) e [AuthorizationSessionGetResponse Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Aggiungere un riferimento alla versione `2.9.10826.1824` di `Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll` per la classe `WindowsFormsWebAuthenticationDialog` usata nel codice.

## <a name="troubleshooting-tips"></a>Suggerimenti per la risoluzione dei problemi

**Sintomo:** quando si copiano dati **in** Azure Data Lake Store, se l'attività di copia non riesce con l'errore seguente:

  ```
  Failed to detect the region for Azure Data Lake account {your account name}. Please make sure that the Resource Group name: {resource group name} and subscription ID: {subscription ID} of this Azure Data Lake Store resource are correct.
  ```

**Causa radice:** ci sono due motivi possibili:

1. `resourceGroupName` e/o `subscriptionId` specificato nel servizio collegato Azure Data Lake Store non è corretto.
2. L'utente o l'entità servizio non ha l'autorizzazione necessaria.

**Risoluzione:**

1. Assicurarsi che `subscriptionId` e `resourceGroupName` specificati in `typeProperties` del servizio collegato siano effettivamente quelli a cui appartiene l'account del data lake.

2. Assicurarsi concedere almeno il ruolo "**Lettore**" all'utente o all'entità servizio per l'account del data lake. Ecco come fare:

    1. Passare al portale di Azure -> account Data Lake Store personale
    2. Fare clic su "Controllo di accesso (IAM)" nel pannello di Data Lake Store
    3. Fare clic su "Aggiungi" nel pannello di "Controllo di accesso (IAM)"
    4. Impostare "Ruolo" come "Lettore" e selezionare l'utente o l'entità di servizio usata per la copia per concedere l'accesso

3. Se non si vuole concedere il ruolo "Lettore" all'utente o all'entità servizio, l'alternativa è [specificare un percorso di esecuzione in modo esplicito](data-factory-data-movement-activities.md#global) per l'attività di copia specificando il percorso di Data Lake Store. Esempio:

    ```json
    {
      "name": "CopyToADLS",
      "type": "Copy",
      ......
      "typeProperties": {
        "source": {
          "type": "<source type>"
        },
        "sink": {
          "type": "AzureDataLakeStoreSink"
        },
        "exeuctionLocation": "West US"
      }
    }
    ```

## <a name="dataset-properties"></a>Proprietà dei set di dati
Per specificare un set di dati per rappresentare i dati di input in Data Lake Store, impostare la proprietà **type** del set di dati su **AzureDataLakeStore**. Impostare la proprietà **linkedServiceName** del set di dati sul nome del servizio collegato Data Lake Store. Per un elenco completo delle proprietà e delle sezioni JSON disponibili per la definizione dei set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni di un set di dati in JSON, quali **struttura**, **disponibilità**, e **criteri**, sono simili per tutti i tipi di set di dati (ad esempio database SQL di Azure, BLOB Azure e tabelle di Azure). La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni quali la posizione e il formato dei dati nell'archivio dati. 

La sezione **typeProperties** per un set di dati di tipo **AzureDataLakeStore** contiene le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| **folderPath** |Percorso del contenitore e della cartella in Data Lake Store. |Sì |
| **fileName** |Nome del file in Azure Data Lake Store. La proprietà **fileName** è facoltativa e fa distinzione tra maiuscole e minuscole. <br/><br/>Se si specifica **fileName**, l'attività (inclusa la copia) viene eseguita sul file specifico.<br/><br/>Quando **fileName** non è specificato, l'attività di copia include tutti i file in **folderPath** nel set di dati di input.<br/><br/>Quando **fileName** non è specificato per un set di dati di output e **preserveHierarchy** non è specificato nel sink dell'attività, il nome del file generato avrà il formato Data._Guid_.txt`. Ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt. |No  |
| **partitionedBy** |La proprietà **partitionedBy** è facoltativa. Può essere utilizzata per specificare un percorso dinamico e un nome file per dati della serie temporale. Ad esempio, è possibile includere parametri per ogni ora di dati in **folderPath**. Per dettagli ed esempio, vedere [Proprietà partitionedBy](#using-partitionedby-property). |No  |
| **format** | Sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** in **format** su uno di questi valori. Per altre informazioni, vedere le sezioni [Formato testo](data-factory-supported-file-and-compression-formats.md#text-format), [Formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato ORC](data-factory-supported-file-and-compression-formats.md#orc-format) e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format) nell'articolo [Formati di file e di compressione supportati da Azure Data Factory](data-factory-supported-file-and-compression-formats.md). <br><br> Per copiare i file "così come sono" tra archivi basati su file (copia binaria), è possibile ignorare la sezione `format` nelle definizioni dei set di dati di input e di output. |No  |
| **compression** | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. I livelli supportati sono **Ottimale** e **Più veloce**. Per altre informazioni, vedere [File e formati di compressione supportati da Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No  |

### <a name="the-partitionedby-property"></a>Proprietà partitionedBy
È possibile specificare proprietà **folderPath** e **fileName** dinamiche per i dati di una serie temporale con la proprietà **partitionedBy**, le funzioni di Data Factory e le variabili di sistema. Per dettagli, vedere l'articolo [Azure Data Factory - Funzioni e variabili di sistema](data-factory-functions-variables.md).


Nell'esempio seguente, `{Slice}` è sostituito con il valore della variabile di sistema `SliceStart` di Data Factory nel formato specificato (`yyyyMMddHH`). Il nome `SliceStart` fa riferimento all'ora di inizio della sezione. La proprietà `folderPath` è diversa per ogni sezione, come in `wikidatagateway/wikisampledataout/2014100103` o `wikidatagateway/wikisampledataout/2014100104`.

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```

Nell'esempio seguente, anno, mese, giorno e ora di `SliceStart` vengono estratti in variabili separate e usati dalle proprietà `folderPath` e `fileName`:
```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
Per ulteriori dettagli sui set di dati di serie temporali, pianificazione e sezioni, vedere gli articoli [Set di dati in Azure Data Factory](data-factory-create-datasets.md) e [Pianificazione ed esecuzione di Data Factory](data-factory-scheduling-and-execution.md). 


## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

Le proprietà disponibili nella sezione **typeProperties** dell'attività variano in base al tipo di attività. Per un'attività di copia, variano in base ai tipi di origine e sink.

**AzureDataLakeStoreSource** supporta la proprietà seguente nella sezione **typeProperties**:

| Proprietà | DESCRIZIONE | Valori consentiti | Obbligatoria |
| --- | --- | --- | --- |
| **recursive** |Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. |True (valore predefinito), False |No  |


**AzureDataLakeStoreSource** supporta le proprietà seguenti nella sezione **typeProperties**:

| Proprietà | DESCRIZIONE | Valori consentiti | Obbligatoria |
| --- | --- | --- | --- |
| **copyBehavior** |Specifica il comportamento di copia. |<b>PreserveHierarchy:</b>: mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><br/><b>FlattenHierarchy</b>: tutti i file della cartella di origine vengono creati nel primo livello della cartella di destinazione. I file di destinazione vengono creati con nomi generati automaticamente.<br/><br/><b>MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se viene specificato il nome del file o del BLOB , il nome del file unito sarà il nome specificato. In caso contrario, il nome del file viene generato automaticamente. |No  |

### <a name="recursive-and-copybehavior-examples"></a>esempi ricorsivi e copyBehavior
In questa sezione viene descritto il comportamento derivante dell'operazione di copia per diverse combinazioni di valori ricorsivi e copyBehavior.

| ricorsiva | copyBehavior | Comportamento risultante |
| --- | --- | --- |
| true |preserveHierarchy |Per una cartella di origine Cartella1 con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5<br/><br/>la cartella di destinazione Cartella1 viene creata con la stessa struttura dell'origine<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5. |
| true |flattenHierarchy |Per una cartella di origine Cartella1 con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5<br/><br/>la Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File5 |
| true |mergeFiles |Per una cartella di origine Cartella1 con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5<br/><br/>la Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 + File3 + File4 + File 5 viene unito in un file con nome file generato automaticamente |
| false |preserveHierarchy |Per una cartella di origine Cartella1 con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5<br/><br/>la cartella di destinazione Cartella1 viene creata con la struttura seguente<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/><br/>Sottocartella1 con File3, File4 e File5 non considerati. |
| false |flattenHierarchy |Per una cartella di origine Cartella1 con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5<br/><br/>la cartella di destinazione Cartella1 viene creata con la struttura seguente<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File2<br/><br/><br/>Sottocartella1 con File3, File4 e File5 non considerati. |
| false |mergeFiles |Per una cartella di origine Cartella1 con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  &nbsp;File5<br/><br/>la cartella di destinazione Cartella1 viene creata con la struttura seguente<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 viene unito in un file con un nome file generato automaticamente. Nome generato automaticamente per File1<br/><br/>Sottocartella1 con File3, File4 e File5 non considerati. |

## <a name="supported-file-and-compression-formats"></a>Formati di file e di compressione supportati
Per i dettagli, vedere l'articolo relativo [File e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

## <a name="json-examples-for-copying-data-to-and-from-data-lake-store"></a>Esempi JSON per la copia dei dati da e in un Data Lake Store
Gli esempi seguenti offrono definizioni JSON di esempio. È possibile usare queste definizioni di esempio per creare una pipeline usando il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Gli esempi mostrano come copiare dati da e in un Data Lake Store e un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati _direttamente_ da qualsiasi origine a qualunque sink supportato. Per altre informazioni, vedere la sezione "Archivi dati e formati supportati" nell'articolo [Spostare dati con l'attività di copia](data-factory-data-movement-activities.md).  

### <a name="example-copy-data-from-azure-blob-storage-to-azure-data-lake-store"></a>Esempio: Copiare dati da un archivio BLOB di Azure ad Azure Data Lake Store
Il codice di esempio di questa sezione mostra:

* Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Un servizio collegato di tipo [AzureDataLakeStore](#linked-service-properties).
* Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureDataLakeStore](#dataset-properties).
* Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [BlobSource](data-factory-azure-blob-connector.md#copy-activity-properties) e [AzureDataLakeStoreSink](#copy-activity-properties).

Gli esempi mostrano come i dati di una serie temporale da un archivio BLOB di Azure vengono copiati in Data Lake Store ogni ora. 

**Servizio collegato Archiviazione di Azure**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Servizio collegato di Azure Data Lake Store**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> Per i dettagli di configurazione, vedere la sezione [Proprietà del servizio collegato](#linked-service-properties).
>

**Set di dati di input del BLOB di Azure**

Nell'esempio seguente, i dati vengono prelevati da un nuovo BLOB ogni ora (`"frequency": "Hour", "interval": 1`). Il percorso della cartella e il nome del file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti relative anno, mese e giorno dell'ora di inizio. Il nome file usa la parte relativa all'ora dell'ora di inizio. L'impostazione `"external": true` comunica al servizio Data Factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ]
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    },
    "policy": {
      "externalData": {
        "retryInterval": "00:01:00",
        "retryTimeout": "00:10:00",
        "maximumRetry": 3
      }
    }
  }
}
```

**Set di dati di output di Azure Data Lake Store**

L'esempio seguente copia i dati in Data Lake Store. I nuovi dati vengono copiati in Data Lake Store ogni ora.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
      "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
              "frequency": "Hour",
              "interval": 1
        }
      }
}
```


**Attività di copia in una pipeline con un'origine BLOB e un sink Data Lake Store**

Nell'esempio seguente, la pipeline contiene un'attività di copia configurata per usare i set di dati di input e di output. L'attività di copia è pianificata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo `source` è impostato su `BlobSource` e il tipo `sink` è impostato su `AzureDataLakeStoreSink`.

```json
{  
    "name":"SamplePipeline",
    "properties":
    {  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [  
              {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureBlobInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureDataLakeStoreOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                      },
                      "sink": {
                        "type": "AzureDataLakeStoreSink"
                      }
                },
                   "scheduler": {
                      "frequency": "Hour",
                      "interval": 1
                },
                "policy": {
                      "concurrency": 1,
                      "executionPriorityOrder": "OldestFirst",
                      "retry": 0,
                      "timeout": "01:00:00"
                }
              }
        ]
    }
}
```

### <a name="example-copy-data-from-azure-data-lake-store-to-an-azure-blob"></a>Esempio: Copiare dati da Azure Data Lake Store a un BLOB di Azure
Il codice di esempio di questa sezione mostra:

* Un servizio collegato di tipo [AzureDataLakeStore](#linked-service-properties).
* Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
* Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureDataLakeStore](#dataset-properties).
* Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
* Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [AzureDataLakeStoreSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Il codice copia i dati di una serie temporale da Data Lake Store in un archivio BLOB di Azure ogni ora. 

**Servizio collegato di Azure Data Lake Store**

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> Per i dettagli di configurazione, vedere la sezione [Proprietà del servizio collegato](#linked-service-properties).
>

**Servizio collegato Archiviazione di Azure**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Set di dati di input di Azure Data Lake**

In questo esempio, impostando `"external"`su `true` si comunica al servizio Data Factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

```json
{
    "name": "AzureDataLakeStoreInput",
      "properties":
    {
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
        "external": true,
        "availability": {
            "frequency": "Hour",
              "interval": 1
        },
        "policy": {
              "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
              }
        }
      }
}
```
**Set di dati di output del BLOB di Azure**

Nell'esempio seguente, i dati vengono scritti su un nuovo BLOB ogni ora (`"frequency": "Hour", "interval": 1`). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
      "partitionedBy": [
        {
          "name": "Year",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "yyyy"
          }
        },
        {
          "name": "Month",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "MM"
          }
        },
        {
          "name": "Day",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "dd"
          }
        },
        {
          "name": "Hour",
          "value": {
            "type": "DateTime",
            "date": "SliceStart",
            "format": "HH"
          }
        }
      ],
      "format": {
        "type": "TextFormat",
        "columnDelimiter": "\t",
        "rowDelimiter": "\n"
      }
    },
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

**Attività di copia in una pipeline con un'origine Azure Data Lake Store e un sink BLOB**

Nell'esempio seguente, la pipeline contiene un'attività di copia configurata per usare i set di dati di input e di output. L'attività di copia è pianificata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo `source` è impostato su `AzureDataLakeStoreSource` e il tipo `sink` è impostato su `BlobSink`.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureDataLakeStoreInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
                      },
                      "sink": {
                        "type": "BlobSink"
                      }
                },
                   "scheduler": {
                      "frequency": "Hour",
                      "interval": 1
                },
                "policy": {
                      "concurrency": 1,
                      "executionPriorityOrder": "OldestFirst",
                      "retry": 0,
                      "timeout": "01:00:00"
                }
              }
         ]
    }
}
```

Nella definizione dell'attività di copia, è anche possibile mappare le colonne dal set di dati di origine a colonne nel set di dati sink. Per altre informazioni, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestazioni e ottimizzazione
Per informazioni sui fattori che incidono sulle prestazioni dell'attività di copia e su come ottimizzarle, vedere l'articolo [Guida alle prestazioni dell'attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).
