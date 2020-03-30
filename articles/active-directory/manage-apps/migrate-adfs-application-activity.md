---
title: Usare il report attività per spostare le app ADFS in Azure Active Directory. Documenti Microsoft'
description: Il report sull'attività delle applicazioni di Active Directory Federation Services (ADFS) consente di eseguire rapidamente la migrazione delle applicazioni da ADFS ad Azure Active Directory (Azure AD). Questo strumento di migrazione per ADFS identifica la compatibilità con Azure AD e fornisce indicazioni sulla migrazione.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/14/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 333e440fdd5f5062dda45fb12a83543c63e66c04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978025"
---
# <a name="use-the-ad-fs-application-activity-report-preview-to-migrate-applications-to-azure-ad"></a>Usare il report dell'attività dell'applicazione ADFS (anteprima) per eseguire la migrazione delle applicazioni in Azure ADUse the AD FS application activity report (preview) to migrate applications to Azure AD

Molte organizzazioni utilizzano Active Directory Federation Services (ADFS) per fornire l'accesso Single Sign-On alle applicazioni cloud. Lo spostamento delle applicazioni ADFS in Azure AD è vantaggioso, in particolare in termini di gestione dei costi, gestione dei rischi, produttività, conformità e governance, offre vantaggi significativi. Tuttavia, comprendere quali applicazioni sono compatibili con Azure AD e identificare passaggi di migrazione specifici può richiedere molto tempo.

Il report sull'attività delle applicazioni ADFS (anteprima) nel portale di Azure consente di identificare rapidamente quali applicazioni sono in grado di eseguire la migrazione ad Azure AD. Valuta la compatibilità di tutte le applicazioni ADFS con Azure AD, verifica la presenza di eventuali problemi e fornisce indicazioni sulla preparazione di singole applicazioni per la migrazione. Con il report attività dell'applicazione ADFS, è possibile:With the AD FS application activity report, you can:

* **Individuare le applicazioni ADFS e definire l'ambito della migrazione.** Il report attività dell'applicazione ADFS elenca tutte le applicazioni ADFS nell'organizzazione e ne indica la disponibilità per la migrazione ad Azure AD.
* **Assegnare priorità alle applicazioni per la migrazione.** Ottenere il numero di utenti univoci che hanno eseguito l'accesso all'applicazione negli ultimi 1, 7 o 30 giorni per determinare la criticità o il rischio di migrazione dell'applicazione.
* **Eseguire i test di migrazione e risolvere i problemi.** Il servizio di report esegue automaticamente i test per determinare se un'applicazione è pronta per la migrazione. I risultati vengono visualizzati nel report attività applicazione ADFS come stato di migrazione. Se vengono identificati potenziali problemi di migrazione, si ottengono indicazioni specifiche su come risolverli.

I dati dell'attività dell'applicazione ADFS sono disponibili per gli utenti a cui è assegnato uno di questi ruoli di amministratore: amministratore globale, lettore di report, lettore di sicurezza, amministratore dell'applicazione o amministratore dell'applicazione cloud.

## <a name="prerequisites"></a>Prerequisiti

