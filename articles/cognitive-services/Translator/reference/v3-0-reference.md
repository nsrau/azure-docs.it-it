---
title: Riferimenti per l'API Traduzione testuale v3.0
titleSuffix: Azure Cognitive Services
description: Documentazione di riferimento per l'API Traduzione testuale v3.0. La versione 3 dell'API Traduzione testuale fornisce un'API Web moderna basata su JSON.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 4/2/2020
ms.author: swmachan
ms.openlocfilehash: fcbaabac0961f1269a929fb4a56f81ac282bae29
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619156"
---
# <a name="translator-text-api-v30"></a>API Traduzione testuale v3.0

## <a name="whats-new"></a>Novità

La versione 3 dell'API Traduzione testuale fornisce un'API Web moderna basata su JSON. Migliora l'usabilità e le prestazioni consolidando le funzionalità esistenti in un minor numero di operazioni e offre nuove funzionalità.

 * Traslitterazione per convertire il testo in una lingua da un carattere all'altro.
 * Traduzione in più lingue in una sola richiesta.
 * Rilevamento della lingua, traduzione e traslitterazione in una sola richiesta.
 * Dizionario per cercare traduzioni alternative di un termine, per trovare traduzioni inequivocrate ed esempi che mostrano i termini utilizzati nel contesto.
 * Altri risultati informativi sul rilevamento della lingua.

## <a name="base-urls"></a>URL di base

