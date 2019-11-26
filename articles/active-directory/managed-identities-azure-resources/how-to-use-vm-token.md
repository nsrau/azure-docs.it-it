---
title: Usare identità gestite in una macchina virtuale per acquisire il token di accesso-Azure AD
description: Istruzioni dettagliate ed esempi per l'utilizzo di identità gestite per risorse di Azure in una macchina virtuale per acquisire un token di accesso OAuth.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/01/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: d14debff8baf4bdeb808b32e64b389ad0f9e2f38
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232204"
---
# <a name="how-to-use-managed-identities-for-azure-resources-on-an-azure-vm-to-acquire-an-access-token"></a>Come usare le identità gestite per risorse di Azure in una macchina virtuale di Azure per acquisire un token di accesso 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]  

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo fornisce vari esempi di codice e script per l'acquisizione di token, oltre a indicazioni su argomenti importanti come la gestione degli errori HTTP e di scadenza dei token. 

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

Se si prevede di usare gli esempi di Azure PowerShell presenti in questo articolo, assicurarsi di installare la versione più recente di [Azure PowerShell](/powershell/azure/install-az-ps).


> [!IMPORTANT]
> - Tutto il codice e gli script di esempio in questo articolo presuppongono che il client sia in esecuzione su una macchina virtuale con identità gestite per risorse di Azure. Usare la funzionalità di connessione alla macchina virtuale nel portale di Azure per eseguire la connessione in remoto alla macchina virtuale. Per informazioni dettagliate sull'abilitazione delle identità gestite per risorse di Azure in una macchina virtuale, vedere [Configurare le identità gestite per le risorse di Azure in una macchina virtuale tramite il portale di Azure](qs-configure-portal-windows-vm.md) o una delle varianti dell'articolo riguardanti PowerShell, l'interfaccia della riga di comando, un modello o un Azure SDK. 

> [!IMPORTANT]
> - Il limite di sicurezza delle identità gestite per risorse di Azure è la risorsa in cui le identità vengono usate. Tutti i codici e gli script in esecuzione in una macchina virtuale possono richiedere e recuperare token per qualsiasi identità gestita disponibile nella macchina stessa. 

## <a name="overview"></a>Overview

