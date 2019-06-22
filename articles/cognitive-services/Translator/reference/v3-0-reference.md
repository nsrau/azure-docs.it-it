---
title: Riferimenti per l'API Traduzione testuale v3.0
titlesuffix: Azure Cognitive Services
description: Documentazione di riferimento per l'API Traduzione testuale v3.0.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-pawal
ms.openlocfilehash: 4133cec401723ab103b0b3bfff62eee21b653e2b
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/22/2019
ms.locfileid: "67331814"
---
# <a name="translator-text-api-v30"></a>API Traduzione testuale v3.0

## <a name="whats-new"></a>Novità

La versione 3 dell'API Traduzione testuale fornisce un'API Web moderna basata su JSON. Migliora l'usabilità e le prestazioni consolidando le funzionalità esistenti in un minor numero di operazioni e offre nuove funzionalità.

 * Traslitterazione per convertire il testo in una lingua da un carattere all'altro.
 * Traduzione in più lingue in una sola richiesta.
 * Rilevamento della lingua, traduzione e traslitterazione in una sola richiesta.
 * Dizionario per la ricerca di traduzioni alternative di un termine, traduzioni inverse ed esempi dell'uso dei termini nel contesto.
 * Altri risultati informativi sul rilevamento della lingua.

## <a name="base-urls"></a>URL di base

