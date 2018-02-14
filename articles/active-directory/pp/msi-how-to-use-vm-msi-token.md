---
title: "Come usare un'identità del servizio gestito assegnata dall'utente per acquisire un token di accesso per una macchina virtuale."
description: "Istruzioni dettagliate ed esempi per l'uso di un'identità del servizio gestito assegnata dall'utente per una macchina virtuale di Azure per acquisire un token di accesso OAuth."
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
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a9513a59ec4540c6d63236519873c6e1e177b65a
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="acquire-an-access-token-for-a-vm-user-assigned-managed-service-identity-msi"></a>Acquisire un token di accesso per l'identità del servizio gestito assegnata dall'utente per una macchina virtuale

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]
Questo articolo fornisce vari esempi di codice e script per l'acquisizione di token, oltre a indicazioni su argomenti importanti come la gestione degli errori HTTP e di scadenza dei token.

## <a name="prerequisites"></a>prerequisiti

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

Se si prevede di usare gli esempi di Azure PowerShell presenti in questo articolo, assicurarsi di installare la versione più recente di [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM).

> [!IMPORTANT]
> - Tutti gli esempi di codice e script in questo articolo presuppongono che il client sia in esecuzione in una macchina virtuale abilitata per l'identità del servizio gestito. Usare la funzionalità di connessione alla macchina virtuale nel portale di Azure per connettersi in remoto alla macchina virtuale. Per informazioni dettagliate sull'abilitazione di un'identità del servizio gestito in una macchina virtuale, vedere [Configure a user-assigned Managed Service Identity (MSI) for a VM, using Azure CLI](msi-qs-configure-cli-windows-vm.md) (Configurare un'identità del servizio gestito assegnata dall'utente per una macchina virtuale tramite l'interfaccia della riga di comando di Azure) o una delle varianti dell'articolo (tramite il portale, PowerShell, un modello o Azure SDK). 

## <a name="overview"></a>Panoramica

