---
title: Nuova esperienza Registrazioni app in Azure AD B2C
description: Introduzione alla nuova esperienza di registrazione delle app in Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 05/25/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eed0527b69dcaacd3a8cd0cf7cd178aa2aca3468
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89433909"
---
# <a name="the-new-app-registrations-experience-for-azure-active-directory-b2c"></a>La nuova esperienza Registrazioni app per Azure Active Directory B2C

La nuova esperienza di **[registrazioni app](https://aka.ms/b2cappregistrations)** per Azure Active Directory B2C (Azure ad B2C) è ora disponibile a livello generale. Se si ha familiarità con l'esperienza delle **applicazioni** per la registrazione di applicazioni per Azure ad B2C, in questa guida viene illustrata la procedura per iniziare a usare la nuova esperienza.

## <a name="overview"></a>Panoramica
In precedenza, era necessario gestire le applicazioni rivolte agli utenti Azure AD B2C separatamente dalle altre app usando l'esperienza legacy. Ciò significava diverse esperienze di creazione di app in posizioni diverse in Azure.

La nuova esperienza Mostra tutte le registrazioni di app Azure AD B2C e Azure AD registrazioni di app in un'unica posizione e fornisce un modo coerente per gestirle. Dalla creazione di un'app destinata ai clienti alla gestione di un'app con Microsoft Graph autorizzazioni per la gestione delle risorse, è sufficiente apprendere solo un modo per eseguire operazioni.

È possibile raggiungere la nuova esperienza passando a **registrazioni app** in un tenant di Azure AD B2C dal **Azure ad B2C** o dai servizi **Azure Active Directory** nella portale di Azure.

L'esperienza di Azure AD B2C Registrazioni app si basa sull' [esperienza di registrazione delle app](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) generale per qualsiasi tenant Azure ad, ma è adattata per Azure ad B2C tenant.

## <a name="whats-not-changing"></a>Cosa non cambia?
- Le applicazioni e le configurazioni correlate possono essere trovate così come sono nella nuova esperienza. Non è necessario registrare di nuovo le applicazioni e gli utenti delle applicazioni non dovranno eseguire di nuovo l'accesso.

> [!NOTE]
> Per visualizzare tutte le applicazioni create in precedenza, passare al pannello **registrazioni app** e selezionare la scheda **tutte le applicazioni** . Verranno visualizzate le app create nell'esperienza legacy, la nuova esperienza e quelle create nel servizio Azure AD.

## <a name="key-new-features"></a>Nuove funzionalità principali

-   Un **elenco di app unificate** Mostra tutte le applicazioni che eseguono l'autenticazione con Azure AD B2C e Azure ad in un'unica posizione. Inoltre, è possibile sfruttare le funzionalità già disponibili per Azure AD applicazioni, tra cui la data di **creazione** , i **certificati &** lo stato dei segreti, la barra di ricerca e molto altro ancora.

-   La **registrazione combinata delle app** consente di registrare rapidamente un'app, che si tratti di un'app per il cliente o di un'app per accedere Microsoft Graph.

- Il riquadro **endpoint** consente di identificare rapidamente gli endpoint pertinenti per lo scenario, tra cui la configurazione di OpenID Connect, i metadati SAML, l'API Microsoft Graph e gli [endpoint del flusso utente OAuth 2,0](tokens-overview.md#endpoints).

- Le **autorizzazioni API** ed **esporre un'API forniscono la** gestione di ambito, autorizzazioni e consenso più ampia. È ora possibile assegnare anche le autorizzazioni di Microsoft Graph e Azure AD Graph a un'app.

-   I **proprietari** e i **manifesti** sono ora disponibili per le app che eseguono l'autenticazione con Azure ad B2C. È possibile aggiungere proprietari per le registrazioni e modificare direttamente le proprietà dell'applicazione [mediante l'editor manifesto](../active-directory/develop/reference-app-manifest.md).


## <a name="new-supported-account-types"></a>Nuovi tipi di account supportati

Nella nuova esperienza è possibile selezionare un tipo di account di supporto dalle opzioni seguenti:
- Solo gli account in questa directory aziendale.
- Account in qualsiasi directory organizzativa (qualsiasi Azure AD directory – multi-tenant).
- Account in qualsiasi directory organizzativa o provider di identità. Per l'autenticazione degli utenti con Azure AD B2C.

Per comprendere i diversi tipi di account, selezionare **help me choose** in the Creation Experience.

Nell'esperienza legacy, le app sono state create sempre come applicazioni rivolte ai clienti. Per queste app, il tipo di account è impostato su **account in qualsiasi directory organizzativa o provider di identità. Per l'autenticazione degli utenti con Azure AD B2C**.
> [!NOTE]
> Questa opzione è necessaria per poter eseguire Azure AD B2C flussi utente per autenticare gli utenti per questa applicazione. Informazioni [su come registrare un'applicazione per l'uso con i flussi utente.](tutorial-register-applications.md)

È anche possibile usare questa opzione per usare Azure AD B2C come provider di servizi SAML. [Altre informazioni](identity-provider-adfs2016-custom.md)

## <a name="applications-for-devops-scenarios"></a>Applicazioni per scenari DevOps
È possibile usare gli altri tipi di account per creare un'app per gestire gli scenari DevOps, ad esempio l'uso di Microsoft Graph per caricare i criteri del Framework dell'esperienza di identità o il provisioning degli utenti. Informazioni [su come registrare un'applicazione Microsoft Graph per gestire le risorse Azure ad B2C](microsoft-graph-get-started.md).

È possibile che non vengano visualizzate tutte le autorizzazioni Microsoft Graph, perché molte di queste autorizzazioni non si applicano agli utenti del consumer di Azure B2C. [Altre informazioni sulla gestione degli utenti con Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="admin-consent-and-offline_accessopenid-scopes"></a>Consenso dell'amministratore e offline_access + ambiti OpenID
<!-- Azure AD B2C doesn't support user consent. That is, when a user signs into an application, the user doesn't see a screen requesting consent for the application permissions. All permissions have to be granted through admin consent.  -->

L'ambito **OpenID** è necessario in modo che Azure ad B2C possa consentire agli utenti di accedere a un'app. L'ambito **offline_access** è necessario per emettere token di aggiornamento per un utente. Questi ambiti sono stati aggiunti in precedenza e il consenso dell'amministratore è stato specificato per impostazione predefinita. A questo punto, è possibile aggiungere facilmente le autorizzazioni per questi ambiti durante il processo di creazione assicurando che sia selezionata l'opzione **concedi il consenso dell'amministratore per OpenID e offline_access autorizzazioni** . In caso contrario, è possibile aggiungere le autorizzazioni di Microsoft Graph con il consenso dell'amministratore nelle impostazioni delle **autorizzazioni API** per un'app esistente.

Altre informazioni sulle [autorizzazioni e il consenso](../active-directory/develop/v2-permissions-and-consent.md).

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Piattaforme/autenticazione: URL di risposta/URI di Reindirizzamento
Nell'esperienza legacy, i vari tipi di piattaforma sono stati gestiti in **Proprietà** come URL di risposta per le app Web/API e l'URI di reindirizzamento per i client nativi. "Native clients" sono noti anche come "client pubblici" e includono app per iOS, macOS, Android e altri tipi di applicazioni per dispositivi mobili e desktop.

Nella nuova esperienza gli URL di risposta e gli URI di reindirizzamento sono entrambi denominati URI di reindirizzamento ed è possibile trovarli nella sezione di **autenticazione** di un'app. Registrazioni app non sono limitati a essere un'app Web o un'applicazione nativa. È possibile usare la stessa registrazione dell'app per tutti questi tipi di piattaforma registrando i rispettivi URI di reindirizzamento.

Gli URI di reindirizzamento devono essere associati a un tipo di app, ovvero Web o pubblico (per dispositivi mobili e desktop). [Altre informazioni sugli URI di Reindirizzamento](../active-directory/develop/quickstart-register-app.md#add-a-redirect-uri)

<!-- Whether an application should be treated as a public client is inferred at run-time from the Redirect URI platform type, if possible. The **Treat application as a public client** setting should be set to **Yes** for flows that might not use a redirect URI, such as ROPC flows. -->

Le piattaforme **iOS/MacOS** e **Android** sono un tipo di client pubblico. Forniscono un modo semplice per configurare app iOS/macOS o Android con URI di reindirizzamento corrispondenti da usare con MSAL. Altre informazioni sulle [Opzioni di configurazione dell'applicazione](../active-directory/develop/msal-client-applications.md).


## <a name="application-certificates--secrets"></a>Certificati dell'applicazione & segreti

Nella nuova esperienza, anziché **chiavi**, viene usato il pannello **certificati & segreti** per gestire i certificati e i segreti. I certificati & segreti consentono alle applicazioni di identificarsi nel servizio di autenticazione quando ricevono i token in una posizione indirizzabile Web (usando uno schema HTTPS). È consigliabile usare un certificato anziché un segreto client per gli scenari di credenziali client quando si esegue l'autenticazione con Azure AD. Non è possibile usare i certificati per l'autenticazione con Azure AD B2C.


## <a name="features-not-applicable-in-azure-ad-b2c-tenants"></a>Funzionalità non applicabili nei tenant Azure AD B2C
Le seguenti funzionalità di registrazione delle app di Azure AD non sono applicabili o disponibili in Azure AD B2C tenant:
- **Ruoli e amministratori** : è necessaria una licenza Azure ad Premium P1 o P2 che non è attualmente disponibile per Azure ad B2C.
- **Personalizzazione: la** personalizzazione dell'interfaccia utente/UX è configurata nell'esperienza di **personalizzazione dell'azienda** o come parte di un flusso utente. Informazioni su come [personalizzare l'interfaccia utente in Azure Active Directory B2C](customize-ui-overview.md).
- **Verifica del dominio dell'editore** : l'app è registrata in *. onmicrosoft.com*, che non è un dominio verificato. Il dominio di pubblicazione viene inoltre usato principalmente per concedere il consenso dell'utente, che non si applica alle app Azure AD B2C per l'autenticazione utente. Ulteriori informazioni [sul dominio di pubblicazione](https://docs.microsoft.com/azure/active-directory/develop/howto-configure-publisher-domain).
- **Configurazione del token** : il token è configurato come parte di un flusso utente anziché un'app.
- L'esperienza delle **guide introduttive** non è attualmente disponibile per Azure ad B2C tenant.
- Il pannello **Integration Assistant** non è attualmente disponibile per Azure ad B2C tenant.


## <a name="limitations"></a>Limitazioni
La nuova esperienza presenta le limitazioni seguenti:
- A questo punto, Azure AD B2C non distingue tra la possibilità di emettere token di accesso o ID per i flussi impliciti; entrambi i tipi di token sono disponibili per il flusso di concessione implicita se l'opzione **token ID** è selezionata nel pannello **autenticazione** .
<!-- - Azure AD B2C doesn't currently support the single-page application "SPA" app type.  -->
- La modifica del valore per gli account supportati non è supportata nell'interfaccia utente. È necessario usare il manifesto dell'app, a meno che non si stia passando tra Azure AD single-tenant e multi-tenant.

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare la nuova esperienza di registrazione delle app:
* Informazioni [su come registrare un'applicazione Web](tutorial-register-applications.md).
* Informazioni [su come registrare un'API Web](add-web-api-application.md).
* Informazioni [su come registrare un'applicazione client nativa](add-native-application.md).
* Informazioni [su come registrare un'applicazione Microsoft Graph per gestire le risorse Azure ad B2C](microsoft-graph-get-started.md).
* Informazioni [su come usare Azure ad B2C come provider di servizi SAML.](identity-provider-adfs2016-custom.md)
* Informazioni sui [tipi di applicazioni](application-types.md).
