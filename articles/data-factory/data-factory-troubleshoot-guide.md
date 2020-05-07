---
title: Risoluzione dei problemi Azure Data Factory | Microsoft Docs
description: Informazioni su come risolvere i problemi relativi alle attività di controllo esterno in Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: f07cc109b21010df89b105576cb9afcf93df774a
ms.sourcegitcommit: 3beb067d5dc3d8895971b1bc18304e004b8a19b3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/04/2020
ms.locfileid: "82744776"
---
# <a name="troubleshoot-azure-data-factory"></a>Risoluzione dei problemi Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra i metodi comuni per la risoluzione dei problemi per le attività di controllo esterno in Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Connettore e attività di copia

Per problemi relativi al connettore, ad esempio un errore di verifica che usa l'attività di copia, vedere [risolvere i problemi di Azure Data Factory connettori](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code-3200"></a>Codice di errore: 3200

- **Messaggio**: errore 403.

- **Motivo**:`The Databricks access token has expired.`

- **Suggerimento**: per impostazione predefinita, il token di accesso Azure Databricks è valido per 90 giorni. Creare un nuovo token e aggiornare il servizio collegato.

### <a name="error-code-3201"></a>Codice di errore: 3201

- **Messaggio**:`Missing required field: settings.task.notebook_task.notebook_path.`

- **Motivo**:`Bad authoring: Notebook path not specified correctly.`

- **Raccomandazione**: specificare il percorso del notebook nell'attività databricks.

<br/> 

- **Messaggio**:`Cluster... does not exist.`

- **Motivo**:`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Raccomandazione**: verificare che il cluster databricks esista.

<br/> 

- **Messaggio**:`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Motivo**:`Bad authoring.`

- **Raccomandazione**: specificare i percorsi assoluti per gli schemi di indirizzamento dell' `dbfs:/folder/subfolder/foo.py` area di lavoro o per i file archiviati nel file System di databricks (DFS).

<br/> 

- **Messaggio**:`{0} LinkedService should have domain and accessToken as required properties.`

- **Motivo**:`Bad authoring.`

- **Raccomandazione**: verificare la [definizione del servizio collegato](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Messaggio**:`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Motivo**:`Bad authoring.`

- **Raccomandazione**: verificare la [definizione del servizio collegato](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Messaggio**:`Node type Standard_D16S_v3 is not supported. Supported node types: Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2.`

- **Motivo**:`Bad authoring.`

- **Raccomandazione**: fare riferimento al messaggio di errore.

<br/>

### <a name="error-code-3202"></a>Codice di errore: 3202

- **Messaggio**:`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit: 1000 job creations per 3600 seconds.`

- **Motivo**:`Too many Databricks runs in an hour.`

- **Consiglio**: selezionare tutte le pipeline che usano questa area di lavoro di databricks per la velocità di creazione del processo. Se le pipeline avviate un numero eccessivo di databricks vengono eseguite in modo aggregato, eseguire la migrazione di alcune pipeline in una nuova area di lavoro.

<br/> 

- **Messaggio**:`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Motivo**:`Authoring error: No value provided for the parameter.`

- **Consiglio**: esaminare il codice JSON della pipeline e verificare che tutti i parametri nel notebook di baseParameters specifichino un valore non vuoto.

<br/> 

- **Messaggio**: `User: `SimpleUserContext {UserID =..., nome =user@company.com, OrgID =...}` is not authorized to access cluster.`

- **Motivo**: l'utente che ha generato il token di accesso non è autorizzato ad accedere al cluster databricks specificato nel servizio collegato.

- **Consiglio**: assicurarsi che l'utente disponga delle autorizzazioni necessarie nell'area di lavoro.

### <a name="error-code-3203"></a>Codice di errore: 3203

- **Messaggio**:`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Motivo**: il cluster è stato terminato. Per i cluster interattivi, questo problema potrebbe essere un race condition.

- **Suggerimento**: per evitare questo errore, usare i cluster di processi.

### <a name="error-code-3204"></a>Codice di errore: 3204

- **Messaggio**:`Job execution failed.`

- **Cause**: i messaggi di errore indicano vari problemi, ad esempio uno stato del cluster imprevisto o un'attività specifica. Spesso non viene visualizzato alcun messaggio di errore.

- **Raccomandazione**: N/A
 
## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics.

La tabella seguente si applica a U-SQL.
 
### <a name="error-code-2709"></a>Codice di errore: 2709

- **Messaggio**:`The access token is from the wrong tenant.`

- **Motivo**: tenant Azure Active Directory (Azure ad) errato.

- **Raccomandazione**: tenant Azure Active Directory (Azure ad) errato.

<br/>

- **Messaggio**:`We cannot accept your job at this moment. The maximum number of queued jobs for your account is 200. `

- **Causa**: questo errore è causato dalla limitazione delle richieste data Lake Analytics.

- **Raccomandazione**: ridurre il numero di processi inviati a data Lake Analytics. Modificare Data Factory trigger e le impostazioni di concorrenza per le attività o aumentare i limiti di Data Lake Analytics.

<br/> 

- **Messaggio**:`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Causa**: questo errore è causato dalla limitazione delle richieste data Lake Analytics.

- **Raccomandazione**: ridurre il numero di processi inviati a data Lake Analytics. Modificare Data Factory trigger e le impostazioni di concorrenza per le attività o aumentare i limiti di Data Lake Analytics.

### <a name="error-code-2705"></a>Codice di errore: 2705

- **Messaggio**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Motivo**: l'entità servizio o il certificato non ha accesso al file nella risorsa di archiviazione.

- **Raccomandazione**: verificare che l'entità servizio o il certificato fornito dall'utente per i processi di data Lake Analytics abbia accesso sia all'account data Lake Analytics che all'istanza di data Lake storage predefinita dalla cartella radice.

### <a name="error-code-2711"></a>Codice di errore: 2711

- **Messaggio**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Motivo**: l'entità servizio o il certificato non ha accesso al file nella risorsa di archiviazione.

- **Raccomandazione**: verificare che l'entità servizio o il certificato fornito dall'utente per i processi di data Lake Analytics abbia accesso sia all'account data Lake Analytics che all'istanza di data Lake storage predefinita dalla cartella radice.

<br/> 

- **Messaggio**:`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Motivo**: il percorso del file U-SQL non è corretto oppure le credenziali del servizio collegato non hanno accesso.

- **Raccomandazione**: verificare il percorso e le credenziali fornite nel servizio collegato.

### <a name="error-code-2704"></a>Codice di errore: 2704

- **Messaggio**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **Motivo**: l'entità servizio o il certificato non ha accesso al file nella risorsa di archiviazione.

- **Raccomandazione**: verificare che l'entità servizio o il certificato fornito dall'utente per i processi di data Lake Analytics abbia accesso sia all'account data Lake Analytics che all'istanza di data Lake storage predefinita dalla cartella radice.

### <a name="error-code-2707"></a>Codice di errore: 2707

- **Messaggio**:`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and 'DataLakeAnalyticsUri'.`

- **Causa**: l'account data Lake Analytics nel servizio collegato non è corretto.

- **Consiglio**: verificare che venga fornito l'account corretto.

### <a name="error-code-2703"></a>Codice di errore: 2703

- **Messaggio**:`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error Id:").`

- **Motivo**: l'errore è data Lake Analytics.

- **Raccomandazione**: il processo è stato inviato a data Lake Analytics e lo script non è riuscito. Esaminare Data Lake Analytics. Nel portale passare all'account Data Lake Analytics e cercare il processo usando l'ID esecuzione dell'attività Data Factory (non usare l'ID di esecuzione della pipeline). Il processo contiene ulteriori informazioni sull'errore e consente di risolvere i problemi.

   Se la risoluzione non è chiara, contattare il team di supporto di Data Lake Analytics e specificare l'URL (Universal Resource Locator) del processo, che include il nome dell'account e l'ID del processo.
 
