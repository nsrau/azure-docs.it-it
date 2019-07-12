---
title: Variabili HTTP per il motore regole della rete CDN di Azure | Microsoft Docs
description: Le variabili HTTP consentono di recuperare i metadati di richiesta e di risposta HTTP.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus
ms.openlocfilehash: 53ad0c516547e17801bd57c2fd6b0d1704383797
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593824"
---
# <a name="http-variables-for-azure-cdn-rules-engine"></a>Variabili HTTP per il motore regole della rete CDN di Azure
Le variabili HTTP forniscono gli strumenti per recuperare i metadati di richiesta e di risposta HTTP. Questi metadati possono essere quindi usati per modificare in modo dinamico una richiesta o una risposta. L'uso di variabili HTTP è limitato alle funzionalità del motore regole seguenti:

- [Cache-Key Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite) (Riscrittura chiave cache)
- [Modify Client Request Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header) (Modifica intestazione richiesta client)
- [Modify Client Response Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header) (Modifica intestazione risposta client)
- [URL Redirect](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect) (Reindirizzamento URL)
- [URL Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite) (Riscrittura URL)

## <a name="definitions"></a>Definizioni
La tabella seguente descrive le variabili HTTP supportate. Quando i metadati di tipo GEO (ad esempio, il codice postale) non sono disponibili per una particolare richiesta, viene restituito un valore vuoto.


