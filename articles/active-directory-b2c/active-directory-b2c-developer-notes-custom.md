---
title: Note per gli sviluppatori per i criteri personalizzati
titleSuffix: Azure AD B2C
description: Note per gli sviluppatori sulla configurazione e la gestione di Azure AD B2C con criteri personalizzati.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: edbd31434715c380badf15118b0779885aed700f
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/09/2019
ms.locfileid: "74949756"
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
- Configurare il monitoraggio attivo e monitorare il tempo di risposta degli ambienti di produzione. Per ulteriori informazioni sull'integrazione con Application Insights, vedere [Azure Active Directory B2C: raccolta di log](active-directory-b2c-custom-guide-eventlogger-appins.md).
- Mantenere aggiornati gli indirizzi di posta elettronica di contatto nella sottoscrizione di Azure e prestare attenzione ai messaggi di posta elettronica del team del sito live Microsoft.
- Intervenire tempestivamente quando consigliato del team del sito live Microsoft.

## <a name="terms-for-features-in-public-preview"></a>Termini per le funzionalità disponibili in anteprima pubblica

- Si consiglia di usare le funzionalità di anteprima pubblica solo a scopo di valutazione.
- I contratti di servizio (SLA) non si applicano alle funzionalità di anteprima pubblica.
- Le richieste di supporto per le funzionalità di anteprima pubblica possono essere archiviate tramite i normali canali di supporto.

## <a name="features-by-stage-and-known-issues"></a>Funzionalità per fase e problemi noti

Le funzionalità del Framework personalizzato di criteri/identità sono soggette a uno sviluppo costante e rapido. La tabella seguente è un indice delle funzionalità e della disponibilità dei componenti.

### <a name="identity-providers-tokens-protocols"></a>Provider di identità, token, protocolli

| Funzionalità | Sviluppo. | Preview | Disponibilità a livello generale | Note |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | X | Ad esempio, Google +.  |
| IDP-OAUTH2 |  |  | X | Ad esempio, Facebook.  |
| IDP-OAUTH1 (Twitter) |  | X |  | Ad esempio Twitter. |
| IDP-OAUTH1 (es-Twitter) |  |  |  | Supporto non disponibile |
| IDP-SAML |  |   | X | Ad esempio, Salesforce, ADFS. |
| IDP-WSFED | X |  |  |  |
| Relying party OAUTH1 |  |  |  | Non supportati. |
| Relying party OAUTH2 |  |  | X |  |
| Relying Party OIDC |  |  | X |  |
| Relying Party SAML | X |  |  |  |
| Relying Party WSFED | X |  |  |  |
| API REST con autenticazione di base e del certificato |  |  | X | Ad esempio, app per la logica di Azure. |

### <a name="component-support"></a>Supporto dei componenti

| Funzionalità | Sviluppo. | Preview | Disponibilità a livello generale | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Multi-Factor Authentication |  |  | X |  |
| Azure Active Directory come directory locale |  |  | X |  |
| Sottosistema posta elettronica di Azure per la verifica della posta elettronica |  |  | X |  |
| Supporto per più linguaggi|  |  | X |  |
| Convalide di predicato |  |  | X | Ad esempio, la complessità della password. |
| Uso di provider di servizi di posta elettronica di terze parti | X |  |  |  |

### <a name="content-definition"></a>Definizione del contenuto

| Funzionalità | Sviluppo. | Preview | Disponibilità a livello generale | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| Pagina di errore, api.error |  |  | X |  |
| Pagina di selezione IDP, api.idpselections |  |  | X |  |
| Selezione IDP per l'iscrizione, api.idpselections.signup |  |  | X |  |
| Password dimenticata, api.localaccountpasswordreset |  |  | X |  |
| Accesso all'account locale, api.localaccountsignin |  |  | X |  |
| Iscrizione dell'account locale, api.localaccountsignin |  |  | X |  |
| Pagina MFA, api.phonefactor |  |  | X |  |
| Iscrizione all'account di social networking autocertificata, API. selfasserted |  |  | X |  |
| Aggiornamento del profilo autocertificato, api.selfasserted.profileupdate |  |  | X |  |
| Pagina di iscrizione o accesso unificata, API. signuporsignin, con il parametro "disableSignup" |  |  | X |  |
| JavaScript/layout di pagina |  | X |  |  |

### <a name="app-ief-integration"></a>Integrazione del framework dell'esperienza di gestione delle identità dell'app

| Funzionalità | Sviluppo. | Preview | Disponibilità a livello generale | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parametro di stringa della query domain_hint |  |  | X | Disponibile come attestazione, può essere passato a IDP. |
| Parametro di stringa della query login_hint |  |  | X | Disponibile come attestazione, può essere passato a IDP. |
| Inserimento di JSON in UserJourney tramite client_assertion | X |  |  | Verrà deprecato. |
| Inserimento di JSON in UserJourney come id_token_hint |  | X |  | Approccio avanzato per passare il codice JSON. |
| Passare il TOKEN IDP all'applicazione |  | X |  | Ad esempio, da Facebook a app. |

### <a name="session-management"></a>Gestione delle sessioni

| Funzionalità | Sviluppo. | Preview | Disponibilità a livello generale | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| Provider di sessioni SSO |  |  | X |  |
| Provider di sessioni di accesso esterno |  |  | X |  |
| Provider di sessioni SSO SAML |  |  | X |  |
| Provider sessione SSO predefinito |  |  | X |  |

### <a name="security"></a>Sicurezza

| Funzionalità | Sviluppo. | Preview | Disponibilità a livello generale | Note |
|-------- | :-----------: | :-------: | :--: | ----- |
| Chiavi dei criteri: generazione, manuale, caricamento |  |  | X |  |
| Chiavi dei criteri: RSA/certificato, segreti |  |  | X |  |
| Caricamento dei criteri |  |  | X |  |

### <a name="developer-interface"></a>Interfaccia di sviluppo

| Funzionalità | Sviluppo. | Preview | Disponibilità a livello generale | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| Portale di Azure: esperienza utente del framework dell'esperienza di gestione delle identità |  |  | X |  |
| Log UserJourney di Application Insights |  | X |  | Usato per la risoluzione dei problemi durante lo sviluppo.  |
| Application Insights registri eventi (tramite i passaggi dell'orchestrazione) |  | X |  | Utilizzato per monitorare i flussi degli utenti nell'ambiente di produzione. |

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sui [criteri personalizzati e le differenze con i flussi utente](active-directory-b2c-overview-custom.md).