## <a name="azure-functions"></a>Funzioni di Azure

### <a name="error-code-3602"></a>Codice di errore: 3602

- **Messaggio**:`Invalid HttpMethod: '%method;'.`

- **Motivo**: il HttpMethod specificato nel payload dell'attività non è supportato dall'attività funzione di Azure.

- **Raccomandazione**: i metodi HTTP supportati sono: Put, post, Get, DELETE, options, Head e Trace.

### <a name="error-code-3603"></a>Codice di errore: 3603

- **Messaggio**:`Response Content is not a valid JObject.`

- **Motivo**: la funzione di Azure chiamata non ha restituito un payload JSON nella risposta. L'attività funzione di Azure Azure Data Factory (ADF) supporta solo il contenuto della risposta JSON.

- **Raccomandazione**: aggiornare la funzione di Azure per restituire un payload JSON valido, ad esempio una funzione C# che può restituire`(ActionResult)new OkObjectResult("{\"Id\":\"123\"}");`

### <a name="error-code-3606"></a>Codice di errore: 3606

- **Messaggio**: attività della funzione di Azure che manca il tasto funzione.

- **Motivo**: la definizione dell'attività funzione di Azure non è completa.

- **Consiglio**: verificare che la definizione JSON dell'attività della funzione di Azure di input `functionKey`includa una proprietà denominata.

### <a name="error-code-3607"></a>Codice di errore: 3607

- **Messaggio**:`Azure function activity missing function name.`

- **Motivo**: la definizione dell'attività funzione di Azure non è completa.

- **Consiglio**: verificare che la definizione JSON dell'attività della funzione di Azure di input `functionName`includa una proprietà denominata.

### <a name="error-code-3608"></a>Codice di errore: 3608

- **Messaggio**:`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Motivo**: i dettagli della funzione di Azure nella definizione di attività potrebbero non essere corretti.

- **Raccomandazione**: correggere i dettagli della funzione di Azure e riprovare.

### <a name="error-code-3609"></a>Codice di errore: 3609

- **Messaggio**:`Azure function activity missing functionAppUrl.`

- **Motivo**: la definizione dell'attività funzione di Azure non è completa.

- **Consiglio**: verificare che la definizione JSON dell'attività della funzione di Azure di input `functionAppUrl`includa una proprietà denominata.

### <a name="error-code-3610"></a>Codice di errore: 3610

- **Messaggio**:`There was an error while calling endpoint.`

- **Motivo**: l'URL della funzione potrebbe non essere corretto.

- **Raccomandazione**: verificare che il valore di `functionAppUrl` nel file JSON dell'attività sia corretto e riprovare.

### <a name="error-code-3611"></a>Codice di errore: 3611

- **Messaggio**:`Azure function activity missing Method in JSON.`

- **Motivo**: la definizione dell'attività funzione di Azure non è completa.

- **Consiglio**: verificare che la definizione JSON dell'attività della funzione di Azure di input `method`includa una proprietà denominata.

### <a name="error-code-3612"></a>Codice di errore: 3612

- **Messaggio**:`Azure function activity missing LinkedService definition in JSON.`

- **Motivo**: la definizione dell'attività funzione di Azure non è completa.

- **Consiglio**: verificare che la definizione JSON dell'attività della funzione di Azure di input includa i dettagli del servizio collegato.

## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code-4101"></a>Codice di errore: 4101

- **Messaggio**:`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Cause**: formato non valido o definizione mancante della `%propertyName;`proprietà.

- **Raccomandazione**: verificare se per l' `%activityName;` attività è stata `%propertyName;` definita la proprietà con i dati corretti.

### <a name="error-code-4110"></a>Codice di errore: 4110

- **Messaggio**:`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Motivo**: la definizione dell'attività AzureMLExecutePipeline non è completa.

- **Consiglio**: verificare che la definizione JSON dell'attività AzureMLExecutePipeline di input abbia correttamente collegato i dettagli del servizio.

### <a name="error-code-4111"></a>Codice di errore: 4111

- **Messaggio**:`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Cause**: definizione di attività non corretta.

- **Consiglio**: verificare che la definizione JSON dell'attività AzureMLExecutePipeline di input abbia correttamente collegato i dettagli del servizio.

### <a name="error-code-4112"></a>Codice di errore: 4112

