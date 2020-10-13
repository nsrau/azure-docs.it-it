---
title: Sportello anteriore di Azure-Caching | Microsoft Docs
description: Questo articolo consente di comprendere il comportamento per la porta anteriore con le regole di routing che hanno abilitato la memorizzazione nella cache.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2020
ms.author: duau
ms.openlocfilehash: 1a8064c3ff89c0bc8b0ceb5249492b912c219ce8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91535832"
---
# <a name="caching-with-azure-front-door"></a>Memorizzazione nella cache con lo sportello anteriore di Azure
Il documento seguente specifica i comportamenti per la porta anteriore con le regole di routing che hanno abilitato la memorizzazione nella cache. La porta anteriore è una rete per la distribuzione di contenuti (CDN) moderna con accelerazione sito dinamico e bilanciamento del carico, supporta anche i comportamenti di memorizzazione nella cache esattamente come qualsiasi altra rete CDN.

## <a name="delivery-of-large-files"></a>Recapito di file di grandi dimensioni
Il front-end di Azure recapita file di grandi dimensioni senza copertura per le dimensioni dei file. Frontdoor di Azure usa una tecnica chiamata suddivisione degli oggetti in blocchi. Quando viene richiesto un file di grandi dimensioni, Frontdoor recupera piccole parti del file dal back-end. Dopo la ricezione di una richiesta di file completa o di intervallo di byte, l'ambiente della porta anteriore richiede il file dal back-end in blocchi di 8 MB.

</br>Una volta che il blocco raggiunge l'ambiente di sportello anteriore, viene memorizzato nella cache e servito immediatamente all'utente. Frontdoor esegue la prelettura del blocco successivo in parallelo. Questa prelettura fa sì che il contenuto resti in anticipo di un blocco rispetto all'utente, riducendo la latenza. Questo processo continua fino a quando non viene scaricato l'intero file (se richiesto) o il client chiude la connessione.

