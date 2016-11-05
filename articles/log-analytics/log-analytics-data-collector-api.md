---
title: API di raccolta dati HTTP di Log Analytics | Microsoft Docs
description: È possibile usare l'API di raccolta dati HTTP di Log Analytics per aggiungere dati POST JSON nel repository di Log Analytics da qualunque client possa chiamare l'API REST. Questo articolo illustra come usare l'API e descrive esempi di come pubblicare i dati con diversi linguaggi di programmazione.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2016
ms.author: bwren

---
# <a name="log-analytics-http-data-collector-api"></a>API di raccolta dati HTTP di Log Analytics
È possibile usare l'API di raccolta dati HTTP di Azure Log Analytics per aggiungere dati POST JSON (JavaScript Object Notation) nel repository di Log Analytics da qualunque client possa chiamare l'API REST. Con questo metodo è possibile inviare dati da applicazioni di terze parti o da script, ad esempio da un runbook in Automazione di Azure.  

## <a name="create-a-request"></a>Creare una richiesta
Le due tabelle seguenti elencano gli attributi necessari per ogni richiesta all'API di raccolta dati HTTP di Log Analytics. Ogni attributo viene descritto con maggiori dettagli più avanti nell'articolo.

### <a name="request-uri"></a>URI della richiesta
| Attributo | Proprietà |
|:--- |:--- |
| Metodo |POST |
| URI |https://<WorkspaceID>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Tipo di contenuto |application/json |

### <a name="request-uri-parameters"></a>Parametri URI della richiesta
| Parametro | Description |
|:--- |:--- |
| CustomerID |Identificatore univoco dell'area di lavoro di Microsoft Operations Management Suite. |
| Risorsa |Nome della risorsa API: /api/logs. |
| Versione dell'API |Versione dell'API da usare con questa richiesta. La versione attuale è 2016-04-01. |

### <a name="request-headers"></a>Intestazioni della richiesta
| Intestazione | Descrizione |
|:--- |:--- |
| Authorization |Firma di autorizzazione. Più avanti nell'articolo sono disponibili informazioni sulla creazione di un'intestazione HMAC-SHA256. |
| Log-Type |Specificare il tipo di record dei dati inviati. Il tipo di log supporta attualmente solo caratteri alfabetici. Non supporta valori numerici o caratteri speciali. |
| x-ms-date |Data di elaborazione della richiesta, in formato RFC 1123. |
| time-generated-field |Nome di un campo nei dati che contiene il timestamp dell'elemento di dati. Se si specifica un campo, il relativo contenuto verrà usato per **TimeGenerated**. Se questo campo non è specificato, il valore predefinito di **TimeGenerated** sarà la data/ora di inserimento del messaggio. Il contenuto del campo del messaggio deve seguire il formato ISO 8601 AAAA-MM-GGThh:mm:ssZ. |

## <a name="authorization"></a>Authorization
Qualsiasi richiesta inviata all'API di raccolta dati HTTP di Log Analytics deve includere l'intestazione dell'autorizzazione. Per autenticare una richiesta è necessario firmarla con la chiave primaria o secondaria dell'area di lavoro che effettua la richiesta. Passare quindi la firma come parte della richiesta.   

Il formato dell'intestazione dell'autorizzazione è il seguente:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* è l'identificatore univoco dell'area di lavoro di Operations Management Suite. *Signature* è un codice [HMAC](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) (Hash-based Message Authentication Code) che viene creato dalla richiesta e quindi calcolato con l'[algoritmo SHA256](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Viene quindi codificato con la codifica Base64.

Usare questo formato per codificare la stringa di firma **SharedKey**:

```
StringToSign = VERB + "\n" +
               Content-Length + "\n" +
               Content-Type + "\n" +
               x-ms-date + "\n" +
               "/api/logs";
```

Di seguito è riportato un esempio di stringa della firma:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

La stringa della firma deve essere codificata usando l'algoritmo HMAC-SHA256 sulla stringa con codifica UTF-8. Il risultato deve essere quindi codificato in Base64. Usare il formato seguente:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Gli esempi nelle sezioni successive indicano il codice di esempio per creare l'intestazione dell'autorizzazione.

