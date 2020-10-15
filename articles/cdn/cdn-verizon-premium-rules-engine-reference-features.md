---
title: Funzionalità del motore regole della rete CDN di Azure da Verizon Premium
description: Documentazione di riferimento per le funzionalità del motore regole della rete CDN di Azure da Verizon Premium.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: article
ms.date: 06/02/2020
ms.author: allensu
ms.openlocfilehash: 0ea4f167b992ccfbc4156ac06c8f636d2ef4a355
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84343201"
---
# <a name="azure-cdn-from-verizon-premium-rules-engine-features"></a>Funzionalità del motore regole della rete CDN di Azure da Verizon Premium

Questo articolo offre una descrizione dettagliata delle funzionalità disponibili per il [motore regole](cdn-verizon-premium-rules-engine.md) della rete di distribuzione dei contenuti (CDN) di Azure.

La terza parte di una regola è la funzionalità. Una funzionalità definisce il tipo di azione che viene applicata al tipo di richiesta identificata da un set di condizioni di corrispondenza.

## <a name="azure-cdn-from-verizon-premium-rules-engine-features-reference"></a><a name="top"></a>Documentazione di riferimento per le funzionalità del motore regole della rete CDN di Azure da Verizon Premium

I tipi di funzionalità disponibili sono:

* [Accesso](#access)
* [Memorizzazione nella cache](#caching)
* [Commento](#comment)
* [Intestazioni](#headers)
* [Log](#logs)
* [Optimize](#optimize) (Ottimizza)
* [Origine](#origin)
* [Specializzazione](#specialty)
* [URL](#url)
* [Web application firewall](#waf)

### <a name="access"></a><a name="access"></a>Access

Queste funzionalità sono progettate per controllare l'accesso al contenuto.

| Nome       | Scopo                                                           |
|------------|-------------------------------------------------------------------|
| [Deny Access (403) (Nega accesso (403))](https://docs.vdms.com/cdn/Content/HRE/F/Deny-Access-403.htm) | Determina se tutte le richieste vengono rifiutate con una risposta 403 Accesso negato. |
| [Autenticazione token](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm) | Determina se l'autenticazione basata su token verrà applicata a una richiesta. |
| [Codice rifiuto autenticazione token](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm) | Determina il tipo di risposta che verrà restituito a un utente quando una richiesta viene rifiutata a causa dell'autenticazione basata su token. |
| [Maiuscole/minuscole URL autenticazione token](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm) | Determina se verrà applicata la distinzione tra maiuscole e minuscole nei confronti di URL eseguiti dall'autenticazione basata su token. |
| [Parametro autenticazione token](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm) | Determina se il parametro della stringa di query dell'autenticazione basata su token deve essere rinominato. |

**[Torna all'inizio](#top)**

### <a name="caching"></a><a name="caching"></a>Memorizzazione nella cache

Queste funzionalità sono progettate per personalizzare come e quando il contenuto viene memorizzato nella cache.

| Nome       | Scopo                                                           |
|------------|-------------------------------------------------------------------|
| [Parametri larghezza di banda](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Parameters.htm) | Determina se i parametri di limitazione della larghezza di banda (ad esempio, ec_rate ed ec_prebuf) saranno attivi. |
| [Limitazione larghezza di banda](https://docs.vdms.com/cdn/Content/HRE/F/Bandwidth-Throttling.htm) | Limita la larghezza di banda per la risposta fornita dai server perimetrali. |
| [Ignora cache](https://docs.vdms.com/cdn/Content/HRE/F/Bypass-Cache.htm) | Determina se la richiesta può sfruttare la tecnologia di memorizzazione nella cache. |
| [Gestione intestazione Cache-Control](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Control-Header-Treatment.htm) |  Controlla la generazione delle intestazioni Cache-Control dal server perimetrale quando la funzionalità Max-Age esterna è attiva. |
| [Stringa di query chiave cache](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Query-String.htm) | Determina se la **chiave di cache*** includerà o escluderà i parametri della stringa di query associati a una richiesta. <br> _* Percorso relativo che identifica in modo univoco un asset ai fini della memorizzazione nella cache.  I server perimetrali usano questo percorso relativo quando controllano il contenuto memorizzato nella cache.  Per impostazione predefinita, una chiave di cache non conterrà parametri di stringa di query._ |
| [Cache-Key Rewrite](https://docs.vdms.com/cdn/Content/HRE/F/Cache-Key-Rewrite.htm) (Riscrittura chiave cache) | Riscrive la chiave della cache associata a una richiesta. |
| [Completa riempimento cache](https://docs.vdms.com/cdn/Content/HRE/F/Complete-Cache-Fill.htm) | Determina ciò che accade quando una richiesta determina un mancato riscontro nella cache parziale in un server perimetrale. |
| [Comprimi tipi di file](https://docs.vdms.com/cdn/Content/HRE/F/Compress-File-Types.htm) | Definisce i formati di file che verranno compressi nel server. | 
| [Max-Age interno predefinito](https://docs.vdms.com/cdn/Content/HRE/F/Default-Internal-Max-Age.htm) | Determina l'intervallo Max-Age predefinito per la riconvalida della cache dal server perimetrale al server di origine. |
| [Gestione intestazione Expires](https://docs.vdms.com/cdn/Content/HRE/F/Expires-Header-Treatment.htm) | Controlla la generazione delle intestazioni Expires da un server perimetrale quando la funzionalità Max-Age esterna è attiva. |
| [Max-Age esterno](https://docs.vdms.com/cdn/Content/HRE/F/External-Max-Age.htm) | Determina l'intervallo Max-Age per la riconvalida della cache dal browser al server perimetrale. |
| [Forza Max-Age interno](https://docs.vdms.com/cdn/Content/HRE/F/Force-Internal-Max-Age.htm) | Determina l'intervallo Max-Age per la riconvalida della cache dal server perimetrale al server di origine. |
| [Supporto H.264 (download progressivo HTTP)](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-HPD.htm) | Determina i tipi di formati di file H.264 che possono essere usati per lo streaming di contenuti. |
| [Parametri di ricerca video del supporto H. 264](https://docs.vdms.com/cdn/Content/HRE/F/H.264-Support-VSP-HPD.htm) | Esegue l'override dei nomi assegnati ai parametri che controllano la ricerca tramite supporti H. 264 quando si usa il download progressivo HTTP. |
| [Rispetta richiesta No-Cache](https://docs.vdms.com/cdn/Content/HRE/F/Honor-No-Cache-Request.htm) | Determina se le richieste no-cache di un client HTTP verranno inoltrate al server di origine. |
| [Ignora origine No-Cache](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Origin-No-Cache.htm) | Determina se la rete CDN ignorerà alcune direttive servite da un server di origine. |
| [Ignora gli intervalli che non è possibile soddisfare](https://docs.vdms.com/cdn/Content/HRE/F/Ignore-Unsatisfiable-Ranges.htm) | Determina la risposta che verrà restituita ai client quando una richiesta genera un codice di stato 416 Impossibile attenersi all'intervallo richiesto. |
| [Max-Stale interno](https://docs.vdms.com/cdn/Content/HRE/F/Internal-Max-Stale.htm) | Controlla per quanto tempo dopo la normale scadenza un asset memorizzato nella cache può essere servito da un server perimetrale quando il server perimetrale non è in grado di riconvalidare l'asset memorizzato nella cache con il server di origine. |
| [Condivisione cache parziale](https://docs.vdms.com/cdn/Content/HRE/F/Partial-Cache-Sharing.htm) | Determina se una richiesta può generare contenuto parzialmente memorizzato nella cache. |
| [Preconvalida contenuto memorizzato nella cache](https://docs.vdms.com/cdn/Content/HRE/F/Prevalidate-Cached-Content.htm) | Determina se il contenuto memorizzato nella cache sarà idoneo per la riconvalida anticipata prima della scadenza della durata (TTL). |
| [Aggiorna i file della cache con zero byte](https://docs.vdms.com/cdn/Content/HRE/F/Refresh-Zero-Byte-Cache-Files.htm) | Determina come viene gestita dai server perimetrali una richiesta di un client HTTP di un asset della cache con 0 byte. |
| [Imposta codici di stato inseribile nella cache](https://docs.vdms.com/cdn/Content/HRE/F/Set-Cacheable-Status-Codes.htm) | Definisce il set di codici di stato che possono generare contenuto memorizzato nella cache. |
| [Distribuzione di contenuto non aggiornato in caso di errore](https://docs.vdms.com/cdn/Content/HRE/F/Stale-Content-Delivery-on-Error.htm) | Determina se il contenuto scaduto memorizzato nella cache verrà distribuito quando si verifica un errore durante la riconvalida della cache o quando si recupera il contenuto richiesto dal server di origine del cliente. | 
| [Client non aggiornato durante la riconvalida](https://docs.vdms.com/cdn/Content/HRE/F/Stale-While-Revalidate.htm) | Migliora le prestazioni consentendo ai server perimetrali di servire un client non aggiornato al richiedente mentre avviene la riconvalida. |

**[Torna all'inizio](#top)**

### <a name="comment"></a><a name="comment"></a>Commento

La funzionalità Commento consente di aggiungere una nota all'interno di una regola.

**[Torna all'inizio](#top)**

### <a name="headers"></a><a name="headers"></a>Headers

Queste funzionalità sono progettate per aggiungere, modificare o eliminare le intestazioni dalla richiesta o dalla risposta.

| Nome       | Scopo                                                           |
|------------|-------------------------------------------------------------------|
| [Intestazione di risposta Age](https://docs.vdms.com/cdn/Content/HRE/F/Age-Response-Header.htm) | Determina se un'intestazione di risposta Age verrà inclusa nella risposta inviata al richiedente. |
| [Intestazioni di risposta di debug per la cache](https://docs.vdms.com/cdn/Content/HRE/F/Debug-Cache-Response-Headers.htm) | Determina se una risposta può includere l' [intestazione della risposta X-EC-debug](https://docs.vdms.com/cdn/Content/Knowledge_Base/X_EC_Debug.htm) che fornisce informazioni sui criteri di cache per l'asset richiesto. |
| [Modify Client Request Header](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Request-Header.htm) (Modifica intestazione richiesta client) | Sovrascrive, aggiunge o elimina un'intestazione da una richiesta. |
| [Modify Client Response Header](https://docs.vdms.com/cdn/Content/HRE/F/Modify-Client-Response-Header.htm) (Modifica intestazione risposta client) | Sovrascrive, aggiunge o elimina un'intestazione da una risposta. |
| [Imposta intestazione personalizzata IP client](https://docs.vdms.com/cdn/Content/HRE/F/Set-Client-IP-Custom-Header.htm) | Consente di aggiungere l'indirizzo IP del client richiedente alla richiesta come un'intestazione personalizzata. |

**[Torna all'inizio](#top)**

### <a name="logs"></a><a name="logs"></a>Log

Queste funzionalità sono progettate per personalizzare i dati archiviati nei file di log non elaborati.

| Nome       | Scopo                                                           |
|------------|-------------------------------------------------------------------|
| [Campo 1 log personalizzato](https://docs.vdms.com/cdn/Content/HRE/F/Custom-Log-Field-1.htm) | Determina il formato e il contenuto che verranno assegnati al campo di log personalizzato in un file di log non elaborato. |
| [Stringa di query log](https://docs.vdms.com/cdn/Content/HRE/F/Log-Query-String.htm) | Determina se una stringa di query verrà archiviata insieme all'URL nei log di accesso. |

**[Torna all'inizio](#top)**

### <a name="optimize"></a><a name="optimize"></a>Ottimizzazione

Queste funzionalità determinano se una richiesta sarà sottoposta alle ottimizzazioni fornite da Edge Optimizer.

| Nome       | Scopo                                                           |
|------------|-------------------------------------------------------------------|
| [Edge Optimizer](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer.htm) | Determina se Edge Optimizer può essere applicato a una richiesta. |
| [Edge Optimizer - Crea istanza configurazione](https://docs.vdms.com/cdn/Content/HRE/F/Edge-Optimizer-Instantiate-Configuration.htm) | Crea un'istanza o attiva la configurazione di Edge Optimizer associata a un sito. |

**[Torna all'inizio](#top)**

### <a name="origin"></a><a name="origin"></a>Origine

Queste funzionalità sono progettate per controllare in che modo la rete CDN comunica con un server di origine.

| Nome       | Scopo                                                           |
|------------|-------------------------------------------------------------------|
| [Numero massimo di richieste Keep-Alive](https://docs.vdms.com/cdn/Content/HRE/F/Maximum-Keep-Alive-Requests.htm) | Definisce il numero massimo di richieste per una connessione Keep-Alive prima della chiusura. |
| [Intestazioni speciali proxy](https://docs.vdms.com/cdn/Content/HRE/F/Proxy-Special-Headers.htm) | Definisce il set di [intestazioni di richiesta specifiche](https://docs.vdms.com/cdn/Content/Knowledge_Base/Request-Format.htm#RequestHeaders) della rete CDN che verranno trasmesse da un server perimetrale a un server di origine. |

**[Torna all'inizio](#top)**

### <a name="specialty"></a><a name="specialty"></a>Specializzazione

Queste funzionalità offrono caratteristiche avanzate che devono essere usate solo dagli utenti esperti.

| Nome       | Scopo                                                           |
|------------|-------------------------------------------------------------------|
| [Metodi HTTP inseribile nella cache](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-HTTP-Methods.htm) | Determina il set di metodi HTTP aggiuntivi che possono essere memorizzati nella cache nella rete. |
| [Dimensioni corpo richiesta inseribile nella cache](https://docs.vdms.com/cdn/Content/HRE/F/Cacheable-Request-Body-Size.htm) | Definisce la soglia per determinare se una risposta POST può essere memorizzata nella cache. |
| [QUIC](https://docs.vdms.com/cdn/Content/HRE/F/QUIC.htm) | Determina se il client verrà informato che il servizio CDN supporta QUIC. |
| [Ottimizzazione dello streaming](https://docs.vdms.com/cdn/Content/HRE/F/Streaming-Optimization.htm) | Ottimizza la configurazione della memorizzazione nella cache per ottimizzare le prestazioni dei flussi live e per ridurre il carico sul server di origine. |
| [User Variable (Variabile utente)](https://docs.vdms.com/cdn/Content/HRE/F/User-Variable.htm) | Assegna un valore a una variabile definita dall'utente che viene passata alla soluzione di elaborazione del traffico su misura. |

**[Torna all'inizio](#top)**

### <a name="url"></a><a name="url"></a>URL

Queste funzionalità consentono il reindirizzamento o la riscrittura di una richiesta in un URL diverso.

| Nome       | Scopo                                                           |
|------------|-------------------------------------------------------------------|
| [Segui reindirizzamenti](https://docs.vdms.com/cdn/Content/HRE/F/Follow-Redirects.htm) | Determina se le richieste possono essere reindirizzate al nome host definito nell'intestazione Location restituita da un server di origine del cliente. |
| [URL Redirect](https://docs.vdms.com/cdn/Content/HRE/F/URL-Redirect.htm) (Reindirizzamento URL) | Reindirizza le richieste tramite l'intestazione Location. |
| [URL Rewrite](https://docs.vdms.com/cdn/Content/HRE/F/URL-Rewrite.htm) (Riscrittura URL) | Riscrive l'URL della richiesta. |

**[Torna all'inizio](#top)**

### <a name="web-application-firewall"></a><a name="waf"></a>Web application firewall

La funzionalità [Web Application Firewall](https://docs.vdms.com/cdn/Content/HRE/F/Web_Application_Firewall.htm) determina se una richiesta verrà visualizzata da Web Application Firewall.

**[Torna all'inizio](#top)**

Per le funzionalità più recenti, vedere la [documentazione del motore regole di Verizon](https://docs.vdms.com/cdn/index.html#Quick_References/HRE_QR.htm#Actions).

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni di riferimento sul motore regole](cdn-verizon-premium-rules-engine-reference.md)
- [Espressioni condizionali del motore regole](cdn-verizon-premium-rules-engine-reference-conditional-expressions.md)
- [Condizioni di corrispondenza del motore regole](cdn-verizon-premium-rules-engine-reference-match-conditions.md)
- [Override del comportamento HTTP predefinito mediante il motore regole](cdn-verizon-premium-rules-engine.md)
- [Panoramica della rete CDN di Azure](cdn-overview.md)
