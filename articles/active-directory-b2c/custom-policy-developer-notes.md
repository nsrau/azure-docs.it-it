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
ms.date: 02/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ee3b5bd3278412949074b77f9d1c53d63a467280
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189396"
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

### <a name="identity-providers-tokens-protocols"></a>Provider di identità, token, protocolli

| Funzionalità | Sviluppo | Anteprima | GA | Note |
|-------- | :-----------: | :-------: | :--: | ----- |
| IDP-OpenIDConnect |  |  | X | Ad esempio, Google.  |
| IDP-OAUTH2 |  |  | X | Ad esempio, Facebook.  |
| IDP-OAUTH1 (twitter) |  | X |  | Ad esempio, Twitter. |
| IDP-OAUTH1 (ex-twitter) |  |  |  | Non supportate |
| IDP-SAML |  |   | X | Ad esempio, Salesforce, ADFS. |
| IDP-WSFED | X |  |  |  |
| Relying Party OAUTH1 |  |  |  | Non supportato. |
| Relying Party OAUTH2 |  |  | X |  |
| Relying Party OIDC |  |  | X |  |
| Relying Party SAML |  |X  |  |  |
| Relying Party WSFED | X |  |  |  |
| API REST con autenticazione di base e certificato |  |  | X | Ad esempio, App per la logica di Azure.For example, Azure Logic Apps. |

### <a name="component-support"></a>Supporto dei componenti

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| Azure Multi-Factor Authentication |  |  | X |  |
| Azure Active Directory come directory locale |  |  | X |  |
| Sottosistema di posta elettronica di Azure per la verifica della posta elettronicaAzure Email subsystem for email verification |  |  | X |  |
| Supporto per più linguaggi|  |  | X |  |
| Convalide dei predicatiPredicate Validations |  |  | X | Ad esempio, complessità della password. |
| Utilizzo di provider di servizi di posta elettronica di terze parti |  |X  |  |  |

### <a name="content-definition"></a>Definizione del contenuto

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| Pagina di errore, api.error |  |  | X |  |
| Pagina di selezione IDP, api.idpselections |  |  | X |  |
| Selezione IDP per l'iscrizione, api.idpselections.signup |  |  | X |  |
| Password dimenticata, api.localaccountpasswordreset |  |  | X |  |
| Accesso all'account locale, api.localaccountsignin |  |  | X |  |
| Iscrizione dell'account locale, api.localaccountsignin |  |  | X |  |
| Pagina MFA, api.phonefactor |  |  | X |  |
| Iscrizione automatica dell'account social, api.selfasserted |  |  | X |  |
| Aggiornamento del profilo autocertificato, api.selfasserted.profileupdate |  |  | X |  |
| Pagina di iscrizione o di accesso unificata, api.signuporsignin, con il parametro "disableSignup" |  |  | X |  |
| JavaScript / Layout di pagina |  | X |  |  |

### <a name="app-ief-integration"></a>Integrazione del framework dell'esperienza di gestione delle identità dell'app

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| Parametro di stringa della query domain_hint |  |  | X | Disponibile come attestazione, può essere passato a IDP. |
| Parametro di stringa della query login_hint |  |  | X | Disponibile come attestazione, può essere passato a IDP. |
| Inserimento di JSON in UserJourney tramite client_assertion | X |  |  | Sarà deprecato. |
| Inserimento di JSON in UserJourney come id_token_hint |  | X |  | Approccio di forward per passare JSON. |
| Passare IDP TOKEN all'applicazione |  | X |  | Ad esempio, da Facebook all'app. |

### <a name="session-management"></a>Gestione delle sessioni

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| Provider di sessioni SSO |  |  | X |  |
| Provider di sessioni di accesso esterno |  |  | X |  |
| Provider di sessioni SSO SAML |  |  | X |  |
| Provider di sessione SSO predefinito |  |  | X |  |

### <a name="security"></a>Security

| Funzionalità | Sviluppo | Anteprima | GA | Note |
|-------- | :-----------: | :-------: | :--: | ----- |
| Chiavi dei criteri: generazione, manuale, caricamento |  |  | X |  |
| Chiavi dei criteri: RSA/certificato, segreti |  |  | X |  |
| Caricamento dei criteri |  |  | X |  |

### <a name="developer-interface"></a>Interfaccia di sviluppo

| Funzionalità | Sviluppo | Anteprima | GA | Note |
| ------- | :-----------: | :-------: | :--: | ----- |
| Portale di Azure: esperienza utente del framework dell'esperienza di gestione delle identità |  |  | X |  |
| Log UserJourney di Application Insights |  | X |  | Utilizzato per la risoluzione dei problemi durante lo sviluppo.  |
| Registri eventi di Application Insights (tramite passaggi di orchestrazione)Application Insights Event Logs (via orchestration steps) |  | X |  | Utilizzato per monitorare i flussi utente nell'ambiente di produzione. |

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sui [criteri personalizzati e sulle differenze con i flussi utente](custom-policy-overview.md).