## <a name="request-body"></a>Corpo della richiesta
Il corpo del messaggio deve essere in formato JSON. Deve includere uno o più record con coppie di nome e valore di proprietà nel formato seguente:

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

È possibile creare batch di più record in una singola richiesta usando il formato seguente. Tutti i record devono essere dello stesso tipo.

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
},
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

## <a name="record-type-and-properties"></a>Proprietà e tipo di record
Quando si inviano dati con l'API di raccolta dati HTTP di Log Analytics si definisce un tipo di record personalizzato. È attualmente possibile scrivere dati nei tipi di record esistenti creati da altri tipi di dati e soluzioni. Log Analytics legge i dati in ingresso e quindi crea le proprietà che corrispondono ai tipi di dati dei valori immessi.

Ogni richiesta all'API di Log Analytics deve includere un'intestazione **Log-Type** con il nome del tipo di record. Il suffisso **_CL** viene aggiunto automaticamente al nome immesso per distinguerlo da altri tipi di log come log personalizzato. Se ad esempio si immette il nome **MyNewRecordType**, Log Analytics crea un record di tipo **MyNewRecordType_CL**. È così possibile evitare conflitti tra i nomi dei tipi creati dall'utente e i nomi forniti nelle soluzioni Microsoft correnti o future.

Per identificare il tipo di dati di una proprietà, Log Analytics aggiunge un suffisso al nome della proprietà. Una proprietà con un valore null non viene inclusa in tale record. Questa tabella elenca il tipo di dati proprietà e il suffisso corrispondente:

| Tipo di dati proprietà | Suffisso |
|:--- |:--- |
| String |_s |
| Boolean |_b |
| Double |_d |
| Data/ora |_t |
| GUID |_g |

Il tipo di dati usato da Log Analytics per ogni proprietà dipende dall'eventuale esistenza di un tipo di record per il nuovo record.

* Se il tipo di record non esiste, Log Analytics ne creerà uno nuovo. Log Analytics usa l'inferenza del tipo JSON per determinare il tipo di dati per ogni proprietà del nuovo record.
* Se il tipo di record esiste, Log Analytics prova a creare un nuovo record in base alle proprietà esistenti. Se il tipo di dati di una proprietà nel nuovo record non corrisponde e non può essere convertito nel tipo esistente, oppure se il record include una proprietà che non esiste, Log Analytics crea una nuova proprietà con il suffisso pertinente.

Questa voce di invio creerà ad esempio un record con tre proprietà, **number_d**, **boolean_b** e **string_s**:

![Esempio di record 1](media/log-analytics-data-collector-api/record-01.png)

Inviando la voce seguente, con tutti i valori formattati come stringhe, le proprietà non cambieranno. Questi valori possono essere convertiti in tipi di dati esistenti:

![Esempio di record 2](media/log-analytics-data-collector-api/record-02.png)

Con l'invio seguente, tuttavia, Log Analytics creerebbe le nuove proprietà **boolean_d** e **string_d**. Questi valori non possono essere convertiti:

![Esempio di record 3](media/log-analytics-data-collector-api/record-03.png)

Inviando la voce seguente, prima della creazione del tipo di record Log Analytics creerebbe un record con tre proprietà, **number_s**, **boolean_s** e **string_s**. In questa voce, ognuno dei valori iniziali viene formattato come stringa:

![Esempio di record 4](media/log-analytics-data-collector-api/record-04.png)

## <a name="return-codes"></a>Codici restituiti
Il codice di stato HTTP 202 indica che la richiesta è stata accettata per l'elaborazione, ma l'elaborazione non è ancora terminata. L'operazione è stata completata correttamente.

Questa tabella elenca il set completo di codici di stato che il servizio può restituire:

