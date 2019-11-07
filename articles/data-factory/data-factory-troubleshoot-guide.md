---
title: Risoluzione dei problemi Azure Data Factory
description: Informazioni su come risolvere i problemi relativi alle attività di controllo esterno in Azure Data Factory.
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 8/26/2019
ms.author: abnarain
ms.reviewer: craigg
ms.openlocfilehash: 66590e40ff2440a166310ec3d57026f5a2cdbd1d
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73676857"
---
# <a name="troubleshoot-azure-data-factory"></a>Risoluzione dei problemi Azure Data Factory

Questo articolo illustra i metodi comuni per la risoluzione dei problemi per le attività di controllo esterno in Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Connettore e attività di copia

Per i problemi del connettore, ad esempio errore durante l'attività di copia, vedere risolvere i problemi [Azure Data Factory connettori](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Codice di errore: 3200

- **Messaggio**: errore 403.

- **Motivo**: `The Databricks access token has expired.`

- **Suggerimento**: per impostazione predefinita, il token di accesso Azure Databricks è valido per 90 giorni. Creare un nuovo token e aggiornare il servizio collegato.


### <a name="error-code--3201"></a>Codice di errore: 3201

- **Messaggio**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **Motivo**: `Bad authoring: Notebook path not specified correctly.`

- **Raccomandazione**: specificare il percorso del notebook nell'attività databricks.

<br/>

- **Messaggio**: `Cluster   ... does not exist.`

- **Motivo**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Raccomandazione**: verificare che il cluster databricks esista.

<br/>

- **Messaggio**: `Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

- **Motivo**: `Bad authoring.`

- **Raccomandazione**: specificare i percorsi assoluti per gli schemi di indirizzamento dell'area di lavoro o `dbfs:/folder/subfolder/foo.py` per i file archiviati nel file System di databricks.

<br/>

- **Messaggio**: `{0} LinkedService should have domain and accessToken as required properties.`

- **Motivo**: `Bad authoring.`

- **Raccomandazione**: verificare la [definizione del servizio collegato](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Messaggio**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Motivo**: `Bad authoring.`

- **Raccomandazione**: verificare la [definizione del servizio collegato](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Messaggio**: `Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Motivo**: `Bad authoring.`

- **Raccomandazione**: fare riferimento al messaggio di errore. 

<br/>

### <a name="error-code--3202"></a>Codice di errore: 3202

- **Messaggio**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Motivo**: `Too many Databricks runs in an hour.`

- **Consiglio**: selezionare tutte le pipeline che usano questa area di lavoro di databricks per la velocità di creazione del processo.  Se le pipeline avviate un numero eccessivo di databricks vengono eseguite in modo aggregato, eseguire la migrazione di alcune pipeline in una nuova area di lavoro.

<br/>

- **Messaggio**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Motivo**: `Authoring error: No value provided for the parameter.`

- **Consiglio**: esaminare il codice JSON della pipeline e verificare che tutti i parametri nel notebook di baseParameters specifichino un valore non vuoto.

<br/>

- **Messaggio**: `User: `SimpleUserContext {UserID =..., name =user@company.com, OrgID =...}` is not   authorized to access cluster.`

- **Motivo**: l'utente che ha generato il token di accesso non è autorizzato ad accedere al cluster databricks specificato nel servizio collegato.

- **Consiglio**: assicurarsi che l'utente disponga delle autorizzazioni necessarie nell'area di lavoro.


### <a name="error-code--3203"></a>Codice di errore: 3203

- **Messaggio**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Motivo**: il cluster è stato terminato. Per i cluster interattivi, potrebbe trattarsi di un race condition.

- **Raccomandazione**: il modo migliore per evitare questo errore consiste nell'usare i cluster di processi.


### <a name="error-code--3204"></a>Codice di errore: 3204

- **Messaggio**: `Job execution failed.`

- **Cause**: i messaggi di errore indicano vari problemi, ad esempio uno stato del cluster imprevisto o un'attività specifica. Spesso non viene visualizzato alcun messaggio di errore. 

- **Raccomandazione**: N/A


## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics.

La tabella seguente si applica a U-SQL.


