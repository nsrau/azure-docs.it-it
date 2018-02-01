---
title: Come funziona la memorizzazione nella cache | Microsoft Docs
description: "La memorizzazione nella cache è il processo di archiviazione dei dati in locale per poter accedere più rapidamente alle future richieste relative ai dati."
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: v-deasim
ms.openlocfilehash: 284b4bcbeafc422a2ed91cec00a5b5b83bb37b7b
ms.sourcegitcommit: 79683e67911c3ab14bcae668f7551e57f3095425
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/25/2018
---
# <a name="how-caching-works"></a>Funzionamento della memorizzazione nella cache

Questo articolo offre una panoramica dei concetti generali di memorizzazione nella cache e illustra il modo in cui la [rete per la distribuzione di contenuti di Azure (CDN)](cdn-overview.md) usa la memorizzazione nella cache per migliorare le prestazioni. Per informazioni su come personalizzare il comportamento della memorizzazione nella cache nell'endpoint della rete CDN, vedere [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con regole](cdn-caching-rules.md) e [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con stringhe di query](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Introduzione alla memorizzazione nella cache

La memorizzazione nella cache è il processo di archiviazione dei dati in locale per poter accedere più rapidamente alle future richieste relative ai dati. Nel tipo più comune di memorizzazione nella cache, ovvero la memorizzazione nella cache del Web browser, un Web browser archivia copie di dati statici in locale su un disco rigido. Grazie alla memorizzazione nella cache, il Web browser può evitare l'esecuzione di più round trip con il server e accedere invece agli stessi dati in locale, risparmiando tempo e risorse. La memorizzazione nella cache è particolarmente adatta per la gestione locale di dati statici di piccole dimensioni, ad esempio immagini statiche, file CSS e file JavaScript.

Analogamente, la memorizzazione nella cache viene usata da una rete per la distribuzione di contenuti nei server perimetrali vicini all'utente per evitare che le richieste tornino all'origine e riducendo così la latenza per l'utente finale. A differenza di una cache del Web browser, usata solo per un singolo utente, la rete CDN ha una cache condivisa. In una cache condivisa della rete CDN, a un file richiesto da un utente possono successivamente accedere altri utenti, riducendo notevolmente il numero di richieste al server di origine.

Non è possibile memorizzare nella cache le risorse dinamiche che cambiano di frequente o che sono univoche per un singolo utente. Questi tipi di risorse possono tuttavia sfruttare l'ottimizzazione 'Accelerazione sito dinamico' nella rete per la distribuzione di contenuti di Azure per migliorare le prestazioni.

La memorizzazione nella cache può essere eseguita a più livelli tra il server di origine e l'utente finale:

- Server Web: usa una cache condivisa (per più utenti).
- Rete per la distribuzione di contenuti: usa una cache condivisa (per più utenti).
- Provider di servizi Internet (ISP): usa una cache condivisa (per più utenti).
- Web browser: usa una cache privata (per un solo utente).

