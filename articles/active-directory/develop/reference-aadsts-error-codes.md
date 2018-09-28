---
title: Codici di errore di autenticazione e autorizzazione di Azure Active Directory | Microsoft Docs
description: Informazioni sui codici di errore AADSTS restituiti dal servizio token di sicurezza di Azure AD.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: hirsin, justhu
ms.custom: aaddev
ms.openlocfilehash: 0643ae39d24f8ef0c1c1c18dcf73cf840c95e277
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46956330"
---
# <a name="authentication-and-authorization-error-codes"></a>Codici di errore di autenticazione e autorizzazione

Se sono necessarie informazioni sui codici di errore AADSTS restituiti dal servizio token di sicurezza di Azure Active Directory (Azure AD), consultare questo documento per trovare le descrizioni degli errori AADSTS, le correzioni e alcune soluzioni alternative consigliate.

> [!NOTE]
> Queste informazioni sono preliminari e soggette a modifiche. In caso di domande o se non è possibile trovare le informazioni necessarie, creare un problema in GitHub oppure fare riferimento alle [opzioni di supporto e assistenza per sviluppatori](active-directory-develop-help-support.md) per informazioni su come ottenere supporto.

## <a name="aadsts-error-codes"></a>Codici di errore AADSTS

| Tipi di errore | DESCRIZIONE |
|---|---|
| AADSTS16000 | SelectUserAccount: interrupt generato da Azure AD, con conseguente attivazione dell'interfaccia utente che permette all'utente di selezionare tra più sessioni SSO valide. Questo errore è piuttosto comune e può essere restituito all'applicazione se è specificato `prompt=none`. |
| AADSTS16001 | UserAccountSelectionInvalid: questo errore viene visualizzato se l'utente fa clic su un riquadro che è stato rifiutato dalla logica di selezione della sessione. Se attivato, questo errore permette all'utente di rimediare effettuando la selezione in un elenco aggiornato di riquadri/sessioni o scegliendo un altro account. Questo errore può verificarsi a causa di un problema del codice o di una condizione di race condition. |
| AADSTS16002 | AppSessionSelectionInvalid: il requisito relativo al SID specificato dall'app non è stato soddisfatto.  |
| AADSTS16003 | SsoUserAccountNotFoundInResourceTenant |
| AADSTS17003 | CredentialKeyProvisioningFailed |
| AADSTS20001 | WsFedSignInResponseError: si è verificato un problema relativo al provider di identità federato. Contattare l'IDP per risolvere il problema. |
| AADSTS20012 | WsFedMessageInvalid: si è verificato un problema relativo al provider di identità federato. Contattare l'IDP per risolvere il problema. |
| AADSTS20033 | FedMetadataInvalidTenantName: si è verificato un problema relativo al provider di identità federato. Contattare l'IDP per risolvere il problema. |
| AADSTS40008 | OAuth2IdPUnretryableServerError: si è verificato un problema relativo al provider di identità federato. Contattare l'IDP per risolvere il problema. |
| AADSTS40009 | OAuth2IdPRefreshTokenRedemptionUserError: si è verificato un problema relativo al provider di identità federato. Contattare l'IDP per risolvere il problema. |
| AADSTS40010 | OAuth2IdPRetryableServerError: si è verificato un problema relativo al provider di identità federato. Contattare l'IDP per risolvere il problema. |
| AADSTS40015 | OAuth2IdPAuthCodeRedemptionUserError: si è verificato un problema relativo al provider di identità federato. Contattare l'IDP per risolvere il problema. |
| AADSTS50000 | TokenIssuanceError: si è verificato un problema relativo al servizio di accesso. [Aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) per risolvere il problema. |
| AADSTS50001 | InvalidResource: la risorsa è disabilitata o non esiste. Controllare il codice dell'app per verificare di avere specificato l'URL della risorsa esatta cui si sta tentando di accedere.  |
| AADSTS50002 | NotAllowedTenant: accesso non riuscito a causa di limitazioni di accesso al proxy nel tenant. Se questo errore è dovuto ai propri criteri per il tenant, è possibile modificare le impostazioni di limitazione per il tenant per correggerlo. |
| AADSTS50003 | MissingSigningKey: accesso non riuscito a causa di una chiave o un certificato di firma mancante. Questo errore può essere dovuto al fatto che non è stata configurata alcuna chiave di firma nell'app. Vedere le soluzioni illustrate in [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#certificate-or-key-not-configured). Se il problema persiste, contattare il proprietario o l'amministratore dell'app. |
| AADSTS50005 | DevicePolicyError: l'utente ha tentato di accedere a un dispositivo da una piattaforma attualmente non supportata tramite criteri di accesso condizionale. |
| AADSTS50006 | InvalidSignature: la verifica della firma non è riuscita a causa di una firma non valida. |
| AADSTS50007 | PartnerEncryptionCertificateMissing: non è stato trovato il certificato di crittografia del partner per l'app. [Aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) presso Microsoft per richiedere la risoluzione del problema. |
| AADSTS50008 | InvalidSamlToken: l'asserzione SAML non è presente o non è configurata correttamente nel token. Contattare il provider di federazione. |
| AADSTS50010 | AudienceUriValidationFailed: la convalida dell'URI destinatario per l'app non è riuscita perché non è stato configurato alcun destinatario del token. |
| AADSTS50011 | InvalidReplyTo: l'indirizzo di risposta è mancante, non è configurato correttamente o non corrisponde agli indirizzi di risposta configurati per l'app. Provare la soluzione illustrata in [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application). Se il problema persiste, contattare il proprietario o l'amministratore dell'app. |
| AADSTS50012 | AuthenticationFailed: l'autenticazione non è riuscita per uno dei motivi seguenti:<ul><li>Il nome soggetto del certificato di firma non è autorizzato</li><li>Non sono stati trovati criteri di autorità attendibile corrispondenti per il nome soggetto autorizzato</li><li>La catena di certificati non è valida</li><li>Il certificato di firma non è valido</li><li>Non sono configurati criteri nel tenant</li><li>L'identificazione personale del certificato di firma non è autorizzata</li><li>l'asserzione client contiene una firma non valida</li></ul> |
| AADSTS50013 | InvalidAssertion: l'asserzione non è valida per vari motivi: - L'emittente del token non corrisponde alla versione API nell'intervallo di tempo valido - È scaduta - Ha un formato non valido - Il token di aggiornamento nell'asserzione non è un token di aggiornamento primario. |
| AADSTS50014 | GuestUserInPendingState |
| AADSTS50015 | ViralUserLegalAgeConsentRequiredState |
| AADSTS50017 | CertificateValidationFailed: la convalida della certificazione non è riuscita a causa dei motivi seguenti:<ul><li>Non è possibile trovare la certificazione nell'elenco di certificati attendibili</li><li>Non è possibile trovare l'elemento CrlSegment previsto</li><li>Non è possibile trovare la certificazione nell'elenco di certificati attendibili</li><li>È configurato un punto di distribuzione CRL differenziale senza un punto di distribuzione CRL corrispondente</li><li>Non è possibile recuperare segmenti CRL validi a causa di un problema di timeout</li><li>Non è possibile scaricare il documento CRL</li></ul>Contattare l'amministratore del tenant. |
| AADSTS50020 | UserUnauthorized: gli utenti non sono autorizzati a chiamare questo endpoint. |
| AADSTS50027 | InvalidJwtToken: token JWT non valido a causa dei motivi seguenti:<ul><li>Non contiene un'attestazione nonce o un'attestazione secondaria</li><li>L'identificatore soggetto non corrisponde</li><li>Attestazione duplicata nelle attestazioni idToken</li><li>Autorità di certificazione imprevista</li><li>Destinatari imprevisti</li><li>Al di fuori dell'intervallo di tempo valido </li><li>Formato del token non corretto</li><li>Il token ID esterno dell'autorità di certificazione non ha superato la verifica della firma</li></ul> |
| AADSTS50029 | URI non valido. Il nome di dominio contiene caratteri non validi. Contattare l'amministratore del tenant. |
| AADSTS50032 | WeakRsaKey: indica il tentativo errato dell'utente di usare una chiave RSA vulnerabile. |
| AADSTS50033 | RetryableError: indica un errore temporaneo non correlato alle operazioni del database. |
| AADSTS50034 | UserAccountNotFound: per accedere all'applicazione, l'account deve essere aggiunto alla directory. |
| AADSTS50042 | UnableToGeneratePairwiseIdentifierWithMissingSalt: il valore salt richiesto per generare un identificatore pairwise non è presente nell'entità di sicurezza. Contattare l'amministratore del tenant. |
| AADSTS50043 | UnableToGeneratePairwiseIdentifierWithMultipleSalts |
| AADSTS50048 | SubjectMismatchesIssuer: il soggetto non corrisponde all'attestazione dell'autorità di certificazione nell'asserzione client. Contattare l'amministratore del tenant. |
| AADSTS50049 | NoSuchInstanceForDiscovery: istanza sconosciuta o non valida. |
| AADSTS50050 | MalformedDiscoveryRequest: la richiesta ha un formato non valido. |
| AADSTS50053 | IdsLocked: l'account è bloccato perché l'utente ha tentato di accedere troppe volte con un ID utente e/o una password non corretti. |
| AADSTS50055 | InvalidPasswordExpiredPassword: la password è scaduta. |
| AADSTS50056 | Password non valida o null: la password non è presente nell'archivio per questo utente. |
| AADSTS50057 | UserDisabled: l'account utente è disabilitato. L'account è stato disabilitato da un amministratore. |
| AADSTS50058 | UserInformationNotProvided: un utente non ha effettuato l'accesso. Si tratta di un errore molto comune che si verifica in genere quando un utente non è autenticato e non ha ancora effettuato l'accesso.</br>Se questo errore viene visualizzato in un contesto SSO in cui l'utente ha appena effettuato l'accesso, significa che la sessione SSO non è stata trovata o non è valida.</br>Questo errore può essere restituito all'applicazione se è specificato prompt=none. |
| AADSTS50059 | MissingTenantRealmAndNoUserInformationProvided: non sono state trovate informazioni di identificazione del tenant nella richiesta né sono state incluse in modo implicito nelle credenziali fornite. L'utente può contattare l'amministratore del tenant per ottenere informazioni per la risoluzione del problema. |
| AADSTS50061 | SignoutInvalidRequest: la richiesta di disconnessione non è valida. |
| AADSTS50064 | CredentialAuthenticationError |
| AADSTS50068 | SignoutInitiatorNotParticipant |
| AADSTS50070 | SignoutUnknownSessionIdentifier |
| AADSTS50071 | SignoutMessageExpired |
| AADSTS50072 | UserStrongAuthEnrollmentRequiredInterrupt: l'utente deve registrarsi per l'autenticazione a due fattori (interattiva). |
| AADSTS50074 | UserStrongAuthClientAuthNRequiredInterrupt: è necessaria l'autenticazione avanzata e l'utente non ha superato la verifica MFA. |
| AADSTS50076 | UserStrongAuthClientAuthNRequired: l'utente deve usare l'autenticazione a più fattori per accedere a questa risorsa. Riprovare con una nuova richiesta di autorizzazione per la risorsa. |
| AADSTS50079 | UserStrongAuthEnrollmentRequired: a causa di una modifica di configurazione apportata dall'amministratore o del trasferimento dell'utente in una nuova località, l'utente è tenuto a usare l'autenticazione a più fattori. |
| AADSTS50085 | Il token di aggiornamento richiede un accesso IDP social. Chiedere all'utente di provare a eseguire di nuovo l'accesso con nome utente e password. |
| AADSTS50086 | SasNonRetryableError |
| AADSTS50087 | SasRetryableError |
| AADSTS50089 | Il token di flusso è scaduto. L'autenticazione non è riuscita. Chiedere all'utente di provare a eseguire di nuovo l'accesso con nome utente e password. |
| AADSTS50097 | DeviceAuthenticationRequired: l'autenticazione del dispositivo è obbligatoria. |
| AADSTS50099 | PKeyAuthInvalidJwtUnauthorized: la firma del token JWT non è valida. |
| AADSTS50105 | EntitlementGrantsNotFound: all'utente che ha eseguito l'accesso non è assegnato alcun ruolo per l'app connessa. Assegnare l'utente all'app. Per altre informazioni: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#user-not-assigned-a-role). |
| AADSTS50107 | InvalidRealmUri: l'oggetto dell'area di autenticazione della federazione richiesto non esiste. Contattare l'amministratore del tenant. |
| AADSTS50120 | ThresholdJwtInvalidJwtFormat: problema relativo all'intestazione JWT. Contattare l'amministratore del tenant. |
| AADSTS50124 | ClaimsTransformationInvalidInputParameter: la trasformazione delle attestazioni contiene un parametro di input non valido. Contattare l'amministratore del tenant per aggiornare i criteri. |
| AADSTS50125 | PasswordResetRegistrationRequiredInterrupt: l'accesso è stato interrotto a causa della reimpostazione della password o di una voce di registrazione password. |
| AADSTS50126 | InvalidUserNameOrPassword: errore durante la convalida delle credenziali a causa di un nome utente e/o di una password non validi. |
| AADSTS50127 | BrokerAppNotInstalled: l'utente deve installare un'app broker per ottenere l'accesso a questo contenuto. |
| AADSTS50128 | Nome di dominio non valido. Non sono state trovate informazioni di identificazione del tenant nella richiesta o incluse in modo implicito nelle credenziali fornite.|
| AADSTS50129 | DeviceIsNotWorkplaceJoined: per registrare il dispositivo, è necessaria l'aggiunta alla rete aziendale. |
| AADSTS50131 | ConditionalAccessFailed: indica diversi errori di accesso condizionale, ad esempio a causa di uno stato del dispositivo Windows non valido, di una richiesta bloccata a causa di un'attività sospetta o di decisioni relative a criteri di accesso o criteri di sicurezza. |
| AADSTS50132 | SsoArtifactInvalidOrExpired: la sessione non è valida perché la password è scaduta o è stata modificata di recente. |
| AADSTS50133 | SsoArtifactRevoked: la sessione non è valida perché la password è scaduta o è stata modificata di recente. |
| AADSTS50134 | DeviceFlowAuthorizeWrongDatacenter |
| AADSTS50135 | PasswordChangeCompromisedPassword: è necessario modificare la password a causa di rischi per l'account. |
| AADSTS50136 | RedirectMsaSessionToApp: è stata rilevata una singola sessione dell'account del servizio gestito. |
| AADSTS50139 | SessionMissingMsaOAuth2RefreshToken |
| AADSTS50140 | KmsiInterrupt: questo errore si è verificato a causa di un interrupt del comando "Mantieni l'accesso" durante l'accesso dell'utente. [Aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) con ID di correlazione, ID richiesta e codice di errore per ottenere altri dettagli. |
| AADSTS50143 | Mancata corrispondenza di sessione. La sessione non è valida perché il tenant dell'utente non corrisponde all'hint di dominio a causa di una risorsa diversa. [Aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) con ID di correlazione, ID richiesta e codice di errore per ottenere altri dettagli. |
| AADSTS50144 | InvalidPasswordExpiredOnPremPassword: la password di Active Directory dell'utente è scaduta. Generare una nuova password per l'utente o chiedere all'utente di usare lo strumento di reimpostazione self-service per reimpostare la password. |
| AADSTS50146 | MissingCustomSigningKey: questa app deve essere configurata con una chiave di firma specifica dell'app. O non è configurata con tale chiave oppure la chiave è scaduta o non è ancora valida. |
| AADSTS50147 | MissingCodeChallenge |
| AADSTS50155 | DeviceAuthenticationFailed: l'autenticazione del dispositivo non è riuscita per questo utente. |
| AADSTS50158 | ExternalSecurityChallenge: la richiesta di sicurezza esterna non è stata soddisfatta. |
| AADSTS50161 | InvalidExternalSecurityChallengeConfiguration: le attestazioni inviate dal provider esterno non sono sufficienti oppure manca un'attestazione richiesta al provider esterno. |
| AADSTS50166 | ExternalClaimsProviderThrottled: non è stato possibile inviare la richiesta al provider di attestazioni. |
| AADSTS50168 | ChromeBrowserSsoInterruptRequired: il client è in grado di ottenere un token SSO tramite l'estensione per gli account Windows 10, ma il token non è stato trovato nella richiesta o il token specificato è scaduto. |
| AADSTS50169 | InvalidRequestBadRealm: l'area di autenticazione non è un'area di autenticazione configurata dello spazio dei nomi del servizio corrente. |
| AADSTS50170 | MissingExternalClaimsProviderMapping |
| AADSTS50177 | ExternalChallengeNotSupportedForPassthroughUsers: la richiesta di verifica esterna non è supportata per gli utenti pass-through. |
| AADSTS50178 | SessionControlNotSupportedForPassthroughUsers: il controllo della sessione non è supportato per gli utenti pass-through. |
| AADSTS50180 | WindowsIntegratedAuthMissing: è necessaria l'autenticazione integrata di Windows. Abilitare il tenant per Seamless SSO. |
| AADSTS50187 | DeviceInformationNotProvided |
| AADSTS51000 | RequiredFeatureNotEnabled |
| AADSTS51001 | DomainHintMustbePresent: deve essere presente il suggerimento di dominio con l'ID di sicurezza locale o l'UPN locale. |
| AADSTS51004 | UserAccountNotInDirectory: l'account utente non è presente nella directory. |
| AADSTS51005 | TemporaryRedirect: equivale allo stato HTTP 307, che indica che le informazioni richieste si trovano nell'URI specificato nell'intestazione location. Quando viene restituito questo stato, seguire l'intestazione location associata alla risposta. Quando il metodo di richiesta originale è POST, anche la richiesta reindirizzata userà il metodo POST. |
| AADSTS51006 | ForceReauthDueToInsufficientAuth: è necessaria l'autenticazione integrata di Windows. L'utente ha eseguito l'accesso con un token di sessione in cui manca l'attestazione di autenticazione integrata di Windows. Chiedere all'utente di ripetere l'accesso. |
| AADSTS52004 | DelegationDoesNotExistForLinkedIn: l'utente non ha fornito il proprio consenso per l'accesso a risorse di LinkedIn. |
| AADSTS53000 | DeviceNotCompliant: il criterio di accesso condizionale richiede un dispositivo conforme, ma il dispositivo non è conforme. L'utente deve registrare il dispositivo con un provider MDM approvato, come Intune. |
| AADSTS53001 | DeviceNotDomainJoined: il criterio di accesso condizionale richiede un dispositivo aggiunto al dominio, ma il dispositivo non è aggiunto al dominio. Chiedere all'utente di usare un dispositivo aggiunto al dominio. |
| AADSTS53002 | ApplicationUsedIsNotAnApprovedApp: l'app usata non è un'app approvata per l'accesso condizionale. L'utente deve usare una delle app incluse nell'elenco delle app approvate per l'uso per ottenere l'accesso. |
| AADSTS53003 | BlockedByConditionalAccess: l'accesso è stato bloccato da criteri di accesso condizionale. Il criterio di accesso non permette il rilascio di token. |
| AADSTS53004 | ProofUpBlockedDueToRisk: l'utente deve completare il processo di registrazione dell'autenticazione a più fattori prima di accedere al contenuto. L'utente deve eseguire la registrazione per l'autenticazione a più fattori. |
| AADSTS54000 | MinorUserBlockedLegalAgeGroupRule |
| AADSTS65001 | DelegationDoesNotExist: l'utente o l'amministratore non ha acconsentito all'uso dell'applicazione con ID X. Inviare una richiesta di autorizzazione interattiva per questo utente e questa risorsa. |
| AADSTS65004 | UserDeclinedConsent: l'utente ha rifiutato il consenso per l'accesso all'app. Chiedere all'utente di riprovare ad accedere e fornire il consenso per l'app.|
| AADSTS65005 | MisconfiguredApplication: l'elenco di accesso alle risorse necessarie per l'applicazione non contiene app individuabili dalla risorsa, l'app client ha richiesto l'accesso a una risorsa non specificata nell'elenco di accesso alle risorse necessarie oppure il servizio Graph ha restituito una risposta di richiesta non valida o di risorsa non trovata. Se l'app supporta SAML, potrebbe essere stata configurata con l'identificatore errato (entità). Provare la soluzione indicata per SAML usando il collegamento seguente: [https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery#no-resource-in-requiredresourceaccess-list](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DMC_AAD_Manage_Apps_Troubleshooting_Nav#no-resource-in-requiredresourceaccess-list) |
| AADSTS67003 | ActorNotValidServiceIdentity |
| AADSTS70000 | InvalidGrant: autenticazione non riuscita. Il token di aggiornamento non è valido. Errore dovuto ai motivi seguenti:<ul><li>L'intestazione di associazione di token è vuota</li><li>L'hash di associazione di token non corrisponde</li></ul> |
| AADSTS70001 | UnauthorizedClient: l'applicazione è disabilitata. |
| AADSTS70002 | InvalidClient: errore di convalida delle credenziali. Il valore di client_secret specificato non corrisponde al valore previsto per questo client. Correggere il valore di client_secret e riprovare. Per altre informazioni, vedere [Usare un codice di autorizzazione per richiedere un token di accesso](v1-protocols-oauth-code.md#use-the-authorization-code-to-request-an-access-token). |
| AADSTS70003 | UnsupportedGrantType: l'app ha restituito un tipo di concessione non supportato. |
| AADSTS70004 | InvalidRedirectUri: l'app ha restituito un URI di reindirizzamento non valido. L'indirizzo di reindirizzamento specificato dal client non corrisponde ad alcun indirizzo configurato o ad alcun indirizzo nell'elenco OIDC approvato. |
| AADSTS70005 | UnsupportedResponseType: l'app ha restituito un tipo di risposta non supportato a causa dei motivi seguenti:<ul><li>Il tipo di risposta "token" non è abilitato per l'app</li><li>Il tipo di risposta "id_token"' richiede l'ambito "OpenID" o contiene un valore di parametro OAuth non supportato nell'elemento wctx codificato</li></ul> |
| AADSTS70007 | UnsupportedResponseMode: l'app ha restituito un valore non supportato per `response_mode` durante la richiesta di un token.  |
| AADSTS70008 | ExpiredOrRevokedGrant: il token di aggiornamento è scaduto a causa di inattività. Il token è stato rilasciato in data XXX ed è rimasto inattivo per un certo periodo di tempo. |
| AADSTS70011 | InvalidScope: l'ambito richiesto dall'app non è valido. |
| AADSTS70012 | MsaServerError: si è verificato un errore del server durante l'autenticazione di un utente (consumer) dell'account del servizio gestito. Riprovare. Se il problema persiste, [aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md). |
| AADSTS70016 | AuthorizationPending: errore del flusso del dispositivo OAuth 2.0. L'autorizzazione è in sospeso. Il dispositivo ritenterà il polling della richiesta. |
| AADSTS70018 | BadVerificationCode: codice di verifica non valido a causa di un errore di digitazione del codice per il flusso del codice del dispositivo da parte dell'utente. L'autorizzazione non è stata approvata. |
| AADSTS70019 | CodeExpired: il codice di verifica è scaduto. Chiedere all'utente di provare a ripetere l'accesso. |
| AADSTS75001 | BindingSerializationError: si è verificato un errore durante l'associazione di messaggi SAML. |
| AADSTS75003 | UnsupportedBindingError: l'app ha restituito un errore relativo a un'associazione non supportata (la risposta del protocollo SAML non può essere inviata tramite associazioni diverse da HTTP POST). |
| AADSTS75005 | Saml2MessageInvalid: Azure AD non supporta la richiesta di SAML inviata dall'app per SSO. |
| AADSTS75008 | RequestDeniedError: la richiesta inviata dall'app è stata negata perché la richiesta SAML contiene una destinazione imprevista. |
| AADSTS75011 | NoMatchedAuthnContextInOutputClaims: il metodo di autenticazione usato dall'utente per il servizio non corrisponde al metodo di autenticazione richiesto. |
| AADSTS75016 | Saml2AuthenticationRequestInvalidNameIDPolicy: la richiesta di autenticazione SAML2 contiene un elemento NameIdPolicy non valido. |
| AADSTS80001 | OnPremiseStoreIsNotAvailable: l'agente di autenticazione non è in grado di connettersi ad Active Directory. Verificare che i server degli agenti siano membri della stessa foresta AD degli utenti le cui password devono essere convalidate e che siano in grado di connettersi ad Active Directory. |
| AADSTS80002 | OnPremisePasswordValidatorRequestTimedout: si è verificato il timeout della richiesta di convalida della password. Verificare che Active Directory sia disponibile e risponda alle richieste degli agenti. |
| AADSTS80005 | OnPremisePasswordValidatorUnpredictableWebException: si è verificato un errore sconosciuto durante l'elaborazione della risposta dall'agente di autenticazione. Ripetere la richiesta. Se il problema persiste, [aprire un ticket di supporto](../fundamentals/active-directory-troubleshooting-support-howto.md) per ottenere altri dettagli sull'errore. |
| AADSTS80007 | OnPremisePasswordValidatorErrorOccurredOnPrem: l'agente di autenticazione non riesce a convalidare la password dell'utente. Controllare i log dell'agente per altre informazioni e verificare che Active Directory funzioni come previsto. |
| AADSTS80010 | OnPremisePasswordValidationEncryptionException: l'agente di autenticazione non riesce a decrittografare la password. |
| AADSTS80012 | OnPremisePasswordValidationAccountLogonInvalidHours: gli utenti hanno tentato di eseguire l'accesso al di fuori delle ore consentite (specificate in Active Directory). |
| AADSTS80013 | OnPremisePasswordValidationTimeSkew: non è stato possibile completare il tentativo di autenticazione a causa di uno sfasamento dell'ora tra il computer che esegue l'agente di autenticazione e Active Directory. Correggere i problemi di sincronizzazione dell'ora. |
| AADSTS81004 | DesktopSsoIdentityInTicketIsNotAuthenticated: un tentativo di autenticazione Kerberos non è riuscito. |
| AADSTS81005 | DesktopSsoAuthenticationPackageNotSupported: il pacchetto di autenticazione non è supportato. |
| AADSTS81006 | DesktopSsoNoAuthorizationHeader: non è stata trovata alcuna intestazione dell'autorizzazione. |
| AADSTS81007 | DesktopSsoTenantIsNotOptIn: il tenant non è abilitato per Seamless SSO. |
| AADSTS81009 | DesktopSsoAuthorizationHeaderValueWithBadFormat: non è stato possibile convalidare il ticket Kerberos dell'utente. |
| AADSTS81010 | DesktopSsoAuthTokenInvalid: l'accesso Seamless SSO non è riuscito perché il ticket Kerberos dell'utente è scaduto o non è valido. |
| AADSTS81011 | DesktopSsoLookupUserBySidFailed: non è stato possibile trovare l'oggetto utente in base alle informazioni contenute nel ticket Kerberos dell'utente. |
| AADSTS81012 | DesktopSsoMismatchBetweenTokenUpnAndChosenUpn: l'utente che sta tentando di accedere ad Azure AD è diverso dall'utente che ha effettuato l'accesso al dispositivo. |
| AADSTS90002 | InvalidTenantName: il nome del tenant non è stato trovato nell'archivio dati. Verificare di avere l'ID tenant corretto. |
| AADSTS90004 | InvalidRequestFormat |
| AADSTS90005 | InvalidRequestWithMultipleRequirements |
| AADSTS90006 | ExternalServerRetryableError |
| AADSTS90007 | InvalidSessionId |
| AADSTS90008 | TokenForItselfRequiresGraphPermission |
| AADSTS90009 | TokenForItselfMissingIdenticalAppIdentifier |
| AADSTS90010 | NotSupported |
| AADSTS90012 | RequestTimeout |
| AADSTS90013 | InvalidUserInput |
| AADSTS90014 | MissingRequiredField: questo codice di errore può essere restituito in diversi casi quando un campo previsto non è presente nelle credenziali. |
| AADSTS90015 | QueryStringTooLong |
| AADSTS90016 | MissingRequiredClaim |
| AADSTS90019 | MissingTenantRealm: Azure AD non è riuscito a determinare l'identificatore del tenant dalla richiesta. |
| AADSTS90022 | AuthenticatedInvalidPrincipalNameFormat: il nome dell'entità ha un formato non valido o non soddisfa il formato `name[/host][@realm]` previsto. Il nome dell'entità è obbligatorio, l'host e l'area di autenticazione sono facoltativi e possono essere impostati su null. |
| AADSTS90023 | InvalidRequest |
| AADSTS90024 | RequestBudgetExceededError |
| AADSTS90033 | MsodsServiceUnavailable |
| AADSTS90036 | MsodsServiceUnretryableFailure |
| AADSTS90038 | NationalCloudTenantRedirection: il tenant specificato "Y" appartiene al cloud nazionale "X". L'istanza cloud "Z" corrente non è federata con "X". Viene restituito un errore di reindirizzamento al cloud. |
| AADSTS90043 | NationalCloudAuthCodeRedirection |
| AADSTS90051 | InvalidNationalCloudId |
| AADSTS90055 | TenantThrottlingError: sono presenti troppe richieste in ingresso. Questa eccezione viene generata per i tenant bloccati. |
| AADSTS90056 | BadResourceRequest: per riscattare il codice per un token di accesso, l'app deve inviare una richiesta POST all'endpoint `/token`. Prima di questa operazione, inoltre, è necessario fornire un codice di autorizzazione e inviarlo nella richiesta POST all'endpoint `/token`. Fare riferimento a questo articolo per una panoramica del flusso del codice di autorizzazione OAuth 2.0: [https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code). Prima di tutto, è necessario reindirizzare l'utente all'endpoint `/authorize`, che restituirà un elemento authorization_code. Inviando una richiesta all'endpoint `/token`, l'utente ottiene il token di accesso. Accedere al portale di Azure e controllare **Registrazioni per l'app > Endpoint** per verificare che i due endpoint siano stati configurati correttamente. |
| AADSTS90072 | PassThroughUserMfaError |
| AADSTS90081 | OrgIdWsFederationMessageInvalid |
| AADSTS90082 | OrgIdWsFederationNotSupported |
| AADSTS90084 | OrgIdWsFederationGuestNotAllowed |
| AADSTS90085 | OrgIdWsFederationSltRedemptionFailed |
| AADSTS90086 | OrgIdWsTrustDaTokenExpired |
| AADSTS90087 | OrgIdWsFederationMessageCreationFromUriFailed |
| AADSTS90090 | GraphRetryableError |
| AADSTS90091 | GraphServiceUnreachable |
| AADSTS90092 | GraphNonRetryableError |
| AADSTS90093 | GraphUserUnauthorized: Graph ha restituito un codice di errore di richiesta non consentita. |
| AADSTS90094 | AdminConsentRequired |
| AADSTS90100 | InvalidRequestParameter |
| AADSTS90101 | InvalidEmailAddress |
| AADSTS90102 | InvalidUriParameter |
| AADSTS90107 | InvalidXml |
| AADSTS90114 | InvalidExpiryDate |
| AADSTS90117 | InvalidRequestInput |
| AADSTS90119 | InvalidUserCode |
| AADSTS90120 | InvalidDeviceFlowRequest |
| AADSTS90121 | InvalidEmptyRequest |
| AADSTS90123 | IdentityProviderAccessDenied |
| AADSTS90124 | V1ResourceV2GlobalEndpointNotSupported |
| AADSTS90125 | DebugModeEnrollTenantNotFound |
| AADSTS90126 | DebugModeEnrollTenantNotInferred |
| AADSTS90130 | NonConvergedAppV2GlobalEndpointNotSupported |
| AADSTS120000 | PasswordChangeIncorrectCurrentPassword |
| AADSTS120002 | PasswordChangeInvalidNewPasswordWeak |
| AADSTS120003 | PasswordChangeInvalidNewPasswordContainsMemberName |
| AADSTS120004 | PasswordChangeOnPremComplexity |
| AADSTS120005 | PasswordChangeOnPremSuccessCloudFail |
| AADSTS120008 | PasswordChangeAsyncJobStateTerminated |
| AADSTS120011 | PasswordChangeAsyncUpnInferenceFailed |
| AADSTS120012 | PasswordChangeNeedsToHappenOnPrem |
| AADSTS120013 | PasswordChangeOnPremisesConnectivityFailure |
| AADSTS120014 | PasswordChangeOnPremUserAccountLockedOutOrDisabled |
| AADSTS120015 | PasswordChangeADAdminActionRequired |
| AADSTS120016 | PasswordChangeUserNotFoundBySspr |
| AADSTS120018 | PasswordChangePasswordDoesnotComplyFuzzyPolicy |
| AADSTS120020 | PasswordChangeFailure |
| AADSTS120021 | PartnerServiceSsprInternalServiceError |
| AADSTS130004 | NgcKeyNotFound |
| AADSTS130005 | NgcInvalidSignature |
| AADSTS130006 | NgcTransportKeyNotFound |
| AADSTS130007 | NgcDeviceIsDisabled |
| AADSTS130008 | NgcDeviceIsNotFound |
| AADSTS135010 | KeyNotFound |
| AADSTS140000 | InvalidRequestNonce |
| AADSTS140001 | InvalidSessionKey |
| AADSTS165000 | InvalidRequestCanary |
| AADSTS165004 | InvalidApiCanary |
| AADSTS165900 | InvalidApiRequest |
| AADSTS220450 | UnsupportedAndroidWebViewVersion |
| AADSTS220501 | InvalidCrlDownload |
| AADSTS221000 | DeviceOnlyTokensNotSupportedByResource: la risorsa non è configurata per accettare token solo dispositivo. |
| AADSTS240001 | BulkAADJTokenUnauthorized |
| AADSTS240002 | RequiredClaimIsMissing |
| AADSTS700020 | InteractionRequired |
| AADSTS700022 | InvalidMultipleResroucesScope |
| AADSTS700023 | InvalidResourcelessScope |
| AADSTS900000 | WebWatsonEnvironmentError |
| AADSTS1000000 | UserNotBoundError |
| AADSTS1000002 | BindCompleteInterruptError |

## <a name="next-steps"></a>Passaggi successivi

* In caso di domande o se non è possibile trovare le informazioni necessarie, creare un problema in GitHub oppure fare riferimento alle [opzioni di supporto e assistenza per sviluppatori](active-directory-develop-help-support.md) per informazioni su come ottenere supporto.