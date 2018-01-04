---
title: 'Azure Active Directory B2C: informazioni sui criteri personalizzati dello starter pack | Microsoft Docs'
description: Un argomento sui criteri personalizzati di Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: mtillman
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/25/2017
ms.author: joroja
ms.openlocfilehash: fccb6cfddc8629de7db0310340f07bffd1ff8a65
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="understanding-the-custom-policies-of-the-azure-ad-b2c-custom-policy-starter-pack"></a>Informazioni sui criteri personalizzati dello starter pack di Azure AD B2C

Questa sezione elenca tutti gli elementi principali del criterio B2C_1A_base disponibile nello **starter pack**, che può essere sfruttato per creare i propri criteri tramite l'ereditarietà del *criterio B2C_1A_base_extensions*.

Più in particolare vengono illustrati i tipi di attestazioni già definiti, le trasformazioni delle attestazioni, le definizioni del contenuto, i provider di attestazioni con i profili tecnici e i percorsi utente principali.

> [!IMPORTANT]
> Microsoft non rilascia alcuna garanzia, espressa o implicita, in merito alle informazioni fornite da questo punto in avanti. Potrebbero essere apportate modifiche in qualsiasi momento, prima della disponibilità a livello generale, al momento della disponibilità a livello generale o in seguito.

Sia i criteri personalizzati che il criterio B2C_1A_base_extensions possono eseguire l'override di queste definizioni ed estendere questo criterio padre fornendone altre, se necessario.

Gli elementi principali del *criterio B2C_1A_base* sono tipi di attestazioni, trasformazioni di attestazioni e definizioni del contenuto. Questi elementi possono essere usati come riferimento nei criteri personalizzati e nel *criterio B2C_1A_base_extensions*.

## <a name="claims-schemas"></a>Schema delle attestazioni

Questo schema delle attestazioni è diviso in tre sezioni:

1.  Una prima sezione che elenca le attestazioni minime necessarie per il corretto funzionamento dei percorsi utente.
2.  Una seconda sezione che elenca le attestazioni necessarie per i parametri della stringa di query e altri speciali parametri da passare ad altri provider di attestazioni, in particolare login.microsoftonline.com per l'autenticazione. **Non modificare queste attestazioni**.
3.  Infine una terza sezione che elenca altre attestazioni facoltative che possono essere raccolte dall'utente, archiviate nella directory e inviate in token durante l'accesso. In questa sezione possono essere aggiunti nuovi tipi di attestazioni che devono essere raccolte dall'utente e/o inviate nel token.

> [!IMPORTANT]
> Lo schema delle attestazioni contiene le limitazioni relative a determinate attestazioni, ad esempio password e nomi utente. Il criterio del framework attendibilità considera Azure AD come qualsiasi altro provider di attestazioni e tutte le limitazioni sono modellate nel criterio premium. Un criterio può essere modificato per aggiungere altre limitazioni o usare un altro provider di attestazioni per la risorsa di archiviazione delle credenziali che avrà le proprie limitazioni.

Di seguito sono elencati i tipi di attestazioni disponibili.

### <a name="claims-that-are-required-for-the-user-journeys"></a>Attestazioni necessarie per i percorsi utente

Le attestazioni seguenti sono necessarie per il corretto funzionamento dei percorsi utente:

| Tipo di attestazione | DESCRIZIONE |
|-------------|-------------|
| *UserId* | Username |
| *signInName* | Nome di accesso |
| *tenantId* | Identificatore (ID) tenant dell'oggetto utente in Azure AD B2C Premium |
| *objectId* | Identificatore (ID) oggetto dell'oggetto utente in Azure AD B2C Premium |
| *password* | Password |
| *newPassword* | |
| *reenterPassword* | |
| *passwordPolicies* | Criteri password usati da Azure AD B2C Premium per determinare la complessità delle password, la scadenza e così via. |
| *sub* | |
| *alternativeSecurityId* | |
| *identityProvider* | |
| *displayName* | |
| *strongAuthenticationPhoneNumber* | Numero di telefono dell'utente |
| *Verified.strongAuthenticationPhoneNumber* | |
| *email* | Indirizzo di posta elettronica che può essere usato per contattare l'utente |
| *signInNamesInfo.emailAddress* | Indirizzo di posta elettronica che l'utente può usare per l'accesso |
| *otherMails* | Indirizzi di posta elettronica che possono essere usati per contattare l'utente |
| *userPrincipalName* | Nome utente archiviato in Azure AD B2C Premium |
| *upnUserName* | Nome utente per la creazione di nome dell'entità utente |
| *mailNickName* | Nome alternativo per la posta elettronica dell'utente archiviato in Azure AD B2C Premium |
| *newUser* | |
| *executed-SelfAsserted-Input* | Attestazione che specifica se gli attributi sono stati raccolti dall'utente |
| *executed-PhoneFactor-Input* | Attestazione che specifica se un nuovo numero di telefono è stato raccolto dall'utente |
| *authenticationSource* | Specifica se l'utente è stato autenticato con il provider di identità basato su social network, login.microsoftonline.com o l'account locale |