### <a name="error-code--2709"></a>Codice di errore: 2709

- **Messaggio**: `The access token is from the wrong tenant.`

- **Motivo**: tenant Azure Active Directory (Azure ad) errato.

- **Raccomandazione**: tenant Azure Active Directory (Azure ad) errato.

<br/>

- **Messaggio**: `We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Causa**: questo errore è causato dalla limitazione delle richieste data Lake Analytics.

- **Raccomandazione**: ridurre il numero di processi inviati a data Lake Analytics modificando Data Factory trigger e le impostazioni di concorrenza per le attività. In alternativa, aumentare i limiti per Data Lake Analytics.

<br/>

- **Messaggio**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Causa**: questo errore è causato dalla limitazione delle richieste data Lake Analytics. 

- **Raccomandazione**: ridurre il numero di processi inviati a data Lake Analytics modificando Data Factory trigger e le impostazioni di concorrenza per le attività. In alternativa, aumentare i limiti per Data Lake Analytics.


### <a name="error-code--2705"></a>Codice di errore: 2705

- **Messaggio**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Motivo**: l'entità servizio o il certificato non ha accesso al file nella risorsa di archiviazione.

- **Raccomandazione**: assicurarsi che l'entità servizio o il certificato fornito dall'utente per data Lake Analytics processi abbia accesso all'account data Lake Analytics e all'istanza di data Lake storage predefinita dalla cartella radice.


### <a name="error-code--2711"></a>Codice di errore: 2711

- **Messaggio**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Motivo**: l'entità servizio o il certificato non ha accesso al file nella risorsa di archiviazione.

- **Raccomandazione**: assicurarsi che l'entità servizio o il certificato fornito dall'utente per data Lake Analytics processi abbia accesso all'account data Lake Analytics e all'istanza di data Lake storage predefinita dalla cartella radice.

<br/>

- **Messaggio**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **Motivo**: il percorso del file U-SQL non è corretto oppure le credenziali del servizio collegato non hanno accesso.

- **Raccomandazione**: verificare il percorso e le credenziali fornite nel servizio collegato.


### <a name="error-code--2704"></a>Codice di errore: 2704

- **Messaggio**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Motivo**: l'entità servizio o il certificato non ha accesso al file nella risorsa di archiviazione.

- **Raccomandazione**: assicurarsi che l'entità servizio o il certificato fornito dall'utente per data Lake Analytics processi abbia accesso all'account data Lake Analytics e all'istanza di data Lake storage predefinita dalla cartella radice.


### <a name="error-code--2707"></a>Codice di errore: 2707

- **Messaggio**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Causa**: l'account data Lake Analytics nel servizio collegato non è corretto.

- **Consiglio**: verificare che venga fornito l'account corretto.


### <a name="error-code--2703"></a>Codice di errore: 2703

- **Messaggio**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Motivo**: l'errore è data Lake Analytics. 

- **Raccomandazione**: un errore come l'esempio indica che il processo è stato inviato a data Lake Analytics e che lo script non è riuscito. Esaminare Data Lake Analytics. Nel portale passare all'account Data Lake Analytics e cercare il processo usando l'ID esecuzione dell'attività Data Factory (non l'ID di esecuzione della pipeline). Il processo contiene ulteriori informazioni sull'errore e consente di risolvere i problemi. Se la risoluzione non è chiara, contattare il team di supporto di Data Lake Analytics e specificare l'URL del processo, che include il nome dell'account e l'ID del processo.



## <a name="azure-functions"></a>Funzioni di Azure

### <a name="error-code--3602"></a>Codice di errore: 3602

- **Messaggio**: `Invalid HttpMethod: {method}.`

- **Motivo**: il metodo HTTP specificato nel payload dell'attività non è supportato dall'attività funzione di Azure. 

- **Raccomandazione**: i metodi HTTP supportati sono put, post, Get, DELETE, options, Head e Trace.


### <a name="error-code--3603"></a>Codice di errore: 3603

- **Messaggio**: `Response content is not a valid JObject.`

- **Motivo**: la funzione di Azure chiamata non ha restituito un payload JSON nella risposta. L'attività della funzione di Azure in Data Factory supporta solo il contenuto della risposta JSON. 

- **Raccomandazione**: aggiornare la funzione di Azure per restituire un payload JSON valido. Ad esempio, una C# funzione può restituire `(ActionResult)new<OkObjectResult("{`\"ID\":\"123\"`}");`.


### <a name="error-code--3606"></a>Codice di errore: 3606

- **Messaggio**: `Azure function activity missing function key.`

- **Motivo**: la definizione dell'attività funzione di Azure non è completa. 

- **Raccomandazione**: verificare che la definizione JSON dell'attività AzureFunction di input includa la proprietà denominata ' functionKey '.


### <a name="error-code--3607"></a>Codice di errore: 3607

- **Messaggio**: `Azure function activity missing function name.`

- **Motivo**: la definizione dell'attività funzione di Azure non è completa. 

- **Raccomandazione**: controllare che la definizione JSON dell'attività AzureFunction di input includa la proprietà denominata ' FunctionName '.


### <a name="error-code--3608"></a>Codice di errore: 3608

- **Messaggio**: `Call to provided Azure function '{FunctionName}' failed with status-'{statusCode}' and message - '{message}'.` 

- **Motivo**: i dettagli della funzione di Azure nella definizione di attività potrebbero non essere corretti. 

- **Raccomandazione**: correggere i dettagli della funzione di Azure e riprovare.


### <a name="error-code--3609"></a>Codice di errore: 3609

- **Messaggio**: `Azure function activity missing functionAppUrl.` 

- **Motivo**: la definizione dell'attività funzione di Azure non è completa. 

- **Raccomandazione**: verificare che la definizione JSON dell'attività AzureFunction di input includa la proprietà denominata ' functionAppUrl '.


### <a name="error-code--3610"></a>Codice di errore: 3610

- **Messaggio**: `There was an error while calling endpoint.`

- **Motivo**: l'URL della funzione potrebbe non essere corretto.

- **Consiglio**: assicurarsi che il valore di ' functionAppUrl ' nel file JSON dell'attività sia corretto e riprovare.


### <a name="error-code--3611"></a>Codice di errore: 3611

- **Messaggio**: `Azure function activity missing Method in JSON.` 

- **Motivo**: la definizione dell'attività funzione di Azure non è completa.

- **Raccomandazione**: verificare se la definizione JSON dell'attività AzureFunction di input ha una proprietà denominata ' Method '.


### <a name="error-code--3612"></a>Codice di errore: 3612

- **Messaggio**: `Azure function activity missing LinkedService definition in JSON.`

- **Motivo**: la definizione dell'attività funzione di Azure potrebbe non essere completa.

- **Raccomandazione**: controllare che la definizione JSON dell'attività AzureFunction di input includa i dettagli del servizio collegato.


## <a name="azure-machine-learning"></a>Azure Machine Learning


### <a name="error-code--4101"></a>Codice di errore: 4101

- **Messaggio**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Cause**: formato non valido o definizione mancante di una proprietà.

- **Consiglio**: verificare se l'attività è definita con i dati corretti.


### <a name="error-code--4110"></a>Codice di errore: 4110

- **Message**: nell'attività AzureMLExecutePipeline manca la definizione LINKEDSERVICE in JSON.

- **Motivo**: la definizione dell'attività AzureMLExecutePipeline non è completa.

- **Consiglio**: verificare se la definizione JSON dell'attività AzureMLExecutePipeline di input contiene i dettagli del servizio collegato.


### <a name="error-code--4111"></a>Codice di errore: 4111

- **Messaggio**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Cause**: definizione di attività non corretta.

- **Consiglio**: verificare se la definizione JSON dell'attività AzureMLExecutePipeline di input contiene i dettagli del servizio collegato corretti.


### <a name="error-code--4112"></a>Codice di errore: 4112

- **Messaggio**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Cause**: formato non valido o definizione mancante di una proprietà.

- **Consiglio**: verificare se la definizione del servizio collegato contiene dati corretti.


### <a name="error-code--4121"></a>Codice di errore: 4121

- **Messaggio**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Motivo**: la credenziale usata per accedere al servizio Azure ml è scaduta.

- **Raccomandazione**: verificare che le credenziali siano valide e riprovare


### <a name="error-code--4122"></a>Codice di errore: 4122

- **Messaggio**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Motivo**: le credenziali fornite nel servizio collegato del servizio AzureML non sono valide o non dispongono dell'autorizzazione per l'operazione.

- **Raccomandazione**: verificare che le credenziali nel servizio collegato siano valide e che disponga delle autorizzazioni per accedere al servizio AzureML.


### <a name="error-code--4123"></a>Codice di errore: 4123

- **Messaggio**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Motivo**: `Properties of the activity such as pipelineParamters are invalid for the Azure ML pipeline.`

- **Raccomandazione**: controllare il valore delle proprietà dell'attività in modo che corrisponda al payload previsto della pipeline di Azure ml pubblicata specificata nel servizio collegato.


### <a name="error-code--4124"></a>Codice di errore: 4124

- **Messaggio**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Motivo**: l'endpoint della pipeline di Azure ml pubblicato non esiste.

- **Raccomandazione**: verificare che l'endpoint della pipeline di Azure ml pubblicato specificato nel servizio collegato esista nel servizio Azure ml.


### <a name="error-code--4125"></a>Codice di errore: 4125

- **Messaggio**: `Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Cause**: errore del server nel servizio Azure ml.