</br>Per altre informazioni sulla richiesta di intervalli di byte, vedere [RFC 7233](https://web.archive.org/web/20171009165003/http://www.rfc-base.org/rfc-7233.html).
La porta anteriore memorizza nella cache tutti i blocchi Man mano che vengono ricevuti, in modo che l'intero file non debba essere memorizzato nella cache della porta anteriore. Le richieste successive per il file o gli intervalli di byte vengono gestite dalla cache. Se i blocchi non sono tutti memorizzati nella cache, il recupero preliminare viene usato per richiedere i blocchi dal back-end. Questa ottimizzazione si basa sulla capacità del back-end di supportare le richieste di intervalli di byte. Se il back-end non supporta le richieste di intervalli di byte, questa ottimizzazione non è efficace.

## <a name="file-compression"></a>Compressione di file
La porta anteriore può comprimere dinamicamente il contenuto sul perimetro, ottenendo tempi di risposta più piccoli e veloci ai client. Tutti i file sono idonei alla compressione. Tuttavia, un file deve essere di tipo MIME per essere idoneo per la compressione. Attualmente, la porta anteriore non consente la modifica di questo elenco. L'elenco corrente consiste in:</br>
- "application/eot"
- "application/font"
- "application/font-sfnt"
- application/javascript
- "application/json"
- "application/opentype"
- "application/otf"
- "application/pkcs7-mime"
- "application/truetype"
- "application/ttf",
- "application/vnd.ms-fontobject"
- "application/xhtml+xml"
- "application/xml"
- "application/xml+rss"
- "application/x-font-opentype"
- "application/x-font-truetype"
- "application/x-font-ttf"
- "application/x-httpd-cgi"
- "application/x-mpegurl"
- "application/x-opentype"
- "application/x-otf"
- "application/x-perl"
- "application/x-ttf"
- "application/x-javascript"
- "font/eot"
- "font/ttf"
- "font/otf"
- "font/opentype"
- "image/svg+xml"
- "text/css"
- "text/csv"
- "text/html"
- "text/javascript"
- "text/js", "text/plain"
- "text/richtext"
- "text/tab-separated-values"
- "text/xml"
- "text/x-script"
- "text/x-component"
- "text/x-java-source"

Inoltre, la dimensione del file deve essere compresa tra 1 KB e 8 MB.

Questi profili supportano le codifiche di compressione seguenti:
- [Gzip (GNU zip)](https://en.wikipedia.org/wiki/Gzip)
- [Brotli](https://en.wikipedia.org/wiki/Brotli)

Se una richiesta supporta la compressione gzip e la compressione Brotli, la compressione Brotli ha la precedenza.</br>
Quando una richiesta di un asset specifica la compressione e la richiesta genera un mancato riscontro nella cache, lo sportello anteriore esegue la compressione dell'asset direttamente nel server POP. In seguito, il file compresso viene gestito nella cache. L'elemento risultante viene restituito con un transfer-encoding: suddiviso in blocchi.

## <a name="query-string-behavior"></a>Comportamento di memorizzazione della stringa di query
Frontdoor consente di controllare la modalità di memorizzazione nella cache dei file per una richiesta Web contenente una stringa di query. In una richiesta Web con una stringa di query, la stringa di query è la parte della richiesta che si verifica dopo un punto di domanda (?). Una stringa di query può contenere una o più coppie chiave-valore, in cui il nome del campo e il relativo valore sono separati da un segno di uguale (=). Ogni coppia chiave-valore è separata da una e commerciale (&). Ad esempio: `http://www.contoso.com/content.mov?field1=value1&field2=value2`. Se è presente più di una coppia chiave-valore in una stringa di query di una richiesta, l'ordine non è rilevante.
- **Ignora stringhe di query**: in questa modalità, la porta anteriore passa le stringhe di query dal richiedente al back-end alla prima richiesta e memorizza nella cache l'asset. Tutte le richieste successive per l'asset che vengono gestite dall'ambiente della porta anteriore ignorano le stringhe di query fino alla scadenza dell'asset memorizzato nella cache.

- **Memorizza nella cache tutti gli URL univoci**: in questa modalità ogni richiesta con URL univoco, compresa la stringa di query, viene considerata un asset univoco con la propria cache. Ad esempio, la risposta dal back-end per una richiesta di `www.example.ashx?q=test1` viene memorizzata nella cache nell'ambiente della porta anteriore e restituita per le cache successive con la stessa stringa di query. Una richiesta di `www.example.ashx?q=test2` viene memorizzata nella cache come asset separato con la propria impostazione di durata (TTL).

## <a name="cache-purge"></a>Ripulire la cache

La porta anteriore memorizza nella cache gli asset fino alla scadenza della durata (TTL) dell'asset. Ogni volta che un client richiede un asset con TTL scaduto, l'ambiente della porta anteriore recupera una nuova copia aggiornata dell'asset per soddisfare la richiesta e quindi archivia la cache aggiornata.

La procedura consigliata per assicurarsi che gli utenti ottengano sempre la copia più recente degli asset consiste nel versioning di questi ultimi per ogni aggiornamento e nella relativa pubblicazione come nuovi URL. Frontdoor recupera immediatamente i nuovi asset per le richieste client successive. A volte si desidera ripulire il contenuto memorizzato nella cache da tutti i nodi periodici e forzare il recupero dei nuovi asset aggiornati. Il motivo potrebbe essere causato da aggiornamenti all'applicazione Web o per aggiornare rapidamente asset che contengono informazioni non corrette.

Selezionare gli asset che si vuole ripulire dai nodi perimetrali. Per cancellare tutti gli asset, selezionare **Ripulisci tutto**. In caso contrario, in **percorso**immettere il percorso di ogni asset che si desidera eliminare.

Questi formati sono supportati negli elenchi di percorsi da eliminare:

- **Eliminazione a percorso singolo**: eliminare singoli asset specificando il percorso completo dell'asset (senza il protocollo e il dominio), con l'estensione di file, ad esempio/Pictures/strasbourg.png;
- **Wildcard purge**: (Eliminazione dei caratteri jolly) l'asterisco (\*) può essere usato come carattere jolly. Eliminare tutte le cartelle, le sottocartelle e i file in un endpoint con/ \* nel percorso o eliminare tutte le sottocartelle e i file in una cartella specifica specificando la cartella seguita da/ \* , ad esempio/Pictures/ \* .
- **Root domain purge**: (Eliminazione del dominio radice) consente di eliminare la radice dell'endpoint inserendo "/" nel percorso.

> [!NOTE]
> **Eliminazione di domini con caratteri jolly**: specificare i percorsi memorizzati nella cache da ripulire come descritto in questa sezione non si applica ai domini con caratteri jolly associati alla porta anteriore. Attualmente non è supportata la cancellazione diretta di domini con caratteri jolly. È possibile eliminare i percorsi da sottodomini specifici specificando il sottodominio specifico e il percorso di ripulitura. Ad esempio, se la mia porta principale è `*.contoso.com` , posso eliminare le risorse del sottodominio `foo.contoso.com` digitando `foo.contoso.com/path/*` . Attualmente, se applicabile, la specifica dei nomi host nel percorso del contenuto di ripulitura è imited sulla ai sottodomini dei domini con caratteri jolly.
>

Le pulizie della cache su Frontdoor non fanno distinzione tra maiuscole e minuscole. Inoltre, sono indipendenti dalla stringa di query, vale a dire che l'eliminazione di un URL eliminerà tutte le varianti della stringa di query. 

## <a name="cache-expiration"></a>Ora di scadenza della cache
Per determinare per quanto tempo un elemento verrà archiviato nella cache, viene usato l'ordine di intestazioni seguente:</br>
1. Cache-Control: s-maxage=\<seconds>
2. Cache-Control: max-age =\<seconds>
3. Scadenza \<http-date>

Cache-Control intestazioni di risposta che indicano che la risposta non verrà memorizzata nella cache, ad esempio cache-Control: private, cache-Control: No-cache e cache-Control: No-Store viene rispettato.  Se non è presente alcun Cache-Control, il comportamento predefinito prevede che la risorsa venga memorizzata nella cache per l'intervallo di tempo di X, dove X viene scelto in modo casuale tra 1 e 3 giorni.

## <a name="request-headers"></a>Intestazioni della richiesta

Le intestazioni di richiesta seguenti non verranno trasmesse a un back-end quando si usa la memorizzazione nella cache.
- Content-Length
- Transfer-encoding

## <a name="cache-duration"></a>Durata cache

La durata della cache può essere configurata sia nella finestra di progettazione front door che nel motore regole. La durata della cache impostata nella finestra di progettazione della porta anteriore è la durata minima della cache. Questa sostituzione non funzionerà se l'intestazione di controllo della cache dall'origine ha un valore TTL maggiore di quello di sostituzione. 

La durata della cache impostata tramite il motore regole è un vero e proprio override della cache, vale a dire che utilizzerà il valore di override indipendentemente dall'intestazione della risposta Origin.

## <a name="next-steps"></a>Passaggi successivi

- Informazioni su come [creare una Frontdoor](quickstart-create-front-door.md).
- Informazioni sul [funzionamento di Frontdoor](front-door-routing-architecture.md).
