---
title: Codici errore dell'API REST di Azure Machine Learning | Documentazione Microsoft
description: Questi codici errore possono essere restituiti da un'operazione in un servizio Web di Azure Machine Learning.
keywords: 
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: 0923074b-3728-439d-a1b8-8a7245e39be4
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/16/2016
ms.author: garye
ms.openlocfilehash: 5cf7d5bb878f323e4e3559822dc745359e43608e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="machine-learning-rest-api-error-codes"></a>Codici errore dell'API REST di Azure Machine Learning
 
I codici errore seguenti possono essere restituiti da un'operazione in un servizio Web di Azure Machine Learning.
 
## <a name="badargument-http-status-code-400"></a>BadArgument (codice di stato HTTP 400)
 
È stato specificato un argomento non valido.
 
Questa classe di errori indica che un argomento specificato nel codice non è valido. Potrebbe trattarsi di una credenziale o della località di una risorsa di archiviazione di Azure oppure un elemento passato al servizio Web. Esaminare il campo "code" dell'errore nella sezione "details" per la individuare l'argomento specifico non valido.
 
| Codice di errore | Messaggio utente |
| ---------- |--------------|
| BadParameterValue | Il valore del parametro fornito non rispetta la regola del parametro per il parametro. |
| BadSubscriptionId | L'ID sottoscrizione usato per calcolare il punteggio non corrisponde a quello presente nella risorsa. |
| BadVersionCall | Un parametro di versione non valido è stato passato durante la chiamata API: {0}. Controllare la pagina della Guida dell'API per informazioni sul passaggio della versione corretta e riprovare. |
| BatchJobInputsNotSpecified | Gli input obbligatori seguenti non sono stati specificati nella richiesta: {0}. Assicurarsi che tutti i dati di input siano specificati e riprovare. |
| BatchJobInputsTooManySpecified | La richiesta ha specificato un numero di input superiore a quello definito nel servizio. Elenco di input accettati: {0}. Assicurarsi che tutti i dati di input siano specificati correttamente e riprovare. |
| BlobNameTooLong | Il percorso dell'archivio BLOB di Azure specificato per l'output di diagnostica è troppo lungo: {0}. Abbreviare il percorso e riprovare. |
| BlobNotFound | Non è possibile accedere al BLOB di Azure specificato {0}.  Messaggio di errore di Azure: {1}. |
| ContainerIsEmpty | Non sono stati specificati nomi di contenitori di archiviazione di Azure. Specificare un nome di contenitore valido e riprovare. |
| ContainerSegmentInvalid | Il nome del contenitore non è valido. Specificare un nome di contenitore valido e riprovare. |
| ContainerValidationFailed | La convalida del contenitore BLOB ha avuto esito negativo con questo errore: {0}. |
| DataTypeNotSupported | È stato specificato un tipo di dati non supportato. Specificare tipi di dati validi e riprovare. |
| DuplicateInputInBatchCall | La richiesta batch non è valida. Non è possibile specificare input singoli e multipli contemporaneamente. Rimuovere uno degli elementi della richiesta e riprovare. |
| ExpiryTimeInThePast | La scadenza specificata è nel passato: {0}. Specificare una scadenza futura in formato UTC e riprovare. Per rimuovere la scadenza, impostare il valore su NULL. |
| IncompleteSettings | Le impostazioni di diagnostica non sono complete. |
| InputBlobRelativeLocationInvalid | Non sono stati specificati nomi del BLOB di Archiviazione di Azure. Specificare un nome di BLOB valido e riprovare. |
| InvalidBlob | La specifica del BLOB non è valida per il BLOB: {0}. Verificare che la stringa di connessione o il percorso relativo oppure la specifica del token di firma di accesso condiviso sia corretta e riprovare. |
| InvalidBlobConnectionString | La stringa di connessione specificata per uno dei BLOB di input/output non è valida: {0}. Correggere e riprovare. |
| InvalidBlobExtension | Il riferimento BLOB {0} include un'estensione file non valida o mancante. Le estensioni di file supportate per questo output sono: "{1}". |
| InvalidInputNames | Sono stati specificati nomi di input del servizio non validi nella richiesta: {0}. Eseguire il mapping dei dati di input agli input del servizio corretti e riprovare. |
| InvalidOutputOverrideName | Il nome di override dell'output non è valido: {0}. Il servizio non include un nodo di output con questo nome. Passare un nome di output corretto per la sostituzione. Viene applicata la distinzione tra maiuscole e minuscole. |
| InvalidQueryParameter | Il parametro di query '{0}' non è valido. {1} |
| MissingInputBlobInformation | Le informazioni sul BLOB di Archiviazione di Azure non sono presenti. Specificare una stringa di connessione valida e il percorso relativo o l'URI e riprovare. |
| MissingJobId | Non è stato specificato alcun ID di processo. Un ID di processo viene restituito quando un processo viene inviato per la prima volta. Verificare che l'ID di processo sia corretto e riprovare. |
| MissingKeys | Non sono state specificate chiavi o non è presente una chiave primaria o secondaria. |
| MissingModelPackage | Non sono stati specificati ID di pacchetto o pacchetti di modelli. Specificare un ID di pacchetto di modelli o un pacchetto di modelli valido e riprovare. |
| MissingOutputOverrideSpecification | La richiesta non include la specifica BLOB per l'override dell'output {0}. Specificare una località valida per il BLOB insieme alla richiesta oppure rimuovere la specifica dell'output se non si vuole che venga eseguito l'override della località. |
| MissingRequestInput | Il servizio Web richiede un input, ma non sono stati specificati input. Assicurarsi che vengano specificati input validi in base alle porte di input pubblicate nel modello e riprovare. |
| MissingRequiredGlobalParameters | Non sono stati specificati tutti i parametri obbligatori del servizio Web. Verificare che i parametri previsti per i moduli siano corretti e riprovare. |
| MissingRequiredOutputOverrides | Quando si chiama un endpoint di servizio crittografato, è obbligatorio passare gli override dell'output per tutti gli output del servizio. Override attualmente mancanti per questi output: {0} |
| MissingWebServiceGroupId | Non sono stati specificati ID del gruppo di servizi. Specificare un ID di gruppo di servizi Web valido e riprovare. |
| MissingWebServiceId | Non sono stati specificati ID del servizio Web. Specificare un ID di servizio Web valido e riprovare. |
| MissingWebServicePackage | Non sono stati specificati pacchetti del servizio Web. Specificare un pacchetto del servizio Web valido e riprovare. |
| MissingWorkspaceId | Non sono stati specificati ID dell'area di lavoro. Specificare un ID dell'area di lavoro valido e riprovare. |
| ModelConfigurationInvalid | La configurazione del modello nel pacchetto di modelli non è valida. Assicurarsi che la configurazione del modello includa la definizione degli endpoint di output, l'endpoint dell'errore standard e l'endpoint di output standard e riprovare. |
| ModelPackageIdInvalid | L'ID del pacchetto di modelli non è valido. Verificare che l'ID del pacchetto di modelli sia corretto e riprovare. |
| RequestBodyInvalid | Non è stato specificato il corpo della richiesta o si è verificato un errore durante la deserializzazione del corpo della richiesta. |
| RequestIsEmpty | Non sono state specificate richieste. Specificare una richiesta valida e riprovare. |
| UnexpectedParameter | Sono stati specificati parametri imprevisti. Verificare che l'ortografia di tutti i nomi di parametro sia corretta e che vengano passati solo i parametri previsti, quindi riprovare. |
| UnknownError | Errore sconosciuto. |
| UserParameterInvalid | {0} |
| WebServiceConcurrentRequestRequirementInvalid | Non è possibile modificare i requisiti delle richieste simultanee per il servizio Web {0}. |
| WebServiceIdInvalid | L'ID del servizio Web specificato non è valido. L'ID del servizio Web deve essere un GUID valido. |
| WebServiceTooManyConcurrentRequestRequirement | Non è possibile impostare il requisito per le richieste simultanee su un valore maggiore di {0}. |
| WebServiceTypeInvalid | Il tipo del servizio Web specificato non è valido. Verificare che il tipo del servizio Web sia corretto e riprovare. Tipo di servizio Web validi: {0}. |
 