- **Raccomandazione**: riprovare più tardi. Contattare il team del servizio Azure ML per assistenza se il problema persiste.


### <a name="error-code--4126"></a>Codice di errore: 4126

- **Messaggio**: `AzureML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in AzureMLService for more error loggings.`

- **Motivo**: esecuzione della pipeline AzureML non riuscita.

- **Suggerimento**: per altre informazioni sulla registrazione degli errori e per la correzione della pipeline ml, vedere AzureMLService.


## <a name="custom"></a>Personalizzate

La tabella seguente si applica a Azure Batch.


### <a name="error-code--2500"></a>Codice di errore: 2500

- **Messaggio**: `Hit unexpected exception and execution failed.`

- **Motivo**: `Can't launch command, or the program returned an error code.`

- **Consiglio**: verificare che il file eseguibile esista. Se il programma è stato avviato, assicurarsi che *stdout. txt* e *stderr. txt* siano stati caricati nell'account di archiviazione. È consigliabile creare log copii nel codice per il debug.


### <a name="error-code--2501"></a>Codice di errore: 2501

- **Messaggio**: `Cannot access user batch account; please check batch account settings.`

- **Cause**: la chiave di accesso batch o il nome del pool non è corretto.

- **Raccomandazione**: verificare il nome del pool e la chiave di accesso batch nel servizio collegato.


