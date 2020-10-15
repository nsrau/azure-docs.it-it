---
title: Guida di riferimento a Translator V 3.0
titleSuffix: Azure Cognitive Services
description: Documentazione di riferimento per Translator V 3.0. La versione 3 di Translator fornisce un'API Web moderna basata su JSON.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 8/11/2020
ms.author: swmachan
ms.openlocfilehash: 6b211dd8ca735ea9ee4a5209aa6030398cca472e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88121018"
---
# <a name="translator-v30"></a>Traduttore v 3.0

## <a name="whats-new"></a>Novità

La versione 3 di Translator fornisce un'API Web moderna basata su JSON. Migliora l'usabilità e le prestazioni consolidando le funzionalità esistenti in un minor numero di operazioni e offre nuove funzionalità.

 * Traslitterazione per convertire il testo in una lingua da un carattere all'altro.
 * Traduzione in più lingue in una sola richiesta.
 * Rilevamento della lingua, traduzione e traslitterazione in una sola richiesta.
 * Dizionario per cercare le traduzioni alternative di un termine, per trovare le traduzioni e gli esempi che mostrano i termini usati nel contesto.
 * Altri risultati informativi sul rilevamento della lingua.

## <a name="base-urls"></a>URL di base

Microsoft Translator è accessibile da più posizioni di data center. Attualmente si trovano in 10 [aree geografiche di Azure](https://azure.microsoft.com/global-infrastructure/regions):

* **Americhe:** Stati Uniti orientali, Stati Uniti centro-meridionali, Stati Uniti centro-occidentali e Stati Uniti occidentali 2 
* **Asia Pacifico:** Corea meridionale, Giappone orientale, Asia sudorientale e Australia orientale
* **Europa:** Europa settentrionale ed Europa occidentale

Le richieste a Microsoft Translator sono nella maggior parte dei casi gestiti dal Data Center più vicino alla posizione in cui ha avuto origine la richiesta. In caso di errore di un Data Center, la richiesta può essere instradata al di fuori della geografia di Azure.

Per forzare la gestione della richiesta da parte di una specifica area geografica di Azure, impostare l'endpoint globale nella richiesta dell'API sull'endpoint di area desiderato:

|Descrizione|Geografia di Azure|URL di base|
|:--|:--|:--|
|Azure|Globale (non a livello di area)|   api.cognitive.microsofttranslator.com|
|Azure|Stati Uniti|   api-nam.cognitive.microsofttranslator.com|
|Azure|Europa|  api-eur.cognitive.microsofttranslator.com|
|Azure|Asia Pacifico|    api-apc.cognitive.microsofttranslator.com|

## <a name="authentication"></a>Authentication

Sottoscrivere il [multiservizio](https://azure.microsoft.com/pricing/details/cognitive-services/) di servizi cognitivi o Translator in Servizi cognitivi di Azure e usare la chiave di sottoscrizione (disponibile nella portale di Azure) per l'autenticazione. 

Sono tre le intestazioni che è possibile usare per autenticare la sottoscrizione. Questa tabella descrive il modo in cui viene usato ogni:

|Intestazioni|Descrizione|
|:----|:----|
|Ocp-Apim-Subscription-Key|*Usare con la sottoscrizione di Servizi cognitivi se si passa la chiave privata*.<br/>Il valore è la chiave privata di Azure per la sottoscrizione a Translator.|
|Autorizzazione|*Usare con la sottoscrizione di Servizi cognitivi se si passa un token di autenticazione*.<br/>Il valore è il token di connessione: `Bearer <token>`.|
|Ocp-Apim-Subscription-Region|*Usare con servizi cognitivi e la risorsa di conversione regionale.*<br/>Il valore è l'area della risorsa per più servizi o per la conversione a livello di area. Questo valore è facoltativo quando si usa una risorsa di conversione globale.|

###  <a name="secret-key"></a>Chiave privata
La prima opzione consiste nell'eseguire l'autenticazione usando l'intestazione `Ocp-Apim-Subscription-Key`. Aggiungere l' `Ocp-Apim-Subscription-Key: <YOUR_SECRET_KEY>` intestazione alla richiesta.

#### <a name="authenticating-with-a-global-resource"></a>Autenticazione con una risorsa globale

Quando si usa una [risorsa di conversione globale](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation), è necessario includere un'intestazione per chiamare il traduttore.

|Intestazioni|Descrizione|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Il valore è la chiave privata di Azure per la sottoscrizione a Translator.|

Ecco una richiesta di esempio per chiamare il traduttore usando la risorsa di conversione globale

```curl
// Pass secret key using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-regional-resource"></a>Autenticazione con una risorsa a livello di area

Quando si usa una [risorsa di conversione regionale](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation).
È necessario chiamare il convertitore per due intestazioni.

|Intestazioni|Descrizione|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Il valore è la chiave privata di Azure per la sottoscrizione a Translator.|
|Ocp-Apim-Subscription-Region| Il valore è l'area della risorsa di conversione. |

Ecco una richiesta di esempio per chiamare il traduttore usando la risorsa di conversione regionale

```curl
// Pass secret key and region using headers
curl -X POST "https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&to=es" \
     -H "Ocp-Apim-Subscription-Key:<your-key>" \
     -H "Ocp-Apim-Subscription-Region:<your-region>" \
     -H "Content-Type: application/json" \
     -d "[{'Text':'Hello, what is your name?'}]"
```

#### <a name="authenticating-with-a-multi-service-resource"></a>Autenticazione con una risorsa multiservizio

Quando si usa una risorsa multiservizio di un servizio cognitivo. In questo modo è possibile usare una singola chiave privata per autenticare le richieste per più servizi. 

Quando si usa una chiave privata a più servizi, è necessario includere due intestazioni di autenticazione con la richiesta. È necessario chiamare il convertitore per due intestazioni.

|Intestazioni|Descrizione|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Il valore è la chiave privata di Azure per la risorsa multiservizio.|
|Ocp-Apim-Subscription-Region| Il valore è l'area della risorsa multiservizio. |

L'area è obbligatoria per la sottoscrizione dell'API del testo multiservizio. L'area selezionata è l'unica area che è possibile usare per la traduzione di testo quando si usa la chiave di sottoscrizione multiservizio e deve essere la stessa area selezionata al momento dell'iscrizione per la sottoscrizione multiservizio tramite il portale di Azure.

Le aree disponibili sono `australiaeast` ,, `brazilsouth` `canadacentral` , `centralindia` , `centralus` , `centraluseuap` , `eastasia` , `eastus` , `eastus2` , `francecentral` , `japaneast` , `japanwest` , `koreacentral` , `northcentralus` , `northeurope` , `southcentralus` , `southeastasia` , `uksouth` , `westcentralus` , `westeurope` , `westus` , `westus2` e `southafricanorth` .

Se si passa la chiave privata nella stringa di query con il parametro `Subscription-Key`, è necessario specificare l'area con il parametro di query `Subscription-Region`.

### <a name="authenticating-with-an-access-token"></a>Autenticazione con un token di accesso
In alternativa, è possibile scambiare la chiave privata con un token di accesso. Questo token viene incluso in ogni richiesta come intestazione `Authorization`. Per ottenere un token di autorizzazione, effettuare una richiesta `POST` all'URL seguente:

| Tipo di risorsa     | URL servizio di autenticazione                                |
|-----------------|-----------------------------------------------------------|
| Globale          | `https://api.cognitive.microsoft.com/sts/v1.0/issueToken` |
| Regione o multiservizio | `https://<your-region>.api.cognitive.microsoft.com/sts/v1.0/issueToken` |

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

Un token di autenticazione è valido per 10 minuti. Il token deve essere riutilizzato quando si effettuano più chiamate al convertitore. Tuttavia, se il programma effettua richieste al traduttore in un periodo di tempo prolungato, il programma deve richiedere un nuovo token di accesso a intervalli regolari, ad esempio ogni 8 minuti.

## <a name="virtual-network-support"></a>Supporto della rete virtuale

Il servizio Translator è ora disponibile con le funzionalità di rete virtuale (VNET) in tutte le aree del cloud pubblico di Azure. Per abilitare la rete virtuale, vedere [configurazione di reti virtuali dei servizi cognitivi di Azure](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-virtual-networks?tabs=portal). 

Una volta attivata questa funzionalità, è necessario utilizzare l'endpoint personalizzato per chiamare il traduttore. Non è possibile usare l'endpoint di conversione globale ("api.cognitive.microsofttranslator.com") e non è possibile eseguire l'autenticazione con un token di accesso.

È possibile trovare l'endpoint personalizzato dopo la creazione di una [risorsa di conversione](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) e consentire l'accesso da reti selezionate ed endpoint privati.

|Intestazioni|Descrizione|
|:-----|:----|
|Ocp-Apim-Subscription-Key| Il valore è la chiave privata di Azure per la sottoscrizione a Translator.|
|Ocp-Apim-Subscription-Region| Il valore è l'area della risorsa di conversione. Questo valore è facoltativo se la risorsa è `global`|

Ecco una richiesta di esempio per chiamare il traduttore usando l'endpoint personalizzato

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
| 400050| Il testo di input è troppo lungo. Visualizzare i [limiti della richiesta](../request-limits.md).|
| 400064| Il parametro "translation" manca o non è valido.|
| 400070| Il numero di script di destinazione (parametro ToScript) non corrisponde al numero di lingue di destinazione (parametro To).|
| 400071| Il valore non è valido per TextType.|
| 400072| La matrice del testo di input contiene troppi elementi.|
| 400073| Il parametro script non è valido.|
| 400074| Il corpo della richiesta non è in formato JSON valido.|
| 400075| La combinazione di coppia di lingue e categoria non è valida.|
| 400077| Le dimensioni massime della richiesta sono state superate. Visualizzare i [limiti della richiesta](../request-limits.md).|
| 400079| Il sistema personalizzato richiesto per la traduzione da/verso la lingua non esiste.|
| 400080| La traslitterazione non è supportata per il linguaggio o lo script.|
| 401000| La richiesta non è autorizzata perché le credenziali mancano o non sono valide.|
| 401015| "Le credenziali specificate si riferiscono a Speech API. Per questa richiesta sono necessarie le credenziali per l'API Testo. Usare una sottoscrizione di Translator ".|
| 403000| L'operazione non è consentita.|
| 403001| L'operazione non è consentita perché la sottoscrizione ha superato la quota gratuita.|
| 405000| Il metodo della richiesta non è supportato per la risorsa richiesta.|
| 408001| È in corso la preparazione del sistema di traduzione richiesto. Riprovare tra qualche minuto.|
| 408002| Timeout della richiesta in attesa del flusso in ingresso. Il client non ha prodotto una richiesta entro il tempo in cui il server è stato preparato per l'attesa. Il client può ripetere la richiesta senza modifiche in un secondo momento.|
| 415000| L'intestazione Content-Type manca o non è valida.|
| 429000, 429001, 429002| Il server ha rifiutato la richiesta perché il client ha superato i limiti di richiesta.|
| 500000| Si è verificato un errore imprevisto. Se l'errore persiste, segnalarlo specificando data e ora dell'errore, identificatore della richiesta indicato in X-RequestId nell'intestazione della risposta e identificatore del client indicato in X-ClientTraceId nell'intestazione della richiesta.|
| 503000| Il servizio è temporaneamente non disponibile. Riprovare. Se l'errore persiste, segnalarlo specificando data e ora dell'errore, identificatore della richiesta indicato in X-RequestId nell'intestazione della risposta e identificatore del client indicato in X-ClientTraceId nell'intestazione della richiesta.|

## <a name="metrics"></a>Metriche 
Le metriche consentono di visualizzare le informazioni sull'utilizzo e sulla disponibilità del convertitore in portale di Azure, nella sezione metrica, come illustrato nella schermata seguente. Per altre informazioni, vedere [metriche dei dati e della piattaforma](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

![Metriche del traduttore](../media/translatormetrics.png)

Questa tabella elenca le metriche disponibili con una descrizione del modo in cui vengono usate per monitorare le chiamate API di traduzione.

| Metriche | Descrizione |
|:----|:-----|
| TotalCalls| Numero totale di chiamate API.|
| TotalTokenCalls| Numero totale di chiamate API tramite il servizio token utilizzando il token di autenticazione.|
| SuccessfulCalls| Numero di chiamate riuscite.|
| TotalErrors| Numero di chiamate con risposta di errore.|
| BlockedCalls| Numero di chiamate che hanno superato il limite di frequenza o di quota.|
| ServerErrors| Numero di chiamate con errore interno del server (5XX).|
| ClientErrors| Numero di chiamate con errore sul lato client (4XX).|
| Latenza| Durata per completare la richiesta in millisecondi.|
| CharactersTranslated| Numero totale di caratteri nella richiesta di testo in ingresso.|
