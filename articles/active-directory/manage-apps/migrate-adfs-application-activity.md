---
title: Usare il report attività per spostare le app AD FS in Azure Active Directory | Microsoft Docs '
description: Il report attività applicazione Active Directory Federation Services (AD FS) consente di eseguire rapidamente la migrazione delle applicazioni da AD FS a Azure Active Directory (Azure AD). Questo strumento di migrazione per AD FS identifica la compatibilità con Azure AD e fornisce indicazioni sulla migrazione.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1245010ae0b21c5bb8e3ebd93a9fe851d48c858b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94835510"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>Usare il report delle attività dell'applicazione AD FS (anteprima) per eseguire la migrazione delle applicazioni ai Azure AD

Molte organizzazioni usano Active Directory Federation Services (AD FS) per fornire Single Sign-On alle applicazioni cloud. Ci sono vantaggi significativi nello trasferire le applicazioni AD FS Azure AD per l'autenticazione, soprattutto in termini di gestione dei costi, gestione dei rischi, produttività, conformità e governance. Tuttavia, comprendere quali applicazioni sono compatibili con Azure AD e identificare passaggi di migrazione specifici possono richiedere molto tempo.

Il AD FS report attività applicazione (anteprima) nel portale di Azure consente di identificare rapidamente quali applicazioni sono in grado di eseguire la migrazione a Azure AD. Valuta tutte le applicazioni AD FS per la compatibilità con Azure AD, verifica la presenza di eventuali problemi e fornisce indicazioni sulla preparazione delle singole applicazioni per la migrazione. Con il AD FS report attività applicazione, è possibile:

* **Individuare AD FS applicazioni e definire l'ambito della migrazione.** Il report attività applicazione AD FS elenca tutte le applicazioni AD FS nell'organizzazione e ne indica la conformità alla migrazione ai Azure AD.
* **Assegnare priorità alle applicazioni per la migrazione.** Ottenere il numero di utenti univoci che hanno eseguito l'accesso all'applicazione negli ultimi 1, 7 o 30 giorni per contribuire a determinare la criticità o il rischio di migrazione dell'applicazione.
* **Eseguire test di migrazione e risolvere i problemi.** Il servizio di creazione report esegue automaticamente i test per determinare se un'applicazione è pronta per la migrazione. I risultati vengono visualizzati nel report attività applicazione AD FS come stato di migrazione. Se vengono identificati potenziali problemi di migrazione, vengono fornite indicazioni specifiche su come risolvere i problemi.

I dati dell'attività AD FS applicazione sono disponibili per gli utenti a cui è stato assegnato uno di questi ruoli di amministratore: amministratore globale, lettore report, lettore sicurezza, amministratore dell'applicazione o amministratore di applicazioni cloud.

## <a name="prerequisites"></a>Prerequisiti

* L'organizzazione deve attualmente usare AD FS per accedere alle applicazioni.
* Azure AD Connect Health deve essere abilitato nel tenant del Azure AD.
* È necessario installare la Azure AD Connect Health per AD FS Agent.
   * [Altre informazioni su Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md)
   * [Introduzione alla configurazione di Azure AD Connect Health e all'installazione dell'agente di AD FS](../hybrid/how-to-connect-health-agent-install.md)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Individuare AD FS applicazioni di cui è possibile eseguire la migrazione 

Il report attività dell'applicazione AD FS è disponibile nella portale di Azure sotto Azure AD **utilizzo & report di Insights** . Il report attività applicazione AD FS analizza ogni applicazione AD FS per determinare se è possibile eseguirne la migrazione così come sono o se è necessaria una revisione aggiuntiva. 

