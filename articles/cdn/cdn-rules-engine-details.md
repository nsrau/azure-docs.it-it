<properties 
	pageTitle="Informazioni dettagliate sulle condizioni di corrispondenza e sulle funzionalità del motore regole della rete per la distribuzione di contenuti (CDN) di Azure" 
	description="In questo argomento sono riportate descrizioni dettagliate delle condizioni di corrispondenza e delle funzionalità disponibili per il motore regole della rete per la distribuzione di contenuti (CDN) di Azure." 
	services="cdn" 
	documentationCenter="" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2015" 
	ms.author="casoper"/>


# Informazioni dettagliate sulle condizioni di corrispondenza e sulle funzionalità del motore regole della rete CDN
	
In questo argomento sono riportate descrizioni dettagliate delle condizioni di corrispondenza e delle funzionalità disponibili per il [motore regole](cdn-rules-engine.md) della rete per la distribuzione di contenuti (CDN) di Azure.

> [AZURE.NOTE]Il motore regole richiede il livello Premium della rete CDN. Per altre informazioni sulle funzionalità dei livelli Standard e Premium della rete CDN, vedere [Panoramica della rete per la distribuzione di contenuti di Azure](cdn-overview.md).

## Condizioni di corrispondenza

Una condizione di corrispondenza identifica specifici tipi di richieste per cui verrà eseguito un set di funzionalità.

Ad esempio, può essere usata per filtrare le richieste di contenuto in una particolare posizione, le richieste generate da un paese o un indirizzo IP specifico oppure in base alle informazioni di intestazione.

### Sempre

La condizione di corrispondenza Sempre è progettata per applicare un set predefinito di funzionalità a tutte le richieste.

### Dispositivo

La condizione di corrispondenza Dispositivo identifica le richieste effettuate da un dispositivo mobile in base alle relative proprietà.

### Località

Queste condizioni di corrispondenza sono progettate per identificare le richieste in base alla posizione del richiedente.

Nome | Scopo
-----|--------
Numero AS | Identifica le richieste che hanno origine da una determinata rete.
Paese | Identifica le richieste che hanno origine dai paesi specificati.
 

### Origine

Queste condizioni di corrispondenza sono progettate per identificare le richieste che puntano all'archivio della rete CDN o a un server di origine del cliente.

Nome | Scopo
-----|--------
Origine CDN | Identifica le richieste di contenuto nell'archivio della rete CDN.
Origine cliente | Identifica le richieste di contenuto in uno specifico server di origine del cliente.


### Richiesta

Queste condizioni di corrispondenza sono progettate per identificare le richieste in base alle relative proprietà.

Nome | Scopo
-----|--------
Indirizzo IP client | Identifica le richieste che hanno origine da un determinato indirizzo IP.
Parametro cookie | Controlla il valore specificato nei cookie associati a ogni richiesta.
Espressione regolare parametro cookie | Controlla l'espressione regolare specificata nei cookie associati a ogni richiesta.
CNAME periferico | Identifica le richieste che fanno riferimento a un CNAME periferico specifico.
Dominio di riferimento | Identifica le richieste con riferimenti da uno o più nomi host specificati.
Valore letterale intestazione richiesta | Identifica le richieste che contengono l'intestazione specificata impostata su uno o più valori specificati.
Espressione regolare intestazione richiesta | Identifica le richieste che contengono l'intestazione specificata impostata su un valore che corrisponde all'espressione regolare specificata.
Carattere jolly intestazione richiesta | Identifica le richieste che contengono l'intestazione specificata impostata su un valore che corrisponde al modello specificato.
Metodo richiesta | Identifica le richieste in base al relativo metodo HTTP.
Schema richiesta | Identifica le richieste in base al relativo protocollo HTTP.

### URL

Queste condizioni di corrispondenza sono progettate per identificare le richieste in base ai relativi URL.

Nome | Scopo
-----|--------
Directory percorso URL | Identifica le richieste in base al percorso relativo.
Estensione percorso URL | Identifica le richieste in base all'estensione del nome file.
Nome file percorso URL | Identifica le richieste in base al nome del file.
Valore letterale percorso URL | Confronta il percorso relativo di una richiesta con il valore specificato.
Espressione regolare percorso URL | Confronta il percorso relativo di una richiesta con l'espressione regolare specificata.
Carattere jolly percorso URL | Confronta il percorso relativo di una richiesta con il modello specificato.
Valore letterale query URL | Confronta la stringa di query di una richiesta con il valore specificato.
Parametro query URL | Identifica le richieste che contengono il parametro della stringa di query specificato impostato su un valore che corrisponde a un modello specificato.
Espressione regolare query URL | Identifica le richieste che contengono il parametro della stringa di query specificato impostato su un valore che corrisponde a un'espressione regolare specificata.
Carattere jolly query URL | Confronta uno o più valori specificati con la stringa di query della richiesta.

