---
title: "Funzionalità del motore regole della rete CDN di Azure | Documentazione Microsoft"
description: "Documentazione di riferimento per le funzionalità e condizioni di corrispondenza del motore regole della rete CDN di Azure."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
translationtype: Human Translation
ms.sourcegitcommit: dccb945e170bd3e3f23283359db25e574a2d4296
ms.openlocfilehash: 6703247aa8b4a6d53ff22ea2d4f22eb4a746e370


---

# <a name="azure-cdn-rules-engine-features"></a>Funzionalità del motore regole della rete CDN di Azure
Questo argomento offre descrizioni dettagliate delle funzionalità disponibili per il [motore regole](cdn-rules-engine.md) della rete per la distribuzione di contenuti (CDN, Content Delivery Network) di Azure.

La terza parte di una regola è la funzionalità. Una funzionalità definisce il tipo di azione che verrà applicato al tipo di richiesta identificata da un set di condizioni di corrispondenza.

## <a name="access"></a>Accesso

Queste funzionalità sono progettate per controllare l'accesso al contenuto.


Nome | Scopo
-----|--------
Nega l'accesso | Determina se tutte le richieste vengono rifiutate con una risposta 403 Accesso negato.
Autenticazione token | Determina se l'autenticazione basata su token verrà applicata a una richiesta.
Codice rifiuto autenticazione token | Determina il tipo di risposta che verrà restituito a un utente quando una richiesta viene rifiutata a causa dell'autenticazione basata su token.
Maiuscole/minuscole URL autenticazione token | Determina se verrà applicata la distinzione tra maiuscole e minuscole nei confronti di URL eseguiti dall'autenticazione basata su token.
Parametro autenticazione token | Determina se il parametro della stringa di query dell'autenticazione basata su token deve essere rinominato.

### <a name="deny-access"></a>Nega l'accesso
**Scopo**: determina se tutte le richieste vengono rifiutate con una risposta 403 - Accesso negato.

Valore | Risultato
------|-------
Enabled| Fa sì che tutte le richieste che soddisfano i criteri di corrispondenza vengano respinte con una risposta 403 - Accesso negato.
Disabled| Ripristina il comportamento predefinito. Il comportamento predefinito prevede di consentire al server di origine di determinare il tipo di risposta da restituire.

**Comportamento predefinito**: Disabled

> [!TIP]
   > Un possibile uso di questa funzionalità è quello di associarla a una condizione di corrispondenza Request Header (Intestazione di richiesta) per bloccare l'accesso a riferimenti HTTP che usano collegamenti inline ai contenuti.

### <a name="token-auth"></a>Autenticazione token
**Scopo:** determina se l'autenticazione basata su token verrà applicata a una richiesta.

Se l'autenticazione basata su token è abilitata, verranno soddisfatte solo le richieste che forniscono un token crittografato e sono conformi ai requisiti specificati dal token.

La chiave di crittografia usata per crittografare e decrittografare i valori dei token viene determinata dalle opzioni relative alla chiave primaria e alla chiave di backup disponibili nella pagina di autenticazione del token. Tenere presente che le chiavi di crittografia sono specifiche della piattaforma.

Valore | Risultato
------|---------
Enabled | Protegge i contenuti richiesti con l'autenticazione basata su token. Verranno soddisfatte solo le richieste provenienti da client che forniscono un token valido e ne soddisfano i requisiti. Dall'autenticazione basata su token sono escluse le transazioni FTP.
Disabled| Ripristina il comportamento predefinito. Il comportamento predefinito prevede di consentire alla configurazione dell'autenticazione basata su token di determinare se una richiesta deve essere protetta.

**Comportamento predefinito:** Disabled.

###<a name="token-auth-denial-code"></a>Codice rifiuto autenticazione token
**Scopo:** determina il tipo di risposta che verrà restituita a un utente quando una richiesta viene rifiutata a causa dell'autenticazione basata su token.

Di seguito sono elencati i codici di risposta disponibili.

Codice risposta|Nome risposta|Descrizione
----------------|-----------|--------
301|Spostato in modo permanente|Questo codice di stato reindirizza gli utenti non autorizzati all'URL specificato nell'intestazione Location (Percorso).
302|Trovato|Questo codice di stato reindirizza gli utenti non autorizzati all'URL specificato nell'intestazione Location (Percorso). Questo codice di stato costituisce il metodo standard per eseguire un'operazione di reindirizzamento.
307|Reindirizzamento temporaneo|Questo codice di stato reindirizza gli utenti non autorizzati all'URL specificato nell'intestazione Location (Percorso).
401|Non autorizzata|La combinazione di questo codice di stato con l'intestazione di risposta WWW-Authenticate consente di richiedere a un utente di eseguire l'autenticazione.
403|Accesso negato|Messaggio di stato standard 403 - Accesso negato che viene visualizzato quando un utente non autorizzato prova ad accedere a contenuti protetti.
404|File non trovato|Questo codice di stato indica che il client HTTP è riuscito a comunicare con il server, ma i contenuti richiesti non sono stati trovati.

#### <a name="url-redirection"></a>Reindirizzamento URL

Questa funzionalità supporta il reindirizzamento dell'URL a un URL definito dall'utente, se configurato per restituire un codice di stato 3xx. Per specificare l'URL definito dall'utente, seguire questa procedura:

1. Selezionare un codice di risposta 3xx per la funzionalità Token Auth Denial Code (Codice rifiuto autenticazione token).
2. Per l'opzione Optional Header Name (Nome intestazione facoltativo) selezionare "Location".
3. Impostare l'opzione Optional Header Value (Valore intestazione facoltativo) sull'URL desiderato.

Se un URL non è definito per un codice di stato 3xx, all'utente verrà restituita la pagina di risposta standard per un codice di stato 3xx.

Il reindirizzamento degli URL può essere usato solo per i codici di risposta 3xx.

L'opzione Optional Header Value (Valore intestazione facoltativo) supporta caratteri alfanumerici, virgolette e spazi.

#### <a name="authentication"></a>Autenticazione

Questa funzionalità prevede la possibilità di includere l'intestazione WWW-Authenticate quando si risponde a una richiesta non autorizzata di contenuti protetti con l'autenticazione basata su token. Se nella configurazione l'intestazione WWW-Authenticate è stata impostata su "basic", all'utente non autorizzato verrà richiesto di specificare le credenziali dell'account.

La configurazione precedente può essere ottenuta seguendo questa procedura:

1. Selezionare "401" come codice di risposta per la funzionalità Token Auth Denial Code (Codice rifiuto autenticazione token).
2. Per l'opzione Optional Header Name (Nome intestazione facoltativo) selezionare "WWW-Authenticate".
3. Impostare l'opzione Optional Header Value (Valore intestazione facoltativo) su "basic".

L'intestazione WWW-Authenticate può essere usata solo per i codici di risposta 401.

### <a name="token-auth-ignore-url-case"></a>Maiuscole/minuscole URL autenticazione token
**Scopo:** determina se durante i confronti di URL eseguiti dall'autenticazione basata su token deve essere applicata la distinzione tra maiuscole e minuscole.

I parametri interessati da questa funzionalità sono:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

I valori validi sono:

Valore|Risultato
---|----
Enabled|Fa sì che il server perimetrale non applichi la distinzione tra maiuscole e minuscole quando confronta gli URL in base ai parametri dell'autenticazione basata su token.
Disabled|Ripristina il comportamento predefinito. Il comportamento predefinito prevede di applicare la distinzioni tra maiuscole e minuscole durante i confronti di URL per l'autenticazione basata su token.

**Comportamento predefinito:** Disabled.
 
### <a name="token-auth-parameter"></a>Parametro autenticazione token
**Scopo:** determina se il parametro della stringa di query dell'autenticazione basata su token deve essere rinominato.

Informazioni chiave:

- L'opzione Valore definisce il nome del parametro della stringa di query tramite il quale è possibile specificare un token.
- L'opzione Valore non può essere impostata su "ec_token".
- Assicurarsi che il nome definito nell'opzione Valore 
- contenga solo caratteri URL validi.

Valore|Risultato
----|----
Enabled|L'opzione Valore definisce il nome del parametro della stringa di query tramite il quale devono essere definiti i token.
Disabled|Un token può essere specificato come un parametro non definito della stringa di query nell'URL della richiesta.

**Comportamento predefinito:** Disabled. Un token può essere specificato come un parametro non definito della stringa di query nell'URL della richiesta.

## <a name="caching"></a>Memorizzazione nella cache

Queste funzionalità sono progettate per personalizzare come e quando il contenuto viene memorizzato nella cache.

