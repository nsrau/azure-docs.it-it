---
title: Eseguire l'integrazione con la piattaforma di identità Microsoft | Azure
description: Informazioni sulle procedure consigliate e sulle supervisione comuni per l'integrazione con la piattaforma di identità Microsoft (v 2.0).
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
ms.date: 05/07/2019
ms.author: ryanwi
ms.reviewer: lenalepa, sureshja, jesakowi
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 33128cbece3b217778182b3831b02e2f3f654f3b
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68853206"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Elenco di controllo dell'integrazione della piattaforma Microsoft Identity

L'elenco di controllo dell'integrazione della piattaforma Microsoft Identity è destinato a garantire un'integrazione sicura e di alta qualità. Vengono evidenziate le procedure consigliate e le supervisione comuni quando si integrano con la piattaforma di gestione delle identità Microsoft, quindi è possibile esaminare l'elenco a intervalli regolari per assicurarsi di mantenere la qualità e la sicurezza dell'integrazione dell'app con la piattaforma di identità. L'elenco di controllo non è destinato a esaminare l'intera applicazione. Il contenuto dell'elenco di controllo è soggetto a modifiche apportate per migliorare la piattaforma.

Se si è appena iniziato, consultare la [documentazione](index.yml) per conoscere le nozioni di base sull'autenticazione, gli scenari applicativi nella piattaforma di identità Microsoft e altro ancora.

## <a name="testing-your-integration"></a>Test dell'integrazione

Usare il seguente elenco di controllo per assicurarsi che l'applicazione sia integrata efficacemente con la [piattaforma di identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop/).

