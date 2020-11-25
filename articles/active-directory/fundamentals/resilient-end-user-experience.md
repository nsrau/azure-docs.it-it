---
title: Esperienza dell'utente finale resiliente usando Azure AD B2C | Microsoft Docs
description: Metodi per compilare la resilienza nell'esperienza dell'utente finale usando Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 057599cc92e27e423d25d528d5d84b978ff4a911
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "95919535"
---
# <a name="resilient-end-user-experience"></a>Esperienza utente finale resiliente

L'esperienza dell'utente finale per l'iscrizione e l'accesso è costituita dagli elementi seguenti:

- Interfacce con cui l'utente interagisce, ad esempio CSS, HTML e JavaScript

- Flussi utente e criteri personalizzati creati, ad esempio iscrizione, accesso e modifica del profilo

- Provider di identità (IDP) per l'applicazione, ad esempio nome utente/password dell'account locale, Outlook, Facebook e Google

![Immagine che mostra i componenti dell'esperienza dell'utente finale](media/resilient-end-user-experiences/end-user-experience-architecture.png)

## <a name="choose-between-user-flow-and-custom-policy"></a>Scegliere tra il flusso utente e i criteri personalizzati  

Per semplificare la configurazione delle attività di identità più comuni, Azure AD B2C fornisce [flussi utente](https://docs.microsoft.com/azure/active-directory-b2c/user-flow-overview)configurabili incorporati. È anche possibile creare [criteri personalizzati](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)che offrono la massima flessibilità. Tuttavia, è consigliabile usare criteri personalizzati solo per risolvere scenari complessi.

### <a name="how-to-decide-between-user-flow-and-custom-policy"></a>Come scegliere tra il flusso utente e i criteri personalizzati

Scegliere flussi utente predefiniti se i requisiti aziendali possono essere soddisfatti. Dal momento che è stato testato da Microsoft, è possibile ridurre al minimo i test necessari per convalidare le funzionalità, le prestazioni e la scalabilità di questi flussi utente Identity a livello di criteri. È comunque necessario testare le applicazioni per le funzionalità, le prestazioni e la scalabilità.

Se si [scelgono criteri personalizzati](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) a causa dei requisiti aziendali, assicurarsi di eseguire test a livello di criteri per funzionalità, prestazioni o scalabilità oltre al test a livello di applicazione.

Vedere l'articolo che consente di [confrontare i flussi utente e i criteri personalizzati](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview#comparing-user-flows-and-custom-policies) per la scelta.

## <a name="choose-multiple-idps"></a>Scegliere più IDP

Quando si usa un [provider di identità esterno](https://docs.microsoft.com/azure/active-directory-b2c/technical-overview#external-identity-providers) , ad esempio Facebook, assicurarsi di disporre di un piano di fallback nel caso in cui il provider esterno diventi non disponibile.

### <a name="how-to-set-up-multiple-idps"></a>Come configurare più IDP

Nell'ambito del processo di registrazione del provider di identità esterno, includere un'attestazione di identità verificata, ad esempio il numero di cellulare o l'indirizzo di posta elettronica dell'utente. Eseguire il commit delle attestazioni verificate nell'istanza di directory Azure AD B2C sottostante. Se il provider esterno non è disponibile, ripristinare l'attestazione di identità verificata ed eseguire il fallback al numero di telefono come metodo di autenticazione. Un'altra opzione è quella di inviare all'utente un codice di accesso monouso per consentire all'utente di eseguire l'accesso.

 Per [compilare percorsi di autenticazione alternativi](https://github.com/azure-ad-b2c/samples/tree/master/policies/idps-filter), attenersi alla procedura seguente:

 1. Configurare i criteri di iscrizione per consentire l'accesso tramite account locale e IDP esterni.

 2. Configurare un criterio del profilo per consentire agli utenti di [collegare l'altra identità al proprio account dopo l'](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/account-linking) accesso.

 3. Notificare e consentire agli utenti di [passare a un provider di identità alternativo](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#configure-dynamic-custom-page-content-uri) durante un'interruzione del servizio.

## <a name="availability-of-multi-factor-authentication"></a>Disponibilità di autenticazione a più fattori

Quando si usa un [servizio telefonico per multi-factor authentication](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication), assicurarsi di prendere in considerazione un provider di servizi alternativo. Il servizio telco locale o il provider di servizi telefonici può compromettere l'utilizzo del servizio.

### <a name="how-to-choose-an-alternate-mfa"></a>Come scegliere un'autenticazione a più fattori alternativa  

Il servizio Azure AD B2C usa un provider di autenticazione a più fattori predefinito basato su telefono, per fornire codici di accesso monouso (soluzioni OTP) basati sul tempo. È sotto forma di chiamata vocale e SMS al numero di telefono preregistrato dell'utente. Per diversi scenari sono disponibili i metodi alternativi seguenti:

Quando si usano i **flussi utente**, sono disponibili due metodi per compilare la resilienza:

- **Modificare la configurazione del flusso utente**: quando si rileva un'interferenza nel recapito di OTP basato su telefono, modificare il metodo di recapito OTP da telefono a basato su posta elettronica e ridistribuire il flusso utente, lasciando invariate le applicazioni.

![screenshot che mostra l'iscrizione](media/resilient-end-user-experiences/create-sign-in.png)

- **Modificare le applicazioni**: per ogni attività di identità, ad esempio l'iscrizione e l'accesso, definire due set di flussi utente. Configurare il primo set per usare OTP basato su telefono e il secondo per l'OTP basato su posta elettronica. Quando si rileva un'interruzioni nel recapito OTP basato su telefono, modificare e ridistribuire le applicazioni per passare dal primo set di flussi utente alla seconda, lasciando invariati i flussi utente.  

Quando si usano **criteri personalizzati**, sono disponibili quattro metodi per compilare la resilienza. Il seguente elenco è in ordine di complessità ed è necessario ridistribuire i criteri aggiornati.

- **Abilitare la selezione utente di OTP basato su telefono o OTP basata sulla posta elettronica**: esporre entrambe le opzioni agli utenti e consentire agli utenti di selezionare autonomamente una delle opzioni. Non è necessario apportare modifiche ai criteri o alle applicazioni.

- **Passa dinamicamente tra OTP basato su telefono e OTP basata sulla posta elettronica**: Raccogli le informazioni telefoniche e di posta elettronica al momento dell'iscrizione. Definire i criteri personalizzati in anticipo per comportarsi in modo condizionale durante un'interruzioni del telefono, dal telefono al recapito di OTP basato sulla posta elettronica. Non è necessario apportare modifiche ai criteri o alle applicazioni.

- **Usare un'app Authenticator**: aggiornare i criteri personalizzati per usare un' [app di autenticazione](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-mfa-totp). Se l'autenticazione a più fattori normale è basata sul telefono o sull'OTP basata sulla posta elettronica, ridistribuire i criteri personalizzati per passare all'uso dell'app Authenticator.

>[!Note]
>Gli utenti devono configurare l'integrazione delle app di autenticazione durante la registrazione.

- **Usare le domande di sicurezza**: se nessuno dei metodi precedenti è applicabile, implementare le domande di sicurezza come backup. Configurare domande di sicurezza per gli utenti durante l'onboarding o la modifica del profilo e archiviare le risposte in un database separato diverso dalla directory. Questo metodo non soddisfa il requisito di autenticazione a più fattori di "qualcosa che si ha", ad esempio telefono, ma offre un "elemento che si conosce".

## <a name="use-a-content-delivery-network"></a>Usare una rete per la distribuzione di contenuti

Le reti per la distribuzione di contenuti (CDNs) sono più efficienti e meno costose degli archivi BLOB per l'archiviazione dell'interfaccia utente personalizzata del flusso utente. Il contenuto della pagina Web viene distribuito più velocemente da una rete geograficamente distribuita di server a disponibilità elevata.  

Testare periodicamente la disponibilità della rete CDN e le prestazioni della distribuzione del contenuto tramite uno scenario end-to-end e i test di carico. Se si sta pianificando un aumento imminente a causa del traffico di promozione o festività, rivedere le stime per i test di carico.
  
## <a name="next-steps"></a>Passaggi successivi

- [Risorse di resilienza per sviluppatori Azure AD B2C](resilience-b2c.md)
  
  - [Interfacce resilienti con processi esterni](resilient-external-processes.md)
  - [Resilienza tramite le procedure consigliate per gli sviluppatori](resilience-b2c-developer-best-practices.md)
  - [Resilienza tramite monitoraggio e analisi](resilience-with-monitoring-alerting.md)
- [Resilienza di compilazione nell'infrastruttura di autenticazione](resilience-in-infrastructure.md)
- [Aumentare la resilienza dell'autenticazione e dell'autorizzazione nelle applicazioni](resilience-app-development-overview.md)
