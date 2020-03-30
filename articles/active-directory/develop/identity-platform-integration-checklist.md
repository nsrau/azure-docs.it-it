---
title: Procedure consigliate per la piattaforma di identità Microsoft Azure
description: Scopri le best practice, i consigli e le sviste comuni durante l'integrazione con la piattaforma di identità Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 56975cebbfe4f6dd6452c850c338d431faea27bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050490"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Procedure consigliate e consigli per la piattaforma di identità Microsoft

In questo articolo vengono evidenziate le procedure consigliate, i consigli e le sviste comuni durante l'integrazione con la piattaforma di identità Microsoft.This article highlights best practices, recommendations, and common oversights when integrating with the Microsoft identity platform.  Questo elenco di controllo ti guiderà verso un'integrazione sicura e di alta qualità. Esamina regolarmente questo elenco per assicurarti di mantenere la qualità e la sicurezza dell'integrazione della tua app con la piattaforma di identità. L'elenco di controllo non è destinato a esaminare l'intera applicazione. I contenuti dell'elenco di controllo sono soggetti a modifiche man mano che apportiamo miglioramenti alla piattaforma.

Se hai appena iniziato, consulta la [documentazione](index.yml) della piattaforma Microsoft identity per informazioni sulle nozioni di base sull'autenticazione, sugli scenari di applicazioni nella piattaforma Microsoft Identity e altro ancora.

Utilizzare l'elenco di controllo seguente per assicurarsi che l'applicazione sia effettivamente integrata con la piattaforma di [identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).

## <a name="basics"></a>Nozioni di base

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Leggere e comprendere i [criteri della piattaforma Microsoft](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Assicurati che l'applicazione aderisca ai termini descritti mentre sono progettati per proteggere gli utenti e la piattaforma. |

## <a name="ownership"></a>Proprietario

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Assicurati che le informazioni associate all'account utilizzato per registrare e gestire le app siano aggiornate. |

## <a name="branding"></a>Personalizzazione

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Rispettare le [linee guida di branding per](howto-add-branding-in-azure-ad-apps.md)le applicazioni . |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Specificare un nome e un logo significativi per l'applicazione. Queste informazioni vengono visualizzate nella [richiesta di consenso dell'applicazione.](application-consent-experience.md) Assicurati che il tuo nome e il tuo logo siano rappresentativi della tua azienda/prodotto in modo che gli utenti possano prendere decisioni informate. Assicurati di non violare alcun marchio. |

## <a name="privacy"></a>Privacy

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Fornisci collegamenti alle condizioni per l'assistenza e l'informativa sulla privacy della tua app. |

