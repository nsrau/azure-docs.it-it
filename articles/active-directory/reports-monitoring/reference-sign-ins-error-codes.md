---
title: Codici di errore del report delle attività di accesso nel portale di Azure Active Directory | Microsoft Docs
description: Informazioni di riferimento sui codici di errore del report delle attività di accesso.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/08/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6972c91a18f9deb240871d8c64a14647474d9d78
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377266"
---
# <a name="sign-in-activity-report-error-codes"></a>Codici di errore del report delle attività di accesso 

Le informazioni contenute nel [report relativo agli accessi utente](concept-sign-ins.md) consentono di rispondere a domande come le seguenti:

- Chi ha eseguito l'accesso all'applicazione?
- A quali applicazioni è stato eseguito l'accesso?
- Quali accessi hanno avuto esito negativo e perché?

Quando un accesso non riesce, verrà visualizzato un codice errore corrispondente all'operazione non riuscita. Questo articolo elenca i codici di errore con le relative descrizioni, oltre a una linea di condotta suggerita, ove applicabile. 

## <a name="how-can-i-display-failed-sign-ins"></a>Come è possibile visualizzare gli accessi non riusciti? 

Andare al [report degli accessi](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/SignIns) nel [portale di Azure](https://portal.azure.com).

![Attività di accesso](./media/reference-sign-ins-error-codes/61.png "Attività di accesso")

Filtrare il report per visualizzare tutti gli accessi non riusciti selezionando **Errore** dalla casella a discesa **Stato accesso**.

![Attività di accesso](./media/reference-sign-ins-error-codes/06.png "Attività di accesso")

Selezionando un elemento dall'elenco filtrato, verrà aperto il pannello **Dettagli attività: accessi**. Questa visualizzazione offre informazioni aggiuntive sull'evento di accesso non riuscito, inclusi il **codice errore di accesso** e il **motivo dell'errore**.

![Attività di accesso](./media/reference-sign-ins-error-codes/05.png "Attività di accesso")

È anche possibile accedere a livello di codice ai dati di accesso usando l'[API di creazione report](concept-reporting-api.md).

## <a name="error-codes"></a>Codici di errore


|Errore|Descrizione|
|---|---|
|16000|Questo è un dettaglio di implementazione interno e non una condizione di errore. È possibile ignorare questo messaggio di errore.|
|20001|Si è verificato un problema con il provider di identità federato. Contattare l'IDP per risolvere il problema.|
|20012|Si è verificato un problema con il provider di identità federato. Contattare l'IDP per risolvere il problema.|
|20033|Si è verificato un problema con il provider di identità federato. Contattare l'IDP per risolvere il problema.|
|40008|Si è verificato un problema con il provider di identità federato. Contattare l'IDP per risolvere il problema.|
|40009|Si è verificato un problema con il provider di identità federato. Contattare l'IDP per risolvere il problema.|
|40014|Si è verificato un problema con il provider di identità federato. Contattare l'IDP per risolvere il problema.|
|50000|Si è verificato un problema con il servizio di accesso. [Aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) per risolvere il problema.|
|50001|Il nome dell'entità servizio non è stato trovato in questo tenant. Questa situazione può verificarsi se l'applicazione non è stata installata dall'amministratore del tenant o se l'entità risorsa non è stata trovata nella directory o non è valida.|
|50002|L'accesso non è riuscito a causa delle limitazioni dell'accesso proxy nel tenant. Se questo errore è dovuto ai propri criteri per il tenant, è possibile modificare le impostazioni di limitazione per il tenant per correggerlo.|
|50003|L'accesso non è riuscito a causa di una chiave di firma o un certificato mancante. Ciò può essere dovuto all'assenza di una chiave di firma configurata nell'applicazione. Vedere le soluzioni illustrate in [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Se il problema persiste, contattare il proprietario dell'applicazione o l'amministratore dell'applicazione.|
|50005|L'utente ha tentato di eseguire l'accesso a un dispositivo da una piattaforma che attualmente non è supportata tramite i criteri di accesso condizionale.|
|50006| La verifica della firma non è riuscita perché la firma non è valida. Vedere la soluzione illustrata in [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery). Se il problema persiste, contattare il proprietario dell'applicazione o l'amministratore dell'applicazione.|
|50007|Non è stato trovato il certificato di crittografia del partner per l'applicazione. [Aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) presso Microsoft per richiedere la risoluzione del problema.|
|50008|L'asserzione SAML non è presente o è configurata in modo errato nel token. Contattare il provider di federazione.|
|50010|La convalida dell'URI destinatario per l'applicazione non è riuscita perché non sono stati configurati destinatari token. Contattare il proprietario dell'applicazione per la risoluzione.|
|50011|L'indirizzo di risposta manca, non è configurato correttamente o non corrisponde agli indirizzi di risposta configurati per l'applicazione. Provare la risoluzione illustrata in [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Se il problema persiste, contattare il proprietario dell'applicazione o l'amministratore dell'applicazione.|
|50012| Si tratta di un messaggio di errore generico che indica che l'autenticazione non è riuscita. Questa situazione può verificarsi per diversi motivi, ad esempio credenziali o attestazioni mancanti o non valide nella richiesta. Verificare che la richiesta venga inviata con le credenziali e le attestazioni corrette. |
|50013|L'asserzione non è valida per più motivi. Ad esempio, l'autorità emittente del token non corrisponde alla versione API nell'intervallo di tempo valido, il token è scaduto o ha un formato non valido oppure il token di aggiornamento nell'asserzione non è un token di aggiornamento primario.|
|50017|La convalida della certificazione non è riuscita, per i motivi seguenti:<ul><li>Non è possibile trovare la certificazione nell'elenco di certificati attendibili</li><li>Non è possibile trovare l'elemento CrlSegment previsto</li><li>Non è possibile trovare la certificazione nell'elenco di certificati attendibili</li><li>È configurato un punto di distribuzione CRL differenziale senza un punto di distribuzione CRL corrispondente</li><li>Non è possibile recuperare segmenti CRL validi a causa di un problema di timeout</li><li>Non è possibile scaricare il documento CRL</li></ul>Contattare l'amministratore del tenant.|
|50020|L'utente non è autorizzato per uno dei motivi seguenti.<ul><li>L'utente sta tentando di eseguire l'accesso con un account MSA con l'endpoint v1.</li><li>L'utente non esiste nel tenant.</li></ul> Contattare il proprietario dell'applicazione.|
|50027|Token JWT non valido a causa dei motivi seguenti:<ul><li>Non contiene un'attestazione nonce o un'attestazione secondaria</li><li>L'identificatore soggetto non corrisponde</li><li>Attestazione duplicata nelle attestazioni idToken</li><li>Autorità di certificazione imprevista</li><li>Destinatari imprevisti</li><li>Al di fuori dell'intervallo di tempo valido </li><li>Formato del token non corretto</li><li>Il token ID esterno dell'autorità di certificazione non ha superato la verifica della firma</li></ul>Contattare il proprietario dell'applicazione.|
|50029|URI non valido. Il nome di dominio contiene caratteri non validi. Contattare l'amministratore del tenant.|
|50034|L'utente non è presente nella directory. Contattare l'amministratore del tenant.|
|50042|Il Salt necessario per generare un identificatore pairwise è mancante in principio. Contattare l'amministratore del tenant.|
|50048|Mancata corrispondenza tra soggetto e attestazione dell'autorità di certificazione nell'asserzione del client. Contattare l'amministratore del tenant.|
|50050|Il formato della richiesta non è valido. Contattare il proprietario dell'applicazione.|
|50053|L'account è bloccato perché l'utente ha cercato di accedere troppe volte con una combinazione non corretta di ID utente e password.|
|50055|È stata immessa una password non valida o scaduta.|
|50056|Password non valida o null: la password non è presente nell'archivio per questo utente.|
|50057|L'account utente è disabilitato. L'account è stato disabilitato da un amministratore.|
|50058|L'applicazione ha provato a eseguire un accesso automatico, ma non è stato possibile completare l'accesso automatico per l'utente. L'applicazione deve avviare un flusso interattivo offrendo agli utenti un'opzione per eseguire l'accesso. Contattare il proprietario dell'applicazione.|
|50059|L'utente non è presente nella directory. Contattare l'amministratore del tenant.|
|50061|La richiesta di disconnessione non è valida. Contattare il proprietario dell'applicazione.|
|50072|L'utente deve registrarsi per l'autenticazione a due fattori (interattiva).|
|50074|L'utente non ha superato la richiesta di verifica MFA.|
|50076|L'utente non ha superato la richiesta di verifica MFA (non interattiva).|
|50079|L'utente deve registrarsi per l'autenticazione a due fattori (accessi non interattivi).|
|50085|Il token di aggiornamento richiede un accesso IDP social. Chiedere all'utente di provare a eseguire di nuovo l'accesso con il nome utente e la password.|
|50089|Il token di flusso è scaduto. L'autenticazione non è riuscita. Chiedere all'utente di provare a eseguire di nuovo l'accesso con il nome utente e la password|
|50097|Autenticazione dispositivo obbligatoria. Le attestazioni DeviceId o DeviceAltSecId potrebbero essere **null** oppure non esistono dispositivi corrispondenti all'identificatore del dispositivo.|
|50099|La firma del token JWT non è valida. Contattare il proprietario dell'applicazione.|
|50105|All'utente che ha eseguito l'accesso non è assegnato un ruolo per l'applicazione in uso. Assegnare l'utente all'applicazione. Per altre informazioni: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role)|
|50107|L'oggetto dell'area di autenticazione della federazione richiesto non esiste. Contattare l'amministratore del tenant.|
|50120|Problema relativo all'intestazione del token JWT. Contattare l'amministratore del tenant.|
|50124|La trasformazione delle attestazioni contiene un parametro di input non valido. Contattare l'amministratore del tenant per aggiornare i criteri.|
|50125|L'accesso è stato interrotto a causa della reimpostazione della password o di una voce di registrazione password.|
|50126|È stato usato un nome utente o una password non valida oppure un nome utente o una password locale non valida.|
|50127|È necessario installare un'applicazione broker per ottenere l'accesso al contenuto.|
|50128|Nome di dominio non valido. Non sono state trovate informazioni di identificazione del tenant nella richiesta o incluse in modo implicito nelle credenziali fornite.|
|50129|Il dispositivo non è aggiunto all'area di lavoro: per registrare il dispositivo è necessaria l'**aggiunta alla rete aziendale**.|
|50130|Il valore attestazione non può essere interpretato come metodo di autenticazione noto.|
|50131|Utilizzato in diversi errori di accesso condizionale. ad esempio Stato del dispositivo Windows non valido, richiesta bloccata a causa di un'attività sospetta oppure a causa di decisioni relative a criteri di accesso e criteri di sicurezza.|
|50132|Le credenziali sono state revocate per i motivi seguenti:<ul><li>L'elemento SSO non è valido o è scaduto</li><li>La sessione non è sufficientemente aggiornata per l'applicazione</li><li>È stata inviata una richiesta di accesso automatico, ma la sessione dell'utente con Azure AD non è valida o è scaduta.</li></ul>|
|50133|La sessione non è valida perché è scaduta o la password è stata modificata di recente.|
|50135|È richiesta la modifica della password a causa di rischi per l'account.|
|50136|Reindirizzare la sessione dell'account del servizio gestito all'applicazione. È stata rilevata una singola sessione dell'account del servizio gestito. |
|50140|Questo errore si è verificato a causa di un interrupt del comando "Mantieni l'accesso" durante l'accesso dell'utente. [Aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) con ID di correlazione, ID richiesta e codice di errore per ottenere altri dettagli. |
|50143|Mancata corrispondenza della sessione: la sessione non è valida perché il tenant utente non corrisponde al suggerimento di dominio a causa di una risorsa diversa.  [Aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) con ID di correlazione, ID richiesta e codice di errore per ottenere altri dettagli.|
|50144|La password di Active Directory dell'utente è scaduta. Generare una nuova password per l'utente o chiedere all'utente finale di usare lo strumento di reimpostazione self-service.|
|50146|Questa applicazione deve essere configurata con una chiave di firma specifica dell'applicazione. O non è configurata con tale chiave oppure la chiave è scaduta o non è ancora valida. Contattare il proprietario dell'applicazione.|
|50148|Il valore specificato per code_verifier non corrisponde al valore di code_challenge fornito nella richiesta di autorizzazione per PKCE. Contattare lo sviluppatore dell'applicazione. |
|50155|L'autenticazione del dispositivo non è riuscita per l'utente.|
|50158|La richiesta di sicurezza esterna non è stata soddisfatta.|
|50161|Le attestazioni inviate dal provider esterno non sono sufficienti oppure manca un'attestazione richiesta al provider esterno.|
|50166|Impossibile inviare la richiesta al provider di attestazioni.|
|50169|L'area di autenticazione non è un'area di autenticazione configurata dello spazio dei nomi del servizio corrente.|
|50172|Il provider di attestazioni esterno non è approvato. Contattare l'amministratore del tenant|
|50173|È necessario un token di autenticazione aggiornato. Chiedere all'utente di eseguire di nuovo l'accesso usando credenziali aggiornate.|
|50177|La richiesta di verifica esterna non è supportata per gli utenti pass-through.|
|50178|Il controllo della sessione non è supportato per gli utenti pass-through.|
|50180|È necessaria l'autenticazione integrata di Windows. Abilitare il tenant per Seamless SSO.|
|51001|Non è presente il suggerimento di dominio con l'ID di sicurezza locale o l'UPN locale.|
|51004|L'account utente non è presente nella directory.|
|51006|È necessaria l'autenticazione integrata di Windows. Utente che ha eseguito l'accesso con il token di sessione mancante tramite attestazione. Chiedere all'utente di eseguire di nuovo l'accesso.|
|52004|L'utente non ha fornito il consenso per l'accesso alle risorse di LinkedIn. |
|53000|Il criterio di accesso condizionale richiede un dispositivo conforme e il dispositivo non è conforme. Chiedere all'utente di registrare il dispositivo con un provider MDM approvato, come Intune.|
|53001|I criteri di accesso condizionale richiedono un dispositivo aggiunto al dominio e il dispositivo non è aggiunto al dominio. Chiedere all'utente di usare un dispositivo aggiunto al dominio.|
|53002|L'applicazione utilizzata non è un'applicazione approvata per l'accesso condizionale. L'utente deve usare una delle app dell'elenco di applicazioni approvate per l'uso per ottenere l'accesso.|
|53003|L'accesso è stato bloccato a causa di criteri di accesso condizionale.|
|53004|L'utente deve completare il processo di registrazione dell'autenticazione a più fattori prima di accedere al contenuto. L'utente deve eseguire la registrazione per l'autenticazione a più fattori.|
|65001|L'applicazione X non è autorizzata ad accedere all'applicazione Y o l'autorizzazione è stata revocata. Oppure, l'utente o l'amministratore non ha acconsentito all'uso dell'applicazione con ID X. Inviare una richiesta di autorizzazione interattiva per questo utente e questa risorsa. Oppure, l'utente o l'amministratore non ha acconsentito all'uso dell'applicazione con ID X. Inviare all'amministratore del tenant una richiesta di autorizzazione ad agire per conto dell'applicazione Y per la risorsa Z.|
|65004|L'utente ha rifiutato il consenso per l'accesso all'app. Chiedere all'utente di riprovare ad accedere e fornire il consenso per l'app.|
|65005|L'elenco di accesso alle risorse necessarie per l'applicazione non contiene applicazioni individuabili dalla risorsa oppure l'applicazione client ha richiesto l'accesso a una risorsa non specificata nell'elenco di accesso alle risorse necessarie oppure il servizio Graph ha restituito una risposta di richiesta non valida o risorsa non trovata. Se l'applicazione supporta SAML, potrebbe essere stata configurata con l'identificatore errato (entità). Provare la soluzione indicata per SAML usando il collegamento seguente: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav)|
|70000|Concessione non valida a causa dei motivi seguenti:<ul><li>L'asserzione SAML 2.0 richiesta ha un metodo di conferma del soggetto non valido</li><li>Il flusso OnBehalfOf dell'app non è supportato nella versione V2</li><li>Il token di aggiornamento primario non è firmato con la chiave della sessione</li><li>Il token di aggiornamento esterno non è valido</li><li>La concessione di accesso è stata ottenuta per un tenant diverso</li></ul>|
|70001|L'applicazione denominata X non è stata trovata nel tenant denominato Y. Ciò può verificarsi se l'applicazione con l'identificatore X non è stata installata dall'amministratore del tenant o non è consentita da uno degli utenti nel tenant. È possibile che il valore dell'identificatore per l'applicazione sia stato configurato in modo errato o che la richiesta di autenticazione sia stata inviata al tenant sbagliato.|
|70002|L'applicazione ha restituito credenziali client non valide. Contattare il proprietario dell'applicazione.|
|70003|L'applicazione ha restituito un tipo di concessione non supportato. Contattare il proprietario dell'applicazione.|
|70004|L'applicazione ha restituito un URI di reindirizzamento non valido. L'indirizzo di reindirizzamento specificato dal client non corrisponde ad alcun indirizzo configurato o ad alcun indirizzo nell'elenco OIDC approvato. Contattare il proprietario dell'applicazione.|
|70005|L'applicazione ha restituito un tipo di risposta non supportato a causa dei motivi seguenti:<ul><li>Il tipo di risposta "token" non è abilitato per l'applicazione</li><li>Il tipo di risposta "id_token"' richiede l'ambito "OpenID" o contiene un valore di parametro OAuth non supportato nell'elemento wctx codificato</li></ul>Contattare il proprietario dell'applicazione.|
|70007|L'applicazione ha restituito un valore non supportato di "response_mode" durante la richiesta di un token. Contattare il proprietario dell'applicazione.|
|70008|Il codice di autorizzazione fornito o il token di aggiornamento è scaduto o è stato revocato. Chiedere all'utente di provare a eseguire di nuovo l'accesso.|
|70011|L'ambito richiesto dall'applicazione non è valido. Contattare il proprietario dell'applicazione.|
|70012|Si è verificato un errore del server durante l'autenticazione di un utente (consumer) dell'account del servizio gestito. Ripetere l'accesso e, se il problema persiste, [aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) |
|70018|Codice di verifica non valido a causa di un errore di digitazione del codice utente per il flusso di codice del dispositivo. L'autorizzazione non è stata approvata.|
|70019|Il codice di verifica è scaduto. Chiedere all'utente di provare a ripetere l'accesso.|
|70037|È stata fornita una risposta non corretta alla richiesta di verifica. Sessione di autorizzazione remota negata.|
|75001|Si è verificato un errore durante l'associazione di messaggi SAML.|
|75003|L'applicazione ha restituito un errore relativo a un'associazione non supportata (la risposta del protocollo SAML non può essere inviata tramite associazioni diverse da HTTP POST). Contattare il proprietario dell'applicazione.|
|75005|Azure AD non supporta la richiesta di SAML inviata dall'applicazione per il Single Sign-On. Contattare il proprietario dell'applicazione.|
|75008|La richiesta dall'applicazione è stata negata perché la richiesta SAML contiene una destinazione imprevista. Contattare il proprietario dell'applicazione.|
|75011|Il metodo di autenticazione usato dall'utente per il servizio non corrisponde al metodo di autenticazione richiesto. Contattare il proprietario dell'applicazione.|
|75016|La richiesta di autenticazione SAML2 ha un elemento NameIdPolicy non valido. Contattare il proprietario dell'applicazione.|
|80001|L'agente di autenticazione non è in grado di connettersi ad Active Directory. Verificare che l'agente di autenticazione sia installato in un computer aggiunto al dominio che dispone di visibilità su un controller di dominio che può servire la richiesta di accesso dell'utente.|
|80002|Errore interno. Si è verificato il timeout della richiesta di convalida della password. Non è stato possibile inviare la richiesta di autenticazione al servizio di gestione delle identità ibride interno. [Aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) per ottenere altri dettagli sull'errore.|
|80003|Risposta non valida ricevuta dall'agente di autenticazione. Si è verificato un errore sconosciuto durante il tentativo di autenticazione nell'istanza locale di Active Directory. [Aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) per ottenere altri dettagli sull'errore.|
|80005|Agente di autenticazione: si è verificato un errore sconosciuto durante l'elaborazione della risposta dall'agente di autenticazione. [Aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) per ottenere altri dettagli sull'errore.|
|80007|L'agente di autenticazione non riesce a convalidare la password dell'utente.|
|80010|L'agente di autenticazione non è in grado di decrittografare la password. |
|80011|L'agente di autenticazione non è in grado di recuperare la chiave di crittografia.|
|80012|Gli utenti hanno cercato di eseguire l'accesso al di fuori delle ore consentite (specificate in AD).|
|80013|Il tentativo di autenticazione non è stato completato a causa di uno sfasamento dell'ora tra il computer che esegue l'agente di autenticazione e AD. Correggere i problemi di sincronizzazione dell'ora.|
|80014|Si è verificato il timeout dell'agente di autenticazione. [Aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) con codice errore, ID di correlazione e valore datetime per ottenere altri dettagli su questo errore.|
|81001|Il ticket Kerberos dell'utente è troppo grande. Ciò può verificarsi se l'utente è incluso in un numero eccessivo di gruppi e quindi il ticket Kerberos contiene troppe appartenenze ai gruppi. Ridurre l'appartenenza a gruppi dell'utente e riprovare.|
|81005|Il pacchetto di autenticazione non è supportato.|
|81007|Il tenant non è abilitato per l'accesso Seamless SSO.|
|81012|Questa non è una condizione di errore. Indica che l'utente che sta tentando di accedere ad Azure AD è diverso dall'utente che ha eseguito l'accesso al dispositivo. È possibile ignorare questo messaggio di errore nei log.|
|90010|La richiesta non è supportata per diversi motivi. La richiesta viene effettuata ad esempio con un metodo di richiesta non supportata (solo il metodo POST è supportato) o l'algoritmo di firma del token richiesto non è supportato. Contattare lo sviluppatore dell'applicazione.|
|90014| Un campo obbligatorio per un messaggio di protocollo non è presente. Contattare il proprietario dell'applicazione. Il proprietario dell'applicazione deve verificare di disporre di tutti i parametri necessari per la richiesta di accesso. |
|90051| Token di delega non valido. È stato specificato un ID cloud nazionale non valido ({cloudId}).|
|90072| L'account deve essere prima aggiunto come utente esterno nel tenant. Disconnettersi ed eseguire nuovamente l'accesso con un account Azure AD diverso.|
|90094| La concessione richiede le autorizzazioni di amministratore. Chiedere all'amministratore del tenant di fornire il consenso per l'applicazione.|
|500021|Il tenant è limitato dal proxy aziendale. Negazione dell'accesso alle risorse.|
|500121| Autenticazione non riuscita durante la richiesta di autenticazione avanzata.|
|500133| L'asserzione non è compresa nell'intervallo di tempo valido. Assicurarsi che il token di accesso non sia scaduto prima di usarlo per l'asserzione utente o richiedere un nuovo token.|
|530021|L'applicazione non soddisfa i requisiti dell'app approvata per l'accesso condizionale.|
|7000218|Il corpo della richiesta deve contenere il parametro seguente:' client_assertion ' o ' client_secret '.|


## <a name="next-steps"></a>Passaggi successivi

* [Panoramica dei report sugli accessi](concept-sign-ins.md)
* [Accesso programmatico ai report di Azure AD](concept-reporting-api.md)
