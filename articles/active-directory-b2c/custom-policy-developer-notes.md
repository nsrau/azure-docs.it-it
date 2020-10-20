---
title: Note per gli sviluppatori relative ai criteri personalizzati
titleSuffix: Azure AD B2C
description: Note per gli sviluppatori sulla configurazione e la gestione di Azure AD B2C con criteri personalizzati.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7788d45a9cc30cb688d33a629fbb6e53f5ca16cb
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92214907"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Note per gli sviluppatori relative ai criteri personalizzati in Azure Active Directory B2C

La configurazione dei criteri personalizzati in Azure Active Directory B2C è ora disponibile a livello generale. Questo metodo di configurazione è destinato agli sviluppatori esperti di soluzioni di gestione delle identità che creano le soluzioni di gestione delle identità complesse. I criteri personalizzati rendono la potenza di Identity Experience Framework disponibile nei tenant di Azure AD B2C.
Gli sviluppatori esperti di soluzioni di gestione delle identità che usano i criteri personalizzati dovranno pianificare di usare il tempo necessario per completare le procedure dettagliate e leggere i documenti di riferimento.

Anche se la maggior parte delle opzioni dei criteri personalizzati disponibili è ora disponibile a livello generale, esistono funzionalità sottostanti, ad esempio tipi di profilo tecnico e API per la definizione di contenuto, che si trovano in fasi diverse del ciclo di vita del software. Molte altre sono in arrivo. La tabella seguente specifica il livello di disponibilità a un livello più granulare.

## <a name="features-that-are-generally-available"></a>Funzionalità generalmente disponibili

- Creare e caricare percorsi utente di autenticazione personalizzati usando criteri personalizzati.
    - Descrivere in modo dettagliato i percorsi utente come scambi tra provider di attestazioni.
    - Definire la diramazione condizionale nei percorsi utente.
- Interagire con i servizi abilitati per API REST nei percorsi utente di autenticazione personalizzati.
- Eseguire la federazione con i provider di identità conformi al protocollo OpenIDConnect.
- Eseguire la federazione con i provider di identità che rispettano il protocollo SAML 2.0.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilità degli sviluppatori che usano il set di funzionalità dei criteri personalizzati

La configurazione manuale dei criteri garantisce un accesso di livello inferiore alla piattaforma sottostante di Azure AD B2C e determina la creazione di un framework attendibilità univoco. Le numerose possibili permutazioni di provider di identità personalizzati, relazioni di trust, integrazioni con servizi esterni e flussi di lavoro dettagliati richiedono un approccio metodico alla progettazione e alla configurazione.

Gli sviluppatori che usano il set di funzionalità dei criteri personalizzati devono rispettare le linee guida seguenti:

