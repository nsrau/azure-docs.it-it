---
title: Procedure consigliate per la piattaforma di identità Microsoft | Azure
description: Informazioni sulle procedure consigliate, sulle raccomandazioni e sulle supervisione comuni per l'integrazione con la piattaforma di identità Microsoft.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/11/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: f7e9b738a55248678a207f0b298ef65e6c2761a4
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72240143"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Procedure consigliate e raccomandazioni per la piattaforma di identità Microsoft

Questo articolo evidenzia le procedure consigliate, le raccomandazioni e le supervisione comuni per l'integrazione con la piattaforma di identità Microsoft.  Questo elenco di controllo ti guiderà a un'integrazione sicura e di alta qualità. Esaminare regolarmente questo elenco per assicurarsi di mantenere la qualità e la sicurezza dell'integrazione dell'app con la piattaforma di identità. L'elenco di controllo non è destinato a esaminare l'intera applicazione. Il contenuto dell'elenco di controllo è soggetto a modifiche apportate per migliorare la piattaforma.

Se si è appena iniziato, consultare la documentazione della [piattaforma di identità Microsoft](index.yml) per informazioni sulle nozioni di base sull'autenticazione, gli scenari applicativi nella piattaforma di identità Microsoft e altro ancora.

Usare il seguente elenco di controllo per assicurarsi che l'applicazione sia integrata efficacemente con la [piattaforma di identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).

## <a name="basics"></a>Nozioni di base

|   |   |
|---|---|
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Leggere e comprendere i [criteri della piattaforma Microsoft](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Assicurarsi che l'applicazione soddisfi le condizioni descritte in quanto sono progettate per proteggere gli utenti e la piattaforma. |

## <a name="ownership"></a>Proprietà

|   |   |
|---|---|
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Assicurarsi che le informazioni associate all'account usato per registrare e gestire le app siano aggiornate. |

## <a name="branding"></a>Personalizzazione

|   |   |
|---|---|
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Rispettare le [linee guida per la personalizzazione delle applicazioni](howto-add-branding-in-azure-ad-apps.md). |
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Fornire un nome e un logo significativi per l'applicazione. Queste informazioni vengono visualizzate nella [richiesta di consenso dell'applicazione](application-consent-experience.md). Verificare che il nome e il logo siano rappresentativi della società o del prodotto, in modo che gli utenti possano prendere decisioni informate. Assicurarsi che non si stiano violando marchi. |

## <a name="privacy"></a>Privacy

|   |   |
|---|---|
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Fornire i collegamenti alle condizioni di servizio e all'informativa sulla privacy dell'app. |

## <a name="security"></a>Security

