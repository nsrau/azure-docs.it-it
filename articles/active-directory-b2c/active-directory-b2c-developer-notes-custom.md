---
title: Note degli sviluppatori per i criteri personalizzati - Azure Active Directory B2C | Microsoft Docs
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
ms.openlocfilehash: 1d0be4ec2ed8feb308839377e0494ef2f4b78368
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66510208"
---
# <a name="developer-notes-for-custom-policies-in-azure-active-directory-b2c"></a>Note degli sviluppatori per i criteri personalizzati in Azure Active Directory B2C

Configurazione dei criteri personalizzati in Azure Active Directory B2C è ora disponibile a livello generale. Questo metodo di configurazione è destinato agli sviluppatori esperti che la creazione di soluzioni di identità complessa. I criteri personalizzati di rendere disponibili nei tenant di Azure AD B2C la potenza del Framework di esperienza di identità. Advanced gli sviluppatori che usano i criteri personalizzati devono pianificare di investire tempo completare le procedure dettagliate e leggere i documenti di riferimento di identità.

Sebbene la maggior parte delle opzioni di criteri personalizzate disponibili sono ora disponibile a livello generale, esistono funzionalità sottostanti, ad esempio tipi di profilo tecnico e definizione del contenuto API che sono nelle diverse fasi del ciclo di vita del software. Molti altri saranno disponibili. Nella tabella seguente specifica il livello di disponibilità a un livello più granulare.  

## <a name="features-that-are-generally-available"></a>Funzionalità disponibili a livello generale

- Creare e caricare percorsi utente di autenticazione personalizzati usando criteri personalizzati.  
    - Descrivere in modo dettagliato i percorsi utente come scambi tra provider di attestazioni.  
    - Definire la diramazione condizionale nei percorsi utente.  
- Interagire con i servizi abilitati per API REST nei percorsi utente di autenticazione personalizzato.  
- Attuare la federazione con provider di identità che sono conformi con il protocollo openid Connect.  
- Attuare la federazione con provider di identità che rispettano il protocollo SAML 2.0.   

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilità degli sviluppatori che usano il set di funzionalità dei criteri personalizzati

Configurazione manuale dei criteri consente l'accesso di livello inferiore alla piattaforma sottostante di Azure AD B2C e comporta la creazione di un valore univoco, di framework attendibilità. Molte possibili permutazioni di provider di identità personalizzati, relazioni di trust, integrazioni con servizi esterni e flussi di lavoro dettagliati richiedono un approccio metodico per progettazione e alla configurazione. 

Gli sviluppatori utilizzano il set di funzionalità dei criteri personalizzati devono rispettare le linee guida seguenti:

- Acquisire familiarità con il linguaggio di configurazione della gestione criteri di chiavi e segreti personalizzato. Per altre informazioni, vedere [TrustFrameworkPolicy](trustframeworkpolicy.md). 
- Assumere la proprietà degli scenari e delle integrazioni personalizzate. Documentare il lavoro e informare l'organizzazione di siti live.  
- Eseguire test metodici degli scenari. 
- Seguire le procedure consigliate di staging e sviluppo software con almeno un ambiente di sviluppo e testing e un ambiente di produzione. 
- Mantenersi aggiornati sui nuovi sviluppi dei servizi e dei provider di identità con cui viene eseguita l'integrazione. Ad esempio, tenere traccia delle modifiche dei segreti e delle modifiche pianificate e non pianificate del servizio. 
- Configurare il monitoraggio attivo e monitorare il tempo di risposta degli ambienti di produzione. Per altre informazioni sull'integrazione con Application Insights, vedere [Azure Active Directory B2C: La raccolta dei log](active-directory-b2c-custom-guide-eventlogger-appins.md). 
- Mantenere aggiornati gli indirizzi di posta elettronica di contatto nella sottoscrizione di Azure e prestare attenzione ai messaggi di posta elettronica del team del sito live Microsoft. 
- Intervenire tempestivamente quando consigliato del team del sito live Microsoft.

## <a name="terms-for-features-in-public-preview"></a>Condizioni per le funzionalità in anteprima pubblica

- Si consiglia di usare le funzionalità di anteprima pubblica solo a fini di valutazione. 
- Contratti di servizio (SLA) non si applicano alle funzionalità di anteprima pubblica.
- È possibile inviare richieste di supporto per le funzionalità di anteprima pubblica tramite i normali canali del supporto. 

## <a name="features-by-stage-and-known-issues"></a>Funzionalità per fase e problemi noti

Le funzionalità di Framework dell'esperienza di identità/criteri personalizzati sono in costante e rapida evoluzione. Nella tabella seguente è un indice di funzionalità e disponibilità dei componenti.

### <a name="identity-providers-tokens-protocols"></a>Provider di identità, token, protocolli