Microsoft Translator è accessibile da più posizioni di data center. Attualmente si trovano in 6 [aree geografiche di Azure](https://azure.microsoft.com/global-infrastructure/regions):

* **Americhe:** Stati Uniti occidentali 2 e Stati Uniti centro-occidentali 
* **Asia Pacifico:** Asia sud-orientale e Corea meridionale
* **Europa:** Europa settentrionale e Europa occidentale

Nella maggior parte dei casi le richieste per l'API Traduzione testuale Microsoft vengono gestite dal data center più vicino all'area di origine della richiesta. In caso di errore di Data Center, la richiesta può essere instradata di fuori dell'area geografica di Azure.

Per forzare la richiesta di essere gestita da una specifica area geografica di Azure, modificare l'endpoint globale nella richiesta di API per l'endpoint a livello di area desiderato:

|Descrizione|Area geografica di Azure|URL di base|
|:--|:--|:--|
|Azure|Global (non a livello di area)|   api.cognitive.microsofttranslator.com|
|Azure|Stati Uniti|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Asia/Pacifico|    api-apc.cognitive.microsofttranslator.com|


## <a name="authentication"></a>Authentication

La sottoscrizione all'API traduzione testuale oppure [multi-servizio servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/) in servizi cognitivi Microsoft e utilizzare la sottoscrizione della chiave (disponibile nel portale di Azure) per l'autenticazione. 

Sono tre le intestazioni che è possibile usare per autenticare la sottoscrizione. Questa tabella spiega come usare ogni intestazione:

|Headers|Descrizione|
|:----|:----|
|Ocp-Apim-Subscription-Key|*Usare con la sottoscrizione di Servizi cognitivi se si passa la chiave privata*.<br/>Il valore è la chiave privata di Azure per la sottoscrizione dell'API Traduzione testuale.|
|Authorization|*Usare con la sottoscrizione di Servizi cognitivi se si passa un token di autenticazione*.<br/>Il valore è il token di connessione: `Bearer <token>`.|
|Ocp-Apim-Subscription-Region|*Usare con servizi cognitivi multiservizio sottoscrizione se si passa una chiave privata multiservizio.*<br/>Il valore è l'area della sottoscrizione multiservizio. Questo valore è facoltativo se non si usa una sottoscrizione di multi-service.|

###  <a name="secret-key"></a>Chiave privata
La prima opzione consiste nell'eseguire l'autenticazione usando l'intestazione `Ocp-Apim-Subscription-Key`. Aggiungere semplicemente l'intestazione `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` alla richiesta.

### <a name="authorization-token"></a>Token di autorizzazione
In alternativa, è possibile scambiare la chiave privata con un token di accesso. Questo token viene incluso in ogni richiesta come intestazione `Authorization`. Per ottenere un token di autorizzazione, effettuare una richiesta `POST` all'URL seguente:

| Environment     | URL servizio di autenticazione                                |
|-----------------|-----------------------------------------------------------|
| Azure           | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |

Di seguito sono riportati esempi di richieste per ottenere un token con una chiave privata:

```
// Pass secret key using header
curl --header 'Ocp-Apim-Subscription-Key: <your-key>' --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken'

// Pass secret key using query string parameter
curl --data "" 'https://api.cognitive.microsoft.com/sts/v1.0/issueToken?Subscription-Key=<your-key>'
```

Una richiesta con esito positivo restituisce il token di accesso codificato come testo normale nel corpo della risposta. Il token valido viene passato al servizio Translator come token di connessione nell'autorizzazione.

```
Authorization: Bearer <Base64-access_token>
```

Un token di autenticazione è valido per 10 minuti. Il token deve essere riutilizzato quando si effettuano più chiamate alle API del servizio Translator. Tuttavia, se il programma effettua richieste all'API del servizio Translator per un periodo di tempo prolungato, il programma deve richiedere un nuovo token di accesso a intervalli regolari (ad esempio, ogni 8 minuti).

### <a name="multi-service-subscription"></a>Sottoscrizione multiservizio

L'ultima opzione authentication è usare multiservizio sottoscrizione di un servizio cognitivi. In questo modo è possibile usare una singola chiave privata per autenticare le richieste per più servizi. 

Quando si usa una chiave privata multiservizio, è necessario includere due intestazioni di autenticazione con la richiesta. La prima passa la chiave privata, mentre la seconda specifica l'area associata alla sottoscrizione. 
* `Ocp-Apim-Subscription-Key`
* `Ocp-Apim-Subscription-Region`

Area è obbligatoria per la sottoscrizione di API traduzione testuale multiservizio. L'area selezionata è l'unica area che è possibile usare per la traduzione del testo quando si usa la chiave di sottoscrizione multiservizio, e deve trovarsi nella stessa area selezionata durante l'iscrizione per la sottoscrizione multi-service tramite il portale di Azure.

Le aree disponibili sono `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus` e `westus2`.

Se si passa la chiave privata nella stringa di query con il parametro `Subscription-Key`, è necessario specificare l'area con il parametro di query `Subscription-Region`.

Se si usa un bearer token, è necessario ottenere il token dall'endpoint di area: `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken`.


## <a name="errors"></a>Errors

Una risposta di errore standard è un oggetto JSON con coppia nome/valore denominato `error`. Il valore è anche un oggetto JSON con proprietà:

  * `code`: codice di errore definito dal server.

  * `message`: stringa che fornisce una rappresentazione dell'errore leggibile dall'utente.

Ad esempio, un cliente con una sottoscrizione della versione di valutazione gratuita riceverebbe l'errore seguente al superamento della quota gratuita:

```
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
| 400050| Il testo di input è troppo lungo. Vista [limiti di richieste](../request-limits.md).|
| 400064| Il parametro "translation" manca o non è valido.|
| 400070| Il numero di script di destinazione (parametro ToScript) non corrisponde al numero di lingue di destinazione (parametro To).|
| 400071| Il valore non è valido per TextType.|
| 400072| La matrice del testo di input contiene troppi elementi.|
| 400073| Il parametro script non è valido.|
| 400074| Il corpo della richiesta non è in formato JSON valido.|
| 400075| La combinazione di coppia di lingue e categoria non è valida.|
| 400077| Le dimensioni massime della richiesta sono state superate. Vista [limiti di richieste](../request-limits.md).|
| 400079| Il sistema personalizzato richiesto per la traduzione da/verso la lingua non esiste.|
| 400080| Traslitterazione non è supportata per la lingua o lo script.|
| 401000| La richiesta non è autorizzata perché le credenziali mancano o non sono valide.|
| 401015| "Le credenziali specificate si riferiscono a Speech API. Per questa richiesta sono necessarie le credenziali per l'API Testo. Usare una sottoscrizione all'API Traduzione testuale."|
| 403000| L'operazione non è consentita.|
| 403001| L'operazione non è consentita perché la sottoscrizione ha superato la quota gratuita.|
| 405000| Il metodo della richiesta non è supportato per la risorsa richiesta.|
| 408001| Il sistema di traduzione richiesto è in fase di preparazione. Riprovare tra qualche minuto.|
| 408002| Timeout della richiesta in attesa di flusso in ingresso. Il client non ha prodotto una richiesta entro l'ora in cui il server è stato preparato in attesa. Il client può ripetere la richiesta senza modifiche in un secondo momento.|
| 415000| L'intestazione Content-Type manca o non è valida.|
| 429000, 429001, 429002| Il server ha rifiutato la richiesta perché il client ha superato i limiti di richiesta.|
| 500000| Si è verificato un errore imprevisto. Se l'errore persiste, segnalarlo specificando data e ora dell'errore, identificatore della richiesta indicato in X-RequestId nell'intestazione della risposta e identificatore del client indicato in X-ClientTraceId nell'intestazione della richiesta.|
| 503000| Il servizio è temporaneamente non disponibile. Riprovare. Se l'errore persiste, segnalarlo specificando data e ora dell'errore, identificatore della richiesta indicato in X-RequestId nell'intestazione della risposta e identificatore del client indicato in X-ClientTraceId nell'intestazione della richiesta.|