|   |   |
|---|---|
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Gestire gli URI di reindirizzamento: <ul><li>Mantenere la proprietà di tutti gli URI di reindirizzamento e mantenere aggiornati i record DNS.</li><li>Non usare i caratteri jolly (*) negli URI.</li><li>Per le app Web, assicurarsi che tutti gli URI siano protetti e crittografati, ad esempio usando gli schemi HTTPS.</li><li>Per i client pubblici, usare gli URI di reindirizzamento specifici della piattaforma, se applicabile (principalmente per iOS e Android). In caso contrario, usare gli URI di reindirizzamento con una quantità elevata di casualità per evitare conflitti quando si richiama l'app.</li><li>Se l'app viene utilizzata da un agente Web isolato, è possibile utilizzare https://login.microsoftonline.com/common/oauth2/nativeclient.</li><li>Esaminare e tagliare tutti gli URI di reindirizzamento non usati o non necessari a intervalli regolari.</li></ul> |
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Se l'app è registrata in una directory, minimizzare e monitorare manualmente l'elenco dei proprietari di registrazione delle app. |
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Non abilitare il supporto per il [flusso di concessione implicita OAuth2](v2-oauth2-implicit-grant-flow.md) , a meno che non sia esplicitamente richiesto. Per informazioni sullo scenario valido, vedere [qui](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Spostare oltre il nome utente/password. Non usare il [flusso di credenziali password del proprietario della risorsa (ROPC)](v2-oauth-ropc.md), che gestisce direttamente le password degli utenti. Questo flusso richiede un elevato livello di attendibilità e l'esposizione dell'utente e deve essere usato solo quando non è possibile usare altri flussi, più sicuri. Questo flusso è ancora necessario in alcuni scenari (ad esempio, DevOps), ma tenere presente che l'uso di questa funzione imporrà vincoli sull'applicazione.  Per approcci più moderni, vedere [flussi di autenticazione e scenari di applicazione](authentication-flows-app-scenarios.md).|
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Proteggi e Gestisci le credenziali delle app riservate per app Web, API Web e app daemon. Usare le [credenziali del certificato](active-directory-certificate-credentials.md)e non le credenziali password (segreti client). Se è necessario usare una credenziale password, non impostarla manualmente. Non archiviare le credenziali nel codice o nella configurazione e non consentirle di gestirle dagli utenti. Se possibile, usare [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) o [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) per archiviare e ruotare periodicamente le credenziali. |
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Assicurarsi che l'applicazione richieda le autorizzazioni con privilegi minimi. Chiedere solo le autorizzazioni necessarie all'applicazione e solo quando servono. Comprendere i diversi [tipi di autorizzazioni](v1-permissions-and-consent.md#types-of-permissions). Usare solo le autorizzazioni dell'applicazione, se necessario. utilizzare le autorizzazioni delegate laddove possibile. Per un elenco completo delle autorizzazioni di Microsoft Graph, vedere questo [riferimento alle autorizzazioni](https://docs.microsoft.com/graph/permissions-reference). |
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Se si sta proteggendo un'API usando la piattaforma di identità Microsoft, è opportuno valutare attentamente le autorizzazioni da esporre. Prendere in considerazione la granularità corretta per la soluzione e le autorizzazioni che richiedono il consenso dell'amministratore. Verificare la presenza di autorizzazioni previste nei token in ingresso prima di prendere decisioni di autorizzazione. |

## <a name="implementation"></a>Implementazione

|   |   |
|---|---|
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Usare le soluzioni di autenticazione moderne (OAuth 2,0, [OpenID Connect](v2-protocols-oidc.md)) per l'accesso sicuro agli utenti. |
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) |  Non programmare direttamente con protocolli come OAuth 2,0 e Open ID. Utilizzare invece [Microsoft Authentication Library (MSAL)](msal-overview.md). Le librerie MSAL incapsulano in modo sicuro i protocolli di sicurezza in una libreria di facile utilizzo e si ottiene il supporto incorporato per gli scenari di [accesso condizionale](/azure/active-directory/conditional-access/overview) , l'Single Sign-on a livello di dispositivo [(SSO)](/azure/active-directory/manage-apps/what-is-single-sign-on)e il supporto per la memorizzazione nella cache dei token incorporati. Per ulteriori informazioni, vedere l'elenco delle librerie [client](reference-v2-libraries.md#microsoft-supported-client-libraries) supportate da Microsoft e delle librerie [middleware](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) e l'elenco delle [librerie client di terze parti compatibili](reference-v2-libraries.md#compatible-client-libraries).<br/><br/>Se è necessario consegnare il codice per i protocolli di autenticazione, è necessario seguire una metodologia come [Microsoft SDL](https://www.microsoft.com/sdl/default.aspx). Prestare particolare attenzione alle considerazioni sulla sicurezza nelle specifiche degli standard per ogni protocollo.|
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) |  Eseguire la migrazione di app esistenti da [Azure Active Directory Authentication Library (adal)](active-directory-authentication-libraries.md) a [Microsoft Authentication Library](msal-overview.md). MSAL è la soluzione più recente della piattaforma di identità Microsoft ed è preferibile a ADAL. È disponibile in .NET e JavaScript ed è disponibile anche in versione di anteprima pubblica per Android, iOS, Python e Java. Scopri di più sulla migrazione delle app [adal.NET](msal-net-migration.md), [adal. js](msal-compare-msal-js-and-adal-js.md)e [adal.NET e iOS broker](msal-net-migration-ios-broker.md) .|
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) |  Per le app per dispositivi mobili, configurare ogni piattaforma usando l'esperienza di registrazione dell'applicazione. Per consentire all'applicazione di sfruttare i vantaggi del Microsoft Authenticator o Microsoft Portale aziendale per l'accesso Single Sign-on, l'app deve avere un "URI di reindirizzamento broker" configurato. Ciò consente a Microsoft di restituire il controllo all'applicazione dopo l'autenticazione. Quando si configura ogni piattaforma, l'esperienza di registrazione delle app guiderà l'utente durante il processo. Usare la Guida introduttiva per scaricare un esempio funzionante. In iOS usare broker e System WebView laddove possibile.|
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) |  In app Web o API Web tenere una cache di token per ogni account.  Per le app Web, la cache dei token deve essere codificata dall'ID account.  Per le API Web, l'account deve essere codificato dall'hash del token usato per chiamare l'API. MSAL.NET fornisce la serializzazione della cache dei token personalizzata nelle sottopiattaforme .NET Framework e .NET Core. Per motivi di sicurezza e prestazioni, è consigliabile serializzare una cache per utente. Per ulteriori informazioni, vedere la pagina relativa alla [serializzazione della cache dei token](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application).|
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Se i dati richiesti dall'app sono disponibili tramite [Microsoft Graph](https://developer.microsoft.com/graph), richiedere le autorizzazioni per questi dati usando l'endpoint Microsoft Graph invece della singola API. |
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) |Non esaminare il valore del token di accesso oppure provare ad analizzarlo come client.  Possono modificare i valori, i formati o persino crittografarli senza avviso: usare sempre token ID se il client deve apprendere qualcosa sull'utente o chiamare Microsoft Graph.  Solo le API Web devono analizzare i token di accesso (poiché sono quelli che definiscono il formato e l'impostazione delle chiavi di crittografia). |