### <a name="claims-required-for-query-string-parameters-and-other-special-parameters"></a>Attestazioni necessarie per i parametri della stringa di query e altri parametri speciali

Le attestazioni seguenti sono necessarie per passare particolari parametri (inclusi alcuni parametri della stringa di query) ad altri provider di attestazioni:

| Tipo di attestazione | DESCRIZIONE |
|-------------|-------------|
| *nux* | Parametro speciale passato per autenticazione dell'account locale in login.microsoftonline.com |
| *nca* | Parametro speciale passato per autenticazione dell'account locale in login.microsoftonline.com |
| *prompt* | Parametro speciale passato per autenticazione dell'account locale in login.microsoftonline.com |
| *mkt* | Parametro speciale passato per autenticazione dell'account locale in login.microsoftonline.com |
| *lc* | Parametro speciale passato per autenticazione dell'account locale in login.microsoftonline.com |
| *grant_type* | Parametro speciale passato per autenticazione dell'account locale in login.microsoftonline.com |
| *scope* | Parametro speciale passato per autenticazione dell'account locale in login.microsoftonline.com |
| *client_id* | Parametro speciale passato per autenticazione dell'account locale in login.microsoftonline.com |
| *objectIdFromSession* | Parametro fornito dal provider di gestione delle sessioni predefinite per indicare che l'oggetto è stato recuperato da una sessione SSO |
| *isActiveMFASession* | Parametro fornito dal provider di gestione delle sessioni MFA per indicare che l'utente ha una sessione MFA attiva |

### <a name="additional-optional-claims-that-can-be-collected"></a>Attestazioni aggiuntive (facoltative) che possono essere raccolte

Le seguenti sono attestazioni aggiuntive che possono essere raccolte dagli utenti, archiviate nella directory e inviate nel token. Come indicato prima, si possono aggiungere altre attestazioni a questo elenco.

| Tipo di attestazione | DESCRIZIONE |
|-------------|-------------|
| *givenName* | Nome di battesimo dell'utente (noto anche come nome) |
| *surname* | Cognome dell'utente |
| *Extension_picture* | Immagine dell'utente da social network |

## <a name="claim-transformations"></a>Trasformazioni delle attestazioni

Di seguito sono elencate le trasformazioni di attestazioni disponibili.

| Trasformazione attestazione | DESCRIZIONE |
|----------------------|-------------|
| *CreateOtherMailsFromEmail* | |
| *CreateRandomUPNUserName* | |
| *CreateUserPrincipalName* | |
| *CreateSubjectClaimFromObjectID* | |
| *CreateSubjectClaimFromAlternativeSecurityId* | |
| *CreateAlternativeSecurityId* | |

## <a name="content-definitions"></a>Definizioni del contenuto

Questa sezione descrive le definizioni del contenuto già dichiarate nel criterio *B2C_1A_base*. È possibile fare riferimento a queste definizioni del contenuto, eseguirne l'override e/o estenderle, se necessario, nei propri criteri oltre che nel criterio *B2C_1A_base_extensions*.

| Provider di attestazioni | DESCRIZIONE |
|-----------------|-------------|
| *Facebook* | |
| *Accesso all'account locale* | |
| *PhoneFactor* | |
| *Azure Active Directory* | |
| *Autocertificazione* | |
| *Account locale* | |
| *Gestione delle sessioni* | |
| *Motore di criteri Trustframework* | |
| *TechnicalProfiles* | |
| *Autorità emittente di token* | |

## <a name="technical-profiles"></a>Profili tecnici