Nome | Scopo
-----|--------
Parametri larghezza di banda | Determina se i parametri di limitazione della larghezza di banda (ad esempio, ec_rate ed ec_prebuf) saranno attivi.
Limitazione larghezza di banda | Limita la larghezza di banda per la risposta fornita dai server perimetrali.
Ignora cache | Determina se la richiesta può sfruttare la tecnologia di memorizzazione nella cache.
Gestione intestazione Cache-Control | Controlla la generazione delle intestazioni Cache-Control dal server perimetrale quando la funzionalità Max-Age esterna è attiva.
Stringa di query chiave cache | Determina se la chiave della cache includerà o escluderà i parametri della stringa di query associati a una richiesta.
Riscrittura chiave cache | Riscrive la chiave della cache associata a una richiesta.
Completa riempimento cache | Determina ciò che accade quando una richiesta determina un mancato riscontro nella cache parziale in un server perimetrale.
Comprimi tipi di file | Definisce i formati di file che verranno compressi nel server.
Max-Age interno predefinito | Determina l'intervallo Max-Age predefinito per la riconvalida della cache dal server perimetrale al server di origine.
Gestione intestazione Expires | Controlla la generazione delle intestazioni Expires da un server perimetrale quando la funzionalità Max-Age esterna è attiva.
Max-Age esterno | Determina l'intervallo Max-Age per la riconvalida della cache dal browser al server perimetrale.
Forza Max-Age interno | Determina l'intervallo Max-Age per la riconvalida della cache dal server perimetrale al server di origine.
Supporto H.264 (download progressivo HTTP) | Determina i tipi di formati di file H.264 che possono essere usati per lo streaming di contenuti.
Rispetta richiesta No-Cache | Determina se le richieste no-cache di un client HTTP verranno inoltrate al server di origine.
Ignora origine No-Cache | Determina se la rete CDN ignorerà alcune direttive servite da un server di origine.
Ignora gli intervalli che non è possibile soddisfare | Determina la risposta che verrà restituita ai client quando una richiesta genera un codice di stato 416 Impossibile attenersi all'intervallo richiesto.
Max-Stale interno | Controlla per quanto tempo dopo la normale scadenza un asset memorizzato nella cache può essere servito da un server perimetrale quando il server perimetrale non è in grado di riconvalidare l'asset memorizzato nella cache con il server di origine.
Condivisione cache parziale | Determina se una richiesta può generare contenuto parzialmente memorizzato nella cache.
Preconvalida contenuto memorizzato nella cache | Determina se il contenuto memorizzato nella cache sarà idoneo per la riconvalida anticipata prima della scadenza della durata (TTL).
Aggiorna i file della cache con zero byte | Determina come viene gestita dai server perimetrali una richiesta di un client HTTP di un asset della cache con 0 byte.
Imposta codici di stato inseribile nella cache | Definisce il set di codici di stato che possono generare contenuto memorizzato nella cache.
Distribuzione di contenuto non aggiornato in caso di errore | Determina se il contenuto scaduto memorizzato nella cache verrà distribuito quando si verifica un errore durante la riconvalida della cache o quando si recupera il contenuto richiesto dal server di origine del cliente.
Client non aggiornato durante la riconvalida | Migliora le prestazioni consentendo ai server perimetrali di servire un client non aggiornato al richiedente mentre avviene la riconvalida.
Commento | La funzionalità Commento consente di aggiungere una nota all'interno di una regola.

###<a name="bandwidth-parameters"></a>Parametri larghezza di banda
**Scopo:** determina se i parametri di limitazione della larghezza di banda (ad esempio ec_rate ed ec_prebuf) saranno attivi.

I parametri di limitazione della larghezza di banda determinano se la velocità di trasferimento dati per una richiesta del client sarà limitata a un valore prestabilito.

Valore|Risultato
--|--
Enabled|Consente ai server perimetrali di soddisfare richieste di limitazione della larghezza di banda.
Disabled|Fa sì che i server perimetrali ignorino i parametri di limitazione della larghezza di banda. I contenuti richiesti verranno serviti normalmente (senza limitazione della larghezza di banda).

**Comportamento predefinito:** Enabled.

###<a name="bandwidth-throttling"></a>Limitazione larghezza di banda
**Scopo:** limita la larghezza di banda per la risposta fornita dai server perimetrali.

Per impostare correttamente la limitazione della larghezza di banda è necessario che siano definite entrambe le opzioni seguenti.

Opzione|Descrizione
--|--
Kbytes per second (KB al secondo)|Impostare questa opzione sulla larghezza di banda massima (KB al secondo) che è possibile usare per inviare la risposta.
Prebuf seconds (Secondi prebuf)|Impostare questa opzione sul numero di secondi di attesa da parte dei server perimetrali prima di attivare la limitazione della larghezza di banda. Lo scopo di questo intervallo di tempo di larghezza di banda senza restrizioni è quello di impedire a un lettore multimediale di riscontrare problemi di stuttering o buffering a causa della limitazione della larghezza di banda.

**Comportamento predefinito:** Disabled.

###<a name="bypass-cache"></a>Ignora cache
**Scopo:** determina se la richiesta può sfruttare la tecnologia di memorizzazione nella cache.

Valore|Risultato
--|--
Enabled|Fa sì che tutte le richieste giungano al server di origine, anche se i contenuti sono stati precedentemente memorizzati nella cache dei server perimetrali.
Disabled|Fa sì che i server perimetrali memorizzino gli asset nella cache in base ai criteri della cache definiti nelle relative intestazioni di risposta.

**Comportamento predefinito:**

- **HTTP Large:** Disabled

<!---
- **ADN:** Enabled
--->

###<a name="cache-control-header-treatment"></a>Cache Control Header Treatment (Gestione intestazioni Cache-Control)
**Scopo:** controlla la generazione delle intestazioni Cache-Control da parte del server perimetrale quando è attiva la funzionalità External Max-Age (Validità massima esterna).

Il modo più semplice per ottenere questo tipo di configurazione è inserire le funzionalità External Max-Age (Validità massima esterna) e Cache-Control Header Treatment (Gestione intestazioni Cache-Control) nella stessa istruzione.

Valore|Risultato
--|--
Overwrite|Garantisce che vengano eseguite le azioni seguenti:<br/> - Sovrascrivere l'intestazione Cache-Control generata dal server di origine. <br/>- Aggiungere alla risposta l'intestazione Cache-Control prodotta dalla funzionalità External Max-Age (Validità massima esterna)
Pass-through|Assicura che l'intestazione Cache-Control prodotta dalla funzionalità External Max-Age (Validità massima esterna) non venga mai aggiunta alla risposta. <br/> Se il server di origine produce un'intestazione Cache-Control, ne verrà eseguito il pass-through all'utente finale. <br/> Se il server di origine non produce un'intestazione Cache-Control, è possibile che, a causa di questa opzione, l'intestazione della risposta non contenga un'intestazione Cache-Control.
Add if Missing (Aggiungi se mancante)|Se un'intestazione Cache-Control non è stata ricevuta dal server di origine, questa opzione aggiunge l'intestazione Cache-Control prodotta dalla funzionalità External Max-Age (Validità massima esterna). Assicura quindi che a tutti gli asset venga assegnata un'intestazione Cache-Control.
Rimuovere| Questa opzione assicura che con la risposta di intestazione non sia inclusa un'intestazione Cache-Control. Se è già stata assegnata un'intestazione Cache-Control, verrà rimossa dalla risposta di intestazione.

**Comportamento predefinito:** Overwrite.

###<a name="cache-key-query-string"></a>Stringa di query chiave cache
**Scopo:** determina se la chiave di cache includerà o escluderà i parametri della stringa di query associati a una richiesta.

Informazioni chiave:

- Specificare uno o più nomi di parametri della stringa di query. Ogni nome di parametro deve essere delimitato da uno spazio singolo.
- Questa funzionalità determina se nella chiave di cache i parametri della stringa di query verranno inclusi o esclusi. Di seguito vengono fornite informazioni aggiuntive per ogni opzione seguente.

Tipo|Descrizione
--|--
 Includi|  Indica che nella chiave di cache deve essere incluso ogni parametro specificato. Verrà generata una chiave di cache univoca per ogni richiesta in cui sia contenuto un valore univoco per un parametro della stringa di query definito in questa funzionalità. 
 Includi tutto  |Indica che verrà creata una chiave di cache univoca per ogni richiesta a un asset contenente una stringa di query univoca. Questo tipo di configurazione, in genere, non è consigliato poiché può comportare una piccola percentuale di riscontri nella cache e un conseguente aumento del carico sul server di origine, che dovrà gestire un maggior numero di richieste. Questa configurazione consente di duplicare il comportamento di memorizzazione nella cache noto come "unique-cache" nella pagina di memorizzazione nella cache della stringa di query. 
 Escludi | Indica che verranno esclusi dalla chiave di cache solo i parametri specificati. Verranno inclusi invece tutti gli altri parametri della stringa di query. 
 Escludi tutto  |Indica che verranno esclusi dalla chiave di cache tutti i parametri della stringa di query. Questa configurazione consente di duplicare il comportamento di memorizzazione nella cache predefinito, noto come "standard-cache", nella pagina di memorizzazione nella cache della stringa di query. 