Un'applicazione client può richiedere un [token di accesso solo app](~/articles/active-directory/develop/active-directory-dev-glossary.md#access-token) dell'identità del servizio gestito per accedere a una determinata risorsa. Il token è [basato sull'entità servizio dell'identità del servizio gestito](msi-overview.md#how-does-it-work). Non è quindi necessario la registrazione del client per ottenere un token di accesso nell'ambito della propria entità servizio. Il token è adatto per l'uso come token di connessione nelle [chiamate da servizio a servizio che richiedono le credenziali client](~/articles/active-directory/active-directory-protocols-oauth-service-to-service.md).

|  |  |
| -------------- | -------------------- |
| [Ottenere un token tramite HTTP](#get-a-token-using-http) | Dettagli del protocollo per l'endpoint del token dell'identità del servizio gestito |
| [Ottenere un token tramite CURL](#get-a-token-using-curl) | Esempio d'uso dell'endpoint REST dell'identità del servizio gestito da un client Bash/CURL |
| [Gestione della scadenza di token](#handling-token-expiration) | Indicazioni per la gestione di token di accesso scaduti |
| [Gestione degli errori](#error-handling) | Indicazioni per la gestione di errori HTTP restituiti dall'endpoint del token dell'identità del servizio gestito |
| [ID di risorsa per servizi di Azure](#resource-ids-for-azure-services) | Come ottenere ID di risorsa per i servizi di Azure supportati |

## <a name="get-a-token-using-http"></a>Ottenere un token tramite HTTP 

L'interfaccia di base per l'acquisizione di un token di accesso è basata su REST, in modo che sia accessibile da qualsiasi applicazione client in esecuzione sulla macchina virtuale che può effettuare chiamate HTTP REST. Si tratta quindi di un meccanismo molto simile al modello di programmazione di Azure AD, tranne per il fatto che il client usa un endpoint localhost nella macchina virtuale (e non un endpoint di Azure AD).

Richiesta di esempio:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F&client_id=712eac09-e943-418c-9be6-9fd5c91078bl HTTP/1.1
Metadata: true
```

| Elemento | DESCRIZIONE |
| ------- | ----------- |
| `GET` | Verbo HTTP, che indica che si vuole recuperare i dati dall'endpoint. In questo caso, un token di accesso OAuth. | 
| `http://localhost:50342/oauth2/token` | Endpoint dell'identità del servizio gestito, dove 50342 è la porta predefinita ed è configurabile. |
| `resource` | Parametro della stringa di query, che indica l'URI ID app della risorsa di destinazione. Viene visualizzato anche nell'attestazione `aud` (audience, destinatari) del token emesso. In questo esempio viene richiesto un token per accedere ad Azure Resource Manager, con l'URI di ID app https://management.azure.com/. |
| `client_id` | Un parametro della stringa di query, che indica l'ID client (noto anche come ID app) dell'entità servizio che rappresenta l'identità del servizio gestito assegnata dall'utente. Questo valore viene restituito nella proprietà `clientId` durante la creazione di un'identità del servizio gestito assegnata dall'utente. Questo esempio richiede un token per l'ID client "712eac09-e943-418c-9be6-9fd5c91078bl". |
| `Metadata` | Campo di intestazione della richiesta HTTP, richiesto dall'identità del servizio gestito come mitigazione contro attacchi SSRF (Server Side Request Forgery). Questo valore deve essere impostato su "true", usando tutte lettere minuscole.

Risposta di esempio:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
  "client_id":"712eac09-e943-418c-9be6-9fd5c91078bl"
}
```

| Elemento | DESCRIZIONE |
| ------- | ----------- |
| `access_token` | Token di accesso richiesto. Quando si chiama un'API REST protetta, il token è incorporato nel campo di intestazione della richiesta `Authorization` come token di connessione, in modo da consentire all'API di autenticare il chiamante. | 
| `expires_in` | Numero di secondi per cui il token di accesso continua a essere valido, prima della scadenza, dal momento del rilascio. L'ora del rilascio è indicata nell'attestazione `iat` del token. |
| `expires_on` | Intervallo di tempo in cui il token di accesso scade. La data è rappresentata come numero di secondi da "1970-01-01T0:0:0Z UTC" (corrisponde all'attestazione `exp` del token). |
| `not_before` | Intervallo di tempo in cui il token di accesso è valido e può essere accettato. La data è rappresentata come numero di secondi da "1970-01-01T0:0:0Z UTC" (corrisponde all'attestazione `nbf` del token). |
| `resource` | Risorsa per cui è stato richiesto il token di accesso, che corrisponde al parametro della stringa di query `resource` della richiesta. |
| `token_type` | Tipo di token, ovvero un token di accesso di connessione, che indica che la risorsa può concedere l'accesso al titolare del token. |
| `client_id` | ID client (noto anche come ID app) dell'entità servizio che rappresenta l'identità del servizio gestito assegnata dall'utente per cui è stato richiesto il token. |

## <a name="get-a-token-using-curl"></a>Ottenere un token tramite CURL

Assicurarsi di sostituire l'ID client (noto anche come ID app) dell'entità servizio dell'identità del servizio gestito assegnata dall'utente per il valore <MSI CLIENT ID> del parametro `client_id`. Questo valore viene restituito nella proprietà `clientId` durante la creazione di un'identità del servizio gestito assegnata dall'utente.

   ```bash
   response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/&client_id=<MSI CLIENT ID>" -H Metadata:true -s)
   access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   echo The MSI access token is $access_token
   ```

   Risposte di esempio:

   ```bash
   user@vmLinux:~$ response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/&client_id=9d484c98-b99d-420e-939c-z585174b63bl" -H Metadata:true -s)
   user@vmLinux:~$ access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
   user@vmLinux:~$ echo The MSI access token is $access_token
   The MSI access token is eyJ0eXAiOiJKV1QiLCJhbGciO...
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
| 400 - Richiesta non valida | bad_request_102 | L'intestazione dei metadati richiesta non è stata specificata. | Il campo di intestazione della richiesta `Metadata` non è presente nella richiesta oppure non è formattato correttamente. Il valore deve essere specificato come `true`, usando tutte lettere minuscole. Per un esempio, vedere la richiesta di esempio nella sezione [Ottenere un token tramite HTTP](#get-a-token-using-http).|
| 401 - Non autorizzato | unknown_source | Origine sconosciuta *\<URI\>* | Verificare che l'URI della richiesta HTTP GET sia formattato correttamente. La parte `scheme:host/resource-path` deve essere specificata come `http://localhost:50342/oauth2/token`. Per un esempio, vedere la richiesta di esempio nella sezione [Ottenere un token tramite HTTP](#get-a-token-using-http).|
|           | invalid_request | Nella richiesta manca un parametro obbligatorio oppure la richiesta include un valore di parametro non valido, contiene uno stesso parametro più volte o non è formata in modo corretto. |  |
|           | unauthorized_client | Il client non è autorizzato a richiedere un token di accesso con questo metodo. | Questo problema viene generato da una richiesta che non usa un loopback locale per chiamare l'estensione o che è presente in una macchina virtuale in cui l'identità del servizio gestito non è configurata correttamente. Vedere [Configurare un'identità del servizio gestito della macchina virtuale tramite il portale di Azure](msi-qs-configure-portal-windows-vm.md) per informazioni sulla configurazione della macchina virtuale. |
|           | access_denied | Il proprietario della risorsa o il server di autorizzazione ha rifiutato la richiesta. |  |
|           | unsupported_response_type | Il server di autorizzazione non supporta l'acquisizione di un token di accesso con questo metodo. |  |
|           | invalid_scope | L'ambito richiesto non è valido, è sconosciuto o ha un formato non valido. |  |
| 500 - Errore interno del server | unknown | Impossibile recuperare il token da Active Directory. Per informazioni dettagliate, vedere i log in *\<percorso del file\>* | Verificare che l'identità del servizio gestito sia stata abilitata nella macchina virtuale. Vedere [Configurare un'identità del servizio gestito della macchina virtuale tramite il portale di Azure](msi-qs-configure-portal-windows-vm.md) per informazioni sulla configurazione della macchina virtuale.<br><br>Verificare anche che l'URI della richiesta HTTP GET sia formattato correttamente, in particolare l'URI della risorsa specificato nella stringa di query. Per un esempio, vedere la richiesta di esempio nella sezione [Ottenere un token tramite HTTP](#get-a-token-using-http) oppure vedere [Servizi di Azure che supportano l'autenticazione di Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication) per un elenco dei servizi e dei rispettivi ID risorsa.

## <a name="resource-ids-for-azure-services"></a>ID di risorsa per i servizi di Azure

Per un elenco di risorse che supportano Azure AD e che sono state testate con l'identità del servizio gestito e i relativi ID di risorsa, vedere [Servizi di Azure che supportano l'autenticazione di Azure AD](msi-overview.md#azure-services-that-support-azure-ad-authentication).


## <a name="next-steps"></a>Passaggi successivi

- Per abilitare un'identità del servizio gestito in una macchina virtuale di Azure, vedere [Configurare un'identità del servizio gestito assegnata dall'utente per una macchina virtuale tramite l'interfaccia della riga di comando di Azure](msi-qs-configure-cli-windows-vm.md).

Usare la sezione dei commenti seguente per fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.