* L'organizzazione deve attualmente utilizzare ADFS per accedere alle applicazioni.
* Azure AD Connect Health deve essere abilitato nel tenant di Azure AD.
   * [Altre informazioni su Azure AD Connect HealthLearn more about Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [Introduzione alla configurazione di Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Individuare le applicazioni ADFS di cui è possibile eseguire la migrazione 

Il report sull'attività dell'applicazione ADFS è disponibile nel portale di Azure in Utilizzo di Azure AD & creazione di **report dettagliati.** Il report dell'attività dell'applicazione ADFS analizza ogni applicazione ADFS per determinare se è possibile eseguirne la migrazione così com'è o se è necessaria un'ulteriore revisione. 

1. Accedere al [portale](https://portal.azure.com) di Azure con un ruolo di amministratore che ha accesso ai dati dell'attività dell'applicazione ADFS (amministratore globale, lettore di report, lettore di sicurezza, amministratore dell'applicazione o amministratore dell'applicazione cloud).

2. Selezionare **Azure Active Directory**, quindi selezionare Applicazioni **aziendali**.

3. In **Attività**selezionare **Informazioni & informazioni dettagliate (anteprima)** e quindi selezionare **Attività applicazione ADFS** per aprire un elenco di tutte le applicazioni ADFS nell'organizzazione.

   ![Attività dell'applicazione ADFSAD FS application activity](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Per ogni applicazione nell'elenco attività di ADFS, visualizzare **lo stato della migrazione:**

   * **Pronto per la migrazione** significa che la configurazione dell'applicazione ADFS è completamente supportata in Azure AD e può essere migrata così com'è.

   * **Revisione delle** esigenze indica che alcune delle impostazioni dell'applicazione possono essere migrate in Azure AD, ma è necessario rivedere le impostazioni di cui non è possibile eseguire la migrazione così com'è.

   * **Passaggi aggiuntivi necessari** indicano che Azure AD non supporta alcune impostazioni dell'applicazione, pertanto non è possibile eseguire la migrazione dell'applicazione nello stato corrente.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Valutare la disponibilità di un'applicazione per la migrazione 

1. Nell'elenco attività applicazione ADFS, fare clic sullo stato nella colonna **Stato migrazione** per aprire i dettagli della migrazione. Verrà visualizzato un riepilogo dei test di configurazione superati, insieme a eventuali problemi di migrazione.

   ![Dettagli sulla migrazione](media/migrate-adfs-application-activity/migration-details.png)

2. Fare clic su un messaggio per aprire ulteriori dettagli della regola di migrazione. Per un elenco completo delle proprietà testate, vedere la tabella dei test di [configurazione dell'applicazione ADFS,](#ad-fs-application-configuration-tests) di seguito.

   ![Dettagli regola di migrazione](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>Test di configurazione dell'applicazione ADFSAD FS application configuration tests

Nella tabella seguente sono elencati tutti i test di configurazione eseguiti nelle applicazioni ADFS.

|Risultato  |Passaggio/avviso/errore  |Descrizione  |
|---------|---------|---------|
|Test-ADFSRPAdditionalAuthenticationRulesTest-ADFSRPAdditionalAuthenticationRules <br> È stata rilevata almeno una regola non migratable per AdditionalAuthentication.       | Passaggio/Avviso          | La relying party dispone di regole per richiedere l'autenticazione a più fattori (MFA). Per passare ad Azure AD, convertire tali regole in criteri di accesso condizionale. Se si usa un'autenticazione a più fattori locale, è consigliabile passare ad Azure MFA. [Ulteriori informazioni sull'accesso condizionale](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).        |
|Test-ADFSRPAdditionalWSFedEndpointTest-ADFSRPAdditionalWSFedEndpoint <br> Relying party ha AdditionalWSFedEndpoint impostato su true.       | Esito positivo o negativo          | La relying party in ADFS consente più endpoint di asserzione WS-Fed.Attualmente, Azure AD ne supporta solo uno.Se si dispone di uno scenario in cui questo risultato blocca la migrazione, [comunicacelo](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints).     |
|Test-ADFSRPAllowedAuthenticationClassReferences <br> Relying Party has set AllowedAuthenticationClassReferences.       | Esito positivo o negativo          | Questa impostazione in ADFS consente di specificare se l'applicazione è configurata per consentire solo determinati tipi di autenticazione. È consigliabile usare l'accesso condizionale per ottenere questa funzionalità. Se si dispone di uno scenario in cui questo risultato blocca la migrazione, [comunicacelo](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication).  [Ulteriori informazioni sull'accesso condizionale](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).          |
|Test-ADFSRPAlwaysRequireAuthentication <br> AlwaysRequireAuthenticationCheckResultAlwaysRequireAuthenticationCheckResult      | Esito positivo o negativo          | Questa impostazione di ADFS consente di specificare se l'applicazione è configurata per ignorare i cookie SSO e **richiedere sempre l'autenticazione**. In Azure AD è possibile gestire la sessione di autenticazione usando i criteri di accesso condizionale per ottenere un comportamento simile. [Ulteriori informazioni sulla configurazione della gestione delle sessioni di autenticazione con Accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|Test-ADFSRPAutoUpdateEnabled <br> Relying Party ha AutoUpdateEnabled impostato su true       | Passaggio/Avviso          | Questa impostazione di ADFS consente di specificare se ADFS è configurato per aggiornare automaticamente l'applicazione in base alle modifiche all'interno dei metadati federativi. Azure AD non supporta questo oggi, ma non deve bloccare la migrazione dell'applicazione ad Azure AD.           |
|Test-ADFSRPClaimsProviderNameTest-ADFSRPClaimsProviderName <br> Relying Party ha più ClaimsProviders abilitati       | Esito positivo o negativo          | Questa impostazione in ADFS chiama i provider di identità da cui la relying party accetta attestazioni. In Azure AD, you can enable external collaboration using Azure AD B2B. [Altre informazioni su Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b).          |
|Test-ADFSRPDelegationAuthorizationRulesTest-ADFSRPDelegationAuthorizationRules      | Esito positivo o negativo          | Per l'applicazione sono definite regole di autorizzazione di delega personalizzate. Si tratta di un concetto di WS-Trust supportato da Azure AD tramite protocolli di autenticazione moderni, ad esempio OpenID Connect e OAuth 2.0. [Ulteriori informazioni su Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|Test-ADFSRPImpersonationAuthorizationRulesTest-ADFSRPImpersonationAuthorizationRules       | Passaggio/Avviso          | L'applicazione dispone di regole di autorizzazione di rappresentazione personalizzate definite.Si tratta di un concetto di WS-Trust supportato da Azure AD tramite protocolli di autenticazione moderni, ad esempio OpenID Connect e OAuth 2.0. [Ulteriori informazioni su Microsoft Identity Platform](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc).          |
|Test-ADFSRPIssuanceAuthorizationRules <br> È stata rilevata almeno una regola non migrabile per IssuanceAuthorization.       | Passaggio/Avviso          | L'applicazione dispone di regole di autorizzazione di rilascio personalizzate definite in ADFS.Azure AD supporta questa funzionalità con l'accesso condizionale di Azure AD. [Ulteriori informazioni sull'accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/overview). <br> È inoltre possibile limitare l'accesso a un'applicazione in base agli utenti o ai gruppi assegnati all'applicazione. [Ulteriori informazioni sull'assegnazione di utenti e gruppi per accedere alle applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups).            |
|Test-ADFSRPIssuanceTransformRulesTest-ADFSRPIssuanceTransformRules <br> È stata rilevata almeno una regola non migrabile per IssuanceTransform.       | Passaggio/Avviso          | L'applicazione dispone di regole di trasformazione rilascio personalizzate definite in ADFS. Azure AD supporta la personalizzazione delle attestazioni rilasciate nel token. Per ulteriori informazioni, consultate [Personalizzare le attestazioni emesse nel token SAML per](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)le applicazioni aziendali.           |
|Test-ADFSRPMonitoringEnabled <br> Per relying party MonitoringEnabled è impostato su true.       | Passaggio/Avviso          | Questa impostazione di ADFS consente di specificare se ADFS è configurato per aggiornare automaticamente l'applicazione in base alle modifiche all'interno dei metadati federativi. Azure AD non supporta questo oggi, ma non deve bloccare la migrazione dell'applicazione ad Azure AD.           |
|Test-ADFSRPNotBeforeSkewTest-ADFSRPNotBeforeSkew <br> NotBeforeSkewCheckResult      | Passaggio/Avviso          | ADFS consente uno sfacumento dell'ora in base ai tempi NotBefore e NotOnOrAfter nel token SAML. Azure AD gestisce automaticamente questa operazione per impostazione predefinita.          |
|Test-ADFSRPRequestMFAFromClaimsProviders <br> Requesting Party ha RequestMFAFromClaimsProviders impostato su true.       | Passaggio/Avviso          | Questa impostazione in ADFS determina il comportamento per l'autenticazione a più fattori quando l'utente proviene da un provider di attestazioni diverso. In Azure AD, you can enable external collaboration using Azure AD B2B. È quindi possibile applicare criteri di accesso condizionale per proteggere l'accesso guest. Altre informazioni su [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) e [accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/overview).          |
|Test-ADFSRPSignedSamlRequestsRequired <br> Relying Party ha SignedSamlRequestsRequired impostato su true       | Esito positivo o negativo          | L'applicazione è configurata in ADFS per verificare la firma nella richiesta SAML. Azure AD accetta una richiesta SAML firmata. tuttavia, non verificherà la firma. Azure AD dispone di diversi metodi per la protezione da chiamate dannose. Ad esempio, Azure AD usa gli URL di risposta configurati nell'applicazione per convalidare la richiesta SAML. Azure AD invierà solo un token per rispondere agli URL configurati per l'applicazione. Se si dispone di uno scenario in cui questo risultato blocca la migrazione, [comunicacelo](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature).          |
|Test-ADFSRPTokenLifetimeTest-ADFSRPTokenLifetime <br> TokenLifetimeCheckResult        | Passaggio/Avviso         | L'applicazione è configurata per una durata del token personalizzato. Il valore predefinito di ADFS è un'ora.Azure AD supporta questa funzionalità usando l'accesso condizionale. Per ulteriori informazioni, vedere Configurare la gestione delle sessioni di [autenticazione con accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|Relying Party è impostato per crittografare le attestazioni. Questa funzionalità è supportata da Azure ADThis is supported by Azure AD       | Pass          | Con Azure AD è possibile crittografare il token inviato all'applicazione. Per altre informazioni, vedere Configurare la [crittografia del token SAML di Azure AD.](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)          |
|EncryptedNameIdRequiredCheckResult      | Esito positivo o negativo          | L'applicazione è configurata per crittografare l'attestazione nameID nel token SAML.Con Azure AD è possibile crittografare l'intero token inviato all'applicazione.La crittografia di attestazioni specifiche non è ancora supportata. Per altre informazioni, vedere Configurare la [crittografia del token SAML di Azure AD.](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)         |

## <a name="check-the-results-of-claim-rule-tests"></a>Controllare i risultati dei test delle regole attestazione

Se è stata configurata una regola attestazione per l'applicazione in ADFS, l'esperienza fornirà un'analisi granulare per tutte le regole attestazione. Vedrai quali regole attestazione possono essere spostate in Azure AD e quali devono essere esaminate ulteriormente.

1. Nell'elenco attività applicazione ADFS, fare clic sullo stato nella colonna **Stato migrazione** per aprire i dettagli della migrazione. Verrà visualizzato un riepilogo dei test di configurazione superati, insieme a eventuali problemi di migrazione.

2. Nella pagina **Dettagli regola** di migrazione espandere i risultati per visualizzare i dettagli sui potenziali problemi di migrazione e per ottenere ulteriori indicazioni. Per un elenco dettagliato di tutte le regole attestazione testate, vedere la tabella Controllare i risultati dei test delle [regole attestazione](#check-the-results-of-claim-rule-tests) di seguito.

   L'esempio seguente mostra i dettagli della regola di migrazione per la regola IssuanceTransform.The example below shows migration rule details for the IssuanceTransform rule. Vengono elencate le parti specifiche dell'attestazione che devono essere esaminate e indirizzate prima di poter eseguire la migrazione dell'applicazione in Azure AD.

   ![Indicazioni aggiuntive sulle regole di migrazioneMigration rule details additional guidance](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Test delle regole attestazione

Nella tabella seguente sono elencati tutti i test delle regole attestazioni eseguiti nelle applicazioni ADFS.

|Proprietà  |Descrizione  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | L'istruzione condition utilizza espressioni regolari per valutare se l'attestazione corrisponde a un determinato modello.Per ottenere una funzionalità simile in Azure AD, è possibile usare la trasformazione predefinita, ad esempio IfEmpty(), StartWith(), Contains(), tra gli altri. Per ulteriori informazioni, consultate [Personalizzare le attestazioni emesse nel token SAML per](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)le applicazioni aziendali.          |
|UNSUPPORTED_CONDITION_CLASS      | L'istruzione condition ha più condizioni che devono essere valutate prima di eseguire l'istruzione di rilascio.Azure AD può supportare questa funzionalità con le funzioni di trasformazione dell'attestazione in cui è possibile valutare più valori di attestazione.Per ulteriori informazioni, consultate [Personalizzare le attestazioni emesse nel token SAML per](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)le applicazioni aziendali.          |
|UNSUPPORTED_RULE_TYPE      | Impossibile riconoscere la regola attestazione. Per altre informazioni su come configurare le attestazioni in Azure AD, vedere [Personalizzare le attestazioni emesse nel token SAML per](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)le applicazioni aziendali.          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | L'istruzione condition usa un'autorità emittente non supportata in Azure AD.Attualmente, Azure AD non fornisce attestazioni da archivi diversi da Active Directory o Azure AD. Se questo impedisce la migrazione delle applicazioni ad Azure AD, [comunicacelo](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).         |
|UNSUPPORTED_CONDITION_FUNCTION      | L'istruzione condition utilizza una funzione di aggregazione per emettere o aggiungere una singola attestazione indipendentemente dal numero di corrispondenze.In Azure AD è possibile valutare l'attributo di un utente per decidere quale valore usare per l'attestazione con funzioni come IfEmpty(), StartWith(), Contains(), tra gli altri.Per ulteriori informazioni, consultate [Personalizzare le attestazioni emesse nel token SAML per](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)le applicazioni aziendali.          |
|RESTRICTED_CLAIM_ISSUED      | L'istruzione condition usa un'attestazione limitata in Azure AD. Potresti essere in grado di emettere un'attestazione con restrizioni, ma non puoi modificarne l'origine o applicare alcuna trasformazione. Per altre informazioni, vedere [Personalizzare le attestazioni generate nei token per un'app specifica in Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)          |
|EXTERNAL_ATTRIBUTE_STORE      | L'istruzione di rilascio utilizza un archivio di attributi diverso da quello di Active Directory. Attualmente, Azure AD non fornisce attestazioni da archivi diversi da Active Directory o Azure AD. Se questo risultato impedisce la migrazione delle applicazioni ad Azure AD, [comunicacelo](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | L'istruzione di rilascio utilizza ADD per aggiungere attestazioni al set di attestazioni in ingresso. In Azure AD può essere configurato come trasformazioni di attestazioni multiple.Per ulteriori informazioni, consultate [Personalizzare le attestazioni emesse nel token SAML per](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping)le applicazioni aziendali.         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | L'istruzione di rilascio utilizza espressioni regolari per trasformare il valore dell'attestazione da generare.Per ottenere funzionalità simili in Azure AD, è possibile usare la trasformazione predefinita, ad esempio Extract(), Trim(), ToLower, tra gli altri. Per ulteriori informazioni, consultate [Personalizzare le attestazioni emesse nel token SAML per](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)le applicazioni aziendali.          |


## <a name="next-steps"></a>Passaggi successivi

- [Video: Come usare il report attività di ADFS per eseguire la migrazione di un'applicazione](https://www.youtube.com/watch?v=OThlTA239lU)
- [Gestione delle applicazioni con Azure Active DirectoryManaging applications with Azure Active Directory](what-is-application-management.md)
- [Manage access to apps](what-is-access-management.md) (Gestire l'accesso alle app)
- [Azure AD Connect e federazione](../hybrid/how-to-connect-fed-whatis.md)