Le funzionalità del motore regole HTTP consente di personalizzare il modo in cui viene implementata la memorizzazione nella cache della stringa di query. È possibile specificare, ad esempio, che la memorizzazione nella cache della stringa di query può essere eseguita solo su determinate posizioni o tipi di file.

Se nella pagina di memorizzazione nella cache della stringa di query si vuole duplicare il comportamento di memorizzazione nella cache della stringa di query noto come "no-cache", sarà necessario creare una regola contenente una condizione di corrispondenza URL Query Wildcard (Carattere jolly query URL) e una funzionalità Bypass Cache (Ignora cache). La condizione di corrispondenza URL Query Wildcard (Carattere jolly query URL) deve essere impostata su un asterisco (*).

#### <a name="sample-scenarios"></a>Scenari di esempio

Di seguito è riportato un uso di esempio di questa funzionalità e, in particolare, una richiesta di esempio e la chiave di cache predefinita.

- **Richiesta di esempio:** http://wpc.0001.&lt;Domain&gt;/800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01
- **Chiave di cache predefinita:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Includi

Configurazione di esempio:

- **Tipo:** Includi
- **Parametri:** language

Questo tipo di configurazione genera la chiave di cache del parametro della stringa di query seguente:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Includi tutto

Configurazione di esempio:

- **Tipo:** Includi tutto

Questo tipo di configurazione genera la chiave di cache del parametro della stringa di query seguente:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Escludi

Configurazione di esempio:

- **Tipo:** Escludi
- **Parametri:** sessionid userid

Questo tipo di configurazione genera la chiave di cache del parametro della stringa di query seguente:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Escludi tutto

Configurazione di esempio:

- **Tipo:** Escludi tutto

Questo tipo di configurazione genera la chiave di cache del parametro della stringa di query seguente:

    /800001/Origin/folder/asset.htm

###<a name="cache-key-rewrite"></a>Riscrittura chiave cache
**Scopo:** riscrive la chiave di cache associata a una richiesta.

Una chiave di cache è il percorso relativo che identifica un asset ai fini della memorizzazione nella cache. In altre parole, i server cercano una versione memorizzata nella cache di un asset in base al relativo percorso, così come definito dalla chiave di cache.

Per configurare questa funzionalità è necessario definire entrambe le opzioni seguenti:

Opzione|Descrizione
--|--
Percorso originale| Consente di definire il percorso relativo dei tipi di richieste di cui verrà riscritta la chiave di cache. Un percorso relativo può essere definito selezionando un percorso di origine di base e quindi definendo un modello di espressione regolare.
Nuovo percorso|Consente di definire il percorso relativo della nuova chiave di cache. Un percorso relativo può essere definito selezionando un percorso di origine di base e quindi definendo un modello di espressione regolare. Questo percorso relativo può essere creato dinamicamente tramite l'uso di variabili HTTP
**Comportamento predefinito:** la chiave di cache di una richiesta è determinata dall'URI della richiesta.

###<a name="complete-cache-fill"></a>Completa riempimento cache
**Scopo:** determina ciò che accade quando una richiesta genera un mancato riscontro nella cache parziale in un server perimetrale.

Un mancato riscontro nella cache parziale descrive lo stato della cache relativo a un asset non completamente scaricato in un server perimetrale. Se un asset è stato solo parzialmente memorizzato nella cache di un server perimetrale, la richiesta successiva dell'asset verrà nuovamente inoltrata al server di origine.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.
--->
In genere, un mancato riscontro nella cache parziale si verifica dopo che un utente interrompe un download o in caso di asset che vengono richiesti esclusivamente tramite richieste di intervallo HTTP. Questa funzionalità è particolarmente utile per gli asset di grandi dimensioni che gli utenti in genere non scaricano per intero (ad esempio i video). Ecco perché questa funzionalità è abilitata per impostazione predefinita nella piattaforma HTTP Large ed è disabilitata in tutte le altre piattaforme.

È consigliabile lasciare la configurazione predefinita per la piattaforma HTTP Large, poiché riduce il carico sul server di origine del cliente e aumenta la velocità con cui i clienti possono scaricare i contenuti.

In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa funzionalità non possa essere associata alle seguenti condizioni di corrispondenza: Edge, Cname, Request Header Literal (Valore letterale intestazione richiesta), Request Header Wildcard (Carattere jolly intestazione richiesta), URL Query Literal (Valore letterale query URL) e URL Query Wildcard (Carattere jolly query URL).

Valore|Risultato
--|--
Enabled|Ripristina il comportamento predefinito. Il comportamento predefinito prevede di obbligare il server periferico a inizializzare il recupero in background dell'asset dal server di origine. Al termine di questa operazione, l'asset si trova nella cache locale del server perimetrale.
Disabled|Impedisce a un server perimetrale di eseguire il recupero in background dell'asset. In questo modo, alla successiva richiesta dell'asset dalla stessa area, il server perimetrale ne eseguirà la richiesta dal server di origine del cliente.

**Comportamento predefinito:** Enabled.

###<a name="compress-file-types"></a>Comprimi tipi di file
**Scopo:** definisce i formati di file che verranno compressi nel server.

Un formato di file può essere specificato usando il rispettivo tipo di elemento multimediale Internet (ad esempio, Content-Type). Il tipo di elemento multimediale Internet è costituito dai metadati indipendenti dalla piattaforma che consentono ai server di identificare il formato di file di un asset specifico. Di seguito è riportato un elenco dei tipi di elementi multimediali Internet.

Tipo di elemento multimediale Internet|Descrizione
--|--
text/plain|File di testo normale
text/html| File HTML
text/css|Fogli di stile CSS
application/x-javascript|JavaScript
application/javascript|JavaScript
Informazioni chiave:

- È possibile specificare più tipi di elementi multimediali Internet delimitandoli ciascuno con uno spazio singolo. 
- Questa funzionalità comprimerà solo asset con dimensioni inferiori a 1 MB. Gli asset con dimensioni superiori non verranno compressi dai server.
- Alcuni tipi di contenuti, come le immagini e i contenuti multimediali audio e video (ad esempio JPG, MP3, MP4 e così via), sono già compressi. Un ulteriore compressione di questi tipi di asset, pertanto, non ne diminuirebbe in modo significativo le dimensioni. È consigliabile quindi non abilitare la compressione su questi tipi di asset.
- Non sono supportati i caratteri jolly come gli asterischi.
- Prima di aggiungere questa funzionalità a una regola, assicurarsi di impostare l'opzione di compressione disabilitata nella pagina di compressione relativa alla piattaforma su cui verrà applicata la regola.

###<a name="default-internal-max-age"></a>Max-Age interno predefinito
**Scopo:** determina l'intervallo di validità massima predefinita per la riconvalida della cache dal server perimetrale al server di origine. In altre parole, la quantità di tempo che deve trascorrere prima che un server perimetrale verifichi se un asset memorizzato nella cache corrisponde all'asset archiviato sul server di origine.

Informazioni chiave:

- Questa azione viene eseguita solo per le risposte generate da un server di origine che non ha assegnato un'indicazione di validità massima nell'intestazione Cache-Control o Expires.
- Questa azione non viene eseguita per gli asset che non sono considerati memorizzabili nella cache.
- Questa azione non riguarda le riconvalide della cache dal browser al server perimetrale. Questi tipi di riconvalida, infatti, vengono determinati dalle intestazioni Cache-Control o Expires inviate al browser, che possono essere personalizzate con la funzionalità External Max-Age (Validità massima esterna).
- I risultati di questa azione non producono alcun effetto osservabile sulle intestazioni di risposta e sui contenuti restituiti dai server periferici, ma possono influire sulla quantità di traffico di riconvalida inviato dai server periferici al server di origine.
- Per configurare questa funzionalità:
    - Selezionare il codice di stato per il quale può essere applicata una validità massima interna predefinita.
    - Specificare un valore intero e quindi selezionare l'unità di tempo desiderata (ad esempio secondi, minuti, ore e così via). Questo valore definisce l'intervallo di validità massima interna predefinita.

- Impostare l'unità di tempo su "Off" per assegnare un intervallo di validità massima interna predefinita di 7 giorni alle richieste a cui non è stata assegnata un'indicazione di validità massima nella rispettiva intestazione Cache-Control o Expires.
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa funzionalità non possa essere associata alle seguenti condizioni di corrispondenza: 
    - Edge 
    - CNAME
    - Valore letterale intestazione richiesta
    - Carattere jolly intestazione richiesta
    - Metodo richiesta
    - Valore letterale query URL
    - Carattere jolly query URL

**Valore predefinito:** 7 giorni

###<a name="expires-header-treatment"></a>Gestione intestazione Expires
**Scopo:** controlla la generazione delle intestazioni Expires da un server perimetrale quando è attiva la funzionalità External Max-Age (Validità massima esterna).

