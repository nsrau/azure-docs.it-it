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
ms.openlocfilehash: ed466b072a771c3aa288a96fa4a0037c31b875f9
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091983"
---
# <a name="troubleshoot-azure-data-factory"></a>Risoluzione dei problemi Azure Data Factory

Questo articolo illustra i metodi comuni per la risoluzione dei problemi per le attività di controllo esterno in Azure Data Factory.

## <a name="connector-and-copy-activity"></a>Connettore e attività di copia

Per i problemi del connettore, ad esempio errore durante l'attività di copia, vedere risolvere i problemi [Azure Data Factory connettori](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code--3200"></a>Codice errore:  3200

- **Messaggio**: Errore 403.

- **Motivo**:`The Databricks access token has expired.`

- **Consiglio**: Per impostazione predefinita, il token di accesso Azure Databricks è valido per 90 giorni. Creare un nuovo token e aggiornare il servizio collegato.


### <a name="error-code--3201"></a>Codice errore:  3201

- **Messaggio**:`Missing required field: settings.task.notebook_task.notebook_path.`

- **Motivo**:`Bad authoring: Notebook path not specified correctly.`

- **Consiglio**: Specificare il percorso del notebook nell'attività databricks.

<br/>

- **Messaggio**:`Cluster   ... does not exist.`

- **Motivo**:`Authoring error: Databricks cluster does not exist or has been deleted.`

- **Consiglio**: Verificare che il cluster databricks esista.

<br/>

- **Messaggio**:`Invalid Python file URI.... Please visit Databricks user guide for supported URI schemes.`

- **Motivo**:`Bad authoring.`

- **Consiglio**: Specificare percorsi assoluti per gli schemi di indirizzamento dell'area `dbfs:/folder/subfolder/foo.py` di lavoro o per i file archiviati nel file System di databricks.

<br/>

- **Messaggio**:`{0} LinkedService should have domain and accessToken as required properties.`

- **Motivo**:`Bad authoring.`

