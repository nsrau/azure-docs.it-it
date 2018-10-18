---
title: Variabili HTTP per il motore regole della rete CDN di Azure | Microsoft Docs
description: Le variabili HTTP consentono di recuperare i metadati di richiesta e di risposta HTTP.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 39084fd8408a123e8152ad96fa92025fd04ed42b
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49092814"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Variabili HTTP per il motore regole della rete CDN di Azure
Le variabili HTTP forniscono gli strumenti per recuperare i metadati di richiesta e di risposta HTTP. Questi metadati possono essere quindi usati per modificare in modo dinamico una richiesta o una risposta. L'uso di variabili HTTP è limitato alle funzionalità del motore regole seguenti:

- [Cache-Key Rewrite](cdn-rules-engine-reference-features.md#cache-key-rewrite) (Riscrittura chiave cache)
- [Modify Client Request Header](cdn-rules-engine-reference-features.md#modify-client-request-header) (Modifica intestazione richiesta client)
- [Modify Client Response Header](cdn-rules-engine-reference-features.md#modify-client-response-header) (Modifica intestazione risposta client)
- [URL Redirect](cdn-rules-engine-reference-features.md#url-redirect) (Reindirizzamento URL)
- [URL Rewrite](cdn-rules-engine-reference-features.md#url-rewrite) (Riscrittura URL)

## <a name="definitions"></a>Definizioni
La tabella seguente descrive le variabili HTTP supportate. Quando i metadati di tipo GEO (ad esempio, il codice postale) non sono disponibili per una particolare richiesta, viene restituito un valore vuoto.


| NOME | Variabile | DESCRIZIONE | Valore di esempio |
| ---- | -------- | ----------- | ------------ |
| ASN (Requester) (ASN (richiedente)) | %{geo_asnum} | Indica il numero AS del richiedente. <br /><br />**Deprecata:** %{virt_dst_asnum}. <br />Questa variabile è stata deprecata a favore di %{geo_asnum}. Anche se una regola che usa questa variabile deprecata continuerà a funzionare, è necessario aggiornarla in modo da usare la nuova variabile. | AS15133 |
| City (Requester) (Città (richiedente)) | %{geo_city} | Indica la città del richiedente. | Los Angeles |
| Continent (Requester) (Continente (richiedente)) | %{geo_continent} | Indica il continente del richiedente tramite la relativa abbreviazione. <br />I valori validi sono: <br />AF: Africa<br />AS: Asia<br />EU: Europa<br />NA: America del Nord<br />OC: Oceania<br />SA: America del Sud<br /><br />**Deprecata:** %{virt_dst_continent}. <ber />Questa variabile è stata deprecata a favore di %{geo_continent}. <br />Anche se una regola che usa questa variabile deprecata continuerà a funzionare, è necessario aggiornarla in modo da usare la nuova variabile.| N/D |
| Cookie Value (Valore cookie) | %{cookie_Cookie} | Restituisce il valore corrispondente alla chiave del cookie identificata dal termine Cookie. | Esempio di utilizzo: <br />%{cookie__utma}<br /><br />Valore di esempio:<br />111662281.2.10.1222100123 |
| Country (Requester) (Paese (richiedente)) | %{geo_country} | Indica il paese di origine del richiedente tramite il relativo codice paese. <br />**Deprecata:** %{virt_dst_country}. <br /><br />Questa variabile è stata deprecata a favore di %{geo_country}. Anche se una regola che usa questa variabile deprecata continuerà a funzionare, è necessario aggiornarla in modo da usare la nuova variabile. | Stati Uniti |
| Designated Market Area (Requester) (Area di mercato designata (richiedente)) | %{geo_dma_code} |Indica il mercato del richiedente tramite il relativo codice area geografica. <br /><br />Questo campo è applicabile solo alle richieste provenienti dagli Stati Uniti.| 745 |
| HTTP Request Method (Metodo di richiesta HTTP) | %{request_method} | Indica il metodo di richiesta HTTP. | GET |
| Codice di stato HTTP | %{status} | Indica il codice di stato HTTP per la risposta. | 200 |
| IP Address (Requester) (Indirizzo IP (richiedente)) | %{virt_dst_addr} | Indica l'indirizzo IP del richiedente. | 192.168.1.1 |
| Latitude (Requester) (Latitudine (richiedente)) | %{geo_latitude} | Indica la latitudine del richiedente. | 34.0995 |
| Longitude (Requester) (Longitudine (richiedente)) | %{geo_longitude} | Indica la longitudine del richiedente. | -118.4143 |
| Metropolitan Statistical Area (Requester) (Area statistica metropolitana (richiedente)) | %{geo_metro_code} | Indica l'area metropolitana del richiedente. <br /><br />Questo campo è applicabile solo alle richieste provenienti dagli Stati Uniti.<br />| 745 |
| Port (Requester) (Porta (richiedente)) | %{virt_dst_port} | Indica la porta temporanea del richiedente. | 55885 |
| Postal Code (Requester) (Codice postale (richiedente)) | %{geo_postal_code} | Indica il codice postale del richiedente. | 90210 |
| Query String Found (Stringa di query trovata) | %{is_args} | Il valore per questa variabile varia a seconda se la richiesta contiene una stringa di query.<br /><br />- Stringa di query trovata: ?<br />- Nessuna stringa di query: NULL | ? |
| Query String Parameter Found (Parametro di stringa di query trovato) | %{is_amp} | Il valore per questa variabile varia a seconda se la richiesta contiene almeno un parametro di stringa di query.<br /><br />- Parametro trovato: &<br />- Nessun parametro: NULL | & |
| Query String Parameter Value (Valore del parametro di stringa di query) | %{arg_&lt;parametro&gt;} | Restituisce il valore corrispondente al parametro di stringa di query identificato dal termine &lt;parametro&gt;. | Esempio di utilizzo: <br />%{arg_language}<br /><br />Parametro di stringa della query di esempio: <br />?language=en<br /><br />Valore di esempio: en |
| Query String Value (Valore di stringa di query) | %{query_string} | Indica il valore di stringa dell'intera query definito nell'URL della richiesta. |key1=val1&key2=val2&key3=val3 |
| Referrer Domain (Dominio referrer) | %{referring_domain} | Indica il dominio definito nell'intestazione della richiesta Referer. | www.google.com |
| Region (Requester) (Regione (richiedente)) | %{geo_region} | Indica la regione del richiedente (ad esempio, stato o provincia) tramite la relativa abbreviazione alfanumerica. | CA |
| Request Header Value (Valore intestazione della richiesta) | %{http_RequestHeader} | Restituisce il valore corrispondente all'intestazione della richiesta identificata dal termine RequestHeader. <br /><br />Se il nome dell'intestazione della richiesta contiene un trattino (ad esempio, User-Agent), sostituirlo con un carattere di sottolineatura (ad esempio, User_Agent).| Esempio di utilizzo: %{http_Connection}<br /><br />Valore di esempio: Keep-Alive | 
| Request Host (Host richiesta) | %{host} | Indica l'host definito nell'URL della richiesta. | www.mydomain.com |
| Request Protocol (Protocollo richiesta) | %{request_protocol} | Indica il protocollo della richiesta. | HTTP/1.1 |
| Schema richiesta | %{scheme} | Indica lo schema della richiesta. |http |
| Request URI (Relative) (URI della richiesta (relativo)) | %{request_uri} | Indica il percorso relativo, compresa la stringa di query, definito nell'URI della richiesta. | /marketing/foo.js?loggedin=true |
| Request URI (Relative without query string) (URI della richiesta (relativo senza stringa di query)) | %{uri} | Indica il percorso relativo del contenuto richiesto. <br /><br/>Informazioni chiave:<br />- Questo percorso relativo esclude la stringa di query.<br />- Questo percorso relativo riflette le riscritture URL. Un URL verrà riscritto nelle condizioni seguenti:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Funzionalità di riscrittura URL: questa funzionalità riscrive il percorso relativo definito nell'URI della richiesta.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL CNAME perimetrale: questo tipo di richiesta viene riscritto nell'URL della rete CDN corrispondente. |/800001/corigin/rewrittendir/foo.js |
| URI della richiesta | %{request} | Descrive la richiesta. <br />Sintassi: &lt;metodo HTTP&gt; &lt;percorso relativo&gt; &lt;protocollo HTTP&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Response Header Value (Valore dell'intestazione di risposta) | %{resp_&lt;ResponseHeader&gt;} | Restituisce il valore corrispondente all'intestazione della risposta identificata dal termine &lt;ResponseHeader&gt;. <br /><br />Se il nome dell'intestazione della risposta contiene un trattino (ad esempio, User-Agent), sostituirlo con un carattere di sottolineatura (ad esempio, User_Agent). | Esempio di utilizzo: %{resp_Content_Length}<br /><br />Valore di esempio: 100 |

## <a name="usage"></a>Uso
La tabella seguente descrive la sintassi corretta per specificare una variabile HTTP.


| Sintassi | Esempio | DESCRIZIONE |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Usare questa sintassi per ottenere il valore intero corrispondente all'oggetto &lt;HTTPVariable&gt; specificato. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | Usare questa sintassi per impostare l'opzione maiuscole/minuscole per il valore intero corrispondente all'oggetto &lt;HTTPVariableDelimiter&gt; specificato. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | Usare un'espressione regolare per &lt;HTTPVariableDelimiterExpression&gt; per sostituire, eliminare o modificare il valore di una variabile HTTP. |

I nomi delle variabili HTTP supportano solo caratteri alfabetici e caratteri di sottolineatura. Convertire i caratteri non supportati in caratteri di sottolineatura.

## <a name="delimiter-reference"></a>Riferimento per i delimitatori
È possibile specificare un delimitatore dopo una variabile HTTP per ottenere uno degli effetti seguenti:

- Trasformare il valore associato alla variabile.

     Esempio: convertire l'intero valore in caratteri minuscoli.

- Eliminare il valore associato alla variabile.

- Modificare il valore associato alla variabile.

     Esempio: usare espressioni regolari per modificare il valore associato alla variabile HTTP.

I delimitatori sono descritti nella tabella seguente.

| Delimitatore | DESCRIZIONE |
| --------- | ----------- |
| := | Indica che un valore predefinito verrà assegnato alla variabile quando è: <br />- mancante <br />- impostata su NULL. |
| :+ | Indica che un valore predefinito verrà assegnato alla variabile quando a quest'ultima è stato assegnato un valore. |
| : | Indica che una sottostringa del valore assegnato alla variabile verrà espansa. |
| # | Indica che il criterio specificato dopo questo delimitatore deve essere eliminato quando viene trovato all'inizio del valore associato alla variabile. |
| % | Indica che il criterio specificato dopo questo delimitatore deve essere eliminato quando viene trovato alla fine del valore associato alla variabile. <br />Questa definizione è applicabile solo quando il simbolo % viene usato come delimitatore. |
| / | Delimita una variabile HTTP o un criterio. |
| // | Trovare e sostituire tutte le istanze del criterio specificato. |
| /= | Trovare, copiare e riscrivere tutte le occorrenze del criterio specificato. |
| , | Convertire il valore associato alla variabile HTTP in caratteri minuscoli. |
| ^ | Convertire il valore associato alla variabile HTTP in caratteri maiuscoli. |
| ,, | Convertire tutte le istanze del carattere specificato nel valore associato alla variabile HTTP in caratteri minuscoli. |
| ^^ | Convertire tutte le istanze del carattere specificato nel valore associato alla variabile HTTP in caratteri maiuscoli. |

## <a name="exceptions"></a>Eccezioni
La tabella seguente descrive le circostanze in cui il testo specificato non è considerato una variabile HTTP.

| Condizione | DESCRIZIONE | Esempio |
| --------- | ----------- | --------|
| Simbolo % con escape | Il simbolo di percentuale può essere preceduto da un carattere di escape usando una barra rovesciata. <br />Il valore di esempio a destra verrà considerato un valore letterale e non una variabile HTTP.| \%{host} |
| Variabili sconosciute | Per le variabili sconosciute viene sempre restituita una stringa vuota. | %{unknownvariable} |
| Caratteri o sintassi non valida | Le variabili che contengono caratteri o sintassi non valida vengono trattate come valori letterali. <br /><br />Esempio 1: il valore specificato contiene un carattere non valido (ad esempio -). <br /><br />Esempio 2: il valore specificato contiene un doppio set di parentesi graffe. <br /><br />Esempio 3: nel valore specificato manca una parentesi graffa di chiusura.<br /> | Esempio 1: %{resp_user-agent} <br /><br />Esempio 2: %{{host}} <br /><br />Esempio 3: %{host |
| Nome variabile mancante | Quando una variabile non è specificata, viene sempre restituito un valore NULL. | %{} |
| Caratteri finali | I caratteri di chiusura di una variabile vengono considerati valori letterali. <br />Il valore di esempio a destra contiene una parentesi graffa di chiusura che verrà considerata un valore letterale. | %{host}} |

## <a name="setting-default-header-values"></a>Impostazione di valori di intestazione predefiniti
Un valore predefinito può essere assegnato a un'intestazione quando risponde a una delle condizioni seguenti:
- Mancante/non impostata
- Impostata su NULL.

La tabella seguente descrive come definire un valore predefinito.

| Condizione | Sintassi | Esempio | DESCRIZIONE |
| --------- | ------ | --------| ----------- |
| Impostare un'intestazione su un valore predefinito quando risponde a una delle condizioni seguenti: <br /><br />- Intestazione mancante <br /><br />- Valore dell'intestazione impostato su NULL.| %{Variable:=Value} | %{http_referer:=unspecified} | L'intestazione Referer verrà impostata solo su *unspecified* quando è mancante o impostata su NULL. Non verrà eseguita alcuna azione se è stata impostata. |
| Impostare un'intestazione su un valore predefinito quando è mancante. | %{Variable=Value} | %{http_referer=unspecified} | L'intestazione Referer verrà impostata solo su *unspecified* quando è mancante. Non verrà eseguita alcuna azione se è stata impostata. |
| Impostare l'intestazione su un valore predefinito quando non risponde a una delle condizioni seguenti: <br /><br />- mancante<br /><br /> - impostata su NULL. | %{Variable:+Value} | %{http_referer:+unspecified} | L'intestazione Referer verrà impostata solo su *unspecified* quando è stato assegnato un valore. Non verrà eseguita alcuna azione se è mancante o impostata su NULL. |

## <a name="manipulating-variables"></a>Modifica delle variabili
Le variabili possono essere modificate nei modi seguenti:

- Espansione di sottostringhe
- Rimozione di criteri

### <a name="substring-expansion"></a>Espansione di sottostringhe
Per impostazione predefinita, una variabile si espanderà fino al valore completo. Usare la sintassi seguente per espandere solo una sottostringa del valore della variabile.

`%<Variable>:<Offset>:<Length>}`

Informazioni chiave:

- Il valore assegnato al termine Offset determina il carattere iniziale della sottostringa:

     - Positivo: il carattere iniziale della sottostringa viene calcolato dal primo carattere nella stringa.
     - Zero: il carattere iniziale della sottostringa è il primo carattere nella stringa.
     - Negativo: il carattere iniziale della sottostringa viene calcolato dall'ultimo carattere nella stringa.

- La lunghezza della sottostringa è determinata dal termine *Length*:

     - Omesso: l'omissione del termine Length consente alla sottostringa di includere tutti i caratteri tra il carattere iniziale e la fine della stringa.
     - Positivo: determina la lunghezza della sottostringa dal carattere iniziale verso destra.
     - Negativo: determina la lunghezza della sottostringa dal carattere iniziale verso sinistra.

#### <a name="example"></a>Esempio:

L'esempio seguente si basa sull'URL della richiesta di esempio seguente:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

La stringa seguente illustra vari metodi per la modifica delle variabili:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

In base all'URL della richiesta di esempio, la modifica della variabile precedente produrrà il valore seguente:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### <a name="pattern-removal"></a>Rimozione di criteri
Il testo corrispondente a un criterio specifico può essere rimosso dall'inizio o dalla fine del valore di una variabile.

| Sintassi | Azione |
| ------ | ------ |
| %{Variable#Pattern} | Rimuovere il testo quando viene trovato il criterio specificato all'inizio del valore di una variabile. |
| %{Variable%Pattern} | Rimuovere il testo quando viene trovato il criterio specificato alla fine del valore di una variabile. |

#### <a name="example"></a>Esempio:

In questo scenario di esempio la variabile *request_uri* è impostata su:

`/800001/myorigin/marketing/product.html?language=en-US`

La tabella seguente illustra il funzionamento di questa sintassi.

| Sintassi di esempio | Risultati |
| ------------- | ------- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Dal momento che la variabile inizia con il criterio, è stato sostituito. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Dal momento che la variabile non termina con il criterio, non è stata apportata alcuna modifica.|

### <a name="find-and-replace"></a>Ricerca e sostituzione
La sintassi di ricerca e sostituzione è descritta nella tabella seguente.

| Sintassi | Azione |
| ------ | ------ |
| %{Variable/Find/Replace} | Trovare e sostituire la prima occorrenza del criterio specificato. |
| %{Variable//Find/Replace} | Trovare e sostituire tutte le occorrenze del criterio specificato. |
| %{Variable^} |Convertire l'intero valore in caratteri maiuscoli. |
| %{Variable^Find} | Convertire la prima occorrenza del criterio specificato in caratteri maiuscoli. |
| %{Variable,} | Convertire l'intero valore in caratteri minuscoli. |
| %{Variable,Find} | Convertire la prima occorrenza del criterio specificato in caratteri minuscoli. |

### <a name="find-and-rewrite"></a>Ricerca e riscrittura
Come variante della ricerca e della sostituzione, usare il testo corrispondente al criterio specificato durante la riscrittura. La sintassi di ricerca e riscrittura è descritta nella tabella seguente.

| Sintassi | Azione |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Trovare, copiare e riscrivere tutte le occorrenze del criterio specificato. |
| %{Variable/^Find/Rewrite} | Trovare, copiare e riscrivere il criterio specificato quando si trova all'inizio della variabile. |
| %{Variable/$Find/Rewrite} | Trovare, copiare e riscrivere il criterio specificato quando si trova alla fine della variabile. |
| %{Variable/Find} | Trovare ed eliminare tutte le occorrenze del criterio specificato. |

Informazioni chiave:

- Espandere il testo corrispondente al criterio specificato specificando un simbolo di dollaro seguito da un numero intero (ad esempio, $1).

- È possibile specificare più criteri. L'ordine in cui viene specificato il criterio determina il numero intero che verrà assegnato. Nell'esempio seguente il primo criterio corrisponde a "www.", il secondo corrisponde al dominio di secondo livello e il terzo corrisponde al dominio di livello superiore:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- Il valore riscritto può essere costituito da qualsiasi combinazione di testo e questi segnaposto.

    Nell'esempio precedente il nome host viene riscritto in `cdn.$2.$3:80` (ad esempio, cdn.mydomain.com:80).

- L'opzione maiuscole/minuscole del segnaposto di un criterio (ad esempio, $1) può essere modificata tramite i flag seguenti:
     - U: scrivere in lettere maiuscole il valore espanso.

         Sintassi di esempio:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: scrivere in lettere minuscole il valore espanso.

         Sintassi di esempio:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- È necessario specificare un operatore prima del criterio. L'operatore specificato determina il comportamento di acquisizione del criterio:

     - `=`: indica che tutte le occorrenze del criterio specificato devono essere acquisite e riscritte.
     - `^`: indica che verrà acquisito solo il testo che inizia con il criterio specificato.
     - `$`: indica che verrà acquisito solo il testo che termina con il criterio specificato.
 
- Se si omette il valore */Rewrite*, il testo corrispondente al criterio viene eliminato.