- Acquisire familiarità con il linguaggio di configurazione dei criteri personalizzati e con la con la gestione di chiavi e segreti. Per altre informazioni, vedere [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Assumere la proprietà degli scenari e delle integrazioni personalizzate. Documentare il lavoro e informare l'organizzazione del sito live.
- Eseguire test metodici degli scenari.
- Seguire le procedure consigliate di staging e sviluppo software con almeno un ambiente di sviluppo e testing e un ambiente di produzione.
- Mantenersi aggiornati sui nuovi sviluppi dei servizi e dei provider di identità con cui viene eseguita l'integrazione. Ad esempio, tenere traccia delle modifiche dei segreti e delle modifiche pianificate e non pianificate del servizio.
- Configurare il monitoraggio attivo e monitorare il tempo di risposta degli ambienti di produzione. Per altre informazioni sull'integrazione con Application Insights, vedere [Azure Active Directory B2C: Raccolta di log](analytics-with-application-insights.md).
- Mantenere aggiornati gli indirizzi di posta elettronica di contatto nella sottoscrizione di Azure e prestare attenzione ai messaggi di posta elettronica del team del sito live Microsoft.
- Intervenire tempestivamente quando consigliato del team del sito live Microsoft.

## <a name="terms-for-features-in-public-preview"></a>Condizioni per le funzionalità disponibili in anteprima pubblica

- È consigliabile usare le funzionalità disponibili in anteprima pubblica solo a scopo di valutazione.
- I contratti di servizio non si applicano alle funzionalità disponibili in anteprima pubblica.
- È possibile inviare richieste per le funzionalità disponibili in anteprima pubblica tramite i normali canali del supporto.

## <a name="features-by-stage-and-known-issues"></a>Funzionalità per fase e problemi noti

Le funzionalità dei criteri personalizzati o di Identity Experience Framework sono in costante e rapida evoluzione. Questa tabella è un indice delle funzionalità e della disponibilità dei componenti.


### <a name="protocols-and-authorization-flows"></a>Protocolli e flussi di autorizzazione

| Funzionalità | Sviluppo | Anteprima | GA | Note |
|-------- | :-----------: | :-------: | :--: | ----- |
| [Codice di autorizzazione OAuth2](authorization-code-flow.md) |  |  | X |  |
| Codice di autorizzazione OAuth2 con PKCE |  |  | X | [Client pubblici e applicazioni a pagina singola](authorization-code-flow.md)  |
| [Flusso implicito OAuth2](implicit-flow-single-page-application.md) |  |  | X |  |
| [Credenziali password del proprietario della risorsa OAuth2](ropc-custom.md) |  | X |  |  |
| [Connessione OIDC](openid-connect.md) |  |  | X |  |
| [SAML2](connect-with-saml-service-providers.md)  |  |  |X  | Binding POST e reindirizzamento. |
| OAuth1 |  |  |  | Non supportato. |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>Identificazione federazione provider 

| Funzionalità | Sviluppo | Anteprima | GA | Note |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | Ad esempio, Google+.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | Ad esempio, Facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | Ad esempio, Twitter. |
| [SAML2](saml-identity-provider-technical-profile.md) |  |   | X | Ad esempio, Salesforce, AD FS. |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>Integrazione dell'API REST

| Funzionalità | Sviluppo | Anteprima | GA | Note |
|-------- | :-----------: | :-------: | :--: | ----- |
| [API REST con autenticazione di base](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [API REST con autenticazione del certificato client](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [API REST con autenticazione con token di connessione OAuth2](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>Supporto dei componenti

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Autenticazione fattore telefono](phone-factor-technical-profile.md) |  |  | X |  |
| [Autenticazione Azure MFA](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [Password monouso](one-time-password-technical-profile.md) |  | X |  |  |
| [Azure Active Directory](active-directory-technical-profile.md) come directory locale |  |  | X |  |
| Sottosistema posta elettronica di Azure per la verifica della posta elettronica |  |  | X |  |
| [Provider di servizi di posta elettronica di terze parti](custom-email-mailjet.md) |  |X  |  |  |
| [Supporto per più linguaggi](localization.md)|  |  | X |  |
| [Convalide di predicato](predicates.md) |  |  | X | Ad esempio, complessità delle password. |
| [Controlli per la visualizzazione](display-controls.md) |  |X  |  |  |


### <a name="page-layout-versions"></a>Versioni del layout di pagina

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| [2.0.0](page-layout.md) |  | X |  |  |
| [1.2.0](page-layout.md) |  | X |  |  |
| [1.1.0](page-layout.md) |  |  | X |  |
| [1.0.0](page-layout.md) |  |  | X |  |
| [Supporto per JavaScript](javascript-samples.md) |  | X |  |  |

### <a name="app-ief-integration"></a>Integrazione del framework dell'esperienza di gestione delle identità dell'app

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parametro della stringa di query `domain_hint` |  |  | X | Disponibile come attestazione, può essere passato all'IDP. |
| Parametro della stringa di query `login_hint` |  |  | X | Disponibile come attestazione, può essere passato all'IDP. |
| Inserisci JSON nel percorso utente tramite `client_assertion` | X |  |  | Verrà deprecato. |
| Inserisci JSON nel percorso utente come `id_token_hint` |  | X |  | Approccio di avanzamento per passare JSON. |
| [Passare il token del provider di identità all'applicazione](idp-pass-through-custom.md) |  | X |  | Ad esempio, da Facebook all'app. |

### <a name="session-management"></a>Gestione delle sessioni

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Provider di sessioni SSO predefinite](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [Provider di sessioni di accesso esterno](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [Provider di sessioni SSO SAML](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |
| [OAuthSSOSessionProvider](custom-policy-reference-sso.md#oauthssosessionprovider)  |  | X |  |  |
| [Single Sign-Out](session-overview.md#sign-out)  |  | X |  |  |

### <a name="security"></a>Sicurezza

| Funzionalità | Sviluppo | Anteprima | GA | Note |
|-------- | :-----------: | :-------: | :--: | ----- |
| Chiavi dei criteri: generazione, manuale, caricamento |  |  | X |  |
| Chiavi dei criteri: RSA/certificato, segreti |  |  | X |  |


### <a name="developer-interface"></a>Interfaccia di sviluppo

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| Portale di Azure: esperienza utente del framework dell'esperienza di gestione delle identità |  |  | X |  |
| Caricamento dei criteri |  |  | X |  |
| [Log del percorso utente di Application Insights](troubleshoot-with-application-insights.md) |  | X |  | Usato per la risoluzione dei problemi durante lo sviluppo.  |
| [Log eventi di Application Insights](application-insights-technical-profile.md) |  | X |  | Usato per monitorare i flussi degli utenti nell'ambiente di produzione. |


## <a name="next-steps"></a>Passaggi successivi

- Controllare le [operazioni di Microsoft Graph disponibili per Azure AD B2C](microsoft-graph-operations.md)
- Altre informazioni su [criteri personalizzati e differenze con i flussi utente](custom-policy-overview.md).