Il modo più semplice per ottenere questo tipo di configurazione è inserire le funzionalità External Max-Age (Validità massima esterna) e Expires Header Treatment (Gestione intestazioni Expires) nella stessa istruzione.

Valore|Risultato
--|--
Overwrite|Garantisce che vengano eseguite le azioni seguenti:<br/>- Sovrascrivere l'intestazione Expires generata dal server di origine.<br/>- Aggiungere alla risposta l'intestazione Expires prodotta dalla funzionalità External Max-Age (Validità massima esterna).
Pass-through|Assicura che l'intestazione Expires prodotta dalla funzionalità External Max-Age (Validità massima esterna) non venga mai aggiunta alla risposta. <br/> Se il server di origine produce un'intestazione Expires, ne verrà eseguito il pass-through all'utente finale. <br/>Se il server di origine non produce un'intestazione Expires, è possibile che, a causa di questa opzione, l'intestazione della risposta non contenga un'intestazione Expires.
Add if Missing (Aggiungi se mancante)| Se un'intestazione Expires non è stata ricevuta dal server di origine, questa opzione aggiunge l'intestazione Expires prodotta dalla funzionalità External Max-Age (Validità massima esterna). Assicura quindi che a tutti gli asset venga assegnata un'intestazione Expires.
Rimuovere| Assicura che con la risposta di intestazione non sia inclusa un'intestazione Expires. Se è già stata assegnata un'intestazione Expires, verrà rimossa dalla risposta di intestazione.

**Comportamento predefinito:** Overwrite

###<a name="external-max-age"></a>Max-Age esterno
**Scopo:** determina l'intervallo di validità massima per la riconvalida della cache dal browser al server perimetrale. In altre parole, la quantità di tempo che deve trascorrere prima che un browser cerchi una nuova versione di un asset da un server periferico.

Abilitando questa funzionalità, dai server periferici verranno generate le intestazioni Cache-Control:max-age e Expires e verranno inviate al client HTTP. Per impostazione predefinita, queste intestazioni sovrascriveranno quelle create dal server di origine. Per modificare questo comportamento, tuttavia, è possibile usare le funzionalità Cache-Control Header Treatment (Gestione intestazioni Cache-Control) e Expires Header Treatment (Gestione intestazioni Expires).

Informazioni chiave:

- Questa azione non riguarda le riconvalide della cache dal server periferico al server di origine. Questi tipi di riconvalida, infatti, vengono determinati dalle intestazioni Cache-controllo/Expires ricevute dal server di origine e possono essere personalizzati con le funzionalità Default Internal Max-Age (Validità massima interna predefinita) e Force Internal Max-Age (Forza validità massima interna).
- Configurare questa funzionalità specificando un valore intero e selezionando l'unità di tempo desiderata (ad esempio secondi, minuti, ore e così via).
- Se si imposta questa funzionalità su un valore negativo, il server periferico invia una direttiva Cache-Control:no-cache e un valore temporale Expires impostato nel passato con ogni risposta inviata al browser. Anche nel caso in cui un client HTTP non memorizzi nella cache la risposta, questa impostazione non influisce sulla capacità dei server periferici di memorizzare nella cache la risposta ottenuta dal server di origine.
- Impostando l'unità di tempo su "Off", questa funzionalità viene disabilitata. Delle intestazioni Cache-Control/Expires memorizzate nella cache con la risposta del server di origine viene eseguito il pass-through al browser.

**Comportamento predefinito:** Off

###<a name="force-internal-max-age"></a>Forza Max-Age interno
**Scopo:** determina l'intervallo di validità massima per la riconvalida della cache dal server perimetrale al server di origine. In altre parole, la quantità di tempo che deve trascorrere prima che un server perimetrale verifichi se un asset memorizzato nella cache corrisponde all'asset archiviato sul server di origine.

Informazioni chiave:

- Questa funzionalità sostituirà l'intervallo di validità massima definito nelle intestazioni Cache-Control o Expires generate da un server di origine.
- Questa funzionalità non riguarda le riconvalide della cache dal browser al server perimetrale. Questi tipi di riconvalida, infatti, vengono determinati dalle intestazioni Cache-Control o Expires inviate al browser.
- Questa funzionalità non produce alcun effetto osservabile sulla risposta fornita da un server perimetrale al richiedente. Può tuttavia influire sulla quantità di traffico di riconvalida inviato dai server perimetrali al server di origine.
- Per configurare questa funzionalità:
    - Selezionare il codice di stato per il quale verrà applicato un intervallo di validità massima interna.
    - Specificare un valore intero e selezionare l'unità di tempo desiderata (ad esempio secondi, minuti, ore e così via). Questo valore definisce l'intervallo di validità massima della richiesta.

- Impostando l'unità di tempo su "Off", questa funzionalità viene disabilitata e agli asset non verrà assegnato un intervallo di validità massima interna. Se nell'intestazione originale non sono contenute istruzioni di memorizzazione nella cache, l'asset verrà memorizzato nella cache in base all'impostazione attiva nella funzionalità Default Internal Max-Age (Validità massima interna predefinita).
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa funzionalità non possa essere associata alle seguenti condizioni di corrispondenza: 
    - Edge 
    - CNAME
    - Valore letterale intestazione richiesta
    - Carattere jolly intestazione richiesta
    - Metodo richiesta
    - Valore letterale query URL
    - Carattere jolly query URL

**Comportamento predefinito:** Off

###<a name="h264-support-http-progressive-download"></a>Supporto H.264 (download progressivo HTTP)
**Scopo:** determina i tipi di formati di file H.264 che possono essere usati per lo streaming di contenuti.

Informazioni chiave:

- Nell'opzione Estensioni file definire un set delimitato da spazi di estensioni di file H.264 consentite. L'opzione Estensioni file sostituirà il comportamento predefinito. Garantire il supporto di file MP4 e F4V includendo queste estensioni durante la configurazione dell'opzione. 
- Assicurarsi di includere un punto per ogni estensione di file aggiunta (ad esempio, .mp4 .f4v).

**Comportamento predefinito:** il download progressivo HTTP supporta file multimediali MP4 e F4V per impostazione predefinita.

###<a name="honor-no-cache-request"></a>Rispetta richiesta no-cache
**Scopo:**: determina se le richieste no-cache di un client HTTP verranno inoltrate al server di origine.

Una richiesta no-cache si verifica quando il client HTTP invia un'intestazione Cache-Control:no-cache e/o Pragma:no-cache nella richiesta HTTP.

Valore|Risultato
--|--
Enabled|Consente alle richieste no-cache di un client HTTP di essere inoltrate al server di origine e al server di origine di restituire al client HTTP le intestazioni di risposta e il corpo tramite il server periferico.
Disabled|Ripristina il comportamento predefinito. Il comportamento predefinito prevede di impedire alle richieste no-cache di essere inoltrate al server di origine.

Per tutto il traffico di produzione, è consigliabile lasciare questa funzionalità nello stato disabilitato predefinito. In caso contrario, i server di origine non risulteranno protetti da eventuali utenti finali che attivino inavvertitamente molte richieste no-cache durante l'aggiornamento di pagine Web o dai numerosi lettori multimediali di uso comune codificati per inviare un'intestazione no-cache con ogni richiesta video. Se applicata a determinate directory di staging o testing non di produzione, tuttavia, questa funzionalità consente di effettuare il pull on-demand di contenuti aggiornati dal server di origine.

Lo stato della cache visualizzato per una richiesta che può essere inoltrata a un server di origine grazie a questa funzionalità è: TCP_Client_Refresh_Miss. Il report sugli stati della cache, disponibile nel modulo di report principale, fornisce informazioni statistiche in base allo stato della cache. Consente quindi di monitorare il numero e la percentuale di richieste inoltrate a un server di origine a causa di questa funzionalità.

**Comportamento predefinito:** Disabled.

###<a name="ignore-origin-no-cache"></a>Ignore Origin no-cache (Ignora origine No-Cache)
**Scopo:** determina se la rete CDN ignorerà le seguenti direttive gestite da un server di origine.

- Cache-Control: private
- Cache-Control: no-store
- Cache-Control: no-cache
- Pragma: no-cache

Informazioni chiave:

- Configurare questa funzionalità definendo un elenco delimitato da spazi di codici di stato per i quali dovranno essere ignorate le direttive sopra riportate.
- I codici di stato validi per questa funzionalità sono: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 e 505.
- Disabilitare questa funzionalità impostandola su un valore vuoto.
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa funzionalità non possa essere associata alle seguenti condizioni di corrispondenza: 
    - Edge 
    - CNAME
    - Valore letterale intestazione richiesta
    - Carattere jolly intestazione richiesta
    - Metodo richiesta
    - Valore letterale query URL
    - Carattere jolly query URL

**Comportamento predefinito:** il comportamento prevede di rispettare le direttive sopra riportate.