- **Messaggio**:`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Motivo**: formato non valido o definizione mancante della proprietà '% propertyName;'.

- **Raccomandazione**: controllare se il servizio collegato ha la proprietà `%propertyName;` definita con i dati corretti.

### <a name="error-code-4121"></a>Codice di errore: 4121

- **Messaggio**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Motivo**: la credenziale usata per accedere a Azure Machine Learning è scaduta.

- **Consiglio**: verificare che le credenziali siano valide e riprovare.

### <a name="error-code-4122"></a>Codice di errore: 4122

- **Messaggio**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Motivo**: le credenziali fornite nel servizio collegato Azure Machine Learning non sono valide o non dispongono dell'autorizzazione per l'operazione.

- **Raccomandazione**: verificare che le credenziali nel servizio collegato siano valide e che disponga delle autorizzazioni per accedere a Azure Machine Learning.

### <a name="error-code-4123"></a>Codice di errore: 4123

- **Messaggio**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Motivo**: le proprietà dell'attività, ad esempio `pipelineParameters` , non sono valide per la pipeline Azure Machine Learning (ml).

- **Consiglio**: verificare che il valore delle proprietà dell'attività corrisponda al payload previsto della pipeline di Azure ml pubblicata specificata nel servizio collegato.

### <a name="error-code-4124"></a>Codice di errore: 4124

- **Messaggio**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Motivo**: l'endpoint della pipeline di Azure ml pubblicato non esiste.

- **Raccomandazione**: verificare che nell'Azure Machine Learning sia presente l'endpoint della pipeline di Azure Machine Learning pubblicato specificato nel servizio collegato.

### <a name="error-code-4125"></a>Codice di errore: 4125

- **Messaggio**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Motivo**: si è verificato un errore del server in Azure Machine Learning.

- **Raccomandazione**: riprovare più tardi. Se il problema persiste, contattare il team di Azure Machine Learning per assistenza.

### <a name="error-code-4126"></a>Codice di errore: 4126

- **Messaggio**:`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Motivo**: esecuzione della pipeline di Azure ml non riuscita.

- **Raccomandazione**: controllare Azure Machine Learning per altri log degli errori, quindi correggere la pipeline ml.

## <a name="common"></a>Comuni

### <a name="error-code-2103"></a>Codice di errore: 2103

- **Messaggio**:`Please provide value for the required property '%propertyName;'.`

- **Motivo**: non è stato specificato il valore obbligatorio per la proprietà.

- **Consiglio**: specificare il valore dal messaggio e riprovare.

### <a name="error-code-2104"></a>Codice di errore: 2104

- **Messaggio**:`The type of the property '%propertyName;' is incorrect.`

- **Motivo**: il tipo di proprietà specificato non è corretto.

- **Raccomandazione**: correggere il tipo della proprietà e riprovare.

### <a name="error-code-2105"></a>Codice di errore: 2105

- **Messaggio**:`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Motivo**: il valore della proprietà non è valido o non è nel formato previsto.

- **Raccomandazione**: fare riferimento alla documentazione relativa alla proprietà e verificare che il valore fornito includa il formato e il tipo corretti.

### <a name="error-code-2106"></a>Codice di errore: 2106

- **Messaggio**:`The storage connection string is invalid. %errorMessage;`

- **Motivo**: la stringa di connessione per l'archiviazione non è valida o il formato non è corretto.

- **Suggerimento**: passare alla portale di Azure e trovare l'archiviazione, quindi copiare e incollare la stringa di connessione nel servizio collegato e riprovare.

### <a name="error-code-2108"></a>Codice di errore: 2108

- **Messaggio**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Causa**: la richiesta non è riuscita a causa di un problema sottostante, ad esempio la connettività di rete, l'errore DNS, la convalida del certificato del server o il timeout.

- **Raccomandazione**: usare Fiddler/post per convalidare la richiesta.

### <a name="error-code-2110"></a>Codice di errore: 2110

- **Messaggio**:`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Motivo**: il servizio collegato specificato nell'attività non è corretto.

- **Raccomandazione**: verificare che il tipo di servizio collegato sia uno dei tipi supportati per l'attività. Ad esempio, il tipo di servizio collegato per le attività HDI può essere HDInsight o HDInsightOnDemand.

### <a name="error-code-2111"></a>Codice di errore: 2111

- **Messaggio**:`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Motivo**: il tipo della proprietà specificata non è corretto.

- **Raccomandazione**: correggere il tipo di proprietà e riprovare.

### <a name="error-code-2112"></a>Codice di errore: 2112

- **Messaggio**:`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Motivo**: il tipo di cloud non è supportato o non può essere determinato per l'archiviazione dal EndpointSuffix.

- **Raccomandazione**: usare l'archiviazione in un altro cloud e riprovare.

### <a name="error-code-2128"></a>Codice di errore: 2128

- **Messaggio**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Motivo**: connettività di rete, errore DNS, convalida o timeout del certificato del server.

- **Raccomandazione**: verificare che l'endpoint che si sta tentando di raggiungere risponda alle richieste. È possibile usare strumenti come Fiddler/postazione.

## <a name="custom"></a>Personalizzato

La tabella seguente si applica a Azure Batch.
 
### <a name="error-code-2500"></a>Codice di errore: 2500

- **Messaggio**:`Hit unexpected exception and execution failed.`

- **Motivo**:`Can't launch command, or the program returned an error code.`

- **Consiglio**: verificare che il file eseguibile esista. Se il programma è stato avviato, verificare che *stdout. txt* e *stderr. txt* siano stati caricati nell'account di archiviazione. È consigliabile includere i log nel codice per il debug.

### <a name="error-code-2501"></a>Codice di errore: 2501

- **Messaggio**:`Cannot access user batch account; please check batch account settings.`

- **Cause**: la chiave di accesso batch o il nome del pool non è corretto.

- **Raccomandazione**: verificare il nome del pool e la chiave di accesso batch nel servizio collegato.

### <a name="error-code-2502"></a>Codice di errore: 2502

- **Messaggio**:`Cannot access user storage account; please check storage account settings.`

- **Causa**: il nome o la chiave di accesso dell'account di archiviazione non è corretto.

- **Raccomandazione**: verificare il nome dell'account di archiviazione e la chiave di accesso nel servizio collegato.

### <a name="error-code-2504"></a>Codice di errore: 2504

- **Messaggio**:`Operation returned an invalid status code 'BadRequest'.`

- **Cause**: troppi file nell'oggetto `folderPath` dell'attività personalizzata. Le dimensioni totali di `resourceFiles` non possono contenere più di 32.768 caratteri.

- **Raccomandazione**: rimuovere i file non necessari o comprimerli e aggiungere un comando unzip per estrarli.
   
   Ad esempio, usare `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2505"></a>Codice di errore: 2505

- **Messaggio**:`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Motivo**: le attività personalizzate supportano solo gli account di archiviazione che usano una chiave di accesso.

- **Raccomandazione**: fare riferimento alla descrizione dell'errore.

