---
title: "Come usare un'identità del servizio gestito di una macchina virtuale di Azure per acquisire un token di accesso"
description: "Istruzioni dettagliate ed esempi relativi all'uso di un'identità del servizio gestito di una macchina virtuale di Azure per acquisire un token di accesso OAuth."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: daveba
ms.openlocfilehash: 3d9d4d682a25d11129e81855a6bf149ac1d5cff0
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-token-acquisition"></a>Come usare un'identità del servizio gestito di una macchina virtuale di Azure per l'acquisizione di token 

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]  
Questo articolo fornisce vari esempi di codice e script per l'acquisizione di token, oltre a indicazioni su argomenti importanti come la gestione degli errori HTTP e di scadenza dei token.

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Se si prevede di usare gli esempi di Azure PowerShell presenti in questo articolo, assicurarsi di installare la versione più recente di [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM).


> [!IMPORTANT]
> - Tutti gli esempi di codice e script in questo articolo presuppongono che il client sia in esecuzione in una macchina virtuale abilitata per l'identità del servizio gestito. Usare la funzionalità di connessione alla macchina virtuale nel portale di Azure per connettersi in remoto alla macchina virtuale. Per informazioni dettagliate sull'abilitazione dell'identità del servizio gestito in una macchina virtuale, vedere [Configurare un'Identità del servizio gestito della macchina virtuale tramite il portale di Azure](msi-qs-configure-portal-windows-vm.md) o una delle varianti dell'articolo (per PowerShell, interfaccia della riga di comando, un modello o una versione di Azure SDK). 

## <a name="overview"></a>Panoramica