## <a name="security"></a>Security

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Gestire gli URI di reindirizzamento: <ul><li>Mantenere aggiornata la proprietà di tutti gli URI di reindirizzamento e mantenere aggiornati i record DNS.</li><li>Non usare i caratteri jolly (-) negli URI.</li><li>Per le app Web, assicurati che tutti gli URI siano protetti e crittografati (ad esempio, usando gli schemi https).</li><li>Per i client pubblici, usare URI di reindirizzamento specifici della piattaforma, se applicabile (principalmente per iOS e Android). In caso contrario, usa gli URI di reindirizzamento con un'elevata quantità di casualità per evitare conflitti quando richiami all'app.</li><li>Se l'app viene usata da un `https://login.microsoftonline.com/common/oauth2/nativeclient`agente Web isolato, è possibile utilizzare .</li><li>Rivedere e tagliare regolarmente tutti gli URI di reindirizzamento inutilizzati o non necessari.</li></ul> |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Se l'app è registrata in una directory, riduci a icona e monitora manualmente l'elenco dei proprietari di registrazione dell'app. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Non abilitare il supporto per il flusso di [concessione implicita OAuth2](v2-oauth2-implicit-grant-flow.md) a meno che non sia esplicitamente richiesto. Per informazioni sullo scenario [valido, fare](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant)clic qui. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Vai oltre il nome utente / password. Non utilizzare il flusso di credenziali delle password del proprietario della [risorsa (ROPC),](v2-oauth-ropc.md)che gestisce direttamente le password degli utenti. Questo flusso richiede un elevato grado di attendibilità e l'esposizione dell'utente e deve essere utilizzato solo quando non è possibile usare altri flussi più sicuri. Questo flusso è ancora necessario in alcuni scenari (ad esempio, DevOps), ma tenere presente che l'uso di questa funzione imporrà vincoli sull'applicazione.  Per approcci più moderni, leggere Flussi di [autenticazione e scenari applicativi](authentication-flows-app-scenarios.md).|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Proteggi e gestisci le credenziali riservate delle app per app Web, API Web e app daemon. Utilizzare [le credenziali del certificato,](active-directory-certificate-credentials.md)non le credenziali delle password (segreti client). Se è necessario utilizzare una credenziale password, non impostarla manualmente. Non archiviare le credenziali nel codice o nella configurazione e non consentirne mai la gestione da parte degli esseri umani. Se possibile, usare [le identità gestite per le risorse](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) di Azure o l'insieme di credenziali delle chiavi di [Azure](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) per archiviare e ruotare regolarmente le credenziali. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Assicurarsi che l'applicazione richieda le autorizzazioni con privilegi minimi. Richiedi solo le autorizzazioni di cui l'applicazione ha assolutamente bisogno e solo quando ne hai bisogno. Comprendere i diversi [tipi di autorizzazioni](v2-permissions-and-consent.md#permission-types). Utilizzare le autorizzazioni dell'applicazione solo se necessario; se possibile, utilizzare le autorizzazioni delegate. Per un elenco completo delle autorizzazioni di Microsoft Graph, vedere questo riferimento alle [autorizzazioni](https://docs.microsoft.com/graph/permissions-reference). |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Se stai proteggendo un'API usando la piattaforma di identità Microsoft, valuta attentamente le autorizzazioni che dovrebbe esporre. Considerare qual è la granularità corretta per la soluzione e quali autorizzazioni richiedono il consenso dell'amministratore. Controllare le autorizzazioni previste nei token in ingresso prima di prendere decisioni di autorizzazione. |

## <a name="implementation"></a>Implementazione

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Utilizzare soluzioni di autenticazione moderne (OAuth 2.0, [OpenID Connect](v2-protocols-oidc.md)) per accedere in modo sicuro agli utenti. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) |  Non programmare direttamente in base a protocolli come OAuth 2.0 e Open ID. Utilizzare invece [Microsoft Authentication Library (MSAL)](msal-overview.md). Le librerie MSAL eseguono il wrapping in modo sicuro dei protocolli di sicurezza in una libreria di facile utilizzo e si ottiene il supporto incorporato per gli scenari di [accesso condizionale,](/azure/active-directory/conditional-access/overview) [l'accesso Single Sign-On (SSO)](/azure/active-directory/manage-apps/what-is-single-sign-on)a livello di dispositivo e il supporto incorporato per la memorizzazione nella cache dei token. Per ulteriori informazioni, vedere l'elenco delle [librerie client](reference-v2-libraries.md#microsoft-supported-client-libraries) supportate da Microsoft e delle [librerie middleware](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) e l'elenco delle librerie client di [terze parti compatibili.](reference-v2-libraries.md#compatible-client-libraries)<br/><br/>Se è necessario eseguire il codice per i protocolli di autenticazione, è necessario seguire una metodologia come [Microsoft SDL](https://www.microsoft.com/sdl/default.aspx). Prestare particolare attenzione alle considerazioni sulla sicurezza nelle specifiche degli standard per ogni protocollo.|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) |  Eseguire la migrazione delle app esistenti da [Azure Active Directory Authentication Library (ADAL)](../azuread-dev/active-directory-authentication-libraries.md) a Microsoft Authentication [Library](msal-overview.md). MSAL è l'ultima soluzione di piattaforma di identità di Microsoft ed è preferito ad ADAL. È disponibile su .NET, JavaScript, Android, iOS, macOS ed è anche in anteprima pubblica per Python e Java. Ulteriori informazioni sulla [migrazione](msal-net-migration.md)ADAL.NET, [ADAL.js](msal-compare-msal-js-and-adal-js.md)e [ADAL.NET e iOS broker.](msal-net-migration-ios-broker.md)|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) |  Per le app per dispositivi mobili, configura ogni piattaforma usando l'esperienza di registrazione dell'applicazione. Affinché l'applicazione sfrutti Microsoft Authenticator o Microsoft Company Portal per l'accesso singolo, l'app deve essere configurato con un URI di reindirizzamento broker. Ciò consente a Microsoft di restituire il controllo all'applicazione dopo l'autenticazione. Quando configuri ogni piattaforma, l'esperienza di registrazione dell'app ti guiderà attraverso il processo. Utilizzare la guida introduttiva per scaricare un esempio funzionante. Su iOS, usa i broker e la visualizzazione web del sistema quando possibile.|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) |  Nelle app Web o nelle API Web, mantieni una cache dei token per account.  Per le app Web, la cache dei token deve essere con chiave dall'ID account.  Per le API Web, l'account deve essere con chiave dall'hash del token utilizzato per chiamare l'API. MSAL.NET fornisce la serializzazione della cache dei token personalizzata nelle sottopiattaforme .NET Framework e .NET Core. Per motivi di sicurezza e prestazioni, è consigliabile serializzare una cache per utente. Per ulteriori informazioni, vedere sulla [serializzazione della cache dei token](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application).|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Se i dati necessari per l'app sono disponibili tramite [Microsoft Graph,](https://developer.microsoft.com/graph)richiedere le autorizzazioni per questi dati usando l'endpoint Microsoft Graph anziché la singola API. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) |Non esaminare il valore del token di accesso o tentare di analizzarlo come client.  Possono modificare i valori, i formati o anche diventare crittografati senza preavviso - utilizzare sempre il id_token se il client ha bisogno di imparare qualcosa sull'utente, o chiamare Microsoft Graph.  Solo le API Web devono analizzare i token di accesso (poiché sono quelle che definiscono il formato e impostano le chiavi di crittografia). |

## <a name="end-user-experience"></a>Esperienza utente finale

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | [Comprendere l'esperienza](application-consent-experience.md) di consenso e configurare le parti del prompt di consenso dell'app in modo che gli utenti finali e gli amministratori dispongano di informazioni sufficienti per determinare se considerano attendibile l'app. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Riduci al minimo il numero di volte in cui un utente deve immettere le credenziali di accesso durante l'uso dell'app tentando l'autenticazione invisibile all'utente (acquisizione di token silenziosa) prima dei flussi interattivi. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Non usare "prompt-consent" per ogni accesso. Usa prompt-consent solo se hai stabilito che devi richiedere il consenso per autorizzazioni aggiuntive (ad esempio, se hai modificato le autorizzazioni necessarie per l'app). |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Se applicabile, arricchire l'applicazione con i dati utente. L'uso [dell'API Microsoft Graph](https://developer.microsoft.com/graph) è un modo semplice per eseguire questa operazione. Lo strumento [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) che consente di iniziare. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Registrare il set completo di autorizzazioni richiesto dall'app, in modo che gli amministratori possano concedere facilmente il consenso al tenant. Usa il [consenso incrementale](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) in fase di esecuzione per aiutare gli utenti a capire perché la tua app richiede autorizzazioni che potrebbero riguardare o confondere gli utenti quando richiesto al primo avvio. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementare un'esperienza di [disconnessione singola pulita.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) Si tratta di una privacy e un requisito di sicurezza, e rende per una buona esperienza utente. |

## <a name="testing"></a>Test

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Testare [i criteri di accesso condizionale](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) che possono influire sulla capacità degli utenti di utilizzare l'applicazione. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Testare l'applicazione con tutti i possibili account che si prevede di supportare, ad esempio account aziendali o dell'istituto di istruzione, account Microsoft personali, account per bambini e account sovrani. |

## <a name="additional-resources"></a>Risorse aggiuntive

Approfondimento su v2.0:

* [Piattaforma di identità Microsoft (panoramica v2.0)](v2-overview.md)
* [Informazioni di riferimento sui protocolli della piattaforma di identità Microsoft](active-directory-v2-protocols.md)
* [Riferimento ai token di accesso](access-tokens.md)
* [Informazioni di riferimento sui token ID](id-tokens.md)
* [Informazioni di riferimento sulle librerie di autenticazione](reference-v2-libraries.md)
* [Autorizzazioni e consenso nella piattaforma di identità Microsoft](v2-permissions-and-consent.md)
* [API Microsoft Graph](https://developer.microsoft.com/graph)