- **Consiglio**: Verificare la [definizione del servizio collegato](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Messaggio**:`{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **Motivo**:`Bad authoring.`

- **Consiglio**: Verificare la [definizione del servizio collegato](compute-linked-services.md#azure-databricks-linked-service).

<br/>

- **Messaggio**:`Node type Standard_D16S_v3 is not supported. Supported node types:   Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3,   Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2,   Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3,   Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2,   Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2,   Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3,   Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s,   Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2,   Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2,   Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3,   Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2,   Standard_L64s_v2, Standard_L80s_v2.`

- **Motivo**:`Bad authoring.`

- **Consiglio**: Fare riferimento al messaggio di errore. 

<br/>

### <a name="error-code--3202"></a>Codice errore:  3202

- **Messaggio**:`There were already 1000 jobs created in past 3600 seconds, exceeding rate limit:   1000 job creations per 3600 seconds.`

- **Motivo**:`Too many Databricks runs in an hour.`

- **Consiglio**: Controllare tutte le pipeline che usano l'area di lavoro databricks per la velocità di creazione del processo.  Se le pipeline avviate un numero eccessivo di databricks vengono eseguite in modo aggregato, eseguire la migrazione di alcune pipeline in una nuova area di lavoro.

<br/>

- **Messaggio**:`Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **Motivo**:`Authoring error: No value provided for the parameter.`

- **Consiglio**: Esaminare il codice JSON della pipeline e verificare che tutti i parametri nel notebook di baseParameters specifichino un valore non vuoto.

<br/>

- **Messaggio**: `User: `SimpleUserContext {UserID =..., nome =user@company.com, OrgID =...}` is not   authorized to access cluster.`

- **Causa**: L'utente che ha generato il token di accesso non è autorizzato ad accedere al cluster databricks specificato nel servizio collegato.

- **Consiglio**: Verificare che l'utente disponga delle autorizzazioni necessarie nell'area di lavoro.


### <a name="error-code--3203"></a>Codice errore:  3203

- **Messaggio**:`The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **Causa**: Il cluster è stato terminato. Per i cluster interattivi, potrebbe trattarsi di un race condition.

- **Consiglio**: Il modo migliore per evitare questo errore consiste nell'usare i cluster di processi.


### <a name="error-code--3204"></a>Codice errore:  3204

- **Messaggio**:`Job execution failed.`

- **Causa**:  I messaggi di errore indicano vari problemi, ad esempio uno stato del cluster imprevisto o un'attività specifica. Spesso non viene visualizzato alcun messaggio di errore. 

- **Consiglio**: N/D


## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics.

La tabella seguente si applica a U-SQL.


### <a name="error-code--2709"></a>Codice errore:  2709

- **Messaggio**:`The access token is from the wrong tenant.`

- **Causa**:  Tenant di Azure Active Directory (Azure AD) errato.

- **Consiglio**: Tenant di Azure Active Directory (Azure AD) errato.

<br/>

- **Messaggio**:`We cannot accept your job at this moment. The maximum number of queued jobs for   your account is 200. `

- **Causa**:  Questo errore è causato dalla limitazione delle richieste Data Lake Analytics.

- **Consiglio**: Ridurre il numero di processi inviati a Data Lake Analytics modificando Data Factory trigger e le impostazioni di concorrenza per le attività. In alternativa, aumentare i limiti per Data Lake Analytics.

<br/>

- **Messaggio**:`This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **Causa**:  Questo errore è causato dalla limitazione delle richieste Data Lake Analytics. 

- **Consiglio**: Ridurre il numero di processi inviati a Data Lake Analytics modificando Data Factory trigger e le impostazioni di concorrenza per le attività. In alternativa, aumentare i limiti per Data Lake Analytics.


### <a name="error-code--2705"></a>Codice errore:  2705

- **Messaggio**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Causa**:  L'entità servizio o il certificato non ha accesso al file nella risorsa di archiviazione.

- **Consiglio**: Verificare che l'entità servizio o il certificato fornito dall'utente per i processi di Data Lake Analytics abbia accesso all'account di Data Lake Analytics e all'istanza di Data Lake Storage predefinita dalla cartella radice.


### <a name="error-code--2711"></a>Codice errore:  2711

- **Messaggio**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Causa**:  L'entità servizio o il certificato non ha accesso al file nella risorsa di archiviazione.

- **Consiglio**: Verificare che l'entità servizio o il certificato fornito dall'utente per i processi di Data Lake Analytics abbia accesso all'account di Data Lake Analytics e all'istanza di Data Lake Storage predefinita dalla cartella radice.

<br/>

- **Messaggio**:`Cannot find the 'Azure Data Lake Store' file or folder.`

- **Causa**:  Il percorso del file U-SQL non è corretto oppure le credenziali del servizio collegato non hanno accesso.

- **Consiglio**: Verificare il percorso e le credenziali fornite nel servizio collegato.


### <a name="error-code--2704"></a>Codice errore:  2704

- **Messaggio**:`Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/><br/>User is   not able to access Data Lake Store.  <br/><br/>User is  not authorized to use Data Lake Analytics.`

- **Causa**:  L'entità servizio o il certificato non ha accesso al file nella risorsa di archiviazione.

- **Consiglio**: Verificare che l'entità servizio o il certificato fornito dall'utente per i processi di Data Lake Analytics abbia accesso all'account di Data Lake Analytics e all'istanza di Data Lake Storage predefinita dalla cartella radice.


### <a name="error-code--2707"></a>Codice errore:  2707

- **Messaggio**:`Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and   'DataLakeAnalyticsUri'.`

- **Causa**:  L'account Data Lake Analytics nel servizio collegato non è corretto.

- **Consiglio**: Verificare che sia specificato l'account corretto.


### <a name="error-code--2703"></a>Codice errore:  2703

- **Messaggio**:`Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error   Id:").`

- **Causa**:  L'errore è stato Data Lake Analytics. 

- **Consiglio**: Un errore come l'esempio indica che il processo è stato inviato a Data Lake Analytics e che lo script non è riuscito. Esaminare Data Lake Analytics. Nel portale passare all'account Data Lake Analytics e cercare il processo usando l'ID esecuzione dell'attività Data Factory (non l'ID di esecuzione della pipeline). Il processo contiene ulteriori informazioni sull'errore e consente di risolvere i problemi. Se la risoluzione non è chiara, contattare il team di supporto di Data Lake Analytics e specificare l'URL del processo, che include il nome dell'account e l'ID del processo.



## <a name="azure-functions"></a>Funzioni di Azure

### <a name="error-code--3602"></a>Codice errore:  3602

- **Messaggio**:`Invalid HttpMethod: {method}.`

- **Causa**: Il metodo HTTP specificato nel payload dell'attività non è supportato dall'attività funzione di Azure. 

- **Consiglio**: I metodi HTTP supportati sono PUT, POST, GET, DELETE, OPTIONS, HEAD e TRACE.


### <a name="error-code--3603"></a>Codice errore:  3603

- **Messaggio**:`Response content is not a valid JObject.`

- **Causa**: La funzione di Azure chiamata non ha restituito un payload JSON nella risposta. L'attività della funzione di Azure in Data Factory supporta solo il contenuto della risposta JSON. 

- **Consiglio**: Aggiornare la funzione di Azure per restituire un payload JSON valido. Ad esempio, una C# funzione può restituire `(ActionResult)new<OkObjectResult("{` \"ID\":\"123\".`}");`


### <a name="error-code--3606"></a>Codice errore:  3606

- **Messaggio**:`Azure function activity missing function key.`

- **Causa**: La definizione dell'attività funzione di Azure non è completa. 

- **Consiglio**: Verificare che la definizione JSON dell'attività AzureFunction di input includa la proprietà denominata ' functionKey '.


### <a name="error-code--3607"></a>Codice errore:  3607

- **Messaggio**:`Azure function activity missing function name.`

- **Causa**: La definizione dell'attività funzione di Azure non è completa. 

- **Consiglio**: Verificare che la definizione JSON dell'attività AzureFunction di input includa la proprietà denominata ' FunctionName '.


### <a name="error-code--3608"></a>Codice errore:  3608

- **Messaggio**:`Call to provided Azure function '{FunctionName}' failed with status-'{statusCode}' and message - '{message}'.` 

- **Causa**: I dettagli della funzione di Azure nella definizione di attività potrebbero non essere corretti. 

- **Consiglio**: Correggere i dettagli della funzione di Azure e riprovare.


### <a name="error-code--3609"></a>Codice errore:  3609

- **Messaggio**:`Azure function activity missing functionAppUrl.` 

- **Causa**: La definizione dell'attività funzione di Azure non è completa. 

- **Consiglio**: Verificare che la definizione JSON dell'attività AzureFunction di input includa la proprietà denominata ' functionAppUrl '.


### <a name="error-code--3610"></a>Codice errore:  3610

- **Messaggio**:`There was an error while calling endpoint.`

- **Causa**: L'URL della funzione potrebbe non essere corretto.

- **Consiglio**: Verificare che il valore di ' functionAppUrl ' nel file JSON dell'attività sia corretto e riprovare.


### <a name="error-code--3611"></a>Codice errore:  3611

- **Messaggio**:`Azure function activity missing Method in JSON.` 

- **Causa**: La definizione dell'attività funzione di Azure non è completa.

- **Consiglio**: Controllare se la definizione JSON dell'attività AzureFunction di input ha una proprietà denominata ' Method '.


### <a name="error-code--3612"></a>Codice errore:  3612

- **Messaggio**:`Azure function activity missing LinkedService definition in JSON.`

- **Causa**: La definizione dell'attività funzione di Azure potrebbe non essere completata.

- **Consiglio**: Controllare la definizione JSON dell'attività AzureFunction di input con i dettagli del servizio collegato.


## <a name="azure-machine-learning"></a>Azure Machine Learning


### <a name="error-code--4101"></a>Codice errore:  4101

- **Messaggio**:`AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **Causa**: Formato non valido o definizione mancante di una proprietà.

- **Consiglio**:  Verificare se l'attività è definita con i dati corretti.


### <a name="error-code--4110"></a>Codice errore:  4110

- **Messaggio**: Nell'attività AzureMLExecutePipeline manca la definizione LinkedService in JSON.

- **Causa**: La definizione dell'attività AzureMLExecutePipeline non è completa.

- **Consiglio**:  Verificare se la definizione JSON dell'attività AzureMLExecutePipeline di input contiene i dettagli del servizio collegato.


### <a name="error-code--4111"></a>Codice errore:  4111

- **Messaggio**:`AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **Causa**: Definizione di attività non corretta.

- **Consiglio**:  Verificare se la definizione JSON dell'attività AzureMLExecutePipeline di input contiene i dettagli del servizio collegato corretti.


### <a name="error-code--4112"></a>Codice errore:  4112

- **Messaggio**:`AzureMLService linked service has invalid value for property '%propertyName;'.`

- **Causa**: Formato non valido o definizione mancante di una proprietà.

- **Consiglio**:  Verificare se i dati della definizione del servizio collegato sono corretti.


### <a name="error-code--4121"></a>Codice errore:  4121

- **Messaggio**:`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Causa**: Le credenziali usate per accedere al servizio Azure ML sono scadute.

- **Consiglio**:  Verificare che le credenziali siano valide e riprovare


### <a name="error-code--4122"></a>Codice errore:  4122

- **Messaggio**:`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Causa**: Le credenziali specificate nel servizio collegato del servizio AzureML non sono valide o non dispongono dell'autorizzazione per l'operazione.

- **Consiglio**:  Verificare che le credenziali nel servizio collegato siano valide e che disponga delle autorizzazioni per accedere al servizio AzureML.


### <a name="error-code--4123"></a>Codice errore:  4123

- **Messaggio**:`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Motivo**:`Properties of the activity such as pipelineParamters are invalid for the Azure ML pipeline.`

- **Consiglio**:  Verificare il valore delle proprietà dell'attività in modo che corrisponda al payload previsto della pipeline di Azure ML pubblicata specificata nel servizio collegato.


### <a name="error-code--4124"></a>Codice errore:  4124

- **Messaggio**:`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Causa**: L'endpoint della pipeline di Azure ML pubblicato non esiste.

- **Consiglio**:  Verificare che l'endpoint della pipeline di Azure ML pubblicato specificato nel servizio collegato esista nel servizio Azure ML.


### <a name="error-code--4125"></a>Codice errore:  4125

- **Messaggio**:`Request sent to AzureML Service for operation '%operation;' failed with http status code '%statusCode;'. Error message from AzureML Service: '%externalMessage;'.`

- **Causa**: Errore del server nel servizio Azure ML.

- **Consiglio**:  Riprovare più tardi. Contattare il team del servizio Azure ML per assistenza se il problema persiste.


### <a name="error-code--4126"></a>Codice errore:  4126

- **Messaggio**:`AzureML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in AzureMLService for more error loggings.`

- **Causa**: Esecuzione della pipeline AzureML non riuscita.

- **Consiglio**:  Controllare AzureMLService per altre registrazioni degli errori e correggere la pipeline ML


## <a name="custom"></a>Personalizzato

La tabella seguente si applica a Azure Batch.


### <a name="error-code--2500"></a>Codice errore:  2500

- **Messaggio**:`Hit unexpected exception and execution failed.`

- **Motivo**:`Can't launch command, or the program returned an error code.`

- **Consiglio**:  Verificare che il file eseguibile esista. Se il programma è stato avviato, assicurarsi che *stdout. txt* e *stderr. txt* siano stati caricati nell'account di archiviazione. È consigliabile creare log copii nel codice per il debug.


### <a name="error-code--2501"></a>Codice errore:  2501

- **Messaggio**:`Cannot access user batch account; please check batch account settings.`

- **Causa**: Il nome del pool o la chiave di accesso batch non è corretta.

- **Consiglio**: Verificare il nome del pool e la chiave di accesso batch nel servizio collegato.


### <a name="error-code--2502"></a>Codice errore:  2502

- **Messaggio**:`Cannot access user storage account; please check storage account settings.`

- **Causa**: Nome dell'account di archiviazione o chiave di accesso non corretta.

- **Consiglio**: Verificare il nome dell'account di archiviazione e la chiave di accesso nel servizio collegato.


### <a name="error-code--2504"></a>Codice errore:  2504

- **Messaggio**:`Operation returned an invalid status code 'BadRequest'.` 

- **Causa**: Troppi file nel folderPath dell'attività personalizzata. La dimensione totale di resourceFiles non può contenere più di 32.768 caratteri.

- **Consiglio**: Rimuovere i file non necessari. Oppure è possibile eseguirne il zip e aggiungere un comando unzip per estrarli. Ad esempio, usare`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;  $folder\yourProgram.exe`


### <a name="error-code--2505"></a>Codice errore:  2505

- **Messaggio**:`Cannot create Shared Access Signature unless Account Key credentials are used.`

- **Causa**: Le attività personalizzate supportano solo gli account di archiviazione che usano una chiave di accesso.

- **Consiglio**: Vedere la descrizione dell'errore.


### <a name="error-code--2507"></a>Codice errore:  2507

- **Messaggio**:`The folder path does not exist or is empty: ....`

- **Causa**: Nessun file si trova nell'account di archiviazione nel percorso specificato.

- **Consiglio**: Il percorso della cartella deve contenere i file eseguibili che si desidera eseguire.


### <a name="error-code--2508"></a>Codice errore:  2508

- **Messaggio**:`There are duplicate files in the resource folder.`

- **Causa**: Più file con lo stesso nome si trovano in sottocartelle diverse di folderPath.

- **Consiglio**: Le attività personalizzate appiattiscono la struttura di cartelle in folderPath.  Se è necessario mantenere la struttura di cartelle, comprimere i file ed estrarli in Azure Batch usando un comando unzip. Ad esempio, usare`powershell.exe -nologo -noprofile   -command "& { Add-Type -A 'System.IO.Compression.FileSystem';   [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ;   $folder\yourProgram.exe`


### <a name="error-code--2509"></a>Codice errore:  2509

- **Messaggio**:`Batch   url ... is invalid; it must be in Uri format.` 

- **Causa**: Gli URL batch devono essere simili a`https://mybatchaccount.eastus.batch.azure.com`

- **Consiglio**: Vedere la descrizione dell'errore.


### <a name="error-code--2510"></a>Codice errore:  2510

- **Messaggio**:`An   error occurred while sending the request.`

- **Causa**: L'URL del batch non è valido. 

- **Consiglio**: Verificare l'URL del batch.


## <a name="hdinsight"></a>HDInsight

La tabella seguente si applica a Spark, hive, MapReduce, Pig e Hadoop Streaming.


### <a name="error-code--2300"></a>Codice errore:  2300

- **Messaggio**:`Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Causa**: L'URI del cluster specificato non è valido. 

- **Consiglio**:  Verificare che il cluster non sia stato eliminato e che l'URI fornito sia corretto. Quando si apre l'URI in un browser, viene visualizzata l'interfaccia utente di Ambari. Se il cluster si trova in una rete virtuale, l'URI deve essere l'URI privato. Per aprirlo, usare una macchina virtuale che fa parte della stessa rete virtuale. Per ulteriori informazioni, vedere la pagina relativa [alla connessione diretta ai servizi Apache Hadoop](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Messaggio**:`Hadoop job submission failed. Job: …, Cluster: …/. Error: A task was canceled.`

- **Causa**: Timeout dell'invio del processo. 

- **Consiglio**: Il problema potrebbe essere la connettività generale HDInsight o la connettività di rete. Verificare innanzitutto che l'interfaccia utente di HDInsight Ambari sia disponibile da qualsiasi browser. Verificare che le credenziali siano ancora valide. Se si usa il runtime di integrazione self-hosted (IR), assicurarsi di eseguire questa operazione dalla macchina virtuale o dal computer in cui è installato il runtime di integrazione self-hosted. Provare quindi a inviare di nuovo il processo da Data Factory. Se il problema persiste, contattare il team di Data Factory per assistenza.


- **Messaggio**:`Unauthorized: Ambari user name or password is incorrect  <br/><br/>Unauthorized: User admin is locked out in Ambari.   <br/><br/>403 - Forbidden: Access is denied.`

- **Causa**: Le credenziali per HDInsight sono errate o scadute.

- **Consiglio**: Correggere le credenziali e ridistribuire il servizio collegato. Assicurarsi prima di tutto che le credenziali funzionino in HDInsight aprendo l'URI del cluster in qualsiasi browser e tentando di eseguire l'accesso. Se le credenziali non funzionano, è possibile reimpostarle dalla portale di Azure.

<br/>

- **Messaggio**:`502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Causa**: Questo errore è di HDInsight.

- **Consiglio**: Questo errore viene dal cluster HDInsight. Per altre informazioni, vedere [errore 502 dell'interfaccia utente di Ambari](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 errori di connessione al server di spark Thrifty](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502 errori di connessione al server di risparmio di Spark](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)e [risoluzione degli errori del gateway non valido nel gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Messaggio**:`Hadoop job submission failed. Job: …, Cluster: ... Error:   {\"error\":\"Unable to service the submit job request as   templeton service is busy with too many submit job requests. Please wait for some time before retrying the operation. Please refer to the config   templeton.parallellism.job.submit to configure concurrent requests. <br/><br/>Hadoop job submission failed. Job: xx, Cluster: name.   Error: {\"error\":\"java.io.IOException:   org.apache.hadoop.yarn.exceptions.YarnException: Failed to submit   application_1561147195099_3730 to YARN :   org.apache.hadoop.security.AccessControlException: Queue root.joblauncher already has 500 applications, cannot accept submission of application:   application_1561147195099_3730\`

- **Causa**: È in corso l'invio di un numero eccessivo di processi a HDInsight nello stesso momento.

- **Consiglio**: È consigliabile limitare il numero di processi simultanei inviati a HDInsight. Fare riferimento a Data Factory concorrenza di attività se i processi vengono inviati dalla stessa attività. Modificare i trigger in modo che le esecuzioni di pipeline simultanee vengano distribuite nel tempo. Vedere la documentazione di HDInsight per `templeton.parallellism.job.submit` modificare come suggerito dall'errore.


### <a name="error-code--2303"></a>Codice errore:  2303

- **Messaggio**:`Hadoop job failed with exit code '5'. See   'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Causa**: Il processo è stato inviato a HDInsight e non è riuscito in HDInsight.

- **Consiglio**: Il processo è stato inviato a HDInsight correttamente. Errore nel cluster. Aprire il processo e i log nell'interfaccia utente di HDInsight Ambari o aprire il file dalla risorsa di archiviazione come suggerito dal messaggio di errore. Il file Mostra i dettagli dell'errore.


### <a name="error-code--2310"></a>Codice errore:  2310

- **Messaggio**:`Hadoop job submission failed. Error: The remote name could not be resolved. <br/><br/>The cluster is not found.`

- **Causa**: L'URI del cluster specificato non è valido. 

- **Consiglio**:  Verificare che il cluster non sia stato eliminato e che l'URI fornito sia corretto. Quando si apre l'URI in un browser, viene visualizzata l'interfaccia utente di Ambari. Se il cluster si trova in una rete virtuale, l'URI deve essere l'URI privato. Per aprirlo, usare una macchina virtuale che fa parte della stessa rete virtuale. Per ulteriori informazioni, vedere la pagina relativa [alla connessione diretta ai servizi Apache Hadoop](https://docs.microsoft.com/azure/hdinsight/hdinsight-extend-hadoop-virtual-network#directly-connect-to-apache-hadoop-services).

<br/>

- **Messaggio**:`502 - Web server received an invalid response while acting as a gateway or proxy server. <br/>Bad gateway.`

- **Causa**: Questo errore è di HDInsight.

- **Consiglio**: Questo errore viene dal cluster HDInsight. Per altre informazioni, vedere [errore 502 dell'interfaccia utente di Ambari](https://hdinsight.github.io/ambari/ambari-ui-502-error.html), [502 errori di connessione al server di spark Thrifty](https://hdinsight.github.io/spark/spark-thriftserver-errors.html), [502 errori di connessione al server di risparmio di Spark](https://hdinsight.github.io/spark/spark-thriftserver-errors.html)e [risoluzione degli errori del gateway non valido nel gateway applicazione](https://docs.microsoft.com/azure/application-gateway/application-gateway-troubleshooting-502).

<br/>

- **Messaggio**:`java.lang.NullPointerException`

- **Causa**: Questo errore si verifica quando il processo viene inviato a un cluster Spark. 

- **Consiglio**: Questa eccezione deriva da HDInsight. Nasconde il problema effettivo. Per assistenza, contattare il team di HDInsight. Specificare il nome del cluster e l'intervallo di tempo di esecuzione dell'attività.


### <a name="error-code--2347"></a>Codice errore:  2347

- **Messaggio**:`Hadoop job failed with exit code '5'. See 'wasbs://adfjobs@xx.blob.core.windows.net/StreamingJobs/da4afc6d-7836-444e-bbd5-635fce315997/18_06_2019_05_36_05_050/stderr' for more details. <br/><br/>Hive execution failed with error code 'UserErrorHiveOdbcCommandExecutionFailure'.   See 'wasbs://adfjobs@xx.blob.core.windows.net/HiveQueryJobs/16439742-edd5-4efe-adf6-9b8ff5770beb/18_06_2019_07_37_50_477/Status/hive.out' for more details.`

- **Causa**: Il processo è stato inviato a HDInsight e non è riuscito in HDInsight.

- **Consiglio**: Il processo è stato inviato a HDInsight correttamente. Errore nel cluster. Aprire il processo e i log nell'interfaccia utente di HDInsight Ambari o aprire il file dalla risorsa di archiviazione come suggerito dal messaggio di errore. Il file Mostra i dettagli dell'errore.


### <a name="error-code--2328"></a>Codice errore:  2328

- **Messaggio**:`Internal server error occurred while processing the request. Please retry the request or contact support. `

- **Causa**: Questo errore si verifica in HDInsight su richiesta.

- **Consiglio**: Questo errore deriva dal servizio HDInsight quando il provisioning di HDInsight ha esito negativo. Contattare il team di HDInsight e specificare il nome del cluster su richiesta.



## <a name="web-activity"></a>Attività Web

### <a name="error-code--2108"></a>Codice errore:  2108

- **Messaggio**:`Invalid HttpMethod: '...'.`

- **Causa**: L'attività Web non supporta il metodo HTTP specificato nel payload dell'attività.

- **Consiglio**:  I metodi HTTP supportati sono PUT, POST, GET e DELETE.

<br/>

- **Messaggio**:`Invalid Server Error 500.`

- **Causa**: Errore interno sull'endpoint.

- **Consiglio**:  Per verificare la funzionalità sull'URL, usare Fiddler o postazione.

<br/>

- **Messaggio**:`Unauthorized 401.`

- **Causa**: Manca l'autenticazione valida per la richiesta.

- **Consiglio**:  Il token potrebbe essere scaduto. Fornire un metodo di autenticazione valido. Per verificare la funzionalità sull'URL, usare Fiddler o postazione.

<br/>

- **Messaggio**:`Forbidden 403.`

- **Causa**: Mancano le autorizzazioni necessarie.

- **Consiglio**:  Controllare le autorizzazioni utente per la risorsa a cui si accede. Per verificare la funzionalità sull'URL, usare Fiddler o postazione.

<br/>

- **Messaggio**:`Bad Request 400.`

- **Causa**: Richiesta HTTP non valida.

- **Consiglio**:   Controllare l'URL, il verbo e il corpo della richiesta. Usare Fiddler o subpost per convalidare la richiesta.

<br/>

- **Messaggio**:`Not found 404.` 

- **Causa**: La risorsa non è stata trovata.   

- **Consiglio**:  Usare Fiddler o subpost per convalidare la richiesta.

<br/>

- **Messaggio**:`Service unavailable.`

- **Causa**: Il servizio non è disponibile.

- **Consiglio**:  Usare Fiddler o subpost per convalidare la richiesta.

<br/>

- **Messaggio**:`Unsupported Media Type.`

- **Causa**: Il tipo di contenuto non corrisponde al corpo dell'attività Web.

- **Consiglio**:  Specificare il tipo di contenuto che corrisponde al formato del payload. Usare Fiddler o subpost per convalidare la richiesta.

<br/>

- **Messaggio**:`The resource you are looking for has been removed, has had its name changed, or is temporarily unavailable.`

- **Causa**: La risorsa non è disponibile. 

- **Consiglio**:  Per controllare l'endpoint, usare Fiddler o postazione.

<br/>

- **Messaggio**:`The page you are looking for cannot be displayed because an invalid method (HTTP verb) is being used.`

- **Causa**: Nella richiesta è stato specificato un metodo di attività Web errato.

- **Consiglio**:  Per controllare l'endpoint, usare Fiddler o postazione.

<br/>

- **Messaggio**:`invalid_payload`

- **Causa**: Il corpo dell'attività Web non è corretto.

- **Consiglio**:  Per controllare l'endpoint, usare Fiddler o postazione.


### <a name="error-code--2208"></a>Codice errore:  2208

- **Messaggio**:`Invoking Web Activity failed with HttpStatusCode - {0}.`

- **Causa**: Il servizio di destinazione ha restituito lo stato di errore.

- **Consiglio**:  Usare Fiddler/post per convalidare la richiesta.


### <a name="error-code--2308"></a>Codice errore:  2308

- **Messaggio**:`No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **Causa**: Possono esserci diversi motivi per questo errore, come la connettività di rete, l'errore DNS, la convalida del certificato del server o il timeout.

- **Consiglio**:  Usare Fiddler/post per convalidare la richiesta.


Per usare Fiddler per creare una sessione HTTP dell'applicazione Web monitorata:

1. Scaricare, installare e aprire [Fiddler](https://www.telerik.com/download/fiddler).

1. Se l'applicazione Web usa HTTPS, passare a **strumenti** > **Fiddler opzioni** > **https**. Selezionare **Acquisisci HTTPS connette** e decrittografare il **traffico HTTPS**. 
   
   ![Opzioni Fiddler](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Se l'applicazione usa certificati SSL, aggiungere il certificato Fiddler al dispositivo. Passare a **strumenti** > **Fiddler opzioni** > **azione** > HTTPS Esporta certificatoradicesuldesktop. > 

1. Per disattivare l'acquisizione, passare a **file** > **Acquisisci traffico**. Oppure premere **F12**.

1. Cancellare la cache del browser in modo che tutti gli elementi memorizzati nella cache vengano rimossi e che sia necessario scaricarli nuovamente.

1. Creare una richiesta: 

   a. Selezionare la scheda Composer.

   b. Impostare il metodo e l'URL HTTP.

   c. Aggiungere intestazioni e un corpo della richiesta, se necessario.

   d. Scegliere **Execute**(Esegui).

9. Riattivare l'acquisizione del traffico e completare la transazione problematica nella pagina.

10. Passare a **file** > **Salva** > **tutte le sessioni**.

Per ulteriori informazioni, vedere la pagina relativa all' [Introduzione a Fiddler](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler).

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sulla risoluzione dei problemi, provare a usare le risorse seguenti:

*  [Blog di Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Richieste di funzionalità Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Video su Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?sort=relevancedesc&brandIgnore=True&searchTerm=data+factory)
*  [Forum Stack Overflow per Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informazioni su Twitter sui Data Factory](https://twitter.com/hashtag/DataFactory)