| Name | Variabile | Descrizione | Valore di esempio |
| ---- | -------- | ----------- | ------------ |
| ASN (Requester) (ASN (richiedente)) | %{geo_asnum} | Indica il numero AS del richiedente. <br /><br />**Deprecata:** %{virt_dst_asnum}. <br />Questa variabile è stata deprecata a favore di %{geo_asnum}. Anche se una regola che usa questa variabile deprecata continuerà a funzionare, è necessario aggiornarla in modo da usare la nuova variabile. | AS15133 |
| City (Requester) (Città (richiedente)) | %{geo_city} | Indica la città del richiedente. | Los Angeles |
| Continent (Requester) (Continente (richiedente)) | %{geo_continent} | Indica il continente del richiedente tramite la relativa abbreviazione. <br />I valori validi sono: <br />AF: Africa<br />AS: Asia<br />UE: Europa<br />NA: America del Nord<br />OC: Oceania<br />ASSOCIAZIONE DI SICUREZZA: America del Sud<br /><br />**Deprecata:** %{virt_dst_continent}. <br />Questa variabile è stata deprecata a favore di % {geo_continent}. <br />Anche se una regola che usa questa variabile deprecata continuerà a funzionare, è necessario aggiornarla in modo da usare la nuova variabile.| N/D |
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
| Query String Found (Stringa di query trovata) | %{is_args} | Il valore per questa variabile varia a seconda se la richiesta contiene una stringa di query.<br /><br />- Stringa di query trovata: ?<br />-Nessuna stringa di Query: NULL | ? |
| Query String Parameter Found (Parametro di stringa di query trovato) | %{is_amp} | Il valore per questa variabile varia a seconda se la richiesta contiene almeno un parametro di stringa di query.<br /><br />- Parametro trovato: &<br />-Nessun parametro: NULL | & |
| Query String Parameter Value (Valore del parametro di stringa di query) | %{arg_&lt;parametro&gt;} | Restituisce il valore corrispondente al parametro di stringa di query identificato dal termine &lt;parametro&gt;. | Esempio di utilizzo: <br />%{arg_language}<br /><br />Parametro di stringa della query di esempio: <br />?language=en<br /><br />Valore di esempio: en |
| Query String Value (Valore di stringa di query) | %{query_string} | Indica il valore di stringa dell'intera query definito nell'URL della richiesta. |key1=val1&key2=val2&key3=val3 |
| Referrer Domain (Dominio referrer) | %{referring_domain} | Indica il dominio definito nell'intestazione della richiesta Referrer. | <www.google.com> |
| Region (Requester) (Regione (richiedente)) | %{geo_region} | Indica la regione del richiedente (ad esempio, stato o provincia) tramite la relativa abbreviazione alfanumerica. | CA |
| Request Header Value (Valore intestazione della richiesta) | %{http_RequestHeader} | Restituisce il valore corrispondente all'intestazione della richiesta identificata dal termine RequestHeader. <br /><br />Se il nome dell'intestazione della richiesta contiene un trattino (ad esempio, User-Agent), sostituirlo con un carattere di sottolineatura (ad esempio, User_Agent).| Esempio di utilizzo: %{http_Connection}<br /><br />Valore di esempio: Keep-Alive | 
| Request Host (Host richiesta) | %{host} | Indica l'host definito nell'URL della richiesta. | <www.mydomain.com> |
| Request Protocol (Protocollo richiesta) | %{request_protocol} | Indica il protocollo della richiesta. | HTTP/1.1 |
| Schema richiesta | %{scheme} | Indica lo schema della richiesta. |http |
| Request URI (Relative) (URI della richiesta (relativo)) | %{request_uri} | Indica il percorso relativo, compresa la stringa di query, definito nell'URI della richiesta. | /marketing/foo.js?loggedin=true |
| Request URI (Relative without query string) (URI della richiesta (relativo senza stringa di query)) | %{uri} | Indica il percorso relativo del contenuto richiesto. <br /><br/>Informazioni chiave:<br />- Questo percorso relativo esclude la stringa di query.<br />- Questo percorso relativo riflette le riscritture URL. Un URL verrà riscritto nelle condizioni seguenti:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-Funzionalità di riscrittura URL: Questa funzionalità riscrive il relativo percorso definito nell'URI della richiesta.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;-URL CNAME perimetrale: Questo tipo di richiesta viene riscritto per l'URL della rete CDN corrispondente. |/800001/corigin/rewrittendir/foo.js |
| URI della richiesta | %{request} | Descrive la richiesta. <br />Sintassi: &lt;Metodo HTTP&gt; &lt;percorso relativo&gt; &lt;protocollo HTTP&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Response Header Value (Valore dell'intestazione di risposta) | %{resp_&lt;ResponseHeader&gt;} | Restituisce il valore corrispondente all'intestazione della risposta identificata dal termine &lt;ResponseHeader&gt;. <br /><br />Se il nome dell'intestazione della risposta contiene un trattino (ad esempio, User-Agent), sostituirlo con un carattere di sottolineatura (ad esempio, User_Agent). | Esempio di utilizzo: %{resp_Content_Length}<br /><br />Valore di esempio: 100 |

## <a name="usage"></a>Utilizzo
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

     Esempio: Convertire l'intero valore in caratteri minuscoli.

- Eliminare il valore associato alla variabile.

- Modificare il valore associato alla variabile.

     Esempio: Usare espressioni regolari per modificare il valore associato alla variabile HTTP.

I delimitatori sono descritti nella tabella seguente.

| Delimitatore | Descrizione |
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

| Condizione | Descrizione | Esempio |
| --------- | ----------- | --------|
| Simbolo % con escape | Il simbolo di percentuale può essere preceduto da un carattere di escape usando una barra rovesciata. <br />Il valore di esempio a destra verrà considerato un valore letterale e non una variabile HTTP.| \%{host} |
| Unknown variables | An empty string is always returned for unknown variables. | %{unknown_variable} |
| Invalid characters or syntax | Variables that contain invalid characters or syntax are treated as literal values. <br /><br />Example #1: The specified value contains an invalid character (for example, -). <br /><br />Example #2: The specified value contains a double set of curly braces. <br /><br />Example #3: The specified value is missing a closing curly brace.<br /> | Example #1: %{resp_user-agent} <br /><br />Example #2: %{{host}} <br /><br />Example #3: %{host |
| Missing variable name | A NULL value is always returned when a variable is not specified. | %{} |
| Trailing characters | Characters that trail a variable are treated as literal values. <br />The sample value to the right contains a trailing curly brace that will be treated as a literal value. | %{host}} |

## Setting default header values
A default value can be assigned to a header when it meets any of the following conditions:
- Missing/unset
- Set to NULL.

The following table describes how to define a default value.

| Condition | Syntax | Example | Description |
| --------- | ------ | --------| ----------- |
| Set a header to a default value when it meets any of the following conditions: <br /><br />- Missing Header <br /><br />- Header value is set to NULL.| %{Variable:=Value} | %{http_referrer:=unspecified} | The Referrer header will only be set to *unspecified* when it is either missing or set to NULL. No action will take place if it has been set. |
| Set a header to a default value when it is missing. | %{Variable=Value} | %{http_referrer=unspecified} | The Referrer header will only be set to *unspecified* when it is missing. No action will take place if it has been set. |
| Set the header to a default value when it does not meet any of the following conditions: <br /><br />- Missing<br /><br /> - Set to NULL. | %{Variable:+Value} | %{http_referrer:+unspecified} | The Referrer header will only be set to *unspecified* when a value has been assigned to it. No action will take place if it is either missing or set to NULL. |

