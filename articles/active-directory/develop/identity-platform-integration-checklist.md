---
title: Integrazione con la piattaforma Microsoft identity | Azure
description: Informazioni sulle procedure consigliate e Sviste comuni quando si integra con la piattaforma delle identità Microsoft (v2.0).
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
ms.custom: aaddev
ms.openlocfilehash: e9070127780659142ab8f956a8016622ecfea144
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540176"
---
# <a name="microsoft-identity-platform-integration-checklist"></a>Elenco di controllo per l'integrazione di Microsoft identity platform

Elenco di controllo per l'integrazione Microsoft identity platform è destinato per ottenere istruzioni utili per un'integrazione di qualità elevata e sicura. Evidenzia le procedure consigliate e Sviste comuni quando si integra con la piattaforma delle identità Microsoft quindi esaminare l'elenco a intervalli regolari per accertarsi di mantenere la qualità e la sicurezza dell'integrazione dell'app con la piattaforma delle identità. L'elenco di controllo non è destinata a esaminare l'intera applicazione. Il contenuto dell'elenco di controllo è soggetti a modifiche nell'ottica dei miglioramenti apportati alla piattaforma.

Se sta appena iniziando a usare, consultare il [documentazione](index.yml) per apprendere nozioni di base di autenticazione, gli scenari di applicazione nella piattaforma delle identità Microsoft e altro ancora.

## <a name="testing-your-integration"></a>Test di integrazione

Attenersi alla procedura seguente per assicurarsi che l'applicazione è integrata in modo efficace con la [piattaforma delle identità Microsoft](https://docs.microsoft.com/legal/mdsa).