Un'applicazione client può richiedere un [token di accesso solo app](../develop/developer-glossary.md#access-token) per le identità gestite per risorse di Azure per accedere a una determinata risorsa. Il token è [basato sulle identità gestite per l'entità servizio delle risorse di Azure](overview.md#how-does-it-work). Non è quindi necessario la registrazione del client per ottenere un token di accesso nell'ambito della propria entità servizio. Il token è adatto per l'uso come token di connessione nelle [chiamate da servizio a servizio che richiedono le credenziali client](../develop/v1-oauth2-client-creds-grant-flow.md).

|  |  |
| -------------- | -------------------- |
| [Ottenere un token tramite HTTP](#get-a-token-using-http) | Dettagli sul protocollo per l'endpoint del token delle identità gestite per risorse di Azure |
| [Ottenere un token usando la libreria Microsoft.Azure.Services.AppAuthentication per .NET](#get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net) | Esempio di utilizzo della libreria Microsoft.Azure.Services.AppAuthentication da un client .NET
| [Ottenere un token tramite C#](#get-a-token-using-c) | Esempio di utilizzo dell'endpoint REST delle identità gestite per risorse di Azure da un client C# |
| [Ottenere un token tramite Java](#get-a-token-using-java) | Esempio di utilizzo dell'endpoint REST delle identità gestite per risorse di Azure da un client Java |
| [Ottenere un token tramite Go](#get-a-token-using-go) | Esempio di utilizzo dell'endpoint REST delle identità gestite per risorse di Azure da un client Go |
| [Ottenere un token tramite Azure PowerShell](#get-a-token-using-azure-powershell) | Esempio di utilizzo dell'endpoint REST delle identità gestite per risorse di Azure da un client PowerShell |
| [Ottenere un token tramite CURL](#get-a-token-using-curl) | Esempio di utilizzo dell'endpoint REST delle identità gestite per risorse di Azure da un client Bash/CURL |
| Gestione della memorizzazione nella cache dei token | Indicazioni per la gestione di token di accesso scaduti |
| [Gestione degli errori](#error-handling) | Materiale sussidiario per la gestione degli errori HTTP restituiti dall'endpoint del token delle identità gestite per risorse di Azure |
| [ID di risorsa per servizi di Azure](#resource-ids-for-azure-services) | Come ottenere ID di risorsa per i servizi di Azure supportati |

## <a name="get-a-token-using-http"></a>Ottenere un token tramite HTTP 

L'interfaccia di base per l'acquisizione di un token di accesso è basata su REST, in modo che sia accessibile da qualsiasi applicazione client in esecuzione sulla macchina virtuale che può effettuare chiamate HTTP REST. Si tratta quindi di un meccanismo molto simile al modello di programmazione di Azure AD, tranne per il fatto che il client usa un endpoint nella macchina virtuale (e non un endpoint di Azure AD).

Richiesta di esempio che usa l'endpoint del servizio metadati dell'istanza (IMDS) *(consigliato)* :

```
GET 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/' HTTP/1.1 Metadata: true
```

| Elemento | DESCRIZIONE |
| ------- | ----------- |
| `GET` | Verbo HTTP, che indica che si vuole recuperare i dati dall'endpoint. In questo caso, un token di accesso OAuth. | 
| `http://169.254.169.254/metadata/identity/oauth2/token` | L'endpoint delle identità gestite per risorse di Azure per il servizio metadati. |
| `api-version`  | Un parametro di stringa della query, che indica la versione dell'API per l'endpoint del servizio metadati dell'istanza. Usare la versione dell'API `2018-02-01` o successiva. |
| `resource` | Parametro della stringa di query, che indica l'URI ID app della risorsa di destinazione. Viene visualizzato anche nell'attestazione `aud` (audience, destinatari) del token emesso. In questo esempio viene richiesto un token per accedere ad Azure Resource Manager, con l'URI di ID app https://management.azure.com/. |
| `Metadata` | Un campo di intestazione della richiesta HTTP, richiesto dalle identità gestite per risorse di Azure come mitigazione degli attacchi SSRF (Server Side Request Forgery). Questo valore deve essere impostato su "true", usando tutte lettere minuscole. |
| `object_id` | (Facoltativo) Un parametro di stringa di query che indichi il valore object_id dell'identità gestita per cui si desidera il token. Obbligatorio, se la macchina virtuale dispone di più identità gestite assegnate dall'utente.|
| `client_id` | (Facoltativo) Un parametro di stringa di query che indichi il valore client_id dell'identità gestita per cui si desidera il token. Obbligatorio, se la macchina virtuale dispone di più identità gestite assegnate dall'utente.|
| `mi_res_id` | Opzionale Parametro della stringa di query, che indica il mi_res_id (ID risorsa di Azure) dell'identità gestita per cui si desidera il token. Obbligatorio, se la macchina virtuale dispone di più identità gestite assegnate dall'utente. |

Richiesta di esempio che usa l'endpoint dell'estensione della macchina virtuale delle identità gestite per risorse di Azure *(la cui deprecazione è pianificata per il gennaio 2019)* :

```http
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Elemento | DESCRIZIONE |
| ------- | ----------- |
| `GET` | Verbo HTTP, che indica che si vuole recuperare i dati dall'endpoint. In questo caso, un token di accesso OAuth. | 
| `http://localhost:50342/oauth2/token` | L'endpoint delle identità gestite per risorse di Azure, dove 50342 è la porta predefinita ed è configurabile. |
| `resource` | Parametro della stringa di query, che indica l'URI ID app della risorsa di destinazione. Viene visualizzato anche nell'attestazione `aud` (audience, destinatari) del token emesso. In questo esempio viene richiesto un token per accedere ad Azure Resource Manager, con l'URI di ID app https://management.azure.com/. |
| `Metadata` | Un campo di intestazione della richiesta HTTP, richiesto dalle identità gestite per risorse di Azure come mitigazione degli attacchi SSRF (Server Side Request Forgery). Questo valore deve essere impostato su "true", usando tutte lettere minuscole.|
| `object_id` | (Facoltativo) Un parametro di stringa di query che indichi il valore object_id dell'identità gestita per cui si desidera il token. Obbligatorio, se la macchina virtuale dispone di più identità gestite assegnate dall'utente.|
| `client_id` | (Facoltativo) Un parametro di stringa di query che indichi il valore client_id dell'identità gestita per cui si desidera il token. Obbligatorio, se la macchina virtuale dispone di più identità gestite assegnate dall'utente.|

Risposta di esempio:

```json
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
| `refresh_token` | Non usata dalle identità gestite per risorse di Azure. |
| `expires_in` | Numero di secondi per cui il token di accesso continua a essere valido, prima della scadenza, dal momento del rilascio. L'ora del rilascio è indicata nell'attestazione `iat` del token. |
| `expires_on` | Intervallo di tempo in cui il token di accesso scade. La data è rappresentata come numero di secondi da "1970-01-01T0:0:0Z UTC" (corrisponde all'attestazione `exp` del token). |
| `not_before` | Intervallo di tempo in cui il token di accesso è valido e può essere accettato. La data è rappresentata come numero di secondi da "1970-01-01T0:0:0Z UTC" (corrisponde all'attestazione `nbf` del token). |
| `resource` | Risorsa per cui è stato richiesto il token di accesso, che corrisponde al parametro della stringa di query `resource` della richiesta. |
| `token_type` | Tipo di token, ovvero un token di accesso di connessione, che indica che la risorsa può concedere l'accesso al titolare del token. |

## <a name="get-a-token-using-the-microsoftazureservicesappauthentication-library-for-net"></a>Ottenere un token usando la libreria Microsoft.Azure.Services.AppAuthentication per .NET

Per le funzioni e le applicazioni .NET, il modo più semplice per usare le identità gestite per risorse di Azure è tramite il pacchetto Microsoft.Azure.Services.AppAuthentication. Questa raccolta consente anche di testare il codice in locale nel computer di sviluppo usando l'account utente da Visual Studio, dall'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) o tramite l'autenticazione integrata di Active Directory. Per altre informazioni sulle opzioni di sviluppo locale con questa libreria, vedere la [documentazione di riferimento della libreria Microsoft.Azure.Services.AppAuthentication](/azure/key-vault/service-to-service-authentication). In questa sezione viene illustrato come muovere i primi passi con la libreria nel codice.

1. Aggiungere un riferimento ai pacchetti NuGet [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) e [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) all'applicazione.

2.  Aggiungere il codice seguente all'applicazione:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```
    
Per altre informazioni su Microsoft.Azure.Services.AppAuthentication e sulle relative operazioni esposte, vedere la [documentazione di riferimento su Microsoft.Azure.Services.AppAuthentication](/azure/key-vault/service-to-service-authentication) e l'[esempio .NET del Servizio app di Azure e dell'insieme di credenziali delle chiavi con le identità gestite per risorse di Azure](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

## <a name="get-a-token-using-c"></a>Ottenere un token tramite C#

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 

// Build request to acquire managed identities for Azure resources token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
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

## <a name="get-a-token-using-java"></a>Ottenere un token tramite Java

Usare questa [libreria JSON](https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-core/2.9.4) per recuperare un token usando Java.

```Java
import java.io.*;
import java.net.*;
import com.fasterxml.jackson.core.*;
 
class GetMSIToken {
    public static void main(String[] args) throws Exception {
 
        URL msiEndpoint = new URL("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https://management.azure.com/");
        HttpURLConnection con = (HttpURLConnection) msiEndpoint.openConnection();
        con.setRequestMethod("GET");
        con.setRequestProperty("Metadata", "true");
 
        if (con.getResponseCode()!=200) {
            throw new Exception("Error calling managed identity token endpoint.");
        }
 
        InputStream responseStream = con.getInputStream();
 
        JsonFactory factory = new JsonFactory();
        JsonParser parser = factory.createParser(responseStream);
 
        while(!parser.isClosed()){
            JsonToken jsonToken = parser.nextToken();
 
            if(JsonToken.FIELD_NAME.equals(jsonToken)){
                String fieldName = parser.getCurrentName();
                jsonToken = parser.nextToken();
 
                if("access_token".equals(fieldName)){
                    String accesstoken = parser.getValueAsString();
                    System.out.println("Access Token: " + accesstoken.substring(0,5)+ "..." + accesstoken.substring(accesstoken.length()-5));
                    return;
                }
            }
        }
    }
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
    
    // Create HTTP request for a managed services for Azure resources token to access Azure Resource Manager
    var msi_endpoint *url.URL
    msi_endpoint, err := url.Parse("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01")
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

    // Call managed services for Azure resources token endpoint
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

L'esempio seguente illustra come usare l'endpoint REST delle identità gestite per risorse di Azure da un client PowerShell per:

1. Acquisire un token di accesso.
2. Usare il token di accesso per chiamare un'API REST di Azure Resource Manager e ottenere informazioni sulla macchina virtuale. Assicurarsi di sostituire l'ID di sottoscrizione, il nome del gruppo di risorse e il nome della macchina virtuale, rispettivamente, per `<SUBSCRIPTION-ID>`, `<RESOURCE-GROUP>`, e `<VM-NAME>`.

```azurepowershell
Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Headers @{Metadata="true"}
```

Esempio di come analizzare il token di accesso dalla risposta:
```azurepowershell
# Get an access token for managed identities for Azure resources
$response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' `
                              -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The managed identities for Azure resources access token is $access_token"

# Use the access token to get resource information for the VM
$vmInfoRest = (Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.Compute/virtualMachines/<VM-NAME>?api-version=2017-12-01' -Method GET -ContentType "application/json" -Headers @{ Authorization ="Bearer $access_token"}).content
echo "JSON returned from call to get VM info:"
echo $vmInfoRest

```

## <a name="get-a-token-using-curl"></a>Ottenere un token tramite CURL

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s
```


Esempio di come analizzare il token di accesso dalla risposta:

```bash
response=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The managed identities for Azure resources access token is $access_token
```

## <a name="token-caching"></a>Memorizzazione nella cache dei token

Mentre il sottosistema delle identità gestite per risorse di Azure (servizio metadati dell'istanza/estensione della macchina virtuale per le identità gestite per risorse di Azure) memorizza i token nella cache, è consigliabile implementare la memorizzazione nella cache del token anche nel codice. Di conseguenza è necessario prepararsi per gli scenari in cui la risorsa indica che il token è scaduto. 

Le chiamate in transito ad Azure AD vengono generate solo quando:
- si verifica il mancato riscontro nella cache a causa dell'assenza di token nella cache del sottosistema delle identità gestite per risorse di Azure
- il token memorizzato nella cache è scaduto

## <a name="error-handling"></a>Gestione degli errori

L'endpoint delle identità gestite per risorse di Azure segnala gli errori tramite il campo del codice di stato dell'intestazione dei messaggi di risposta HTTP, come errori di tipo 4xx o 5xx:

| Codice di stato | Motivo dell'errore | Come gestirlo |
| ----------- | ------------ | ------------- |
| 404 - Non trovato. | L'endpoint IMDS è in fase di aggiornamento. | Riprovare con il backoff esponenziale. Seguire le indicazioni riportate di seguito. |
| 429 - Numero eccessivo di richieste. |  È stato raggiunto il limite del servizio metadati dell'istanza. | Riprovare con il backoff esponenziale. Seguire le indicazioni riportate di seguito. |
| Errore 4xx nella richiesta. | Uno o più dei parametri della richiesta non sono corretti. | Non riprovare.  Esaminare i dettagli dell'errore per maggiori informazioni.  Errori 4xx in fase di progettazione.|
| Errore temporaneo 5xx dal servizio. | Il sottosistema delle identità gestite per risorse di Azure o Azure Active Directory ha restituito un errore temporaneo. | È consigliabile attendere almeno 1 secondo prima di riprovare.  Se si riprova troppo presto o troppo spesso, è possibile che il servizio metadati dell'istanza e/o Azure AD restituiscano un errore di limite di velocità (429).|
| timeout | L'endpoint IMDS è in fase di aggiornamento. | Riprovare con il backoff esponenziale. Seguire le indicazioni riportate di seguito. |

Se si verifica un errore, il corpo della risposta HTTP corrispondente contiene dati JSON con i dettagli dell'errore:

| Elemento | DESCRIZIONE |
| ------- | ----------- |
| error   | Identificatore dell'errore. |
| error_description | Descrizione dettagliata dell'errore. **Le descrizioni degli errori possono cambiare in qualsiasi momento. Non scrivere codice che si dirama in base ai valori nella descrizione dell'errore.**|

### <a name="http-response-reference"></a>Riferimenti per la risposta HTTP

Questa sezione illustra le possibili risposte di errore. Uno stato di tipo "200 OK" costituisce una risposta con esito positivo e il token di accesso è contenuto nei dati JSON del corpo della risposta, nell'elemento access_token.

| Codice di stato | Tipi di errore | Descrizione dell'errore | Soluzione |
| ----------- | ----- | ----------------- | -------- |
| 400 - Richiesta non valida | invalid_resource | AADSTS50001: l'applicazione denominata *\<URI\>* non è stata trovata nel tenant denominato *\<TENANT-ID\>* . Questa situazione può verificarsi se l'applicazione non è stata installata dall'amministratore del tenant o non è consentita da uno degli utenti nel tenant. È possibile che la richiesta di autenticazione sia stata inviata al tenant sbagliato.\ | (Solo Linux) |
| 400 - Richiesta non valida | bad_request_102 | L'intestazione dei metadati richiesta non è stata specificata. | Il campo di intestazione della richiesta `Metadata` non è presente nella richiesta oppure non è formattato correttamente. Il valore deve essere specificato come `true`, usando tutte lettere minuscole. Per un esempio, vedere "Richiesta di esempio" nella sezione REST precedente.|
| 401 - Non autorizzato | unknown_source | Origine sconosciuta *\<URI\>* | Verificare che l'URI della richiesta HTTP GET sia formattato correttamente. La parte `scheme:host/resource-path` deve essere specificata come `http://localhost:50342/oauth2/token`. Per un esempio, vedere "Richiesta di esempio" nella sezione REST precedente.|
|           | invalid_request | Nella richiesta manca un parametro obbligatorio oppure la richiesta include un valore di parametro non valido, contiene uno stesso parametro più volte o non è formata in modo corretto. |  |
|           | unauthorized_client | Il client non è autorizzato a richiedere un token di accesso con questo metodo. | La causa di questo problema è una richiesta che non ha usato il loopback locale per chiamare l'estensione o una macchina virtuale in cui le identità gestite per risorse di Azure non sono configurate correttamente. Per informazioni sulla configurazione della macchina virtuale, vedere [Configurare le identità gestite per le risorse di Azure in una macchina virtuale tramite il portale di Azure](qs-configure-portal-windows-vm.md). |
|           | access_denied | Il proprietario della risorsa o il server di autorizzazione ha rifiutato la richiesta. |  |
|           | unsupported_response_type | Il server di autorizzazione non supporta l'acquisizione di un token di accesso con questo metodo. |  |
|           | invalid_scope | L'ambito richiesto non è valido, è sconosciuto o ha un formato non valido. |  |
| 500 - Errore interno del server | unknown | Impossibile recuperare il token da Active Directory. Per informazioni dettagliate, vedere i log in *\<percorso del file\>* | Verificare che le identità gestite per le risorse di Azure è stata abilitata nella macchina virtuale. Per informazioni sulla configurazione della macchina virtuale, vedere [Configurare le identità gestite per le risorse di Azure in una macchina virtuale tramite il portale di Azure](qs-configure-portal-windows-vm.md).<br><br>Verificare anche che l'URI della richiesta HTTP GET sia formattato correttamente, in particolare l'URI della risorsa specificato nella stringa di query. Vedere "Richiesta di esempio" nella sezione REST precedente per un esempio oppure vedere [Servizi di Azure che supportano l'autenticazione di Azure AD](services-support-msi.md) per un elenco di servizi con i relativi ID di risorsa.

## <a name="retry-guidance"></a>Materiale sussidiario sulla ripetizione di tentativi 

Si consiglia di riprovare in caso si riceva un codice di errore 404, 429 o 5xx (consultare [Gestione errori](#error-handling) in alto).

I limiti delle richieste si applicano al numero di chiamate effettuate all'endpoint IMDS. Al superamento della soglia di limitazione delle richieste, l'endpoint IMDS limiterà eventuali altre richieste mentre la limitazione è attiva. Durante questo intervallo l'endpoint IMDS restituirà il codice di stato HTTP 429 ("Numero eccessivo di richieste") e si verificherà un errore delle richieste. 

Per eseguire nuovi tentativi è consigliabile seguire la strategia seguente: 

| **Strategia di ripetizione dei tentativi** | **Impostazioni** | **Valori** | **How it works** (Funzionamento) |
| --- | --- | --- | --- |
|ExponentialBackoff |Numero tentativi<br />Interruzione temporanea minima<br />Interruzione temporanea massima<br />Interruzione temporanea delta<br />Primo tentativo rapido |5<br />0 secondi<br />60 secondi<br />2 secondi<br />false |Tentativo di 1 - intervallo di 0 sec<br />Tentativo 2 - intervallo di ~2 sec<br />Tentativo 3 - intervallo di ~6 sec<br />Tentativo 4 - intervallo di ~14 sec<br />Tentativo 5 - intervallo di 30 sec |

## <a name="resource-ids-for-azure-services"></a>ID di risorsa per i servizi di Azure

Per un elenco di risorse che supportano Azure AD e che sono state testate con le identità gestite per risorse di Azure e i relativi ID risorsa, vedere [Servizi di Azure che supportano l'autenticazione di Azure AD](services-support-msi.md).


## <a name="next-steps"></a>Passaggi successivi

- Per abilitare le identità gestite per le risorse di Azure in una macchina virtuale di Azure, vedere [Configurare le identità gestite per le risorse di Azure in una macchina virtuale tramite il portale di Azure](qs-configure-portal-windows-vm.md).








