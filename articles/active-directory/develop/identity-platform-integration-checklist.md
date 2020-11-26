---
title: Procedure consigliate per la piattaforma di identità Microsoft | Azure
description: Informazioni sulle procedure consigliate, sulle raccomandazioni e sulle supervisione comuni per l'integrazione con la piattaforma di identità Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/08/2020
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 94da22fbb2940bddaad1324bedc8759706eca37b
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96173567"
---
# <a name="microsoft-identity-platform-best-practices-and-recommendations"></a>Procedure consigliate e raccomandazioni per Microsoft Identity Platform

Questo articolo evidenzia le procedure consigliate, le raccomandazioni e le supervisione comuni per l'integrazione con la piattaforma di identità Microsoft.  Questo elenco di controllo ti guiderà a un'integrazione sicura e di alta qualità. Esaminare regolarmente questo elenco per assicurarsi di mantenere la qualità e la sicurezza dell'integrazione dell'app con la piattaforma di identità. L'elenco di controllo non è destinato a esaminare l'intera applicazione. Il contenuto dell'elenco di controllo è soggetto a modifiche apportate per migliorare la piattaforma.

Se si è appena iniziato, consultare la documentazione della [piattaforma di identità Microsoft](index.yml) per informazioni sulle nozioni di base sull'autenticazione, gli scenari applicativi nella piattaforma di identità Microsoft e altro ancora.

Usare il seguente elenco di controllo per assicurarsi che l'applicazione sia integrata efficacemente con la [piattaforma di identità Microsoft](./index.yml).

> [!TIP]
> *Integration Assistant* nella portale di Azure può essere utile per applicare molti di questi consigli e procedure consigliate. Selezionare una delle registrazioni per l' [app](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) nel portale di Azure, quindi selezionare la voce di menu **Integration Assistant (anteprima)** per iniziare a usare l'assistente.

## <a name="basics"></a>Operazioni di base

![casella ](./media/active-directory-integration-checklist/checkbox-two.svg) di controllo leggere e comprendere i [criteri della piattaforma Microsoft](/legal/microsoft-identity-platform/terms-of-use). Assicurarsi che l'applicazione soddisfi le condizioni descritte in quanto sono progettate per proteggere gli utenti e la piattaforma.

## <a name="ownership"></a>Proprietario

![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) Assicurarsi che le informazioni associate all'account usato per registrare e gestire le app siano aggiornate.

## <a name="branding"></a>Marchio

![](./media/active-directory-integration-checklist/checkbox-two.svg)osservare le [linee guida sulla personalizzazione per le applicazioni](howto-add-branding-in-azure-ad-apps.md).

![casella ](./media/active-directory-integration-checklist/checkbox-two.svg) di controllo specificare un nome e un logo significativi per l'applicazione. Queste informazioni vengono visualizzate nella [richiesta di consenso dell'applicazione](application-consent-experience.md). Verificare che il nome e il logo siano rappresentativi della società o del prodotto, in modo che gli utenti possano prendere decisioni informate. Assicurarsi che non si stiano violando marchi.

## <a name="privacy"></a>Privacy

![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) Fornire i collegamenti alle condizioni di servizio e all'informativa sulla privacy dell'app.

## <a name="security"></a>Sicurezza

![casella di controllo ](./media/active-directory-integration-checklist/checkbox-two.svg) Gestisci gli URI di reindirizzamento: <ul><li>Mantenere la proprietà di tutti gli URI di reindirizzamento e mantenere aggiornati i record DNS.</li><li>Non usare i caratteri jolly (*) negli URI.</li><li>Per le app Web, assicurarsi che tutti gli URI siano protetti e crittografati, ad esempio usando gli schemi HTTPS.</li><li>Per i client pubblici, usare gli URI di reindirizzamento specifici della piattaforma, se applicabile (principalmente per iOS e Android). In caso contrario, usare gli URI di reindirizzamento con una quantità elevata di casualità per evitare conflitti quando si richiama l'app.</li><li>Se l'app viene usata da un agente Web isolato, è possibile usare `https://login.microsoftonline.com/common/oauth2/nativeclient` .</li><li>Esaminare e tagliare tutti gli URI di reindirizzamento non usati o non necessari a intervalli regolari.</li></ul>

![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) Se l'app è registrata in una directory, minimizzare e monitorare manualmente l'elenco dei proprietari di registrazione delle app.

![CheckBox ](./media/active-directory-integration-checklist/checkbox-two.svg) non Abilita il supporto per il [flusso di concessione implicita OAuth2](v2-oauth2-implicit-grant-flow.md) , a meno che non sia esplicitamente richiesto. Per informazioni sullo scenario valido, vedere [qui](v2-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant).