Microsoft Translator è accessibile da più posizioni di data center. Attualmente si trovano in 10 [aree geografiche](https://azure.microsoft.com/global-infrastructure/regions)di Azure:Currently they are located in 10 Azure geographies :

* **Americhe:** Stati Uniti orientali, Stati Uniti centro-meridionali, Stati Uniti centro-occidentali e Stati Uniti occidentali 2 
* **Asia Pacifico:** Corea del Sud, Giappone Est, Sud-Est asiatico e Australia Est
* **Europa:** Europa settentrionale ed occidentale

Nella maggior parte dei casi le richieste per l'API Traduzione testuale Microsoft vengono gestite dal data center più vicino all'area di origine della richiesta. In caso di errore del data center, la richiesta può essere instradata all'esterno dell'area geografica di Azure.In case of a datacenter failure, the request may be routed outside of the Azure geography.

Per forzare la gestione della richiesta da parte di un'area geografica di Azure specifica, modificare l'endpoint globale nella richiesta API impostandolo sull'endpoint regionale desiderato:To force the request to be handled by a specific Azure geography, change the Global endpoint in the API request to the desired regional endpoint:

|Descrizione|Area geografica di AzureAzure geography|URL di base|
|:--|:--|:--|
|Azure|Globale (non regionale)|   api.cognitive.microsofttranslator.com|
|Azure|Stati Uniti|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Asia Pacifico|    api-apc.cognitive.microsofttranslator.com|

## <a name="authentication"></a>Authentication

Sottoscrivere il multiservizio Dell'API Translator Text o [Dei servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/) nei servizi cognitivi di Azure e usare la chiave di sottoscrizione (disponibile nel portale di Azure) per l'autenticazione. 

Sono tre le intestazioni che è possibile usare per autenticare la sottoscrizione. Questa tabella descrive come viene utilizzato ciascuno di essi:This table describes how each is used:

|Headers|Descrizione|
|:----|:----|
|Ocp-Apim-Subscription-Key|*Usare con la sottoscrizione di Servizi cognitivi se si passa la chiave privata*.<br/>Il valore è la chiave privata di Azure per la sottoscrizione dell'API Traduzione testuale.|
|Autorizzazione|*Usare con la sottoscrizione di Servizi cognitivi se si passa un token di autenticazione*.<br/>Il valore è il token di connessione: `Bearer <token>`.|
|Ocp-Apim-Subscription-Region|*Utilizzare con le risorse di traduttore multiservizio e di traduttore regionale di Servizi cognitivi.*<br/>Il valore è l'area della risorsa traduttore multiservizio o regionale. Questo valore è facoltativo quando si usa una risorsa di conversione globale.|

###  <a name="secret-key"></a>Chiave privata
La prima opzione consiste nell'eseguire l'autenticazione usando l'intestazione `Ocp-Apim-Subscription-Key`. Aggiungere `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` l'intestazione alla richiesta.

#### <a name="authenticating-with-a-global-resource"></a>Autenticazione con una risorsa globaleAuthenticating with a global resource

Quando si utilizza una [risorsa traduttore globale](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation), è necessario includere un'intestazione per chiamare l'API del traduttore.

|Headers|Descrizione|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Il valore è la chiave privata di Azure per la sottoscrizione dell'API Traduzione testuale.|

Ecco una richiesta di esempio per chiamare l'API Translator usando la risorsa traduttore globaleHere's an example request to call the Translator API using the global translator resource

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>Autenticazione con una risorsa regionale

Quando si utilizza una [risorsa traduttore regionale](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation).
Ci sono 2 intestazioni che è necessario chiamare l'API traduttore.

|Headers|Descrizione|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Il valore è la chiave privata di Azure per la sottoscrizione dell'API Traduzione testuale.|
|Ocp-Apim-Subscription-Region| Il valore è l'area della risorsa traduttore. |

Ecco una richiesta di esempio per chiamare l'API Translator usando la risorsa traduttore regionaleHere's an example request to call the Translator API using the regional translator resource

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>Autenticazione con una risorsa multiservizioAuthenticating with a Multi-service resource

Quando si usa la risorsa multiservizio di un servizio cognitivo. In questo modo è possibile usare una singola chiave privata per autenticare le richieste per più servizi. 

Quando si utilizza una chiave segreta per più servizi, è necessario includere due intestazioni di autenticazione con la richiesta. Ci sono 2 intestazioni che è necessario chiamare l'API traduttore.

|Headers|Descrizione|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Il valore è la chiave segreta di Azure per la risorsa multiservizio.|
|Ocp-Apim-Subscription-Region| Il valore è l'area della risorsa multiservizio. |

L'area è necessaria per la sottoscrizione all'API Text multiservizio. L'area selezionata è l'unica area che è possibile usare per la traduzione di testo quando si usa la chiave di sottoscrizione multiservizio e deve essere la stessa area selezionata al momento dell'iscrizione alla sottoscrizione multiservizio tramite il portale di Azure.The region you select is the only region that you can use for text translation when using the multi-service subscription key, and must be the same region you selected when you signed up for your multi-service subscription through the Azure portal.

Le aree `canadacentral`disponibili `centralindia` `centralus`sono `centraluseuap` `eastasia` `eastus` `eastus2` `francecentral` `japaneast` `japanwest` `koreacentral` `northcentralus` `northeurope` `southcentralus` `southeastasia` `uksouth` `westcentralus` `westeurope` `westus` `westus2` `southafricanorth`, `brazilsouth`, , , , , , , , , , , , , , , , , , , , , , , , , , e . `australiaeast`

Se si passa la chiave privata nella stringa di query con il parametro `Subscription-Key`, è necessario specificare l'area con il parametro di query `Subscription-Region`.

### <a name="authenticating-with-an-access-token"></a>Autenticazione con un token di accessoAuthenticating with an access token
In alternativa, è possibile scambiare la chiave privata con un token di accesso. Questo token viene incluso in ogni richiesta come intestazione `Authorization`. Per ottenere un token di autorizzazione, effettuare una richiesta `POST` all'URL seguente:

| Tipo di risorsa     | URL servizio di autenticazione                                |
|-----------------|-----------------------------------------------------------|
| Global          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| Regionale o Multi-Service | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Di seguito sono riportati esempi di richieste per ottenere un token con una chiave privata:

```curl
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Una richiesta con esito positivo restituisce il token di accesso codificato come testo normale nel corpo della risposta. Il token valido viene passato al servizio Translator come token di connessione nell'autorizzazione.

```http
Authorization: Bearer <Base64-access_token>
```

Un token di autenticazione è valido per 10 minuti. Il token deve essere riutilizzato quando si effettuano più chiamate alle API Translator. Tuttavia, se il programma effettua richieste all'API Translator per un periodo di tempo prolungato, il programma deve richiedere un nuovo token di accesso a intervalli regolari (ad esempio, ogni 8 minuti).

## <a name="virtual-network-support"></a>Supporto della rete virtuale

Il servizio Translator è ora disponibile con`WestUS2` `EastUS`le `SouthCentralUS` `WestUS`funzionalità `Central US EUAP` `global`di rete virtuale in aree limitate ( , , , , , ). Per abilitare la rete virtuale, vedere [Configurazione delle reti virtuali di Servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal). 

Dopo aver attivato questa funzionalità, è necessario usare l'endpoint personalizzato per chiamare l'API Translator.Once you turn on this capability, you must use the custom endpoint to call the Translator API. Non è possibile usare l'endpoint del traduttore globale ("api.cognitive.microsofttranslator.com") e non è possibile eseguire l'autenticazione con un token di accesso.

È possibile trovare l'endpoint personalizzato dopo aver creato la [risorsa traduttore.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)

|Headers|Descrizione|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Il valore è la chiave privata di Azure per la sottoscrizione dell'API Traduzione testuale.|
|Ocp-Apim-Subscription-Region| Il valore è l'area della risorsa traduttore. Questo valore è facoltativo se la risorsa è`global`|

Ecco una richiesta di esempio per chiamare l'API Translator usando l'endpoint personalizzatoHere's an example request to call the Translator API using the custom endpoint

```curl
// Pass secret key and region using headers
curl -X POST "https://<your-custom-domain>.cognitiveservices.azure.com/translator/text/v3.0/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

## <a name="errors"></a>Errors

Una risposta di errore standard è un oggetto JSON con coppia nome/valore denominato `error`. Il valore è anche un oggetto JSON con proprietà:

  * `code`: codice di errore definito dal server.
  * `message`: stringa che fornisce una rappresentazione dell'errore leggibile dall'utente.

Ad esempio, un cliente con una sottoscrizione della versione di valutazione gratuita riceverebbe l'errore seguente al superamento della quota gratuita:

```json
{
  "error": {
    "code":403001,
    "message":"The operation is not allowed because the subscription has exceeded its free quota."
    }
}
```
Il codice errore è un numero a 6 cifre che combina il codice di stato HTTP a 3 cifre seguito da un numero a 3 cifre per classificare ulteriormente l'errore. Codici errore comuni sono:

| Codice | Descrizione |
|:----|:-----|
| 400000| Uno degli input della richiesta non è valido.|
| 400001| Il parametro "scope" non è valido.|
| 400002| Il parametro "category" non è valido.|
| 400003| Un identificatore di lingua manca o non è valido.|
| 400004| Un identificatore di script di destinazione ("To script") manca o non è valido.|
| 400005| Un testo di input manca o non è valido.|
| 400006| La combinazione di lingua e script non è valida.|
| 400018| Un identificatore di script di origine ("From script") manca o non è valido.|
| 400019| Una delle lingue specificate non è supportata.|
| 400020| Uno degli elementi nella matrice del testo di input non è valido.|
| 400021| Il parametro della versione API manca o non è valido.|
| 400023| Una delle coppie di lingue specificata non è valida.|
| 400035| La lingua di origine (campo "From") non è valida.|
| 400036| La lingua di destinazione (campo "To") manca o non è valida.|
| 400042| Una delle opzioni specificate (campo "Options") non è valida.|
| 400043| L'ID traccia client (campo ClientTraceId o intestazione X-ClientTraceId) manca o non è valido.|
| 400050| Il testo di input è troppo lungo. Visualizzare [i limiti delle richieste](../request-limits.md).|
| 400064| Il parametro "translation" manca o non è valido.|
| 400070| Il numero di script di destinazione (parametro ToScript) non corrisponde al numero di lingue di destinazione (parametro To).|
| 400071| Il valore non è valido per TextType.|
| 400072| La matrice del testo di input contiene troppi elementi.|
| 400073| Il parametro script non è valido.|
| 400074| Il corpo della richiesta non è in formato JSON valido.|
| 400075| La combinazione di coppia di lingue e categoria non è valida.|
| 400077| Le dimensioni massime della richiesta sono state superate. Visualizzare [i limiti delle richieste](../request-limits.md).|
| 400079| Il sistema personalizzato richiesto per la traduzione da/verso la lingua non esiste.|
| 400080| La traslitterazione non è supportata per la lingua o lo script.|
| 401000| La richiesta non è autorizzata perché le credenziali mancano o non sono valide.|
| 401015| "Le credenziali specificate si riferiscono a Speech API. Per questa richiesta sono necessarie le credenziali per l'API Testo. Usare una sottoscrizione all'API Translator Text."|
| 403000| L'operazione non è consentita.|
| 403001| L'operazione non è consentita perché la sottoscrizione ha superato la quota gratuita.|
| 405000| Il metodo della richiesta non è supportato per la risorsa richiesta.|
| 408001| Il sistema di traduzione richiesto è in fase di preparazione. Riprovare tra qualche minuto.|
| 408002| Timeout della richiesta in attesa del flusso in ingresso. Il client non ha prodotto una richiesta entro il tempo in cui il server era pronto ad attendere. Il client può ripetere la richiesta senza modifiche in un secondo momento.|
| 415000| L'intestazione Content-Type manca o non è valida.|
| 429000, 429001, 429002| Il server ha rifiutato la richiesta perché il client ha superato i limiti di richiesta.|
| 500000| Si è verificato un errore imprevisto. Se l'errore persiste, segnalarlo specificando data e ora dell'errore, identificatore della richiesta indicato in X-RequestId nell'intestazione della risposta e identificatore del client indicato in X-ClientTraceId nell'intestazione della richiesta.|
| 503000| Il servizio è temporaneamente non disponibile. Riprovare. Se l'errore persiste, segnalarlo specificando data e ora dell'errore, identificatore della richiesta indicato in X-RequestId nell'intestazione della risposta e identificatore del client indicato in X-ClientTraceId nell'intestazione della richiesta.|

## <a name="metrics"></a>Metriche 
Le metriche consentono di visualizzare le informazioni sull'utilizzo e la disponibilità del traduttore nel portale di Azure, nella sezione Metriche, come illustrato nella schermata seguente. Per ulteriori informazioni, consultate [Metriche di dati e piattaforme.](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics)

![Metriche del traduttore](../media/translatormetrics.png)

Questa tabella elenca le metriche disponibili con la descrizione di come vengono utilizzate per monitorare le chiamate API di conversione.

| Metriche | Descrizione |
|:----|:-----|
| TotalCalls| Numero totale di chiamate API.|
| TotalTokenCalls| Numero totale di chiamate API tramite il servizio token tramite token di autenticazione.|
| SuccessfulCalls| Numero di chiamate riuscite.|
| TotalErrors| Numero di chiamate con risposta di errore.|
| BlockedCalls| Numero di chiamate che hanno superato il limite di frequenza o di quota.|
| ServerErrors| Numero di chiamate con errore interno del server (5XX).|
| ClientErrors| Numero di chiamate con errore lato client(4XX).|
| Latenza| Durata della richiesta in millisecondi.|
| CharactersTranslated| Numero totale di caratteri nella richiesta di testo in ingresso.|
