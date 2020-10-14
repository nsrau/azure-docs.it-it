---
title: Recuperare i log di IoT Edge-Azure IoT Edge
description: IoT Edge il recupero del log del modulo e il caricamento nell'archiviazione BLOB di Azure.
author: v-tcassi
manager: philmea
ms.author: v-tcassi
ms.date: 09/14/2020
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: f5f2a9800d3796d217294e757076d6ff706281d1
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92044199"
---
# <a name="retrieve-logs-from-iot-edge-deployments"></a>Recuperare i log da distribuzioni IoT Edge

Recuperare i log dalle distribuzioni di IoT Edge senza richiedere l'accesso fisico o SSH al dispositivo usando i metodi diretti inclusi nel modulo IoT Edge Agent. I metodi diretti vengono implementati nel dispositivo e quindi possono essere richiamati dal cloud. L'agente IoT Edge include metodi diretti che consentono di monitorare e gestire i dispositivi di IoT Edge in modalità remota. I metodi diretti descritti in questo articolo sono disponibili a livello generale con la versione 1.0.10.

Per altre informazioni sui metodi diretti, su come usarli e su come implementarli nei moduli personali, vedere [comprendere e richiamare metodi diretti dall'hub](../iot-hub/iot-hub-devguide-direct-methods.md)Internet.

I nomi di questi metodi diretti vengono gestiti con distinzione tra maiuscole e minuscole.

## <a name="recommended-logging-format"></a>Formato di registrazione consigliato

Sebbene non sia necessario, per una migliore compatibilità con questa funzionalità, il formato di registrazione consigliato è:

```
<{Log Level}> {Timestamp} {Message Text}
```

`{Log Level}` deve seguire il [formato del livello di gravità syslog](https://wikipedia.org/wiki/Syslog#Severity_lnevel) e `{Timestamp}` deve essere formattato come `yyyy-mm-dd hh:mm:ss.fff zzz` .

La [classe logger in IOT Edge](https://github.com/Azure/iotedge/blob/master/edge-util/src/Microsoft.Azure.Devices.Edge.Util/Logger.cs) funge da implementazione canonica.

## <a name="retrieve-module-logs"></a>Recuperare i log dei moduli

Usare il metodo diretto **GetModuleLogs** per recuperare i log di un modulo IOT Edge.

Questo metodo accetta un payload JSON con lo schema seguente:

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": int,
                "since": int,
                "until": int,
                "loglevel": int,
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Nome | Type | Descrizione |
|-|-|-|
| schemaVersion | Stringa | Impostare su `1.0` |
| items | Matrice JSON | Matrice con `id` `filter` Tuple e. |
| ID | Stringa | Espressione regolare che fornisce il nome del modulo. Può corrispondere a più moduli in un dispositivo perimetrale. È previsto il formato delle [espressioni regolari di .NET](/dotnet/standard/base-types/regular-expressions) . |
| filter | Sezione JSON | Filtri di log da applicare ai moduli corrispondenti all' `id` espressione regolare nella tupla. |
| coda | numero intero | Numero di righe di log nel passato da recuperare a partire dall'ultima. FACOLTATIVO |
| since | numero intero | Restituire i log solo dopo questa volta, come durata (1 d, 90 m, 2 giorni 3 ore 2 minuti), timestamp rfc3339 o timestamp UNIX.  Se `tail` `since` vengono specificati sia che, i log vengono recuperati usando `since` prima il valore. Quindi, il `tail` valore viene applicato al risultato e viene restituito il risultato finale. FACOLTATIVO |
| until | numero intero | Restituisce i log solo prima dell'ora specificata, come timestamp rfc3339, timestamp UNIX o durata (1 d, 90 m, 2 giorni 3 ore 2 minuti). FACOLTATIVO |
| livello di registrazione | numero intero | Filtrare le righe di log inferiori o uguali al livello di log specificato. Le linee di log devono seguire il formato di registrazione consigliato e usare lo standard del [livello di gravità syslog](https://en.wikipedia.org/wiki/Syslog#Severity_level) . FACOLTATIVO |
| regex | Stringa | Filtrare le righe di log con contenuto corrispondente all'espressione regolare specificata utilizzando il formato delle [espressioni regolari di .NET](/dotnet/standard/base-types/regular-expressions) . FACOLTATIVO |
| codifica | Stringa | `gzip` o `none`. Il valore predefinito è `none`. |
| contentType | string | `json` o `text`. Il valore predefinito è `text`. |

> [!NOTE]
> Se il contenuto dei log supera il limite di dimensioni della risposta di metodi diretti, che è attualmente 128 KB, la risposta restituisce un errore.

Il recupero dei log con esito positivo restituisce uno **"stato": 200** seguito da un payload contenente i log recuperati dal modulo, filtrati in base alle impostazioni specificate nella richiesta.

Ad esempio:

```azurecli
az iot hub invoke-module-method --method-name 'GetModuleLogs' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
'
```

Nel portale di Azure richiamare il metodo con il nome del metodo `GetModuleLogs` e il payload JSON seguente:

```json
    {
       "schemaVersion": "1.0",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Richiama il metodo diretto ' GetModuleLogs ' in portale di Azure](./media/how-to-retrieve-iot-edge-logs/invoke-get-module-logs.png)

È anche possibile inviare tramite pipe l'output dell'interfaccia della riga di comando alle utilità Linux, ad esempio [gzip](https://en.wikipedia.org/wiki/Gzip), per elaborare una risposta compressa. Ad esempio:

```azurecli
az iot hub invoke-module-method \
  --method-name 'GetModuleLogs' \
  -n <hub name> \
  -d <device id> \
  -m '$edgeAgent' \
  --method-payload '{"contentType": "text","schemaVersion": "1.0","encoding": "gzip","items": [{"id": "edgeHub","filter": {"since": "2d","tail": 1000}}],}' \
  -o tsv --query 'payload[0].payloadBytes' \
  | base64 --decode \
  | gzip -d
```

## <a name="upload-module-logs"></a>Caricare i registri dei moduli

Usare il metodo diretto **UploadModuleLogs** per inviare i log richiesti a un contenitore di archiviazione BLOB di Azure specificato.

Questo metodo accetta un payload JSON simile a **GetModuleLogs**, con l'aggiunta della chiave "sasUrl":

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "Full path to SAS URL",
       "items": [
          {
             "id": "regex string",
             "filter": {
                "tail": int,
                "since": int,
                "until": int,
                "loglevel": int,
                "regex": "regex string"
             }
          }
       ],
       "encoding": "gzip/none",
       "contentType": "json/text" 
    }
```

| Nome | Type | Descrizione |
|-|-|-|
| sasURL | stringa (URI) | [URL della firma di accesso condiviso con accesso in scrittura al contenitore di archiviazione BLOB di Azure](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer). |

Una richiesta di caricamento dei log riuscita restituisce uno **"status": 200** seguito da un payload con lo schema seguente:

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Nome | Type | Descrizione |
|-|-|-|
| status | string | Uno tra `NotStarted` , `Running` , `Completed` , `Failed` o `Unknown` . |
| message | string | Message se error, stringa vuota in caso contrario. |
| correlationId | string   | ID per eseguire una query sullo stato della richiesta di caricamento. |

Ad esempio:

La chiamata seguente carica le ultime 100 righe di log da tutti i moduli, in formato JSON compresso:

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": ".*",
                "filter": {
                    "tail": 100
                }
            }
        ],
        "encoding": "gzip",
        "contentType": "json"
    }
'
```

La chiamata seguente carica le ultime 100 righe di log da edgeAgent e edgeHub con le ultime 1000 righe di log del modulo tempSensor in formato testo non compresso:

```azurecli
az iot hub invoke-module-method --method-name UploadModuleLogs -n <hub name> -d <device id> -m \$edgeAgent --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "<sasUrl>",
        "items": [
            {
                "id": "edge",
                "filter": {
                    "tail": 100
                }
            },
            {
                "id": "tempSensor",
                "filter": {
                    "tail": 1000
                }
            }
        ],
        "encoding": "none",
        "contentType": "text"
    }
