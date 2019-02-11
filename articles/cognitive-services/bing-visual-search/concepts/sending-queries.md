---
title: Invio di query di ricerca all'API Ricerca visiva Bing
titlesuffix: Azure Cognitive Services
description: Informazioni sui parametri dell'API REST utilizzati nell'API Ricerca visiva Bing.
services: cognitive-services
author: aahill
manager: cgronlun
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: article
ms.date: 12/18/2018
ms.author: aahi
ms.openlocfilehash: 07f471debad87a2fa2f6583db624e9ee204d02bc
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55821168"
---
# <a name="sending-search-queries-to-the-bing-visual-search-api"></a>Invio di query di ricerca all'API Ricerca visiva Bing

Questo articolo descrive i parametri e gli attributi delle richieste inviate all'API Ricerca visiva Bing, nonché l'oggetto risposta.

È possibile ottenere informazioni dettagliate su un'immagine in tre modi: 

- Usando un token di informazioni dettagliate che si ottiene da un'immagine in una chiamata precedente a uno degli endpoint dell'[API Ricerca immagini Bing](https://docs.microsoft.com/rest/api/cognitiveservices/bing-images-api-v7-reference).
- Inviando l'URL di un'immagine
- Caricando un'immagine, in formato binario


## <a name="bing-visual-search-requests"></a>Richieste all'API Ricerca visiva Bing

Se si invia a Ricerca visiva un URL o un token di immagine, la figura seguente illustra l'oggetto JSON che è necessario includere nel corpo del POST. 

```json
{
    "imageInfo" : {
        "url" : "",
        "imageInsightsToken" : "",
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.5,
            "right" : 0.9,
            "bottom" : 0.9
        }
    },
    "knowledgeRequest" : {
      "filters" : {
        "site" : ""
      }
    }
}
```

L'oggetto `imageInfo` deve includere il campo `url` o `imageInsightsToken`, ma non entrambi. Impostare il campo `url` sull'URL di un'immagine accessibile in Internet. Le dimensioni massime supportate per l'immagine sono di 1 MB.

L'oggetto `imageInsightsToken` deve essere impostato su un token di informazioni dettagliate. Per ottenerlo, chiamare l'API Ricerca immagini Bing. La risposta contiene un elenco di oggetti `Image`. Ogni oggetto `Image` contiene un `imageInsightsToken` campo, che a sua volta contiene il token.

Il campo `cropArea` è facoltativo. L'area di ritaglio specifica l'angolo superiore sinistro e l'angolo inferiore destro di un'area di interesse. Specificare i valori compresi nell'intervallo tra 0,0 e 1,0. I valori sono una percentuale della larghezza o dell'altezza complessiva. Nell'esempio precedente la parte destra dell'immagine viene contrassegnata come l'area di interesse. Includere tale valore se si intende limitare la richiesta di informazioni dettagliate all'area di interesse.

L'oggetto `filters` contiene un filtro di sito (vedere il campo `site`) che è possibile usare per limitare le immagini e i risultati di prodotti simili a un dominio specifico. Se ad esempio l'immagine è di un Surface Book, è possibile impostare `site` su www.microsoft.com. 

Se si vuole ottenere informazioni dettagliate su una copia locale di un'immagine, caricare l'immagine come dati binari.

Per informazioni dettagliate su come includere queste opzioni nel corpo del POST, vedere [Tipi di formato per il contenuto](#content-form-types).


### <a name="search-endpoint"></a>Endpoint di Ricerca

L'endpoint di Ricerca visiva è https:\/\/api.cognitive.microsoft.com/bing/v7.0/images/visualsearch.

Le richieste devono essere inviate solo come richieste HTTP POST. 


### <a name="query-parameters"></a>Parametri di query

Di seguito vengono indicati i parametri di query da specificare nella richiesta. È necessario includere almeno il parametro d query `mkt`.

| NOME                              | Valore                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 | Type   | Obbligatoria |
|-----------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------|----------|
| <a name="cc" />cc                 | Codice di 2 caratteri del paese da cui provengono i risultati.<br /><br /> Se si imposta questo parametro, è necessario specificare anche l'intestazione [Accept-Language](#acceptlanguage). Bing usa la prima lingua supportata individuata nell'elenco delle lingue e combina la lingua con il codice paese specificato per determinare il mercato da cui restituire i risultati. Se nell'elenco non è presenta alcuna lingua supportata, Bing rileva la lingua e il mercato più vicini che supportano la richiesta. In alternativa, Bing può usare un mercato aggregato o predefinito per i risultati anziché quello specificato.<br /><br /> È necessario usare questo parametro di query e il parametro di query `Accept-Language` solo se si specificano più lingue. In caso contrario, è necessario usare i parametri di query `mkt` e `setLang`.<br /><br /> Tale parametro e il parametro di query [mkt](#mkt) si escludono a vicenda&mdash;non specificarli entrambi. | string | No        |
| <a name="mkt" />mkt               | Mercato dal quale provengono i risultati. <br /><br /> **NOTA:** è consigliabile specificare sempre il mercato, se noto. La specifica del mercato consente a Bing indirizzare la richiesta e di restituire una risposta appropriata e ottimale.<br /><br /> Tale parametro e il parametro di query [cc](#cc) si escludono a vicenda&mdash;non specificarli entrambi.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           | string | Sì      |
| <a name="safesearch" />safeSearch | Filtro usato per filtrare il contenuto per adulti. Di seguito sono indicati i possibili valori di filtro con distinzione tra maiuscole e minuscole.<br /><ul><li>Off&mdash;Vengono restituite pagine Web con testo o immagini per adulti.<br /><br/></li><li>Moderate&mdash;Vengono restituite pagine Web con testo per adulti, ma non con immagini per adulti.<br /><br/></li><li>Strict&mdash;Non viene restituita alcuna pagina Web con testo o immagini per adulti.</li></ul><br /> Il valore predefinito è Moderate.<br /><br /> **NOTA:** se la richiesta proviene da un mercato in cui il criterio per adulti di Bing richiede che `safeSearch` sia impostato su Strict, Bing ignora il valore di `safeSearch` e usa il valore Strict.<br/><br/>**NOTA:** se si usa l'operatore di query `site:`, esiste la possibilità che la risposta possa includere contenuti per adulti indipendentemente dall'impostazione del parametro di query `safeSearch`. Usare `site:` solo se si è a conoscenza del contenuto del sito e lo scenario prevede la possibilità di contenuto per adulti.                                    | string | No        |
| <a name="setlang" />setLang       | Lingua da usare per le stringhe dell'interfaccia utente. Specificare la lingua tramite il codice lingua a due lettere ISO 639-1. Ad esempio, il codice lingua per l'inglese è EN. L'impostazione predefinita è EN (inglese).<br /><br /> Sebbene sia facoltativo, è opportuno specificare sempre la lingua. In genere, si imposta `setLang` sulla stessa lingua specificata da `mkt`, a meno che non si intenda visualizzare le stringhe dell'interfaccia utente in un'altra lingua.<br /><br /> Questo parametro e l'intestazione [Accept-Language](#acceptlanguage) si escludono a vicenda&mdash;non specificare entrambi.<br /><br /> Una stringa di interfaccia utente è una stringa usata come etichetta in un'interfaccia utente. Gli oggetti di risposta JSON contengono poche stringhe di interfaccia utente. Eventuali collegamenti alle proprietà Bing.com negli oggetti risposta si applicano anche alla lingua specificata.                                                                                                            | string | No        |

## <a name="headers"></a>Headers

Di seguito vengono indicate le intestazioni che la richiesta deve specificare. Le intestazioni Content-Type e Ocp-Apim-Subscription-Key sono le sole intestazioni richieste, ma è necessario anche includer User-Agent, X-MSEdge-ClientID, X-MSEdge-ClientIP e X-Search-Location.


| Intestazione                                                | DESCRIZIONE                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| <a name="acceptlanguage" />Accept-Language            | Intestazione di richiesta facoltativa.<br /><br /> Elenco delimitato da virgole di lingue da usare per le stringhe dell'interfaccia utente. L'elenco è in ordine decrescente di preferenza. Per altre informazioni, incluso il formato previsto, vedere [RFC2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Questa intestazione e il parametro di query [setLang](#setlang) si escludono a vicenda&mdash;non specificare entrambi.<br /><br /> Se si imposta questa intestazione, è necessario anche specificare il parametro di query [cc](#cc). Per determinare il mercato per cui restituire i risultati, Bing usa la prima lingua supportata individuata nell'elenco e la combina con il valore del parametro `cc`. Se l'elenco non include una lingua supportata, Bing trova la corrispondenza più vicina della lingua e il mercato che supporta la richiesta oppure usa un mercato aggregato o predefinito per i risultati. Per determinare il mercato usato da Bing, vedere l'intestazione BingAPIs-Market.<br /><br /> Usare questa intestazione e il parametro di query `cc` solo se si specificano più lingue. In caso contrario, usare i parametri di query [mkt](#mkt) e [setLang](#setlang).<br /><br /> Una stringa di interfaccia utente è una stringa usata come etichetta in un'interfaccia utente. Gli oggetti di risposta JSON contengono poche stringhe di interfaccia utente. Eventuali collegamenti alle proprietà Bing.com negli oggetti di risposta si applicano alla lingua specificata.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| <a name="contenttype" />Content-Type                  |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| <a name="market" />BingAPIs-Market                    | Intestazione della risposta.<br /><br /> Il mercato usato dalla richiesta. Il formato è \<languageCode\>-\<countryCode\>, ad esempio en-US.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
| <a name="traceid" />BingAPIs-TraceId                  | Intestazione della risposta.<br /><br /> L'ID della voce di registro che contiene i dettagli della richiesta. In caso di errore acquisire questo ID. Se non si riesce a individuare e risolvere il problema, includere questo ID con le altre informazioni inviate al team di supporto.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                |
| <a name="subscriptionkey" />Ocp-Apim-Subscription-Key | Intestazione della richiesta obbligatoria.<br /><br /> La chiave di sottoscrizione ricevuta quando è stata eseguita la registrazione per questo servizio in [Servizi cognitivi](https://www.microsoft.com/cognitive-services/).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                          |
| <a name="pragma" />Pragma                             |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| <a name="useragent" />User-Agent                      | Intestazione di richiesta facoltativa.<br /><br /> L'agente utente di origine della richiesta. Bing usa l'agente utente per fornire agli utenti di dispositivi mobili un'esperienza ottimizzata. Sebbene sia facoltativa, è consigliabile specificare sempre questa intestazione.<br /><br /> La stringa user-agent deve essere la stessa stringa inviata da qualsiasi browser di uso comune. Per informazioni sugli agenti utente, vedere [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).<br /><br /> Di seguito sono riportati esempi di stringhe user-agent.<br /><ul><li>Windows Phone&mdash;Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)<br /><br /></li><li>Android&mdash;Mozilla/5.0 (Linux; U; Android 2.3.5; en-us; SCH-I500 Build/GINGERBREAD) AppleWebKit/533.1 (KHTML; like Gecko) Version/4.0 Mobile Safari/533.1<br /><br /></li><li>iPhone&mdash;Mozilla/5.0 (iPhone; CPU iPhone OS 6_1 like Mac OS X) AppleWebKit/536.26 (KHTML; like Gecko) Mobile/10B142 iPhone4;1 BingWeb/3.03.1428.20120423<br /><br /></li><li>PC&mdash;Mozilla/5.0 (Windows NT 6.3; WOW64; Trident/7.0; Touch; rv:11.0) like Gecko<br /><br /></li><li>iPad&mdash;Mozilla/5.0 (iPad; CPU OS 7_0 like Mac OS X) AppleWebKit/537.51.1 (KHTML, like Gecko) Version/7.0 Mobile/11A465 Safari/9537.53</li></ul>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| <a name="clientid" />X-MSEdge-ClientID                | Intestazione di richiesta e risposta facoltativa.<br /><br /> Bing usa questa intestazione per fornire agli utenti un comportamento coerente tra le chiamate API Bing. Bing spesso invia in versione di anteprima i miglioramenti e le nuove funzionalità e usa l'ID client come chiave per l'assegnazione del traffico per le diverse versioni di anteprima. Se non si usa lo stesso ID client per un utente in più richieste, Bing può assegnare l'utente a più versioni di anteprima in conflitto. L'assegnazione a più versioni di anteprima in conflitto può generare un'esperienza utente incoerente. Ad esempio, se alla seconda richiesta viene assegnata una versione di anteprima diversa rispetto alla prima, è possibile che l'esperienza non sia quella prevista. Inoltre, Bing può usare l'ID client per adattare i risultati Web alla cronologia di ricerca dell'ID client, fornendo un'esperienza più completa per l'utente.<br /><br /> Bing usa questa intestazione anche per migliorare le classifiche dei risultati analizzando l'attività generata da un ID client. I miglioramenti di pertinenza migliorano la qualità dei risultati forniti dalle API Bing consentendo, di conseguenza, percentuali di click-through più elevate per il consumer API.<br /><br /> **IMPORTANTE:** sebbene sia facoltativa, è opportuno considerare obbligatoria questa intestazione. Salvare in modo permanente l'ID client in più richieste per la stessa combinazione di dispositivo e utente finale consente 1) al consumer API di ottenere un'esperienza utente coerente e 2) percentuali di click-through più elevate migliorando la qualità dei risultati dalle API Bing.<br /><br /> Di seguito sono indicate le regole d'uso di base che si applicano a questa intestazione.<br /><ul><li>Ogni utente che usa l'applicazione nel dispositivo deve avere un ID client univoco generato da Bing.<br /><br/>Se non si include questa intestazione nella richiesta, Bing genera un ID e lo restituisce nell'intestazione della risposta X-MSEdge-ClientID. L'unica volta in cui NON è necessario includere questa intestazione in una richiesta è la prima volta l'utente usa l'app nel dispositivo.<br /><br/></li><li>**ATTENZIONE:** è necessario assicurarsi che questo ID client non sia collegabile ad alcuna informazione relativa agli account utente autenticati.</li><li>Usare l'ID client per ogni richiesta dell'API Bing che l'app effettua per questo utente nel dispositivo.<br /><br/></li><li>Salvare l'ID client in modo permanente. Per salvare in modo permanente l'ID in un'app browser, usare un cookie HTTP permanente per far sì che l'ID venga usato in tutte le sessioni. Non usare un cookie di sessione. Per altre app, ad esempio le app per dispositivi mobili, usare l'archiviazione permanente del dispositivo per salvare in modo permanente l'ID.<br /><br/>La volta successiva che l'utente usa l'app in questo dispositivo, ottiene l'ID client salvato in modo permanente.</li></ul><br /> **NOTA:** le risposte Bing possono includere o meno questa intestazione. Se la risposta include questa intestazione, acquisire l'ID client e usarlo per tutte le richieste Bing successive per l'utente in tale dispositivo.<br /><br /> **NOTA:** se si include X-MSEdge-ClientID, non includere i cookie nella richiesta. |
| <a name="clientip" />X-MSEdge-ClientIP                | Intestazione di richiesta facoltativa.<br /><br /> Indirizzo IPv4 o IPv6 del dispositivo client. L'indirizzo IP viene usato per individuare la posizione dell'utente. Bing usa le informazioni sulla posizione per determinare il comportamento di ricerca sicura.<br /><br /> **NOTA:** sebbene sia facoltativa, è consigliabile specificare sempre questa intestazione e l'intestazione X-Search-Location.<br /><br /> Non offuscare l'indirizzo, ad esempio, modificando l'ultimo ottetto su 0. L'offuscamento dei risultati degli indirizzi nella posizione in un punto non vicino alla posizione effettiva del dispositivo può generare risultati errati in Bing.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
| <a name="location" />X-Search-Location                | Intestazione di richiesta facoltativa.<br /><br /> Elenco delimitato da punto e virgola di coppie chiave/valore che descrivono la posizione geografica del client. Bing usa le informazioni sulla posizione per determinare il comportamento di ricerca sicura e per restituire il contenuto locale pertinente. Specificare la coppia chiave/valore come \<chiave\>:\<valore\>. Di seguito sono indicate le chiavi usate per specificare la posizione dell'utente.<br /><br /><ul><li>lat&mdash;Obbligatoria. Latitudine della posizione del client, in gradi. La latitudine deve essere maggiore o uguale a -90,0 e minore o uguale a + 90,0. I valori negativi indicano le latitudini meridionali e i valori positivi indicano le latitudini settentrionali.<br /><br /></li><li>long&mdash;Obbligatoria. Longitudine della posizione del client, in gradi. La longitudine deve essere maggiore o uguale a -180,0 e minore o uguale a +180,0. I valori negativi indicano le latitudini occidentali e i valori positivi indicano le latitudini orientali.<br /><br /></li><li>re&mdash;Obbligatoria. Raggio, in metri, che specifica la precisione orizzontale delle coordinate. Passare il valore restituito dal servizio che rileva la posizione del dispositivo. I valori tipici potrebbero essere 22 m per GPS/Wi-Fi, 380 m per la triangolazione dei ripetitori di rete e 18.000 m per la ricerca inversa degli indirizzi IP.<br /><br /></li><li>ts&mdash;Facoltativa. Timestamp UNIX UTC del momento in cui il client si trovava nella posizione. Il timestamp UNIX è il numero di secondi trascorsi dal 1° gennaio 1970.<br /><br /></li><li>head&mdash;Facoltativa. Intestazione o direzione di viaggio relative al client. Specificare la direzione di viaggio come gradi da 0 a 360, partendo in senso orario rispetto al vero nord. Specificare questa chiave solo se la chiave `sp` è diversa da zero.<br /><br /></li><li>sp&mdash;Facoltativa. Velocità orizzontale, in metri al secondo, con cui viaggia il dispositivo client.<br /><br /></li><li>alt&mdash;Facoltativa. Altitudine del dispositivo client, in metri.<br /><br /></li><li>are&mdash;Facoltativa. Raggio, in metri, che specifica la precisione verticale delle coordinate. Specificare questa chiave solo se si specifica la chiave `alt`.<br /><br /></li></ul> **NOTA:** sebbene molte chiavi siano facoltative, più informazioni vengono specificate, più accurati sono i risultati relativi alle posizioni.<br /><br /> **NOTA:** anche se è facoltativa, è opportuno specificare sempre la posizione geografica dell'utente. Specificare la posizione è particolarmente importante se l'indirizzo IP del client non riflette accuratamente la posizione fisica dell'utente, ad esempio, se il client usa una VPN. Per ottenere risultati ottimali, è necessario includere questa intestazione e l'intestazione X-MSEdge-ClientIP ma, è necessario almeno includere questa intestazione.                                                                                                                                                              |

> [!NOTE] 
> Si tenga presente che le condizioni per l'utilizzo richiedono la conformità con tutte le leggi applicabili, tra cui l'uso di queste intestazioni. Per esempio, in alcune giurisdizioni, come l'Europa, sono previsti requisiti per ottenere il consenso dell'utente prima di inserire dispositivi di tracciamento nei dispositivi dell'utente.


<a name="content-form-types" />

### <a name="content-form-types"></a>Tipi di formato per il contenuto

Ogni richiesta deve includere l'intestazione Content-Type. L'intestazione deve essere impostata su multipart/form-data; boundary=\<boundary string\>, dove \<boundary string\> è una stringa univoca opaca che identifica il limite dei dati di formato. Ad esempio, boundary=boundary_1234-abcd.


Se si invia a Ricerca visiva un URL o un token di immagine, la figura seguente illustra i dati di formato che è necessario includere nel corpo del POST. I dati di formato devono includere l'intestazione Content-Disposition e il relativo parametro `name` deve essere impostato su "knowledgeRequest". Per informazioni dettagliate sull'oggetto `imageInfo`, consultare la richiesta.


```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "url" : "https://contoso.com/2018/05/fashion/red.jpg"
    }
}

--boundary_1234-abcd--
```

Se si carica un'immagine locale, la figura seguente illustra i dati del modulo che è necessario includere nel corpo del POST. I dati dl modulo devono includere l'intestazione Content-Disposition. Il relativo parametro `name` deve essere impostato su "image" e il parametro `filename` può essere impostato su qualsiasi stringa. L'intestazione Content-Type può essere impostata su qualsiasi tipo MIME di immagine di uso comune. Il contenuto del modulo è il file binario dell'immagine. La dimensione massima delle immagini che è possibile caricare è 1 MB. La larghezza o l'altezza massima deve essere di 1.500 pixel o inferiore.


```
--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
Content-Type: image/jpeg

ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```

Di seguito viene illustrato come specificare l'area di interesse di un'immagine caricata.

```
--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "cropArea" : {
            "top" : 0.2,
            "left" : 0.3,
            "bottom" : 0.7,
            "right" : 0.6
        }
    }
}

--boundary_1234-abcd
Content-Disposition: form-data; name="image"; filename="image"
Content-Type: image/jpeg


ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...

--boundary_1234-abcd--
```



### <a name="example-request"></a>Richiesta di esempio

Di seguito viene illustrata una richiesta di informazioni dettagliate che passa un token di immagine e l'area di interesse. Il token di informazioni dettagliate viene ottenuto da una chiamata precedente a /images/search.


```  
POST https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch?mkt=en-us HTTP/1.1  
Content-Type: multipart/form-data; boundary=boundary_1234-abcd
Ocp-Apim-Subscription-Key: 123456789ABCDE  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com 

--boundary_1234-abcd
Content-Disposition: form-data; name="knowledgeRequest"

{
    "imageInfo" : {
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "cropArea" : {
            "top" : 0.1,
            "left" : 0.2,
            "bottom" : 0.7,
            "right" : 0.5
        }
    }
}

--boundary_1234-abcd--
```


## <a name="bing-visual-search-responses"></a>Risposte dell'API Ricerca visiva Bing

Se per l'immagine sono disponibili informazioni dettagliate, la risposta contiene uno o più oggetti `tags` che contengono tali informazioni. Il campo `image` contiene il token di informazioni dettagliate per l'immagine di input.

```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {...},
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

Il campo `tags` contiene un nome visualizzato e un elenco di azioni (informazioni dettagliate). Uno dei tag contiene un campo `displayName` che viene impostato su una stringa vuota. Tale tag contiene le informazioni dettagliate predefinite, ad esempio le pagine Web che includono l'immagine, le immagini visivamente simili e i luoghi di acquisto per gli elementi presenti nell'immagine. Poiché l'intera immagine è di particolare interesse, il tag di informazioni dettagliate predefinito non include rettangoli di selezione per le aree di interesse.


```json
{
  "_type" : "ImageKnowledge",
  "tags" : [
    {
      "displayName" : "",
      "actions" : [
        {...},
        {...},
        {...},
        {...}
      ]
    },
    {...},
    {...},
    {...},
    {...}
  ],
  "image" : {
    "imageInsightsToken" : "bcid_AF8C9CA409421B..."
  }
}
```

Per un elenco di informazioni dettagliate predefinite, vedere [Default insights](../default-insights-tag.md) (Informazioni dettagliate predefinite).



I tag restanti contengono altre informazioni dettagliate importanti che possono essere di interesse per l'utente. Se ad esempio l'immagine contiene, un tag può includere un'informazione TextResults, che contiene il testo riconosciuto. In alternativa, se Bing riconosce un'entità (persona, luogo o cosa) nell'immagine, un tag può identificare l'entità. Ricerca visiva restituisce anche un set eterogeneo di termini (tag) derivati dall'immagine di input. Tali tag consentono agli utenti di esplorare i concetti trovati nell'immagine. Se ad esempio l'immagine di input è di un atleta famoso, un tag potrebbe essere Sport, che contiene i collegamenti alle immagini di sportive.

Ogni tag include un nome visualizzato che è possibile usare per suddividere in categorie le informazioni dettagliate, un rettangolo di selezione che identifica l'area di interesse cui applicare le informazioni, le informazioni dettagliate stesse e un'anteprima dell'immagine. Se ad esempio l'immagine è quella di una persona che indossa una maglia sportiva, un tag può includere un rettangolo di selezione che delimita la maglia e include le informazioni dettagliate VisualSearch e ProductVisualSearch. Un altro tag potrebbe includere un'informazione ImageResults contenente un URL per una richiesta all'API /images/search per ottenere immagini tipicamente correlate oppure un URL d ricerca su Bing.com che indirizza l'utente ai risultati di ricerca delle immagini su Bing.com.

Tutti i tag diversi da quello di informazioni dettagliate predefinito includono rettangoli di selezione che identificano le aree di interesse nell'immagine. Se ad esempio l'immagine include più persone riconosciute, i tag possono includere rettangoli di selezione per ogni persona o se l'immagine contiene articoli di abbigliamento riconosciuti il tag può includere rettangoli di selezione per ogni articolo di abbigliamento riconosciuto. È possibile usare i rettangoli di selezione per creare aree sensibili dell'immagine. Se si fa clic su una di tali aree, vengono visualizzati dettagli sul contenuto dell'area stessa. Non è necessario includere aree sensibili in un'immagine per i rettangoli di selezione che identificano l'intera immagine.

### <a name="text-recognition"></a>Riconoscimento di testo

Se l'immagine contiene testo che il servizio riconosce, un tag contiene un'informazione TextResults (azione). L'oggetto `displayName` relativo alle informazioni dettagliate contiene il testo riconosciuto. 

```json
    {
        "image" : {
            "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23Text..."
        },
        "displayName" : "##TextRecognition",
        "boundingBox" : {
            "queryRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            },
            "displayRectangle" : {
                "topLeft" : {"x" : 0, "y" : 0},
                "topRight" : {"x" : 1, "y" : 0},
                "bottomRight" : {"x" : 1, "y" : 1},
                "bottomLeft" : {"x" : 0, "y" : 1}
            }
        },
        "actions" : [{
            "displayName" : "WALK BIKE ACROSS BRIDGE",
            "actionType" : "TextResults"
        }],
        "sources" : ["OCR"]
    }
```

Poiché il campo `displayName` del tag contiene ##TextRecognition, non usarlo come titolo di categoria nell'esperienza utente. La stessa condizione di applica a ogni nome visualizzato che inizia con ##. In alternativa, usare il nome visualizzato dell'azione.


Il riconoscimento di testo è in grado di riconoscere anche le informazioni di contatto nei biglietti da visita, ad esempio il numero di telefono e gli indirizzi di posta elettronica. Il rettangolo di selezione identifica la posizione delle informazioni di contatto sul biglietto. 

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.635, "y" : 0},
          "topRight" : {"x" : 0.77, "y" : 0},
          "bottomRight" : {"x" : 0.77, "y" : 0.4873333},
          "bottomLeft" : {"x" : 0.635, "y" : 0.4873333}
        }
      },
      "actions" : [
        {
          "url" : "tel:888%20555%201212",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.63, "y" : 0},
          "topRight" : {"x" : 0.866, "y" : 0},
          "bottomRight" : {"x" : 0.866, "y" : 0.5553334},
          "bottomLeft" : {"x" : 0.63, "y" : 0.5553334}
        }
      },
      "actions" : [
        {
          "url" : "mailto:someone@outlook.com",
          "actionType" : "Uri"
        }
      ],
      "sources" : ["OCR"]
    },
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse3.mm.bing.net\/th?q=%23%23TextRecognition..."
      },
      "displayName" : "##TextRecognition",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0, "y" : 0},
          "topRight" : {"x" : 1, "y" : 0},
          "bottomRight" : {"x" : 1, "y" : 1},
          "bottomLeft" : {"x" : 0, "y" : 1}
        }
      },
      "actions" : [
        {
          "displayName" : "CHARLENE WHITNEY Graphic Designer 888 555 1212 someone@outlook.com www.contoso.com",
          "actionType" : "TextResults"
        }
      ],
      "sources" : ["OCR"]
    }
```

Se l'immagine contiene un'entità riconosciuta, ad esempio una persona, un luogo o una cosa, un tag può includere un'informazione Entità. 

```json
    {
      "image" : {
        "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?q=Statue+of+Liberty..."
      },
      "displayName" : "Statue of Liberty",
      "boundingBox" : {
        "queryRectangle" : {
          "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
          "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
          "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
          "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
        },
        "displayRectangle" : {
          "topLeft" : {"x" : 0.40625, "y" : 0.1757813},
          "topRight" : {"x" : 0.6171875, "y" : 0.1757813},
          "bottomRight" : {"x" : 0.6171875, "y" : 0.3867188},
          "bottomLeft" : {"x" : 0.40625, "y" : 0.3867188}
        }
      },
      "actions" : [
        {
          "_type" : "ImageEntityAction",
          "webSearchUrl" : "https:\/\/www.bing.com\/search?q=Statue+of+Liberty",
          "displayName" : "Statue of Liberty",
          "actionType" : "Entity",
        }
      ]
    }
```

## <a name="see-also"></a>Vedere anche 

* [Informazioni sull'API Ricerca visiva Bing](../overview.md)
* [Esercitazione: Creare un'app Web a pagina singola - Ricerca visiva Bing](../tutorial-bing-visual-search-single-page-app.md)
