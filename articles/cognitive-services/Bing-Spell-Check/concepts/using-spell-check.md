---
title: Uso dell'API Controllo ortografico Bing
titleSuffix: Azure Cognitive Services
description: Modalità, impostazioni e altre informazioni relative all'API Controllo ortografico Bing.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: overview
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: 7fd528b5f21622051bdad67c6ffcb6dd8500386a
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423637"
---
# <a name="using-the-bing-spell-check-api"></a>Uso dell'API Controllo ortografico Bing

Questo articolo illustra come usare l'API Controllo ortografico Bing per eseguire il controllo grammaticale e ortografico contestuale. Mentre la maggior parte dei correttori ortografici usa set di regole basate sul dizionario, il controllo ortografico Bing usa l'apprendimento automatico e la traduzione automatica statistica per fornire correzioni accurate e contestuali. 

## <a name="spell-check-modes"></a>Modalità di controllo ortografico

L'API supporta due modalità di controllo ortografico, `Proof` e `Spell`.  Alcuni esempi sono disponibili [qui](https://azure.microsoft.com/services/cognitive-services/spell-check/).

### <a name="proof---for-documents"></a>Proof - per i documenti 

La modalità predefinita è `Proof`. La modalità di controllo ortografico `Proof` offre i controlli più completi, incluse la distinzione tra maiuscole e minuscole, la punteggiatura di base e altre funzionalità utili per la creazione di documenti. È tuttavia disponibile solo nei mercati en-US (Inglese - Stati Uniti), es-ES (Spagnolo), pt-BR (Portoghese). Nota: per lo spagnolo e il portoghese è disponibile solo la versione beta. Per tutti gli altri mercati è necessario impostare su Spell il parametro della query per la modalità. 

> [!NOTE]
> se la lunghezza del testo della query è superiore a 4096, il testo verrà troncato a 4096 caratteri e quindi verrà elaborato. 

### <a name="spell----for-web-searchesqueries"></a>Spell - per ricerche Web/query

L'opzione `Spell` è più aggressiva, in modo da restituire risultati della ricerca migliori. La modalità `Spell` individua la maggior parte degli errori di ortografia, ma non trova alcuni errori grammaticali rilevati da `Proof`, ad esempio gli errori relativi a maiuscole e minuscole e alle parole ripetute.

> [!NOTE]
> * La lunghezza massima supportata della query è indicata di seguito. Se la query supera la lunghezza massima, la query e i relativi risultati non verranno modificati.
>    * 130 caratteri per i codici lingua seguenti: en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, it, zh, ko. 
>    * 65 caratteri per tutti gli altri.
> * La modalità Spell non supporta l'uso dei caratteri di parentesi quadra (`[` e `]`) nelle query e può causare risultati incoerenti. È consigliabile rimuoverli dalle query quando si usa la modalità Spell.

## <a name="market-setting"></a>Impostazione relativa al mercato

È necessario specificare un [codice di mercato](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#market-codes) con il parametro di query `mkt` nella richiesta, altrimenti l'API userà un mercato predefinito basato sull'indirizzo IP della richiesta.


## <a name="http-post-and-get-support"></a>Supporto di HTTP POST e HTTP GET

L'API supporta HTTP POST o HTTP GET. L'opzione usata dipende dalla lunghezza del testo da sottoporre a controllo ortografico. Se le stringhe hanno lunghezza sempre inferiore a 1.500 caratteri, è consigliabile usare GET. Per supportare stringhe con lunghezza massina di 10.000 caratteri, è necessario usare POST. La stringa di testo può includere qualsiasi carattere UTF-8 valido.

L'esempio seguente mostra una richiesta POST per il controllo dell'ortografia e della grammatica di una stringa di testo. L'esempio include il parametro di query [mode](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#mode) per motivi di completezza. È anche possibile non usarlo in questo caso, perché per `mode` viene usata per impostazione predefinita la modalità Proof. Il parametro di query [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text) contiene la stringa da sottoporre a controllo ortografico.
  
```  
POST https://api.cognitive.microsoft.com/bing/v7.0/spellcheck?mode=proof&mkt=en-us HTTP/1.1  
Content-Type: application/x-www-form-urlencoded  
Content-Length: 47  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com  
 
text=when+its+your+turn+turn,+john,+come+runing  
``` 

Se si usa HTTP GET, è necessario includere il parametro di query `text` nella stringa di query dell'URL
  
Di seguito è riportata la risposta alla richiesta precedente. La risposta contiene un oggetto [SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#spellcheck). 
  
```json
{  
    "_type" : "SpellCheck",  
    "flaggedTokens" : [{  
        "offset" : 5,  
        "token" : "its",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "it's",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 25,  
        "token" : "john",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "John",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 19,  
        "token" : "turn",  
        "type" : "RepeatedToken",  
        "suggestions" : [{  
            "suggestion" : "",  
            "score" : 1  
        }]  
    },  
    {  
        "offset" : 35,  
        "token" : "runing",  
        "type" : "UnknownToken",  
        "suggestions" : [{  
            "suggestion" : "running",  
            "score" : 1  
        }]  
    }]  
}  
```  
  
Il campo [flaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#flaggedtokens) elenca gli errori di ortografia e di grammatica rilevati dall'API nella stringa [text](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference#text). Il campo `token` contiene la parola da sostituire. È necessario usare l'offset in base zero nel campo `offset` per trovare il token nella stringa `text`. Sostituire quindi la parola in tale posizione con la parola specificata nel campo `suggestion`. 

Se il campo `type` è RepeatedToken, il token verrà comunque sostituito con `suggestion` ma è probabile che sia anche necessario rimuovere lo spazio finale.

## <a name="throttling-requests"></a>Limitazione delle richieste

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sull'API Controllo ortografico Bing](../overview.md)
- [Informazioni di riferimento per l'API Controllo ortografico Bing v7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
