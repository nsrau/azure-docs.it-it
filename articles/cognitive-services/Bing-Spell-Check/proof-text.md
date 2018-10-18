---
title: Informazioni sull'API Controllo ortografico Bing
titlesuffix: Azure Cognitive Services
description: L'API Controllo ortografico Bing usa le funzionalità di Machine Learning e di traduzione automatica statistica per il controllo ortografico contestuale.
services: cognitive-services
author: noellelacharite
manager: cgronlun
ms.service: cognitive-services
ms.component: bing-spell-check
ms.topic: overview
ms.date: 05/03/2018
ms.author: nolachar
ms.openlocfilehash: 4caa05ffa96dbc15922fed85edfdefdb68ead68b
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49361712"
---
# <a name="what-is-bing-spell-check-api"></a>Informazioni sull'API Controllo ortografico Bing

L'API Controllo ortografico Bing consente di eseguire il controllo grammaticale e ortografico contestuale.

Esiste una differenza tra i normali correttori ortografici e il correttore ortografico di terza generazione di Bing. I correttori ortografici attuali si basano sulla verifica dell'ortografia e della grammatica rispetto a set di regole basati sul dizionario, che vengono aggiornati ed espansi periodicamente. In altre parole, il correttore ortografico è efficace solo quanto il dizionario che lo supporta e quanto il personale che gestisce il dizionario. Questo è il tipo di correttore ortografico usato in Microsoft Word e in altri elaboratori di testo.

Bing ha invece sviluppato un correttore ortografico basato sul Web, che sfrutta i vantaggi offerti da Machine Learning e dalla traduzione automatica statistica per eseguire in modo dinamico il training di un algoritmo altamente contestuale in continua evoluzione. Il correttore ortografico è basato su un numero elevatissimo di ricerche Web e documenti.

Questo correttore ortografico può gestire qualsiasi scenario di elaborazione di testo:

- Riconoscimento di espressioni gergali e linguaggio informale
- Riconoscimento degli errori comuni relativi ai nomi nel contesto
- Correzione dei problemi di separazione delle parole con un singolo flag
- Correzione di omofoni nel contesto e di altri errori difficili da individuare
- Supporto per nuovi marchi, contenuti multimediali ed espressioni molto diffuse non appena emergono
- Distinzione di parole con suono simile ma significato e ortografia diverse, ad esempio "see" e "sea" per l'inglese.

## <a name="spell-check-modes"></a>Modalità di controllo ortografico

L'API supporta due modalità di controllo ortografico, `Proof` e `Spell`.  Alcuni esempi sono disponibili [qui](https://azure.microsoft.com/services/cognitive-services/spell-check/).
### <a name="proof---for-documents-scenario"></a>Proof - Per scenari relativi ai documenti
La modalità predefinita è `Proof`. La modalità di controllo ortografico `Proof` offre i controlli più completi, incluse la distinzione tra maiuscole e minuscole, la punteggiatura di base e altre funzionalità utili per la creazione di documenti. È tuttavia disponibile solo nei mercati en-US (Inglese - Stati Uniti), es-ES (Spagnolo), pt-BR (Portoghese). Nota: per lo spagnolo e il portoghese è disponibile solo la versione beta. Per tutti gli altri mercati è necessario impostare su Spell il parametro della query per la modalità. 
<br /><br/>**NOTA:** se la lunghezza del testo della query è superiore a 4096, il testo verrà troncato a 4096 caratteri e quindi verrà elaborato. 
### <a name="spell----for-web-searchesqueries-scenario"></a>Spell - Per scenari di ricerche Web/query
L'opzione `Spell` è più aggressiva, in modo da restituire risultati della ricerca migliori. La modalità `Spell` individua la maggior parte degli errori di ortografia, ma non trova alcuni errori grammaticali rilevati da `Proof`, ad esempio gli errori relativi a maiuscole e minuscole e alle parole ripetute.
<br /></br>**NOTA:** la lunghezza massima supportata per le query è indicata più avanti. Se la query supera il limite, il risultato visualizzato indica che la query non è stata modificata.
<ul><li>130 caratteri per le lingue con codice en, de, es, fr, pl, pt, sv, ru, nl, nb, tr-tr, it, zh, ko. </li>
<li>65 caratteri per le altre lingue</li></ul>

## <a name="market-setting"></a>Impostazione relativa al mercato
È necessario specificare il mercato nel parametro della query nell'URL della richiesta. In caso contrario, il correttore ortografico userà il mercato predefinito in base all'indirizzo IP.


## <a name="post-vs-get"></a>Confronto tra POST e GET

L'API supporta HTTP POST o HTTP GET. L'opzione usata dipende dalla lunghezza del testo da sottoporre a controllo ortografico. Se le stringhe hanno lunghezza sempre inferiore a 1.500 caratteri, è consigliabile usare GET. Per supportare stringhe con lunghezza massina di 10.000 caratteri, è necessario usare POST. La stringa di testo può includere qualsiasi carattere UTF-8 valido.

L'esempio seguente mostra una richiesta POST per il controllo dell'ortografia e della grammatica di una stringa di testo. L'esempio include il parametro di query [mode](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#mode) per motivi di completezza. È anche possibile non usarlo in questo caso, perché per `mode` viene usata per impostazione predefinita la modalità Proof. Il parametro di query [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text) contiene la stringa da sottoporre a controllo ortografico.
  
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
  
Di seguito è riportata la risposta alla richiesta precedente. La risposta contiene un oggetto [SpellCheck](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#spellcheck). 
  
```  
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
  
Il campo [flaggedTokens](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#flaggedtokens) elenca gli errori di ortografia e di grammatica rilevati dall'API nella stringa [text](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference#text). Il campo `token` contiene la parola da sostituire. È necessario usare l'offset in base zero nel campo `offset` per trovare il token nella stringa `text`. Sostituire quindi la parola in tale posizione con la parola specificata nel campo `suggestion`. 

Se il campo `type` è RepeatedToken, il token verrà comunque sostituito con `suggestion` ma è probabile che sia anche necessario rimuovere lo spazio finale.

## <a name="throttling-requests"></a>Limitazione delle richieste

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Passaggi successivi

Per iniziare rapidamente con la prima richiesta, vedere [Making your first entities request](quickstarts/csharp.md) (Come effettuare la prima richiesta di entità).

Acquisire familiarità con [Bing Spell Check API Reference](https://docs.microsoft.com/rest/api/cognitiveservices/bing-spell-check-api-v7-reference) (Informazioni di riferimento sull'API Controllo ortografico Bing). La documentazione di riferimento contiene l'elenco di endpoint, intestazioni e parametri di query da usare per richiedere i risultati della ricerca e le definizioni degli oggetti della risposta. 

Per non violare nessuna delle regole relative all'uso dei risultati, vedere [Bing Use and Display Requirements](./useanddisplayrequirements.md) (Requisiti per l'uso e la visualizzazione di Bing).