### <a name="basics"></a>Generale

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Leggere e comprendere le [i criteri della piattaforma Microsoft](https://docs.microsoft.com/legal/mdsa). Assicurarsi che l'applicazione rispetti le condizioni descritte come sono stati progettati per proteggere gli utenti e la piattaforma. |

### <a name="ownership"></a>Proprietà

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Verificare le informazioni associate all'account usato per registrare e gestire le app siano aggiornate. |

### <a name="branding"></a>Personalizzazione

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Rispettare le [le linee guida per le applicazioni](howto-add-branding-in-azure-ad-apps.md). |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Specificare un nome significativo e logo per l'applicazione. Queste informazioni vengono visualizzate nella richiesta di consenso dell'applicazione. Assicurarsi che il nome e il logo sono rappresentante della società/prodotto in modo che gli utenti possano prendere decisioni consapevoli. Verificare che sta non stanno violando qualsiasi marchi. |

### <a name="privacy"></a>Privacy

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Fornire collegamenti alle condizioni dell'app del servizio e informativa sulla privacy. |

### <a name="security"></a>Security

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Mantenere la proprietà di tutti gli URI di reindirizzamento e mantenere aggiornati i record DNS per loro. Non usare caratteri jolly (*) negli URI. Per le app web, assicurarsi che tutti gli URI sono protette e crittografate (ad esempio, utilizzando schemi https). Per i client pubblici, usare gli URI di reindirizzamento specifico della piattaforma se applicabile (principalmente per iOS e Android). In caso contrario, utilizzare URI di reindirizzamento con un elevato livello di casualità per impedire conflitti quando si chiama nuovamente all'app. Se l'app viene usato da un agente web isolato, è possibile usare https://login.microsoftonline.com/nativeclient. Rivedere e trim tutti non necessari o inutilizzati gli URI di reindirizzamento a intervalli regolari. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Se l'app viene registrata in una directory, ridurre al minimo e monitorare manualmente l'elenco dei proprietari di registrazione di app. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Non abilitare il supporto per la [flusso di concessione implicita OAuth2](v2-oauth2-implicit-grant-flow.md) se non esplicitamente richiesto. Altre informazioni sullo scenario valido [qui](v1-oauth2-implicit-grant-flow.md#suitable-scenarios-for-the-oauth2-implicit-grant). |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Non usare [flusso delle credenziali password del proprietario della risorsa (ROPC)](v2-oauth-ropc.md), che gestisce direttamente le password degli utenti. Questo flusso richiede un elevato livello di attendibilità e l'utente l'esposizione e deve essere usato solo quando non è possibile usare altri, più sicuro, i flussi. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Proteggere e gestire le credenziali dell'app. Uso [credenziali del certificato](active-directory-certificate-credentials.md), non le credenziali di password (i segreti client). Se è necessario usare una credenziale della password, non impostarlo manualmente. Non archiviare le credenziali nel codice o configurazione e mai consentire loro di essere gestiti dagli utenti. Se possibile, usare [gestite le identità per le risorse di Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) oppure [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis) per archiviare e ruotare periodicamente le credenziali. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Assicurarsi che l'applicazione richiede le autorizzazioni con privilegi minimi. Richiedere solo le autorizzazioni strettamente necessarie all'applicazione e solo quando ti servono. Comprendere i diversi [tipi di autorizzazioni](v1-permissions-and-consent.md#types-of-permissions). Usare solo le autorizzazioni dell'applicazione se necessario. dove possibile, usare autorizzazioni delegate. Per un elenco completo delle autorizzazioni di Microsoft Graph, vedere questo [riferimento alle autorizzazioni](https://docs.microsoft.com/graph/permissions-reference). |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Se si sta proteggere un'API usando la piattaforma delle identità Microsoft, valutare attentamente le autorizzazioni che devono essere esposte. Si consideri ciò che è giusto livello di granularità per la soluzione e quali autorizzazioni richiedono il consenso dell'amministratore. Controllo delle autorizzazioni previste nei token in ingresso prima di prendere qualsiasi decisione di autorizzazione. |

### <a name="implementation"></a>Implementazione

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Usare soluzioni di autenticazione moderna (OAuth 2.0 [OpenID Connect](v2-protocols-oidc.md)) per accedere in modo sicuro gli utenti. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Non implementare i protocolli: usare [supportato da Microsoft authentication Library](reference-v2-libraries.md) (MSAL, middleware del server). Assicurarsi che si usa la versione più recente della libreria di autenticazione che è stato integrato con. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Se i dati richieste dall'applicazione sono disponibili tramite [Microsoft Graph](https://developer.microsoft.com/graph), richiedere le autorizzazioni per i dati usando l'endpoint di Microsoft Graph anziché l'API singoli. |

### <a name="end-user-experience"></a>Esperienza utente finale

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | [Comprendere l'esperienza di consenso](application-consent-experience.md) e configurare le parti della richiesta di consenso dell'app in modo che gli utenti finali e gli amministratori hanno informazioni sufficienti per determinare se devono considerare attendibili le app. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Ridurre al minimo il numero di volte in cui un utente deve immettere le credenziali di accesso quando si usa l'app provando invisibile all'utente l'autenticazione (acquisizione di token invisibile all'utente) prima di flussi interattivi. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Non usare "prompt = consent" per ogni accesso. Usare solo prompt = consent se è stato verificato che è necessario richiedere il consenso per le autorizzazioni aggiuntive (ad esempio, se è stato modificato le autorizzazioni necessarie dell'app). |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Dove applicabile, migliorare l'applicazione con i dati utente. Usare la [API Microsoft Graph](https://developer.microsoft.com/graph) è un modo semplice per eseguire questa operazione. Il [Graph explorer](https://developer.microsoft.com/graph/graph-explorer) strumento che può aiutarti a iniziare. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Registrare il set completo di autorizzazioni richieste dall'applicazione in modo che gli amministratori possono concedere il consenso facilmente con il tenant. Uso [consenso incrementale](azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent) in fase di esecuzione per consentire agli utenti di capire perché l'app richiede autorizzazioni che potrebbero riguardare o confondono gli utenti quando richiesto il primo avvio. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Implementare una [pulire l'esperienza single Sign-Out](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut). È un requisito di sicurezza e informativa sulla privacy e consente un'esperienza utente soddisfacente. |

### <a name="testing"></a>Test

|   |   |
|---|---|
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Per testare [criteri di accesso condizionale](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-6-SignOut) che potrebbero influire sulla capacità degli utenti di usare l'applicazione. |
| ![casella di controllo](./media/active-directory-integration-checklist/checkbox-two.svg) | Testare l'applicazione con tutti gli account possibili che si intende supportare (ad esempio, lavoro o dell'istituto di istruzione, gli account Microsoft personali, account per bambini e sovrane account). |

## <a name="additional-resources"></a>Risorse aggiuntive

Approfondimento su v2.0:

* [Piattaforma delle identità di Microsoft (panoramica v2.0)](v2-overview.md)
* [Riferimento di protocolli di Microsoft identity platform](active-directory-v2-protocols.md)
* [Riferimento ai token di accesso](access-tokens.md)
* [Informazioni di riferimento sui token ID](id-tokens.md)
* [Riferimento sulle librerie di autenticazione](reference-v2-libraries.md)
* [Le autorizzazioni e consenso dell'utente nella piattaforma delle identità Microsoft](v2-permissions-and-consent.md)
* [API Microsoft Graph](https://developer.microsoft.com/graph)