## Manipulating variables
Variables can be manipulated in the following ways:

- Expanding substrings
- Removing patterns

### Substring expansion
By default, a variable will expand to its full value. Use the following syntax to only expand a substring of the variable's value.

`%<Variable>:<Offset>:<Length>}`

Key information:

- The value assigned to the Offset term determines the starting character of the substring:

     - Positive: The starting character of the substring is calculated from the first character in the string.
     - Zero: The starting character of the substring is the first character in the string.
     - Negative: The starting character of the substring is calculated from the last character in the string.

- The length of the substring is determined by the *Length* term:

     - Omitted: Omitting the Length term allows the substring to include all characters between the starting character and the end of the string.
     - Positive: Determines the length of the substring from the starting character to the right.
     - Negative: Determines the length of the substring from the starting character to the left.

#### Example:

The following example relies on the following sample request URL:

https:\//cdn.mydomain.com/folder/marketing/myconsultant/proposal.html

The following string demonstrates various methods for manipulating variables:

https:\//www%{http_host:3}/mobile/%{request_uri:7:10}/%{request_uri:-5:-8}.htm

Based on the sample request URL, the above variable manipulation will produce the following value:

https:\//www.mydomain.com/mobile/marketing/proposal.htm


### Pattern removal
Text that matches a specific pattern can be removed from either the beginning or the end of a variable's value.

