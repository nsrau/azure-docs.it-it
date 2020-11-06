---
title: Risoluzione dei problemi di Azure Data Factory | Microsoft Docs
description: Informazioni su come risolvere i problemi relativi alle attività di controllo esterne in Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 09/01/2020
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 6f16e4b1f9728ae8d9cb36ab442603083e83eb92
ms.sourcegitcommit: 46c5ffd69fa7bc71102737d1fab4338ca782b6f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2020
ms.locfileid: "94331380"
---
# <a name="troubleshoot-azure-data-factory"></a>Risoluzione dei problemi di Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra i metodi più comuni per la risoluzione dei problemi relativi alle attività di controllo esterne in Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Connettore e attività di copia

Per problemi relativi al connettore, ad esempio un errore durante l'attività di copia, vedere [Risolvere i problemi dei connettori di Azure Data Factory](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code-3200"></a>Codice errore: 3200

- **Messaggio** : Errore 403.

- **Causa** : `The Databricks access token has expired.`

- **Raccomandazione** : per impostazione predefinita, il token di accesso di Azure Databricks è valido per 90 giorni. Creare un nuovo token e aggiornare il servizio collegato.

### <a name="error-code-3201"></a>Codice errore: 3201

- **Messaggio** : `Missing required field: settings.task.notebook_task.notebook_path.`

- **Causa** : `Bad authoring: Notebook path not specified correctly.`

- **Raccomandazione** : specificare il percorso del notebook nell'attività Databricks.

<br/> 

- **Messaggio** : `Cluster... does not exist.`

- **Causa** : `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Raccomandazione** : verificare che sia presente il cluster Databricks.

<br/> 

- **Messaggio** : `Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Causa** : `Bad authoring.`

- **Raccomandazione** : specificare i percorsi assoluti per gli schemi di indirizzamento dell'area di lavoro o `dbfs:/folder/subfolder/foo.py` per i file archiviati nel Databricks File System (DFS).

<br/> 

- **Messaggio** : `{0} LinkedService should have domain and accessToken as required properties.`

- **Causa** : `Bad authoring.`