'
```

Nel portale di Azure richiamare il metodo con il nome del metodo `UploadModuleLogs` e il payload JSON seguente dopo aver popolato il sasURL con le informazioni:

```json
    {
       "schemaVersion": "1.0",
       "sasUrl": "<sasUrl>",
       "items": [
          {
             "id": "edgeAgent",
             "filter": {
                "tail": 10
             }
          }
       ],
       "encoding": "none",
       "contentType": "text"
    }
```

![Richiama il metodo diretto ' UploadModuleLogs ' in portale di Azure](./media/how-to-retrieve-iot-edge-logs/invoke-upload-module-logs.png)

## <a name="upload-support-bundle-diagnostics"></a>Diagnostica di caricamento del bundle di supporto

Usare il metodo diretto **UploadSupportBundle** per aggregare e caricare un file zip dei log del modulo IOT Edge in un contenitore di archiviazione BLOB di Azure disponibile. Questo metodo diretto esegue il [`iotedge support-bundle`](./troubleshoot.md#gather-debug-information-with-support-bundle-command) comando sul dispositivo IOT Edge per ottenere i log.

Questo metodo accetta un payload JSON con lo schema seguente:

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

| Nome | Type | Descrizione |
|-|-|-|
| schemaVersion | Stringa | Impostare su `1.0` |
| sasURL | stringa (URI) | [URL della firma di accesso condiviso con accesso in scrittura al contenitore di archiviazione BLOB di Azure](/archive/blogs/jpsanders/easily-create-a-sas-to-download-a-file-from-azure-storage-using-azure-storage-explorer) |
| since | numero intero | Restituire i log solo dopo questa volta, come durata (1 d, 90 m, 2 giorni 3 ore 2 minuti), timestamp rfc3339 o timestamp UNIX. FACOLTATIVO |
| until | numero intero | Restituisce i log solo prima dell'ora specificata, come timestamp rfc3339, timestamp UNIX o durata (1 d, 90 m, 2 giorni 3 ore 2 minuti). FACOLTATIVO |
| edgeRuntimeOnly | boolean | Se è true, vengono restituiti solo i log dell'agente Edge, dell'Hub Edge e del daemon di sicurezza perimetrale. Valore predefinito: false.  FACOLTATIVO |

> [!IMPORTANT]
> IoT Edge bundle di supporto potrebbe contenere informazioni personali.

Una richiesta di caricamento dei log riuscita restituisce uno **"status": 200** seguito da un payload con lo stesso schema della risposta **UploadModuleLogs** :

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Nome | Type | Descrizione |
|-|-|-|
| status | string | Uno tra `NotStarted` , `Running` , `Completed` , `Failed` o `Unknown` . |
| message | string | Message se error, stringa vuota in caso contrario. |
| correlationId | string   | ID per eseguire una query sullo stato della richiesta di caricamento. |

Ad esempio:

```azurecli
az iot hub invoke-module-method --method-name 'UploadSupportBundle' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
'
```

Nel portale di Azure richiamare il metodo con il nome del metodo `UploadSupportBundle` e il payload JSON seguente dopo aver popolato il sasURL con le informazioni:

```json
    {
        "schemaVersion": "1.0",
        "sasUrl": "Full path to SAS url",
        "since": "2d",
        "until": "1d",
        "edgeRuntimeOnly": false
    }
