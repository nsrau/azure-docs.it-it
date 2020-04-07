---
title: L'API REST di gestione della sessioneThe session management REST API
description: Descrive come gestire le sessioni
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 46560f067e020236031487677ad4f48a9560d4e1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681246"
---
# <a name="use-the-session-management-rest-api"></a>Usare l'API REST di gestione della sessioneUse the session management REST API

Per usare la funzionalità di rendering remoto di Azure, è necessario creare una *sessione*. Ogni sessione corrisponde a una macchina virtuale (VM) allocata in Azure e in attesa della connessione di un dispositivo client. Quando un dispositivo si connette, la macchina virtuale esegue il rendering dei dati richiesti e serve il risultato come flusso video. Durante la creazione della sessione, si sceglie il tipo di server in cui eseguire, che determina i prezzi. Una volta che la sessione non è più necessaria, dovrebbe essere interrotta. Se non viene interrotto manualmente, verrà arrestato automaticamente alla scadenza del tempo di *lease* della sessione.

Viene fornito uno script di PowerShell nel [repository degli esempi DI ARR](https://github.com/Azure/azure-remote-rendering) nella cartella *Scripts,* denominata *RenderingSession.ps1*, che illustra l'utilizzo del servizio. Lo script e la relativa configurazione sono descritti di seguito: [Script di PowerShell](../samples/powershell-example-scripts.md) di esempio

> [!TIP]
> I comandi di PowerShell elencati in questa pagina hanno lo scopo di completarsi a vicenda. Se si eseguono tutti gli script in sequenza all'interno dello stesso prompt dei comandi di PowerShell, questi verranno compilati uno sopra l'altro.

## <a name="regions"></a>Regioni

Consulta [l'elenco delle aree disponibili](../reference/regions.md) per gli URL di base a cui inviare le richieste.

Per gli script di esempio riportati di seguito è stata scelta l'area *westus2*.

### <a name="example-script-choose-an-endpoint"></a>Script di esempio: scegliere un endpointExample script: Choose an endpoint

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>Account

Se non si dispone di un account di rendering remoto, [crearne uno](create-an-account.md). Ogni risorsa è identificata da un *accountId*, che viene utilizzato in tutte le API della sessione.

### <a name="example-script-set-accountid-and-accountkey"></a>Script di esempio: Impostare accountId e accountKeyExample script: Set accountId and accountKey

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>Intestazioni di richiesta comuni

* L'intestazione *Authorization* deve`Bearer TOKEN`avere il`TOKEN`valore di " ", dove " " è il token di autenticazione [restituito dal servizio token di](tokens.md)sicurezza .

### <a name="example-script-request-a-token"></a>Script di esempio: richiedere un tokenExample script: Request a token

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>Intestazioni di risposta comuni

* L'intestazione *MS-CV* può essere utilizzata dal team del prodotto per tracciare la chiamata all'interno del servizio.

## <a name="create-a-session"></a>Creare una sessione

Questo comando crea una sessione. Restituisce l'ID della nuova sessione. È necessario l'ID di sessione per tutti gli altri comandi.

| URI | Metodo |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/create | POST |

**Corpo della richiesta:**

* maxLeaseTime (timespan): un valore di timeout quando la macchina virtuale verrà rimossa automaticamente
* models (array): URL del contenitore di asset da precaricare
* size (stringa): la dimensione della macchina virtuale (**"standard"** o **"premium"**). Vedere [limitazioni](../reference/limits.md#overall-number-of-polygons)specifiche delle dimensioni della macchina virtuale.

**Risposte:**

| Codice di stato | payload JSON | Commenti |
|-----------|:-----------|:-----------|
| 202 | - sessionId: GUID | Operazione completata |

### <a name="example-script-create-a-session"></a>Script di esempio: creare una sessioneExample script: Create a session

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/create" -Method Post -ContentType "application/json" -Body "{ 'maxLeaseTime': '4:0:0', 'models': [], 'size': 'standard' }" -Headers @{ Authorization = "Bearer $token" }
```

Output di esempio:

```PowerShell
StatusCode        : 202
StatusDescription : Accepted
Content           : {"sessionId":"d31bddca-dab7-498e-9bc9-7594bc12862f"}
RawContent        : HTTP/1.1 202 Accepted
                    MS-CV: 5EqPJ1VdTUakDJZc6/ZhTg.0
                    Content-Length: 52
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:17:50 GMT
                    Location: accounts/11111111-1111-1111-11...
Forms             : {}
Headers           : {[MS-CV, 5EqPJ1VdTUakDJZc6/ZhTg.0], [Content-Length, 52], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:17:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 52
```

### <a name="example-script-store-sessionid"></a>Script di esempio: Store sessionIdExample script: Store sessionId

La risposta dalla richiesta precedente include un **sessionId**, che è necessario per tutte le richieste di completamento.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="update-a-session"></a>Aggiornare una sessione

Questo comando aggiorna i parametri di una sessione. Attualmente è possibile estendere solo il tempo di lease di una sessione.

> [!IMPORTANT]
> Il tempo di lease viene sempre assegnato come tempo totale dall'inizio della sessione. Ciò significa che se è stata creata una sessione con un tempo di lease di un'ora e si desidera estendere il tempo di lease per un'altra ora, è necessario aggiornare maxLeaseTime a due ore.

| URI | Metodo |
|-----------|:-----------|
| /v1/accounts/*accountID*/sessions/*iddellasessione* | PATCH |

**Corpo della richiesta:**

* maxLeaseTime (timespan): un valore di timeout quando la macchina virtuale verrà rimossa automaticamente

**Risposte:**

| Codice di stato | payload JSON | Commenti |
|-----------|:-----------|:-----------|
| 200 | | Operazione completata |

### <a name="example-script-update-a-session"></a>Script di esempio: aggiornare una sessioneExample script: Update a session

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Patch -ContentType "application/json" -Body "{ 'maxLeaseTime': '5:0:0' }" -Headers @{ Authorization = "Bearer $token" }
```

Output di esempio:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: Fe+yXCJumky82wuoedzDTA.0
                    Content-Length: 0
                    Date: Thu, 09 May 2019 16:27:31 GMT


Headers           : {[MS-CV, Fe+yXCJumky82wuoedzDTA.0], [Content-Length, 0], [Date, Thu, 09 May 2019 16:27:31 GMT]}
RawContentLength  : 0
```

## <a name="get-active-sessions"></a>Ottenere sessioni attiveGet active sessions

Questo comando restituisce un elenco di sessioni attive.

| URI | Metodo |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions | GET |

**Risposte:**

| Codice di stato | payload JSON | Commenti |
|-----------|:-----------|:-----------|
| 200 | - sessioni: matrice di proprietà della sessione- sessions: array of session properties | vedere la sezione "Ottenere le proprietà della sessione" per una descrizione delle proprietà della sessione |

### <a name="example-script-query-active-sessions"></a>Script di esempio: eseguire query sulle sessioni attiveExample script: Query active sessions

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Output di esempio:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    MS-CV: WfB9Cs5YeE6S28qYkp7Bhw.1
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 25 Jul 2019 16:23:50 GMT

                    {"sessions":[]}
Forms             : {}
Headers           : {[MS-CV, WfB9Cs5YeE6S28qYkp7Bhw.1], [Content-Length, 2], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 25 Jul 2019 16:23:50 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

## <a name="get-sessions-properties"></a>Ottenere le proprietà delle sessioniGet sessions properties

Questo comando restituisce informazioni su una sessione, ad esempio il nome host della macchina virtuale.

| URI | Metodo |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/*sessionId*/properties | GET |

**Risposte:**

| Codice di stato | payload JSON | Commenti |
|-----------|:-----------|:-----------|
| 200 | - messaggio: stringa<br/>- sessionElapsedTime: periodo di tempo<br/>- sessionHostname: stringa<br/>- sessionId: stringa<br/>- sessionMaxLeaseTime: intervallo di tempo<br/>- sessionSize: enum<br/>- sessionStatus: enum | enum sessionStatus - avvio, pronto, arresto, arresto, scaduto, errore<br/>Se lo stato è 'errore' o 'scaduto', il messaggio conterrà ulteriori informazioni |

### <a name="example-script-get-session-properties"></a>Script di esempio: ottenere le proprietà della sessioneExample script: Get session properties

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId/properties" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

Output di esempio:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {"message":null,"sessionElapsedTime":"00:00:01","sessionHostname":"5018fee8-817e-4366-9179-556af79a4240.remoterenderingvm.westeurope.mixedreality.azure.com","sessionId":"e13d2c44-63e0-4591-991e-f9e05e599a93","sessionMaxLeaseTime":"04:00:00","sessionStatus":"Ready"}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: CMXegpZRMECH4pbOW2j5GA.0
                    Content-Length: 60
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:30:38 GMT

                    {"message":null,...
Forms             : {}
Headers           : {[MS-CV, CMXegpZRMECH4pbOW2j5GA.0], [Content-Length, 60], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:30:38 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 60
```

## <a name="stop-a-session"></a>Interrompere una sessione

Questo comando interrompe una sessione. La macchina virtuale allocata verrà recuperata poco dopo.

| URI | Metodo |
|-----------|:-----------|
| /v1/accounts/*accountId*/session/*iddellasessione* | DELETE |

**Risposte:**

| Codice di stato | payload JSON | Commenti |
|-----------|:-----------|:-----------|
| 204 | | Operazione completata |

### <a name="example-script-stop-a-session"></a>Script di esempio: interrompere una sessioneExample script: Stop a session

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Delete -Headers @{ Authorization = "Bearer $token" }
```

Output di esempio:

```PowerShell
StatusCode        : 204
StatusDescription : No Content
Content           : {}
RawContent        : HTTP/1.1 204 No Content
                    MS-CV: YDxR5/7+K0KstH54WG443w.0
                    Date: Thu, 09 May 2019 16:45:41 GMT


Headers           : {[MS-CV, YDxR5/7+K0KstH54WG443w.0], [Date, Thu, 09 May 2019 16:45:41 GMT]}
RawContentLength  : 0
```

## <a name="next-steps"></a>Passaggi successivi

* [Script di PowerShell di esempioExample PowerShell scripts](../samples/powershell-example-scripts.md)
