---
title: Note per gli sviluppatori per i criteri personalizzati
titleSuffix: Azure AD B2C
description: Note per gli sviluppatori sulla configurazione e la gestione di Azure AD B2C con criteri personalizzati.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 403ca480bcf0743d81e375c122c888db96bbf543
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80408707"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Note per gli sviluppatori per i criteri personalizzati in Azure Active Directory B2C

La configurazione dei criteri personalizzati in Azure Active Directory B2C è ora disponibile a livello generale. Questo metodo di configurazione è destinato agli sviluppatori di identità avanzati che compilano soluzioni di identità complesse. I criteri personalizzati rendono disponibile la potenza del Framework dell'esperienza di identità in Azure AD B2C tenant.
Gli sviluppatori di identità avanzati che usano criteri personalizzati devono pianificare un certo tempo per completare la procedura dettagliata e leggere i documenti di riferimento.

Anche se la maggior parte delle opzioni dei criteri personalizzati disponibili è ora disponibile a livello generale, esistono funzionalità sottostanti, ad esempio tipi di profilo tecnico e API per la definizione di contenuto che si trovano in fasi diverse del ciclo di vita del software. Molti altri sono in arrivo. La tabella seguente specifica il livello di disponibilità a un livello più granulare.

## <a name="features-that-are-generally-available"></a>Funzionalità disponibili a livello generale

- Creare e caricare percorsi utente di autenticazione personalizzati usando criteri personalizzati.
    - Descrivere in modo dettagliato i percorsi utente come scambi tra provider di attestazioni.
    - Definire la diramazione condizionale nei percorsi utente.
- Interoperare con i servizi abilitati per l'API REST nei percorsi utente di autenticazione personalizzati.
- Federazione con provider di identità conformi al protocollo OpenIDConnect.
- Federazione con provider di identità che rispettano il protocollo SAML 2,0.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilità degli sviluppatori che usano il set di funzionalità dei criteri personalizzati

La configurazione manuale dei criteri garantisce un accesso di livello inferiore alla piattaforma sottostante di Azure AD B2C e comporta la creazione di un Framework di attendibilità univoco. Le numerose permutazioni possibili di provider di identità personalizzati, relazioni di trust, integrazioni con servizi esterni e flussi di lavoro Step-by-Step richiedono un approccio metodico per la progettazione e la configurazione.

Gli sviluppatori che utilizzano il set di funzionalità dei criteri personalizzati devono rispettare le linee guida seguenti:

- Acquisire familiarità con il linguaggio di configurazione dei criteri personalizzati e la gestione delle chiavi e dei segreti. Per ulteriori informazioni, vedere [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Assumere la proprietà degli scenari e delle integrazioni personalizzate. Documentare il lavoro e informare l'organizzazione del sito Live.
- Eseguire test metodici degli scenari.
- Seguire le procedure consigliate di staging e sviluppo software con almeno un ambiente di sviluppo e testing e un ambiente di produzione.
- Mantenersi aggiornati sui nuovi sviluppi dei servizi e dei provider di identità con cui viene eseguita l'integrazione. Ad esempio, tenere traccia delle modifiche dei segreti e delle modifiche pianificate e non pianificate del servizio.
- Configurare il monitoraggio attivo e monitorare il tempo di risposta degli ambienti di produzione. Per ulteriori informazioni sull'integrazione con Application Insights, vedere [Azure Active Directory B2C: raccolta di log](analytics-with-application-insights.md).
- Mantenere aggiornati gli indirizzi di posta elettronica di contatto nella sottoscrizione di Azure e prestare attenzione ai messaggi di posta elettronica del team del sito live Microsoft.
- Intervenire tempestivamente quando consigliato del team del sito live Microsoft.

## <a name="terms-for-features-in-public-preview"></a>Termini per le funzionalità disponibili in anteprima pubblica

- Si consiglia di usare le funzionalità di anteprima pubblica solo a scopo di valutazione.
- I contratti di servizio (SLA) non si applicano alle funzionalità di anteprima pubblica.
- Le richieste di supporto per le funzionalità di anteprima pubblica possono essere archiviate tramite i normali canali di supporto.

## <a name="features-by-stage-and-known-issues"></a>Funzionalità per fase e problemi noti

Le funzionalità del Framework personalizzato di criteri/identità sono soggette a uno sviluppo costante e rapido. La tabella seguente è un indice delle funzionalità e della disponibilità dei componenti.


### <a name="protocols-and-authorization-flows"></a>Protocolli e flussi di autorizzazione

| Funzionalità | Sviluppo | Anteprima | GA | Note |
|-------- | :-----------: | :-------: | :--: | ----- |
| [Codice di autorizzazione OAuth2](authorization-code-flow.md) |  |  | X |  |
| Codice di autorizzazione OAuth2 con PKCE |  |  | X | Solo applicazioni per dispositivi mobili  |
| [Flusso implicito OAuth2](implicit-flow-single-page-application.md) |  |  | X |  |
| [Credenziali password del proprietario della risorsa OAuth2](ropc-custom.md) |  | X |  |  |
| [Connessione OIDC](openid-connect.md) |  |  | X |  |
| [SAML2](connect-with-saml-service-providers.md)  |  |X  |  | Binding POST e reindirizzamento. |
| OAuth1 |  |  |  | Non supportato. |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>Identificazione Federazione provider 

| Funzionalità | Sviluppo | Anteprima | GA | Note |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | Ad esempio, Google +.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | Ad esempio, Facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | Ad esempio Twitter. |
| [SAML2](saml-technical-profile.md) |  |   | X | Ad esempio, Salesforce, ADFS. |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>Integrazione dell'API REST

| Funzionalità | Sviluppo | Anteprima | GA | Note |
|-------- | :-----------: | :-------: | :--: | ----- |
| [API REST con autenticazione di base](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [API REST con autenticazione del certificato client](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [API REST con OAuth2 Bearer auth](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>Supporto componenti

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Autenticazione del fattore telefono](phone-factor-technical-profile.md) |  |  | X |  |
| [Autenticazione a più fattori di Azure](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [Password monouso](one-time-password-technical-profile.md) |  | X |  |  |
| [Azure Active Directory](active-directory-technical-profile.md) come directory locale |  |  | X |  |
| Sottosistema posta elettronica di Azure per la verifica della posta elettronica |  |  | X |  |
| [Provider di servizi di posta elettronica di terze parti](custom-email.md) |  |X  |  |  |
| [Supporto per più linguaggi](localization.md)|  |  | X |  |
| [Convalide di predicato](predicates.md) |  |  | X | Ad esempio, la complessità della password. |
| [Controlli di visualizzazione](display-controls.md) |  |X  |  |  |


### <a name="page-layout-versions"></a>Versioni del layout di pagina

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| [2.0.0](page-layout.md#200) |  | X |  |  |
| [1.2.0](page-layout.md#120) |  | X |  |  |
| [1.1.0](page-layout.md#110) |  |  | X |  |
| [1.0.0](page-layout.md#100) |  |  | X |  |
| [Supporto per JavaScript](javascript-samples.md) |  | X |  |  |

### <a name="app-ief-integration"></a>Integrazione del framework dell'esperienza di gestione delle identità dell'app

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parametro della stringa di query`domain_hint` |  |  | X | Disponibile come attestazione, può essere passato a IDP. |
| Parametro della stringa di query`login_hint` |  |  | X | Disponibile come attestazione, può essere passato a IDP. |
| Inserisci JSON nel percorso utente tramite`client_assertion` | X |  |  | Verrà deprecato. |
| Inserisci JSON nel percorso utente come`id_token_hint` |  | X |  | Approccio avanzato per passare il codice JSON. |
| [Passare il token del provider di identità all'applicazione](idp-pass-through-custom.md) |  | X |  | Ad esempio, da Facebook a app. |

### <a name="session-management"></a>Gestione delle sessioni

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Provider sessione SSO predefinito](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [Provider sessione di accesso esterno](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [Provider sessione SSO SAML](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |


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
| [Log del percorso utente Application Insights](troubleshoot-with-application-insights.md) |  | X |  | Usato per la risoluzione dei problemi durante lo sviluppo.  |
| [Application Insights registri eventi](application-insights-technical-profile.md) |  | X |  | Utilizzato per monitorare i flussi degli utenti nell'ambiente di produzione. |


## <a name="next-steps"></a>Passaggi successivi

- Controllare le [operazioni di Microsoft Graph disponibili per Azure ad B2C](microsoft-graph-operations.md)
- Altre informazioni sui [criteri personalizzati e le differenze con i flussi utente](custom-policy-overview.md).