## Funzionalità

Una funzionalità definisce il tipo di azione che verrà applicato al tipo di richiesta identificata da un set di condizioni di corrispondenza.

### Accesso

Queste funzionalità sono progettate per controllare l'accesso al contenuto.

Nome | Scopo
-----|--------
Nega l'accesso | Determina se tutte le richieste vengono rifiutate con una risposta 403 Accesso negato.
Autenticazione token | Determina se l'autenticazione basata su token verrà applicata a una richiesta. 
Codice rifiuto autenticazione token | Determina il tipo di risposta che verrà restituito a un utente quando una richiesta viene rifiutata a causa dell'autenticazione basata su token. 
Maiuscole/minuscole URL autenticazione token | Determina se verrà applicata la distinzione tra maiuscole e minuscole nei confronti di URL eseguiti dall'autenticazione basata su token. 
Parametro autenticazione token | Determina se il parametro della stringa di query dell'autenticazione basata su token deve essere rinominato.

### Memorizzazione nella cache

Queste funzionalità sono progettate per personalizzare come e quando il contenuto viene memorizzato nella cache.

Nome | Scopo
-----|--------
Parametri larghezza di banda | Determina se i parametri di limitazione della larghezza di banda (ad esempio, ec\_rate ed ec\_prebuf) saranno attivi.
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

### Headers

Queste funzionalità sono progettate per aggiungere, modificare o eliminare le intestazioni dalla richiesta o dalla risposta.

Nome | Scopo
-----|--------
Intestazione di risposta Age | Determina se un'intestazione di risposta Age verrà inclusa nella risposta inviata al richiedente. 
Debug intestazioni di risposta cache | Determina se una risposta può includere l'intestazione di risposta X-EC-Debug che fornisce informazioni sul criterio di cache per l'asset richiesto.
Modifica intestazione richiesta client | Sovrascrive, aggiunge o elimina un'intestazione da una richiesta.
Modificare intestazione risposta client | Sovrascrive, aggiunge o elimina un'intestazione da una risposta.
Imposta intestazione personalizzata IP client | Consente di aggiungere l'indirizzo IP del client richiedente alla richiesta come un'intestazione personalizzata. 

### Log

Queste funzionalità sono progettate per personalizzare i dati archiviati nei file di log non elaborati.

Nome | Scopo
-----|--------
Campo 1 log personalizzato | Determina il formato e il contenuto che verranno assegnati al campo di log personalizzato in un file di log non elaborato. 
Stringa di query log | Determina se una stringa di query verrà archiviata insieme all'URL nei log di accesso.

### Eseguire l'ottimizzazione

Queste funzionalità determinano se una richiesta sarà sottoposta alle ottimizzazioni fornite da Edge Optimizer.

Nome | Scopo
-----|--------
Edge Optimizer | Determina se Edge Optimizer può essere applicato a una richiesta. 
Edge Optimizer - Crea istanza configurazione | Crea un'istanza o attiva la configurazione di Edge Optimizer associata a un sito. 
 

### Origine

Queste funzionalità sono progettate per controllare in che modo la rete CDN comunica con un server di origine.

Nome | Scopo
-----|--------
Numero massimo di richieste Keep-Alive | Definisce il numero massimo di richieste per una connessione Keep-Alive prima della chiusura. 
Intestazioni speciali proxy | Definisce il set di intestazioni di richiesta specifiche della rete CDN che verranno inoltrate da un server perimetrale a un server di origine. 

### Funzionalità specializzate

Queste funzionalità offrono caratteristiche avanzate che devono essere usate solo dagli utenti esperti.

Nome | Scopo
-----|--------
Metodi HTTP inseribile nella cache | Determina il set di metodi HTTP aggiuntivi che possono essere memorizzati nella cache nella rete.
Dimensioni corpo richiesta inseribile nella cache | Definisce la soglia per determinare se una risposta POST può essere memorizzata nella cache. 
 

### URL

Queste funzionalità consentono il reindirizzamento o la riscrittura di una richiesta in un URL diverso.

Nome | Scopo
-----|--------
Segui reindirizzamenti | Determina se le richieste possono essere reindirizzate al nome host definito nell'intestazione Location restituita da un server di origine del cliente. 
Reindirizzamento URL | Reindirizza le richieste tramite l'intestazione Location. 
Riscrittura URL | Riscrive l'URL della richiesta. 

### Web application firewall

La funzionalità Web application firewall determina se una richiesta sarà verificata da Web application firewall.

## Vedere anche
* [Panoramica della rete CDN di Azure](cdn-overview.md)
* [Override del comportamento HTTP predefinito mediante il motore di regole](cdn-rules-engine.md)

<!---HONumber=AcomDC_1203_2015-->