Un'applicazione client può richiedere un [token di accesso solo app](develop/active-directory-dev-glossary.md#access-token) dell'identità del servizio gestito per accedere a una determinata risorsa. Il token è [basato sull'entità servizio dell'identità del servizio gestito](msi-overview.md#how-does-it-work). Non è quindi necessario la registrazione del client per ottenere un token di accesso nell'ambito della propria entità servizio. Il token è adatto per l'uso come token di connessione nelle [chiamate da servizio a servizio che richiedono le credenziali client](active-directory-protocols-oauth-service-to-service.md).

|  |  |
| -------------- | -------------------- |
| [Ottenere un token tramite HTTP](#get-a-token-using-http) | Dettagli del protocollo per l'endpoint del token dell'identità del servizio gestito |
| [Ottenere un token tramite C#](#get-a-token-using-c) | Esempio d'uso dell'endpoint REST dell'identità del servizio gestito da un client C# |
| [Ottenere un token tramite Go](#get-a-token-using-go) | Esempio d'uso dell'endpoint REST dell'identità del servizio gestito da un client Go |
| [Ottenere un token tramite Azure PowerShell](#get-a-token-using-azure-powershell) | Esempio d'uso dell'endpoint REST dell'identità del servizio gestito da un client PowerShell |
| [Ottenere un token tramite CURL](#get-a-token-using-curl) | Esempio d'uso dell'endpoint REST dell'identità del servizio gestito da un client Bash/CURL |
| [Gestione della scadenza di token](#handling-token-expiration) | Indicazioni per la gestione di token di accesso scaduti |
| [Gestione degli errori](#error-handling) | Indicazioni per la gestione di errori HTTP restituiti dall'endpoint del token dell'identità del servizio gestito |
| [ID di risorsa per servizi di Azure](#resource-ids-for-azure-services) | Come ottenere ID di risorsa per i servizi di Azure supportati |

## <a name="get-a-token-using-http"></a>Ottenere un token tramite HTTP 

L'interfaccia di base per l'acquisizione di un token di accesso è basata su REST, in modo che sia accessibile da qualsiasi applicazione client in esecuzione sulla macchina virtuale che può effettuare chiamate HTTP REST. Si tratta quindi di un meccanismo molto simile al modello di programmazione di Azure AD, tranne per il fatto che il client usa un endpoint localhost nella macchina virtuale (e non un endpoint di Azure AD).

Richiesta di esempio:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | DESCRIZIONE |
| ------- | ----------- |
| `GET` | Verbo HTTP, che indica che si vuole recuperare i dati dall'endpoint. In questo caso, un token di accesso OAuth. | 
| `http://localhost:50342/oauth2/token` | Endpoint dell'identità del servizio gestito, dove 50342 è la porta predefinita ed è configurabile. |
| `resource` | Parametro della stringa di query, che indica l'URI ID app della risorsa di destinazione. Viene visualizzato anche nell'attestazione `aud` (audience, destinatari) del token emesso. In questo esempio viene richiesto un token per accedere ad Azure Resource Manager, con l'URI di ID app https://management.azure.com/. |
| `Metadata` | Campo di intestazione della richiesta HTTP, richiesto dall'identità del servizio gestito come mitigazione contro attacchi SSRF (Server Side Request Forgery). Questo valore deve essere impostato su "true", usando tutte lettere minuscole.

Risposta di esempio:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Elemento | DESCRIZIONE |
| ------- | ----------- |
| `access_token` | Token di accesso richiesto. Quando si chiama un'API REST protetta, il token è incorporato nel campo di intestazione della richiesta `Authorization` come token di connessione, in modo da consentire all'API di autenticare il chiamante. | 
| `refresh_token` | Non usato dall'identità del servizio gestito. |
| `expires_in` | Numero di secondi per cui il token di accesso continua a essere valido, prima della scadenza, dal momento del rilascio. L'ora del rilascio è indicata nell'attestazione `iat` del token. |
| `expires_on` | Intervallo di tempo in cui il token di accesso scade. La data è rappresentata come numero di secondi da "1970-01-01T0:0:0Z UTC" (corrisponde all'attestazione `exp` del token). |
| `not_before` | Intervallo di tempo in cui il token di accesso è valido e può essere accettato. La data è rappresentata come numero di secondi da "1970-01-01T0:0:0Z UTC" (corrisponde all'attestazione `nbf` del token). |
| `resource` | Risorsa per cui è stato richiesto il token di accesso, che corrisponde al parametro della stringa di query `resource` della richiesta. |
| `token_type` | Tipo di token, ovvero un token di accesso di connessione, che indica che la risorsa può concedere l'accesso al titolare del token. |

## <a name="get-a-token-using-c"></a>Ottenere un token tramite C#

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

## <a name="get-a-token-using-go"></a>Ottenere un token tramite Go

```
package main

import (
  "fmt"
  "io/ioutil"
  "net/http"
  "net/url"
  "encoding/json"
)

type responseJson struct {
  AccessToken string `json:"access_token"`
  RefreshToken string `json:"refresh_token"`
  ExpiresIn string `json:"expires_in"`
  ExpiresOn string `json:"expires_on"`
  NotBefore string `json:"not_before"`
  Resource string `json:"resource"`
  TokenType string `json:"token_type"`
}

func main() {
    
    // Create HTTP request for MSI token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://localhost:50342/oauth2/token")
    if err != nil {
      fmt.Println("Error creating URL: ", err)
      return 
    }
    msi_parameters := url.Values{}
    msi_parameters.Add("resource", "https://management.azure.com/")
    msi_endpoint.RawQuery = msi_parameters.Encode()
    req, err := http.NewRequest("GET", msi_endpoint.String(), nil)
    if err != nil {
      fmt.Println("Error creating HTTP request: ", err)
      return 
    }
    req.Header.Add("Metadata", "true")

    // Call MSI /token endpoint
    client := &http.Client{}
    resp, err := client.Do(req) 
    if err != nil{
      fmt.Println("Error calling token endpoint: ", err)
      return
    }

    // Pull out response body
    responseBytes,err := ioutil.ReadAll(resp.Body)
    defer resp.Body.Close()
    if err != nil {
      fmt.Println("Error reading response body : ", err)
      return
    }

    // Unmarshall response body into struct
    var r responseJson
    err = json.Unmarshal(responseBytes, &r)
    if err != nil {
      fmt.Println("Error unmarshalling the response:", err)
      return
    }

    // Print HTTP response and marshalled response body elements to console
    fmt.Println("Response status:", resp.Status)
    fmt.Println("access_token: ", r.AccessToken)
    fmt.Println("refresh_token: ", r.RefreshToken)
    fmt.Println("expires_in: ", r.ExpiresIn)
    fmt.Println("expires_on: ", r.ExpiresOn)
    fmt.Println("not_before: ", r.NotBefore)
    fmt.Println("resource: ", r.Resource)
    fmt.Println("token_type: ", r.TokenType)
}
```

## <a name="get-a-token-using-azure-powershell"></a>Ottenere un token tramite Azure PowerShell

L'esempio seguente illustra come usare l'endpoint REST dell'identità del servizio gestito da un client PowerShell per:

1. Acquisire un token di accesso.
2. Usare il token di accesso per chiamare un'API REST di Azure Resource Manager e ottenere informazioni sulla macchina virtuale. Assicurarsi di sostituire l'ID di sottoscrizione, il nome del gruppo di risorse e il nome della macchina virtuale, rispettivamente, per `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>`, e `<VM-NAME>`.

```azurepowershell
# Get an access token for the MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01 -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Ottenere un token tramite CURL

```bash
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

## <a name="handling-token-expiration"></a>Gestione della scadenza di token

Il sottosistema dell'identità del servizio gestito locale memorizza i token nella cache. È quindi possibile eseguire tutte le chiamate desiderate e una chiamata in transito ad Azure AD restituisce un risultato solo se:
- si verifica un mancato riscontro nella cache a causa dell'assenza di token nella cache
- il token è scaduto

Se si memorizza nella cache il token nel codice, è consigliabile essere preparati a gestire gli scenari in cui la risorsa indica che il token è scaduto.

## <a name="error-handling"></a>Gestione degli errori 

L'endpoint dell'identità del servizio gestito segnala gli errori tramite il campo del codice di stato dell'intestazione dei messaggi di risposta HTTP, come errori di tipo 4xx o 5xx:

| Codice di stato | Motivo dell'errore | Come gestirlo |
| ----------- | ------------ | ------------- |
| Errore 4xx nella richiesta. | Uno o più dei parametri della richiesta non sono corretti. | Non riprovare.  Esaminare i dettagli dell'errore per maggiori informazioni.  Errori 4xx in fase di progettazione.|
| Errore temporaneo 5xx dal servizio. | Il sottosistema dell'identità del servizio gestito o Azure Active Directory ha restituito un errore temporaneo. | È consigliabile attendere almeno 1 secondo prima di riprovare.  Se si riprova troppo presto o troppo spesso, è possibile che Azure AD restituisca un errore di limite di velocità (429).|

Se si verifica un errore, il corpo della risposta HTTP corrispondente contiene dati JSON con i dettagli dell'errore:

| Elemento | DESCRIZIONE |
| ------- | ----------- |
| error   | Identificatore dell'errore. |
| error_description | Descrizione dettagliata dell'errore. **Le descrizioni degli errori possono cambiare in qualsiasi momento: non scrivere codice che crei rami in base ai valori contenuti nella descrizione dell'errore.**|

### <a name="http-response-reference"></a>Riferimenti per la risposta HTTP

Questa sezione illustra le possibili risposte di errore. Uno stato di tipo "200 OK" costituisce una risposta con esito positivo e il token di accesso è contenuto nei dati JSON del corpo della risposta, nell'elemento access_token.

| Codice di stato | Tipi di errore | Descrizione dell'errore | Soluzione |
| ----------- | ----- | ----------------- | -------- |
| 400 - Richiesta non valida | invalid_resource | AADSTS50001: l'applicazione denominata *\<URI\>* non è stata trovata nel tenant denominato *\<TENANT-ID\>*. Questa situazione può verificarsi se l'applicazione non è stata installata dall'amministratore del tenant o non è consentita da uno degli utenti nel tenant. È possibile che la richiesta di autenticazione sia stata inviata al tenant sbagliato.\ | (Solo Linux) |
| 400 - Richiesta non valida | bad_request_102 | L'intestazione dei metadati richiesta non è stata specificata. | Il campo di intestazione della richiesta `Metadata` non è presente nella richiesta oppure non è formattato correttamente. Il valore deve essere specificato come `true`, usando tutte lettere minuscole. Per un esempio, vedere Richiesta di esempio nella [sezione REST precedente](#rest).|
| 401 - Non autorizzato | unknown_source | Origine sconosciuta *\<URI\>* | Verificare che l'URI della richiesta HTTP GET sia formattato correttamente. La parte `scheme:host/resource-path` deve essere specificata come `http://localhost:50342/oauth2/token`. Per un esempio, vedere Richiesta di esempio nella [sezione REST precedente](#rest).|
|           | invalid_request | Nella richiesta manca un parametro obbligatorio oppure la richiesta include un valore di parametro non valido, contiene uno stesso parametro più volte o non è formata in modo corretto. |  |
|           | unauthorized_client | Il client non è autorizzato a richiedere un token di accesso con questo metodo. | Questo problema viene generato da una richiesta che non usa un loopback locale per chiamare l'estensione o che è presente in una macchina virtuale in cui l'identità del servizio gestito non è configurata correttamente. Vedere [Configurare un'identità del servizio gestito della macchina virtuale tramite il portale di Azure](msi-qs-configure-portal-windows-vm.md) per informazioni sulla configurazione della macchina virtuale. |
|           | access_denied | Il proprietario della risorsa o il server di autorizzazione ha rifiutato la richiesta. |  |
|           | unsupported_response_type | Il server di autorizzazione non supporta l'acquisizione di un token di accesso con questo metodo. |  |
|           | invalid_scope | L'ambito richiesto non è valido, è sconosciuto o ha un formato non valido. |  |
| 500 - Errore interno del server | unknown | Impossibile recuperare il token da Active Directory. Per informazioni dettagliate, vedere i log in *\<percorso del file\>* | Verificare che l'identità del servizio gestito sia stata abilitata nella macchina virtuale. Vedere [Configurare un'identità del servizio gestito della macchina virtuale tramite il portale di Azure](msi-qs-configure-portal-windows-vm.md) per informazioni sulla configurazione della macchina virtuale.<br><br>Verificare anche che l'URI della richiesta HTTP GET sia formattato correttamente, in particolare l'URI della risorsa specificato nella stringa di query. Vedere Richiesta di esempio nella [sezione REST precedente](#rest) per un esempio oppure vedere [Servizi di Azure che supportano l'autenticazione di Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) per un elenco di servizi con i relativi ID di risorsa.

## <a name="resource-ids-for-azure-services"></a>ID di risorsa per i servizi di Azure

Per un elenco di risorse che supportano Azure AD e che sono state testate con l'identità del servizio gestito e i relativi ID di risorsa, vedere [Servizi di Azure che supportano l'autenticazione di Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication).


## <a name="related-content"></a>Contenuti correlati

- Per abilitare l'identità del servizio gestito in una macchina virtuale di Azure, vedere [Configurare un'identità del servizio gestito della macchina virtuale tramite il portale di Azure](msi-qs-configure-portal-windows-vm.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.