## <a name="end-user-experience"></a>Esperienza utente finale

|   |   |
|---|---|
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | [Comprendere l'esperienza di consenso](application-consent-experience.md) e configurare i componenti della richiesta di consenso dell'app in modo che gli utenti finali e gli amministratori dispongano di informazioni sufficienti per determinare se considerano attendibile l'app. |
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Ridurre al minimo il numero di volte in cui un utente deve immettere le credenziali di accesso durante l'uso dell'app provando l'autenticazione invisibile all'utente (acquisizione di token invisibile all'utente) prima dei flussi interattivi. |
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Non usare "prompt = consenso" per ogni accesso. Usare prompt = consenso solo se si è stabilito che è necessario richiedere il consenso per autorizzazioni aggiuntive (ad esempio, se sono state modificate le autorizzazioni necessarie dell'app). |
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Laddove applicabile, arricchire l'applicazione con i dati utente. L'uso dell' [API Microsoft Graph](https://developer.microsoft.com/graph) è un modo semplice per eseguire questa operazione. Lo strumento [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) che può essere utile per iniziare. |
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Registrare il set completo di autorizzazioni richieste dall'app, in modo che gli amministratori possano concedere facilmente il consenso al tenant. Usare il [consenso incrementale](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) in fase di esecuzione per aiutare gli utenti a comprendere il motivo per cui l'app richiede autorizzazioni che potrebbero riguardare o confondere gli utenti quando richiesto al primo avvio. |
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementare un' [esperienza di accesso Single Sign-out pulita](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Si tratta di un requisito relativo alla privacy e alla sicurezza e offre un'esperienza utente ottimale. |

## <a name="testing"></a>Test

|   |   |
|---|---|
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Testare i [criteri di accesso condizionale](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) che potrebbero influire sulla capacità degli utenti di usare l'applicazione. |
| ![casella](./media/active-directory-integration-checklist/checkbox-two.svg) | Testare l'applicazione con tutti gli account possibili che si intende supportare, ad esempio account aziendali o dell'Istituto di istruzione, account Microsoft personali, account figlio e account sovrani. |

## <a name="additional-resources"></a>Risorse aggiuntive

Approfondimento su v2.0:

* [Piattaforma Microsoft Identity (panoramica della versione 2.0)](v2-overview.md)
* [Riferimenti per i protocolli della piattaforma Microsoft Identity](active-directory-v2-protocols.md)
* [Riferimento ai token di accesso](access-tokens.md)
* [Informazioni di riferimento sui token ID](id-tokens.md)
* [Informazioni di riferimento sulle librerie di autenticazione](reference-v2-libraries.md)
* [Autorizzazioni e consenso nella piattaforma di identità Microsoft](v2-permissions-and-consent.md)
* [API Microsoft Graph](https://developer.microsoft.com/graph)