###<a name="ignore-unsatisfiable-ranges"></a>Ignora gli intervalli che non è possibile soddisfare 
**Scopo:** determina la risposta che verrà restituita ai client quando una richiesta genera un codice di stato 416 Impossibile attenersi all'intervallo richiesto.

Per impostazione predefinita, questo codice di stato viene restituito quando la richiesta di intervallo di byte specificata non può essere soddisfatta da un server periferico e non è stato specificato un campo di intestazione di richiesta If-Range.

Valore|Risultato
-|-
Enabled|Impedisce ai server periferici di rispondere a una richiesta di intervallo di byte non valida con un codice di stato 416 - Impossibile attenersi all'intervallo richiesto. I server forniranno invece l'asset richiesto e restituiranno al client il codice 200 - OK.
Disabled|Ripristina il comportamento predefinito. Il comportamento predefinito prevede di rispettare il codice di stato 416 - Impossibile attenersi all'intervallo richiesto.

**Comportamento predefinito:** Disabled.

###<a name="internal-max-stale"></a>Max-Stale interno
**Scopo:** controlla per quanto tempo, dopo la normale scadenza, un asset memorizzato nella cache può essere servito da un server perimetrale quando il server perimetrale non è in grado di riconvalidare l'asset memorizzato nella cache con il server di origine.

In genere, quando l'intervallo di validità massima di un asset scade, il server periferico invia una richiesta di riconvalida al server di origine. Il server di origine risponde quindi con un codice di stato 304 - Non modificato per fornire al server periferico un lease aggiornato sull'asset memorizzato nella cache o con un codice 200 - OK per fornire al server periferico una versione aggiornata dell'asset memorizzato nella cache.

Se il server periferico non è in grado di stabilire una connessione con il server di origine durante il tentativo di riconvalida, la funzionalità Internal Max-Stale (Tempo di non aggiornamento massimo interno) controlla se e per quanto tempo il server periferico può continuare a servire l'asset non aggiornato.

Questo intervallo di tempo inizia nel momento in cui scade la validità massima dell'asset, non quando la riconvalida ha esito negativo. Il periodo massimo durante il quale un asset può essere servito senza riconvalida corrisponde quindi alla quantità di tempo determinata dalla combinazione dei valori Max-Age (Validità massima) e Max-Stale (Tempo di non aggiornamento massimo). Se, ad esempio, un asset è stato memorizzato nella cache alle 9.00 con una validità massima di 30 minuti e un tempo di non aggiornamento massimo di 15 minuti, in caso di tentativo di riconvalida non riuscito alle 9.44 un utente finale riceverebbe l'asset memorizzato nella cache non aggiornato, mentre in caso di tentativo di riconvalida non riuscito alle 9.46 l'utente finale riceverebbe un codice di stato 504 - Timeout gateway.

Qualsiasi valore configurato per questa funzionalità viene sostituito dall'intestazione Cache-Control:must-revalidate o Cache-Control:proxy-revalidate ricevuta dal server di origine. Se una di queste intestazioni viene ricevuta dal server di origine all'inizio della procedura di memorizzazione dell'asset nella cache, il server periferico non servirà un asset memorizzato nella cache non aggiornato. In questo caso, se il server periferico non è in grado di eseguire la riconvalida con il server di origine alla scadenza dell'intervallo di validità massima dell'asset, il server periferico restituirà un codice di stato 504 - Timeout gateway.

Informazioni chiave:

- Per configurare questa funzionalità:
    - Selezionare il codice di stato in base al quale verrà applicato il tempo di non aggiornamento massimo.
    - Specificare un valore intero e quindi selezionare l'unità di tempo desiderata (ad esempio secondi, minuti, ore e così via). Questo valore definisce il tempo di non aggiornamento massimo interno che verrà applicato.

- Impostando l'unità di tempo su "Off", questa funzionalità viene disabilitata. Un asset memorizzato nella cache non verrà servito dopo la normale scadenza.
- In base al modo in cui vengono monitorate le impostazioni della cache, è possibile che questa funzionalità non possa essere associata alle seguenti condizioni di corrispondenza: 
    - Edge 
    - CNAME
    - Valore letterale intestazione richiesta
    - Carattere jolly intestazione richiesta
    - Metodo richiesta
    - Valore letterale query URL
    - Carattere jolly query URL

**Comportamento predefinito:** 2 minuti

###<a name="partial-cache-sharing"></a>Condivisione cache parziale
**Scopo:** determina se una richiesta può generare contenuti parzialmente memorizzati nella cache.

Questa cache parziale può essere quindi usata per soddisfare nuove richieste dei contenuti desiderati fino a quando non risultano completamente memorizzati nella cache.

Valore|Risultato
-|-
Enabled|Le richieste possono generare contenuti parzialmente memorizzati nella cache.
Disabled|Le richieste possono generare solo una versione completamente memorizzata nella cache dei contenuti richiesti.

**Comportamento predefinito:** Disabled.

###<a name="prevalidate-cached-content"></a>Preconvalida contenuto memorizzato nella cache
**Scopo:** determina se i contenuti memorizzati nella cache saranno idonei per la riconvalida anticipata prima della scadenza della durata (TTL).

Definire l'intervallo di tempo prima della scadenza della durata dei contenuti richiesti durante il quale i contenuti saranno idonei per la riconvalida anticipata.

Informazioni chiave:

- Se si imposta l'unità di tempo su "Off", la riconvalida dovrà essere eseguita dopo la scadenza della durata dei contenuti memorizzati nella cache. Non è necessario specificare alcun valore di tempo, poiché verrebbe ignorato.

**Comportamento predefinito:** Off. La riconvalida può aver luogo solo dopo la scadenza della durata dei contenuti memorizzati nella cache.

###<a name="refresh-zero-byte-cache-files"></a>Aggiorna i file della cache con zero byte
**Scopo:** determina come viene gestita dai server perimetrali una richiesta da parte di un client HTTP di un asset della cache con 0 byte.

I valori validi sono:

Valore|Risultato
--|--
Enabled|Fa sì che il server periferico recuperi nuovamente l'asset dal server di origine.
Disabled|Ripristina il comportamento predefinito. Il comportamento predefinito prevede di rendere disponibili asset di cache validi su richiesta.
Questa funzionalità non è necessaria per eseguire correttamente operazioni di memorizzazione nella cache o distribuzione di contenuti, ma può essere utile come soluzione alternativa. I generatori di contenuti dinamici nei server di origine, ad esempio, possono determinare inavvertitamente l'invio di risposte con 0 byte ai server periferici. Questi tipi di risposte, in genere, vengono memorizzati nella cache dai server periferici. Presupponendo che una risposta con 0 byte non sia mai una risposta valida 

per questo tipo di contenuti, questa funzionalità può impedire che ai client vengano serviti questi tipi di asset.

**Comportamento predefinito:** Disabled.

###<a name="set-cacheable-status-codes"></a>Imposta codici di stato inseribile nella cache
**Scopo:** definisce il set di codici di stato che possono generare contenuti memorizzati nella cache.

Per impostazione predefinita, la memorizzazione nella cache è abilitata solo per le risposte 200 - OK.

Definire un set delimitato da spazi dei codici di stato desiderati.

Informazioni chiave:

- Abilitare anche la funzionalità Ignore Origin No-Cache (Ignora origine No-Cache). In caso contrario, è possibile che le risposte diverse da&200; - OK non vengano memorizzate nella cache.
- I codici di stato validi per questa funzionalità sono: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 e 505.
- Questa funzionalità non può essere usata per disabilitare la memorizzazione nella cache delle risposte che generano un codice di stato 200 - OK.

**Comportamento predefinito:** la memorizzazione nella cache è abilitata solo per le risposte che generano un codice di stato 200 - OK.
###<a name="stale-content-delivery-on-error"></a>Distribuzione di contenuto non aggiornato in caso di errore
**Scopo:** 

Determina se il contenuto scaduto memorizzato nella cache verrà distribuito quando si verifica un errore durante la riconvalida della cache o quando si recupera il contenuto richiesto dal server di origine del cliente.

Valore|Risultato
-|-
Enabled|Se si verifica un errore durante una connessione a un server di origine, al richiedente vengono serviti contenuti non aggiornati.
Disabled|L'errore del server di origine viene inoltrato al richiedente.

**Comportamento predefinito:** Disabled

###<a name="stale-while-revalidate"></a>Client non aggiornato durante la riconvalida
**Scopo:** migliora le prestazioni consentendo ai server perimetrali di servire contenuti non aggiornati al richiedente mentre è in corso la riconvalida.

Informazioni chiave:

- Il comportamento di questa funzionalità varia in base all'unità di tempo selezionata.
    - **Unità di tempo:** specificare un intervallo di tempo e selezionare un'unità di tempo (ad esempio secondi, minuti, ore e così via) per consentire la distribuzione di contenuti non aggiornati. Questo tipo di configurazione consente alla rete CDN di estendere l'intervallo di tempo durante il quale può distribuire contenuti prima di richiedere la convalida in base alla formula seguente: **Durata** + **Stale While Revalidate Time** (Client non aggiornato durante la riconvalida) 
    - **Off:** selezionare "Off" per richiedere la riconvalida prima che possa essere servita una richiesta di contenuti non aggiornati.
        - Non specificare un intervallo di tempo poiché non è applicabile e verrebbe ignorato.

**Comportamento predefinito:** Off. La riconvalida deve aver luogo prima che possano essere serviti i contenuti richiesti.

###<a name="comment"></a>Commento
**Scopo:** consente di aggiungere una nota all'interno di una regola.

Uno dei possibili usi di questa funzionalità è quello di fornire informazioni aggiuntive sullo scopo generale di una regola o sui motivi per cui alla regola è stata aggiunta una determinata funzionalità o condizione di corrispondenza.

Informazioni chiave:

- Non possono essere specificati più di 150 caratteri.
- Assicurarsi di usare solo caratteri alfanumerici.
- Questa funzionalità non influisce sul comportamento della regola. È stata concepita al solo scopo di mettere a disposizione un'area in cui poter fornire informazioni da usare come riferimento futuro o per risolvere un problema relativo alla regola.
 
## <a name="headers"></a>headers

Queste funzionalità sono progettate per aggiungere, modificare o eliminare le intestazioni dalla richiesta o dalla risposta.

Nome | Scopo
-----|--------
Intestazione di risposta Age | Determina se un'intestazione di risposta Age verrà inclusa nella risposta inviata al richiedente.
Intestazioni di risposta di debug per la cache | Determina se una risposta può includere l'intestazione di risposta X-EC-Debug che fornisce informazioni sul criterio di cache per l'asset richiesto.
Modifica intestazione richiesta client | Sovrascrive, aggiunge o elimina un'intestazione da una richiesta.
Modificare intestazione risposta client | Sovrascrive, aggiunge o elimina un'intestazione da una risposta.
Imposta intestazione personalizzata IP client | Consente di aggiungere l'indirizzo IP del client richiedente alla richiesta come un'intestazione personalizzata.

###<a name="age-response-header"></a>Intestazione di risposta Age
**Scopo**: determina se un'intestazione di risposta Age verrà inclusa nella risposta inviata al richiedente.
Valore|Risultato
--|--
Enabled | L'intestazione di risposta Age verrà inclusa nella risposta inviata al richiedente.
Disabled | L'intestazione di risposta Age verrà esclusa dalla risposta inviata al richiedente.

**Comportamento predefinito:**: Disabled.

###<a name="debug-cache-response-headers"></a>Intestazioni di risposta di debug per la cache
**Scopo:** determina se una risposta può includere l'intestazione di risposta X-EC-Debug che fornisce informazioni sui criteri di cache per l'asset richiesto.

Le intestazioni di risposta di debug per la cache vengono incluse nella risposta quando sono soddisfatte entrambe le condizioni seguenti:

- Nella richiesta desiderata è stata abilitata la funzionalità relativa alle intestazioni di risposta di debug per la cache.
- La richiesta precedente definisce il set di intestazioni di risposta di debug per la cache incluso nella risposta.

Le intestazioni di risposta di debug per la cache possono essere richieste includendo nella richiesta l'intestazione seguente e le direttive desiderate:

X-EC-Debug: _Direttiva1_,_Direttiva2_,_DirettivaN_

**Esempio:**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Valore|Risultato
-|-
Enabled|Le richieste di intestazioni di risposta di debug per la cache restituiranno una risposta che include l'intestazione X-EC-Debug.
Disabled|L'intestazione di risposta X-EC-Debug verrà esclusa dalla risposta.

**Comportamento predefinito:** Disabled.

###<a name="modify-client-response-header"></a>Modificare intestazione risposta client
**Scopo:** ogni richiesta contiene un set di [intestazioni di richiesta]() che la descrivono. Questa funzionalità può:

- Aggiungere o sovrascrivere il valore assegnato a un'intestazione di richiesta. Se l'intestazione di richiesta specificata non esiste, questa funzionalità la aggiungerà alla richiesta.
- Eliminare dalla richiesta un'intestazione di richiesta.

Le richieste inoltrate a un server di origine rifletteranno le modifiche apportate da questa funzionalità.

Su un'intestazione di richiesta è possibile eseguire una delle azioni seguenti:

Opzione|Descrizione|Esempio
-|-|-
Append|Il valore specificato verrà aggiunto alla fine del valore dell'intestazione di richiesta esistente.|**Valore intestazione richiesta (Client):**Value1 <br/> **Valore intestazione richiesta (Motore regole HTTP):** Value2 <br/>**Valore nuova intestazione di richiesta:** Value1Value2
Overwrite|Il valore dell'intestazione di richiesta verrà impostato sul valore specificato.|**Valore intestazione richiesta (Client):**Value1 <br/>**Valore intestazione richiesta (Motore regole HTTP):** Value2 <br/>**Valore nuova intestazione richiesta:** Value2 <br/>
Elimina|Elimina l'intestazione di richiesta specificata.|**Valore intestazione richiesta (Client):**Value1 <br/> **Configurazione Modify Client Request Header (Modifica intestazione richiesta client):** elimina l'intestazione di richiesta in questione. <br/>**Risultato:** l'intestazione di richiesta specificata non verrà inoltrata al server di origine.

Informazioni chiave:

- Assicurarsi che il valore specificato nell'opzione Nome corrisponda esattamente all'intestazione di richiesta desiderata.
- Ai fini dell'identificazione di un'intestazione non viene fatta distinzione tra maiuscole e minuscole. Per identificare l'intestazione Cache-Control, ad esempio, è possibile usare una delle varianti di nome seguenti:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Assicurarsi di usare solo caratteri alfanumerici, trattini o caratteri di sottolineatura quando si specifica un nome di intestazione.
- L'eliminazione di un'intestazione impedisce che venga inoltrata a un server di origine dai server perimetrali.
- Le intestazioni seguenti sono riservate e non possono essere modificate da questa funzionalità:
    - forwarded
    - host
    - via
    - Avviso
    - x-forwarded-for
    - Tutti i nomi di intestazione che iniziano con "x-ec" sono riservati.

###<a name="modify-client-response-header"></a>Modificare intestazione risposta client
Ogni risposta contiene un set di [intestazioni di risposta]() che la descrivono. Questa funzionalità può:

- Aggiungere o sovrascrivere il valore assegnato a un'intestazione di risposta. Se l'intestazione di richiesta specificata non esiste, questa funzionalità la aggiungerà alla risposta.
- Eliminare dalla risposta un'intestazione di risposta.

Per impostazione predefinita, i valori delle intestazioni di risposta vengono definiti da un server di origine e dai server periferici.

Su un'intestazione di risposta è possibile eseguire una delle azioni seguenti:

Opzione|Descrizione|Esempio
-|-|-
Append|Il valore specificato verrà aggiunto alla fine del valore dell'intestazione di richiesta esistente.|**Valore intestazione risposta (Client):**Value1 <br/> **Valore intestazione risposta (Motore regole HTTP):** Value2 <br/>**Valore nuova intestazione risposta:** Value1Value2
Overwrite|Il valore dell'intestazione di richiesta verrà impostato sul valore specificato.|**Valore intestazione risposta (Client):**Value1 <br/>**Valore intestazione risposta (Motore regole HTTP):** Value2 <br/>**Valore nuova intestazione risposta:** Value2 <br/>
Elimina|Elimina l'intestazione di richiesta specificata.|**Valore intestazione richiesta (Client):**Value1 <br/> **Configurazione Modify Client Request Header (Modifica intestazione richiesta client):** elimina l'intestazione di risposta in questione. <br/>**Risultato:** l'intestazione di risposta specificata non verrà inoltrata al richiedente.

Informazioni chiave:

- Assicurarsi che il valore specificato nell'opzione Nome corrisponda esattamente all'intestazione di risposta desiderata. 
- Ai fini dell'identificazione di un'intestazione non viene fatta distinzione tra maiuscole e minuscole. Per identificare l'intestazione Cache-Control, ad esempio, è possibile usare una delle varianti di nome seguenti:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- L'eliminazione di un'intestazione impedisce che venga inoltrata al richiedente.
- Le intestazioni seguenti sono riservate e non possono essere modificate da questa funzionalità:
    - accept-encoding
    - age
    - connection
    - content-encoding
    - content-length
    - content-range
    - date
    - server
    - trailer
    - transfer-encoding
    - Aggiornamento
    - vary
    - via
    - Avviso
    - Tutti i nomi di intestazione che iniziano con "x-ec" sono riservati.

###<a name="set-client-ip-custom-header"></a>Imposta intestazione personalizzata IP client
**Scopo:** aggiunge un'intestazione personalizzata che identifica il client richiedente in base all'indirizzo IP della richiesta.