| Syntax | Action |
| ------ | ------ |
| %{Variable#Pattern} | Remove text when the specified pattern is found at the beginning of a variable's value. |
| %{Variable%Pattern} | Remove text when the specified pattern is found at the end of a variable's value. |

#### Example:

In this sample scenario, the *request_uri* variable is set to:

`/800001/myorigin/marketing/product.html?language=en-US`

The following table demonstrates how this syntax works.

| Sample syntax | Results | |
| ------------- | ------- | --- |
| %{request_uri#/800001}/customerorigin | /customerorigin/myorigin/marketing/product.html?language=en-US | Because the variable starts with the pattern, it was replaced. |
| %{request_uri%html}htm | /800001/myorigin/marketing/product.html?language=en-US | Because the variable doesn't end with the pattern, there was no change.|

### Find and replace
The find and replace syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/Find/Replace} | Find and replace first occurrence of the specified pattern. |
| %{Variable//Find/Replace} | Find and replace all occurrences of the specified pattern. |
| %{Variable^} |Convert the entire value to uppercase. |
| %{Variable^Find} | Convert the first occurrence of the specified pattern to uppercase. |
| %{Variable,} | Convert the entire value to lowercase. |
| %{Variable,Find} | Convert the first occurrence of the specified pattern to lowercase. |

### Find and rewrite
For a variation on find and replace, use the text that matches the specified pattern when rewriting it. The find and rewrite syntax is described in the following table.

| Syntax | Action |
| ------ | ------ |
| %{Variable/=Find/Rewrite} | Find, copy, and rewrite all occurrences of the specified pattern. |
| %{Variable/^Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the start of the variable. |
| %{Variable/$Find/Rewrite} | Find, copy, and rewrite the specified pattern when it occurs at the end of the variable. |
| %{Variable/Find} | Find and delete all occurrences of the specified pattern. |

Key information:

- Expand text that matches the specified pattern by specifying a dollar sign followed by a whole integer (for example, $1).

- Multiple patterns can be specified. The order in which the pattern is specified determines the integer that will be assigned to it. In the following example, the first pattern matches "www.," the second pattern matches the second-level domain, and the third pattern matches the top-level domain:

    `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80}`

- The rewritten value can consist of any combination of text and these placeholders.

    In the previous example, the hostname is rewritten to `cdn.$2.$3:80` (for example, cdn.mydomain.com:80).

- The case of a pattern placeholder (for example, $1) can be modified through the following flags:
     - U: Uppercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Lowercase the expanded value.

         Sample syntax:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- An operator must be specified before the pattern. The specified operator determines the pattern-capturing behavior:

     - `=`: Indicates that all occurrences of the specified pattern must be captured and rewritten.
     - `^`: Indicates that only text that starts with the specified pattern will be captured.
     - `$`: Indicates that only text that ends with the specified pattern will be capture.
 
- If you omit the */Rewrite* value, the text that matches the pattern is deleted.

\`{host}le: HTTP variables for Azure CDN rules engine | Microsoft Docs
description: HTTP variables allow you to retrieve HTTP request and response metadata.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''

ms.assetid: 
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2018
ms.author: magattus


---
# HTTP variables for Azure CDN rules engine
HTTP variables provide the means through which you can retrieve HTTP request and response metadata. This metadata can then be used to dynamically alter a request or a response. The use of HTTP variables is restricted to the following rules engine features:

- [Cache-Key Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#cache-key-rewrite)
- [Modify Client Request Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-request-header)
- [Modify Client Response Header](cdn-verizon-premium-rules-engine-reference-features.md#modify-client-response-header)
- [URL Redirect](cdn-verizon-premium-rules-engine-reference-features.md#url-redirect)
- [URL Rewrite](cdn-verizon-premium-rules-engine-reference-features.md#url-rewrite)

## Definitions
The following table describes the supported HTTP variables. A blank value is returned when GEO metadata (for example, postal code) is unavailable for a particular request.


| Name | Variable | Description | Sample value |
| ---- | -------- | ----------- | ------------ |
| ASN (Requester) | %{geo_asnum} | Indicates the requester's AS number. <br /><br />**Deprecated:** %{virt_dst_asnum}. <br />This variable has been deprecated in favor of %{geo_asnum}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | AS15133 |
| City (Requester) | %{geo_city} | Indicates the requester's city. | Los Angeles |
| Continent (Requester) | %{geo_continent} | Indicates the requester's continent through its abbreviation. <br />Valid values are: <br />AF: Africa<br />AS: Asia<br />EU: Europe<br />NA: North America<br />OC: Oceania<br />SA: South America<br /><br />**Deprecated:** %{virt_dst_continent}. <br />This variable has been deprecated in favor of %{geo_continent}. <br />Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable.| N/A |
| Cookie Value | %{cookie_Cookie} | Returns the value corresponding to the cookie key identified by the Cookie term. | Sample Usage: <br />%{cookie__utma}<br /><br />Sample Value:<br />111662281.2.10.1222100123 |
| Country (Requester) | %{geo_country} | Indicates the requester's country of origin through its country code. <br />**Deprecated:** %{virt_dst_country}. <br /><br />This variable has been deprecated in favor of %{geo_country}. Although a rule that uses this deprecated variable will continue to work, you should update it to use the new variable. | US |
| Designated Market Area (Requester) | %{geo_dma_code} |Indicates the requester's media market by its region code. <br /><br />This field is only applicable to requests that originate from the United States.| 745 |
| HTTP Request Method | %{request_method} | Indicates the HTTP request method. | GET |
| HTTP Status Code | %{status} | Indicates the HTTP status code for the response. | 200 |
| IP Address (Requester) | %{virt_dst_addr} | Indicates the requester's IP address. | 192.168.1.1 |
| Latitude (Requester) | %{geo_latitude} | Indicates the requester's latitude. | 34.0995 |
| Longitude (Requester) | %{geo_longitude} | Indicates the requester's longitude. | -118.4143 |
| Metropolitan Statistical Area (Requester) | %{geo_metro_code} | Indicates the requester's metropolitan area. <br /><br />This field is only applicable to requests that originate from the United States.<br />| 745 |
| Port (Requester) | %{virt_dst_port} | Indicates the requester's ephemeral port. | 55885 |
| Postal Code (Requester) | %{geo_postal_code} | Indicates the requester's postal code. | 90210 |
| Query String Found | %{is_args} | The value for this variable varies according to whether the request contains a query string.<br /><br />- Query String Found: ?<br />- No Query String: NULL | ? |
| Query String Parameter Found | %{is_amp} | The value for this variable varies according to whether the request contains at least one query string parameter.<br /><br />- Parameter Found: &<br />- No Parameters: NULL | & |
| Query String Parameter Value | %{arg_&lt;parameter&gt;} | Returns the value corresponding to the query string parameter identified by the &lt;parameter&gt; term. | Sample Usage: <br />%{arg_language}<br /><br />Sample Query String Parameter: <br />?language=en<br /><br />Sample Value: en |
| Query String Value | %{query_string} | Indicates the entire query string value defined in the request URL. |key1=val1&key2=val2&key3=val3 |
| Referrer Domain | %{referring_domain} | Indicates the domain defined in the Referrer request header. | <www.google.com> |
| Region (Requester) | %{geo_region} | Indicates the requester's region (for example, state or province) through its alphanumeric abbreviation. | CA |
| Request Header Value | %{http_RequestHeader} | Returns the value corresponding to the request header identified by the RequestHeader term. <br /><br />If the name of the request header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent).| Sample Usage: %{http_Connection}<br /><br />Sample Value: Keep-Alive | 
| Request Host | %{host} | Indicates the host defined in the request URL. | <www.mydomain.com> |
| Request Protocol | %{request_protocol} | Indicates the request protocol. | HTTP/1.1 |
| Request Scheme | %{scheme} | Indicates the request scheme. |http |
| Request URI (Relative) | %{request_uri} | Indicates the relative path, including the query string, defined in the request URI. | /marketing/foo.js?loggedin=true |
| Request URI (Relative without query string) | %{uri} | Indicates the relative path to the requested content. <br /><br/>Key information:<br />- This relative path excludes the query string.<br />- This relative path reflects URL rewrites. A URL will be rewritten under the following conditions:<br />  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- URL Rewrite Feature: This feature rewrites the relative path defined in the request URI.<br />    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;- Edge CNAME URL: This type of request is rewritten to the corresponding CDN URL. |/800001/corigin/rewrittendir/foo.js |
| Request URI | %{request} | Describes the request. <br />Syntax: &lt;HTTP method&gt; &lt;relative path&gt; &lt;HTTP protocol&gt; | GET /marketing/foo.js?loggedin=true HTTP/1.1 |
| Response Header Value | %{resp_&lt;ResponseHeader&gt;} | Returns the value corresponding to the response header identified by the &lt;ResponseHeader&gt; term. <br /><br />If the name of the response header contains a dash (for example, User-Agent), replace it with an underscore (for example, User_Agent). | Sample Usage: %{resp_Content_Length}<br /><br />Sample Value: 100 |

## Usage
The following table describes the proper syntax for specifying an HTTP variable.


| Syntax | Example | Description |
| ------ | -------- | ---------- |
| %{&lt;HTTPVariable&gt;} | %{host} | Use this syntax to get the entire value corresponding to the specified &lt;HTTPVariable&gt;. |
| %{&lt;HTTPVariableDelimiter&gt;} | %{host,} | Use this syntax to set the case for the entire value corresponding to the specified  &lt;HTTPVariableDelimiter&gt;. |
| %{&lt;HTTPVariableDelimiterExpression&gt;} | %{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$2.$3:80} | Use a regular expression for &lt;HTTPVariableDelimiterExpression&gt; to replace, delete, or manipulate an HTTP variable's value. |

HTTP variable names only support alphabetic characters and underscores. Convert unsupported characters to underscores.

## Delimiter reference
A delimiter can be specified after an HTTP variable to achieve any of the following effects:

- Transform the value associated with the variable.

     Example: Convert the entire value to lowercase.

- Delete the value associated with the variable.

- Manipulate the value associated with the variable.

     Example: Use regular expressions to change the value associated with the HTTP variable.

The delimiters are described in the following table.

| Delimiter | Description |
| --------- | ----------- |
| := | Indicates that a default value will be assigned to the variable when it is either: <br />- Missing <br />- Set to NULL. |
| :+ | Indicates that a default value will be assigned to the variable when a value has been assigned to it. |
| : | Indicates that a substring of the value assigned to the variable will be expanded. |
| # | Indicates that the pattern specified after this delimiter should be deleted when it is found at the beginning of the value associated with the variable. |
| % | Indicates that the pattern specified after this delimiter should be deleted when it is found at the end of the value associated with the variable. <br />This definition is only applicable when the % symbol is used as a delimiter. |
| / | Delimits an HTTP variable or a pattern. |
| // | Find and replace all instances of the specified pattern. |
| /= | Find, copy, and rewrite all occurrences of the specified pattern. |
| , | Convert the value associated with the HTTP variable to lowercase. |
| ^ | Convert the value associated with the HTTP variable to uppercase. |
| ,, | Convert all instances of the specified character in the value associated with the HTTP variable to lowercase. |
| ^^ | Convert all instances of the specified character in the value associated with the HTTP variable to uppercase. |

## Exceptions
The following table describes circumstances under which the specified text isn't treated as an HTTP variable.

| Condition | Description | Example |
| --------- | ----------- | --------|
| Escaping % symbol | The percentage symbol can be escaped through the use of a backslash. <br />The sample value to the right will be treated as a literal value and not as an HTTP variable.| \%{host} |
| Variabili sconosciute | Per le variabili sconosciute viene sempre restituita una stringa vuota. | %{unknown_variable} |
| Caratteri o sintassi non valida | Le variabili che contengono caratteri o sintassi non valida vengono trattate come valori letterali. <br /><br />Esempio 1 #: Il valore specificato contiene un carattere non valido (ad esempio,-). <br /><br />Esempio #2: Il valore specificato contiene un set di parentesi graffe doppio. <br /><br />Esempio 3 #: Il valore specificato manca una parentesi graffa di chiusura.<br /> | Esempio 1: %{resp_user-agent} <br /><br />Esempio 2: %{{host}} <br /><br />Esempio 3: %{host |
| Nome variabile mancante | Quando una variabile non è specificata, viene sempre restituito un valore NULL. | %{} |
| Caratteri finali | I caratteri di chiusura di una variabile vengono considerati valori letterali. <br />Il valore di esempio a destra contiene una parentesi graffa di chiusura che verrà considerata un valore letterale. | %{host}} |

## <a name="setting-default-header-values"></a>Impostazione di valori di intestazione predefiniti
Un valore predefinito può essere assegnato a un'intestazione quando risponde a una delle condizioni seguenti:
- Mancante/non impostata
- Impostata su NULL.

La tabella seguente descrive come definire un valore predefinito.

| Condizione | Sintassi | Esempio | Descrizione |
| --------- | ------ | --------| ----------- |
| Impostare un'intestazione su un valore predefinito quando risponde a una delle condizioni seguenti: <br /><br />- Intestazione mancante <br /><br />- Valore dell'intestazione impostato su NULL.| %{Variable:=Value} | %{http_referrer:=unspecified} | L'intestazione Referrer sarà impostata solo *non specificato* quando è mancante o impostato su NULL. Non verrà eseguita alcuna azione se è stata impostata. |
| Impostare un'intestazione su un valore predefinito quando è mancante. | %{Variable=Value} | %{http_referrer=unspecified} | L'intestazione Referrer sarà impostata solo *non specificato* quando è manca. Non verrà eseguita alcuna azione se è stata impostata. |
| Impostare l'intestazione su un valore predefinito quando non risponde a una delle condizioni seguenti: <br /><br />- mancante<br /><br /> - impostata su NULL. | %{Variable:+Value} | %{http_referrer:+unspecified} | L'intestazione Referrer sarà impostata solo *non specificato* quando è stato assegnato un valore a esso. Non verrà eseguita alcuna azione se è mancante o impostata su NULL. |

## <a name="manipulating-variables"></a>Modifica delle variabili
Le variabili possono essere modificate nei modi seguenti:

- Espansione di sottostringhe
- Rimozione di criteri

### <a name="substring-expansion"></a>Espansione di sottostringhe
Per impostazione predefinita, una variabile si espanderà fino al valore completo. Usare la sintassi seguente per espandere solo una sottostringa del valore della variabile.

`%<Variable>:<Offset>:<Length>}`

Informazioni chiave:

- Il valore assegnato al termine Offset determina il carattere iniziale della sottostringa:

     - Positivo: Il carattere iniziale della sottostringa viene calcolato dal primo carattere nella stringa.
     - Zero: Il carattere iniziale della sottostringa è il primo carattere nella stringa.
     - Negativo: Il carattere iniziale della sottostringa viene calcolato l'ultimo carattere nella stringa.

- La lunghezza della sottostringa è determinata dal termine *Length*:

     - Omesso: Omettendo il termine lunghezza consente la sottostringa da includere tutti i caratteri tra il carattere iniziale e la fine della stringa.
     - Positivo: Determina la lunghezza della sottostringa tra il carattere iniziale a destra.
     - Negativo: Determina la lunghezza della sottostringa tra il carattere iniziale di sinistra.

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

| Sintassi di esempio | Risultati | |
| ------------- | ------- | --- |
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
     - U: Il valore espanso di caratteri maiuscoli.

         Sintassi di esempio:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$U2.$3:80}`

     - L: Minuscolo il valore espanso.

         Sintassi di esempio:

         `%{host/=^www\.([^\.]+)\.([^\.:]+)/cdn.$L2.$3:80}`

- È necessario specificare un operatore prima del criterio. L'operatore specificato determina il comportamento di acquisizione del criterio:

     - `=`: Indica che tutte le occorrenze del modello specificato devono essere acquisite e riscritte.
     - `^`: Indica che verrà acquisito solo testo che inizia con il modello specificato.
     - `$`: Indica che solo il testo che termina con il modello specificato saranno acquisizione.
 
- Se si omette il valore */Rewrite*, il testo corrispondente al criterio viene eliminato.