### <a name="error-code--2502"></a>Codice di errore: 2502

- **Messaggio**: `Cannot access user storage account; please check storage account settings.`

- **Causa**: il nome o la chiave di accesso dell'account di archiviazione non è corretto.

- **Raccomandazione**: verificare il nome dell'account di archiviazione e la chiave di accesso nel servizio collegato.


### <a name="error-code--2504"></a>Codice di errore: 2504

- **Messaggio**: `Operation returned an invalid status code 'BadRequest'.` 

- **Cause**: troppi file nel folderPath dell'attività personalizzata. La dimensione totale di resourceFiles non può contenere più di 32.768 caratteri.

- **Raccomandazione**: rimuovere i file non necessari. Oppure è possibile eseguirne il zip e aggiungere un comando unzip per estrarli. Ad esempio, usare `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Codice di errore: 2505

- **Messaggio**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Motivo**: le attività personalizzate supportano solo gli account di archiviazione che usano una chiave di accesso.

- **Raccomandazione**: fare riferimento alla descrizione dell'errore.


### <a name="error-code--2507"></a>Codice di errore: 2507

- **Messaggio**: `The folder path does not exist or is empty: ....`

- **Causa**: nessun file si trova nell'account di archiviazione nel percorso specificato.

- **Raccomandazione**: il percorso della cartella deve contenere i file eseguibili che si desidera eseguire.


### <a name="error-code--2508"></a>Codice di errore: 2508

- **Messaggio**: `There are duplicate files in the resource folder.`

- **Motivo**: più file con lo stesso nome si trovano in sottocartelle diverse di folderPath.

- **Raccomandazione**: le attività personalizzate appiattiscono la struttura di cartelle in folderPath.  Se è necessario mantenere la struttura di cartelle, comprimere i file ed estrarli in Azure Batch usando un comando unzip. Ad esempio, usare `powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Codice di errore: 2509