![casella di controllo ](./media/active-directory-integration-checklist/checkbox-two.svg) sposta oltre il nome utente/password. Non usare il [flusso di credenziali password del proprietario della risorsa (ROPC)](v2-oauth-ropc.md), che gestisce direttamente le password degli utenti. Questo flusso richiede un elevato livello di attendibilità e l'esposizione dell'utente e deve essere usato solo quando non è possibile usare altri flussi, più sicuri. Questo flusso è ancora necessario in alcuni scenari (ad esempio, DevOps), ma tenere presente che l'uso di questa funzione imporrà vincoli sull'applicazione.  Per approcci più moderni, vedere [flussi di autenticazione e scenari di applicazione](authentication-flows-app-scenarios.md).

![casella ](./media/active-directory-integration-checklist/checkbox-two.svg) di controllo proteggere e gestire le credenziali dell'app riservata per le app Web, le API Web e le app daemon. Usare le [credenziali del certificato](active-directory-certificate-credentials.md)e non le credenziali password (segreti client). Se è necessario usare una credenziale password, non impostarla manualmente. Non archiviare le credenziali nel codice o nella configurazione e non consentirle di gestirle dagli utenti. Se possibile, usare [identità gestite per le risorse di Azure](../managed-identities-azure-resources/overview.md) o [Azure Key Vault](../../key-vault/general/basic-concepts.md) per archiviare e ruotare periodicamente le credenziali.

