---
title: Intestazioni HTTP X-EC-Debug per il motore regole della rete CDN di Azure | Microsoft Docs
description: L' intestazione della richiesta di debug della cache, X-EC-Debug, fornisce ulteriori informazioni sui criteri della cache applicati all'asset richiesto. Queste intestazioni sono specifiche di Verizon.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2018
ms.author: magattus
ms.openlocfilehash: 4ba42850ee28e2e212d9bc2b7b64be103218757c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60736973"
---
# <a name="x-ec-debug-http-headers-for-azure-cdn-rules-engine"></a>Intestazioni HTTP X-EC-Debug per il motore regole della rete CDN di Azure
L' intestazione della richiesta di debug della cache, `X-EC-Debug`, fornisce ulteriori informazioni sui criteri della cache applicati all'asset richiesto. Queste intestazioni sono specifiche dei prodotti **Azure CDN Premium di Verizon**.

## <a name="usage"></a>Uso
La risposta inviata dai server POP a un utente include l'intestazione `X-EC-Debug` solo quando sono soddisfatte le condizioni seguenti:

- Nel motore regole per la richiesta specificata è stata abilitata la [funzionalità relativa alle intestazioni di risposta di debug per la cache](cdn-rules-engine-reference-features.md#debug-cache-response-headers).
- La richiesta specificata definisce il set di intestazioni di risposta di debug per la cache che verrà incluso nella risposta.

## <a name="requesting-debug-cache-information"></a>Richiesta di informazioni di debug per la cache
Usare le direttive seguenti nella richiesta specificata per definire le informazioni di debug per la cache che verranno incluse nella risposta:

Intestazione della richiesta | DESCRIZIONE |
---------------|-------------|
X-EC-Debug: x-ec-cache | [Codice di stato della cache](#cache-status-code-information)
X-EC-Debug: x-ec-cache-remote | [Codice di stato della cache](#cache-status-code-information)
X-EC-Debug: x-ec-check-cacheable | [Inseribile nella cache](#cacheable-response-header)
X-EC-Debug: x-ec-cache-key | [Chiave di cache](#cache-key-response-header)
X-EC-Debug: x-ec-cache-state | [Stato cache](#cache-state-response-header)

### <a name="syntax"></a>Sintassi

Le intestazioni di risposta di debug per la cache possono essere richieste includendo nella richiesta l'intestazione seguente e le direttive specificate:

`X-EC-Debug: Directive1,Directive2,DirectiveN`

### <a name="sample-x-ec-debug-header"></a>Esempio di intestazione X-EC-Debug

`X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state`

## <a name="cache-status-code-information"></a>Informazioni sul codice di stato della cache
L'intestazione della risposta X-EC-Debug può identificare un server e il modo in cui ha gestito la risposta tramite le direttive seguenti:

Intestazione | DESCRIZIONE
-------|------------
X-EC-Debug: x-ec-cache | Questa intestazione viene segnalata ogni volta che viene eseguito il routing di contenuto attraverso la rete CDN. Identifica il server POP che ha soddisfatto la richiesta.
X-EC-Debug: x-ec-cache-remote | Questa intestazione viene visualizzata solo quando il contenuto richiesto è stato memorizzato nella cache in un server shield di origine o in un server gateway ADN.

### <a name="response-header-format"></a>Formato dell'intestazione della risposta

L'intestazione X-EC-Debug visualizza le informazioni sul codice di stato della cache nel formato seguente:

- `X-EC-Debug: x-ec-cache: <StatusCode from Platform (POP/ID)>`

- `X-EC-Debug: x-ec-cache-remote: <StatusCode from Platform (POP/ID)>`

I termini usati nella sintassi dell'intestazione della risposta riportata sopra sono definiti nel modo seguente:
- StatusCode: Indica come il contenuto richiesto è stato gestito dalla rete CDN, rappresentato tramite un codice di stato della cache.
    
    Il codice di stato TCP_DENIED può essere visualizzato al posto di NONE quando una richiesta non autorizzata viene rifiutata a causa dell'autenticazione basata su token. Tuttavia, il codice di stato NONE continuerà a essere usato nella visualizzazione dei report di stato della cache o nei dati di log non elaborati.

- Piattaforma: Indica la piattaforma su cui è stato richiesto il contenuto. I codici seguenti sono validi per questo campo:

    Codice  | Piattaforma
    ------| --------
    ECAcc | HTTP Large
    ECS   | HTTP Small
    ECD   | Application Delivery Network (ADN)

- POP: Indica la [POP](cdn-pop-abbreviations.md) che ha gestito la richiesta. 

### <a name="sample-response-headers"></a>Intestazioni di risposta di esempio

Le intestazioni di esempio seguenti forniscono le informazioni sul codice di stato della cache per una richiesta:

- `X-EC-Debug: x-ec-cache: TCP_HIT from ECD (lga/0FE8)`

- `X-EC-Debug: x-ec-cache-remote: TCP_HIT from ECD (dca/EF00)`

## <a name="cacheable-response-header"></a>Intestazione di risposta Cacheable
L'intestazione di risposta `X-EC-Debug: x-ec-check-cacheable` indica se il contenuto richiesto era inseribile nella cache.

Questa intestazione non indica se l'inserimento nella cache è effettivamente avvenuto. Indica piuttosto se la richiesta fosse idonea per la memorizzazione nella cache.

### <a name="response-header-format"></a>Formato dell'intestazione della risposta

L'intestazione di risposta `X-EC-Debug` che segnala se la richiesta fosse idonea per la memorizzazione nella cache è nel formato seguente:

`X-EC-Debug: x-ec-check-cacheable: <cacheable status>`

Il termine usato nella sintassi dell'intestazione della risposta riportata sopra è definito nel modo seguente:

Value  | DESCRIZIONE
-------| --------
SÌ    | Indica che il contenuto richiesto era idoneo per la memorizzazione nella cache.
NO     | Indica che il contenuto richiesto non era idoneo per la memorizzazione nella cache. Questo stato può essere dovuto a una delle cause seguenti: <br /> Configurazione specifici del cliente: Una configurazione specifica dell'account può impedire ai server pop di memorizzazione nella cache un asset. Ad esempio, il motore regole può impedire la memorizzazione di un asset nella cache abilitando la funzionalità Ignora cache per le richieste qualificate.<br /> -Memorizza nella cache le intestazioni di risposta: Le intestazioni Cache-Control ed Expires dell'asset richiesto possono impedire i server POP di memorizzarla nella cache.
UNKNOWN | Indica che i server non sono stati in grado di valutare se l'asset richiesto fosse inseribile nella cache. Questo stato si verifica in genere quando la richiesta viene rifiutata a causa dell'autenticazione basata su token.

### <a name="sample-response-header"></a>Esempio di intestazione di risposta

L'intestazione di risposta di esempio seguente indica se il contenuto richiesto era inseribile nella cache:

`X-EC-Debug: x-ec-check-cacheable: YES`

## <a name="cache-key-response-header"></a>Intestazione di risposta Cache-Key
L'intestazione di risposta `X-EC-Debug: x-ec-cache-key` indica la chiave di cache fisica associata al contenuto richiesto. Una chiave di cache fisica è costituita da un percorso che identifica un asset ai fini della memorizzazione nella cache. In altre parole, i server cercano una versione memorizzata nella cache di un asset in base al relativo percorso, così come definito dalla chiave di cache.

Questa chiave di cache fisica inizia con una doppia barra (//) seguita dal protocollo usato per richiedere il contenuto (HTTP o HTTPS). Questo protocollo è seguito dal percorso relativo dell'asset richiesto, che inizia con il punto di accesso del contenuto (ad esempio _/000001/_).

Per impostazione predefinita, le piattaforme HTTP sono configurate per l'uso dell'intestazione *standard-cache*, tramite la quale le stringhe di query vengono ignorate dal meccanismo di memorizzazione nella cache. Questo tipo di configurazione impedisce alla chiave di cache di includere dati di stringhe di query.

Se una stringa di query viene registrata nella chiave di cache, viene convertita nel suo equivalente hash e quindi inserita tra il nome dell'asset richiesto e l'estensione di file (ad esempio, asset&lt;valore hash&gt;.html).

### <a name="response-header-format"></a>Formato dell'intestazione della risposta

L'intestazione di risposta `X-EC-Debug` visualizza le informazioni sulla chiave di cache fisica nel formato seguente:

`X-EC-Debug: x-ec-cache-key: CacheKey`

### <a name="sample-response-header"></a>Esempio di intestazione di risposta

L'intestazione di risposta di esempio seguente indica la chiave di cache fisica relativa al contenuto richiesto:

`X-EC-Debug: x-ec-cache-key: //http/800001/origin/images/foo.jpg`

## <a name="cache-state-response-header"></a>Intestazione di risposta cache-state
L'intestazione di risposta `X-EC-Debug: x-ec-cache-state` indica lo stato della cache del contenuto richiesto al momento della richiesta.

### <a name="response-header-format"></a>Formato dell'intestazione della risposta

L'intestazione di risposta `X-EC-Debug` visualizza le informazioni sullo stato della cache nel formato seguente:

`X-EC-Debug: x-ec-cache-state: max-age=MASeconds (MATimePeriod); cache-ts=UnixTime (ddd, dd MMM yyyy HH:mm:ss GMT); cache-age=CASeconds (CATimePeriod); remaining-ttl=RTSeconds (RTTimePeriod); expires-delta=ExpiresSeconds`

I termini usati nella sintassi dell'intestazione della risposta riportata sopra sono definiti nel modo seguente:

- MASeconds: Indica la durata massima (in secondi) come definito dalle intestazioni Cache-Control del contenuto richiesto.

- MATimePeriod: Converte il valore di max-age (ossia MASeconds) nell'equivalente approssimativo di un'unità superiore (ad esempio giorni). 

- UnixTime: Indica il timestamp della cache del contenuto richiesto in orario Unix (noto anche come formato data/ora POSIX o periodo Unix). Il timestamp della cache indica la data/ora a partire dalla quale verrà calcolato il TTL di un asset. 

    Se il server di origine non usa un server di memorizzazione nella cache HTTP di terze parti o se tale server non restituisce l'intestazione di risposta Age, il timestamp della cache corrisponderà sempre alla data/ora in cui l'asset è stato recuperato o riconvalidato. In caso contrario, i server POP verranno utilizzato il campo Age per calcolare il TTL dell'asset nel modo seguente: Retrieval/RevalidateDateTime - Age.

- ddd, dd MMM yyyy hh: mm: GMT: Indica il timestamp della cache del contenuto richiesto. Per altre informazioni, vedere il termine UnixTime più indietro.

- CASeconds: Indica il numero di secondi trascorsi il timestamp della cache.

- RTSeconds: Indica il numero di secondi rimanenti per il quale il contenuto memorizzato nella cache sarà considerato aggiornato. Questo valore viene calcolato come segue: RTSeconds = max-age - cache age.

- RTTimePeriod: Converte il valore TTL rimanente (ossia RTSeconds) nell'equivalente approssimativo di un'unità superiore (ad esempio giorni).

- ExpiresSeconds: Indica il numero di secondi rimanenti prima della data e ora specificata nel `Expires` intestazione della risposta. Se l'intestazione di risposta `Expires` non era inclusa nella risposta, il valore di questo termine è *none*.

### <a name="sample-response-header"></a>Esempio di intestazione di risposta

L'intestazione di risposta di esempio seguente indica lo stato della cache del contenuto richiesto al momento della richiesta:

```X-EC-Debug: x-ec-cache-state: max-age=604800 (7d); cache-ts=1341802519 (Mon, 09 Jul 2012 02:55:19 GMT); cache-age=0 (0s); remaining-ttl=604800 (7d); expires-delta=none```

