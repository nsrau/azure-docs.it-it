---
title: Risolvere i problemi relativi a Azure Data Factory Documenti Microsoft
description: Informazioni su come risolvere i problemi relativi alle attività di controllo esterno in Azure Data Factory.Learn how to troubleshoot external control activities in Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: e284060893e00ed7459edd0d1a03075c813dc5b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80065387"
---
# <a name="troubleshoot-azure-data-factory"></a>Risolvere i problemi di Azure Data FactoryTroubleshoot Azure Data Factory

Questo articolo illustra i metodi di risoluzione dei problemi comuni per le attività di controllo esterno in Azure Data Factory.This article explores common troubleshooting methods for external control activities in Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Attività di copia e connettore

Per problemi relativi [ai connettori,](connector-troubleshoot-guide.md)ad esempio se si verificano errori durante l'utilizzo dell'attività di copia, vedere Risoluzione dei problemi relativi ai connettori di Azure Data Factory .
  

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Codice di errore: 3200

- **Messaggio**: Errore 403.

- **Causa**:`The Databricks access token has expired.`

- **Consiglio:** per impostazione predefinita, il token di accesso di Azure Databricks è valido per 90 giorni. Creare un nuovo token e aggiornare il servizio collegato.


### <a name="error-code--3201"></a>Codice di errore: 3201

- **Messaggio**:`Missing required field: settings.task.notebook_task.notebook_path.`

- **Causa**:`Bad authoring: Notebook path not specified correctly.`

- **Consiglio:** specificare il percorso del blocco appunti nell'attività Databricks.

<br/>  

- **Messaggio**:`Cluster... does not exist.`

- **Causa**:`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Consiglio:** verificare che il cluster Databricks esista.

<br/>  

- **Messaggio**:`Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **Causa**:`Bad authoring.`

- **Raccomandazione:** specificare i percorsi assoluti per gli schemi di indirizzamento dell'area di lavoro o `dbfs:/folder/subfolder/foo.py` per i file archiviati nel file system Databricks.

<br/>  

- **Messaggio**:`{0} LinkedService should have domain and accessToken as required properties.`

- **Causa**:`Bad authoring.`