### <a name="error-code-2507"></a>Codice di errore: 2507

- **Messaggio**:`The folder path does not exist or is empty: ...`

- **Causa**: nessun file si trova nell'account di archiviazione nel percorso specificato.

- **Raccomandazione**: il percorso della cartella deve contenere i file eseguibili che si desidera eseguire.

### <a name="error-code-2508"></a>Codice di errore: 2508

- **Messaggio**:`There are duplicate files in the resource folder.`

- **Motivo**: più file con lo stesso nome si trovano in sottocartelle diverse di folderPath.

- **Raccomandazione**: le attività personalizzate appiattiscono la struttura di cartelle in folderPath. Se è necessario mantenere la struttura di cartelle, comprimere i file ed estrarli in Azure Batch usando un comando unzip.
   
   Ad esempio, usare `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2509"></a>Codice di errore: 2509

- **Messaggio**:`Batch url ... is invalid; it must be in Uri format.`

- **Motivo**: gli URL di batch devono essere simili a`https://mybatchaccount.eastus.batch.azure.com`

- **Raccomandazione**: fare riferimento alla descrizione dell'errore.

### <a name="error-code-2510"></a>Codice di errore: 2510

- **Messaggio**:`An error occurred while sending the request.`

- **Motivo**: l'URL del batch non è valido.

- **Raccomandazione**: verificare l'URL del batch.
 
## <a name="hdinsight"></a>HDInsight

### <a name="error-code-200"></a>Codice di errore: 200

- **Messaggio**:`Unexpected error happened: '%error;'.`

- **Causa**: si è verificato un problema interno del servizio.

- **Consiglio**: per ulteriore assistenza, contattare il supporto di ADF.

### <a name="error-code-201"></a>Codice di errore: 201

- **Messaggio**:`JobType %jobType; is not found.`

- **Motivo**: è disponibile un nuovo tipo di processo non supportato da ADF.

- **Consiglio**: per ulteriore assistenza, contattare il team di supporto di ADF.

### <a name="error-code-202"></a>Codice di errore: 202

- **Messaggio**:`Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Motivo**: il messaggio di errore include i dettagli relativi al problema.

- **Raccomandazione**: i dettagli del messaggio di errore dovrebbero consentire di risolvere il problema. Se non sono disponibili informazioni sufficienti, contattare il supporto di ADF per ulteriore assistenza.

### <a name="error-code-203"></a>Codice di errore: 203

- **Messaggio**:`Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Motivo**: il messaggio di errore include i dettagli relativi al problema.

- **Raccomandazione**: i dettagli del messaggio di errore dovrebbero consentire di risolvere il problema. Se non sono disponibili informazioni sufficienti, contattare il supporto di ADF per ulteriore assistenza.

### <a name="error-code-204"></a>Codice di errore: 204

- **Messaggio**:`The resumption token is missing for runId '%runId;'.`

- **Causa**: si è verificato un problema interno del servizio.

- **Consiglio**: per ulteriore assistenza, contattare il supporto di ADF.

### <a name="error-code-205"></a>Codice di errore: 205

- **Messaggio**:`Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Motivo**: si è verificato un errore durante la creazione del cluster su richiesta HDI.

- **Consiglio**: per ulteriore assistenza, contattare il supporto di ADF.

### <a name="error-code-206"></a>Codice di errore: 206

- **Messaggio**:`The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Causa**: si è verificato un problema interno con il servizio che ha causato l'errore.

- **Raccomandazione**: questo problema potrebbe essere temporaneo. Ripetere il processo. se il problema persiste, contattare il supporto di ADF per ulteriore assistenza.

### <a name="error-code-207"></a>Codice di errore: 207

- **Messaggio**:`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Causa**: si è verificato un errore interno durante il tentativo di determinare l'area dell'account di archiviazione primario.

- **Raccomandazione**: provare con un'altra risorsa di archiviazione. Se questa opzione non è accettabile, contattare il team di supporto di ADF per ulteriore assistenza.

### <a name="error-code-208"></a>Codice di errore: 208

- **Messaggio**:`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Motivo**: si è verificato un errore interno durante il tentativo di leggere l'entità servizio o di creare un'istanza dell'autenticazione MSI.

- **Raccomandazione**: considerare la possibilità di fornire un'entità servizio, che dispone delle autorizzazioni per creare un cluster HDInsight nella sottoscrizione fornita e riprovare. Verificare che le [identità gestite siano configurate correttamente](https://docs.microsoft.com/azure/hdinsight/hdinsight-managed-identities).

   Se questa opzione non è accettabile, contattare il team di supporto di ADF per ulteriore assistenza.

### <a name="error-code-2300"></a>Codice di errore: 2300