### <a name="basics"></a>Generale

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Leggere e comprendere i [criteri della piattaforma Microsoft](https://go.microsoft.com/fwlink/?linkid=2090497&clcid=0x409). Assicurarsi che l'applicazione soddisfi le condizioni descritte in quanto sono progettate per proteggere gli utenti e la piattaforma. |

### <a name="ownership"></a>Proprietà

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Assicurarsi che le informazioni associate all'account usato per registrare e gestire le app siano aggiornate. |

### <a name="branding"></a>Personalizzazione

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Rispettare le [linee guida per la personalizzazione delle applicazioni](howto-add-branding-in-azure-ad-apps.md). |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Fornire un nome e un logo significativi per l'applicazione. Queste informazioni vengono visualizzate nella richiesta di consenso dell'applicazione. Verificare che il nome e il logo siano rappresentativi della società o del prodotto, in modo che gli utenti possano prendere decisioni informate. Assicurarsi che non si stiano violando marchi. |

### <a name="privacy"></a>Privacy

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Fornire i collegamenti alle condizioni di servizio e all'informativa sulla privacy dell'app. |

### <a name="security"></a>Security

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Mantenere la proprietà di tutti gli URI di reindirizzamento e mantenere aggiornati i record DNS. Non usare i caratteri jolly (*) negli URI. Per le app Web, assicurarsi che tutti gli URI siano protetti e crittografati, ad esempio usando gli schemi HTTPS. Per i client pubblici, usare gli URI di reindirizzamento specifici della piattaforma, se applicabile (principalmente per iOS e Android). In caso contrario, usare gli URI di reindirizzamento con una quantità elevata di casualità per evitare conflitti quando si richiama l'app. Se l'app viene usata da un agente Web isolato, è possibile usare https://login.microsoftonline.com/nativeclient. Esaminare e tagliare tutti gli URI di reindirizzamento non usati o non necessari a intervalli regolari. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Se l'app è registrata in una directory, minimizzare e monitorare manualmente l'elenco dei proprietari di registrazione delle app. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Non abilitare il supporto per il [flusso di concessione implicita OAuth2](v2-oauth2-implicit-grant-flow.md) , a meno che non sia esplicitamente richiesto. Per informazioni sullo scenario valido, vedere [qui](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Non usare il [flusso di credenziali password del proprietario della risorsa (ROPC)](v2-oauth-ropc.md), che gestisce direttamente le password degli utenti. Questo flusso richiede un elevato livello di attendibilità e l'esposizione dell'utente e deve essere usato solo quando non è possibile usare altri flussi, più sicuri. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Proteggere e gestire le credenziali dell'app. Usare le [credenziali del certificato](active-directory-certificate-credentials.md)e non le credenziali password (segreti client). Se è necessario usare una credenziale password, non impostarla manualmente. Non archiviare le credenziali nel codice o nella configurazione e non consentirle di gestirle dagli utenti. Se possibile, usare [identità gestite per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) o [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) per archiviare e ruotare periodicamente le credenziali. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Assicurarsi che l'applicazione richieda le autorizzazioni con privilegi minimi. Chiedere solo le autorizzazioni necessarie all'applicazione e solo quando servono. Comprendere i diversi [tipi di autorizzazioni](v1-permissions-and-consent.md#types-of-permissions). Usare solo le autorizzazioni dell'applicazione, se necessario. utilizzare le autorizzazioni delegate laddove possibile. Per un elenco completo delle autorizzazioni di Microsoft Graph, vedere questo [riferimento alle autorizzazioni](https://docs.microsoft.com/graph/permissions-reference). |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Se si sta proteggendo un'API usando la piattaforma di identità Microsoft, è opportuno valutare attentamente le autorizzazioni da esporre. Prendere in considerazione la granularità corretta per la soluzione e le autorizzazioni che richiedono il consenso dell'amministratore. Verificare la presenza di autorizzazioni previste nei token in ingresso prima di prendere decisioni di autorizzazione. |

### <a name="implementation"></a>Implementazione

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Usare le soluzioni di autenticazione moderne (OAuth 2,0, [OpenID Connect](v2-protocols-oidc.md)) per l'accesso sicuro agli utenti. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Non implementare i protocolli personalmente: usare le [librerie di autenticazione supportate da Microsoft](reference-v2-libraries.md) (MSAL, middleware Server). Assicurarsi di usare la versione più recente della libreria di autenticazione integrata con. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Se i dati richiesti dall'app sono disponibili tramite [Microsoft Graph](https://developer.microsoft.com/graph), richiedere le autorizzazioni per questi dati usando l'endpoint Microsoft Graph invece della singola API. |

### <a name="end-user-experience"></a>Esperienza utente finale

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | [Comprendere l'esperienza di consenso](application-consent-experience.md) e configurare i componenti della richiesta di consenso dell'app in modo che gli utenti finali e gli amministratori dispongano di informazioni sufficienti per determinare se considerano attendibile l'app. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Ridurre al minimo il numero di volte in cui un utente deve immettere le credenziali di accesso durante l'uso dell'app provando l'autenticazione invisibile all'utente (acquisizione di token invisibile all'utente) prima dei flussi interattivi. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Non usare "prompt = consenso" per ogni accesso. Usare prompt = consenso solo se si è stabilito che è necessario richiedere il consenso per autorizzazioni aggiuntive (ad esempio, se sono state modificate le autorizzazioni necessarie dell'app). |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Laddove applicabile, arricchire l'applicazione con i dati utente. Usare l' [API Microsoft Graph](https://developer.microsoft.com/graph) è un modo semplice per eseguire questa operazione. Lo strumento [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) che può essere utile per iniziare. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Registrare il set completo di autorizzazioni richieste dall'app, in modo che gli amministratori possano concedere facilmente il consenso al tenant. Usare il [consenso incrementale](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) in fase di esecuzione per aiutare gli utenti a comprendere il motivo per cui l'app richiede autorizzazioni che potrebbero riguardare o confondere gli utenti quando richiesto al primo avvio. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementare un' [esperienza di accesso Single Sign-out pulita](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). Si tratta di un requisito relativo alla privacy e alla sicurezza e offre un'esperienza utente ottimale. |

### <a name="testing"></a>Test

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Testare i [criteri di accesso condizionale](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) che potrebbero influire sulla capacità degli utenti di usare l'applicazione. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Testare l'applicazione con tutti gli account possibili che si intende supportare, ad esempio account aziendali o dell'Istituto di istruzione, account Microsoft personali, account figlio e account sovrani. |

## <a name="additional-resources"></a>Risorse aggiuntive

Approfondimento su v2.0:

* [Piattaforma Microsoft Identity (panoramica della versione 2.0)](v2-overview.md)
* [Riferimenti per i protocolli della piattaforma Microsoft Identity](active-directory-v2-protocols.md)
* [Riferimento ai token di accesso](access-tokens.md)
* [Informazioni di riferimento sui token ID](id-tokens.md)
* [Informazioni di riferimento sulle librerie di autenticazione](reference-v2-libraries.md)
* [Autorizzazioni e consenso nella piattaforma di identità Microsoft](v2-permissions-and-consent.md)
* [API Microsoft Graph](https://developer.microsoft.com/graph)
