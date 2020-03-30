---
title: Informazioni di riferimento per i codici di errore
titleSuffix: Azure AD B2C
description: Elenco dei codici di errore che possono essere restituiti dal servizio B2C di Azure Active Directory.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 01/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b09b3c19ab1c5b23e56e25afc1d9631cd1caa68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188665"
---
# <a name="error-codes-azure-active-directory-b2c"></a>Codici di errore: Azure Active Directory B2C

Gli errori seguenti possono essere restituiti dal servizio B2C di Azure Active Directory.The following errors can be returned by the Azure Active Directory B2C service.

| Codice di errore | Message |
| ---------- | ------- |
| `AADB2C90002` | La risorsa CORS '{0}ha restituito un 404 non trovato. |
| `AADB2C90006` | L'URI{0}di reindirizzamento ' ' fornito nella{1}richiesta non è registrato per l'ID client ' '. |
| `AADB2C90007` | L'applicazione associata{0}all'ID client ' ' non dispone di URI di reindirizzamento registrati. |
| `AADB2C90008` | La richiesta non contiene un parametro id client. |
| `AADB2C90010` | La richiesta non contiene un parametro di ambito. |
| `AADB2C90011` | L'ID{0}client ' ' fornito nella richiesta{1}non corrisponde all'ID client ' ' registrato nei criteri. |
| `AADB2C90012` | L'ambito{0}' ' fornito nella richiesta non è supportato. |
| `AADB2C90013` | Il tipo di{0}risposta richiesto ' ' fornito nella richiesta non è supportato. |
| `AADB2C90014` | La modalità{0}di risposta richiesta ' ' fornita nella richiesta non è supportata. |
| `AADB2C90016` | Il tipo di{0}asserzione client richiesta{1}' ' non corrisponde al tipo previsto ' '. |
| `AADB2C90017` | L'asserzione client fornita nella richiesta non è valida:{0} |
| `AADB2C90018` | L'ID{0}client ' ' specificato nella richiesta{1}non è registrato nel tenant ' '. |
| `AADB2C90019` | Il contenitore di{0}chiavi con{1}ID ' ' nel tenant ' ' non dispone di una chiave valida. Motivo: {2}. |
| `AADB2C90021` | Il profilo{0}tecnico ' ' non{1}esiste nella{2}polizza ' ' dell'inquilino ' . |
| `AADB2C90022` | Impossibile restituire i metadati per il criterio '{0}' nel tenant '{1}'. |
| `AADB2C90023` | Il{0}profilo ' ' non contiene{1}la chiave di metadati richiesta ' '. |
| `AADB2C90025` | Il{0}profilo '{1}' nel{2}criterio ' ' nel tenant{3}' non contiene la chiave crittografica richiesta ' '. |
| `AADB2C90027` | Le credenziali di{0}base specificate per ' ' non sono valide. Verificare che le credenziali siano corrette e che l'accesso sia stato concesso dalla risorsa. |
| `AADB2C90028` | Il certificato client{0}specificato per ' ' non è valido. Verificare che il certificato sia corretto, che contenga una chiave privata e che l'accesso sia stato concesso dalla risorsa. |
| `AADB2C90031` | Criterio{0}' non specifica un percorso utente predefinito. Assicurarsi che il criterio o i suoi genitori specifichino un percorso utente predefinito come parte di una sezione relying party. |
| `AADB2C90035` | Il servizio è temporaneamente non disponibile. Riprovare dopo alcuni minuti. |
| `AADB2C90036` | La richiesta non contiene un URI per reindirizzare l'utente alla disconnessione. Specificare un URI nel campo del parametro post_logout_redirect_uri. |
| `AADB2C90037` | Si è verificato un errore durante l'elaborazione della richiesta. Contattare l'amministratore del sito a cui si sta tentando di accedere. |
| `AADB2C90039` | La richiesta contiene un'asserzione{0}client, ma{1}nel criterio fornito ' ' nel tenant ' manca un client_secret in RelyingPartyPolicy. |
| `AADB2C90040` | Il percorso{0}utente ' non contiene un passaggio di invio delle attestazioni. |
| `AADB2C90043` | Il prompt incluso nella richiesta contiene valori non validi. Previsto 'none', 'login', 'consenso' o 'select_account'. |
| `AADB2C90044` | L'attestazione '{0}' non è{1}supportata dal resolver di attestazioni ' '. |
| `AADB2C90046` | Abbiamo problemi a caricare il tuo stato attuale. Potresti provare ad avviare la sessione dall'inizio. |
| `AADB2C90047` | La risorsa{0}' ' contiene errori di script che ne impediscono il caricamento. |
| `AADB2C90048` | Si è verificata un'eccezione non gestita nel server. |
| `AADB2C90051` | Non sono stati trovati fornitori di attestazioni idonei. |
| `AADB2C90052` | Nome utente o password non validi. |
| `AADB2C90053` | Impossibile trovare un utente con la credenziale specificata. |
| `AADB2C90054` | Nome utente o password non validi. |
| `AADB2C90055` | L'ambito{0}' fornito nella richiesta deve specificare una risorsa, ad esempio 'https://example.com/calendar.read'. |
| `AADB2C90057` | L'applicazione fornita non è configurata per consentire il flusso implicito OAuth. |
| `AADB2C90058` | L'applicazione fornita non è configurata per consentire i client pubblici. |
| `AADB2C90067` | Il formato non è{0}valido per l'URI di reindirizzamento postlogout ' ' . Specificare un URL basato su https, ad esempio 'https://example.com/return' o per i client nativi utilizzare l'URI del client nativo IETF 'urn:ietf:wg:oauth:2.0:oob'. |
| `AADB2C90068` | L'applicazione fornita{0}con ID ' non è valida per questo servizio. Utilizzare un'applicazione creata tramite il portale B2C e riprovare. |
| `AADB2C90075` | L'errore{0}di tipo di{1}attestazione ' ' specificato{2}nel passaggio ' ha restituito la risposta di errore HTTP con codice ' ' e Motivo '{3}'. |
| `AADB2C90077` | L'utente non dispone di un parametro di{0}richiesta di sessione e richiesta esistente ha un valore di ' '. |
| `AADB2C90079` | I clienti devono inviare un client_secret al momento del riscatto di una sovvenzione confidenziale. |
| `AADB2C90080` | La sovvenzione fornita è scaduta. Ripetere l'autenticazione e riprovare. Ora corrente: {0}, Concedere {1}l'ora emessa: , Concedere l'ora di scadenza dell'intervallo scorrevole: {2}. |
| `AADB2C90081` | Il valore di client_secret specificato non corrisponde al valore previsto per questo client. Correggere il client_secret e riprovare. |
| `AADB2C90083` | Nella richiesta manca il {0}parametro obbligatorio: . |
| `AADB2C90084` | I clienti pubblici non devono inviare un client_secret al momento del riscatto di una sovvenzione acquisita pubblicamente. |
| `AADB2C90085` | Il servizio ha rilevato un errore interno. Ripetere l'autenticazione e riprovare. |
| `AADB2C90086` | Il grant_type fornito{0}[ ] non è supportato. |
| `AADB2C90087` | La concessione fornita non è stata rilasciata per questa versione dell'endpoint del protocollo. |
| `AADB2C90088` | La sovvenzione fornita non è stata rilasciata per questo endpoint. Valore effettivo {0} : e Valore previsto :{1} |
| `AADB2C90092` | L'applicazione fornita{0}con ID ' '{1}è disabilitata per il tenant ' '. Abilitare l'applicazione e riprovare. |
| `AADB2C90107` | L'applicazione con{0}ID ' ' non può ottenere un token ID perché l'ambito openid non è stato fornito nella richiesta o se l'applicazione non è autorizzata per essa. |
| `AADB2C90108` | Il passaggio di{0}orchestrazione ' ' non specifica un CpimIssuerTechnicalProfileReferenceId quando ne era previsto uno. |
| `AADB2C90110` | Il parametro scope deve includere 'openid' quando si richiede un response_type che include 'id_token'. |
| `AADB2C90111` | L'account è stato bloccato. Contattare il supporto tecnico per sbloccarlo, quindi riprovare. |
| `AADB2C90114` | L'account è stato temporaneamente bloccato per impedirne l'uso non autorizzato. Riprovare più tardi. |
| `AADB2C90115` | Quando si richiede il response_type 'codice', il parametro di ambito deve includere una risorsa o un ID client per i token di accesso e 'openid' per i token ID. Includere inoltre 'offline_access' per i token di aggiornamento. |
| `AADB2C90117` | L'ambito{0}' ' fornito nella richiesta non è supportato. |
| `AADB2C90118` | L'utente ha dimenticato la password. |
| `AADB2C90120` | Il parametro{0}max age ' specificato nella richiesta non è valido. L'età massima deve{1}essere un{2}numero intero compreso tra ' ' e ' .' inclusivo. |
| `AADB2C90122` | L'input{0}per ' ricevuto nella richiesta non ha superato la convalida della richiesta HTTP. Assicurarsi che l'input non contenga caratteri come < o &. |
| `AADB2C90128` | L'account associato a questa sovvenzione non esiste più. Ripetere l'autenticazione e riprovare. |
| `AADB2C90129` | La sovvenzione fornita è stata revocata. Ripetere l'autenticazione e riprovare. |
| `AADB2C90145` | Non sono stati trovati numeri di telefono non verificati e i criteri non consentono a un utente di immettere un numero. |
| `AADB2C90146` | L'ambito{0}' fornito nella richiesta specifica più di una risorsa per un token di accesso, che non è supportato. |
| `AADB2C90149` | Impossibile{0}caricare lo script ' ' . |
| `AADB2C90151` | L'utente ha superato il numero massimo di tentativi per l'autenticazione a più fattori. |
| `AADB2C90152` | Una richiesta di polling a più fattori non è riuscita a ottenere una risposta dal servizio. |
| `AADB2C90154` | Una richiesta di verifica a più fattori non è riuscita a ottenere un ID di sessione dal servizio. |
| `AADB2C90155` | Una richiesta di verifica a più{0}fattori non è riuscita con motivo ' '. |
| `AADB2C90156` | Una richiesta di convalida a più{0}fattori non è riuscita con motivo ' '. |
| `AADB2C90157` | L'utente ha superato il numero massimo di tentativi per un passaggio auto-asserito. |
| `AADB2C90158` | Una richiesta di convalida auto-asserita non è riuscita con motivo '{0}'. |
| `AADB2C90159` | Una richiesta di verifica auto-asserita non è riuscita con motivo '{0}'. |
| `AADB2C90161` | Una risposta di invio auto-assestata{0}non è riuscita con la ragione ' '. |
| `AADB2C90165` | Impossibile trovare nello stato il messaggio{0}di id ' ' samL con id ' . |
| `AADB2C90168` | La richiesta http-Redirect non contiene{0}il parametro obbligatorio ' ' per una richiesta firmata. |
| `AADB2C90178` | Il certificato{0}di firma ' ' non dispone di alcuna chiave privata. |
| `AADB2C90182` | Il code_verifier fornito non corrisponde al code_challenge associato |
| `AADB2C90183` | Il code_verifier fornito non è valido |
| `AADB2C90184` | Il code_challenge_method fornito non è supportato. I valori supportati sono plain o S256Supported values are plain or S256 |
| `AADB2C90188` | Il profilo tecnico{0}SAML ' ' specifica{1}un URL PartnerEntity di '{2}', ma il recupero dei metadati ha esito negativo con motivo ' '. |
| `AADB2C90194` | L'attestazione '{0}specificata per il token di connessione non è presente nelle attestazioni disponibili. Rivendicazioni{1}disponibili ' '. |
| `AADB2C90205` | Questa applicazione non dispone di autorizzazioni sufficienti su questa risorsa Web per eseguire l'operazione. |
| `AADB2C90206` | Si è verificato un timeout durante l'inizializzazione del client. |
| `AADB2C90208` | Il parametro id_token_hint fornito è scaduto. Fornire un altro token e riprovare. |
| `AADB2C90209` | Il parametro id_token_hint fornito non contiene un gruppo di destinatari accettato. Valori di gruppo{0}di destinatari validi: ' '. Fornire un altro token e riprovare. |
| `AADB2C90210` | Impossibile convalidare il parametro id_token_hint fornito. Fornire un altro token e riprovare. |
| `AADB2C90211` | La richiesta conteneva un cookie di stato incompleto. |
| `AADB2C90212` | La richiesta conteneva un cookie di stato non valido. |
| `AADB2C90220` | Il contenitore di{0}chiavi nel{1}tenant ' ' con identificatore di archiviazione ' ' esiste ma non contiene un certificato valido. Il certificato potrebbe essere scaduto o il certificato potrebbe diventare attivo in futuro (nbf). |
| `AADB2C90223` | Si è verificato un errore durante la sanificazione della risorsa CORS. |
| `AADB2C90224` | Il flusso del proprietario della risorsa non è stato abilitato per l'applicazione. |
| `AADB2C90225` | Il nome utente o la password forniti nella richiesta non sono validi. |
| `AADB2C90226` | Lo scambio di token specificato è supportato solo su HTTP POST. |
| `AADB2C90232` | Il parametro id_token_hint fornito non contiene un'autorità emittente accettata. Emittenti validi: '{0}'. Fornire un altro token e riprovare. |
| `AADB2C90233` | Il parametro id_token_hint fornito non ha superato la convalida della firma. Fornire un altro token e riprovare. |
| `AADB2C90235` | Il id_token fornito è scaduto. Fornire un altro token e riprovare. |
| `AADB2C90237` | Il id_token fornito non contiene un gruppo di destinatari valido. Valori di gruppo{0}di destinatari validi: ' '. Fornire un altro token e riprovare. |
| `AADB2C90238` | Il id_token fornito non contiene un'autorità emittente valida. Valori validi dell'autorità emittente: '{0}'. Fornire un altro token e riprovare. |
| `AADB2C90239` | Il id_token fornito non ha superato la convalida della firma. Fornire un altro token e riprovare. |
| `AADB2C90240` | Il id_token fornito non è valido e non può essere analizzato. Fornire un altro token e riprovare. |
| `AADB2C90242` | Il profilo tecnico{0}SAML ' ' specifica PartnerEntity CDATA{1}che non può essere caricato per il motivo ' '. |
| `AADB2C90243` | La chiave/segreto client dell'IDP non è configurata correttamente. |
| `AADB2C90244` | In questo momento sono presenti troppe richieste. Attendere qualche minuto e riprovare. |
| `AADB2C90248` | Il flusso del proprietario della risorsa può essere utilizzato solo dalle applicazioni create tramite il portale di amministrazione B2C. |
| `AADB2C90250` | L'endpoint di accesso generico non è supportato. |
| `AADB2C90255` | Lo scambio di sinistri specificato nel profilo tecnico '{0}' non è stato completato come previsto. Potresti provare ad avviare la sessione dall'inizio. |
| `AADB2C90261` | Le attestazioni{0}' '{1}specificate nel passaggio ' hanno restituito una risposta di errore HTTP che non è stato possibile analizzare. |
| `AADB2C90272` | Il parametro id_token_hint non è stato specificato nella richiesta. Fornire il token e riprovare. |
| `AADB2C90273` | È stata ricevuta una{0}risposta non valida: ' ' |
| `AADB2C90274` | I metadati del provider non specificano un singolo servizio di disconnessione o l'associazione dell'endpoint non è uno dei 'urn:oasis:names:tc:2.0:bindings:HTTP-Redirect' o 'urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST'. |
| `AADB2C90276` | La richiesta non è coerente{0}con l'impostazione di{2}controllo '{3}':{4}'{1}' in technicalProfile ' ' per i criteri ' tenant ' '. |
| `AADB2C90277` | Il passaggio di{0}orchestrazione '{1}' del{2}percorso utente ' ' del criterio ' non contiene un riferimento alla definizione del contenuto. |
| `AADB2C90279` | L'ID client{0}fornito ' ' non corrisponde all'ID client che ha emesso la sovvenzione. |
| `AADB2C90284` | L'applicazione con{0}identificatore ' non ha ricevuto il consenso e non può essere utilizzata per gli account locali. |
| `AADB2C90285` | L'applicazione con{0}identificatore ' ' non è stata trovata. |
| `AADB2C90288` | UserJourney con{0}id ' ' a{1}cui viene fatto riferimento{2}in TechnicalProfile '{3}' per il riscatto del token di aggiornamento per il tenant ' ' non esiste nei criteri ' ' o in uno dei relativi criteri di base. |
| `AADB2C90289` | Si è verificato un errore durante la connessione al provider di identità. Riprovare più tardi. |
| `AADB2C90296` | L'applicazione non è stata configurata correttamente. Contattare l'amministratore del sito a cui si sta tentando di accedere. |
| `AADB2C99005` | La richiesta contiene un parametro di{0}ambito non valido che include un carattere non valido ' '. |
| `AADB2C99006` | Azure AD B2C non riesce a{0}trovare l'app delle estensioni con id app ' '. Si https://go.microsoft.com/fwlink/?linkid=851224 prega di visitare per ulteriori informazioni. |
| `AADB2C99011` | Il valore{0}dei metadati ' '{1}non è{2}stato specificato in TechnicalProfile ' ' nel criterio ' '. |
| `AADB2C99013` | Le grant_type di{0}combinazione di{1}proprietà [ ] e token_type di combinazione [ ] non sono supportate. |
| `AADB2C99015` | Nel{0}profilo '{1}' nel{2}criterio ' ' nel tenant ' mancano tutte le InputClaims necessarie per il flusso delle credenziali della password del proprietario della risorsa. |