- **Messaggio**:`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **Motivo**: il messaggio di errore contiene un messaggio simile `The remote name could not be resolved.`a. L'URI del cluster specificato potrebbe non essere valido.

- **Raccomandazione**: verificare che il cluster non sia stato eliminato e che l'URI fornito sia corretto. Quando si apre l'URI in un browser, viene visualizzata l'interfaccia utente di Ambari. Se il cluster si trova in una rete virtuale, l'URI deve essere l'URI privato. Per aprirlo, usare una macchina virtuale (VM) che fa parte della stessa rete virtuale.

   Per ulteriori informazioni, vedere la pagina relativa [alla connessione diretta ai servizi Apache Hadoop](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services).
 
 </br>

- **Motivo**: se il messaggio di errore contiene un messaggio simile `A task was canceled.`a, si è verificato il timeout dell'invio del processo.

- **Raccomandazione**: il problema potrebbe essere la connettività di rete HDInsight generale o la connettività di rete. Verificare innanzitutto che l'interfaccia utente di HDInsight Ambari sia disponibile da qualsiasi browser. Verificare quindi che le credenziali siano ancora valide.
   
   Se si usa un runtime di integrazione self-hosted (IR), eseguire questo passaggio dalla macchina virtuale o dal computer in cui è installato il runtime di integrazione self-hosted. Provare quindi a inviare di nuovo il processo da Data Factory. Se il problema persiste, contattare il team di Data Factory per assistenza.

   Per altre informazioni, vedere l' [interfaccia utente Web di Ambariri](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-manage-ambari#ambari-web-ui).

 </br>

- **Motivo**: quando il messaggio di errore contiene un messaggio simile `User admin is locked out in Ambari` a `Unauthorized: Ambari user name or password is incorrect`o, le credenziali per HDInsight sono errate o sono scadute.

- **Raccomandazione**: correggere le credenziali e ridistribuire il servizio collegato. Verificare prima di tutto che le credenziali funzionino in HDInsight aprendo l'URI del cluster in qualsiasi browser e tentando di eseguire l'accesso. Se le credenziali non funzionano, è possibile reimpostarle dalla portale di Azure.

   Per il cluster ESP, reimpostare la password tramite la [reimpostazione della password self-service](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-passwords-update-your-own-password).

 </br>

- **Motivo**: quando il messaggio di errore contiene un messaggio simile `502 - Web server received an invalid response while acting as a gateway or proxy server`a, questo errore viene restituito dal servizio HDInsight.

- **Raccomandazione**: si verifica spesso un errore 502 quando il processo del server Ambari è stato arrestato. È possibile riavviare i servizi Ambari riavviando il nodo head.

    1. Connettersi a uno dei nodi in HDInsight usando SSH.
    1. Identificare l'host del nodo head attivo eseguendo `ping headnodehost`.
    1. Connettersi al nodo head attivo perché il server Ambari si trova nel nodo head attivo tramite SSH. 
    1. Riavviare il nodo head attivo.

       Per altre informazioni, vedere la documentazione sulla risoluzione dei problemi di Azure HDInsight. Ad esempio:

       * [Errore 502 dell'interfaccia utente di Ambari](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)
       * [RpcTimeoutException per il server di Apache Spark parsimonia](https://docs.microsoft.com/azure/hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception)
       * [Risoluzione degli errori del gateway non valido nel gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

 </br>

- **Motivo**: quando il messaggio di errore contiene un messaggio simile `Unable to service the submit job request as templeton service is busy with too many submit job requests` a `Queue root.joblauncher already has 500 applications, cannot accept submission of application`o, un numero eccessivo di processi viene inviato a HDInsight nello stesso momento.

- **Raccomandazione**: limitare il numero di processi simultanei inviati a HDInsight. Fare riferimento a Data Factory concorrenza di attività se i processi vengono inviati dalla stessa attività. Modificare i trigger in modo che le esecuzioni di pipeline simultanee vengano distribuite nel tempo.

   Vedere la [documentazione di HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors) per `templeton.parallellism.job.submit` modificare come suggerito dall'errore.

### <a name="error-code-2301"></a>Codice di errore: 2301

- **Messaggio**:`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Motivo**: il cluster o il servizio HDInsight presenta problemi.

- **Raccomandazione**: questo errore si verifica quando ADF non riceve una risposta dal cluster HDInsight durante il tentativo di richiedere lo stato del processo in esecuzione. Questo problema potrebbe essere dovuto al cluster stesso oppure il servizio HDInsight potrebbe avere un'interruzione del servizio.

   Per ulteriori informazioni, vedere la documentazione https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guiderelativa alla risoluzione dei problemi di HDInsight in oppure contattare il supporto tecnico.

### <a name="error-code-2302"></a>Codice di errore: 2302

- **Messaggio**:`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Motivo**: il processo è stato inviato al cluster HDI e non è riuscito.

- **Consiglio**: 

 1. Controllare l'interfaccia utente di Ambari:
    1. Verificare che tutti i servizi siano ancora in esecuzione.
    1. Dall'interfaccia utente di Ambariri, selezionare la sezione Alert nel dashboard.
       1. Per ulteriori informazioni sugli avvisi e le risoluzioni degli avvisi, vedere [gestione e monitoraggio di un cluster](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
    1. Esaminare la memoria YARN. Se la memoria di YARN è elevata, è possibile che l'elaborazione dei processi venga posticipata. Se non si dispone di risorse sufficienti per gestire l'applicazione o il processo Spark, aumentare il numero di dimensioni del cluster per garantire che il cluster disponga di memoria e Core sufficienti. 
 1. Eseguire un processo di test di esempio.
    1. Se si esegue lo stesso processo nel back-end HDInsight, verificare che sia stato completato. Per esempi di esecuzioni di esempio, vedere [eseguire gli esempi di MapReduce inclusi in HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-run-samples-linux) 
 1. Se il processo non è ancora riuscito in HDInsight, verificare i registri dell'applicazione e le informazioni da fornire per supportare:
    1. Controllare se il processo è stato inviato a YARN. Se il processo non è stato inviato a Yarn `--master yarn`, usare.
    1. Se l'applicazione ha terminato l'esecuzione, raccogliere l'ora di inizio e l'ora di fine dell'applicazione YARN. Se l'esecuzione dell'applicazione non è stata completata, raccogliere ora di inizio/ora di avvio.
    1. Controllare e raccogliere il registro applicazioni `yarn logs -applicationId <Insert_Your_Application_ID>`con.
    1. Controllare e raccogliere i log dei Gestione risorse Yarn sotto `/var/log/hadoop-yarn/yarn` la directory.
    1. Se questa procedura non è sufficiente per risolvere il problema, contattare il team di Azure HDInsight per assistenza e fornire i log e i timestamp indicati sopra.

### <a name="error-code-2303"></a>Codice di errore: 2303

- **Messaggio**:`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Motivo**: il processo è stato inviato al cluster HDI e non è riuscito.

