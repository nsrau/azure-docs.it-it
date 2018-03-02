---
title: 'Azure Active Directory B2C: note per gli sviluppatori sull''uso dei criteri personalizzati | Microsoft Docs'
description: Note per gli sviluppatori sulla configurazione e la gestione di Azure AD B2C con criteri personalizzati
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
ms.date: 10/13/2017
ms.author: joroja
ms.openlocfilehash: 7f65ad324e1bf0887ea293254621466a2f1ddaff
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2018
---
# <a name="release-notes-for-azure-active-directory-b2c-custom-policy-public-preview"></a>Note sulla versione di anteprima pubblica dei criteri personalizzati di Azure Active Directory B2C
Il set di funzionalità dei criteri personalizzati è ora disponibile per la valutazione in anteprima pubblica per tutti i clienti di Azure Active Directory B2C (Azure AD B2C). Questo set di funzionalità è destinato agli sviluppatori esperti che creano le soluzioni di gestione delle identità più complesse.  

Attualmente, questo set di funzionalità richiede agli sviluppatori di configurare direttamente il framework dell'esperienza di gestione delle identità tramite la modifica di file XML. Si tratta di un metodo di configurazione avanzato e complesso. Gli sviluppatori esperti di soluzioni di gestione delle identità che usano questo framework dovranno includere nei piani il tempo necessario per completare le procedure dettagliate e leggere i documenti di riferimento. 

## <a name="features-included-in-this-public-preview"></a>Funzionalità incluse nell'anteprima pubblica
Con le nuove funzionalità introdotte nell'anteprima pubblica, gli sviluppatori possono eseguire queste attività:<br>

* Creare e caricare percorsi utente di autenticazione personalizzati usando criteri personalizzati. 
   * Descrivere in modo dettagliato i percorsi utente come scambi tra provider di attestazioni. 
   * Definire la diramazione condizionale nei percorsi utente. 
* Integrare servizi abilitati per API REST nei percorsi utente di autenticazione personalizzati.  
* Aggiungere la federazione con provider di identità conformi allo standard OpenID Connect. <br>
* Aggiungere la federazione con provider di identità che rispettano il protocollo SAML 2.0. 

## <a name="terms-of-the-public-preview"></a>Condizioni per l'anteprima pubblica

* È consigliabile usare le nuove funzionalità solo a scopo di valutazione.<br>
* Le nuove funzionalità non sono destinate all'uso in un ambiente di produzione.<br>
* I contratti di servizio non si applicano alle nuove funzionalità. <br>
* È possibile inviare richieste di supporto tramite i normali canali del supporto. <br>
* Non è stata fissata una data per la disponibilità generale.<br>
* A propria discrezione e per qualsiasi motivo, Microsoft può contrassegnare e rifiutare o limitare gli scenari e i percorsi utente che esulano dagli obiettivi del prodotto Azure AD B2C di offrire una piattaforma di gestione delle identità e degli accessi per i clienti.

## <a name="responsibilities-of-custom-policy-feature-set-developers"></a>Responsabilità degli sviluppatori che usano il set di funzionalità dei criteri personalizzati
La configurazione manuale dei criteri garantisce un accesso di livello inferiore alla piattaforma sottostante di Azure AD B2C e determina la creazione di un framework attendibilità interamente personalizzabile e univoco. Le possibili permutazioni di provider di identità personalizzati, relazioni di trust, integrazioni con servizi esterni e flussi di lavoro dettagliati risultano particolarmente impegnative per gli sviluppatori esperti da cui vengono utilizzate.

Per sfruttare appieno l'anteprima pubblica, è consigliabile che gli sviluppatori che utilizzano il set di funzionalità dei criteri personalizzati si attengano alle linee guida seguenti:
* Acquisire familiarità con il linguaggio di configurazione del framework dell'esperienza di gestione delle identità e con la gestione di chiavi e segreti.
* Assumere la proprietà degli scenari e delle integrazioni personalizzate.
* Eseguire test metodici degli scenari.
* Seguire le procedure consigliate di staging e sviluppo software con almeno un ambiente di sviluppo e testing e un ambiente di produzione.
* Mantenersi aggiornati sui nuovi sviluppi dei servizi e dei provider di identità con cui viene eseguita l'integrazione. Ad esempio, tenere traccia delle modifiche dei segreti e delle modifiche pianificate e non pianificate del servizio.
* Configurare il monitoraggio attivo e monitorare il tempo di risposta degli ambienti di produzione.
* Mantenere aggiornati gli indirizzi di posta elettronica di contatto nella sottoscrizione di Azure e prestare attenzione ai messaggi di posta elettronica del team del sito live Microsoft.
* Intervenire tempestivamente quando consigliato del team del sito live Microsoft. 