## <a name="baduserargument-http-status-code-400"></a>BadUserArgument (codice di stato HTTP 400)
 
È stato specificato un argomento utente non valido.
 
| Codice di errore | Messaggio utente |
| ---------- |--------------|
| InputMismatchError | I dati di input non corrispondono allo schema di porte di input. |
| InputParseError | L'analisi dei vettori di input non è riuscita.  Assicurarsi che il vettore di input abbia il numero corretto di colonne e di tipi di dati.  Dettagli aggiuntivi: {0}. |
| MissingRequiredGlobalParameters | I parametri previsti dal servizio Web non sono presenti. Assicurarsi che tutti i parametri obbligatori previsti dal servizio Web siano corretti e riprovare. |
| UnexpectedParameter | Assicurarsi che vengano passati solo i parametri previsti dal servizio Web e riprovare. |
| UserParameterInvalid | {0} |
 
## <a name="invalidoperation-http-status-code-400"></a>InvalidOperation (codice di stato HTTP 400)
 
La richiesta non è valida nel contesto corrente.
 
| Codice di errore | Messaggio utente |
| ---------- |--------------|
| CannotStartJob | Il processo non può essere avviato perché ha stato {0}. |
| IncompatibleModel | Il modello non è compatibile con la versione della richiesta. La versione della richiesta supporta solo i modelli di output con datatable singola. |
| MultipleInputsNotAllowed | Il modello non consente gli input multipli. |
 
