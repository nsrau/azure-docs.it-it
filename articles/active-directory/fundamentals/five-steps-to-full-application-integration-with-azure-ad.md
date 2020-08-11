---
title: Cinque passaggi per l'integrazione di tutte le app con Azure AD
description: Questa guida illustra come integrare tutte le applicazioni con Azure AD. In ogni passaggio viene illustrato il valore e vengono forniti collegamenti alle risorse che descrivono i dettagli tecnici.
services: active-directory
author: knicholasa
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 08/05/2020
ms.author: nichola
ms.openlocfilehash: c75d04faf9ac59f21aad1dcd88dfe83699a11941
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88057255"
---
# <a name="five-steps-for-integrating-all-your-apps-with-azure-ad"></a>Cinque passaggi per l'integrazione di tutte le app con Azure AD

Azure Active Directory (Azure AD) è il servizio Microsoft di gestione delle identità e degli accessi basato sul cloud. Azure AD offre soluzioni di autenticazione e autorizzazione sicure in modo che i clienti, i partner e i dipendenti possano accedere alle applicazioni di cui hanno bisogno. Con Azure AD, [l'accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/overview), [l'autenticazione](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)a più fattori, l'accesso [Single Sign-on e il](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso) [provisioning utenti automatico](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) rendono facile e sicura la gestione delle identità e degli accessi.