| Funzionalità | Sviluppo. | Anteprima | GA | Note |
|-------- | ----------- | ------- | -- | ----- |
| IDP-OpenIDConnect |  |  | X | Ad esempio, Google +.  |
| IDP-OAUTH2 |  |  | X | Ad esempio Facebook.  |
| IDP-OAUTH1 (twitter) |  | X |  | Ad esempio Twitter. |
| IDP-OAUTH1 (ex-twitter) |  |  |  | Non supportate |
| IDP-SAML |  |   | X | Ad esempio, Salesforce, ad FS. |
| IDP-WSFED | X |  |  |  |
| Relying Party OAUTH1 |  |  |  | Non supportati. |
| Relying Party OAUTH2 |  |  | X |  |
| Relying Party OIDC |  |  | X |  |
| Relying Party SAML | X |  |  |  |
| Relying Party WSFED | X |  |  |  |
| API REST con basic e autenticazione del certificato |  |  | X | Ad esempio, App per la logica. |

### <a name="component-support"></a>Supporto dei componenti

| Funzionalità | Sviluppo. | Anteprima | GA | Note |
| ------- | ----------- | ------- | -- | ----- |
| Azure Multi-Factor Authentication |  |  | X |  |
| Azure Active Directory come directory locale |  |  | X |  |
| Azure sottosistema posta elettronica per la verifica tramite posta elettronica |  |  | X |  |
| Supporto per più linguaggi|  |  | X |  |
| Convalide del predicato |  |  | X | Ad esempio, la complessità delle password. |
| Uso di provider di servizi di posta elettronica di terze parti | X |  |  |  |

### <a name="content-definition"></a>Definizione del contenuto

| Funzionalità | Sviluppo. | Anteprima | GA | Note |
| ------- | ----------- | ------- | -- | ----- |
| Pagina di errore, api.error |  |  | X |  |
| Pagina di selezione IDP, api.idpselections |  |  | X |  |
| Selezione IDP per l'iscrizione, api.idpselections.signup |  |  | X |  |
| Password dimenticata, api.localaccountpasswordreset |  |  | X |  |
| Accesso all'account locale, api.localaccountsignin |  |  | X |  |
| Iscrizione dell'account locale, api.localaccountsignin |  |  | X |  |
| Pagina MFA, api.phonefactor |  |  | X |  |
| Account di social networking autocertificato api.selfasserted effettua l'iscrizione, |  |  | X |  |
| Aggiornamento del profilo autocertificato, api.selfasserted.profileupdate |  |  | X |  |
| Nella pagina di accesso, API. signuporsignin, con il parametro "disableSignup" o iscrizione unificata |  |  | X |  |
| JavaScript / pagina del contratto |  | X |  |  |

### <a name="app-ief-integration"></a>Integrazione del framework dell'esperienza di gestione delle identità dell'app

| Funzionalità | Sviluppo. | Anteprima | GA | Note |
| ------- | ----------- | ------- | -- | ----- |
| Parametro di stringa della query domain_hint |  |  | X | Disponibile come attestazione, può essere passato al provider di identità. |
| Parametro di stringa della query login_hint |  |  | X | Disponibile come attestazione, può essere passato al provider di identità. |
| Inserimento di JSON in UserJourney tramite client_assertion | X |  |  | Verrà deprecato. |
| Inserimento di JSON in UserJourney come id_token_hint |  | X |  | Approccio di avanzamento per passare JSON. |
| Passare il TOKEN IDP per l'applicazione |  | X |  | Ad esempio, da Facebook all'app. |

### <a name="session-management"></a>Gestione delle sessioni

| Funzionalità | Sviluppo. | Anteprima | GA | Note |
| ------- | ----------- | ------- | -- | ----- |
| Provider di sessioni SSO |  |  | X |  |
| Provider di sessioni di accesso esterno |  |  | X |  |
| Provider di sessioni SSO SAML |  |  | X |  |
| Provider della sessione SSO predefinito |  |  | X |  |

### <a name="security"></a>Security

| Funzionalità | Sviluppo. | Anteprima | GA | Note |
|-------- | ----------- | ------- | -- | ----- |
| Chiavi dei criteri: generazione, manuale, caricamento |  |  | X |  |
| Chiavi dei criteri: RSA/certificato, segreti |  |  | X |  |
| Caricamento dei criteri |  |  | X |  |

### <a name="developer-interface"></a>Interfaccia di sviluppo

| Funzionalità | Sviluppo. | Anteprima | GA | Note |
| ------- | ----------- | ------- | -- | ----- |
| Portale di Azure: esperienza utente del framework dell'esperienza di gestione delle identità |  |  | X |  |
| Log UserJourney di Application Insights |  | X |  | Utilizzato per la risoluzione dei problemi relativi durante lo sviluppo.  |
| Log eventi di Application Insights (tramite i passaggi di orchestrazione) |  | X |  | Utilizzato per monitorare i flussi degli utenti nell'ambiente di produzione. |

## <a name="next-steps"></a>Passaggi successivi
[Altre informazioni sui criteri personalizzati e le differenze con flussi utente](active-directory-b2c-overview-custom.md).