```

![Richiama il metodo diretto ' UploadSupportBundle ' in portale di Azure](./media/how-to-retrieve-iot-edge-logs/invoke-upload-support-bundle.png)

## <a name="get-upload-request-status"></a>Ottenere lo stato della richiesta di caricamento

Usare il metodo diretto **GetTaskStatus** per eseguire una query sullo stato di una richiesta di caricamento dei log. Il payload della richiesta **GetTaskStatus** usa la `correlationId` della richiesta di caricamento dei log per ottenere lo stato dell'attività. `correlationId`Viene restituito in risposta alla chiamata al metodo diretto **UploadModuleLogs** .

Questo metodo accetta un payload JSON con lo schema seguente:

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

Una richiesta di caricamento dei log riuscita restituisce uno **"status": 200** seguito da un payload con lo stesso schema della risposta **UploadModuleLogs** :

```json
    {
        "status": "string",
        "message": "string",
        "correlationId": "GUID"
    }
```

| Nome | Type | Descrizione |
|-|-|-|
| status | string | Uno tra `NotStarted` , `Running` , `Completed` , `Failed` o `Unknown` . |
| message | string | Message se error, stringa vuota in caso contrario. |
| correlationId | string   | ID per eseguire una query sullo stato della richiesta di caricamento. |

Ad esempio:

```azurecli
az iot hub invoke-module-method --method-name 'GetTaskStatus' -n <hub name> -d <device id> -m '$edgeAgent' --method-payload \
'
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
'
```

Nel portale di Azure richiamare il metodo con il nome del metodo `UploadModuleLogs` e il payload JSON seguente dopo aver compilato il GUID con le informazioni:

```json
    {
      "schemaVersion": "1.0",
      "correlationId": "<GUID>"
    }
```

![Richiama il metodo diretto ' GetTaskStatus ' in portale di Azure](./media/how-to-retrieve-iot-edge-logs/invoke-get-task-status.png)

## <a name="next-steps"></a>Passaggi successivi

[Proprietà dei moduli gemelli "agente di IoT Edge" e "hub di IoT Edge"](module-edgeagent-edgehub.md)