Se la società ha una sottoscrizione di Microsoft 365, è probabile che si [usi già](https://docs.microsoft.com/office365/enterprise/about-office-365-identity) Azure ad. Tuttavia, Azure AD può essere usato per tutte le applicazioni e [centralizzando la gestione delle applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps/common-scenarios) è possibile usare le stesse funzionalità, gli strumenti e i criteri di gestione delle identità nell'intero portfolio di app. In questo modo si fornirà una soluzione unificata che migliora la sicurezza, riduce i costi, aumenta la produttività e consente di garantire la conformità. E si otterrà l'accesso remoto alle app locali.

Questa guida illustra come integrare tutte le applicazioni con Azure AD. In ogni passaggio viene illustrato il valore e vengono forniti collegamenti alle risorse che descrivono i dettagli tecnici. Questi passaggi vengono presentati in un ordine consigliato. Tuttavia, è possibile passare a qualsiasi parte del processo per iniziare a usare qualsiasi aggiunta del valore più alto.

Altre risorse in questo argomento, incluso il processo di business approfondito white paper, sono reperibili nelle [risorse per la migrazione di applicazioni a Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) pagina.

## <a name="1-use-azure-ad-for-new-applications"></a>1. utilizzare Azure AD per le nuove applicazioni

Per prima cosa, concentrarsi sulle applicazioni appena acquisite. Quando l'azienda inizia a usare una nuova applicazione, [aggiungerla immediatamente al tenant Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal) . Configurare un criterio aziendale in modo che l'aggiunta di nuove app a Azure AD sia la prassi standard dell'organizzazione. Si tratta di un problema minimo per i processi aziendali esistenti, che consente di analizzare e dimostrare il valore ottenuto dall'integrazione delle app senza modificare il modo in cui gli utenti lavorano oggi nel proprio ambiente.

Azure Active Directory (Azure AD) include una raccolta che contiene migliaia di applicazioni preintegrate che consentono di iniziare a usare in modo semplice. È possibile [aggiungere un'app della raccolta all'organizzazione Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/add-gallery-app) con le [esercitazioni](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) dettagliate per l'integrazione con le app più diffuse, ad esempio:

- [ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-tutorial)
- [Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-tutorial)
- [Salesforce](https://docs.microsoft.com/azure/active-directory/saas-apps/salesforce-tutorial)
- [AWS](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial)
- [Slack](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-tutorial)

Inoltre, è possibile [integrare le applicazioni non presenti nella raccolta](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app), incluse tutte le applicazioni già esistenti nell'organizzazione o qualsiasi applicazione di terze parti da un fornitore che non fa già parte della raccolta di Azure ad. È anche possibile [aggiungere l'app alla raccolta](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-app-gallery-listing) , se non è presente.

Infine, è anche possibile integrare le app sviluppate internamente. Questa procedura è illustrata nel passaggio 5 di questa guida.

## <a name="2-determine-existing-application-usage-and-prioritize-work"></a>2. determinare l'utilizzo dell'applicazione esistente e definire le priorità del lavoro

Successivamente, individuare i dipendenti delle applicazioni che usano spesso e assegnare priorità al lavoro per integrarli con Azure AD.

È possibile iniziare usando Microsoft Cloud App Security&#39;s [cloud Discovery Tools](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) per individuare e gestire &quot; shadow &quot; it nella rete (ovvero app non gestite dal reparto IT). È possibile [utilizzare Microsoft Defender Advanced Threat Protection (ATP)](https://docs.microsoft.com/cloud-app-security/wdatp-integration) per semplificare ed estendere il processo di individuazione.

Inoltre, è possibile usare il [report attività ad FS applicazioni](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) nel portale di Azure per individuare tutte le app ad FS dell'organizzazione, il numero di utenti univoci che hanno eseguito l'accesso e la compatibilità per l'integrazione con Azure ad.

Una volta individuato il paesaggio esistente, è necessario [creare un piano](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources) e assegnare priorità alle app con priorità più elevata per l'integrazione. Di seguito sono riportate alcune domande di esempio che è possibile richiedere per guidare il processo:

- Quali sono le app più utilizzate?
- Quali sono i più rischioso?
- Quali app verranno rimosse in futuro, rendendo superfluo lo spostamento?
- Quali app devono rimanere in locale e non possono essere spostate nel cloud?

Potrai visualizzare i vantaggi più grandi e i risparmi sui costi dopo che tutte le tue app sono state integrate e non ti affidi più a soluzioni di identità diverse. Tuttavia, si verificherà una gestione delle identità più semplice e una maggiore sicurezza Man via che si procede gradualmente verso questo obiettivo. Si vuole usare questo tempo per definire le priorità del lavoro e decidere cosa è sensato per la situazione.

## <a name="3-integrate-apps-that-rely-on-other-identity-providers"></a>3. integrare le app che si basano su altri provider di identità

Durante il processo di individuazione, è possibile che siano state trovate applicazioni che non sono state rilevate dal reparto IT, che lasciano vulnerabili i dati e le risorse. È anche possibile che siano presenti applicazioni che usano soluzioni di identità alternative, tra cui Active Directory Federation Services (ADFS) o altri provider di identità. Prendere in considerazione il modo in cui è possibile consolidare la gestione delle identità e dell'accesso per risparmiare denaro e migliorare la sicurezza. La riduzione del numero di soluzioni di identità disponibili sarà:

- Risparmia denaro evitando la necessità di provisioning e autenticazione per gli utenti locali, oltre ai costi di licenza pagati ad altri provider di identità cloud per lo stesso servizio.
- Ridurre il sovraccarico amministrativo e garantire una sicurezza più stretta con meno ridondanze nel processo di gestione delle identità e dell'accesso.
- Consentire ai dipendenti di ottenere l'accesso sicuro Single Sign-On a tutte le applicazioni necessarie tramite il [portale app](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).
- Migliorare l'intelligence dei servizi correlati a Azure AD&#39;s [Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection) , ad esempio l'accesso condizionale aumentando la quantità di dati ottenuti dall'utilizzo dell'app e estendendo i vantaggi alle app appena aggiunte.

Sono state pubblicate informazioni aggiuntive per la gestione del processo di business per l'integrazione di app con Azure AD, tra cui un [poster](https://aka.ms/AppOnePager) e una [presentazione](https://aka.ms/AppGuideline) che è possibile usare per rendere i proprietari aziendali e delle applicazioni consapevoli e interessati. È possibile modificare questi esempi con una personalizzazione personalizzata e pubblicarli nell'organizzazione tramite il portale aziendale, la newsletter o un altro supporto per il completamento di questo processo.

Per iniziare, è opportuno valutare l'uso di Active Directory Federation Services (ADFS). Molte organizzazioni usano ADFS per l'autenticazione con app SaaS, app line-of-business personalizzate e app Office 365 e basate su Azure AD:

![Il diagramma mostra le app locali, le app line-of-business, le app SaaS e, tramite Azure AD, Office 365 che si connettono con linee tratteggiate in Active Directory e AD FS.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-1.png)

È possibile aggiornare questa configurazione [sostituendo ADFS con Azure ad come centro](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-apps-to-azure) della soluzione di gestione delle identità. In questo modo, è possibile accedere a ogni app a cui i dipendenti vogliono accedere e rende più semplice per i dipendenti trovare qualsiasi applicazione aziendale necessaria tramite il [portale app](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)Web, oltre agli altri vantaggi indicati in precedenza.

![Il diagramma mostra le app locali tramite Active Directory e AD FS, app line-of-business, app SaaS e Office 365 che si connettono con linee tratteggiate in Azure Active Directory.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-2.png)

Una volta che Azure AD diventa il provider di identità centrale, è possibile passare da ADFS completamente, anziché usare una soluzione federata. Le app che in precedenza usavano ADFS per l'autenticazione possono ora usare solo Azure AD.

![Il diagramma mostra le app line-of-business locali, le app SaaS e Office 365 che si connettono con linee tratteggiate in Azure Active Directory. Active Directory e AD FS non sono presenti.](\media\five-steps-to-full-application-integration-with-azure-ad\adfs-integration-3.png)

È anche possibile eseguire la migrazione di app che usano un provider di identità basato sul cloud diverso per Azure AD. È possibile che l'organizzazione disponga di più soluzioni IAM (Identity Access Management). La migrazione a un'infrastruttura Azure AD è un'opportunità per ridurre le dipendenze dalle licenze IAM (locale o nel cloud) e dai costi dell'infrastruttura. Nei casi in cui potrebbe essere già stato effettuato il pagamento per Azure AD tramite licenze M365, non esiste alcun motivo per pagare il costo aggiuntivo di un'altra soluzione IAM.

## <a name="4-integrate-on-premises-applications"></a>4. integrare le applicazioni locali

Tradizionalmente, le applicazioni sono state mantenute sicure consentendo l'accesso solo quando si è connessi alla rete aziendale. Tuttavia, in un mondo sempre più connesso si vuole consentire l'accesso alle app per clienti, partner e/o dipendenti, indipendentemente da dove si trovano nel mondo. [Azure ad Application Proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy) (AppProxy) è una funzionalità di Azure ad che connette le app locali esistenti a Azure ad e non richiede la gestione di server perimetrali o altre infrastrutture aggiuntive.

![Un diagramma mostra il servizio proxy di applicazione in azione. Un utente accede a " https://sales.contoso.com " e la richiesta viene reindirizzata tramite " https://sales-contoso.msappproxy.net " in Azure Active Directory all'indirizzo locale " http://sales "](./media/five-steps-to-full-application-integration-with-azure-ad\app-proxy.png)

È possibile usare l' [esercitazione: aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application) per abilitare il proxy di applicazione e aggiungere un'applicazione locale al tenant di Azure ad.

Inoltre, è possibile integrare i controller di distribuzione delle applicazioni come F5 Big-IP APM o zScaler private Access. Integrando questi elementi con Azure AD, si ottiene la moderna gestione dell'autenticazione e delle identità di Azure AD insieme alle funzionalità di gestione del traffico e di sicurezza del prodotto partner. Questa soluzione viene chiamata [accesso ibrido sicuro](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access). Se attualmente si usa uno dei servizi seguenti, sono disponibili esercitazioni che illustrano come integrarli con Azure AD.

- [Akamai Enterprise Application Access (EAA)](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)
- [Citrix Application Deliver controller (ADC)](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial) (precedentemente noto come Citrix NetScaler)
- [F5 Big-IP APM](https://docs.microsoft.com/azure/active-directory/saas-apps/headerf5-tutorial)
- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)

## <a name="5-integrate-apps-your-developers-build"></a>5. integrare le app create dagli sviluppatori

Per le app compilate nell'azienda, gli sviluppatori possono usare la [piattaforma di identità Microsoft](https://docs.microsoft.com/azure/active-directory/develop/) per implementare l'autenticazione e l'autorizzazione. Le applicazioni integrate con la piattaforma possono essere [registrate con Azure ad](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app) e gestite esattamente come qualsiasi altra app nel portfolio.

Gli sviluppatori possono usare la piattaforma per le app per uso interno e per le app rivolte ai clienti e sono disponibili altri vantaggi grazie alla piattaforma. [Microsoft Authentication Libraries (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview), che fa parte della piattaforma, consente agli sviluppatori di abilitare esperienze moderne come l'autenticazione a più fattori e l'uso di chiavi di sicurezza per accedere alle proprie app senza che sia necessario implementarle autonomamente. Inoltre, le app integrate con la piattaforma Microsoft Identity possono accedere a [Microsoft Graph](https://docs.microsoft.com/azure/active-directory/develop/microsoft-graph-intro) un endpoint API unificato fornendo i dati Microsoft 365 che descrivono i modelli di produttività, identità e sicurezza in un'organizzazione. Gli sviluppatori possono utilizzare queste informazioni per implementare funzionalità che aumentano la produttività degli utenti. Ad esempio, identificando le persone con cui l'utente interagisce recentemente e mostrandole nell'interfaccia utente di&#39;.

Abbiamo una [serie di video](https://www.youtube.com/watch?v=zjezqZPPOfc&amp;list=PLLasX02E8BPBxGouWlJV-u-XZWOc2RkiX) che fornisce un'introduzione completa alla piattaforma, nonché [molti esempi di codice](https://docs.microsoft.com/azure/active-directory/develop/sample-v2-code) in linguaggi e piattaforme supportate.

## <a name="next-steps"></a>Passaggi successivi

- [Risorse per la migrazione delle applicazioni ad Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/migration-resources)