L'opzione Nome intestazione definisce il nome dell'intestazione di richiesta personalizzata in cui verrà archiviato l'indirizzo IP del client.

Questa funzionalità consente a un server di origine del cliente di trovare gli indirizzi IP dei client tramite un'intestazione di richiesta personalizzata. Se la richiesta viene gestita dalla cache, al server di origine non verrà comunicato l'indirizzo IP del client. È consigliabile quindi usare questa funzionalità con reti ADN e asset che non verranno memorizzati nella cache.

Assicurarsi che il nome di intestazione specificato non coincida con nessuno dei nomi seguenti:

- Nomi di intestazioni di richiesta standard. L'elenco dei nomi di intestazioni standard è disponibile in [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Nomi di intestazioni riservate:
    - forwarded-for
    - host
    - vary
    - via
    - Avviso
    - x-forwarded-for
    - Tutti i nomi di intestazione che iniziano con "x-ec" sono riservati.
 
## <a name="logs"></a>Log

Queste funzionalità sono progettate per personalizzare i dati archiviati nei file di log non elaborati.

Nome | Scopo
-----|--------
Campo 1 log personalizzato | Determina il formato e il contenuto che verranno assegnati al campo di log personalizzato in un file di log non elaborato.
Stringa di query log | Determina se una stringa di query verrà archiviata insieme all'URL nei log di accesso.

###<a name="custom-log-field-1"></a>Campo 1 log personalizzato
**Scopo:** determina il formato e i contenuti che verranno assegnati al campo di log personalizzato in un file di log non elaborato.

Lo scopo principale di questo campo personalizzato è quello di consentire all'utente di determinare i valori delle intestazioni di richiesta e di risposta che verranno archiviati nei file di log.

Per impostazione predefinita, il campo del log personalizzato è denominato "x-ec_custom-1". Questo nome, tuttavia, può essere personalizzato dalla pagina delle [impostazioni del file di log non elaborato]().

Di seguito è definita la formattazione da usare per specificare le intestazioni di richiesta e di risposta.

Tipo di intestazione|Format|Esempi
-|-|-
Intestazione di richiesta|%{[RequestHeader]()}[i]() | %{Accept-Encoding}i <br/> {Referer}i <br/> %{Authorization}i
Intestazione di risposta|%{[ResponseHeader]()}[o]()| %{Age}o <br/> %{Content-Type}o <br/> %{Cookie}o

Informazioni chiave:

- Un campo di log personalizzato può contenere qualsiasi combinazione di campi di intestazione e testo normale.
- I caratteri validi per questo campo comprendono: caratteri alfanumerici (ad esempio 0-9, a-z e A-Z), trattini, due punti, punti e virgola, apostrofi, virgole, punti, caratteri di sottolineatura, segni di uguale, parentesi, parentesi quadre e spazi. Il simbolo di percentuale e le parentesi graffe sono consentiti solo se vengono usati per specificare un campo di intestazione.
- L'ortografia di ogni campo di intestazione specificato deve corrispondere esattamente al nome di intestazione di richiesta/risposta desiderato.
- Se si vuole specificare più intestazioni, è consigliabile usare un separatore per indicare ogni intestazione. Per ogni intestazione, ad esempio, è possibile usare un'abbreviazione. Di seguito è riportata una sintassi di esempio.
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o 

**Valore predefinito:** -

###<a name="log-query-string"></a>Stringa di query log
**Scopo:** determina se una stringa di query verrà archiviata insieme all'URL nei log di accesso.

Valore|Risultato
-|-
Enabled|Consente l'archiviazione di stringhe di query durante la registrazione degli URL in un log di accesso. Se un URL non contiene una stringa di query, questa opzione non produrrà alcun effetto.
Disabled|Ripristina il comportamento predefinito. Il comportamento predefinito prevede di ignorare le stringhe di query durante la registrazione degli URL in un log di accesso.

**Comportamento predefinito:** Disabled.

<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

###Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled
 

###Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin"></a>Origine

Queste funzionalità sono progettate per controllare in che modo la rete CDN comunica con un server di origine.

Nome | Scopo
-----|--------
Numero massimo di richieste Keep-Alive | Definisce il numero massimo di richieste per una connessione Keep-Alive prima della chiusura.
Intestazioni speciali proxy | Definisce il set di intestazioni di richiesta specifiche della rete CDN che verranno inoltrate da un server perimetrale a un server di origine.


###<a name="maximum-keep-alive-requests"></a>Numero massimo di richieste Keep-Alive
**Scopo:** definisce il numero massimo di richieste per una connessione Keep-Alive prima della chiusura.

L'impostazione del numero massimo di richieste su un valore basso è fortemente sconsigliata e può influire negativamente sulle prestazioni.

Informazioni chiave:

- Specificare questo valore come un numero intero.
- Non includere virgole o punti nel valore specificato.

**Valore predefinito:** 10.000 richieste

###<a name="proxy-special-headers"></a>Intestazioni speciali proxy
**Scopo:** definisce il set di [intestazioni di richiesta specifiche della rete CDN]() che verranno inoltrate da un server perimetrale a un server di origine.

Informazioni chiave:

- Ogni intestazione di richiesta specifica della rete CDN definita nell'ambito di questa funzionalità verrà inoltrata a un server di origine.
- Impedire che un'intestazione di richiesta specifica della rete CDN venga inviata a un server di origine rimuovendola dall'elenco.

**Comportamento predefinito:** tutte le [intestazioni di richiesta specifiche della rete CDN]() verranno inoltrate al server di origine.

## <a name="specialty"></a>Funzionalità specializzate

Queste funzionalità offrono caratteristiche avanzate che devono essere usate solo dagli utenti esperti.

Nome | Scopo
-----|--------
Metodi HTTP inseribile nella cache | Determina il set di metodi HTTP aggiuntivi che possono essere memorizzati nella cache nella rete.
Dimensioni corpo richiesta inseribile nella cache | Definisce la soglia per determinare se una risposta POST può essere memorizzata nella cache.

###<a name="cacheable-http-methods"></a>Metodi HTTP inseribile nella cache
**Scopo:** determina il set di metodi HTTP aggiuntivi che possono essere memorizzati nella cache nella rete.

Informazioni chiave:

- Questa funzionalità presuppone che le risposte GET vengano sempre memorizzate nella cache. Di conseguenza, è opportuno non includere il metodo GET HTTP quando si imposta questa funzionalità.
- Questa funzionalità supporta solo il metodo POST HTTP. Per abilitare la memorizzazione nella cache della risposta POST, impostare questa funzionalità su POST. 
- Per impostazione predefinita, vengono memorizzate nella cache solo le richieste con un corpo di dimensioni inferiori a 14 Kb. Usare la funzionalità Cacheable Request Body Size (Dimensioni corpo richiesta inseribile nella cache) per impostare le dimensioni massime del corpo della richiesta.

**Comportamento predefinito:** vengono memorizzate nella cache solo le risposte GET.

###<a name="cacheable-request-body-size"></a>Dimensioni corpo richiesta inseribile nella cache

**Scopo:** definisce la soglia per determinare se una risposta POST può essere memorizzata nella cache.

Questa soglia viene determinata specificando la dimensione massima del corpo della richiesta. Non verranno memorizzate nella cache le richieste il cui corpo supera le dimensioni specificate.

Informazioni chiave:

- Questa funzionalità è applicabile solo se le risposte POST sono idonee per la memorizzazione nella cache. Usare la funzionalità Cacheable HTTP Methods (Metodi HTTP inseribili nella cache) per abilitare la memorizzazione nella cache di richieste POST.
- Il corpo della richiesta viene preso in considerazione per:
    - Valori x-www-form-urlencoded
    - Garantire una chiave di cache univoca
- La definizione di un valore molto alto per le dimensioni massime del corpo della richiesta può rallentare le prestazioni in fase di distribuzione dei contenuti.
    - **Valore consigliato:** 14 Kb
    - **Valore minimo:** 1 Kb

**Comportamento predefinito:** 14 Kb
 
## <a name="url"></a>URL

Queste funzionalità consentono il reindirizzamento o la riscrittura di una richiesta in un URL diverso.

Nome | Scopo
-----|--------
Segui reindirizzamenti | Determina se le richieste possono essere reindirizzate al nome host definito nell'intestazione Location restituita da un server di origine del cliente.
Reindirizzamento URL | Reindirizza le richieste tramite l'intestazione Location.
Riscrittura URL  | Riscrive l'URL della richiesta.

###<a name="follow-redirects"></a>Segui reindirizzamenti
**Scopo:** determina se le richieste possono essere reindirizzate al nome host definito nell'intestazione Location restituita da un server di origine del cliente.

Informazioni chiave:

- Le richieste possono essere reindirizzate solo ai CNAME periferici che corrispondono alla stessa piattaforma.

