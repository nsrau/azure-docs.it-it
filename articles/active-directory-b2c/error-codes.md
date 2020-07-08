---
title: Informazioni di riferimento per i codici di errore
titleSuffix: Azure AD B2C
description: Elenco dei codici di errore che possono essere restituiti dal servizio Azure Active Directory B2C.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9d205998ad5710ecad346db4d7be18a68747c087
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85388528"
---
# <a name="error-codes-azure-active-directory-b2c"></a>Codici di errore: Azure Active Directory B2C

Il servizio Azure Active Directory B2C può restituire gli errori seguenti.

| Codice di errore | Message |
| ---------- | ------- |
| `AADB2C90002` | La risorsa CORS ' {0} ' ha restituito un 404 non trovato. |
| `AADB2C90006` | L'URI di reindirizzamento ' {0} ' specificato nella richiesta non è registrato per l'ID client ' {1} '. |
| `AADB2C90007` | L'applicazione associata all'ID client ' {0} ' non contiene URI di reindirizzamento registrati. |
| `AADB2C90008` | La richiesta non contiene un parametro ID client. |
| `AADB2C90010` | La richiesta non contiene un parametro di ambito. |
| `AADB2C90011` | L'ID client ' {0} ' specificato nella richiesta non corrisponde all'ID client ' {1} ' registrato nei criteri. |
| `AADB2C90012` | L'ambito ' {0} ' specificato nella richiesta non è supportato. |
| `AADB2C90013` | Il tipo di risposta richiesto ' {0} ' specificato nella richiesta non è supportato. |
| `AADB2C90014` | La modalità di risposta richiesta ' {0} ' specificata nella richiesta non è supportata. |
| `AADB2C90016` | Il tipo di asserzione client richiesto ' {0} ' non corrisponde al tipo previsto ' {1} '. |
| `AADB2C90017` | L'asserzione client specificata nella richiesta non è valida:{0} |
| `AADB2C90018` | L'ID client ' {0} ' specificato nella richiesta non è registrato nel tenant ' {1} '. |
| `AADB2C90019` | La chiave del contenitore di chiavi con ID ' {0} ' nel tenant ' {1} ' non è valida. Motivo: {2} . |
| `AADB2C90021` | Il profilo tecnico ' {0} ' non esiste nel criterio ' {1} ' del tenant ' {2} '. |
| `AADB2C90022` | Non è possibile restituire i metadati per il criterio ' {0} ' nel tenant ' {1} '. |
| `AADB2C90023` | Il profilo ' {0} ' non contiene la chiave dei metadati richiesta ' {1} '. |
| `AADB2C90025` | Il profilo ' {0} ' nel criterio ' {1} ' nel tenant ' {2} ' non contiene la chiave crittografica richiesta ' {3} '. |
| `AADB2C90027` | Le credenziali di base specificate per ' {0} ' non sono valide. Verificare che le credenziali siano corrette e che l'accesso sia stato concesso dalla risorsa. |
| `AADB2C90028` | Il certificato client specificato per ' {0} ' non è valido. Verificare che il certificato sia corretto, che contenga una chiave privata e che l'accesso sia stato concesso dalla risorsa. |
| `AADB2C90031` | Il criterio ' {0} ' non specifica un percorso utente predefinito. Verificare che il criterio o gli elementi padre specifichino un percorso utente predefinito come parte di una sezione relying party. |
| `AADB2C90035` | Il servizio è temporaneamente non disponibile. Riprovare tra qualche minuto. |
| `AADB2C90036` | La richiesta non contiene un URI per reindirizzare l'utente a post-disconnessione. Specificare un URI nel campo del parametro post_logout_redirect_uri. |
| `AADB2C90037` | Si è verificato un errore durante l'elaborazione della richiesta. Contattare l'amministratore del sito a cui si sta tentando di accedere. |
| `AADB2C90039` | La richiesta contiene un'asserzione client, ma il criterio ' {0} ' specificato nel tenant ' {1} ' manca un Client_secret in RelyingPartyPolicy. |
| `AADB2C90040` | Il percorso utente ' {0} ' non contiene un passaggio Invia attestazioni. |
| `AADB2C90043` | Il prompt incluso nella richiesta contiene valori non validi. Previsto ' none ',' login ',' consenso ' o ' select_account '. |
| `AADB2C90044` | L'attestazione ' {0} ' non è supportata dal resolver di attestazioni ' {1} '. |
| `AADB2C90046` | Si sono verificati problemi durante il caricamento dello stato corrente. Potrebbe essere necessario provare a avviare la sessione dall'inizio. |
| `AADB2C90047` | La risorsa ' {0} ' contiene errori di script che ne impediscono il caricamento. |
| `AADB2C90048` | Si è verificata un'eccezione non gestita nel server. |
| `AADB2C90051` | Non sono stati trovati provider di attestazioni appropriati. |
| `AADB2C90052` | Nome utente o password non validi. |
| `AADB2C90053` | Impossibile trovare un utente con le credenziali specificate. |
| `AADB2C90054` | Nome utente o password non validi. |
| `AADB2C90055` | L'ambito ' {0} ' specificato nella richiesta deve specificare una risorsa, ad esempio ' https://example.com/calendar.read '. |
| `AADB2C90057` | L'applicazione specificata non è configurata per consentire il flusso implicito OAuth. |
| `AADB2C90058` | L'applicazione specificata non è configurata per consentire i client pubblici. |
| `AADB2C90067` | Il formato dell'URI di reindirizzamento dopo la disconnessione ' {0} ' non è valido. Specificare un URL basato su HTTPS, ad esempio ' https://example.com/return ', oppure per i client nativi usare l'URI di IETF Native Client ' urn: IETF: WG: OAuth: 2.0: OOB '. |
| `AADB2C90068` | L'applicazione specificata con ID ' {0} ' non è valida per questo servizio. Usare un'applicazione creata tramite il portale B2C e riprovare. |
| `AADB2C90075` | Lo scambio di attestazioni ' {0} ' specificato nel passaggio ' {1} ' ha restituito la risposta di errore HTTP con codice ' {2} ' e motivo ' {3} '. |
| `AADB2C90077` | L'utente non dispone di una sessione esistente e il parametro del prompt della richiesta ha un valore ' {0} '. |
| `AADB2C90079` | Quando si riscatta una concessione riservata, i client devono inviare un client_secret. |
| `AADB2C90080` | La concessione specificata è scaduta. Ripetere l'autenticazione e riprovare. Ora corrente: {0} , concedi ora rilasciata: {1} , concedi la scadenza della finestra temporale scorrevole: {2} . |
| `AADB2C90081` | Il valore di client_secret specificato non corrisponde al valore previsto per questo client. Correggere il client_secret e riprovare. |
| `AADB2C90083` | Nella richiesta manca il parametro obbligatorio: {0} . |
| `AADB2C90084` | I client pubblici non devono inviare un client_secret quando si riscatta una concessione acquisita pubblicamente. |
| `AADB2C90085` | Si è verificato un errore interno del servizio. Ripetere l'autenticazione e riprovare. |
| `AADB2C90086` | Il grant_type specificato [ {0} ] non è supportato. |
| `AADB2C90087` | La concessione specificata non è stata rilasciata per questa versione dell'endpoint del protocollo. |
| `AADB2C90088` | La concessione specificata non è stata rilasciata per questo endpoint. Valore effettivo: {0} e valore previsto:{1} |
| `AADB2C90092` | L'applicazione specificata con ID ' {0} ' è disabilitata per il tenant ' {1} '. Abilitare l'applicazione e riprovare. |
| `AADB2C90107` | L'applicazione con ID ' {0} ' non può ottenere un token ID perché l'ambito OpenID non è stato specificato nella richiesta o l'applicazione non è autorizzata. |
| `AADB2C90108` | Il passaggio di orchestrazione ' {0} ' non specifica un CpimIssuerTechnicalProfileReferenceId se ne era previsto uno. |
| `AADB2C90110` | Quando si richiede un response_type che include ' id_token ', il parametro di ambito deve includere ' OpenID '. |
| `AADB2C90111` | L'account è stato bloccato. Contattare il supporto tecnico per sbloccarlo, quindi riprovare. |
| `AADB2C90114` | L'account è stato temporaneamente bloccato per impedirne l'uso non autorizzato. Riprovare più tardi. |
| `AADB2C90115` | Quando si richiede il response_type ' code ', il parametro scope deve includere una risorsa o un ID client per i token di accesso è OpenID ' per i token ID. Includere anche ' offline_access ' per i token di aggiornamento. |
| `AADB2C90117` | L'ambito ' {0} ' specificato nella richiesta non è supportato. |
| `AADB2C90118` | L'utente ha dimenticato la password. |
| `AADB2C90120` | Il parametro di validità massima ' {0} ' specificato nella richiesta non è valido. La validità massima deve essere un numero intero compreso tra' {1} ' è {2} '. |
| `AADB2C90122` | L'input per ' {0} ' ricevuto nella richiesta non ha superato la convalida delle richieste HTTP. Verificare che l'input non contenga caratteri quali < o &. |
| `AADB2C90128` | L'account associato a questa concessione non esiste più. Ripetere l'autenticazione e riprovare. |
| `AADB2C90129` | La concessione fornita è stata revocata. Ripetere l'autenticazione e riprovare. |
| `AADB2C90145` | Non sono stati trovati numeri di telefono non verificati e i criteri non consentono un numero immesso dall'utente. |
| `AADB2C90146` | L'ambito ' {0} ' specificato nella richiesta specifica più di una risorsa per un token di accesso, che non è supportato. |
| `AADB2C90149` | {0}Non è stato possibile caricare lo script ''. |
| `AADB2C90151` | L'utente ha superato il numero massimo di tentativi per l'autenticazione a più fattori. |
| `AADB2C90152` | Una richiesta di poll a più fattori non è riuscita a ottenere una risposta dal servizio. |
| `AADB2C90154` | Una richiesta di verifica a più fattori non è riuscita a ottenere un ID di sessione dal servizio. |
| `AADB2C90155` | Una richiesta di verifica a più fattori non è riuscita con motivo ' {0} '. |
| `AADB2C90156` | Una richiesta di convalida a più fattori non è riuscita con motivo ' {0} '. |
| `AADB2C90157` | L'utente ha superato il numero massimo di tentativi per un passaggio autocertificato. |
| `AADB2C90158` | Una richiesta di convalida autocertificata non è riuscita con motivo ' {0} '. |
| `AADB2C90159` | Una richiesta di verifica autocertificata non è riuscita con motivo ' {0} '. |
| `AADB2C90161` | Una risposta di invio autocertificata non è riuscita. motivo {0} :''. |
| `AADB2C90165` | Impossibile trovare il messaggio di inizializzazione SAML con ID ' {0} ' nello stato. |
| `AADB2C90168` | La richiesta di reindirizzamento HTTP non contiene il parametro obbligatorio ' {0} ' per una richiesta firmata. |
| `AADB2C90178` | Il certificato di firma ' {0} ' non ha una chiave privata. |
| `AADB2C90182` | Il code_verifier fornito non corrisponde code_challenge associato |
| `AADB2C90183` | Il code_verifier fornito non è valido |
| `AADB2C90184` | Il code_challenge_method fornito non è supportato. I valori supportati sono Plain o S256 |
| `AADB2C90188` | Il profilo tecnico SAML ' {0} ' specifica un URL PartnerEntity di ' {1} ', ma il recupero dei metadati ha esito negativo con motivo ' {2} '. |
| `AADB2C90194` | L'attestazione ' {0} ' specificata per il Bearer token non è presente nelle attestazioni disponibili. Attestazioni disponibili ' {1} '. |
| `AADB2C90205` | Questa applicazione non dispone di autorizzazioni sufficienti per questa risorsa Web per eseguire l'operazione. |
| `AADB2C90206` | Si è verificato un timeout durante l'inizializzazione del client. |
| `AADB2C90208` | Il parametro id_token_hint specificato è scaduto. Fornire un altro token e riprovare. |
| `AADB2C90209` | Il parametro id_token_hint specificato non contiene un gruppo di destinatari accettato. Valori validi per i destinatari:' {0} '. Fornire un altro token e riprovare. |
| `AADB2C90210` | Non è stato possibile convalidare il parametro id_token_hint fornito. Fornire un altro token e riprovare. |
| `AADB2C90211` | La richiesta contiene un cookie di stato incompleto. |
| `AADB2C90212` | La richiesta contiene un cookie di stato non valido. |
| `AADB2C90220` | Il contenitore di chiavi nel tenant ' {0} ' con identificatore di archiviazione ' {1} ' esiste ma non contiene un certificato valido. Il certificato potrebbe essere scaduto o il certificato potrebbe diventare attivo in futuro (NBF). |
| `AADB2C90223` | Si è verificato un errore durante la purificazione della risorsa CORS. |
| `AADB2C90224` | Il flusso del proprietario della risorsa non è stato abilitato per l'applicazione. |
| `AADB2C90225` | Il nome utente o la password specificati nella richiesta non sono validi. |
| `AADB2C90226` | Lo scambio di token specificato è supportato solo tramite HTTP POST. |
| `AADB2C90232` | Il parametro id_token_hint specificato non contiene un'autorità emittente accettata. Autorità emittenti valide:' {0} '. Fornire un altro token e riprovare. |
| `AADB2C90233` | La convalida della firma del parametro id_token_hint specificato non è riuscita. Fornire un altro token e riprovare. |
| `AADB2C90235` | Il id_token specificato è scaduto. Fornire un altro token e riprovare. |
| `AADB2C90237` | Il id_token fornito non contiene un gruppo di destinatari valido. Valori validi per i destinatari:' {0} '. Fornire un altro token e riprovare. |
| `AADB2C90238` | Il id_token fornito non contiene un'autorità emittente valida. Valori di autorità emittente validi:' {0} '. Fornire un altro token e riprovare. |
| `AADB2C90239` | Il id_token fornito non ha superato la convalida della firma. Fornire un altro token e riprovare. |
| `AADB2C90240` | Il id_token specificato non è valido e non può essere analizzato. Fornire un altro token e riprovare. |
| `AADB2C90242` | Il profilo tecnico SAML ' {0} ' specifica il CDATA PartnerEntity che non può essere caricato per il motivo ' {1} '. |
| `AADB2C90243` | La chiave o il segreto client dell'IDP non è configurato correttamente. |
| `AADB2C90244` | In questo momento sono presenti troppe richieste. Attendere qualche minuto e riprovare. |
| `AADB2C90248` | Il flusso del proprietario della risorsa può essere usato solo da applicazioni create tramite il portale di amministrazione B2C. |
| `AADB2C90250` | L'endpoint di accesso generico non è supportato. |
| `AADB2C90255` | Lo scambio di attestazioni specificato nel profilo tecnico ' {0} ' non è stato completato come previsto. Potrebbe essere necessario provare a avviare la sessione dall'inizio. |
| `AADB2C90261` | Lo scambio di attestazioni ' {0} ' specificato nel passaggio ' {1} ' ha restituito una risposta di errore HTTP che non è stato possibile analizzare. |
| `AADB2C90272` | Il parametro id_token_hint non è stato specificato nella richiesta. Fornire il token e riprovare. |
| `AADB2C90273` | È stata ricevuta una risposta non valida:' {0} ' |
| `AADB2C90274` | I metadati del provider non specificano un singolo servizio di disconnessione o l'associazione dell'endpoint non è uno dei seguenti:' urn: Oasis: Names: TC: SAML: 2.0: bindings: HTTP-redirect ' o ' urn: Oasis: Names: TC: SAML: 2.0: bindings: HTTP-POST '. |
| `AADB2C90276` | La richiesta non è coerente con l'impostazione di controllo ' {0} ':' {1} ' in technicalProfile ' {2} ' per il {3} tenant '' del criterio '' {4} . |
| `AADB2C90277` | Il passaggio di orchestrazione ' {0} ' del percorso utente ' {1} ' del criterio ' {2} ' non contiene un riferimento alla definizione del contenuto. |
| `AADB2C90279` | L'ID client ' {0} ' specificato non corrisponde all'ID client che ha emesso la concessione. |
| `AADB2C90284` | All'applicazione con identificatore ' {0} ' non è stato concesso il consenso e non è possibile utilizzarla per gli account locali. |
| `AADB2C90285` | L'applicazione con identificatore ' {0} ' non è stata trovata. |
| `AADB2C90288` | UserJourney con ID ' {0} ' a cui si fa riferimento nel TechnicalProfile ' {1} ' per il riscatto del token di aggiornamento per il tenant ' {2} ' non esiste nel criterio ' {3} ' o in uno dei criteri di base. |
| `AADB2C90289` | Si è verificato un errore durante la connessione al provider di identità. Riprova più tardi. |
| `AADB2C90296` | L'applicazione non è stata configurata correttamente. Contattare l'amministratore del sito a cui si sta tentando di accedere. |
| `AADB2C99005` | La richiesta contiene un parametro di ambito non valido che include un carattere non valido ' {0} '. |
| `AADB2C99006` | Azure AD B2C non è possibile trovare l'app estensioni con ID app ' {0} '. https://go.microsoft.com/fwlink/?linkid=851224Per ulteriori informazioni, visitare. |
| `AADB2C99011` | Il valore dei metadati ' {0} ' non è stato specificato in TechnicalProfile ' {1} ' nei criteri ' {2} '. |
| `AADB2C99013` | La combinazione di grant_type [ {0} ] e TOKEN_TYPE [ {1} ] fornita non è supportata. |
| `AADB2C99015` | Nel tenant '' {0} {1} {2} mancano tutti i InputClaims necessari per il flusso di credenziali della password del proprietario della risorsa nel criterio '' nel tenant ''. |