Ogni cache gestisce in genere il proprio aggiornamento delle risorse ed esegue la convalida quando un file non è aggiornato. Questo comportamento viene definito nella specifica di memorizzazione nella cache HTTP, [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Aggiornamento delle risorse

Dato che una risorsa memorizzata nella cache può essere potenzialmente non aggiornata rispetto alla risorsa corrispondente nel server di origine, è importante che qualsiasi meccanismo di memorizzazione nella cache abbia il controllo della tempistica di aggiornamento del contenuto. Per risparmiare tempo e ridurre il consumo di larghezza di banda, una risorsa memorizzata nella cache non viene confrontata con la versione presente nel server di origine ogni volta che si accede a tale risorsa. Finché la risorsa memorizzata nella cache è considerata aggiornata, si presuppone invece che si tratti della versione più recente e viene inviata direttamente al client. Una risorsa memorizzata nella cache è considerata aggiornata quando la sua età è inferiore all'età o al periodo definito da una impostazione della cache. Quando ad esempio ricarica una pagina Web, un browser verifica che ogni risorsa memorizzata nella cache nel disco rigido sia aggiornata e la carica. Se la risorsa non è aggiornata, viene caricata una copia aggiornata dal server.

### <a name="validation"></a>Convalida

Se una risorsa è considerata non aggiornata, viene chiesto al server di origine di convalidarla, ovvero di determinare se i dati presenti nella cache corrispondono ancora ai dati presenti nel server di origine. Se il file è stato modificato nel server di origine, la cache aggiorna la propria versione della risorsa. Se invece la risorsa è aggiornata, i dati vengono inviati direttamente dalla cache, senza prima convalidarli.

### <a name="cdn-caching"></a>Memorizzazione nella cache della rete CDN

La memorizzazione nella cache è parte integrante del modo in cui la rete CDN velocizza il recapito e riduce il carico nell'origine per risorse statiche quali immagini, tipi di carattere e video. Nella memorizzazione nella cache della rete CDN, le risorse statiche vengono archiviate in modo selettivo in server strategici più vicini a un utente, offrendo i vantaggi seguenti:

- Poiché la maggior parte del traffico Web è statico (ad esempio immagini, tipi di carattere e video), la memorizzazione nella cache della rete CDN riduce la latenza di rete avvicinando il contenuto all'utente, riducendo così la distanza percorsa dai dati.

- Grazie all'offload del lavoro in una rete CDN, la memorizzazione nella cache consente di ridurre il traffico di rete e il carico nel server di origine. È così possibile ridurre i costi e i requisiti in termini di risorse per l'applicazione, anche in presenza di un numero elevato di utenti.

In modo simile a come la memorizzazione nella cache è implementata in un Web browser, è possibile definire come viene eseguita la memorizzazione nella cache della rete CDN inviando intestazioni di direttive di memorizzazione nella cache. Le intestazioni di direttive di memorizzazione nella cache sono intestazioni HTTP, in genere aggiunte dal server di origine. Anche se la maggior parte di queste intestazioni è stata originariamente progettata per la memorizzazione nella cache nei browser client, le intestazioni vengono ora usate anche da tutte le cache intermedie, ad esempio le reti CDN. 

È possibile usare due intestazioni per definire l'aggiornamento della cache: `Cache-Control` e `Expires`. `Cache-Control` è più recente e ha la precedenza su `Expires`, in presenza di entrambe. Esistono inoltre due tipi di intestazioni usate per la convalida (denominati validator): `ETag` e `Last-Modified`. `ETag` è più recente e ha la precedenza su `Last-Modified`, se sono definite entrambe.  

## <a name="cache-directive-headers"></a>Intestazioni di direttive della cache

> [!IMPORTANT]
> Per impostazione predefinita, un endpoint della rete CDN di Azure ottimizzato per DSA ignora le intestazioni di direttive della cache e la memorizzazione nella cache. È possibile modificare il modo in cui l'endpoint della rete CDN di Azure gestisce queste intestazioni usando regole di memorizzazione nella cache della rete CDN per abilitare la memorizzazione nella cache. Per altre informazioni, vedere [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con regole](cdn-caching-rules.md).

La rete CDN di Azure supporta le seguenti intestazioni di direttive della cache HTTP, che definiscono la durata e la condivisione della cache: 

`Cache-Control`
- Introdotta nella specifica HTTP 1.1 per superare le limitazioni dell'intestazione `Expires` e per offrire un maggiore controllo sui contenuti nella pubblicazione di contenuti Web.
- Ha la precedenza sull'intestazione `Expires`, se questa è definita insieme all'intestazione `Cache-Control`.
- Quando viene usata in un'intestazione di richiesta, `Cache-Control` viene ignorata dalla rete CDN di Azure per impostazione predefinita.
- Quando viene usata in un'intestazione risposta, la rete CDN di Azure supporta le seguenti direttive `Cache-Control` in base al prodotto: 
   - **Rete CDN di Azure di Verizon**: supporta tutte le direttive `Cache-Control`. 
   - **Rete CDN di Azure di Akamai**: supporta solo le seguenti direttive `Cache-Control`. Tutte le altre vengono ignorate: 
      - `max-age`: una cache può archiviare il contenuto per il numero di secondi specificato, Ad esempio, `Cache-Control: max-age=5`. Questa direttiva specifica il tempo massimo per il quale il contenuto viene considerato aggiornato.
      - `no-cache`: il contenuto viene memorizzato nella cache, ma è necessario convalidarlo ogni volta prima di inviarlo dalla cache. È equivalente a `Cache-Control: max-age=0`.
      - `no-store`: il contenuto non viene mai memorizzato nella cache e viene rimosso se è stato archiviato in precedenza.

`Expires`
- Intestazione legacy introdotta in HTTP 1.0; supportata per la compatibilità con le versioni precedenti.
- Usa una scadenza basata sulla data con precisione al secondo. 
- Simile a `Cache-Control: max-age`.
- Usata quando `Cache-Control` non esiste.

`Pragma`
   - Per impostazione predefinita, non viene rispettata dalla rete CDN di Azure.
   - Intestazione legacy introdotta in HTTP 1.0; supportata per la compatibilità con le versioni precedenti.
   - Usata come intestazione della richiesta client con la direttiva seguente: `no-cache`. Questa direttiva indica al server di distribuire una versione aggiornata della risorsa.
   - `Pragma: no-cache` equivale a: `Cache-Control: no-cache`.

## <a name="validators"></a>Validator

Quando la cache non è aggiornata, vengono usati validator della cache HTTP per confrontare la versione di un file memorizzata nella cache con la versione presente nel server di origine. La **rete CDN di Azure di Verizon** supporta i validator ETag e Last-Modified per impostazione predefinita, mentre la **rete CDN di Azure di Akamai** supporta solo Last-Modified per impostazione predefinita.

`ETag`
- La **rete CDN di Azure di Verizon** usa `ETag` per impostazione predefinita, mentre la **rete CDN di Azure di Akamai** non lo usa.
- `ETag` definisce una stringa univoca per ogni file e versione di un file, Ad esempio, `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Introdotta nella specifica HTTP 1.1 ed è più recente di `Last-Modified`. È utile quando è difficile determinare la data dell'ultima modifica.
- Supporta sia la convalida avanzata che la convalida debole. La rete CDN di Azure supporta tuttavia solo la convalida avanzata. Per la convalida avanzata, le due rappresentazioni della risorsa devono essere identiche byte per byte. 
- La cache convalida un file che usa `ETag` inviando un'intestazione `If-None-Match` con uno o più validator `ETag` nella richiesta, Ad esempio, `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Se la versione del server corrisponde a un validator `ETag` nell'elenco, il server invia il codice di stato 304 (Non modificato) nella risposta. Se la versione è diversa, il server risponde con il codice di stato 200 (OK) e la risorsa aggiornata.

`Last-Modified`
- Solo per la **rete CDN di Azure di Verizon**, viene usato Last-Modified se ETag non fa parte della risposta HTTP. 
- Specifica la data e ora dell'ultima modifica della risorsa secondo quanto determinato dal server, Ad esempio, `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- La cache convalida un file che usa `Last-Modified` inviando un'intestazione `If-Modified-Since` con la data e ora nella richiesta. Il server di origine confronta tale data con l'intestazione `Last-Modified` della risorsa più recente. Se la risorsa non è stata modificata dopo la data e ora specificate, il server restituisce il codice di stato 304 (Non modificato) nella risposta. Se la risorsa è stata modificata, il server restituisce il codice di stato 200 (OK) e la risorsa aggiornata.

## <a name="determining-which-files-can-be-cached"></a>Determinazione dei file che possono essere memorizzati nella cache

Non tutte le risorse possono essere memorizzate nella cache. La tabella seguente mostra le risorse che possono essere memorizzate nella cache, in base al tipo di risposta HTTP. Non è possibile memorizzare nella cache le risorse distribuite con risposte HTTP che non soddisfano tutte queste condizioni. Solo per la **rete CDN di Azure di Verizon Premium** è possibile usare il motore delle regole per personalizzare alcune di queste condizioni.

|                   | Rete CDN di Azure di Verizon | Rete CDN di Azure di Akamai            |
|------------------ |------------------------|----------------------------------|
| Codici di stato HTTP | 200                    | 200, 203, 300, 301, 302 e 401 |
| Metodo HTTP       | GET                    | GET                              |
| Dimensioni complete         | 300 GB                 | - Ottimizzazione distribuzione Web generale: 1,8 GB<br />- Ottimizzazioni dello streaming multimediale: 1,8 GB<br />- Ottimizzazione di file di grandi dimensioni: 150 GB |

## <a name="default-caching-behavior"></a>Comportamento predefinito di memorizzazione nella cache

La tabella seguente descrive il comportamento predefinito di memorizzazione nella cache per i prodotti della rete CDN di Azure e le relative ottimizzazioni.

|                    | Verizon - Distribuzione Web generale | Verizon - DSA | Akamai - Distribuzione Web generale | Akamai - DSA | Akamai - Download di file di grandi dimensioni | Akamai - Streaming multimediale generale o di video on demand |
|--------------------|--------|------|-----|----|-----|-----|
| **Rispetta intestazioni origine**   | Sì    | No    | Sì | No  | Sì | Sì |
| **Durata cache rete CDN** | 7 giorni | Nessuna | 7 giorni | Nessuna | 1 giorno | 1 anno |

**Honor origin** (Rispetta intestazioni origine): specifica se rispettare le [intestazioni di direttive di memorizzazione nella cache supportate](#http-cache-directive-headers) se presenti nella risposta HTTP del server di origine.

**CDN cache duration** (Durata cache rete CDN): specifica il tempo per il quale una risorsa rimane memorizzata nella cache della rete CDN di Azure. Se tuttavia **Rispetta intestazioni origine** è Sì e la risposta HTTP del server di origine include l'intestazione di direttive della cache `Expires` o `Cache-Control: max-age`, la rete CDN di Azure userà invece il valore della durata specificato dall'intestazione. 

## <a name="next-steps"></a>Passaggi successivi

- Per informazioni su come personalizzare e sostituire il comportamento predefinito di memorizzazione nella cache della rete CDN tramite regole, vedere [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con regole](cdn-caching-rules.md). 
- Per informazioni su come usare le stringhe di query per controllare il comportamento di memorizzazione nella cache, vedere [Controllare il comportamento di memorizzazione nella cache della rete CDN di Azure con stringhe di query](cdn-query-string.md).



