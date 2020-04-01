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
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80408707"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Note per gli sviluppatori per i criteri personalizzati in Azure Active Directory B2CDeveloper notes for custom policies in Azure Active Directory B2C

La configurazione dei criteri personalizzati in Azure Active Directory B2C è ora disponibile in genere. Questo metodo di configurazione è destinato agli sviluppatori di identità avanzati che creano soluzioni di identità complesse. I criteri personalizzati rendono disponibile la potenza di Identity Experience Framework nei tenant B2C di Azure AD.
Gli sviluppatori di identità avanzati che utilizzano criteri personalizzati devono pianificare di investire tempo per completare le guide e leggere i documenti di riferimento.

Sebbene la maggior parte delle opzioni dei criteri personalizzate disponibili siano ora disponibili in genere, esistono funzionalità sottostanti, ad esempio tipi di profilo tecnico e API di definizione del contenuto che si trovano in fasi diverse del ciclo di vita del software. Molti altri stanno arrivando. La tabella seguente specifica il livello di disponibilità a un livello più granulare.

## <a name="features-that-are-generally-available"></a>Funzionalità generalmente disponibili

- Creare e caricare percorsi utente di autenticazione personalizzati usando criteri personalizzati.
    - Descrivere in modo dettagliato i percorsi utente come scambi tra provider di attestazioni.
    - Definire la diramazione condizionale nei percorsi utente.
- Interagisci con i servizi abilitati per l'API REST nei percorsi utente di autenticazione personalizzati.
- Eseguire la federazione con provider di identità conformi al protocollo OpenIDConnect.
- Federare con provider di identità conformi al protocollo SAML 2.0.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilità degli sviluppatori che usano il set di funzionalità dei criteri personalizzati

La configurazione manuale dei criteri concede l'accesso di livello inferiore alla piattaforma sottostante di Azure AD B2C e comporta la creazione di un framework di attendibilità univoco. Le molte possibili permutazioni di provider di identità personalizzati, relazioni di trust, integrazioni con servizi esterni e flussi di lavoro dettagliati richiedono un approccio metodico alla progettazione e alla configurazione.

Gli sviluppatori che utilizzano il set di funzionalità dei criteri personalizzati devono rispettare le linee guida seguenti:Developers consuming the custom policy feature set should adhere to the following guidelines:

- Acquisire familiarità con la lingua di configurazione dei criteri personalizzati e la gestione di chiavi/segreti. Per ulteriori informazioni, vedere [TrustFrameworkPolicy](trustframeworkpolicy.md).
- Assumere la proprietà degli scenari e delle integrazioni personalizzate. Documenta il tuo lavoro e informa la tua organizzazione del sito dal vivo.
- Eseguire test metodici degli scenari.
- Seguire le procedure consigliate di staging e sviluppo software con almeno un ambiente di sviluppo e testing e un ambiente di produzione.
- Mantenersi aggiornati sui nuovi sviluppi dei servizi e dei provider di identità con cui viene eseguita l'integrazione. Ad esempio, tenere traccia delle modifiche dei segreti e delle modifiche pianificate e non pianificate del servizio.
- Configurare il monitoraggio attivo e monitorare il tempo di risposta degli ambienti di produzione. Per altre informazioni sull'integrazione con Application Insights, vedere [Azure Active Directory B2C: Raccolta di log](analytics-with-application-insights.md).
- Mantenere aggiornati gli indirizzi di posta elettronica di contatto nella sottoscrizione di Azure e prestare attenzione ai messaggi di posta elettronica del team del sito live Microsoft.
- Intervenire tempestivamente quando consigliato del team del sito live Microsoft.

## <a name="terms-for-features-in-public-preview"></a>Termini per le funzionalità nell'anteprima pubblica

- Ti invitiamo a utilizzare le funzionalità di anteprima pubblica solo a scopo di valutazione.
- I contratti di servizio non si applicano alle funzionalità di anteprima pubblica.
- Le richieste di supporto per le funzionalità di anteprima pubblica possono essere archiviate tramite canali di supporto regolari.

## <a name="features-by-stage-and-known-issues"></a>Funzionalità per fase e problemi noti

Le funzionalità di Policy/Identity Experience Framework personalizzate sono in fase di sviluppo costante e rapido. La tabella seguente è un indice delle funzionalità e della disponibilità dei componenti.


### <a name="protocols-and-authorization-flows"></a>Protocolli e flussi di autorizzazione

| Funzionalità | Sviluppo | Anteprima | GA | Note |
|-------- | :-----------: | :-------: | :--: | ----- |
| [Codice di autorizzazione OAuth2](authorization-code-flow.md) |  |  | X |  |
| Codice di autorizzazione OAuth2 con PKCE |  |  | X | Solo applicazioni mobili  |
| [Flusso implicito OAuth2](implicit-flow-single-page-application.md) |  |  | X |  |
| [Credenziali password del proprietario della risorsa OAuth2](ropc-custom.md) |  | X |  |  |
| [Connessione OIDC](openid-connect.md) |  |  | X |  |
| [SAML2 (in famiglia)](connect-with-saml-service-providers.md)  |  |X  |  | Binding POST e Redirect. |
| OAuth1 |  |  |  | Non supportato. |
| WSFED | X |  |  |  |