- **Raccomandazione** : verificare la [definizione del servizio collegato](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Messaggio** : `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Causa** : `Bad authoring.`

- **Raccomandazione** : verificare la [definizione del servizio collegato](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Messaggio** : `Node type Standard_D16S_v3 is not supported. Supported node types: Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2.`

- **Causa** : `Bad authoring.`

- **Raccomandazione** : fare riferimento al messaggio di errore.

<br/>

### <a name="error-code-3202"></a>Codice errore: 3202

- **Messaggio** : `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit: 1000 job creations per 3600 seconds.`

- **Causa** : `Too many Databricks runs in an hour.`

- **Raccomandazione** : controllare la velocità di creazione del processo per tutte le pipeline che usano l'area di lavoro di Databricks. Se le pipeline avviano troppe esecuzioni di Databricks aggregate, migrare alcune pipeline a una nuova area di lavoro.

<br/> 

- **Messaggio** : `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Causa** : `Authoring error: No value provided for the parameter.`

- **Raccomandazione** : esaminare il JSON della pipeline e verificare che per tutti i parametri del notebook baseParameters sia specificato un valore non vuoto.

<br/> 

- **Messaggio** : `User: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` is not authorized to access cluster.`

- **Causa** : l'utente che ha generato il token di accesso non è autorizzato a accedere al cluster Databricks specificato nel servizio collegato.

- **Raccomandazione** : verificare che l'utente disponga delle autorizzazioni richieste nell'area di lavoro.

### <a name="error-code-3203"></a>Codice errore: 3203

- **Messaggio** : `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Causa** : il cluster è stato arrestato. In caso di cluster interattivi, il problema potrebbe essere una race condition.

- **Raccomandazione** : per evitare questo errore, usare cluster di processo.

### <a name="error-code-3204"></a>Codice errore: 3204

- **Messaggio** : `Job execution failed.`

- **Causa** : i messaggi di errore indicano diversi problemi, ad esempio uno stato imprevisto del cluster o un'attività specifica. Spesso non vengono visualizzati messaggi di errore.

- **Raccomandazione** : N/D

### <a name="error-code-3208"></a>Codice di errore: 3208

- **Messaggio** : `An error occurred while sending the request.`

- **Motivo** : la connessione di rete al servizio databricks è stata interrotta.

- **Suggerimento** : se si usa un runtime di integrazione self-hosted, assicurarsi che la connessione di rete sia affidabile dai nodi di Integration Runtime. Se si usa il runtime di integrazione di Azure, il nuovo tentativo funziona in genere.
 
## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics.

La tabella seguente si riferisce a U-SQL.
 
### <a name="error-code-2709"></a>Codice errore: 2709

- **Messaggio** : `The access token is from the wrong tenant.`

- **Causa** : Tenant di Azure Active Directory (Azure AD) errato.

- **Raccomandazione** : Tenant di Azure Active Directory (Azure AD) errato.

<br/>

- **Messaggio** : `We cannot accept your job at this moment. The maximum number of queued jobs for your account is 200. `

- **Causa** : questo errore è causato dalla limitazione in Data Lake Analytics.

- **Raccomandazione** : ridurre il numero di processi inviati a Data Lake Analytics. Modificare i trigger di Data Factory e le impostazioni di concorrenza per le attività o aumentare i limiti di Data Lake Analytics.

<br/> 

- **Messaggio** : `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Causa** : questo errore è causato dalla limitazione in Data Lake Analytics.

- **Raccomandazione** : ridurre il numero di processi inviati a Data Lake Analytics. Modificare i trigger di Data Factory e le impostazioni di concorrenza per le attività o aumentare i limiti di Data Lake Analytics.

### <a name="error-code-2705"></a>Codice errore: 2705

- **Messaggio** : `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Causa** : L'entità servizio o il certificato non hanno accesso al file nella risorsa di archiviazione.

- **Raccomandazione** : verificare che l'entità servizio o il certificato forniti dall'utente per i processi Data Lake Analytics possano accedere sia all'account Data Lake Analytics, sia all'istanza Data Lake Storage predefinita dalla cartella radice.

### <a name="error-code-2711"></a>Codice errore: 2711

- **Messaggio** : `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Causa** : L'entità servizio o il certificato non hanno accesso al file nella risorsa di archiviazione.

- **Raccomandazione** : verificare che l'entità servizio o il certificato forniti dall'utente per i processi Data Lake Analytics possano accedere sia all'account Data Lake Analytics, sia all'istanza Data Lake Storage predefinita dalla cartella radice.

<br/> 

- **Messaggio** : `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Causa** : il percorso del file U-SQL è errato o non è possibile accedere con le credenziali del servizio associato.

- **Raccomandazione** : verificare percorso e credenziali forniti nel servizio associato.

### <a name="error-code-2704"></a>Codice errore: 2704

- **Messaggio** : `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Causa** : L'entità servizio o il certificato non hanno accesso al file nella risorsa di archiviazione.

- **Raccomandazione** : verificare che l'entità servizio o il certificato forniti dall'utente per i processi Data Lake Analytics possano accedere sia all'account Data Lake Analytics, sia all'istanza Data Lake Storage predefinita dalla cartella radice.

### <a name="error-code-2707"></a>Codice errore: 2707

- **Messaggio** : `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and 'DataLakeAnalyticsUri'.`

- **Causa** : account Data Lake Analytics errato nel servizio associato.

- **Raccomandazione** : verificare di aver inserito l'account corretto.

### <a name="error-code-2703"></a>Codice errore: 2703

- **Messaggio** : `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error Id:").`

- **Causa** : l'errore riguarda Data Lake Analytics.

- **Raccomandazione** : si è verificato un errore nel processo inviato a Data Lake Analytics e nello script. Cercare la causa in Data Lake Analytics. Accedere all'account Data Lake Analytics dal portale e cercare il processo usando l'ID di esecuzione attività di Data Factory (non usare l'ID di esecuzione della pipeline). Nel processo è possibile trovare più informazioni sull'errore per risolvere il problema.

   Se la soluzione non è chiara, contattare il team di supporto di Data Lake Analytics e fornire l'URL del processo, che contiene il nome dell'account e l'ID del processo.
 
## <a name="azure-functions"></a>Funzioni di Azure

### <a name="error-code-3602"></a>Codice errore: 3602

- **Messaggio** : `Invalid HttpMethod: '%method;'.`

- **Causa** : il metodo Http specificato nel payload delle attività non è supportato dall'attività della funzione di Azure.

- **Raccomandazione** : i metodi Http supportati sono: PUT, POST, GET, DELETE, OPTIONS, HEAD e TRACE.

### <a name="error-code-3603"></a>Codice errore: 3603

- **Messaggio** : `Response Content is not a valid JObject.`

- **Causa** : la funzione di Azure chiamata non ha restituito in risposta un payload JSON. La funzione Azure Data Factory (ADF) supporta solo risposte con contenuto JSON.

- **Raccomandazione** : aggiornare la funzione di Azure in modo che restituisca un payload JSON valido, come quello di una funzione C# `(ActionResult)new OkObjectResult("{\"Id\":\"123\"}");`

### <a name="error-code-3606"></a>Codice errore: 3606

- **Messaggio** : chiave della funzione mancante nell'attività della funzione di Azure.

- **Causa** : la definizione dell'attività della funzione di Azure non è completa.

- **Raccomandazione** : verificare che nella definizione JSON dell'attività della funzione di input di Azure sia presente una proprietà con nome `functionKey`.

### <a name="error-code-3607"></a>Codice errore: 3607

- **Messaggio** : `Azure function activity missing function name.`

- **Causa** : la definizione dell'attività della funzione di Azure non è completa.

- **Raccomandazione** : verificare che nella definizione JSON dell'attività della funzione di input di Azure sia presente una proprietà con nome `functionName`.

### <a name="error-code-3608"></a>Codice errore: 3608

- **Messaggio** : `Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Causa** : i dettagli della funzione di Azure nella definizione dell'attività potrebbero non essere corretti.

- **Raccomandazione** : correggere i dettagli della funzione di Azure e riprovare.

### <a name="error-code-3609"></a>Codice errore: 3609

- **Messaggio** : `Azure function activity missing functionAppUrl.`

- **Causa** : la definizione dell'attività della funzione di Azure non è completa.

- **Raccomandazione** : verificare che nella definizione JSON dell'attività della funzione di input di Azure sia presente una proprietà con nome `functionAppUrl`.

### <a name="error-code-3610"></a>Codice errore: 3610

- **Messaggio** : `There was an error while calling endpoint.`

- **Causa** : l'URL della funzione potrebbe non essere corretto.

- **Raccomandazione** : verificare che il valore di `functionAppUrl` nel JSON dell'attività sia corretto e riprovare.

### <a name="error-code-3611"></a>Codice errore: 3611

- **Messaggio** : `Azure function activity missing Method in JSON.`

- **Causa** : la definizione dell'attività della funzione di Azure non è completa.

- **Raccomandazione** : verificare che nella definizione JSON dell'attività della funzione di input di Azure sia presente una proprietà con nome `method`.

### <a name="error-code-3612"></a>Codice errore: 3612

- **Messaggio** : `Azure function activity missing LinkedService definition in JSON.`

- **Causa** : la definizione dell'attività della funzione di Azure non è completa.

- **Raccomandazione** : verificare che nella definizione JSON dell'attività della funzione di input di Azure siano presenti dettagli sul servizio collegato.

## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code-4101"></a>Codice errore: 4101

- **Messaggio** : `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Causa** : formato errato o definizione della proprietà mancante `%propertyName;`.

- **Raccomandazione** : controllare che nell'attività `%activityName;` la proprietà `%propertyName;` sia definita con i dati corretti.

### <a name="error-code-4110"></a>Codice errore: 4110

- **Messaggio** : `AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Causa** : la definizione dell'attività AzureMLExecutePipeline non è completa.

- **Raccomandazione** : verificare che nella definizione JSON dell'attività della funzione di input AzureMLExecutePipeline i dettagli del servizio siano collegati correttamente.

### <a name="error-code-4111"></a>Codice errore: 4111

- **Messaggio** : `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Causa** : la definizione dell'attività non è corretta.

- **Raccomandazione** : verificare che nella definizione JSON dell'attività della funzione di input AzureMLExecutePipeline i dettagli del servizio siano collegati correttamente.

### <a name="error-code-4112"></a>Codice errore: 4112

- **Messaggio** : `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Causa** : formato errato o definizione della proprietà "%NomeProprietà;" mancante.

- **Raccomandazione** : verificare che la proprietà `%propertyName;` del servizio collegato sia definita con i dati corretti.

### <a name="error-code-4121"></a>Codice errore: 4121

- **Messaggio** : `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa** : le credenziali usate per accedere a Azure Machine Learning sono scadute.

- **Raccomandazione** : verificare che le credenziali siano valide e riprovare.

### <a name="error-code-4122"></a>Codice errore: 4122

- **Messaggio** : `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa** : le credenziali fornite nel servizio associato di Azure Machine Learning non sono valide o non dispongono dell'autorizzazione per l'operazione.

- **Raccomandazione** : verificare che le credenziali del servizio associato siano valide e che dispongano dell'autorizzazione per accedere a Azure Machine Learning.

### <a name="error-code-4123"></a>Codice errore: 4123

- **Messaggio** : `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa** : le proprietà dell'attività, ad esempio `pipelineParameters` non sono valide per la pipeline di Azure Machine Learning (ML).

- **Raccomandazione** : verificare che il valore delle proprietà dell'attività corrisponda al payload previsto per la pipeline Azure ML pubblicata specificato in Servizio associato.

### <a name="error-code-4124"></a>Codice errore: 4124

- **Messaggio** : `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa** : l'endpoint della pipeline Azure ML pubblicato non esiste.

- **Raccomandazione** : verificare che l'endpoint della pipeline Azure Machine Learning pubblicato in Servizio associato sia presente in Azure Machine Learning.

### <a name="error-code-4125"></a>Codice errore: 4125

- **Messaggio** : `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa** : si è verificato un errore del server in Azure Machine Learning.

- **Raccomandazione** : Riprovare in un secondo momento. Se il problema persiste, contattare il team di Azure Machine Learning.

### <a name="error-code-4126"></a>Codice errore: 4126

- **Messaggio** : `Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Causa** : esecuzione della pipeline di Azure Machine Learning non riuscita.

- **Raccomandazione** : verificare la presenza di altri log di errore in Azure Machine Learning e correggere la pipeline.

## <a name="common"></a>Comuni

### <a name="error-code-2103"></a>Codice errore: 2103

- **Messaggio** : `Please provide value for the required property '%propertyName;'.`

- **Causa** : non è stato inserito il valore richiesto per la proprietà.

- **Raccomandazione** : inserire il valore indicato nel messaggio e riprovare.

### <a name="error-code-2104"></a>Codice errore: 2104

- **Messaggio** : `The type of the property '%propertyName;' is incorrect.`

- **Causa** : il tipo di proprietà indicato non è corretto.

- **Raccomandazione** : correggere il tipo di proprietà e riprovare.

### <a name="error-code-2105"></a>Codice errore: 2105

- **Messaggio** : `An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Causa** : il valore della proprietà non è valido o nel formato previsto.

- **Raccomandazione** : consultare la documentazione della proprietà e verificare che il valore fornito includa formato e tipo corretti.

### <a name="error-code-2106"></a>Codice errore: 2106

- **Messaggio** : `The storage connection string is invalid. %errorMessage;`

- **Causa** : la stringa di connessione per la risorsa di archiviazione non è valida o il formato non è corretto.

- **Raccomandazione** : trovare la risorsa di archiviazione nel portale di Azure, quindi copiare e incollare la stringa di connessione nel servizio associato e riprovare.

### <a name="error-code-2108"></a>Codice errore: 2108

- **Messaggio** : `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Causa** : la richiesta non è riuscita a causa di un problema sottostante relativo, ad esempio, alla connettività di rete, a un errore DNS, alla convalida del certificato del server o a un timeout.

- **Raccomandazione** : usare Fiddler/Postman per convalidare la richiesta.

### <a name="error-code-2110"></a>Codice errore: 2110

- **Messaggio** : `The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Causa** : il servizio collegato specificato nell'attività non è corretto.

- **Raccomandazione** : verificare che il tipo di servizio collegato sia uno di quelli supportati per l'attività. Ad esempio, il tipo di servizio collegato per le attività HDI può essere HDInsight o HDInsightOnDemand.

### <a name="error-code-2111"></a>Codice errore: 2111

- **Messaggio** : `The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Causa** : il tipo di proprietà indicato non è corretto.

- **Raccomandazione** : correggere il tipo di proprietà e riprovare.

### <a name="error-code-2112"></a>Codice errore: 2112

- **Messaggio** : `The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Causa** : il tipo di cloud non è supportato o non può essere determinato da EndpointSuffix per l'archiviazione.

- **Raccomandazione** : archiviare in un altro cloud e riprovare.

### <a name="error-code-2128"></a>Codice errore: 2128

- **Messaggio** : `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Causa** : connettività di rete, errore DNS, convalida del certificato del server o timeout.

- **Raccomandazione** : assicurarsi che l'endpoint che si tenta di raggiungere risponda alle richieste. È possibile usare strumenti come Fiddler/Postman.

## <a name="custom"></a>Personalizzato

La tabella seguente si applica a Azure Batch.
 
### <a name="error-code-2500"></a>Codice errore: 2500

- **Messaggio** : `Hit unexpected exception and execution failed.`

- **Causa** : `Can't launch command, or the program returned an error code.`

- **Raccomandazione** : assicurarsi che il file eseguibile esista. Se il programma si è avviato, verificare che *stdout.txt* e *stderr.txt* siano stati caricati nell'account di archiviazione. È consigliabile includere i log nel codice per il debug.

### <a name="error-code-2501"></a>Codice errore: 2501

- **Messaggio** : `Cannot access user batch account; please check batch account settings.`

- **Causa** : chiave di accesso a Batch o nome del pool errati.

- **Raccomandazione** : verificare il nome del pool e la chiave di accesso a Batch nel servizio collegato.

### <a name="error-code-2502"></a>Codice errore: 2502

- **Messaggio** : `Cannot access user storage account; please check storage account settings.`

- **Causa** : il nome dell'account di archiviazione o la chiave di accesso non sono corretti.

- **Raccomandazione** : verificare il nome dell'account di archiviazione e la chiave di accesso nel servizio collegato.

### <a name="error-code-2504"></a>Codice errore: 2504

- **Messaggio** : `Operation returned an invalid status code 'BadRequest'.`

- **Causa** : troppi file nel `folderPath` dell'attività personalizzata. `resourceFiles` non può contenere più di 32.768 caratteri.

- **Raccomandazione** : Rimuovere i file non necessari o comprimerli e aggiungere un comando di decompressione per estrarli.
   
   Ad esempio, usare `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2505"></a>Codice errore: 2505

- **Messaggio** : `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Causa** : le attività personalizzate supportano solo account di archiviazione che usano una chiave di accesso.

- **Raccomandazione** : consultare la descrizione dell'errore.

### <a name="error-code-2507"></a>Codice errore: 2507

- **Messaggio** : `The folder path does not exist or is empty: ...`

- **Causa** : nell'account di archiviazione nel percorso specificato non sono presenti file.

- **Raccomandazione** : il percorso della cartella deve contenere i file eseguibili da usare.

### <a name="error-code-2508"></a>Codice errore: 2508

- **Messaggio** : `There are duplicate files in the resource folder.`

- **Causa** : in sottocartelle di folderPath sono presenti più file con lo stesso nome.

- **Raccomandazione** : le attività personalizzate rendono flat la struttura in folderPath. Se è necessario mantenere la struttura di cartelle, comprimere i file ed estrarli in Azure Batch usando un comando di decompressione.
   
   Ad esempio, usare `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2509"></a>Codice errore: 2509

- **Messaggio** : `Batch url ... is invalid; it must be in Uri format.`

- **Causa** : gli URL Batch devono essere simili a `https://mybatchaccount.eastus.batch.azure.com`

- **Raccomandazione** : consultare la descrizione dell'errore.

### <a name="error-code-2510"></a>Codice errore: 2510

- **Messaggio** : `An error occurred while sending the request.`

- **Causa** : URL batch non valido.

- **Raccomandazione** : verificare l'URL del batch.
 
## <a name="hdinsight"></a>HDInsight

### <a name="error-code-206"></a>Codice errore: 206

- **Messaggio** : `The batch ID for Spark job is invalid. Please retry your job.`

- **Causa** : l'errore è stato causato da un problema interno del servizio.

- **Raccomandazione** : questo problema potrebbe essere temporaneo. Ripetere il processo dopo qualche minuto.

### <a name="error-code-207"></a>Codice errore: 207

- **Messaggio** : `Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI.`

- **Causa** : errore interno durante il tentativo di determinare l'area dall'account di archiviazione primario.

- **Raccomandazione** : provare con un'altra risorsa di archiviazione. 

### <a name="error-code-208"></a>Codice errore: 208

- **Messaggio** : `Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again.`

- **Causa** : si è verificato un errore interno durante il tentativo di leggere l'entità servizio o di creare un'istanza dell'autenticazione MSI.

- **Raccomandazione** : fornire un'entità servizio con autorizzazioni per creare un cluster HDInsight nella sottoscrizione fornita e riprovare. Verificare che le [identità gestite siano configurate correttamente](../hdinsight/hdinsight-managed-identities.md).


### <a name="error-code-2300"></a>Codice errore: 2300

- **Messaggio** : `Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **Causa** : il messaggio di errore è simile a `The remote name could not be resolved.`. L'URI cluster fornito potrebbe non essere valido.

- **Raccomandazione** : verificare che il cluster non sia stato eliminato e che l'URI sia corretto. Quando si apre l'URI in un browser, dovrebbe essere visualizzata interfaccia utente di Ambari. Se il cluster si trova in una rete virtuale, l'URI dovrebbe essere privato. Per aprirlo, usare una macchina virtuale all'interno della stessa rete virtuale.

   Per altre informazioni, vedere [Connettersi direttamente ai servizi Apache Hadoop](../hdinsight/hdinsight-plan-virtual-network-deployment.md#directly-connect-to-apache-hadoop-services).
 
 </br>

- **Causa** : se il messaggio di errore è simile a `A task was canceled.`, si è verificato il timeout dell'invio del processo.

- **Raccomandazione** : il problema potrebbe riguardare la connettività generale di HDInsight o la connettività di rete. Per prima cosa, verificare che l'interfaccia utente di HDInsight Ambari sia disponibile in qualsiasi browser. In seguito, controllare che le credenziali siano ancora valide.
   
   Se si usa un runtime di integrazione self-hosted, procedere come segue usando la macchina virtuale o il computer in cui è installato il runtime. Provare quindi a inviare di nuovo il processo da Data Factory.

   Per altre informazioni, vedere [Interfaccia utente Web Ambari](../hdinsight/hdinsight-hadoop-manage-ambari.md#ambari-web-ui).

 </br>

- **Causa** : quando il messaggio di errore contiene un messaggio simile a `User admin is locked out in Ambari` o `Unauthorized: Ambari user name or password is incorrect`, le credenziali di HDInsight sono errate o sono scadute.

- **Raccomandazione** : correggere le credenziali e distribuire di nuovo il servizio collegato. Verificare prima di tutto che le credenziali funzionino in HDInsight aprendo l'URI del cluster in qualsiasi browser e tentando di eseguire l'accesso. Se le credenziali non funzionano, è possibile reimpostarle nel portale di Azure.

   Se si usa un cluster ESP, reimpostare la password con [Reimpostazione password self-service](../active-directory/user-help/active-directory-passwords-update-your-own-password.md).

 </br>

- **Causa** : se il messaggio di errore è simile a `502 - Web server received an invalid response while acting as a gateway or proxy server`, questo errore viene restituito dal servizio HDInsight.

- **Raccomandazione** : quando si arresta il processo del server Ambari, spesso si verifica un errore 502. È possibile riavviare i servizi Ambari riavviando il nodo head.

    1. Connettersi a uno dei nodi su HDInsight tramite SSH.
    1. Identificare l'host del nodo head attivo eseguendo `ping headnodehost`.
    1. Quando il server Ambari si trova sul nodo, connettersi al nodo head attivo tramite SSH. 
    1. Riavviare il nodo head attivo.

       Per altre informazioni, vedere la documentazione sulla risoluzione dei problemi di Microsoft Azure HDInsight. Ad esempio:

       * [Errore 502 dell'interfaccia utente di Ambari](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)
       * [RpcTimeoutException per il server Apache Spark Thrift](../hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception.md)
       * [Risoluzione di errori di gateway non valido nel gateway applicazione](../application-gateway/application-gateway-troubleshooting-502.md).

 </br>

- **Causa** : se il messaggio di errore è simile a `Unable to service the submit job request as templeton service is busy with too many submit job requests` o `Queue root.joblauncher already has 500 applications, cannot accept submission of application`, il numero di processi inviati contemporaneamente a HDInsight è eccessivo.

- **Raccomandazione** : limitare il numero di processi inviati contemporaneamente a HDInsight. Se i processi vengono inviati dalla stessa attività, fare riferimento alla concorrenza delle attività di Data Factory. Modificare i trigger in modo che le esecuzioni simultanee della pipeline vengano distribuite nel tempo.

   Vedere la [documentazione di HDInsight](../hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors.md) per configurare `templeton.parallellism.job.submit` come suggerito dall'errore.

### <a name="error-code-2301"></a>Codice errore: 2301

- **Messaggio** : `Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Causa** : problemi del cluster HDInsight o del servizio.

- **Raccomandazione** : questo errore si verifica quando Azure Data Factory non riceve una risposta dal cluster HDInsight quando tenta di richiedere lo stato del processo in esecuzione. Questo problema potrebbe essere dovuto al cluster stesso oppure a un'interruzione del servizio HDInsight.

   Fare riferimento alla documentazione di risoluzione dei problemi di HDInsight in https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide o contattare l'assistenza per ottenere supporto.

### <a name="error-code-2302"></a>Codice errore: 2302

- **Messaggio** : `Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Causa** : l'errore si è verificato dopo l'invio del processo al cluster HDI.

- **Raccomandazione** : 

 1. Controllare l'interfaccia utente di Ambari:
    1. Verificare che tutti i servizi siano ancora in esecuzione.
    1. Nell'interfaccia utente di Ambari, controllare la sezione degli avvisi della dashboard.
       1. Per altre informazioni su avvisi e relative soluzioni, vedere [Gestione e monitoraggio di un cluster](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
    1. Controllare la memoria YARN. Se l'uso di memoria YARN è alto, l'elaborazione dei processi potrebbe subire ritardi. Se le risorse disponibili non sono sufficienti per gestire l'applicazione o il processo Spark, aumentare il numero di istanze del cluster per garantire che il cluster abbia memoria e core sufficienti. 
 1. Eseguire un processo di esempio.
    1. Se si esegue lo stesso processo nel back-end HDInsight, verificare che sia stato completato. Per esempi di esecuzione, vedere [Eseguire gli esempi di MapReduce inclusi in HDInsight](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md) 
 1. Se il processo non è ancora riuscito in HDInsight, identificare i registri e le informazioni dell'applicazione da fornire all'assistenza:
    1. Controllare se il processo è stato inviato a YARN. In caso contrario, usare `--master yarn`.
    1. Se l'esecuzione dell'applicazione è stata completata, recuperare ora di inizio e di fine dell'applicazione YARN. Se l'esecuzione dell'applicazione non è stata completata, recuperare l'ora di inizio/avvio.
    1. Esaminare e recuperare il registro applicazioni con `yarn logs -applicationId <Insert_Your_Application_ID>`.
    1. Esaminare e recuperare i log di YARN Resource Manager nella directory `/var/log/hadoop-yarn/yarn`.
    1. Se questa procedura non risolve il problema, contattare il team Microsoft Azure HDInsight per supporto e fornire i log indicati con le indicazioni su data e ora.

### <a name="error-code-2303"></a>Codice errore: 2303

- **Messaggio** : `Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Causa** : l'errore si è verificato dopo l'invio del processo al cluster HDI.

- **Raccomandazione** : 

 1. Controllare l'interfaccia utente di Ambari:
    1. Verificare che tutti i servizi siano ancora in esecuzione.
    1. Nell'interfaccia utente di Ambari, controllare la sezione degli avvisi della dashboard.
       1. Per altre informazioni su avvisi e relative soluzioni, vedere [Gestione e monitoraggio di un cluster](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
    1. Controllare la memoria YARN. Se l'uso di memoria YARN è alto, l'elaborazione dei processi potrebbe subire ritardi. Se le risorse disponibili non sono sufficienti per gestire l'applicazione o il processo Spark, aumentare il numero di istanze del cluster per garantire che il cluster abbia memoria e core sufficienti. 
 1. Eseguire un processo di esempio.
    1. Se si esegue lo stesso processo nel back-end HDInsight, verificare che sia stato completato. Per esempi di esecuzione, vedere [Eseguire gli esempi di MapReduce inclusi in HDInsight](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md) 
 1. Se il processo non è ancora riuscito in HDInsight, identificare i registri e le informazioni dell'applicazione da fornire all'assistenza:
    1. Controllare se il processo è stato inviato a YARN. In caso contrario, usare `--master yarn`.
    1. Se l'esecuzione dell'applicazione è stata completata, recuperare ora di inizio e di fine dell'applicazione YARN. Se l'esecuzione dell'applicazione non è stata completata, recuperare l'ora di inizio/avvio.
    1. Esaminare e recuperare il registro applicazioni con `yarn logs -applicationId <Insert_Your_Application_ID>`.
    1. Esaminare e recuperare i log di YARN Resource Manager nella directory `/var/log/hadoop-yarn/yarn`.
    1. Se questa procedura non risolve il problema, contattare il team Microsoft Azure HDInsight per supporto e fornire i log indicati con le indicazioni su data e ora.

### <a name="error-code-2304"></a>Codice errore: 2304

- **Messaggio** : `MSI authentication is not supported on storages for HDI activities.`

- **Causa** : i servizi di archiviazione collegati usati nel servizio HDInsight (HDI) o nell'attività HDI sono configurati con un'autenticazione MSI non supportata.

- **Raccomandazione** : fornire stringhe di connessione complete per gli account di archiviazione usati nel servizio HDI collegato o nell'attività HDI.

### <a name="error-code-2305"></a>Codice errore: 2305

- **Messaggio** : `Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Causa** : le informazioni di connessione per il cluster HDI non sono corrette, l'utente indicato non dispone delle autorizzazioni per eseguire l'azione richiesta o il servizio HDInsight non riesce a rispondere alle richieste di Azure Data Factory.

- **Raccomandazione** : verificare che le informazioni dell'utente siano corrette e che sia possibile aprire l'interfaccia utente di Ambari per il cluster HDI in un browser nella macchina virtuale in cui è installato il runtime di integrazione (in caso di runtime di integrazione self-hosted), o in un qualsiasi computer (per Azure IR).

### <a name="error-code-2306"></a>Codice errore: 2306

- **Messaggio** : `An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Causa** : il file JSON fornito per l'azione di scripting non è valido.

- **Raccomandazione** : il messaggio di errore dovrebbe consentire di identificare il problema. Correggere la configurazione JSON e riprovare.

   Per altre informazioni vedere [Servizio collegato Azure HDInsight su richiesta](./compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

### <a name="error-code-2310"></a>Codice errore: 2310

- **Messaggio** : `Failed to submit Spark job. Error: '%message;'`

- **Causa** : Azure Data Factory ha provato a creare un batch in un cluster Spark usando l'API Livy (livy/batch), ma ha ricevuto un errore.

- **Raccomandazione** : per correggere il problema, attenersi al messaggio di errore. Se le informazioni non sono sufficienti per risolverlo, contattare il team HDI e fornire ID del batch e ID del processo, disponibili nell'output dell'attività eseguita all'interno della pagina di monitoraggio di Azure Data Factory. Per una risoluzione dei problemi più approfondita, recuperare il log completo del processo batch.

   Per altre informazioni su come recuperare il log completo, vedere [Ottenere il log completo di un processo batch](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job).

### <a name="error-code-2312"></a>Codice errore: 2312

- **Messaggio** : `Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Causa** : errore del processo nel cluster HDInsight Spark.

- **Raccomandazione** : per risolvere i problemi di esecuzione nel cluster HDInsight Spark, vedere i collegamenti nell'output dell'attività eseguita all'interno della pagina di monitoraggio di Azure Data Factory. Contattare il team di supporto di HDInsight per ricevere assistenza.

   Per altre informazioni su come recuperare il log completo, vedere [Ottenere il log completo di un processo batch](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job).

### <a name="error-code-2313"></a>Codice errore: 2313

- **Messaggio** : `The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Causa** : il batch è stato eliminato nel cluster HDInsight Spark.

- **Raccomandazione** : risolvere i problemi relativi ai batch nel cluster HDInsight Spark. Contattare l'assistenza di HDInsight per richiedere supporto. 

   Per altre informazioni su come raccogliere il log completo, vedere [Ottenere il log completo di un processo batch](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job) e condividere il log completo con il supporto di HDInsight per un'assistenza più approfondita.

### <a name="error-code-2328"></a>Codice errore: 2328

- **Messaggio** : `Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Causa** : il messaggio di errore include i dettagli relativi al problema.

- **Raccomandazione** : il messaggio di errore dovrebbe consentire di risolvere il problema.

### <a name="error-code-2329"></a>Codice errore: 2329

- **Messaggio** : `Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Causa** : il messaggio di errore include i dettagli relativi al problema.

- **Raccomandazione** : il messaggio di errore dovrebbe consentire di risolvere il problema.

### <a name="error-code-2331"></a>Codice errore: 2331

- **Messaggio** : `The file path should not be null or empty.`

- **Causa** : il percorso del file specificato è vuoto.

- **Raccomandazione** : fornire il percorso di un file esistente.

### <a name="error-code-2340"></a>Codice errore: 2340

- **Messaggio** : `HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Causa** : il servizio HDInsightOnDemand collegato non supporta l'esecuzione tramite il runtime di integrazione self-hosted.

- **Raccomandazione** : selezionare un runtime di integrazione Azure e riprovare.

### <a name="error-code-2341"></a>Codice errore: 2341

- **Messaggio** : `HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Causa** : il formato dell'URL specificato non è corretto.

- **Raccomandazione** : correggere l'URL del cluster e riprovare.

### <a name="error-code-2342"></a>Codice errore: 2342

- **Messaggio** : `Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Causa** : le credenziali specificate non sono corrette per il cluster o si è verificato un problema di configurazione della rete o di connessione, oppure il runtime di integrazione non riesce a collegarsi al cluster.

- **Raccomandazione** : 
    1. verificare che le credenziali siano corrette aprendo l'interfaccia utente di Ambari per il cluster HDInsight in un browser.
    1. Se il cluster si trova in una rete virtuale (VNet) e si usa un runtime di integrazione self-hosted, l'URL di HDI deve essere quello privato nelle reti virtuali e dopo il nome del cluster deve essere specificato "-int".
    
       Ad esempio, sostituire `https://mycluster.azurehdinsight.net/` con `https://mycluster-int.azurehdinsight.net/`. Notare `-int` dopo `mycluster`, ma prima di `.azurehdinsight.net`
    1. Se il cluster si trova nella VNet, si usa il runtime di integrazione self-hosted e l'URL privato e si verificano ancora problemi di connessione, la macchina virtuale in cui è installato il runtime di integrazione non riesce a connettersi a HDI. 
    
       Collegarsi alla macchina virtuale in cui è installato il runtime di integrazione e aprire l'interfaccia utente di Ambari in un browser. Per il cluster, usare l'URL privato. Dal browser la connessione dovrebbe funzionare. In caso contrario, contattare il team di supporto HDInsight per ricevere assistenza.
    1. Se non si usa un runtime di integrazione self-hosted, dovrebbe essere possibile accedere pubblicamente al cluster HDI. Aprire l'interfaccia utente di Ambari in un browser e verificare che si apra correttamente. In caso di problemi con il cluster o i relativi servizi, contattare il team di supporto HDInsight per ricevere assistenza.

       Il runtime di integrazione (self-hosted o di Azure) di Azure Data Factory deve essere in grado di accedere all'URL del cluster HDI usato nel servizio ADF collegato per consentire la connessione di test e il funzionamento delle esecuzioni. Questo stato può essere verificato aprendo l'URL da un browser da una macchina virtuale o da un computer pubblico.

### <a name="error-code-2343"></a>Codice errore: 2343

- **Messaggio** : `User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Motivo** : il nome utente o la password è vuota.

- **Raccomandazione** : specificare le credenziali corrette per la connessione a HDI e riprovare.

### <a name="error-code-2345"></a>Codice errore: 2345

- **Messaggio** : `Failed to read the content of the hive script. Error: '%message;'`

- **Causa** : file di script non esistente o Azure Data Factory non riesce a connettersi al percorso dello script.

- **Raccomandazione** : verificare che lo script sia presente e che le credenziali del servizio collegato associato siano corrette per consentire la connessione.

### <a name="error-code-2346"></a>Codice errore: 2346

- **Messaggio** : `Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Causa** : Azure Data Factory ha tentato di stabilire una connessione ODBC (Open Database Connectivity) al cluster HDI, ma il tentativo non è andato a buon fine a causa di un errore.

- **Raccomandazione** : 

   1. verificare di aver configurato correttamente la connessione ODBC/JDBC (Java Database Connectivity) connection.
      1. Per JDBC, se si usa la stessa rete virtuale, è possibile ottenere la connessione da:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. Per assicurarsi che la configurazione di JDBC sia corretta, vedere [Eseguire una query Apache Hive tramite il driver JDBC in HDInsight](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
      1. Per ODB (Open Database), vedere [Esercitazione: Eseguire una query in Apache Hive con ODBC e PowerShell](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md) per assicurarsi che la configurazione sia corretta. 
   1. Verificare che Hiveserver2, Hive Metastore e Hiveserver2 Interactive siano attivi e in funzione. 
   1. Controllare l'interfaccia utente di Ambari:
      1. Verificare che tutti i servizi siano ancora in esecuzione.
      1. Esaminare la sezione degli avvisi nella dashboard all'interno dell'interfaccia utente di Ambari.
         1. Per altre informazioni su avvisi e relative soluzioni, vedere [Gestione e monitoraggio di un cluster](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
   1. Se questa procedura non ha consentito di risolvere il problema, contattare il team di Microsoft Azure HDInsight.

### <a name="error-code-2347"></a>Codice errore: 2347

- **Messaggio** : `Hive execution through ODBC failed with error message '%message;'.`

- **Causa** : Azure Data Factory ha inviato lo script hive per l'esecuzione al cluster HDI con una connessione ODBC e lo script non è andato a buon fine in HDI.

- **Raccomandazione** : 

   1. verificare di aver configurato correttamente la connessione ODBC/JDBC (Java Database Connectivity) connection.
      1. Per JDBC, se si usa la stessa rete virtuale, è possibile ottenere la connessione da:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. Per assicurarsi che la configurazione di JDBC sia corretta, vedere [Eseguire una query Apache Hive tramite il driver JDBC in HDInsight](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
      1. Per ODB (Open Database), vedere [Esercitazione: Eseguire una query in Apache Hive con ODBC e PowerShell](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md) per assicurarsi che la configurazione sia corretta. 
   1. Verificare che Hiveserver2, Hive Metastore e Hiveserver2 Interactive siano attivi e in funzione. 
   1. Controllare l'interfaccia utente di Ambari:
      1. Verificare che tutti i servizi siano ancora in esecuzione.
      1. Esaminare la sezione degli avvisi nella dashboard all'interno dell'interfaccia utente di Ambari.
         1. Per altre informazioni su avvisi e relative soluzioni, vedere [Gestione e monitoraggio di un cluster](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
   1. Se questa procedura non ha consentito di risolvere il problema, contattare il team di Microsoft Azure HDInsight.

### <a name="error-code-2348"></a>Codice errore: 2348

- **Messaggio** : `The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Causa** : le proprietà del servizio di archiviazione collegato non sono configurate correttamente.

- **Raccomandazione** : nel servizio di archiviazione collegato per le attività HDI sono supportate solo le stringhe di connessione complete. Verificare che non siano in uso autorizzazioni o applicazioni MSI.

### <a name="error-code-2350"></a>Codice errore: 2350

- **Messaggio** : `Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Causa** : le credenziali fornite per la connessione alla risorsa di archiviazione in cui si trova il file non sono corrette o il file non esiste.

- **Raccomandazione** : questo errore si verifica quando Azure Data Factory si prepara per le attività HDI e tenta di copiare i file nella risorsa di archiviazione principale prima di inviare il processo a HDI. Verificare che il file sia presente nel percorso specificato e che la connessione alla risorsa di archiviazione funzioni correttamente. Poiché le attività HDI di Azure Data Factory non supportano l'autenticazione MSI negli account di archiviazione relativi alle attività HDI, verificare che le chiavi dei servizi collegati siano complete o che sia in uso Azure Key Vault.

### <a name="error-code-2351"></a>Codice errore: 2351

- **Messaggio** : `Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Causa** : il file non è presente nel percorso indicato.

- **Raccomandazione** : controllare se il file è presente e che le credenziali del servizio collegato, con le informazioni di connessione che rimandano a questo file, siano corrette.

### <a name="error-code-2352"></a>Codice errore: 2352

- **Messaggio** : `The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Causa** : le proprietà del servizio di archiviazione file collegato non sono configurate correttamente.

- **Raccomandazione** : verificare che le proprietà del servizio di archiviazione file collegato siano configurate correttamente.

### <a name="error-code-2353"></a>Codice errore: 2353

- **Messaggio** : `The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Causa** : le proprietà del servizio di archiviazione script collegato non sono configurate correttamente.

- **Raccomandazione** : verificare che le proprietà del servizio di archiviazione script collegato siano configurate correttamente.

### <a name="error-code-2354"></a>Codice errore: 2354

- **Messaggio** : `The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Causa** : il tipo di servizio di archiviazione collegato non è supportato dall'attività.

- **Raccomandazione** : verificare che il tipo di servizio collegato selezionato sia uno di quelli supportati dall'attività. Le attività HDI supportano i servizi collegati AzureBlobStorage e AzureBlobFSStorage.

   Per altre informazioni, vedere [Confronto delle opzioni di archiviazione disponibili per l'uso con cluster Azure HDInsight](../hdinsight/hdinsight-hadoop-compare-storage-options.md)

### <a name="error-code-2355"></a>Codice errore: 2355

- **Messaggio** : `The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Causa** : il valore specificato per `commandEnvironment` non è corretto.

- **Raccomandazione** : verificare che il valore indicato sia simile a:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Controllare anche che tutte le variabili siano presenti solo una volta nell'elenco.

### <a name="error-code-2356"></a>Codice errore: 2356

- **Messaggio** : `The commandEnvironment already contains a variable named '%variableName;'.`

- **Causa** : il valore specificato per `commandEnvironment` non è corretto.

- **Raccomandazione** : verificare che il valore indicato sia simile a:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Controllare anche che tutte le variabili siano presenti solo una volta nell'elenco.

### <a name="error-code-2357"></a>Codice errore: 2357

- **Messaggio** : `The certificate or password is wrong for ADLS Gen 1 storage.`

- **Causa** : le credenziali specificate non sono corrette.

- **Raccomandazione** : verificare che nelle informazioni di connessione in ADLS Gen 1 sia presente il collegamento al servizio e che la connessione di test vada a buon fine.

### <a name="error-code-2358"></a>Codice errore: 2358

- **Messaggio** : `The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Causa** : il formato del valore indicato per la proprietà richiesta `TimeToLive` non è valido. 

- **Raccomandazione** : aggiornare il valore all'intervallo suggerito e riprovare.

### <a name="error-code-2359"></a>Codice errore: 2359

- **Messaggio** : `The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Causa** : il valore fornito per la proprietà `roles` non è valido.

- **Raccomandazione** : aggiornare il valore con uno di quelli suggeriti e riprovare.

### <a name="error-code-2360"></a>Codice errore: 2360

- **Messaggio** : `The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Causa** : la stringa di connessione specificata per `HCatalogLinkedService` non è valida.

- **Raccomandazione** : aggiornare il valore con una stringa di connessione Azure SQL corretta e riprovare.

### <a name="error-code-2361"></a>Codice errore: 2361

- **Messaggio** : `Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Causa** : creazione del cluster non riuscita, Azure Data Factory non ha ricevuto un errore dal servizio HDInsight.

- **Raccomandazione** : aprire il portale di Azure e cercare la risorsa HDI con il nome fornito, quindi controllare lo stato di provisioning. Contattare il team di supporto di HDInsight per ricevere assistenza.

### <a name="error-code-2362"></a>Codice errore: 2362

- **Messaggio** : `Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Causa** : la risorsa di archiviazione specificata non è un archivio BLOB di Azure.

- **Raccomandazione** : indicare un account di archiviazione BLOB di Azure come account di archiviazione aggiuntivo per il servizio HDInsight su richiesta collegato.

## <a name="web-activity"></a>Attività Web

### <a name="error-code-2128"></a>Codice errore: 2128

- **Messaggio** : `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Causa** : questo problema è causato dalla connettività di rete, da un errore DNS, da un errore di convalida del certificato del server o da un timeout.

- **Raccomandazione** : assicurarsi che l'endpoint che si tenta di raggiungere risponda alle richieste. È possibile usare strumenti come **Fiddler/Postman**.

### <a name="error-code-2108"></a>Codice errore: 2108

- **Messaggio** : `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Causa** : la richiesta non è andata a buon fine a causa di un problema sottostante relativo, ad esempio, alla connettività di rete, a un errore DNS, alla convalida del certificato del server o a un timeout.

- **Raccomandazione** : usare Fiddler/Postman per convalidare la richiesta.

#### <a name="more-details"></a>Altre informazioni
Uso di **Fiddler** per creare una sessione HTTP dell'applicazione Web monitorata:

1. Scaricare, installare e aprire [Fiddler](https://www.telerik.com/download/fiddler).

1. Se l'applicazione Web usa HTTPS, aprire **Tools** (Strumenti) > **Options** (Opzioni) > **HTTPS**.

   1. Nella scheda HTTPS, selezionare sia **Capture HTTPS CONNECTs** (Acquisisci connessioni HTTP) che **Decrypt HTTPS traffic** (Decrittografa traffico HTTPS).

      ![Opzioni di Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Se l'applicazione usa certificati TLS/SSL, aggiungere il certificato di Fiddler al dispositivo.

   Passare a: **Tools** (Strumenti) > **Options** (Opzioni) > **HTTPS** > **Actions** (Azioni) > **Export Root Certificate to Desktop** (Esporta certificato radice su desktop).

1. Disattivare l'acquisizione in **File** > **Capture Traffic** (Acquisisci traffico). In alternativa, premere **F12**.

1. Cancellare la cache del browser in modo che tutti gli elementi presenti vengano rimossi e debbano essere scaricati di nuovo.

1. Creare una richiesta:

1. Selezionare la scheda **Composer** (Compositore).

   1. Configurare il metodo HTTP e l'URL.
 
   1. Se necessario, aggiungere intestazioni e un corpo della richiesta.

   1. Scegliere **Execute** (Esegui).

1. Attivare di nuovo l'acquisizione del traffico e completare la transazione problematica nella pagina.

1. Passare a: **File** > **Save** (Salva) > **All Sessions** (Tutte le sessioni).

Per altre informazioni, vedere [Getting started with Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler) (Introduzione a Fiddler).

## <a name="general"></a>Generale

### <a name="activity-stuck-issue"></a>Problema relativo all'attività bloccata
Quando si osserva che l'attività è in esecuzione molto più a lungo rispetto alle normali esecuzioni senza lo stato di avanzamento, è possibile che si verifichino blocchi. È possibile provare ad annullarla e riprovare a verificare se è utile. Se si tratta di un'attività di copia, è possibile ottenere informazioni sul monitoraggio delle prestazioni e la risoluzione dei problemi da risolvere i problemi relativi alle [prestazioni dell'attività di copia](copy-activity-performance-troubleshooting.md) Se si tratta di un flusso di dati, vedere la Guida alle prestazioni e all'ottimizzazione dei [flussi di dati di mapping](concepts-data-flow-performance.md) .

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi, usare le risorse seguenti:

* [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Richieste di funzionalità di Data Factory](https://feedback.azure.com/forums/270578-data-factory)
* [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)
* [Video di Azure](https://azure.microsoft.com/resources/videos/index/)
* [Pagina delle domande di Domande e risposte Microsoft](/answers/topics/azure-data-factory.html)