- **Messaggio**: `Batch   url ... is invalid; it must be in Uri format.` 

- **Motivo**: gli URL batch devono essere simili a `https://mybatchaccount.eastus.batch.azure.com`

- **Raccomandazione**: fare riferimento alla descrizione dell'errore.


### <a name="error-code--2510"></a>Codice di errore: 2510

- **Messaggio**: `An   error occurred while sending the request.`

- **Motivo**: l'URL del batch non è valido. 

- **Raccomandazione**: verificare l'URL del batch.


## <a name="hdinsight"></a>HDInsight

La tabella seguente si applica a Spark, hive, MapReduce, Pig e Hadoop Streaming.


### <a name="error-code--2300"></a>Codice di errore: 2300

- **Messaggio**: `Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Motivo**: l'URI del cluster specificato non è valido. 

- **Consiglio**: assicurarsi che il cluster non sia stato eliminato e che l'URI specificato sia corretto. Quando si apre l'URI in un browser, viene visualizzata l'interfaccia utente di Ambari. Se il cluster si trova in una rete virtuale, l'URI deve essere l'URI privato. Per aprirlo, usare una macchina virtuale che fa parte della stessa rete virtuale. Per ulteriori informazioni, vedere la pagina relativa [alla connessione diretta ai servizi Apache Hadoop](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Messaggio**: `Hadoop job submission failed. Job: …, Cluster: …/. Error: A task was canceled.`

- **Motivo**: timeout dell'invio del processo. 

- **Raccomandazione**: il problema potrebbe essere la connettività di rete HDInsight generale o la connettività di rete. Verificare innanzitutto che l'interfaccia utente di HDInsight Ambari sia disponibile da qualsiasi browser. Verificare che le credenziali siano ancora valide. Se si usa il runtime di integrazione self-hosted (IR), assicurarsi di eseguire questa operazione dalla macchina virtuale o dal computer in cui è installato il runtime di integrazione self-hosted. Provare quindi a inviare di nuovo il processo da Data Factory. Se il problema persiste, contattare il team di Data Factory per assistenza.


- **Messaggio**: `Unauthorized: Ambari user name or password is incorrect  <br/><br/>Unauthorized: User admin is locked out in Ambari.   <br/><br/>403 - Forbidden: Access is denied.`

- **Motivo**: le credenziali per HDInsight sono errate o scadute.

- **Raccomandazione**: correggere le credenziali e ridistribuire il servizio collegato. Assicurarsi prima di tutto che le credenziali funzionino in HDInsight aprendo l'URI del cluster in qualsiasi browser e tentando di eseguire l'accesso. Se le credenziali non funzionano, è possibile reimpostarle dalla portale di Azure.

<br/>

- **Messaggio**: `502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Motivo**: questo errore è di HDInsight.