| Codice | Stato | Codice di errore | Descrizione |
|:--- |:--- |:--- |:--- |
| 202 |Accepted | |La richiesta è stata accettata. |
| 400 |Richiesta non valida |InactiveCustomer |L'area di lavoro è stata chiusa. |
| 400 |Richiesta non valida |InvalidApiVersion |La versione API specificata non è stata riconosciuta dal servizio. |
| 400 |Richiesta non valida |InvalidCustomerId |L'ID area di lavoro specificato non è valido. |
| 400 |Richiesta non valida |InvalidDataFormat |È stata inviato JSON non valido. Il corpo della risposta può contenere altre informazioni sulla risoluzione dell'errore. |
| 400 |Richiesta non valida |InvalidLogType |Il tipo di log specificato conteneva caratteri speciali o numeri. |
| 400 |Richiesta non valida |MissingApiVersion |La versione API non è stata specificata. |
| 400 |Richiesta non valida |MissingContentType |Il tipo di contenuto non è stato specificato. |
| 400 |Richiesta non valida |MissingLogType |Il tipo di log dei valori non è stato specificato. |
| 400 |Richiesta non valida |UnsupportedContentType |Il tipo di contenuto non è stato impostato su **application/json**. |
| 403 |Accesso negato |InvalidAuthorization |Il servizio non è riuscito ad autenticare la richiesta. Verificare che l'ID dell'area di lavoro e la chiave di connessione siano validi. |
| 500 |Internal Server Error |UnspecifiedError |Errore interno del servizio. Si prega di ripetere la richiesta. |
| 503 |Servizio non disponibile |ServiceUnavailable |Il servizio non è attualmente disponibile per la ricezione delle richieste. Si prega di ripetere la richiesta. |

## <a name="query-data"></a>Eseguire query sui dati
Per eseguire query sui dati inviati dall'API di raccolta dati HTTP di Log Analytics, cercare i record con valore di **Type** uguale al valore **LogType** specificato, con l'aggiunta di **_CL**. Usando ad esempio **MyCustomLog** verranno restituiti tutti i record con **Type=MyCustomLog_CL**.

## <a name="sample-requests"></a>Richieste di esempio
Nelle sezioni successive sono disponibili esempi di come inviare dati all'API di raccolta dati HTTP di Log Analytics usando diversi linguaggi di programmazione.

Per ogni esempio, seguire questa procedura per impostare le variabili per l'intestazione dell'autorizzazione:

1. Nel portale di Operations Management Suite selezionare il riquadro **Impostazioni** e quindi la scheda **Origini connesse**.
2. A destra di **ID area di lavoro** selezionare l'icona di copia e quindi incollare l'ID come valore della variabile **ID cliente**.
3. A destra di **Chiave primaria** selezionare l'icona di copia e quindi incollare l'ID come valore della variabile **Chiave condivisa**.

In alternativa è possibile modificare le variabili per il tipo di log e i dati JSON.

### <a name="powershell-sample"></a>Esempio PowerShell
```
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# Specify a field with the created time for the records
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-OMSData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -fileName $fileName `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-OMSData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c#-sample"></a>Esempio C
```
using System;
using System.Net;
using System.Security.Cryptography;

namespace OIAPIExample
{
    class ApiExample
    {
// An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField1"":""DemoValue3"",""DemoField2"":""DemoValue4""}]";

// Update customerId to your Operations Management Suite workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

// For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

// LogName is name of the event type that is being submitted to Log Analytics
        static string LogName = "DemoExample";

// You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
// Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            string stringToHash = "POST\n" + json.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

// Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

// Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            string url = "https://"+ customerId +".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";
            using (var client = new WebClient())
            {
                client.Headers.Add(HttpRequestHeader.ContentType, "application/json");
                client.Headers.Add("Log-Type", LogName);
                client.Headers.Add("Authorization", signature);
                client.Headers.Add("x-ms-date", date);
                client.Headers.Add("time-generated-field", TimeStampField);
                client.UploadString(new Uri(url), "POST", json);
            }
        }
    }
}
```

### <a name="python-sample"></a>Esempio Python
```
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Operations Management Suite workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, string_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code == 202):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

## <a name="next-steps"></a>Passaggi successivi
* Usare [Progettazione viste](log-analytics-view-designer.md) per creare visualizzazioni personalizzate dei dati inviati.

<!--HONumber=Oct16_HO2-->