### <a name="identify-providers-federation"></a>Identificare la federazione dei provider 

| Funzionalità | Sviluppo | Anteprima | GA | Note |
|-------- | :-----------: | :-------: | :--: | ----- |
| [OpenID Connect](openid-connect-technical-profile.md) |  |  | X | Ad esempio, Google.  |
| [OAuth2](oauth2-technical-profile.md) |  |  | X | Ad esempio, Facebook.  |
| [OAuth1](oauth1-technical-profile.md) |  | X |  | Ad esempio, Twitter. |
| [SAML2 (in famiglia)](saml-technical-profile.md) |  |   | X | Ad esempio, Salesforce, ADFS. |
| WSFED| X |  |  |  |


### <a name="rest-api-integration"></a>Integrazione dell'API REST

| Funzionalità | Sviluppo | Anteprima | GA | Note |
|-------- | :-----------: | :-------: | :--: | ----- |
| [API REST con autenticazione di base](secure-rest-api.md#http-basic-authentication) |  |  | X |  |
| [API REST con autenticazione del certificato client](secure-rest-api.md#https-client-certificate-authentication) |  |  | X |  |
| [API REST con autenticazione di connessione OAuth2](secure-rest-api.md#oauth2-bearer-authentication) |  | X |  |  |

### <a name="component-support"></a>Supporto dei componenti

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Autenticazione del fattore di telefono](phone-factor-technical-profile.md) |  |  | X |  |
| [Autenticazione Azure MFAAzure MFA authentication](multi-factor-auth-technical-profile.md) |  | X |  |  |
| [Password monouso](one-time-password-technical-profile.md) |  | X |  |  |
| [Azure Active Directory](active-directory-technical-profile.md) come directory locale |  |  | X |  |
| Sottosistema di posta elettronica di Azure per la verifica della posta elettronicaAzure email subsystem for email verification |  |  | X |  |
| [Fornitori di servizi di posta elettronica di terze parti](custom-email.md) |  |X  |  |  |
| [Supporto per più linguaggi](localization.md)|  |  | X |  |
| [Convalide dei predicatiPredicate validations](predicates.md) |  |  | X | Ad esempio, complessità della password. |
| [Controlli di visualizzazione](display-controls.md) |  |X  |  |  |


### <a name="page-layout-versions"></a>Versioni del layout di pagina

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| [2.0.0](page-layout.md#200) |  | X |  |  |
| [1.2.0](page-layout.md#120) |  | X |  |  |
| [1.1.0](page-layout.md#110) |  |  | X |  |
| [1.0.0](page-layout.md#100) |  |  | X |  |
| [Supporto JavaScript](javascript-samples.md) |  | X |  |  |

### <a name="app-ief-integration"></a>Integrazione del framework dell'esperienza di gestione delle identità dell'app

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parametro della stringa di query`domain_hint` |  |  | X | Disponibile come attestazione, può essere passato a IDP. |
| Parametro della stringa di query`login_hint` |  |  | X | Disponibile come attestazione, può essere passato a IDP. |
| Inserire JSON nel percorso utente tramite`client_assertion` | X |  |  | Sarà deprecato. |
| Inserisci JSON nel percorso utente come`id_token_hint` |  | X |  | Approccio di forward per passare JSON. |
| [Passare il token del provider di identità all'applicazionePass identity provider token to the application](idp-pass-through-custom.md) |  | X |  | Ad esempio, da Facebook all'app. |

### <a name="session-management"></a>Gestione delle sessioni

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| [Provider di sessione SSO predefinito](custom-policy-reference-sso.md#defaultssosessionprovider) |  |  | X |  |
| [Provider di sessione di accesso esterno](custom-policy-reference-sso.md#externalloginssosessionprovider) |  |  | X |  |
| [Provider di sessione SAML SSO](custom-policy-reference-sso.md#samlssosessionprovider) |  |  | X |  |


### <a name="security"></a>Security

| Funzionalità | Sviluppo | Anteprima | GA | Note |
|-------- | :-----------: | :-------: | :--: | ----- |
| Chiavi dei criteri: generazione, manuale, caricamento |  |  | X |  |
| Chiavi dei criteri: RSA/certificato, segreti |  |  | X |  |


### <a name="developer-interface"></a>Interfaccia di sviluppo

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| Portale di Azure: esperienza utente del framework dell'esperienza di gestione delle identità |  |  | X |  |
| Caricamento dei criteri |  |  | X |  |
| [Log del percorso utente di Application Insights](troubleshoot-with-application-insights.md) |  | X |  | Utilizzato per la risoluzione dei problemi durante lo sviluppo.  |
| [Registri eventi di Application Insights](application-insights-technical-profile.md) |  | X |  | Utilizzato per monitorare i flussi utente nell'ambiente di produzione. |


## <a name="next-steps"></a>Passaggi successivi

- Controllare le operazioni di [Microsoft Graph disponibili per Azure AD B2CCheck the Microsoft Graph operations available for Azure AD B2C](microsoft-graph-operations.md)
- Ulteriori informazioni sui [criteri personalizzati e sulle differenze con i flussi utente](custom-policy-overview.md).