Questa sezione illustra i profili tecnici già dichiarati per ogni provider di attestazioni nel criterio *B2C_1A_base*. È possibile fare ulteriore riferimento a questi profili tecnici, eseguirne l'override e/o estenderli, se necessario, nei propri criteri oltre che nel criterio *B2C_1A_base_extensions*.

### <a name="technical-profiles-for-facebook"></a>Profili tecnici per Facebook

| Profilo tecnico | DESCRIZIONE |
|-------------------|-------------|
| *Facebook-OAUTH* | |

### <a name="technical-profiles-for-local-account-signin"></a>Profili tecnici per l'accesso all'account locale

| Profilo tecnico | DESCRIZIONE |
|-------------------|-------------|
| *Login-NonInteractive* | |

### <a name="technical-profiles-for-phone-factor"></a>Profili tecnici per PhoneFactor

| Profilo tecnico | DESCRIZIONE |
|-------------------|-------------|
| *PhoneFactor-Input* | |
| *PhoneFactor-InputOrVerify* | |
| *PhoneFactor-Verify* | |

### <a name="technical-profiles-for-azure-active-directory"></a>Profili tecnici per Azure Active Directory

| Profilo tecnico | DESCRIZIONE |
|-------------------|-------------|
| *AAD-Common* | Profilo tecnico incluso dagli altri profili tecnici AAD-xxx |
| *AAD-UserWriteUsingAlternativeSecurityId* | Profilo tecnico per gli accessi basati su social network |
| *AAD-UserReadUsingAlternativeSecurityId* | Profilo tecnico per gli accessi basati su social network |
| *AAD-UserReadUsingAlternativeSecurityId-NoError* | Profilo tecnico per gli accessi basati su social network |
| *AAD-UserWritePasswordUsingLogonEmail* | Profili tecnici per gli account locali |
| *AAD-UserReadUsingEmailAddress* | Profili tecnici per gli account locali |
| *AAD-UserWriteProfileUsingObjectId* | Profilo tecnico per aggiornare il record utente usando objectId |
| *AAD-UserWritePhoneNumberUsingObjectId* | Profilo tecnico per aggiornare il record utente usando objectId |
| *AAD-UserWritePasswordUsingObjectId* | Profilo tecnico per aggiornare il record utente usando objectId |
| *AAD-UserReadUsingObjectId* | Il profilo tecnico viene usato per leggere i dati dopo l'autenticazione dell'utente |

### <a name="technical-profiles-for-self-asserted"></a>Profili tecnici per l'autocertificazione

| Profilo tecnico | DESCRIZIONE |
|-------------------|-------------|
| *SelfAsserted-Social* | |
| *SelfAsserted-ProfileUpdate* | |

### <a name="technical-profiles-for-local-account"></a>Profili tecnici per l'account locale

| Profilo tecnico | DESCRIZIONE |
|-------------------|-------------|
| *LocalAccountSignUpWithLogonEmail* | |

### <a name="technical-profiles-for-session-management"></a>Profili tecnici per la gestione delle sessioni

| Profilo tecnico | DESCRIZIONE |
|-------------------|-------------|
| *SM-Noop* | |
| *SM-AAD* | |
| *SM-SocialSignup* | Il nome del profilo viene usato per evitare ambiguità nella sessione AAD tra iscrizione e accesso |
| *SM-SocialLogin* | |
| *SM-MFA* | |

### <a name="technical-profiles-for-trustframework-policy-engine-technicalprofiles"></a>Profili tecnici per TechnicalProfiles del motore di criteri Trustframework

Non sono attualmente definiti profili tecnici per il provider di attestazioni **TechnicalProfiles del motore di criteri Trustframework**.

### <a name="technical-profiles-for-token-issuer"></a>Profili tecnici per l'autorità emittente di token

| Profilo tecnico | DESCRIZIONE |
|-------------------|-------------|
| *JwtIssuer* | |

## <a name="user-journeys"></a>Percorsi utente

Questa sezione illustra i percorsi utente già dichiarati nel criterio *B2C_1A_base*. È possibile fare ulteriore riferimento a questi percorsi utente, eseguirne l'override e/o estenderli, se necessario, nei propri criteri oltre che nel criterio *B2C_1A_base_extensions*.

| Percorso utente | DESCRIZIONE |
|--------------|-------------|
| *SignUp* | |
| *SignIn* | |
| *SignUpOrSignIn* | |
| *EditProfile* | |
| *PasswordReset* | |