- **Consiglio:** verificare la [definizione](compute-linked-services.md#azure-databricks-linked-service)del servizio collegato .

<br/>  

- **Messaggio**:`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Causa**:`Bad authoring.`

- **Consiglio:** verificare la [definizione](compute-linked-services.md#azure-databricks-linked-service)del servizio collegato .

<br/>  

- **Messaggio**:`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Causa**:`Bad authoring.`

- **Raccomandazione**: Fare riferimento al messaggio di errore.

<br/>

### <a name="error-code--3202"></a>Codice di errore: 3202

- **Messaggio**:`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Causa**:`Too many Databricks runs in an hour.`

- **Consiglio:** controllare tutte le pipeline che utilizzano questo spazio di lavoro Databricks per la frequenza di creazione di processi.  Se le pipeline avviate vengono avviate con un numero di pipeline di dati di dimensioni ineguagliato da un numero di pipeline, eseguire la migrazione di alcune pipeline in una nuova area di lavoro.

<br/>  

- **Messaggio**:`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Causa**:`Authoring error: No value provided for the parameter.`

- **Consiglio:** esaminare il codice JSON della pipeline e verificare che tutti i parametri nel blocco appunti baseParameters specifichino un valore non vuoto.

<br/>  

- **Messaggio** `User: `: SimpleUserContext , userIduser@company.com... , nome , orgId , ... .` is not   authorized to access cluster.`

- **Causa:** l'utente che ha generato il token di accesso non può accedere al cluster Databricks specificato nel servizio collegato.

- **Consiglio:** verificare che l'utente disponga delle autorizzazioni necessarie nell'area di lavoro.


### <a name="error-code--3203"></a>Codice di errore: 3203

- **Messaggio**:`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Causa**: il cluster è stato terminato. Per i cluster interattivi, potrebbe trattarsi di una race condition.

- **Consiglio:** il modo migliore per evitare questo errore consiste nell'utilizzare i cluster di processi.


### <a name="error-code--3204"></a>Codice di errore: 3204

- **Messaggio**:`Job execution failed.`

- **Causa:** i messaggi di errore indicano vari problemi, ad esempio uno stato imprevisto del cluster o un'attività specifica. Il più delle volte non viene visualizzato alcun messaggio di errore.

- **Raccomandazione**: N/D
            

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics.

La tabella seguente si applica a U-SQL.
      
### <a name="error-code--2709"></a>Codice di errore: 2709

- **Messaggio**:`The access token is from the wrong tenant.`

- **Causa:** tenant di Azure Active Directory (Azure AD) non corretto.

- **Raccomandazione:** tenant di Azure Active Directory (Azure AD) non corretto.

<br/>

- **Messaggio**:`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Causa:** questo errore è causato dalla limitazione di Data Lake Analytics.

- **Consiglio:** ridurre il numero di processi inviati a Data Lake Analytics modificando i trigger di Data Factory e le impostazioni di concorrenza sulle attività. Oppure aumenta i limiti di Data Lake Analytics.

<br/>  

- **Messaggio**:`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Causa:** questo errore è causato dalla limitazione di Data Lake Analytics.

- **Consiglio:** ridurre il numero di processi inviati a Data Lake Analytics modificando i trigger di Data Factory e le impostazioni di concorrenza sulle attività. Oppure aumenta i limiti di Data Lake Analytics.


### <a name="error-code--2705"></a>Codice di errore: 2705

- **Messaggio**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Causa:** l'entità servizio o il certificato non ha accesso al file nell'archivio.

- **Consiglio: verificare**che l'entità servizio o il certificato fornito dall'utente per i processi di Data Lake Analytics abbia accesso all'account Data Lake Analytics e all'istanza predefinita di Data Lake Storage dalla cartella radice.


### <a name="error-code--2711"></a>Codice di errore: 2711

- **Messaggio**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Causa:** l'entità servizio o il certificato non ha accesso al file nell'archivio.

- **Consiglio: verificare**che l'entità servizio o il certificato fornito dall'utente per i processi di Data Lake Analytics abbia accesso all'account Data Lake Analytics e all'istanza predefinita di Data Lake Storage dalla cartella radice.

<br/>  

- **Messaggio**:`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Causa**: il percorso del file U-SQL è errato o le credenziali del servizio collegato non hanno accesso.

- **Consiglio:** verificare il percorso e le credenziali forniti nel servizio collegato.


### <a name="error-code--2704"></a>Codice di errore: 2704

- **Messaggio**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/>  <br/>  User is   not able to access Data Lake Store.  <br/>  <br/>  User is  not authorized to use Data Lake Analytics.`

- **Causa:** l'entità servizio o il certificato non ha accesso al file nell'archivio.

- **Consiglio: verificare**che l'entità servizio o il certificato fornito dall'utente per i processi di Data Lake Analytics abbia accesso all'account Data Lake Analytics e all'istanza predefinita di Data Lake Storage dalla cartella radice.


### <a name="error-code--2707"></a>Codice di errore: 2707

- **Messaggio**:`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Causa:** l'account Data Lake Analytics nel servizio collegato è errato.

- **Consiglio:** verificare che venga fornito l'account corretto.


### <a name="error-code--2703"></a>Codice di errore: 2703

- **Messaggio**:`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Causa**: L'errore proviene da Data Lake Analytics.

- **Raccomandazione:** un errore come l'esempio indica che il processo è stato inviato a Data Lake Analytics e lo script non è riuscito. Analizzare data Lake Analytics.Investigate in Data Lake Analytics. Nel portale passare all'account Data Lake Analytics e cercare il processo usando l'ID di esecuzione dell'attività di Data Factory (non l'ID di esecuzione della pipeline). Il lavoro fornisce ulteriori informazioni sull'errore e vi aiuterà a risolvere i problemi. Se la risoluzione non è chiara, contatta il team di supporto di Data Lake Analytics e fornisci l'URL del processo, che include il nome dell'account e l'ID processo.
          

## <a name="azure-functions"></a>Funzioni di Azure

### <a name="error-code--3602"></a>Codice di errore: 3602

- **Messaggio**:`Invalid HttpMethod: '%method;'.`

- **Causa:** il metodo Http specificato nel payload dell'attività non è supportato dall'attività delle funzioni di Azure.Cause : Http method specified in the activity payload is not supported by Azure Function Activity.

- **Raccomandazione**: I metodi Http supportati sono PUT, POST, GET, DELETE, OPTIONS, HEAD e TRACE.


### <a name="error-code--3603"></a>Codice di errore: 3603

- **Messaggio**:`Response Content is not a valid JObject.`

- **Causa:** la funzione di Azure chiamata non ha restituito un payload JSON nella risposta. L'attività della funzione ADF Azure supporta solo il contenuto della risposta JSON.

- **Raccomandazione:** aggiornare la funzione di Azure in modo da restituire un payload JSON valido,\"\"ad\"esempio\"una funzione di C, può restituire (ActionResult)new OkObjectResult("ID: 123 ");


### <a name="error-code--3606"></a>Codice di errore: 3606

- **Messaggio:** Tasto funzione mancante per l'attività della funzione di Azure.Message: Azure function activity missing function key.

- **Causa:** la definizione dell'attività della funzione di Azure non è completa.

- **Consiglio:** controllare la definizione JSON dell'attività AzureFunction di input con proprietà denominata 'functionKey'.


### <a name="error-code--3607"></a>Codice di errore: 3607

- **Messaggio**:`Azure function activity missing function name.`

- **Causa:** la definizione dell'attività della funzione di Azure non è completa.

- **Consiglio:** controllare la definizione JSON dell'attività AzureFunction di input con proprietà denominata 'functionName'.


### <a name="error-code--3608"></a>Codice di errore: 3608

- **Messaggio**:`Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **Causa:** i dettagli delle funzioni di Azure nella definizione dell'attività potrebbero non essere corretti.

- **Consiglio:** correggere i dettagli della funzione azure e riprovare.


### <a name="error-code--3609"></a>Codice di errore: 3609

- **Messaggio**:`Azure function activity missing functionAppUrl.`

- **Causa:** la definizione dell'attività della funzione di Azure non è completa.

- **Consiglio:** controllare che la definizione JSON dell'attività AzureFunction di input abbia una proprietà denominata 'functionAppUrl'.


### <a name="error-code--3610"></a>Codice di errore: 3610

- **Messaggio**:`There was an error while calling endpoint.`

- **Causa**: l'URL della funzione potrebbe non essere corretto.

- **Consiglio:** assicurarsi che il valore di 'functionAppUrl' nell'attività JSON sia corretto e riprovare.


### <a name="error-code--3611"></a>Codice di errore: 3611

- **Messaggio**:`Azure function activity missing Method in JSON.`

- **Causa:** la definizione dell'attività della funzione di Azure non è completa.

- **Raccomandazione:** verificare che la definizione JSON dell'attività AzureFunction di input abbia una proprietà denominata 'metodo'.


### <a name="error-code--3612"></a>Codice di errore: 3612

- **Messaggio**:`Azure function activity missing LinkedService definition in JSON.`

- **Causa:** la definizione dell'attività della funzione di Azure non è completa.

- **Consiglio:** verificare che la definizione JSON dell'attività AzureFunction di input contivi dettagli del servizio collegato.



## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code--4101"></a>Codice di errore: 4101

- **Messaggio**:`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Causa:** formato non valido o definizione mancante della proprietà '%propertyName;'.

- **Consiglio:** verificare se l'attività '%activityName;' ha la proprietà '%propertyName;' definita con i dati corretti.


### <a name="error-code--4110"></a>Codice di errore: 4110

- **Messaggio**:`AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **Causa:** la definizione dell'attività AzureMLExecutePipeline non è completa.

- **Consiglio:** verificare se l'attività di input dell'attività AzureMLExecutePipeline La definizione JSON ha i dettagli del servizio collegato.


### <a name="error-code--4111"></a>Codice di errore: 4111

- **Messaggio**:`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Causa**: Definizione di attività non corretta.

- **Consiglio:** verificare se l'attività di AzureMLExecutePipeline di input La definizione JSON ha i dettagli del servizio collegato corretti.


### <a name="error-code--4112"></a>Codice di errore: 4112

- **Messaggio**:`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Causa:** formato non valido o definizione mancante della proprietà '%propertyName;'.

- **Consiglio:** verificare se al servizio collegato è definita la proprietà '%propertyName;' con i dati corretti.


### <a name="error-code--4121"></a>Codice di errore: 4121

- **Messaggio**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa:** le credenziali usate per accedere ad Azure Machine Learning sono scadute.

- **Consiglio:** verificare che le credenziali siano valide e riprovare


### <a name="error-code--4122"></a>Codice di errore: 4122

- **Messaggio**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa:** le credenziali fornite nel servizio collegato di Azure Machine Learning non sono valide o non dispongono dell'autorizzazione per l'operazione.

- **Consiglio:** verificare che le credenziali nel servizio collegato siano valide e dispongano dell'autorizzazione per accedere ad Azure Machine Learning.Recommendation : Please verify credential in Linked Service is valid and has permission to access Azure Machine Learning.


### <a name="error-code--4123"></a>Codice di errore: 4123

- **Messaggio**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa:** le proprietà dell'attività, ad esempio pipelineParameters, non sono valide per la pipeline di Azure ML.

- **Consiglio:** controllare il valore delle proprietà dell'attività in modo che corrisponda al payload previsto della pipeline di Azure ML pubblicata specificata in Servizio collegato.


### <a name="error-code--4124"></a>Codice di errore: 4124

- **Messaggio**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa:** l'endpoint della pipeline di Azure ML pubblicato non esiste.

- **Consiglio:** verificare che l'endpoint della pipeline di Azure Machine Learning pubblicato specificato in Servizio collegato esista in Azure Machine Learning.Recommendation: Please verify the published Azure Machine Learning pipeline endpoint specified in Linked Service exists in Azure Machine Learning.


### <a name="error-code--4125"></a>Codice di errore: 4125

- **Messaggio**:`Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **Causa:** errore del server in Azure Machine Learning.

- **Consiglio:** riprova più tardi. Contattare il team di Azure Machine Learning per assistenza in caso di problemi.


### <a name="error-code--4126"></a>Codice di errore: 4126

- **Messaggio**:`Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **Causa:** l'esecuzione della pipeline di Azure ML non è riuscita.

- **Consiglio:** verificare in Azure Machine Learning altri log degli errori e correggere la pipeline di Machine Learning.



## <a name="common"></a>Comuni

### <a name="error-code--2103"></a>Codice di errore: 2103

- **Messaggio**:`Please provide value for the required property '%propertyName;'.`

- **Causa**: il valore della proprietà non è stato fornito, tuttavia è obbligatorio nello scenario.

- **Consiglio:** fornire il valore del messaggio e riprovare.


### <a name="error-code--2104"></a>Codice di errore: 2104

- **Messaggio**:`The type of the property '%propertyName;' is incorrect.`

- **Causa**: il tipo della proprietà fornita non è quello previsto.

- **Consiglio**: Correggere il tipo di proprietà e riprovare.


### <a name="error-code--2105"></a>Codice di errore: 2105

- **Messaggio**:`An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **Causa**: il valore della proprietà non è valido o non ha il formato previsto.

- **Raccomandazione**: Cercare la documentazione per la proprietà e assicurarsi che il valore fornito ha previsto il formato e il tipo.


### <a name="error-code--2106"></a>Codice di errore: 2106

- **Messaggio**:`The storage connection string is invalid. %errorMessage;`

- **Causa:** la stringa di connessione per l'archiviazione non è valida o ha un formato non corretto.

- **Consiglio:** passare al portale di Azure, trovare lo spazio di archiviazione, copiare la stringa di connessione e incollare nel servizio collegato e riprovare.


### <a name="error-code--2108"></a>Codice di errore: 2108

- **Messaggio**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Causa:** la richiesta non è riuscita a causa di un problema sottostante, ad esempio la connettività di rete, l'errore DNS, la convalida del certificato server o il timeout.

- **Consiglio:** utilizzare Fiddler/Postman per convalidare la richiesta.


### <a name="error-code--2110"></a>Codice di errore: 2110

- **Messaggio**:`The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **Causa:** il servizio collegato specificato nell'attività è errato.

- **Consiglio: assicurarsi**che il tipo di servizio collegato sia uno dei tipi supportati per l'attività. For example, for HDI activities the linked service type can be HDInsight or HDInsightOnDemand.


### <a name="error-code--2111"></a>Codice di errore: 2111

- **Messaggio**:`The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **Causa**: il tipo della proprietà fornita non è quello previsto.

- **Consiglio**: Correggere il tipo di proprietà e riprovare.


### <a name="error-code--2112"></a>Codice di errore: 2112

- **Messaggio**:`The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **Causa:** il tipo di cloud non è supportato o non è stato possibile determinare per l'archiviazione da EndpointSuffix.

- **Consiglio:** utilizzare l'archiviazione in un altro cloud e riprovare.


### <a name="error-code--2128"></a>Codice di errore: 2128

- **Messaggio**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Causa:** connettività di rete, errore DNS, convalida del certificato server o timeout.

- **Consiglio:** verificare che l'endpoint che si sta tentando di raggiungere risponda alle richieste. È possibile utilizzare strumenti come Fiddler/Postman.



## <a name="custom"></a>Personalizzato

La tabella seguente si applica ad Azure Batch.The following table applies to Azure Batch.
      
### <a name="error-code--2500"></a>Codice di errore: 2500

- **Messaggio**:`Hit unexpected exception and execution failed.`

- **Causa**:`Can't launch command, or the program returned an error code.`

- **Consiglio**: Assicurarsi che il file eseguibile esista. Se il programma è stato avviato, assicurarsi che *stdout.txt* e *stderr.txt* siano stati caricati nell'account di archiviazione. È consigliabile generare log copiosi nel codice per il debug.


### <a name="error-code--2501"></a>Codice di errore: 2501

- **Messaggio**:`Cannot access user batch account; please check batch account settings.`

- **Causa:** chiave di accesso batch o nome del pool non corretto.

- **Consiglio:** verificare il nome del pool e la chiave di accesso Batch nel servizio collegato.


### <a name="error-code--2502"></a>Codice di errore: 2502

- **Messaggio**:`Cannot access user storage account; please check storage account settings.`

- **Causa:** nome dell'account di archiviazione o chiave di accesso non corretto.

- **Consiglio:** verificare il nome dell'account di archiviazione e la chiave di accesso nel servizio collegato.


### <a name="error-code--2504"></a>Codice di errore: 2504

- **Messaggio**:`Operation returned an invalid status code 'BadRequest'.`

- **Causa**: Troppi file nella cartellaPercorso dell'attività personalizzata. La dimensione totale di resourceFiles non può essere superiore a 32.768 caratteri.

- **Consiglio:** rimuovere i file non necessari. O zip e aggiungere un comando di decomprimere per estrarli. Ad esempio, utilizzare`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Codice di errore: 2505

- **Messaggio**:`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Causa:** le attività personalizzate supportano solo account di archiviazione che usano una chiave di accesso.

- **Raccomandazione**: Fare riferimento alla descrizione dell'errore.


### <a name="error-code--2507"></a>Codice di errore: 2507

- **Messaggio**:`The folder path does not exist or is empty: ...`

- **Causa:** nell'account di archiviazione non si trova alcun file nel percorso specificato.

- **Consiglio:** il percorso della cartella deve contenere i file eseguibili che si desidera eseguire.


### <a name="error-code--2508"></a>Codice di errore: 2508

- **Messaggio**:`There are duplicate files in the resource folder.`

- **Causa**: Più file con lo stesso nome si trovano in sottocartelle diverse di folderPath.

- **Consiglio:** le attività personalizzate appiattiscono la struttura delle cartelle in folderPath.  Se è necessario mantenere la struttura di cartelle, comprimere i file ed estrarli in Azure Batch usando un comando di decompressione. Ad esempio, utilizzare`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Codice di errore: 2509

- **Messaggio**:`Batch   url ... is invalid; it must be in Uri format.`

- **Causa**: gli URL batch devono essere simili a`https://mybatchaccount.eastus.batch.azure.com`

- **Raccomandazione**: Fare riferimento alla descrizione dell'errore.


### <a name="error-code--2510"></a>Codice di errore: 2510

- **Messaggio**:`An   error occurred while sending the request.`

- **Causa:** l'URL del batch non è valido.

- **Consiglio:** verificare l'URL del batch.
            

## <a name="hdinsight"></a>HDInsight

### <a name="error-code--200"></a>Codice di errore: 200

- **Messaggio**:`Unexpected error happened: '%error;'.`

- **Causa**: Si è verificato un problema interno del servizio.

- **Raccomandazione**: Si prega di contattare il supporto ADF per ulteriore assistenza.


### <a name="error-code--201"></a>Codice di errore: 201

- **Messaggio**:`JobType %jobType; is not found.`

- **Causa:** esiste un nuovo tipo di processo non supportato da ADF.

- **Raccomandazione**: Si prega di contattare il team di supporto ADF per ulteriore assistenza.


### <a name="error-code--202"></a>Codice di errore: 202

- **Messaggio**:`Failed to create on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Causa**: Il messaggio di errore dovrebbe mostrare i dettagli di ciò che è andato storto.

- **Consiglio:** il messaggio di errore dovrebbe aiutare a risolvere il problema. Se le informazioni non sono sufficienti, contattare il supporto ADF per ulteriore assistenza.


### <a name="error-code--203"></a>Codice di errore: 203

- **Messaggio**:`Failed to delete on demand HDI cluster. Cluster name or linked service name: '%clusterName;', error: '%message;'`

- **Causa**: Il messaggio di errore dovrebbe mostrare i dettagli di ciò che è andato storto.

- **Consiglio:** il messaggio di errore dovrebbe aiutare a risolvere il problema. Se le informazioni non sono sufficienti, contattare il supporto ADF per ulteriore assistenza.


### <a name="error-code--204"></a>Codice di errore: 204

- **Messaggio**:`The resumption token is missing for runId '%runId;'.`

- **Causa**: Si è verificato un problema interno del servizio.

- **Raccomandazione**: Si prega di contattare il supporto ADF per ulteriore assistenza.


### <a name="error-code--205"></a>Codice di errore: 205

- **Messaggio**:`Failed to prepare cluster for LinkedService '%linkedServiceName;', the current resource status is '%status;'.`

- **Causa:** si è verificato un errore durante la creazione del cluster HDI su richiesta.

- **Raccomandazione**: Si prega di contattare il supporto ADF per ulteriore assistenza.


### <a name="error-code--206"></a>Codice di errore: 206

- **Messaggio**:`The batch ID for Spark job is invalid. Please retry your job, and if the problem persists, contact the ADF support for further assistance.`

- **Causa**: Si è verificato un problema interno con il servizio che ha causato questo.

- **Raccomandazione:** Potrebbe trattarsi di un problema temporaneo. Riprovare il processo e, se il problema persiste, contattare il supporto ADF per ulteriore assistenza.


### <a name="error-code--207"></a>Codice di errore: 207

- **Messaggio**:`Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI or contact ADF support team and provide the activity run ID.`

- **Causa:** si è verificato un errore interno durante il tentativo di determinare l'area dall'account di archiviazione primario.

- **Consiglio:** provare un altro spazio di archiviazione. Nel caso in cui questa non sia una soluzione accettabile, contattare il team di supporto ADF per ulteriore assistenza.


### <a name="error-code--208"></a>Codice di errore: 208

- **Messaggio**:`Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again. In case if this is not an acceptable solution, contact ADF support team for further assistance.`

- **Causa:** si è verificato un errore interno durante il tentativo di lettura dell'entità servizio o la creazione di un'istanza dell'autenticazione MSI.

- **Consiglio:** è consigliabile fornire un'entità servizio che disponga delle autorizzazioni per creare un cluster HDInsight nella sottoscrizione fornita e riprovare. Nel caso in cui questa non sia una soluzione accettabile, contattare il team di supporto ADF per ulteriore assistenza.


### <a name="error-code--2300"></a>Codice di errore: 2300

- **Messaggio**:`Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

<br>

- **Causa**: quando il messaggio di errore contiene un messaggio simile a 'Impossibile risolvere il nome remoto', ciò potrebbe significare che l'URI del cluster fornito non è valido.


- **Consiglio:** assicurarsi che il cluster non sia stato eliminato e che l'URI fornito sia corretto. Quando apri l'URI in un browser, dovresti vedere l'interfaccia utente di Ambari. Se il cluster si trova in una rete virtuale, l'URI deve essere l'URI privato. Per aprirlo, usare una macchina virtuale che fa parte della stessa rete virtuale. Per altre informazioni, vedere [this](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment#directly-connect-to-apache-hadoop-services).
                  

<br>

- **Causa:** quando il messaggio di errore contiene un messaggio simile a 'Un'attività è stata annullata', significa che l'invio del processo è svenuto.

- **Consiglio:** il problema potrebbe essere la connettività generale HDInsight o la connettività di rete. Verificare innanzitutto che l'interfaccia utente hdInsight Ambari sia disponibile da qualsiasi browser. Verificare che le credenziali siano ancora valide. Se si usa il runtime integrato (IR) self-hosted, assicurarsi di eseguire questa operazione dalla macchina virtuale o dal computer in cui è installato il runtime di accesso autonomo. Provare quindi a inviare nuovamente il processo da Data Factory.Then try to submit the job from Data Factory again. Se il problema persiste, contattare il team di Data Factory per assistenza.

<br>

- **Causa**: quando il messaggio di errore contiene un messaggio simile a 'L'amministratore utente è bloccato in Ambari' o 'Non autorizzato: il nome utente o la password di Ambari non è corretta', significa che le credenziali per HDInsight non sono corrette o sono scadute.

- **Consiglio:** correggere le credenziali e ridistribuire il servizio collegato. Assicurarsi innanzitutto che le credenziali funzionino su HDInsight aprendo l'URI del cluster in qualsiasi browser e tentando di accedere. Se le credenziali non funzionano, è possibile reimpostarle dal portale di Azure.If the credentials don't work, you can reset them from the Azure portal.

<br>

- **Causa:** quando il messaggio di errore contiene un messaggio simile a '502 - Il server Web ha ricevuto una risposta non valida mentre agisce come gateway o server proxy", questo errore viene restituito dal servizio HDInsight.


- **Consiglio:** esaminare la documentazione per https://hdinsight.github.io/ambari/ambari-ui-502-error.htmlla https://hdinsight.github.io/spark/spark-thriftserver-errors.html https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502risoluzione dei problemi di Azure HDInsight, ad esempio , , .
                  

<br>

- **Causa:** quando il messaggio di errore contiene un messaggio simile a 'Impossibile soddisfare la richiesta di processo di invio come servizio Templeton è occupato con troppe richieste di processo di invio' o 'Queue root.joblauncher ha già 500 applicazioni, non può accettare l'invio dell'applicazione', significa che troppi processi vengono inviati a HDInsight contemporaneamente.

- **Consiglio:** è consigliabile limitare il numero di processi simultanei inviati a HDInsight.Recommendation: Consider limiting the number of concurrent jobs submitted to HDInsight. Fare riferimento alla concorrenza dell'attività di Data Factory se i processi vengono inviati dalla stessa attività. Modificare i trigger in modo che le esecuzioni simultanee della pipeline vengano distribuite nel tempo. Fare riferimento alla documentazione di HDInsight per regolare templeton.parallellism.job.submit come suggerisce l'errore.


### <a name="error-code--2301"></a>Codice di errore: 2301

- **Messaggio**:`Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **Causa:** il cluster o il servizio HDInsight presenta problemi.


- **Consiglio:** questo errore si verifica quando ADF non ottiene risposta dal cluster HDInsight quando si tenta di ottenere lo stato del processo in esecuzione. Potrebbe essere la causa di problemi nel cluster stesso o il servizio HDInsight potrebbe avere un'interruzione. Fare riferimento alla documentazione https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guideper la risoluzione dei problemi di HDInsight all'indirizzo o contattare il supporto per ulteriore assistenza.
                


### <a name="error-code--2302"></a>Codice di errore: 2302

- **Messaggio**:`Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **Causa:** il processo è stato inviato al cluster HDI e non è riuscito.

- **Consiglio**: seguire il collegamento Registri Yarn nell'esecuzione dell'attività Output e cercare gli errori nell'output HDI. Se necessario, contattare il team di HDInsight per assistenza.


### <a name="error-code--2303"></a>Codice di errore: 2303

- **Messaggio**:`Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **Causa:** il processo è stato inviato al cluster HDI e non è riuscito.

- **Consiglio**: seguire il collegamento Registri Yarn nell'esecuzione dell'attività Output e cercare gli errori nell'output HDI. Riprovare o contattare il team HDInsight per assistenza, se necessario.


### <a name="error-code--2304"></a>Codice di errore: 2304

- **Messaggio**:`MSI authentication is not supported on storages for HDI activities.`

- **Causa**: i servizi collegati di archiviazione utilizzati nel servizio collegato HDI o nell'attività HDI sono configurati con l'autenticazione MSI che non è supportata.

- **Consiglio:** fornire stringhe di connessione complete per gli account di archiviazione usati nel servizio collegato HDI o nell'attività HDI.


### <a name="error-code--2305"></a>Codice di errore: 2305

- **Messaggio**:`Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **Causa:** le informazioni di connessione per il cluster HDI non sono corrette, l'utente fornito non dispone delle autorizzazioni per eseguire l'azione richiesta o il servizio HDInsight ha avuto problemi di risposta alle richieste provenienti da ADF.

- **Raccomandazione**: Assicurarsi che le informazioni dell'utente siano corrette. Verificare inoltre che l'interfaccia utente di Ambari per il cluster HDI possa essere aperta in un browser dalla macchina virtuale in cui è installato il prodotto a oggetti di integrazione in caso di prodotto a raggio self-hosted oppure può essere aperto da qualsiasi computer nel caso in cui il controllo di proprietà aggiuntivo di Azure sia installato.


### <a name="error-code--2306"></a>Codice di errore: 2306

- **Messaggio**:`An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **Causa**: il json fornito per l'azione script non è valido.


- **Raccomandazione:** il messaggio di errore dovrebbe aiutare a identificare il problema. Correggere la configurazione json e riprovare. Verificare https://docs.microsoft.com/azure/data-factory/compute-linked-services#azure-hdinsight-on-demand-linked-service la presenza di ulteriori informazioni.
                


### <a name="error-code--2310"></a>Codice di errore: 2310

- **Messaggio**:`Failed to submit Spark job. Error: '%message;'`

- **Causa:** ADF ha tentato di creare un batch in un cluster Spark utilizzando l'API Livy (livy/batch), ma ha ricevuto un errore.

- **Raccomandazione**: Seguire il messaggio di errore per risolvere il problema. Se non ci sono informazioni sufficienti per risolverlo risolto, contattare il team HDI e fornire loro l'ID batch e l'ID processo che possono essere trovati nella pagina di esecuzione dell'attività Output in Monitoraggio ADF.


### <a name="error-code--2312"></a>Codice di errore: 2312

- **Messaggio**:`Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **Causa:** il processo non è riuscito nel cluster HDInsight Spark.

- **Consiglio:** seguire i collegamenti nella pagina Di seguito dell'esecuzione dell'attività Output nel monitoraggio ADF per risolvere i problemi relativi all'esecuzione nel cluster HDInsight Spark. Contattare il team di supporto HDInsight per ulteriore assistenza.


### <a name="error-code--2313"></a>Codice di errore: 2313

- **Messaggio**:`The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **Causa:** il batch è stato eliminato nel cluster HDInsight Spark.

- **Consiglio:** risolvere i problemi relativi ai batch nel cluster HDInsight Spark.Recommendation: Troubleshoot batches on the HDInsight Spark cluster. Contattare il supporto HDInsight per ulteriore assistenza. 


### <a name="error-code--2328"></a>Codice di errore: 2328

- **Messaggio**:`Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Causa**:`The error message should show the details of what went wrong.`

- **Consiglio:** il messaggio di errore dovrebbe aiutare a risolvere il problema.


### <a name="error-code--2329"></a>Codice di errore: 2329

- **Messaggio**:`Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **Causa**: Il messaggio di errore dovrebbe mostrare i dettagli di ciò che è andato storto.

- **Consiglio:** il messaggio di errore dovrebbe aiutare a risolvere il problema.


### <a name="error-code--2331"></a>Codice di errore: 2331

- **Messaggio**:`The file path should not be null or empty.`

- **Causa**: il percorso del file fornito è vuoto.

- **Consiglio**: specificare un percorso per un file esistente.


### <a name="error-code--2340"></a>Codice di errore: 2340

- **Messaggio**:`HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **Causa:** il servizio collegato HDInsightOnDemand non supporta l'esecuzione tramite Il sistema di gestione delle richieste di gestione internet SelfHosted.

- **Consiglio:** selezionare un ri-/o azure e riprovare.


### <a name="error-code--2341"></a>Codice di errore: 2341

- **Messaggio**:`HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **Causa**: l'URL fornito non è nel formato corretto.

- **Consiglio:** correggere l'URL del cluster e riprovare.


### <a name="error-code--2342"></a>Codice di errore: 2342

- **Messaggio**:`Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **Causa**: le credenziali fornite sono errate per il cluster o si è verificato un problema di connessione o il sistema di gestione delle applicazioni ha problemi di connessione al cluster.

- **Consiglio**:  
      1. Verificare che le credenziali siano corrette aprendo l'interfaccia utente Ambari del cluster HDInsight in un browser.
      2. Se il cluster è in vNet e viene utilizzato il pacchetto di proprietà del componente aggiuntivo, l'URL HDI deve essere l'URL privato nelle reti virtuali, il che significa che deve avere '-int' dopo il nome del cluster. Ad esempiohttps://mycluster.azurehdinsight.net/" " devehttps://mycluster-int.azurehdinsight.net/essere modificato in " ".
      2. Se il cluster è in vNet, viene utilizzato il prodotto a motore self-hosted ed è stato utilizzato l'URL privato e la connessione non è ancora riuscita, la macchina virtuale in cui è installato il prodotto a metallico ha avuto problemi di connessione all'HDI. Connettersi alla macchina virtuale in cui è installato il codice A/I e aprire l'interfaccia utente di Ambari in un browser. Utilizzare l'URL privato per il cluster. Questa connessione dovrebbe funzionare dal browser. In caso contrario, contattare il team di supporto di HDInsight per ulteriore assistenza.
      3. Se il rinsiasta self-hosted non viene utilizzato, il cluster HDI deve essere accessibile pubblicamente. Apri l'interfaccia utente di Ambari in un browser e assicurati che si apra. In caso di problemi con il cluster o i servizi su di esso, contattare il team di supporto HDInsight per assistenza.
      Pertanto, in generale, l'URL del cluster HDI usato nel servizio collegato ADF deve essere accessibile per ADF IR (self-hosted o Azure) affinché la connessione di test venga superata e che le esecuzioni funzionino. Questo può essere facilmente verificato aprendo tale URL da un browser dalla macchina virtuale o da qualsiasi macchina pubblica.
    


### <a name="error-code--2343"></a>Codice di errore: 2343

- **Messaggio**:`User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **Causa:** il nome utente o la password sono vuoti.

- **Consiglio:** fornire le credenziali corrette per connettersi a HDI e riprovare.


### <a name="error-code--2345"></a>Codice di errore: 2345

- **Messaggio**:`Failed to read the content of the hive script. Error: '%message;'`

- **Causa**: Il file di script non esiste o ADF non è riuscito a connettersi al percorso dello script.

- **Consiglio:** verificare che lo script esista e che il servizio collegato associato disponga delle credenziali appropriate per la connessione.


### <a name="error-code--2346"></a>Codice di errore: 2346

- **Messaggio**:`Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **Causa:** ADF ha tentato di stabilire una connessione ODBC al cluster HDI e non è riuscito con errore.

- **Consiglio:** il messaggio di errore e il codice di errore consentono di risolvere gli errori di connessione ODBC. Nel caso in cui non siano sufficienti per risolvere il problema, contattare il team di Azure HDInsight per assistenza.


### <a name="error-code--2347"></a>Codice di errore: 2347

- **Messaggio**:`Hive execution through ODBC failed with error message '%message;'.`

- **Causa**: ADF ha inviato lo script hive per l'esecuzione al cluster HDI tramite la connessione ODBC e lo script non è riuscito su HDI.

- **Consiglio:** l'esecuzione dello script hive non è riuscita nel cluster HDI e il messaggio di errore e il codice di errore dovrebbero essere utili per la risoluzione dei problemi. Nel caso in cui non siano sufficienti per risolvere il problema, contattare il team di Azure HDInsight per assistenza.


### <a name="error-code--2348"></a>Codice di errore: 2348

- **Messaggio**:`The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **Causa:** le proprietà del servizio collegato di archiviazione non sono impostate correttamente.

- **Consiglio:** nel servizio collegato di archiviazione principale sono supportate solo le stringhe di connessione complete per le attività HDI. Assicurarsi di non utilizzare l'autenticazione MSI o le applicazioni.


### <a name="error-code--2350"></a>Codice di errore: 2350

- **Messaggio**:`Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **Causa**: le credenziali fornite per connettersi all'archivio in cui devono trovarsi i file non sono corrette o i file non esistono.

- **Consiglio:** questo errore si verifica quando ADF esegue i passaggi di preparazione per le attività HDI. Tenta di copiare i file nell'archivio principale prima di inviare il processo a HDI. Assicurarsi che i file esistano nel percorso fornito, la connessione di archiviazione sia corretta. ADF HDI activities do not support MSI authentication on storage accounts related to HDI activities, so make sure that those linked services have full keys or are using Azure Key Vault.


### <a name="error-code--2351"></a>Codice di errore: 2351

- **Messaggio**:`Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **Causa**: il file non esiste nel percorso specificato.

- **Consiglio**: Verificare se il file esiste effettivamente e il servizio collegato con le informazioni di connessione che puntano a questo file dispone delle credenziali corrette.


### <a name="error-code--2352"></a>Codice di errore: 2352

- **Messaggio**:`The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **Causa:** le proprietà del servizio collegato di archiviazione file non sono impostate correttamente.

- **Consiglio:** assicurarsi che le proprietà del servizio collegato di archiviazione file siano configurate correttamente.


### <a name="error-code--2353"></a>Codice di errore: 2353

- **Messaggio**:`The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **Causa:** le proprietà del servizio collegato di archiviazione script non sono impostate correttamente.

- **Consiglio:** assicurarsi che le proprietà del servizio collegato di archiviazione script siano configurate correttamente.


### <a name="error-code--2354"></a>Codice di errore: 2354

- **Messaggio**:`The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **Causa:** il tipo di servizio collegato di archiviazione non è supportato dall'attività.

- Consiglio : **Assicurarsi**che il servizio collegato selezionato disponga di uno dei tipi supportati per l'attività. Le attività HDI supportano i servizi collegati AzureBlobStorage e AzureBlobFSStorage.HDI activities support AzureBlobStorage and AzureBlobFSStorage linked services.


### <a name="error-code--2355"></a>Codice di errore: 2355

- **Messaggio**:`The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **Causa**: il fornito per commandEnvironment non è corretto.

- **Consiglio**:  
      Assicurarsi che il valore fornito \"sia\"simile \"a: commandEnvironment : [ nomevariabile : valorevariabile\" ] E ogni variabile viene visualizzata nell'elenco una sola volta.
    


### <a name="error-code--2356"></a>Codice di errore: 2356

- **Messaggio**:`The commandEnvironment already contains a variable named '%variableName;'.`

- **Causa**: la variabile è stata fornita due volte in commandEnvironment .

- **Consiglio**:  
      Assicurarsi che il valore fornito \"sia\"simile \"a: commandEnvironment : [ nomevariabile : valorevariabile\" ] E ogni variabile viene visualizzata nell'elenco una sola volta.
    


### <a name="error-code--2357"></a>Codice di errore: 2357

- **Messaggio**:`The certificate or password is wrong for ADLS Gen 1 storage.`

- **Causa**: le credenziali fornite non sono corrette.

- **Consiglio:** verificare le informazioni di connessione nel servizio collegato ADLS Gen 1 e assicurarsi che la connessione di prova abbia esito positivo.


### <a name="error-code--2358"></a>Codice di errore: 2358

- **Messaggio**:`The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **Causa**: il valore fornito per la proprietà obbligatoria 'TimeToLive' ha formato non valido. 

- **Consiglio**: aggiornare il valore nell'intervallo suggerito e riprovare.


### <a name="error-code--2359"></a>Codice di errore: 2359

- **Messaggio**:`The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **Causa**: il valore fornito per la proprietà 'roles' non è valido.

- **Consiglio:** aggiornare il valore in modo che sia uno dei suggerimenti e riprovare.


### <a name="error-code--2360"></a>Codice di errore: 2360

- **Messaggio**:`The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **Causa:** la stringa di connessione fornita per HCatalogLinkedService non è valida.

- **Consiglio:** aggiornare il valore a una stringa di connessione SQL di Azure corretta e riprovare.


### <a name="error-code--2361"></a>Codice di errore: 2361

- **Messaggio**:`Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **Causa:** la creazione del cluster non è riuscita e ADF non ha rilevato un errore dal servizio HDInsight.

- **Consiglio:** aprire il portale di Azure e provare a trovare la risorsa HDI con il nome fornito e controllare lo stato del provisioning. Contattare il team di supporto di HDInsight per ulteriore assistenza.


### <a name="error-code--2362"></a>Codice di errore: 2362

- **Messaggio**:`Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **Causa:** l'archiviazione aggiuntiva fornita non era Archiviazione BLOB di Azure.For : The provided additional storage was not Azure Blob storage.

- **Consiglio:** fornire l'account di archiviazione BLOB di Azure come spazio di archiviazione aggiuntivo per il servizio collegato HDInsight su richiesta.



## <a name="web-activity"></a>Attività Web

### <a name="error-code--2128"></a>Codice di errore: 2128

- **Messaggio**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Causa:** connettività di rete, errore DNS, convalida del certificato server o timeout.

- **Consiglio:** verificare che l'endpoint che si sta tentando di raggiungere risponda alle richieste. È possibile utilizzare strumenti come Fiddler/Postman.


### <a name="error-code--2108"></a>Codice di errore: 2108

- **Messaggio**:`Error calling the endpoint '%url;'. Response status code: '%code;'`

- **Causa:** la richiesta non è riuscita a causa di un problema sottostante, ad esempio la connettività di rete, l'errore DNS, la convalida del certificato server o il timeout.

- **Consiglio:** utilizzare Fiddler/Postman per convalidare la richiesta.
<br>


#### <a name="more-details"></a>Altre informazioni
Per utilizzare Fiddler per creare una sessione HTTP dell'applicazione Web monitorata:

1. Scaricare, installare e aprire [Fiddler](https://www.telerik.com/download/fiddler).

1. Se l'applicazione Web utilizza HTTPS, passare a **Strumenti** > **Opzioni Fiddler** > **HTTPS**. Selezionare **Acquisisci CONNECT HTTPS** e **Decrittografa traffico HTTPS**.

   ![Opzioni Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Se l'applicazione utilizza certificati TLS/SSL, aggiungere il certificato Fiddler al dispositivo. Passare a **Strumenti** > **Opzioni Fiddler** > **Azioni** > **HTTPS** > **Esportazione certificato radice sul desktop**.

1. Disattivare l'acquisizione accedendo a Traffico**acquisizione** **file** > . In alternativa, premere **F12**.

1. Cancellare la cache del browser in modo che tutti gli elementi memorizzati nella cache vengano rimossi e debbano essere scaricati di nuovo.

1. Creare una richiesta:Create a request:

   1. Selezionare la scheda **Compositore.**

   1. Impostare il metodo HTTP e l'URL.
   
   1. Se necessario, aggiungere intestazioni e il corpo di una richiesta.

   1. Scegliere **Execute**(Esegui).

1. Attiva nuovamente l'acquisizione del traffico e completa la transazione problematica nella tua pagina.

1. Vai a **Salva tutte** > **Save** > **le sessioni**.

Per ulteriori informazioni, vedere [Introduzione a Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla risoluzione dei problemi, prova queste risorse:For more troubleshooting help, try these resources:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità di Data FactoryData Factory feature requests](https://feedback.azure.com/forums/270578-data-factory)
*  [Video di Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum sull'overflow dello stack per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter su Data Factory](https://twitter.com/hashtag/DataFactory)


            
