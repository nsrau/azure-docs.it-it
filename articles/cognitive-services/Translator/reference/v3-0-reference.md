---
title: Riferimenti per l'API Traduzione testuale v3.0
titlesuffix: Azure Cognitive Services
description: Documentazione di riferimento per l'API Traduzione testuale v3.0.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-jansko
ms.openlocfilehash: 5c952370908919deb6531e0b175063dc2657ae98
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52870403"
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

Microsoft Translator è accessibile da più posizioni di data center. Attualmente tali posizioni si trovano in sei [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions):

* **Americhe:** Stati Uniti occidentali 2 e Stati Uniti centro-occidentali 
* **Asia Pacifico:** Asia sud-orientale e Corea meridionale
* **Europa:** Europa settentrionale e Europa occidentale

Nella maggior parte dei casi le richieste per l'API Traduzione testuale Microsoft vengono gestite dal data center più vicino all'area di origine della richiesta. In caso di errore del data center la richiesta può essere instradata all'esterno dell'area.

Per fare in modo che la richiesta venga gestita da un data center specifico, cambiare l'endpoint Globale nella richiesta API nell'endpoint desiderato a livello di area:

|Descrizione|Area|URL di base|
|:--|:--|:--|
|Azure|Globale|  api.cognitive.microsofttranslator.com|
|Azure|America del Nord|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Asia/Pacifico|    api-apc.cognitive.microsofttranslator.com|


## <a name="authentication"></a>Authentication

Sottoscrivere l'API Traduzione testuale o l'[offerta per Servizi cognitivi integrati](https://azure.microsoft.com/pricing/details/cognitive-services/) in Servizi cognitivi Microsoft e usare la chiave di sottoscrizione (disponibile nel portale di Azure) per eseguire l'autenticazione. 

Sono tre le intestazioni che è possibile usare per autenticare la sottoscrizione. Questa tabella spiega come usare ogni intestazione:

|Intestazioni|Descrizione|
|:----|:----|
|Ocp-Apim-Subscription-Key|*Usare con la sottoscrizione di Servizi cognitivi se si passa la chiave privata*.<br/>Il valore è la chiave privata di Azure per la sottoscrizione dell'API Traduzione testuale.|
|Authorization|*Usare con la sottoscrizione di Servizi cognitivi se si passa un token di autenticazione*.<br/>Il valore è il token di connessione: `Bearer <token>`.|
|Ocp-Apim-Subscription-Region|*Usare con la sottoscrizione integrata di Servizi cognitivi se si passa una chiave privata integrata.*<br/>Il valore corrisponde all'area della sottoscrizione integrata. Questo valore è facoltativo quando non si usa una sottoscrizione integrata.|

###  <a name="secret-key"></a>Chiave privata
La prima opzione consiste nell'eseguire l'autenticazione usando l'intestazione `Ocp-Apim-Subscription-Key`. Aggiungere semplicemente l'intestazione `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` alla richiesta.

### <a name="authorization-token"></a>Token di autorizzazione
In alternativa, è possibile scambiare la chiave privata con un token di accesso. Questo token viene incluso in ogni richiesta come intestazione `Authorization`. Per ottenere un token di autorizzazione, effettuare una richiesta `POST` all'URL seguente:

| Ambiente     | URL servizio di autenticazione                                |
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

### <a name="all-in-one-subscription"></a>Sottoscrizione integrata

L'ultima opzione di autenticazione consiste nell'usare una sottoscrizione integrata di Servizi cognitivi. In questo modo è possibile usare una singola chiave privata per autenticare le richieste per più servizi. 

Quando si usa una chiave privata integrata, è necessario includere due intestazioni di autenticazione nella richiesta. La prima passa la chiave privata, mentre la seconda specifica l'area associata alla sottoscrizione. 
* `Ocp-Api-Subscription-Key`
* `Ocp-Apim-Subscription-Region`

Se si passa la chiave privata nella stringa di query con il parametro `Subscription-Key`, è necessario specificare l'area con il parametro di query `Subscription-Region`.

Se si usa un bearer token, è necessario ottenere il token dall'endpoint di area: `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken`.

Le aree disponibili sono `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus` e `westus2`.

L'area è obbligatoria per la sottoscrizione integrata dell'API Traduzione testuale.

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
| 400050| Il testo di input è troppo lungo.|
| 400064| Il parametro "translation" manca o non è valido.|
| 400070| Il numero di script di destinazione (parametro ToScript) non corrisponde al numero di lingue di destinazione (parametro To).|
| 400071| Il valore non è valido per TextType.|
| 400072| La matrice del testo di input contiene troppi elementi.|
| 400073| Il parametro script non è valido.|
| 400074| Il corpo della richiesta non è in formato JSON valido.|
| 400075| La combinazione di coppia di lingue e categoria non è valida.|
| 400077| Le dimensioni massime della richiesta sono state superate.|
| 400079| Il sistema personalizzato richiesto per la traduzione da/verso la lingua non esiste.|
| 401000| La richiesta non è autorizzata perché le credenziali mancano o non sono valide.|
| 401015| "Le credenziali specificate si riferiscono a Speech API. Per questa richiesta sono necessarie le credenziali per l'API Testo. Usare una sottoscrizione all'API Traduzione testuale."|
| 403000| L'operazione non è consentita.|
| 403001| L'operazione non è consentita perché la sottoscrizione ha superato la quota gratuita.|
| 405000| Il metodo della richiesta non è supportato per la risorsa richiesta.|
| 408001| Il sistema di traduzione personalizzato richiesto non è ancora disponibile. Riprovare tra qualche minuto.|
| 415000| L'intestazione Content-Type manca o non è valida.|
| 429000, 429001, 429002| Il server ha rifiutato la richiesta perché il client invia un numero eccessivo di richieste. Ridurre la frequenza per evitare la limitazione delle richieste.|
| 500000| Si è verificato un errore imprevisto. Se l'errore persiste, segnalarlo specificando data e ora dell'errore, identificatore della richiesta indicato in X-RequestId nell'intestazione della risposta e identificatore del client indicato in X-ClientTraceId nell'intestazione della richiesta.|
| 503000| Il servizio è temporaneamente non disponibile. Riprovare. Se l'errore persiste, segnalarlo specificando data e ora dell'errore, identificatore della richiesta indicato in X-RequestId nell'intestazione della risposta e identificatore del client indicato in X-ClientTraceId nell'intestazione della richiesta.|