## <a name="features-by-stage-and-known-issues"></a>Funzionalità per fase e problemi noti
Le funzionalità dei criteri personalizzati e del framework dell'esperienza di gestione delle identità sono in costante e rapida evoluzione.  Questa tabella è un indice di funzionalità/disponibilità dei componenti.

Pubblicare domande su Stack Overflow all'indirizzo [https://aka.ms/aadb2cso](https://aka.ms/aadb2cso)


### <a name="identity-providers-tokens-protocols"></a>Provider di identità, token, protocolli
Interfacce con applicazioni e componenti esterni

| Funzionalità | Sviluppo. | Preview | GA | Note |
|---------------------------------------------|-------------|---------|----|-------|
| IDP-OpenIDConnect |  | x |  | Ad esempio, Google+ |
| IDP-OAUTH2 |  | x |  | Ad esempio, Facebook  |
| IDP-OAUTH1 |  | x |  | Ad esempio, Twitter |
| IDP-SAML |  | x |  | Ad esempio, Salesforce, AD FS |
| IDP-WSFED | x |  |  |  |
| Relying party OAUTH |  | x |  |  |
| Relying Party OIDC |  | x |  |  |
| Relying Party SAML | x |  |  |  |
| Relying Party WSFED | x |  |  |  |
| API REST con autenticazione di base e del certificato |  | x |  | Ad esempio, Funzioni di Azure |


### <a name="component-support"></a>Supporto dei componenti


| Funzionalità | Sviluppo. | Preview | GA | Note |
|-------------------------------------------|-------------|---------|----|-------|
| Azure Multi-Factor Authentication |  | x |  |  |
| Azure Active Directory come directory locale |  | x |  |  |
| Sottosistema di posta elettronica di Azure per l'autenticazione a due fattori |  | x |  |  |
| Supporto per più linguaggi|  | x |  |  |
| Complessità password | x |  |  |  |


### <a name="content-definition"></a>Definizione del contenuto

| Funzionalità | Sviluppo. | Preview | GA | Note |
|-----------------------------------------------------------------------------|-------------|---------|----|-------|
|   Pagina di errore, api.error |  | x |  |  |
|   Pagina di selezione IDP, api.idpselections |  | x |  |  |
|   Selezione IDP per l'iscrizione, api.idpselections.signup |  | x |  |  |
|   Password dimenticata, api.localaccountpasswordreset |  | x |  |  |
|   Accesso all'account locale, api.localaccountsignin |  | x |  |  |
|   Iscrizione dell'account locale, api.localaccountsignin |  | x |  |  |
|   Pagina MFA, api.phonefactor |  | x |  |  |
|   Autocertificazione, ad esempio iscrizione all'account di social networking, api.selfasserted |  | x |  |  |
|   Aggiornamento del profilo autocertificato, api.selfasserted.profileupdate |  | x |  |  |
|   Pagina di accesso o iscrizione unificata, api.signuporsignin |  | x |  |  |


### <a name="app-ief-integration"></a>Integrazione del framework dell'esperienza di gestione delle identità dell'app
| Funzionalità | Sviluppo. | Preview | GA | Note |
|--------------------------------------------------|-------------|---------|----|-------------------------------------------------|
| Parametro di stringa della query id_token_hint | x |  |  |  |
| Parametro di stringa della query domain_hint |  | x |  | Disponibile come attestazione, può essere passato a all'IDP |
| Parametro di stringa della query login_hint |  | x |  | Disponibile come attestazione, può essere passato a all'IDP |
| Inserimento di JSON in UserJourney tramite client_assertion | x |  |  | Verrà deprecato |
| Inserimento di JSON in UserJourney come id_token_hint | x |  |  | Approccio di avanzamento per passare JSON |


### <a name="session-management"></a>Gestione delle sessioni

| Funzionalità | Sviluppo. | Preview | GA | Note |
|---------------------------------|-------------|---------|----|-------|
| Provider di sessioni SSO |  | x |  |  |
| Provider di sessioni di accesso esterno |  | x |  |  |
| Provider di sessioni SSO SAML |  | x |  |  |


### <a name="security"></a>Sicurezza
| Funzionalità | Sviluppo. | Preview | GA | Note |
|---------------------------------------------|-------------|---------|----|-------|
| Chiavi dei criteri: generazione, manuale, caricamento |  | x |  |  |
| Chiavi dei criteri: RSA/certificato, segreti |  | x |  |  |


### <a name="developer-interface"></a>Interfaccia di sviluppo
| Funzionalità | Sviluppo. | Preview | GA | Note |
|---------------------------------------------|-------------|---------|----|-------|
| Portale di Azure: esperienza utente del framework dell'esperienza di gestione delle identità |  | x |  |  |
| Log UserJourney di Application Insights  |  | x |  |  |
| Log eventi di Application Insights |x|  |  |  |



## <a name="next-steps"></a>Passaggi successivi
[Introduzione ai criteri personalizzati](active-directory-b2c-get-started-custom.md)
