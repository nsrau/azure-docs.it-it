---
title: Usare il report attività per spostare le app AD FS in Azure Active Directory | Microsoft Docs '
description: Il report attività applicazione Active Directory Federation Services (AD FS) consente di eseguire rapidamente la migrazione delle applicazioni da AD FS a Azure Active Directory (Azure AD). Questo strumento di migrazione per AD FS identifica la compatibilità con Azure AD e fornisce indicazioni sulla migrazione.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/30/2019
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10a267811b7e7ac8715b6823a24c2b3a1f0d430c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73180537"
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
   * [Altre informazioni su Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs)
   * [Introduzione alla configurazione di Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-agent-install)

## <a name="discover-ad-fs-applications-that-can-be-migrated"></a>Individuare AD FS applicazioni di cui è possibile eseguire la migrazione 

Il report attività dell'applicazione AD FS è disponibile nella portale di Azure sotto Azure AD **utilizzo & report di Insights** . Il report attività applicazione AD FS analizza ogni applicazione AD FS per determinare se è possibile eseguirne la migrazione così come sono o se è necessaria una revisione aggiuntiva. 

1. Accedere al [portale di Azure](https://portal.azure.com) con un ruolo di amministratore che abbia accesso ai dati delle attività dell'applicazione ad FS (amministratore globale, lettore di report, lettore di sicurezza, amministratore dell'applicazione o amministratore di applicazioni cloud).

2. Selezionare **Azure Active Directory**, quindi selezionare **applicazioni aziendali**.

3. In **attività**selezionare **utilizzo & Insights (anteprima)** e quindi selezionare **ad FS attività dell'applicazione** per aprire un elenco di tutte le applicazioni ad FS nell'organizzazione.

   ![Attività AD FS applicazione](media/migrate-adfs-application-activity/adfs-application-activity.png)

4. Per ogni applicazione nell'elenco attività AD FS applicazione, visualizzare lo **stato di migrazione**:

   * **Pronto per la migrazione** significa che la configurazione dell'applicazione ad FS è completamente supportata in Azure ad ed è possibile eseguirne la migrazione così come sono.

   * **Verifica delle esigenze** significa che è possibile eseguire la migrazione di alcune impostazioni dell'applicazione in Azure ad, ma è necessario rivedere le impostazioni di cui non è possibile eseguire la migrazione così come sono.

   * **Passaggi aggiuntivi necessari** significa che Azure ad non supporta alcune delle impostazioni dell'applicazione, pertanto non è possibile eseguire la migrazione dell'applicazione nello stato corrente.

## <a name="evaluate-the-readiness-of-an-application-for-migration"></a>Valutare la conformità di un'applicazione per la migrazione 

1. Nell'elenco attività dell'applicazione AD FS fare clic sullo stato nella colonna **stato migrazione** per aprire i dettagli della migrazione. Verrà visualizzato un riepilogo dei test di configurazione passati, insieme a eventuali problemi di migrazione potenziali.

   ![Dettagli della migrazione](media/migrate-adfs-application-activity/migration-details.png)

2. Fare clic su un messaggio per aprire i dettagli aggiuntivi della regola di migrazione. Per un elenco completo delle proprietà sottoposte a test, vedere la tabella dei [test di configurazione dell'applicazione ad FS](#ad-fs-application-configuration-tests) riportata di seguito.

   ![Dettagli regola di migrazione](media/migrate-adfs-application-activity/migration-rule-details.png)

### <a name="ad-fs-application-configuration-tests"></a>Test di configurazione dell'applicazione AD FS

Nella tabella seguente sono elencati tutti i test di configurazione eseguiti su AD FS applicazioni.

|Proprietà  |Status  |Description  |
|---------|---------|---------|
|È stata rilevata almeno una regola non migrazione nei per AdditionalAuthentication.       | Pass/avviso          | Il relying party in AD FS usa un provider di autenticazione a più fattori locale. Per passare alla Azure AD, è consigliabile passare all'autenticazione a più fattori di Azure o ai controlli personalizzati con il provider di autenticazione a più fattori di terze parti.  [Altre informazioni sull'autenticazione a più fattori di Azure](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).        |
|Il valore di AdditionalWSFedEndpoint del componente è impostato su true.       | Esito positivo o negativo          | Il relying party in AD FS consente più endpoint di asserzione WS-Fed. Azure AD supporta solo (1) uno di questi giorni. Se si dispone di uno scenario in cui questo blocca la migrazione [, segnalare](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695621-allow-multiple-ws-fed-assertion-endpoints)il problema.     |
|Il componente ha impostato AllowedAuthenticationClassReferences.       | Esito positivo o negativo          | Si tratta di un'impostazione in AD FS che consente di specificare se l'applicazione è configurata in modo da consentire solo determinati tipi di autenticazione. Azure AD attualmente non è supportato. Se si dispone di uno scenario in cui questo blocca la migrazione [, segnalare](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695672-allow-in-azure-ad-to-specify-certain-authentication)il problema.          |
|AlwaysRequireAuthentication è impostato.      | Esito positivo o negativo          | Indica se l'applicazione è configurata in modo da ignorare i cookie SSO e richiedere sempre l'autenticazione. Attualmente non supportato da Azure AD.          |
|Il valore di AutoUpdateEnabled del componente è impostato su true       | Pass/avviso          | Si tratta di un'impostazione in AD FS che consente di specificare se AD FS è configurato per l'aggiornamento automatico dell'applicazione in base alle modifiche apportate all'interno dei metadati di Federazione. Azure AD attualmente non supporta questa operazione, ma non deve bloccare la migrazione dell'applicazione a Azure AD.           |
|Per la relying party sono abilitati più ClaimsProviders       | Esito positivo o negativo          | Il relying party è configurato per l'origine delle attestazioni da un provider di attestazioni diverso da Active Directory.  Questa operazione non è supportata in Azure AD. Se si dispone di uno scenario in cui questo blocca la migrazione [, segnalare](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire)il problema.          |
|DelegationAuthorization è valido      | Esito positivo o negativo          | Per l'applicazione sono definite regole di autorizzazione di delega personalizzate. Azure AD attualmente non è supportato. Se si dispone di uno scenario in cui questo blocca la migrazione [, segnalare](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695747-allow-to-define-delegation-authorization-rules)il problema.          |
|Il componente è ADFSRPImpersonationAuthorizationRules       | Pass/avviso          | Per l'applicazione sono definite regole di autorizzazione di rappresentazione personalizzate. Azure AD attualmente non supporta questa operazione, ma non deve bloccare la migrazione dell'applicazione a Azure AD.          |
|È stata rilevata almeno una regola non migrazione nei per IssuanceAuthorization.       | Pass/avviso          | L'applicazione dispone di regole di autorizzazione di rilascio personalizzate definite in AD FS. Azure AD supporta questa funzionalità con Azure AD l'accesso condizionale. [Altre informazioni sull'accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/overview). Inoltre, in Azure AD è possibile limitare l'accesso all'applicazione da parte dell'utente o dei gruppi assegnati all'applicazione. [Altre informazioni sull'assegnazione di utenti e gruppi per l'accesso alle applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps/methods-for-assigning-users-and-groups). Per informazioni sulle regole IssuanceAuthorization non elaborate configurate in AD FS, vedere la tabella dei test delle regole [attestazioni](#check-the-results-of-claim-rule-tests)riportata di seguito.           |
|È stata rilevata almeno una regola non migrazione nei per IssuanceTransform.       | Pass/avviso          | L'applicazione dispone di regole di trasformazione rilascio personalizzate definite in AD FS. Azure AD supporta la personalizzazione delle attestazioni rilasciate nel token. Per altre informazioni, vedere [personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).  Per informazioni sulle regole IssuanceTransformation non elaborate configurate in AD FS, vedere la tabella dei test delle regole [attestazioni](#check-the-results-of-claim-rule-tests)riportata di seguito.          |
|Il valore di MonitoringEnabled del componente è impostato su true.       | Pass/avviso          | Si tratta di un'impostazione in AD FS che consente di specificare se AD FS è configurato per monitorare i metadati di una federazione per questa applicazione. Questa operazione non è supportata in Azure AD ma non deve bloccare la migrazione dell'applicazione a Azure AD.          |
|Il componente NotBeforeSkew è configurato.      | Pass/avviso          | AD FS consente uno sfasamento dell'ora in base ai tempi di NotBefore e NotOnOrAfter nel token SAML. Azure AD attualmente non supporta questa operazione, ma non deve bloccare la migrazione dell'applicazione a Azure AD.          |
|Il valore di RequestMFAFromClaimsProviders del componente è impostato su true.       | Pass/avviso          | Si tratta di un'impostazione in AD FS che consente di specificare se l'applicazione è hardcoded a un altro provider di attestazioni e richiede l'autenticazione a più fattori. Per passare alla Azure AD, è consigliabile passare all'integrazione con autenticazione a più fattori di Azure o ai controlli personalizzati con un provider di autenticazione a più fattori di terze parti.  [Altre informazioni sull'autenticazione a più fattori di Azure](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).          |
|Il valore di SignedSamlRequestsRequired del componente è impostato su true       | Esito positivo o negativo          | L'applicazione è configurata in AD FS per verificare la firma nella richiesta SAML. Si tratta di un'impostazione facoltativa e non deve bloccare la migrazione. Azure Active Directory usa gli URL di risposta per convalidare il provider di servizi.  Se si dispone di uno scenario in cui questo blocca la migrazione [, segnalare](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/13394589-saml-signature)il problema.          |
|TokenLifetimeCheckResult        | Esito positivo o negativo         | L'applicazione viene configurata per la durata di un token personalizzato. AD FS valore predefinito è 1 ora. Azure AD supporta questa funzionalità usando l'accesso condizionale. Per altre informazioni, vedere [configurare la gestione delle sessioni di autenticazione con l'accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).          |
|Il componente è impostato per la crittografia delle attestazioni. Questa operazione è supportata da Azure AD       | Pass          | Con Azure AD è possibile crittografare l'invio del token all'applicazione. Per altre informazioni, vedere [configurare Azure ad crittografia di token SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).          |
|Il componente è impostato per crittografare NameID nel token SAML.      | Esito positivo o negativo          | L'applicazione è configurata per crittografare l'attestazione nameID nel token SAML. Con Azure AD è possibile crittografare l'intero token inviato all'applicazione. La crittografia di attestazioni specifiche non è ancora supportata. Per altre informazioni, vedere [configurare Azure ad crittografia di token SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).         |

## <a name="check-the-results-of-claim-rule-tests"></a>Verificare i risultati dei test delle regole attestazioni

Se è stata configurata una regola attestazioni per l'applicazione in AD FS, l'esperienza fornirà un'analisi granulare per tutte le regole attestazioni. Verranno visualizzate le regole attestazioni che possono essere spostate Azure AD e quelle che necessitano di ulteriori verifiche.

1. Nell'elenco attività dell'applicazione AD FS fare clic sullo stato nella colonna **stato migrazione** per aprire i dettagli della migrazione. Verrà visualizzato un riepilogo dei test di configurazione passati, insieme a eventuali problemi di migrazione potenziali.

2. Nella pagina **Dettagli regola di migrazione** espandere i risultati per visualizzare i dettagli sui potenziali problemi di migrazione e ottenere altre informazioni aggiuntive. Per un elenco dettagliato di tutte le regole attestazioni testate, vedere la tabella [verificare i risultati dei test delle regole attestazioni](#check-the-results-of-claim-rule-tests) , più avanti.

   Nell'esempio seguente vengono illustrati i dettagli della regola di migrazione per la regola IssuanceTransform. Elenca le parti specifiche dell'attestazione che devono essere esaminate e risolte prima di poter eseguire la migrazione dell'applicazione a Azure AD.

   ![Informazioni aggiuntive sulla regola di migrazione](media/migrate-adfs-application-activity/migration-rule-details-guidance.png)

### <a name="claim-rule-tests"></a>Test delle regole attestazioni

Nella tabella seguente sono elencati tutti i test delle regole attestazioni eseguiti su AD FS applicazioni.

|Proprietà  |Description  |
|---------|---------|
|UNSUPPORTED_CONDITION_PARAMETER      | L'istruzione Condition usa espressioni regolari per valutare se l'attestazione corrisponde a un determinato modello.  Per ottenere una funzionalità simile in Azure AD, è possibile usare tra gli altri la trasformazione predefinita, ad esempio IfEmpty (), cominciamo (), Contains (). Per altre informazioni, vedere [personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_CONDITION_CLASS      | L'istruzione Condition presenta più condizioni che devono essere valutate prima di eseguire l'istruzione di rilascio. Azure AD possibile supportare questa funzionalità con le funzioni di trasformazione dell'attestazione in cui è possibile valutare più valori di attestazione.  Per altre informazioni, vedere [personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|UNSUPPORTED_RULE_TYPE      | Non è stato possibile riconoscere la regola attestazioni. Per altre informazioni su come configurare le attestazioni in Azure AD, vedere [personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|CONDITION_MATCHES_UNSUPPORTED_ISSUER      | L'istruzione Condition usa un'autorità emittente non supportata in Azure AD. Attualmente, Azure AD non prevede l'origine di attestazioni da archivi diversi da Active Directory o Azure AD. Se questa operazione impedisce la migrazione di applicazioni a Azure AD, è possibile [inviarle informazioni](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).         |
|UNSUPPORTED_CONDITION_FUNCTION      | L'istruzione Condition utilizza una funzione di aggregazione per emettere o aggiungere una singola attestazione indipendentemente dal numero di corrispondenze.  In Azure AD è possibile valutare l'attributo di un utente per decidere quale valore usare per l'attestazione con funzioni come IfEmpty (), cominciamo (), Contains (), tra gli altri. Per altre informazioni, vedere [personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |
|RESTRICTED_CLAIM_ISSUED      | L'istruzione Condition usa un'attestazione limitata in Azure AD. Potrebbe essere possibile emettere un'attestazione con restrizioni, ma non è possibile modificarne l'origine o applicare una trasformazione. Per altre informazioni, vedere [personalizzare le attestazioni emesse nei token per un'app specifica in Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).          |
|EXTERNAL_ATTRIBUTE_STORE      | L'istruzione di rilascio usa un archivio di attributi diverso da quello Active Directory. Attualmente, Azure AD non prevede l'origine di attestazioni da archivi diversi da Active Directory o Azure AD. Se questa operazione impedisce la migrazione di applicazioni a Azure AD, è possibile [inviarle informazioni](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/38695717-allow-to-source-user-attributes-from-external-dire).          |
|UNSUPPORTED_ISSUANCE_CLASS      | L'istruzione di rilascio USA Aggiungi per aggiungere attestazioni al set di attestazioni in ingresso. In Azure AD questa operazione può essere configurata come più trasformazioni di attestazioni.  Per altre informazioni, vedere [personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).         |
|UNSUPPORTED_ISSUANCE_TRANSFORMATION      | L'istruzione di rilascio usa espressioni regolari per trasformare il valore dell'attestazione da emettere. Per ottenere una funzionalità simile in Azure AD, è possibile usare una trasformazione predefinita, ad esempio Extract (), Trim (), ToLower, tra gli altri. Per altre informazioni, vedere [personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).          |


## <a name="next-steps"></a>Passaggi successivi

- [Gestione di applicazioni con Azure Active Directory](what-is-application-management.md)
- [Gestire l'accesso alle app](what-is-access-management.md)
- [Azure AD Connect e federazione](../hybrid/how-to-connect-fed-whatis.md)