- **Consiglio**: 

 1. Controllare l'interfaccia utente di Ambari:
    1. Verificare che tutti i servizi siano ancora in esecuzione.
    1. Dall'interfaccia utente di Ambariri, selezionare la sezione Alert nel dashboard.
       1. Per ulteriori informazioni sugli avvisi e le risoluzioni degli avvisi, vedere [gestione e monitoraggio di un cluster](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
    1. Esaminare la memoria YARN. Se la memoria di YARN è elevata, è possibile che l'elaborazione dei processi venga posticipata. Se non si dispone di risorse sufficienti per gestire l'applicazione o il processo Spark, aumentare il numero di dimensioni del cluster per garantire che il cluster disponga di memoria e Core sufficienti. 
 1. Eseguire un processo di test di esempio.
    1. Se si esegue lo stesso processo nel back-end HDInsight, verificare che sia stato completato. Per esempi di esecuzioni di esempio, vedere [eseguire gli esempi di MapReduce inclusi in HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-run-samples-linux) 
 1. Se il processo non è ancora riuscito in HDInsight, verificare i registri dell'applicazione e le informazioni da fornire per supportare:
    1. Controllare se il processo è stato inviato a YARN. Se il processo non è stato inviato a Yarn `--master yarn`, usare.
    1. Se l'applicazione ha terminato l'esecuzione, raccogliere l'ora di inizio e l'ora di fine dell'applicazione YARN. Se l'esecuzione dell'applicazione non è stata completata, raccogliere ora di inizio/ora di avvio.
    1. Controllare e raccogliere il registro applicazioni `yarn logs -applicationId <Insert_Your_Application_ID>`con.
    1. Controllare e raccogliere i log dei Gestione risorse Yarn sotto `/var/log/hadoop-yarn/yarn` la directory.
    1. Se questa procedura non è sufficiente per risolvere il problema, contattare il team di Azure HDInsight per assistenza e fornire i log e i timestamp indicati sopra.

### <a name="error-code-2304"></a>Codice di errore: 2304

- **Messaggio**:`MSI authentication is not supported on storages for HDI activities.`

- **Motivo**: i servizi collegati di archiviazione usati nel servizio collegato HDINSIGHT (HDI) o nell'attività HDI sono configurati con un'autenticazione MSI non supportata.

- **Consiglio**: fornire stringhe di connessione complete per gli account di archiviazione usati nel servizio collegato HDI o nell'attività HDI.

### <a name="error-code-2305"></a>Codice di errore: 2305

- **Messaggio**:`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Causa**: le informazioni di connessione per il cluster HDI non sono corrette, l'utente specificato non dispone delle autorizzazioni per eseguire l'azione richiesta o il servizio HDInsight presenta problemi di risposta alle richieste provenienti da ADF.

- **Raccomandazione**: verificare che le informazioni utente siano corrette e che l'interfaccia utente di Ambari per il cluster HDI possa essere aperta in un browser dalla macchina virtuale in cui è installato il runtime di integrazione (per un runtime di integrazione self-hosted) o che possa essere aperto da qualsiasi computer (per Azure IR).

### <a name="error-code-2306"></a>Codice di errore: 2306

- **Messaggio**:`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Causa**: il codice JSON fornito per l'azione script non è valido.

- **Raccomandazione**: il messaggio di errore dovrebbe aiutare a identificare il problema. Correggere la configurazione JSON e riprovare.

   Per ulteriori informazioni, controllare [il servizio collegato Azure HDInsight su richiesta](https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service) .

### <a name="error-code-2310"></a>Codice di errore: 2310

- **Messaggio**:`Failed to submit Spark job. Error: '%message;'`

- **Motivo**: ADF ha tentato di creare un batch in un cluster Spark usando l'API Livio (Livio/batch), ma ha ricevuto un errore.

- **Raccomandazione**: seguire il messaggio di errore per risolvere il problema. Se non sono disponibili informazioni sufficienti per risolverlo, contattare il team HDI e fornire l'ID batch e l'ID processo, che si trova nella pagina esecuzione attività nell'output della pagina Monitoraggio ADF. Per la risoluzione dei problemi, raccogliere il log completo del processo batch.

   Per ulteriori informazioni su come raccogliere il log completo, vedere [ottenere il log completo di un processo batch](https://docs.microsoft.com/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job).

### <a name="error-code-2312"></a>Codice di errore: 2312

- **Messaggio**:`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Motivo**: il processo non è riuscito nel cluster HDInsight Spark.

- **Raccomandazione**: seguire i collegamenti nell'output dell'esecuzione dell'attività nella pagina Monitoraggio di ADF per risolvere i problemi relativi all'esecuzione nel cluster HDInsight Spark. Per ulteriore assistenza, contattare il team di supporto di HDInsight.

   Per ulteriori informazioni su come raccogliere il log completo, vedere [ottenere il log completo di un processo batch](https://docs.microsoft.com/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job).

### <a name="error-code-2313"></a>Codice di errore: 2313

- **Messaggio**:`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Motivo**: il batch è stato eliminato nel cluster HDInsight Spark.

- **Raccomandazione**: risolvere i problemi relativi ai batch nel cluster HDInsight Spark. Per ulteriore assistenza, contattare il supporto di HDInsight. 

   Per ulteriori informazioni su come raccogliere il log completo, vedere [ottenere il log completo di un processo batch](https://docs.microsoft.com/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)e condividere il log completo con il supporto di HDInsight per ulteriore assistenza.

### <a name="error-code-2328"></a>Codice di errore: 2328

- **Messaggio**:`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Motivo**: il messaggio di errore dovrebbe mostrare i dettagli relativi al problema.

- **Raccomandazione**: il messaggio di errore dovrebbe essere utile per risolvere il problema.

### <a name="error-code-2329"></a>Codice di errore: 2329

- **Messaggio**:`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Motivo**: il messaggio di errore dovrebbe mostrare i dettagli relativi al problema.

- **Raccomandazione**: il messaggio di errore dovrebbe essere utile per risolvere il problema.

### <a name="error-code-2331"></a>Codice di errore: 2331

- **Messaggio**:`The file path should not be null or empty.`

- **Motivo**: il percorso del file specificato è vuoto.

- **Raccomandazione**: specificare un percorso per un file esistente.

### <a name="error-code-2340"></a>Codice di errore: 2340

- **Messaggio**:`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Motivo**: il servizio collegato HDInsightOnDemand non supporta l'esecuzione tramite SelfHosted IR.

- **Consiglio**: selezionare un Azure IR e riprovare.

### <a name="error-code-2341"></a>Codice di errore: 2341

- **Messaggio**:`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Motivo**: il formato dell'URL specificato non è corretto.

- **Raccomandazione**: correggere l'URL del cluster e riprovare.

### <a name="error-code-2342"></a>Codice di errore: 2342

- **Messaggio**:`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Causa**: le credenziali fornite sono errate per il cluster o si è verificato un problema di connessione o configurazione di rete oppure si sono verificati problemi durante la connessione del runtime di integrazione al cluster.

- **Consiglio**: 
    1. Verificare che le credenziali siano corrette aprendo l'interfaccia utente di Ambari del cluster HDInsight in un browser.
    1. Se il cluster si trova in una rete virtuale (VNet) ed è in uso un runtime di integrazione self-hosted, l'URL HDI deve essere l'URL privato in reti virtuali e deve essere elencato '-int ' dopo il nome del cluster.
    
       Ad esempio, sostituire `https://mycluster.azurehdinsight.net/` con `https://mycluster-int.azurehdinsight.net/`. Si noti `-int` il `mycluster`dopo, ma prima di`.azurehdinsight.net`
    1. Se il cluster si trova in VNet, viene usato il runtime di integrazione self-hosted e l'URL privato è stato usato e la connessione non è ancora riuscita, quindi la macchina virtuale in cui è installato il runtime di integrazione ha riscontrato problemi durante la connessione a HDI. 
    
       Connettersi alla macchina virtuale in cui è installato il runtime di integrazione e aprire l'interfaccia utente di Ambari in un browser. Usare l'URL privato per il cluster. Questa connessione dovrebbe funzionare dal browser. In caso contrario, contattare il team di supporto di HDInsight per ulteriore assistenza.
    1. Se non si usa il runtime di integrazione self-hosted, il cluster HDI dovrebbe essere accessibile pubblicamente. Aprire l'interfaccia utente di Ambari in un browser e verificare che sia aperta. Se si verificano problemi con il cluster o i servizi, contattare il team di supporto di HDInsight per ottenere assistenza.

       L'URL del cluster HDI usato nel servizio collegato di ADF deve essere accessibile per il runtime di integrazione Azure (self-hosted o Azure) per consentire il passaggio della connessione di test e per il corretto funzionamento delle esecuzioni. Questo stato può essere verificato aprendo l'URL da un browser dalla macchina virtuale o da qualsiasi computer pubblico.

### <a name="error-code-2343"></a>Codice di errore: 2343

- **Messaggio**:`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Motivo**: il nome utente o la password sono vuoti.

- **Raccomandazione**: specificare le credenziali corrette per connettersi a HDI e riprovare.

### <a name="error-code-2345"></a>Codice di errore: 2345

- **Messaggio**:`Failed to read the content of the hive script. Error: '%message;'`

- **Motivo**: il file di script non esiste o ADF non è riuscito a connettersi al percorso dello script.

- **Raccomandazione**: verificare che lo script esista e che il servizio collegato associato disponga delle credenziali appropriate per una connessione.

### <a name="error-code-2346"></a>Codice di errore: 2346

- **Messaggio**:`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Motivo**: ADF ha tentato di stabilire una connessione Open Database Connectivity (ODBC) al cluster HDI e non è riuscito con un errore.

- **Consiglio**: 

   1. Verificare di aver configurato correttamente la connessione ODBC/Java Database Connectivity (JDBC).
      1. Per JDBC, se si usa la stessa rete virtuale, è possibile ottenere la connessione da:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. Per assicurarsi di avere configurato JDBC corretto, vedere [eseguire Query Apache hive tramite il driver JDBC in HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver).
      1. Per open database (ODB), vedere [esercitazione: eseguire Query Apache hive con ODBC e PowerShell](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell) per assicurarsi di avere la configurazione corretta. 
   1. Verificare che Hiveserver2, Metastore hive e Hiveserver2 Interactive siano attivi e in funzione. 
   1. Controllare l'interfaccia utente di Ambari:
      1. Verificare che tutti i servizi siano ancora in esecuzione.
      1. Dall'interfaccia utente di Ambariri, selezionare la sezione Alert nel dashboard.
         1. Per ulteriori informazioni sugli avvisi e le risoluzioni degli avvisi, vedere [gestione e monitoraggio di un cluster ](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
   1. Se questa procedura non è sufficiente per risolvere il problema, contattare il team di Azure HDInsight.

### <a name="error-code-2347"></a>Codice di errore: 2347

- **Messaggio**:`Hive execution through ODBC failed with error message '%message;'.`

- **Motivo**: ADF ha inviato lo script hive per l'esecuzione al cluster HDI tramite la connessione ODBC e lo script non è riuscito in HDI.

- **Consiglio**: 

   1. Verificare di aver configurato correttamente la connessione ODBC/Java Database Connectivity (JDBC).
      1. Per JDBC, se si usa la stessa rete virtuale, è possibile ottenere la connessione da:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. Per assicurarsi di avere configurato JDBC corretto, vedere [eseguire Query Apache hive tramite il driver JDBC in HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver).
      1. Per open database (ODB), vedere [esercitazione: eseguire Query Apache hive con ODBC e PowerShell](https://docs.microsoft.com/azure/hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell) per assicurarsi di avere la configurazione corretta. 
   1. Verificare che Hiveserver2, Metastore hive e Hiveserver2 Interactive siano attivi e in funzione. 
   1. Controllare l'interfaccia utente di Ambari:
      1. Verificare che tutti i servizi siano ancora in esecuzione.
      1. Dall'interfaccia utente di Ambariri, selezionare la sezione Alert nel dashboard.
         1. Per ulteriori informazioni sugli avvisi e le risoluzioni degli avvisi, vedere [gestione e monitoraggio di un cluster ](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
   1. Se questa procedura non è sufficiente per risolvere il problema, contattare il team di Azure HDInsight.

### <a name="error-code-2348"></a>Codice di errore: 2348

- **Messaggio**:`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Motivo**: le proprietà del servizio collegato di archiviazione non sono impostate correttamente.

- **Raccomandazione**: solo le stringhe di connessione complete sono supportate nel servizio collegato archiviazione principale per le attività HDI. Verificare che non si stiano utilizzando autorizzazioni o applicazioni MSI.

### <a name="error-code-2350"></a>Codice di errore: 2350

- **Messaggio**:`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Motivo**: le credenziali fornite per connettersi alla risorsa di archiviazione in cui si trovano i file non sono corrette oppure i file non esistono.

- **Raccomandazione**: questo errore si verifica quando ADF si prepara per le attività HDI e tenta di copiare i file nella risorsa di archiviazione principale prima di inviare il processo a HDI. Verificare che i file esistano nel percorso specificato e che la connessione di archiviazione sia corretta. Poiché le attività HDI di ADF non supportano l'autenticazione MSI negli account di archiviazione correlati alle attività HDI, verificare che i servizi collegati dispongano di chiavi complete o utilizzino Azure Key Vault.

### <a name="error-code-2351"></a>Codice di errore: 2351

- **Messaggio**:`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Motivo**: il file non esiste nel percorso specificato.

- **Consiglio**: controllare se il file esiste effettivamente e che il servizio collegato con le informazioni di connessione che punta a questo file disponga delle credenziali corrette.

### <a name="error-code-2352"></a>Codice di errore: 2352

- **Messaggio**:`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Motivo**: le proprietà del servizio collegato archiviazione file non sono impostate correttamente.

- **Raccomandazione**: verificare che le proprietà del servizio collegato archiviazione file siano configurate correttamente.

### <a name="error-code-2353"></a>Codice di errore: 2353

- **Messaggio**:`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Motivo**: le proprietà del servizio collegato di archiviazione script non sono impostate correttamente.

- **Raccomandazione**: verificare che le proprietà del servizio collegato archiviazione script siano configurate correttamente.

### <a name="error-code-2354"></a>Codice di errore: 2354

- **Messaggio**:`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Motivo**: il tipo di servizio collegato di archiviazione non è supportato dall'attività.

- **Raccomandazione**: verificare che il servizio collegato selezionato disponga di uno dei tipi supportati per l'attività. Le attività HDI supportano i servizi collegati AzureBlobStorage e AzureBlobFSStorage.

   Per altre informazioni, vedere [confrontare le opzioni di archiviazione per l'uso con i cluster HDInsight di Azure](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-compare-storage-options)

### <a name="error-code-2355"></a>Codice di errore: 2355

- **Messaggio**:`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Motivo**: il valore specificato per `commandEnvironment` non è corretto.

- **Raccomandazione**: verificare che il valore fornito sia simile al seguente:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Verificare inoltre che ogni variabile venga visualizzata solo una volta nell'elenco.

### <a name="error-code-2356"></a>Codice di errore: 2356

- **Messaggio**:`The commandEnvironment already contains a variable named '%variableName;'.`

- **Motivo**: il valore specificato per `commandEnvironment` non è corretto.

- **Raccomandazione**: verificare che il valore fornito sia simile al seguente:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Verificare inoltre che ogni variabile venga visualizzata solo una volta nell'elenco.

### <a name="error-code-2357"></a>Codice di errore: 2357

- **Messaggio**:`The certificate or password is wrong for ADLS Gen 1 storage.`

- **Motivo**: le credenziali specificate non sono corrette.

- **Raccomandazione**: verificare che le informazioni di connessione in ADLS generazione 1 siano collegate al servizio e verificare che la connessione di test abbia esito positivo.

### <a name="error-code-2358"></a>Codice di errore: 2358

- **Messaggio**:`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Motivo**: il formato del valore specificato per la `TimeToLive` proprietà Required non è valido. 

- **Raccomandazione**: aggiornare il valore nell'intervallo suggerito e riprovare.

### <a name="error-code-2359"></a>Codice di errore: 2359

- **Messaggio**:`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Motivo**: il valore specificato per la proprietà `roles` non è valido.

- **Raccomandazione**: aggiornare il valore in modo che sia uno dei suggerimenti e riprovare.

### <a name="error-code-2360"></a>Codice di errore: 2360

- **Messaggio**:`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Motivo**: la stringa di connessione specificata per `HCatalogLinkedService` l'oggetto non è valida.

- **Raccomandazione**: aggiornare il valore a una stringa di connessione SQL di Azure corretta e riprovare.

### <a name="error-code-2361"></a>Codice di errore: 2361

- **Messaggio**:`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Motivo**: la creazione del cluster non è riuscita e l'ADF non ha restituito un errore dal servizio HDInsight.

- **Consiglio**: aprire il portale di Azure e provare a trovare la risorsa HDI con il nome fornito, quindi controllare lo stato del provisioning. Per ulteriore assistenza, contattare il team di supporto di HDInsight.

### <a name="error-code-2362"></a>Codice di errore: 2362

- **Messaggio**:`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Motivo**: lo spazio di archiviazione aggiuntivo fornito non è un archivio BLOB di Azure.

- **Raccomandazione**: fornire un account di archiviazione BLOB di Azure come risorsa di archiviazione aggiuntiva per il servizio collegato HDInsight su richiesta.

## <a name="web-activity"></a>Attività Web

### <a name="error-code-2128"></a>Codice di errore: 2128

- **Messaggio**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Causa**: il problema è causato dalla connettività di rete, da un errore DNS, da una convalida del certificato del server o da un timeout.

- **Raccomandazione**: verificare che l'endpoint che si sta tentando di raggiungere risponda alle richieste. È possibile usare strumenti come **Fiddler/postazione**.

### <a name="error-code-2108"></a>Codice di errore: 2108

- **Messaggio**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Causa**: la richiesta non è riuscita a causa di un problema sottostante, ad esempio la connettività di rete, un errore DNS, una convalida del certificato del server o un timeout.

- **Raccomandazione**: usare Fiddler/post per convalidare la richiesta.

#### <a name="more-details"></a>Altre informazioni
Per usare **Fiddler** per creare una sessione HTTP dell'applicazione Web monitorata:

1. Scaricare, installare e aprire [Fiddler](https://www.telerik.com/download/fiddler).

1. Se l'applicazione Web usa HTTPS, passare a **strumenti** > **Fiddler opzioni** > **https**.

   1. Nella scheda HTTPS selezionare entrambe le funzionalità di **acquisizione HTTPS connette** e **decrittografare il traffico HTTPS**.

      ![Opzioni Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Se l'applicazione usa certificati TLS/SSL, aggiungere il certificato Fiddler al dispositivo.

   Passare a: **strumenti** > **Fiddler opzioni** > **HTTPS** > **azione** > HTTPS**Esporta certificato radice sul desktop**.

1. Per disattivare l'acquisizione, passare a **file** > **Acquisisci traffico**. Oppure premere **F12**.

1. Cancellare la cache del browser in modo che tutti gli elementi memorizzati nella cache vengano rimossi e che sia necessario scaricarli nuovamente.

1. Creare una richiesta:

1. Selezionare la scheda **Composer** .

   1. Impostare il metodo e l'URL HTTP.
 
   1. Se necessario, aggiungere intestazioni e un corpo della richiesta.

   1. Selezionare **Esegui**.

1. Riattivare l'acquisizione del traffico e completare la transazione problematica nella pagina.

1. Vai a: **file** > **Salva** > **tutte le sessioni**.

Per ulteriori informazioni, vedere la pagina relativa all' [Introduzione a Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

* [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Richieste di funzionalità Data Factory](https://feedback.azure.com/forums/270578-data-factory)
* [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Informazioni su Twitter sui Data Factory](https://twitter.com/hashtag/DataFactory)
* [Video di Azure](https://azure.microsoft.com/resources/videos/index/)
* [Forum MSDN](https://social.msdn.microsoft.com/Forums/home)