Valore|Risultato
-|-
Enabled|Le richieste possono essere reindirizzate.
Disabled|Le richieste non verranno reindirizzate.

**Comportamento predefinito:** Disabled.
###<a name="url-redirect"></a>Reindirizzamento URL
**Scopo:** reindirizza le richieste tramite l'intestazione Location.

Per configurare questa funzionalità è necessario impostare le opzioni seguenti:

Opzione|Descrizione
-|-
Codice|Selezionare il codice di risposta che verrà restituito al richiedente.
Source & Pattern (Origine e modello)| Queste impostazioni definiscono un modello di URI di richiesta che identifica il tipo di richieste che possono essere reindirizzate. Verranno reindirizzate solo le richieste il cui URL soddisfa entrambi i criteri seguenti: <br/> <br/> **Origine** (o un punto di accesso dei contenuti): selezionare un percorso relativo che identifica un server di origine. È costituito dalla sezione "/XXXX/" e dal nome dell'endpoint. <br/> **Origine (modello):** deve essere definito un modello che identifica le richieste in base al percorso relativo. Questo modello di espressione regolare deve definire un percorso che inizia immediatamente dopo il punto di accesso dei contenuti selezionato in precedenza (vedere sopra). <br/> - Assicurarsi che i criteri dell'URI di richiesta (ad esempio origine e modello) definiti in precedenza non entrino in conflitto con le condizioni di corrispondenza definite per questa funzionalità. <br/> - Assicurarsi di specificare un modello. Se si usa un valore vuoto come modello, la corrispondenza si verificherà solo con le richieste inviate alla cartella radice del server di origine selezionato (ad esempio, http://cdn.mydomain.com/).
Destination| Definire l'URL a cui verranno reindirizzate le richieste precedenti. <br/> Costruire l'URL in modo dinamico usando: <br/> - Un modello di espressione regolare <br/>- Variabili HTTP <br/> Sostituire nel modello di destinazione i valori acquisiti nel modello di origine usando $_n_ dove _n_ identifica un valore in base all'ordine in cui è stato acquisito. Ad esempio, $1 rappresenta il primo valore acquisito nel modello di origine e $2 rappresenta il secondo valore. <br/> 
È consigliabile usare un URL assoluto, poiché l'uso di un URL relativo può reindirizzare gli URL CDN su un percorso non valido.

**Scenario di esempio**

In questo esempio viene descritto come reindirizzare un URL CNAME periferico che viene risolto in questo URL CDN di base: http://marketing.azureedge.net/brochures

Le richieste idonee verranno reindirizzate a questo URL CNAME periferico di base: http://cdn.mydomain.com/resources

Questo reindirizzamento URL può essere ottenuto tramite la configurazione seguente: ![](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Punti principali:**

- La funzionalità Reindirizzamento URL definisce gli URL di richiesta che verranno reindirizzati. Non sono quindi necessarie condizioni di corrispondenza aggiuntive. Anche se la condizione di corrispondenza è stata definita come "Always" (Sempre), verranno reindirizzate solo le richieste che puntano alla cartella "brochures" nell'origine cliente "marketing". 
- Tutte le richieste corrispondenti verranno reindirizzate all'URL CNAME periferico definito nell'opzione Destinazione. 
    - Scenario di esempio #1: 
        - Richiesta di esempio (URL CDN): http://marketing.azureedge.net/brochures/widgets.pdf 
        - URL richiesta (dopo il reindirizzamento): http://cdn.mydomain.com/resources/widgets.pdf  
    - Scenario di esempio #2: 
        - Richiesta di esempio (URL CDN periferico): http://marketing.mydomain.com/brochures/widgets.pdf 
        - URL richiesta (dopo il reindirizzamento): http://cdn.mydomain.com/resources/widgets.pdf  Sample scenario
    - Scenario di esempio #3: 
        - Richiesta di esempio (URL CNAME periferico): http://brochures.mydomain.com/campaignA/final/productC.ppt 
        - URL richiesta (dopo il reindirizzamento): http://cdn.mydomain.com/resources/campaignA/final/productC.ppt  
- Nell'opzione Destinazione è stata usata la variabile Request Scheme (Schema richiesta) (%{scheme}) in modo che lo schema della richiesta rimanga invariato dopo il reindirizzamento.
- I segmenti di URL acquisiti dalla richiesta vengono aggiunti al nuovo URL tramite "$1."
 
###<a name="url-rewrite"></a>Riscrittura URL
**Scopo:** riscrive l'URL della richiesta.

Informazioni chiave:

- Per configurare questa funzionalità è necessario impostare le opzioni seguenti:

Opzione|Descrizione
-|-
 Source & Pattern (Origine e modello) | Queste impostazioni definiscono un modello di URI di richiesta che identifica il tipo di richieste che possono essere riscritte. Verranno riscritte solo le richieste il cui URL soddisfa entrambi i criteri seguenti: <br/>     - **Origine (o un punto di accesso dei contenuti):** selezionare un percorso relativo che identifica un server di origine. È costituito dalla sezione "/XXXX/" e dal nome dell'endpoint. <br/> - **Origine (modello):** deve essere definito un modello che identifica le richieste in base al percorso relativo. Questo modello di espressione regolare deve definire un percorso che inizia immediatamente dopo il punto di accesso dei contenuti selezionato in precedenza (vedere sopra). <br/> Assicurarsi che i criteri dell'URI di richiesta (ad esempio origine e modello) definiti in precedenza non entrino in conflitto con le condizioni di corrispondenza definite per questa funzionalità. Assicurarsi di specificare un modello. Se si usa un valore vuoto come modello, la corrispondenza si verificherà solo con le richieste inviate alla cartella radice del server di origine selezionato (ad esempio, http://cdn.mydomain.com/). 
 Destination  |Definire l'URL relativo in cui verranno riscritte le richieste precedenti: <br/>    1. Selezionando un punto di accesso dei contenuti che identifichi un server di origine. <br/>    2. Definendo di un percorso tramite: <br/>        - Un modello di espressione regolare <br/>        - Variabili HTTP <br/> <br/> Sostituire nel modello di destinazione i valori acquisiti nel modello di origine usando $_n_ dove _n_ identifica un valore in base all'ordine in cui è stato acquisito. Ad esempio, $1 rappresenta il primo valore acquisito nel modello di origine e $2 rappresenta il secondo valore. 
 Questa funzionalità consente ai server periferici di riscrivere l'URL senza eseguire un reindirizzamento tradizionale. In questo modo, il richiedente riceverà lo stesso codice di risposta come se fosse stato richiesto l'URL riscritto.

**Scenario di esempio 1**

Questo esempio dimostra come reindirizzare un URL CNAME periferico che viene risolto in questo URL CDN di base: http://marketing.azureedge.net/brochures/

Le richieste idonee verranno reindirizzate a questo URL CNAME periferico di base: http://MyOrigin.azureedge.net/resources/

Questo reindirizzamento URL può essere ottenuto tramite la configurazione seguente: ![](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Scenario di esempio 2**

Questo esempio dimostra come reindirizzare un URL CNAME periferico da MAIUSCOLO a minuscolo mediante espressioni regolari.

Questo reindirizzamento URL può essere ottenuto tramite la configurazione seguente: ![](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)


**Punti principali:**

- La funzionalità di riscrittura URL definisce gli URL di richiesta che verranno riscritti. Non sono quindi necessarie condizioni di corrispondenza aggiuntive. Anche se la condizione di corrispondenza è stata definita come "Always" (Sempre), verranno riscritte solo le richieste che puntano alla cartella "brochures" nell'origine cliente "marketing".

- I segmenti di URL acquisiti dalla richiesta vengono aggiunti al nuovo URL tramite "$1."



###<a name="compatibility"></a>Compatibilità

Questa funzionalità include i criteri di corrispondenza che devono essere soddisfatti affinché possa essere applicata a una richiesta. Per evitare conflitti tra i criteri di corrispondenza impostati, questa funzionalità non è compatibile con le seguenti condizioni di corrispondenza:

- Numero AS
- Origine CDN
- Indirizzo IP client
- Origine cliente
- Schema richiesta
- Directory percorso URL
- Estensione percorso URL
- Nome file percorso URL
- Valore letterale percorso URL
- Espressione regolare percorso URL
- Carattere jolly percorso URL
- Valore letterale query URL
- Parametro query URL
- Espressione regolare query URL
- Carattere jolly query URL


## <a name="next-steps"></a>Passaggi successivi
* [Informazioni di riferimento sul motore regole](cdn-rules-engine-reference.md)
* [Espressioni condizionali del motore regole](cdn-rules-engine-reference-conditional-expressions.md)
* [Condizioni di corrispondenza del motore regole](cdn-rules-engine-reference-match-conditions.md)
* [Override del comportamento HTTP predefinito mediante il motore di regole](cdn-rules-engine.md)
* [Panoramica della rete CDN di Azure](cdn-overview.md)



<!--HONumber=Jan17_HO4-->


