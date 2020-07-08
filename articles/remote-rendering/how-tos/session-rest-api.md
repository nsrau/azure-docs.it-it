---
title: API REST di gestione della sessione
description: Viene descritto come gestire le sessioni
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 46560f067e020236031487677ad4f48a9560d4e1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80681246"
---
# <a name="use-the-session-management-rest-api"></a>Usare l'API REST di gestione delle sessioni

Per usare la funzionalità di rendering remoto di Azure, è necessario creare una *sessione*. Ogni sessione corrisponde a una macchina virtuale (VM) allocata in Azure e in attesa della connessione di un dispositivo client. Quando un dispositivo si connette, la macchina virtuale esegue il rendering dei dati richiesti e fornisce il risultato come flusso video. Durante la creazione della sessione, è stato scelto il tipo di server in cui si desidera eseguire, che determina i prezzi. Quando la sessione non è più necessaria, è consigliabile arrestarla. Se non viene arrestato manualmente, viene arrestato automaticamente al termine del *periodo di lease* della sessione.

Viene fornito uno script di PowerShell nel [repository di esempi arr](https://github.com/Azure/azure-remote-rendering) nella cartella *Scripts* , denominato *RenderingSession.ps1*, che illustra l'uso del servizio. Lo script e la relativa configurazione sono descritti di seguito: [script di PowerShell di esempio](../samples/powershell-example-scripts.md)

> [!TIP]
> I comandi di PowerShell elencati in questa pagina sono destinati a essere integrati tra loro. Se si eseguono tutti gli script in sequenza all'interno dello stesso prompt dei comandi di PowerShell, verranno compilati uno sopra l'altro.

## <a name="regions"></a>Regioni

Vedere l' [elenco delle aree disponibili](../reference/regions.md) per gli URL di base a cui inviare le richieste.

Per gli script di esempio seguenti è stata scelta l'area *westus2*.

### <a name="example-script-choose-an-endpoint"></a>Script di esempio: scegliere un endpoint

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>Account

Se non si dispone di un account di rendering remoto, [crearne uno](create-an-account.md). Ogni risorsa viene identificata da un *accountId*, che viene usato in tutte le API della sessione.

### <a name="example-script-set-accountid-and-accountkey"></a>Script di esempio: set accountId e accountKey

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>Intestazioni di richiesta comuni

* L'intestazione dell' *autorizzazione* deve avere il valore " `Bearer TOKEN` ", dove " `TOKEN` " è il token di autenticazione [restituito dal servizio token di sicurezza](tokens.md).

### <a name="example-script-request-a-token"></a>Script di esempio: richiedere un token

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>Intestazioni di risposta comuni

* L'intestazione *MS-CV* può essere usata dal team del prodotto per tracciare la chiamata all'interno del servizio.

## <a name="create-a-session"></a>Creare una sessione

Questo comando crea una sessione. Restituisce l'ID della nuova sessione. È necessario l'ID di sessione per tutti gli altri comandi.

| URI | Metodo |
|-----------|:-----------|
| /V1/Accounts/*accountId*/Sessions/create | POST |

**Corpo della richiesta:**

* maxLeaseTime (TimeSpan): valore di timeout quando la macchina virtuale verrà ritirata automaticamente
* modelli (Array): URL del contenitore di asset da precaricare
* Size (String): dimensioni della macchina virtuale (**"standard"** o **"Premium"**). Vedere [Limitazioni delle dimensioni della macchina virtuale](../reference/limits.md#overall-number-of-polygons) per le specifiche.

**Risposte**

| Codice di stato | payload JSON | Commenti |
|-----------|:-----------|:-----------|
| 202 | -sessionId: GUID | Operazione completata |

### <a name="example-script-create-a-session"></a>Script di esempio: creare una sessione

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

### <a name="example-script-store-sessionid"></a>Script di esempio: Archivio sessionId

La risposta della richiesta precedente include un **SessionID**, necessario per tutte le richieste di completamento.

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="update-a-session"></a>Aggiornare una sessione

Questo comando Aggiorna i parametri di una sessione. Attualmente è possibile estendere solo il tempo di lease di una sessione.

> [!IMPORTANT]
> Il tempo di lease viene sempre fornito come tempo totale dall'inizio della sessione. Ciò significa che se è stata creata una sessione con una durata di lease di un'ora e si desidera estendere la durata del lease per un'altra ora, è necessario aggiornare il maxLeaseTime a due ore.

| URI | Metodo |
|-----------|:-----------|
| /V1/Accounts/*AccountID*/Sessions/*SessionID* | PATCH |

**Corpo della richiesta:**

* maxLeaseTime (TimeSpan): valore di timeout quando la macchina virtuale verrà ritirata automaticamente

**Risposte**

| Codice di stato | payload JSON | Commenti |
|-----------|:-----------|:-----------|
| 200 | | Operazione completata |

### <a name="example-script-update-a-session"></a>Script di esempio: aggiornare una sessione

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

## <a name="get-active-sessions"></a>Ottenere sessioni attive

Questo comando restituisce un elenco di sessioni attive.

| URI | Metodo |
|-----------|:-----------|
| /V1/Accounts/*accountId*/Sessions | GET |

**Risposte**

| Codice di stato | payload JSON | Commenti |
|-----------|:-----------|:-----------|
| 200 | -Sessions: matrice di proprietà della sessione | vedere la sezione "ottenere le proprietà della sessione" per una descrizione delle proprietà della sessione |

### <a name="example-script-query-active-sessions"></a>Script di esempio: eseguire query sulle sessioni attive

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

## <a name="get-sessions-properties"></a>Proprietà Get Sessions

Questo comando restituisce informazioni su una sessione, ad esempio il nome host della macchina virtuale.

| URI | Metodo |
|-----------|:-----------|
| /V1/Accounts/*accountId*/Sessions/*SessionID*/Properties | GET |

**Risposte**

| Codice di stato | payload JSON | Commenti |
|-----------|:-----------|:-----------|
| 200 | -Message: stringa<br/>-sessionElapsedTime: TimeSpan<br/>-sessionHostname: stringa<br/>-sessionId: stringa<br/>-sessionMaxLeaseTime: TimeSpan<br/>-sessionSize: enumerazione<br/>-sessionStatus: enumerazione | enum sessionStatus {starting, Ready, stoping, Stopped, expired, Error}<br/>Se lo stato è' Error ' o ' expired ', il messaggio conterrà ulteriori informazioni |

### <a name="example-script-get-session-properties"></a>Script di esempio: ottenere le proprietà della sessione

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

## <a name="stop-a-session"></a>Arrestare una sessione

Questo comando Arresta una sessione. La macchina virtuale allocata verrà recuperata subito dopo.

| URI | Metodo |
|-----------|:-----------|
| /V1/Accounts/*accountId*/Sessions/*SessionID* | DELETE |

**Risposte**

| Codice di stato | payload JSON | Commenti |
|-----------|:-----------|:-----------|
| 204 | | Operazione completata |

### <a name="example-script-stop-a-session"></a>Script di esempio: arrestare una sessione

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

* [Script di Azure PowerShell di esempio](../samples/powershell-example-scripts.md)