- **Raccomandazione**: questo errore viene dal cluster HDInsight. Per altre informazioni, vedere [errore 502 dell'interfaccia utente di Ambari](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 errori di connessione al server di spark Thrifty](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502 errori di connessione al server di risparmio di Spark](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)e [risoluzione degli errori del gateway non valido nel gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Messaggio**: `Hadoop job submission failed. Job: …, Cluster: ... Error:   {\"error\":\"Unable to service the submit job request as   templeton service is busy with too many submit job requests. Please wait for some time before retrying the operation. Please refer to the config   templeton.parallellism.job.submit to configure concurrent requests. <br/><br/>Hadoop job submission failed. Job: xx, Cluster: name.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Queue root.joblauncher already has 500 applications, cannot accept submission of application:   application_1561147195099_3730\`

- **Motivo**: è in corso l'invio di troppi processi a HDInsight nello stesso momento.

- **Raccomandazione**: è consigliabile limitare il numero di processi simultanei inviati a HDInsight. Fare riferimento a Data Factory concorrenza di attività se i processi vengono inviati dalla stessa attività. Modificare i trigger in modo che le esecuzioni di pipeline simultanee vengano distribuite nel tempo. Vedere la documentazione di HDInsight per modificare `templeton.parallellism.job.submit` come suggerito dall'errore.


### <a name="error-code--2303"></a>Codice di errore: 2303

- **Messaggio**: `Hadoop job failed with exit code '5'. See   'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Motivo**: il processo è stato inviato a HDInsight e non è riuscito in HDInsight.

- **Raccomandazione**: il processo è stato inviato correttamente a HDInsight. Errore nel cluster. Aprire il processo e i log nell'interfaccia utente di HDInsight Ambari o aprire il file dalla risorsa di archiviazione come suggerito dal messaggio di errore. Il file Mostra i dettagli dell'errore.


### <a name="error-code--2310"></a>Codice di errore: 2310

- **Messaggio**: `Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Motivo**: l'URI del cluster specificato non è valido. 

- **Consiglio**: assicurarsi che il cluster non sia stato eliminato e che l'URI specificato sia corretto. Quando si apre l'URI in un browser, viene visualizzata l'interfaccia utente di Ambari. Se il cluster si trova in una rete virtuale, l'URI deve essere l'URI privato. Per aprirlo, usare una macchina virtuale che fa parte della stessa rete virtuale. Per ulteriori informazioni, vedere la pagina relativa [alla connessione diretta ai servizi Apache Hadoop](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Messaggio**: `502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Motivo**: questo errore è di HDInsight.

- **Raccomandazione**: questo errore viene dal cluster HDInsight. Per altre informazioni, vedere [errore 502 dell'interfaccia utente di Ambari](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 errori di connessione al server di spark Thrifty](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502 errori di connessione al server di risparmio di Spark](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)e [risoluzione degli errori del gateway non valido nel gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Messaggio**: `java.lang.NullPointerException`

- **Motivo**: questo errore si verifica quando il processo viene inviato a un cluster Spark. 

- **Raccomandazione**: questa eccezione deriva da HDInsight. Nasconde il problema effettivo. Per assistenza, contattare il team di HDInsight. Specificare il nome del cluster e l'intervallo di tempo di esecuzione dell'attività.


### <a name="error-code--2347"></a>Codice di errore: 2347

- **Messaggio**: `Hadoop job failed with exit code '5'. See 'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Motivo**: il processo è stato inviato a HDInsight e non è riuscito in HDInsight.

- **Raccomandazione**: il processo è stato inviato correttamente a HDInsight. Errore nel cluster. Aprire il processo e i log nell'interfaccia utente di HDInsight Ambari o aprire il file dalla risorsa di archiviazione come suggerito dal messaggio di errore. Il file Mostra i dettagli dell'errore.


### <a name="error-code--2328"></a>Codice di errore: 2328

- **Messaggio**: `Internal server error occurred while processing the request. Please retry the request or contact support. `

- **Motivo**: questo errore si verifica in HDInsight su richiesta.

- **Raccomandazione**: questo errore deriva dal servizio HDInsight quando il provisioning di HDInsight ha esito negativo. Contattare il team di HDInsight e specificare il nome del cluster su richiesta.



## <a name="web-activity"></a>Attività Web

### <a name="error-code--2108"></a>Codice di errore: 2108

- **Messaggio**: `Invalid HttpMethod: '...'.`

- **Motivo**: l'attività Web non supporta il metodo HTTP specificato nel payload dell'attività.

- **Raccomandazione**: i metodi HTTP supportati sono put, post, Get ed Delete.

<br/>

- **Messaggio**: `Invalid Server Error 500.`

- **Cause**: errore interno sull'endpoint.

- **Raccomandazione**: usare Fiddler o Postee per verificare la funzionalità sull'URL.

<br/>

- **Messaggio**: `Unauthorized 401.`

- **Motivo**: manca l'autenticazione valida per la richiesta.

- **Raccomandazione**: il token potrebbe essere scaduto. Fornire un metodo di autenticazione valido. Per verificare la funzionalità sull'URL, usare Fiddler o postazione.

<br/>

- **Messaggio**: `Forbidden 403.`

- **Motivo**: mancano le autorizzazioni necessarie.

- **Raccomandazione**: controllare le autorizzazioni utente per la risorsa a cui si accede. Per verificare la funzionalità sull'URL, usare Fiddler o postazione.

<br/>

- **Messaggio**: `Bad Request 400.`

- **Cause**: richiesta HTTP non valida.

- **Raccomandazione**: controllare l'URL, il verbo e il corpo della richiesta. Usare Fiddler o subpost per convalidare la richiesta.

<br/>

- **Messaggio**: `Not found 404.` 

- **Motivo**: la risorsa non è stata trovata.   

- **Raccomandazione**: usare Fiddler o subpost per convalidare la richiesta.

<br/>

- **Messaggio**: `Service unavailable.`

- **Motivo**: il servizio non è disponibile.

- **Raccomandazione**: usare Fiddler o subpost per convalidare la richiesta.

<br/>

- **Messaggio**: `Unsupported Media Type.`

- **Motivo**: il tipo di contenuto non corrisponde al corpo dell'attività Web.

- **Raccomandazione**: specificare il tipo di contenuto che corrisponde al formato del payload. Usare Fiddler o subpost per convalidare la richiesta.

<br/>

- **Messaggio**: `The resource you are looking for has been removed, has had its name changed, or is temporarily unavailable.`

- **Motivo**: la risorsa non è disponibile. 

- **Raccomandazione**: usare Fiddler o Postee per controllare l'endpoint.

<br/>

- **Messaggio**: `The page you are looking for cannot be displayed because an invalid method (HTTP verb) is being used.`

- **Motivo**: nella richiesta è stato specificato un metodo di attività Web errato.

- **Raccomandazione**: usare Fiddler o Postee per controllare l'endpoint.

<br/>

- **Messaggio**: `invalid_payload`

- **Motivo**: il corpo dell'attività Web non è corretto.

- **Raccomandazione**: usare Fiddler o Postee per controllare l'endpoint.


### <a name="error-code--2208"></a>Codice di errore: 2208

- **Messaggio**: `Invoking Web Activity failed with HttpStatusCode - {0}.`

- **Motivo**: il servizio di destinazione ha restituito lo stato di errore.

- **Raccomandazione**: usare Fiddler/post per convalidare la richiesta.


### <a name="error-code--2308"></a>Codice di errore: 2308

- **Messaggio**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Causa**: possono esserci diversi motivi per questo errore, come la connettività di rete, l'errore DNS, la convalida del certificato del server o il timeout.

- **Raccomandazione**: usare Fiddler/post per convalidare la richiesta.


Per usare Fiddler per creare una sessione HTTP dell'applicazione Web monitorata:

1. Scaricare, installare e aprire [Fiddler](https://www.telerik.com/download/fiddler).

1. Se l'applicazione Web usa HTTPS, passare a **strumenti** > **Opzioni Fiddler** > **https**. Selezionare **Acquisisci HTTPS connette** e **decrittografare il traffico HTTPS**. 
   
   ![Opzioni Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Se l'applicazione usa certificati SSL, aggiungere il certificato Fiddler al dispositivo. Passare a **strumenti** > **Opzioni Fiddler** > **azioni** > **https** > **esportare il certificato radice sul desktop**.

1. Per disattivare l'acquisizione, passare a **File** > **Acquisisci traffico**. Oppure premere **F12**.

1. Cancellare la cache del browser in modo che tutti gli elementi memorizzati nella cache vengano rimossi e che sia necessario scaricarli nuovamente.

1. Creare una richiesta: 

   a. Selezionare la scheda **Composer** .

   b. Impostare il metodo e l'URL HTTP.

   c. Aggiungere intestazioni e un corpo della richiesta, se necessario.

   d. Scegliere **Execute**(Esegui).

9. Riattivare l'acquisizione del traffico e completare la transazione problematica nella pagina.

10. Passare a **File** > **Salva** > **tutte le sessioni**.

Per ulteriori informazioni, vedere la pagina relativa all' [Introduzione a Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video su Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter sui Data Factory](https://twitter.com/hashtag/DataFactory)