## <a name="libraryexecutionerror-http-status-code-400"></a>LibraryExecutionError (codice di stato HTTP 400)
 
Si è verificato un errore di libreria interno durante l'esecuzione del modulo.
 
 
## <a name="moduleexecutionerror-http-status-code-400"></a>ModuleExecutionError (codice di stato HTTP 400)
 
Si è verificato un errore durante l'esecuzione del modulo.
 
 
## <a name="webservicepackageerror-http-status-code-400"></a>WebServicePackageError (codice di stato HTTP 400)
 
Il pacchetto del servizio Web non è valido. Assicurarsi che il pacchetto del servizio Web specificato sia corretto e riprovare.
 
| Codice di errore | Messaggio utente |
| ---------- |--------------|
| FormatError | Il formato del pacchetto del servizio Web non è corretto. Dettagli: {0} |
| RuntimesError | L'elemento grafico del pacchetto del servizio Web non è valido. Dettagli: {0} |
| ValidationError | L'elemento grafico del pacchetto del servizio Web non è valido. Dettagli: {0} |
 
## <a name="unauthorized-http-status-code-401"></a>Unauthorized (codice di stato HTTP 401)
 
La richiesta non è autorizzata ad accedere alla risorsa.
 
| Codice di errore | Messaggio utente |
| ---------- |--------------|
| AdminRequestUnauthorized | Non autorizzata |
| ManagementRequestUnauthorized | Non autorizzata |
| ScoreRequestUnauthorized | Sono state specificate credenziali non valide. |
 
## <a name="notfound-http-status-code-404"></a>NotFound (codice di stato HTTP 404)
 
La risorsa non è stata trovata.
 
| Codice di errore | Messaggio utente |
| ---------- |--------------|
| ModelPackageNotFound | Il pacchetto di modelli non è stato trovato. Verificare che l'ID del pacchetto di modelli sia corretto e riprovare. |
| WebServiceIdNotFoundInWorkspace | Il servizio Web in questa area di lavoro non è stato trovato. I valori webServiceId e workspaceId non corrispondono. Assicurarsi che il servizio Web specificato sia parte dell'area di lavoro e riprovare. |
| WebServiceNotFound | Il servizio Web non è stato trovato. Assicurarsi che l'ID del servizio Web specificato sia corretto e riprovare. |
| WorkspaceNotFound | L'area di lavoro non è stata trovata. Assicurarsi che l'ID dell'area di lavoro sia corretto e riprovare. |
 
## <a name="requesttimeout-http-status-code-408"></a>RequestTimeout (codice di stato HTTP 408)
 
L'operazione non è stata completata nel tempo consentito.
 
| Codice di errore | Messaggio utente |
| ---------- |--------------|
| RequestCanceled | La richiesta è stata annullata dal client. |
| ScoreRequestTimeout | Si è verificato il timeout dell'esecuzione della richiesta. |
 
## <a name="conflict-http-status-code-409"></a>Conflict (codice di stato HTTP 409)
 
La risorsa esiste già.
 
| Codice di errore | Messaggio utente |
| ---------- |--------------|
| ModelOutputMetadataMismatch | Il nome del parametro di output non è valido. Provare a usare il modulo dell'editor di metadati per rinominare le colonne e riprovare. |
 
## <a name="memoryquotaviolation-http-status-code-413"></a>MemoryQuotaViolation (codice di stato HTTP 413)
 
Il modello ha superato la quota di memoria assegnata ad esso.
 
| Codice di errore | Messaggio utente |
| ---------- |--------------|
| OutOfMemoryLimit | Il modello ha utilizzato una quantità di memoria superiore rispetto a quella prevista. La quantità massima di memoria consentita per il modello è {0} MB. Controllare il modello per individuare problemi. |
 
## <a name="internalerror-http-status-code-500"></a>InternalError (codice di stato HTTP 500)
 
Si è verificato un errore interno durante l'esecuzione.
 