1. Accedere al [portale di Azure](https://portal.azure.com) con un ruolo di amministratore che abbia accesso ai dati delle attività dell'applicazione ad FS (amministratore globale, lettore di report, lettore di sicurezza, amministratore dell'applicazione o amministratore di applicazioni cloud).

2. Selezionare **Azure Active Directory**, quindi selezionare **applicazioni aziendali**.

3. In **attività** selezionare **utilizzo & Insights (anteprima)** e quindi selezionare **ad FS attività dell'applicazione** per aprire un elenco di tutte le applicazioni ad FS nell'organizzazione.

   ![Attività AD FS applicazione](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Per ogni applicazione nell'elenco attività AD FS applicazione, visualizzare lo **stato di migrazione**:

   * **Pronto per la migrazione** significa che la configurazione dell'applicazione ad FS è completamente supportata in Azure ad ed è possibile eseguirne la migrazione così come sono.

   * **Verifica delle esigenze** significa che è possibile eseguire la migrazione di alcune impostazioni dell'applicazione in Azure ad, ma è necessario rivedere le impostazioni di cui non è possibile eseguire la migrazione così come sono.

   * **Passaggi aggiuntivi necessari** significa che Azure ad non supporta alcune delle impostazioni dell'applicazione, pertanto non è possibile eseguire la migrazione dell'applicazione nello stato corrente.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Valutare la conformità di un'applicazione per la migrazione 

1. Nell'elenco attività dell'applicazione AD FS fare clic sullo stato nella colonna **stato migrazione** per aprire i dettagli della migrazione. Verrà visualizzato un riepilogo dei test di configurazione passati, insieme a eventuali problemi di migrazione potenziali.

   ![Dettagli sulla migrazione](media/migrate-adfs-application-activity/migration-details.png)

2. Fare clic su un messaggio per aprire i dettagli aggiuntivi della regola di migrazione. Per un elenco completo delle proprietà sottoposte a test, vedere la tabella dei [test di configurazione dell'applicazione ad FS](#ad-fs-application-configuration-tests) riportata di seguito.

   ![Dettagli regola di migrazione](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>Test di configurazione dell'applicazione AD FS

Nella tabella seguente sono elencati tutti i test di configurazione eseguiti su AD FS applicazioni.

|Risultato  |Superato/avviso/esito negativo  |Descrizione  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRules <br> È stata rilevata almeno una regola non migrazione nei per AdditionalAuthentication.       | Pass/avviso          | Il relying party dispone di regole per richiedere l'autenticazione a più fattori (multi-factor authentication). Per spostarsi in Azure AD, tradurre tali regole in criteri di accesso condizionale. Se si usa un'autenticazione a più fattori locale, si consiglia di passare a Azure AD autenticazione a più fattori. [Altre informazioni sull'accesso condizionale](../authentication/concept-mfa-howitworks.md).        |
|Test-ADFSRPAdditionalWSFedEndpoint <br> Il valore di AdditionalWSFedEndpoint del componente è impostato su true.       | Esito positivo o negativo          | Il relying party in AD FS consente più endpoint dell'asserzione di WS-Fed.Attualmente Azure AD ne supporta solo uno.Se si dispone di uno scenario in cui il risultato è il blocco della migrazione, segnalare il [problema.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> Il componente ha impostato AllowedAuthenticationClassReferences.       | Esito positivo o negativo          | Questa impostazione in AD FS consente di specificare se l'applicazione è configurata in modo da consentire solo determinati tipi di autenticazione. Per ottenere questa funzionalità, è consigliabile usare l'accesso condizionale. Se si dispone di uno scenario in cui il risultato è il blocco della migrazione, segnalare il [problema.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)  [Altre informazioni sull'accesso condizionale](../authentication/concept-mfa-howitworks.md).          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResult      | Esito positivo o negativo          | Questa impostazione in AD FS consente di specificare se l'applicazione è configurata in modo da ignorare i cookie SSO e **richiedere sempre l'autenticazione**. In Azure AD, è possibile gestire la sessione di autenticazione usando i criteri di accesso condizionale per ottenere un comportamento simile. [Altre informazioni sulla configurazione della gestione delle sessioni di autenticazione con l'accesso condizionale](../conditional-access/howto-conditional-access-session-lifetime.md).          |
|Test-ADFSRPAutoUpdateEnabled <br> Il valore di AutoUpdateEnabled del componente è impostato su true       | Pass/avviso          | Questa impostazione in AD FS consente di specificare se la AD FS è configurata in modo da aggiornare automaticamente l'applicazione in base alle modifiche apportate all'interno dei metadati di Federazione. Azure AD attualmente non supporta questa operazione, ma non deve bloccare la migrazione dell'applicazione a Azure AD.           |
|Test-ADFSRPClaimsProviderName <br> Per la relying party sono abilitati più ClaimsProviders       | Esito positivo o negativo          | Questa impostazione in AD FS chiama i provider di identità da cui il relying party accetta attestazioni. In Azure AD, è possibile abilitare la collaborazione esterna utilizzando Azure AD B2B. [Scopri di più su Azure ad B2B](../external-identities/what-is-b2b.md).          |
|Test-ADFSRPDelegationAuthorizationRules      | Esito positivo o negativo          | Per l'applicazione sono definite regole di autorizzazione di delega personalizzate. Si tratta di un concetto di WS-Trust che Azure AD supporta usando i protocolli di autenticazione moderni, ad esempio OpenID Connect e OAuth 2,0. [Scopri di più sulla piattaforma di identità Microsoft](../develop/v2-protocols-oidc.md).          |
|Test-ADFSRPImpersonationAuthorizationRules       | Pass/avviso          | Per l'applicazione sono definite regole di autorizzazione di rappresentazione personalizzate.Si tratta di un concetto di WS-Trust che Azure AD supporta usando i protocolli di autenticazione moderni, ad esempio OpenID Connect e OAuth 2,0. [Scopri di più sulla piattaforma di identità Microsoft](../develop/v2-protocols-oidc.md).          |
|Test-ADFSRPIssuanceAuthorizationRules <br> È stata rilevata almeno una regola non migrazione nei per IssuanceAuthorization.       | Pass/avviso          | L'applicazione dispone di regole di autorizzazione di rilascio personalizzate definite in AD FS.Azure AD supporta questa funzionalità con Azure AD l'accesso condizionale. [Altre informazioni sull'accesso condizionale](../conditional-access/overview.md). <br> È inoltre possibile limitare l'accesso a un'applicazione da parte di utenti o gruppi assegnati all'applicazione. [Altre informazioni sull'assegnazione di utenti e gruppi per l'accesso alle applicazioni](./assign-user-or-group-access-portal.md).            |
|Test-ADFSRPIssuanceTransformRules <br> È stata rilevata almeno una regola non migrazione nei per IssuanceTransform.       | Pass/avviso          | L'applicazione dispone di regole di trasformazione rilascio personalizzate definite in AD FS. Azure AD supporta la personalizzazione delle attestazioni rilasciate nel token. Per altre informazioni, vedere [personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](../develop/active-directory-saml-claims-customization.md).           |
|Test-ADFSRPMonitoringEnabled <br> Il valore di MonitoringEnabled del componente è impostato su true.       | Pass/avviso          | Questa impostazione in AD FS consente di specificare se la AD FS è configurata in modo da aggiornare automaticamente l'applicazione in base alle modifiche apportate all'interno dei metadati di Federazione. Azure AD attualmente non supporta questa operazione, ma non deve bloccare la migrazione dell'applicazione a Azure AD.           |
|Test-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | Pass/avviso          | AD FS consente uno sfasamento dell'ora in base ai tempi di NotBefore e NotOnOrAfter nel token SAML. Azure AD gestisce automaticamente questo valore per impostazione predefinita.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> Il valore di RequestMFAFromClaimsProviders del componente è impostato su true.       | Pass/avviso          | Questa impostazione in AD FS determina il comportamento per l'autenticazione a più fattori quando l'utente deriva da un provider di attestazioni diverso. In Azure AD, è possibile abilitare la collaborazione esterna utilizzando Azure AD B2B. Quindi, è possibile applicare i criteri di accesso condizionale per proteggere l'accesso guest. Altre informazioni su [Azure ad B2B](../external-identities/what-is-b2b.md) e sull' [accesso condizionale](../conditional-access/overview.md).          |
|Test-ADFSRPSignedSamlRequestsRequired <br> Il valore di SignedSamlRequestsRequired del componente è impostato su true       | Esito positivo o negativo          | L'applicazione è configurata in AD FS per verificare la firma nella richiesta SAML. Azure AD accetta una richiesta SAML firmata; Tuttavia, non verificherà la firma. Azure AD dispone di metodi diversi per la protezione da chiamate dannose. Ad esempio, Azure AD usa gli URL di risposta configurati nell'applicazione per convalidare la richiesta SAML. Azure AD invierà un token solo agli URL di risposta configurati per l'applicazione. Se si dispone di uno scenario in cui il risultato è il blocco della migrazione, segnalare il [problema.](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)          |
|Test-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Pass/avviso         | L'applicazione viene configurata per la durata di un token personalizzato. Il AD FS valore predefinito è un'ora.Azure AD supporta questa funzionalità usando l'accesso condizionale. Per altre informazioni, vedere [configurare la gestione delle sessioni di autenticazione con l'accesso condizionale](../conditional-access/howto-conditional-access-session-lifetime.md).          |
|Il componente è impostato per la crittografia delle attestazioni. Questa operazione è supportata da Azure AD       | Pass          | Con Azure AD, è possibile crittografare il token inviato all'applicazione. Per altre informazioni, vedere [configurare Azure ad crittografia di token SAML](./howto-saml-token-encryption.md).          |
|EncryptedNameIdRequiredCheckResult      | Esito positivo o negativo          | L'applicazione è configurata per crittografare l'attestazione nameID nel token SAML.Con Azure AD, è possibile crittografare l'intero token inviato all'applicazione.La crittografia di attestazioni specifiche non è ancora supportata. Per altre informazioni, vedere [configurare Azure ad crittografia di token SAML](./howto-saml-token-encryption.md).         |

## <a name="check-the-results-of-claim-rule-tests"></a>Verificare i risultati dei test delle regole attestazioni

Se è stata configurata una regola attestazioni per l'applicazione in AD FS, l'esperienza fornirà un'analisi granulare per tutte le regole attestazioni. Verranno visualizzate le regole attestazioni che possono essere spostate Azure AD e quelle che necessitano di ulteriori verifiche.

1. Nell'elenco attività dell'applicazione AD FS fare clic sullo stato nella colonna **stato migrazione** per aprire i dettagli della migrazione. Verrà visualizzato un riepilogo dei test di configurazione passati, insieme a eventuali problemi di migrazione potenziali.

2. Nella pagina **Dettagli regola di migrazione** espandere i risultati per visualizzare i dettagli sui potenziali problemi di migrazione e ottenere altre informazioni aggiuntive. Per un elenco dettagliato di tutte le regole attestazioni testate, vedere la tabella [verificare i risultati dei test delle regole attestazioni](#check-the-results-of-claim-rule-tests) , più avanti.

   Nell'esempio seguente vengono illustrati i dettagli della regola di migrazione per la regola IssuanceTransform. Elenca le parti specifiche dell'attestazione che devono essere esaminate e risolte prima di poter eseguire la migrazione dell'applicazione a Azure AD.

   ![Informazioni aggiuntive sulla regola di migrazione](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Test delle regole attestazioni

Nella tabella seguente sono elencati tutti i test delle regole attestazioni eseguiti su AD FS applicazioni.

|Proprietà  |Descrizione  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | L'istruzione Condition usa espressioni regolari per valutare se l'attestazione corrisponde a un determinato modello.Per ottenere una funzionalità simile in Azure AD, è possibile usare tra gli altri la trasformazione predefinita, ad esempio IfEmpty (), cominciamo (), Contains (). Per altre informazioni, vedere [personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](../develop/active-directory-saml-claims-customization.md).          |
|UNSUPPORTED_CONDITION_CLASS      | L'istruzione Condition presenta più condizioni che devono essere valutate prima di eseguire l'istruzione di rilascio.Azure AD possibile supportare questa funzionalità con le funzioni di trasformazione dell'attestazione in cui è possibile valutare più valori di attestazione.Per altre informazioni, vedere [personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](../develop/active-directory-saml-claims-customization.md).          |
|UNSUPPORTED_RULE_TYPE      | Non è stato possibile riconoscere la regola attestazioni. Per altre informazioni su come configurare le attestazioni in Azure AD, vedere [personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](../develop/active-directory-saml-claims-customization.md).          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | L'istruzione Condition usa un'autorità emittente non supportata in Azure AD.Attualmente, Azure AD non prevede l'origine di attestazioni da archivi diversi da Active Directory o Azure AD. Se questa operazione impedisce la migrazione di applicazioni a Azure AD, è possibile [inviarle informazioni](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).         |
|UNSUPPORTED_CONDITION_FUNCTION      | L'istruzione Condition utilizza una funzione di aggregazione per emettere o aggiungere una singola attestazione indipendentemente dal numero di corrispondenze.In Azure AD, è possibile valutare l'attributo di un utente per decidere quale valore usare per l'attestazione con funzioni come IfEmpty (), cominciamo (), Contains (), tra gli altri.Per altre informazioni, vedere [personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](../develop/active-directory-saml-claims-customization.md).          |
|RESTRICTED_CLAIM_ISSUED      | L'istruzione Condition usa un'attestazione limitata in Azure AD. Potrebbe essere possibile emettere un'attestazione con restrizioni, ma non è possibile modificarne l'origine o applicare una trasformazione. Per altre informazioni, vedere [personalizzare le attestazioni emesse nei token per un'app specifica in Azure ad](../develop/active-directory-claims-mapping.md).          |
|EXTERNAL_ATTRIBUTE_STORE      | L'istruzione di rilascio usa un archivio di attributi diverso da quello Active Directory. Attualmente, Azure AD non prevede l'origine di attestazioni da archivi diversi da Active Directory o Azure AD. Se il risultato è il blocco della migrazione di applicazioni a Azure AD [, segnalare](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)il problema.          |
|UNSUPPORTED_ISSUANCE_CLASS      | L'istruzione di rilascio USA Aggiungi per aggiungere attestazioni al set di attestazioni in ingresso. In Azure AD, questo può essere configurato come più trasformazioni di attestazioni.Per altre informazioni, vedere [personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](../develop/active-directory-claims-mapping.md).         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | L'istruzione di rilascio usa espressioni regolari per trasformare il valore dell'attestazione da emettere.Per ottenere una funzionalità simile in Azure AD, è possibile usare una trasformazione predefinita, ad esempio Extract (), Trim (), ToLower, tra gli altri. Per altre informazioni, vedere [personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](../develop/active-directory-saml-claims-customization.md).          |


## <a name="next-steps"></a>Passaggi successivi

- [Video: come usare il report attività di AD FS per eseguire la migrazione di un'applicazione](https://www.youtube.com/watch?v=OThlTA239lU)
- [Gestione di applicazioni con Azure Active Directory](what-is-application-management.md)
- [Gestire l'accesso alle app](what-is-access-management.md)
- [Azure AD Connect e federazione](../hybrid/how-to-connect-fed-whatis.md)