![verificare ](./media/active-directory-integration-checklist/checkbox-two.svg) che l'applicazione richieda le autorizzazioni con privilegi minimi. Chiedere solo le autorizzazioni necessarie all'applicazione e solo quando servono. Comprendere i diversi [tipi di autorizzazioni](v2-permissions-and-consent.md#permission-types). Usare solo le autorizzazioni dell'applicazione, se necessario. utilizzare le autorizzazioni delegate laddove possibile. Per un elenco completo delle autorizzazioni di Microsoft Graph, vedere questo [riferimento alle autorizzazioni](/graph/permissions-reference).

![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) Se si sta proteggendo un'API usando la piattaforma di identità Microsoft, è opportuno valutare attentamente le autorizzazioni da esporre. Prendere in considerazione la granularità corretta per la soluzione e le autorizzazioni che richiedono il consenso dell'amministratore. Verificare la presenza di autorizzazioni previste nei token in ingresso prima di prendere decisioni di autorizzazione.

## <a name="implementation"></a>Implementazione

![CheckBox ](./media/active-directory-integration-checklist/checkbox-two.svg) Usa le soluzioni di autenticazione moderne (OAuth 2,0, [OpenID Connect](v2-protocols-oidc.md)) per accedere in modo sicuro agli utenti.

![](./media/active-directory-integration-checklist/checkbox-two.svg)la casella di controllo non viene programmata direttamente con protocolli come OAuth 2,0 e Open ID. Utilizzare invece [Microsoft Authentication Library (MSAL)](msal-overview.md). Le librerie MSAL incapsulano in modo sicuro i protocolli di sicurezza in una libreria di facile utilizzo e si ottiene il supporto incorporato per gli scenari di [accesso condizionale](../conditional-access/overview.md) , l'Single Sign-on a livello di dispositivo [(SSO)](../manage-apps/what-is-single-sign-on.md)e il supporto per la memorizzazione nella cache dei token incorporati. Per ulteriori informazioni, vedere l'elenco delle librerie [client](reference-v2-libraries.md#microsoft-supported-client-libraries) supportate da Microsoft e delle librerie [middleware](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries) e l'elenco delle [librerie client di terze parti compatibili](reference-v2-libraries.md#compatible-client-libraries).<br/><br/>Se è necessario consegnare il codice per i protocolli di autenticazione, è necessario seguire una metodologia come [Microsoft SDL](https://www.microsoft.com/sdl/default.aspx). Prestare particolare attenzione alle considerazioni sulla sicurezza nelle specifiche degli standard per ogni protocollo.

![casella ](./media/active-directory-integration-checklist/checkbox-two.svg) di controllo migrate Apps existing da [Azure Active Directory Authentication Library (adal)](../azuread-dev/active-directory-authentication-libraries.md) a [Microsoft Authentication Library](msal-overview.md). MSAL è la soluzione più recente della piattaforma di identità Microsoft ed è preferibile a ADAL. È disponibile in .NET, JavaScript, Android, iOS e macOS ed è disponibile anche in anteprima pubblica per Python e Java. Scopri di più sulla migrazione di app broker [adal.NET](msal-net-migration.md), [ADAL.js](msal-compare-msal-js-and-adal-js.md)e [adal.NET e iOS](msal-net-migration-ios-broker.md) .

![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) Per le app per dispositivi mobili, configurare ogni piattaforma usando l'esperienza di registrazione dell'applicazione. Per consentire all'applicazione di sfruttare i vantaggi del Microsoft Authenticator o Microsoft Portale aziendale per l'accesso Single Sign-on, l'app deve avere un "URI di reindirizzamento broker" configurato. Ciò consente a Microsoft di restituire il controllo all'applicazione dopo l'autenticazione. Quando si configura ogni piattaforma, l'esperienza di registrazione delle app guiderà l'utente durante il processo. Usare la Guida introduttiva per scaricare un esempio funzionante. In iOS usare broker e System WebView laddove possibile.

![casella ](./media/active-directory-integration-checklist/checkbox-two.svg) di controllo in app Web o API Web, Mantieni una cache di token per ogni account.  Per le app Web, la cache dei token deve essere codificata dall'ID account.  Per le API Web, l'account deve essere codificato dall'hash del token usato per chiamare l'API. MSAL.NET fornisce la serializzazione della cache dei token personalizzata nelle sottopiattaforme .NET Framework e .NET Core. Per motivi di sicurezza e prestazioni, è consigliabile serializzare una cache per utente. Per ulteriori informazioni, vedere la pagina relativa alla [serializzazione della cache dei token](msal-net-token-cache-serialization.md#token-cache-for-a-web-app-confidential-client-application).

![casella ](./media/active-directory-integration-checklist/checkbox-two.svg) di controllo se i dati richiesti dall'app sono disponibili tramite [Microsoft Graph](https://developer.microsoft.com/graph), richiedere le autorizzazioni per questi dati usando l'endpoint Microsoft Graph invece della singola API.

![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) Non esaminare il valore del token di accesso oppure provare ad analizzarlo come client.  Possono modificare i valori, i formati o persino crittografarli senza avviso: usare sempre il id_token se il client deve apprendere qualcosa sull'utente o chiamare Microsoft Graph.  Solo le API Web devono analizzare i token di accesso (poiché sono quelli che definiscono il formato e l'impostazione delle chiavi di crittografia).

## <a name="end-user-experience"></a>Esperienza utente finale

![casella ](./media/active-directory-integration-checklist/checkbox-two.svg) di controllo [comprendere l'esperienza di consenso](application-consent-experience.md) e configurare i componenti della richiesta di consenso dell'app in modo che gli utenti finali e gli amministratori dispongano di informazioni sufficienti per determinare se considerano attendibile l'app.

![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) Ridurre al minimo il numero di volte in cui un utente deve immettere le credenziali di accesso durante l'uso dell'app provando l'autenticazione invisibile all'utente (acquisizione di token invisibile all'utente) prima dei flussi interattivi.

![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) Non usare "prompt = consenso" per ogni accesso. Usare prompt = consenso solo se si è stabilito che è necessario richiedere il consenso per autorizzazioni aggiuntive (ad esempio, se sono state modificate le autorizzazioni necessarie dell'app).

![casella ](./media/active-directory-integration-checklist/checkbox-two.svg) di controllo, se applicabile, arricchire l'applicazione con i dati utente. L'uso dell' [API Microsoft Graph](https://developer.microsoft.com/graph) è un modo semplice per eseguire questa operazione. Lo strumento [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) che può essere utile per iniziare.

![casella ](./media/active-directory-integration-checklist/checkbox-two.svg) di controllo consente di registrare il set completo di autorizzazioni richieste dall'app per consentire agli amministratori di concedere facilmente il consenso al tenant. Usare il [consenso incrementale](../azuread-dev/azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) in fase di esecuzione per aiutare gli utenti a comprendere il motivo per cui l'app richiede autorizzazioni che potrebbero riguardare o confondere gli utenti quando richiesto al primo avvio.

![casella ](./media/active-directory-integration-checklist/checkbox-two.svg) di controllo implementare un' [esperienza di Single Sign-out pulita](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Si tratta di un requisito relativo alla privacy e alla sicurezza e offre un'esperienza utente ottimale.

## <a name="testing"></a>Test

![casella ](./media/active-directory-integration-checklist/checkbox-two.svg) di controllo per i [criteri di accesso condizionale](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) che potrebbero influire sulla capacità degli utenti di usare l'applicazione.

![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) Testare l'applicazione con tutti gli account possibili che si intende supportare, ad esempio account aziendali o dell'Istituto di istruzione, account Microsoft personali, account figlio e account sovrani.

## <a name="additional-resources"></a>Risorse aggiuntive

Approfondimento su v2.0:

* [Piattaforma Microsoft Identity (panoramica della versione 2.0)](v2-overview.md)
* [Riferimenti per i protocolli della piattaforma Microsoft Identity](active-directory-v2-protocols.md)
* [Riferimento ai token di accesso](access-tokens.md)
* [Informazioni di riferimento sui token ID](id-tokens.md)
* [Informazioni di riferimento sulle librerie di autenticazione](reference-v2-libraries.md)
* [Autorizzazioni e consenso in Microsoft Identity Platform](v2-permissions-and-consent.md)
* [API Microsoft Graph](https://developer.microsoft.com/graph)