| Codice di errore | Messaggio utente |
| ---------- |--------------|
| AdminAuthenticationFailed |  |
| BackendArgumentError |  |
| BackendBadRequest |  |
| ClusterConfigBlobMisconfigured |  |
| ContainerProcessTerminatedWithSystemError | Il processo contenitore si è arrestato con un errore di sistema. |
| ContainerProcessTerminatedWithUnknownError | Il processo contenitore si è arrestato con un errore sconosciuto. |
| ContainerValidationFailed | La convalida del contenitore BLOB ha avuto esito negativo con questo errore: {0}. |
| DeleteWebServiceResourceFailed |  |
| ExceptionDeserializationError |  |
| FailedGettingApiDocument |  |
| FailedStoringWebService |  |
| InvalidMemoryConfiguration | InvalidMemoryConfiguration, ConfigValue: {0} |
| InvalidResourceCacheConfiguration |  |
| InvalidResourceDownloadConfiguration |  |
| InvalidWebServiceResources |  |
| MissingTaskInstance | Non sono stati specificati argomenti. Assicurarsi che vengano passati argomenti validi e riprovare. |
| ModelPackageInvalid |  |
| ModuleExecutionFailed |  |
| ModuleLoadFailed |  |
| ModuleObjectCloneFailed |  |
| OutputConversionFailed |  |
| PortDataTypeNotSupported | La porta con ID={0} ha un tipo di dati non supportato: {1}. |
| ResourceDownload |  |
| ResourceLoadFailed |  |
| ServiceUrisNotFound |  |
| SwaggerGeneration | La generazione di Swagger non è riuscita. Dettagli: {0} |
| UnexpectedScoreStatus |  |
| UnknownBackendErrorResponse |  |
| UnknownError |  |
| UnknownJobStatusCode | Codice di stato del processo sconosciuto {0}. |
| UnknownModuleError |  |
| UpdateWebServiceResourceFailed |  |
| WebServiceGroupNotFound |  |
| WebServicePackageInvalid | InvalidWebServicePackage. Dettagli: {0} |
| WorkerAuthorizationFailed |  |
| WorkerUnreachable |  |
 
## <a name="internalerrorsystemlowonmemory-http-status-code-500"></a>InternalErrorSystemLowOnMemory (codice di stato HTTP 500)
 
Si è verificato un errore interno durante l'esecuzione. La memoria del sistema è insufficiente. Riprova più tardi.
 
 
## <a name="modelpackageformaterror-http-status-code-500"></a>ModelPackageFormatError (codice di stato HTTP 500)
 
Il pacchetto di modelli non è valido. Verificare che il pacchetto di modelli specificato sia corretto e riprovare.
 
 
## <a name="webservicepackageinternalerror-http-status-code-500"></a>WebServicePackageInternalError (codice di stato HTTP 500)
 
Il pacchetto del servizio Web non è valido. Verificare che il pacchetto Web specificato sia corretto e riprovare.
 
| Codice di errore | Messaggio utente |
| ---------- |--------------|
| ModuleError | L'elemento grafico del pacchetto del servizio Web non è valido. Dettagli: {0} |
 
## <a name="initializingcontainers-http-status-code-503"></a>InitializingContainers (codice di stato HTTP 503)
 
Non è possibile eseguire la ricerca, perché i contenitori sono in fase di inizializzazione.
 
 
## <a name="serviceunavailable-http-status-code-503"></a>ServiceUnavailable (codice di stato HTTP 503)
 
Il servizio è temporaneamente non disponibile.
 
| Codice di errore | Messaggio utente |
| ---------- |--------------|
| NoMoreResources | Non sono disponibili risorse per la richiesta. |
| RequestThrottled | La richiesta è stata limitata per l'endpoint {0}. La concorrenza massima per l'endpoint è {1}. |
| TooManyConcurrentRequests | Sono state inviate troppe richieste simultanee. |
| TooManyHostsBeingInitialized | Il numero di host inizializzati contemporaneamente è troppo elevato. Prendere in considerazione la limitazione o la ripetizione dei tentativi. |
| TooManyHostsBeingInitializedPerModel | Il numero di host inizializzati contemporaneamente è troppo elevato. Prendere in considerazione la limitazione o la ripetizione dei tentativi. |
 
## <a name="gatewaytimeout-http-status-code-504"></a>GatewayTimeout (codice di stato HTTP 504)
 
L'operazione non è stata completata nel tempo consentito.
 
| Codice di errore | Messaggio utente |
| ---------- |--------------|
| BackendInitializationTimeout | L'inizializzazione del servizio Web non è stata completata nel tempo consentito. |
| BackendScoreTimeout | L'esecuzione della richiesta del servizio Web non è stata completata nel tempo consentito. |
 
