---
title: Gestione del Cloud Azure AD governata per carichi di lavoro locali-Azure
description: Questo argomento descrive la gestione gestita dal cloud per i carichi di lavoro locali.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b4d1041b9d330227fadf31f6afc1804174ea2ad
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96340850"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Come Azure AD offre la gestione gestita dal cloud per i carichi di lavoro locali

Azure Active Directory (Azure AD) è una soluzione IDaaS (Identity As a Service) completa usata da milioni di organizzazioni che si estendono su tutti gli aspetti di identità, gestione dell'accesso e sicurezza. Azure AD contiene più di un miliardo di identità utente e consente agli utenti di accedere e accedere in modo sicuro a entrambi:

* Risorse esterne, ad esempio Microsoft 365, portale di Azure e migliaia di altre applicazioni SaaS (software-as-a-Service).
* Risorse interne, ad esempio applicazioni nella rete aziendale e Intranet dell'organizzazione, insieme alle applicazioni cloud sviluppate da tale organizzazione.

Le organizzazioni possono usare Azure AD se sono "pure cloud" o "ibrido" se hanno carichi di lavoro locali. Una distribuzione ibrida di Azure AD può far parte di una strategia per un'organizzazione per eseguire la migrazione delle risorse IT nel cloud o per continuare a integrare l'infrastruttura locale esistente insieme ai nuovi servizi cloud.

Storicamente, le organizzazioni "ibride" hanno visto Azure AD come estensione dell'infrastruttura locale esistente. In queste distribuzioni, l'amministrazione della governance delle identità locale, Windows Server Active Directory o altri sistemi di directory internamente, sono i punti di controllo e gli utenti e i gruppi vengono sincronizzati da tali sistemi a una directory cloud, ad esempio Azure AD. Una volta che le identità si trovano nel cloud, possono essere rese disponibili per Microsoft 365, Azure e altre applicazioni.

![Ciclo di vita delle identità](media/cloud-governed-management-for-on-premises//image1.png)

Poiché le organizzazioni spostano una maggiore infrastruttura IT insieme alle applicazioni nel cloud, molti stanno cercando la sicurezza migliorata e le funzionalità di gestione semplificate della gestione delle identità come servizio. Le funzionalità IDaaS fornite dal cloud in Azure AD accelerano la transizione alla gestione governata dal cloud fornendo le soluzioni e le funzionalità che consentono alle organizzazioni di adottare e spostare in modo rapido una maggiore gestione delle identità dai sistemi locali tradizionali a Azure AD, pur continuando a supportare le applicazioni esistenti e nuove.

In questo documento viene illustrata la strategia di Microsoft per IDaaS ibrido e viene descritto il modo in cui le organizzazioni possono utilizzare Azure AD per le applicazioni esistenti.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Approccio Azure AD alla gestione delle identità governata dal cloud

Man mano che le organizzazioni passano al cloud, hanno bisogno di garanzie che dispongano di controlli sull'ambiente completo, più sicurezza e visibilità sulle attività, supportate dall'automazione e informazioni dettagliate proattive. "**Gestione cloud governata**" descrive il modo in cui le organizzazioni gestiscono e governano gli utenti, le applicazioni, i gruppi e i dispositivi dal cloud.

In questo mondo moderno, le organizzazioni devono essere in grado di gestire in modo efficace su larga scala, a causa della proliferazione di applicazioni SaaS e del ruolo crescente di collaborazione e identità esterne. Il nuovo panorama di rischio del cloud significa che un'organizzazione deve essere più reattiva: un attore malintenzionato che compromette un utente cloud potrebbe influire sulle applicazioni cloud e locali.

In particolare, le organizzazioni ibride devono essere in grado di delegare e automatizzare le attività, che in passato venivano effettuate manualmente. Per automatizzare le attività, sono necessari API e processi che orchestrano il ciclo di vita delle diverse risorse correlate all'identità (utenti, gruppi, applicazioni, dispositivi), in modo che possano delegare la gestione quotidiana di tali risorse a più utenti esterni al personale IT principale. Azure AD risolve questi requisiti tramite la gestione degli account utente e l'autenticazione nativa per gli utenti senza richiedere l'infrastruttura di identità locale. La mancata creazione di un'infrastruttura locale può trarre vantaggio dalle organizzazioni che hanno nuove community di utenti, ad esempio partner commerciali, che non hanno avuto origine nella directory locale, ma la cui gestione degli accessi è fondamentale per ottenere risultati aziendali.

Inoltre, la gestione non è completa, senza governance---e governance in questo nuovo mondo è una parte integrante del sistema di identità anziché un componente aggiuntivo. La governance delle identità offre alle organizzazioni la possibilità di gestire il ciclo di vita delle identità e degli accessi tra dipendenti, partner e fornitori aziendali e servizi e applicazioni.

L'incorporamento della governance delle identità rende più semplice consentire all'organizzazione di passare alla gestione governata dal cloud, consente la scalabilità, risolve nuove problemi con i guest e fornisce informazioni più dettagliate e automazione rispetto a quelle offerte dai clienti con l'infrastruttura locale. La governance in questo nuovo mondo indica la possibilità per un'organizzazione di avere trasparenza, visibilità e controlli appropriati sull'accesso alle risorse all'interno dell'organizzazione. Con Azure AD, le operazioni di sicurezza e i team di controllo hanno visibilità in merito a chi ha---e a chi deve accedere alle risorse dell'organizzazione (su quali dispositivi), a cosa fanno gli utenti con tale accesso e se l'organizzazione ha e usa i controlli appropriati per rimuovere o limitare l'accesso in base ai criteri aziendali o normativi.

Il nuovo modello di gestione avvantaggia le organizzazioni con applicazioni SaaS e line-of-business (LOB), perché sono più facilmente in grado di gestire e proteggere l'accesso a tali applicazioni. Grazie all'integrazione di applicazioni con Azure AD, le organizzazioni saranno in grado di usare e gestire in modo coerente l'accesso tra identità di origine cloud e locali. La gestione del ciclo di vita delle applicazioni diventa più automatizzata e Azure AD fornisce informazioni dettagliate sull'utilizzo delle applicazioni che non è stato facilmente raggiungibile nella gestione delle identità locale. Grazie alla Azure AD, Microsoft 365 le funzionalità self-service per i gruppi e i team, le organizzazioni possono creare facilmente gruppi per la gestione e la collaborazione degli accessi e aggiungere o rimuovere utenti nel cloud per consentire i requisiti di gestione per la collaborazione e l'accesso.

La selezione delle funzionalità di Azure AD corrette per la gestione gestita dal cloud dipende dalle applicazioni da usare e dal modo in cui tali applicazioni verranno integrate con Azure AD. Le sezioni seguenti illustrano gli approcci da eseguire per le applicazioni integrate AD e le applicazioni che usano protocolli di federazione, ad esempio SAML, OAuth o OpenID Connect.

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Gestione basata sul cloud per applicazioni integrate con Active Directory

Azure AD migliora la gestione per le applicazioni locali Active Directory-Integrated di un'organizzazione tramite l'accesso remoto sicuro e l'accesso condizionale a tali applicazioni. Inoltre, Azure AD fornisce anche la gestione del ciclo di vita dell'account e la gestione delle credenziali per gli account di Active Directory esistenti dell'utente, tra cui:

* **Accesso remoto sicuro e accesso condizionale per le applicazioni locali**

Per molte organizzazioni, il primo passaggio per la gestione dell'accesso dal cloud per le applicazioni basate su desktop remoto e Web integrato in AD locale è la distribuzione del [proxy di applicazione](../manage-apps/application-proxy.md) davanti a tali applicazioni per fornire accesso remoto sicuro.

Dopo un accesso singolo (SSO) ad Azure AD, gli utenti possono accedere sia alle applicazioni nel cloud che a quelle locali tramite un URL esterno o un portale per le applicazioni interno. Ad esempio, il proxy di applicazione fornisce accesso remoto e Single Sign-On a Desktop remoto, SharePoint, oltre ad app quali tableau e Qlik e applicazioni line-of-business (LOB). Inoltre, i criteri di accesso condizionale possono includere la visualizzazione delle condizioni per l' [utilizzo](../conditional-access/terms-of-use.md) e [la garanzia che l'utente abbia acconsentito](../conditional-access/require-tou.md) prima di poter accedere a un'applicazione.

![Architettura del proxy di app](media/cloud-governed-management-for-on-premises/image2.png)

* **Gestione automatica del ciclo di vita per account Active Directory**

La governance delle identità consente alle organizzazioni di raggiungere un equilibrio tra la *produttività* ---la velocità con cui un utente può accedere alle risorse necessarie, ad esempio quando partecipa all'organizzazione? ---e *sicurezza* ---come dovrebbe cambiare l'accesso nel tempo, ad esempio quando cambia lo stato occupazionale della persona? La gestione del ciclo di vita delle identità è la base per la governance delle identità e per una governance efficace su larga scala occorre modernizzare l'infrastruttura di gestione del ciclo di vita delle identità per le applicazioni.

Per molte organizzazioni, il ciclo di vita delle identità per i dipendenti è associato alla rappresentazione di tale utente in un sistema di gestione di capitale umano (HCM). Per le organizzazioni che usano la giornata lavorativa come sistema HCM, Azure AD possibile garantire che agli account utente in Active Directory venga automaticamente effettuato il [provisioning e il deprovisioning per i lavoratori della giornata lavorativa](../saas-apps/workday-inbound-tutorial.md). Questa operazione consente di migliorare la produttività degli utenti tramite l'automazione degli account di primogenitura e di gestire i rischi garantendo che l'accesso alle applicazioni venga aggiornato automaticamente quando un utente modifica i ruoli o lascia l'organizzazione. Il [piano di distribuzione](https://aka.ms/WorkdayDeploymentPlan) per il provisioning degli utenti guidato dalla giornata lavorativa è una guida dettagliata che illustra le organizzazioni tramite l'implementazione delle procedure consigliate per Active Directory soluzione di provisioning degli utenti in un processo in cinque passaggi.

Azure AD Premium include anche Microsoft Identity Manager, che consente di importare i record da altri sistemi di gestione delle reti locali, tra cui SAP, Oracle eBusiness e Oracle PeopleSoft.

Per la collaborazione business-to-business è sempre necessario concedere l'accesso a utenti esterni all'organizzazione. [Azure ad collaborazione B2B](/azure/active-directory/b2b/) consente alle organizzazioni di condividere in modo sicuro le applicazioni e i servizi con utenti guest e partner esterni, mantenendo al tempo stesso il controllo sui propri dati aziendali.

Azure AD possibile [creare automaticamente account in Active Directory per gli utenti Guest](../external-identities/hybrid-cloud-to-on-premises.md) , in base alle esigenze, consentendo ai Guest aziendali di accedere alle applicazioni locali ad integrate senza che sia necessaria un'altra password. Le organizzazioni possono configurare i [criteri di autenticazione a più fattori per gli utenti Guest](../external-identities/conditional-access.md), in modo che i controlli dell'autenticazione a più fattori vengano eseguiti durante l'autenticazione del proxy di applicazione. Inoltre, le verifiche di [accesso](../governance/manage-guest-access-with-access-reviews.md) eseguite sugli utenti B2B del cloud si applicano agli utenti locali. Ad esempio, se l'utente cloud viene eliminato con i criteri di gestione del ciclo di vita, viene eliminato anche l'utente locale.

**Gestione delle credenziali per gli account di Active Directory** Azure AD la reimpostazione della password self-service consente agli utenti che hanno dimenticato la password di essere riautenticati e reimpostare le password, con le password modificate [scritte nell'Active Directory locale](../authentication/concept-sspr-writeback.md). Il processo di reimpostazione della password può anche usare i criteri di Active Directory password locali: quando un utente reimposta la password, viene controllato per verificare che soddisfi i criteri di Active Directory locali prima di eseguirne il commit in tale directory. Il [piano di distribuzione](../authentication/howto-sspr-deployment.md) della reimpostazione della password self-service delinea le procedure consigliate per implementare la reimpostazione della password self-service agli utenti tramite esperienze Web e integrate in Windows.

![Architettura Azure AD SSPR](media/cloud-governed-management-for-on-premises/image3.png)

Infine, per le organizzazioni che consentono agli utenti di modificare le proprie password in Active Directory, è possibile configurare Active Directory per l'uso degli stessi criteri password dell'organizzazione che usa in Azure AD tramite la [funzionalità di protezione Azure ad password](../authentication/concept-password-ban-bad-on-premises.md), attualmente disponibile in anteprima pubblica.

Quando un'organizzazione è pronta per spostare un'applicazione integrata con Active Directory nel cloud spostando il sistema operativo che ospita l'applicazione in Azure, [Azure ad Domain Services](../../active-directory-domain-services/overview.md) fornisce servizi di dominio compatibili con ad, ad esempio aggiunta a un dominio, criteri di gruppo, LDAP e autenticazione Kerberos/NTLM. Azure AD Domain Services si integra con il tenant di Azure AD esistente dell'organizzazione, consentendo agli utenti di accedere usando le credenziali aziendali. Inoltre, i gruppi e gli account utente esistenti possono essere usati per proteggere l'accesso alle risorse, garantendo un "Lift-and-Shift" più agevole delle risorse locali ai servizi di infrastruttura di Azure.

![Servizi di dominio Azure Active Directory](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Gestione gestita dal cloud per le applicazioni locali basate sulla Federazione

Per un'organizzazione che usa già un provider di identità locale, lo strumento di trasferimento delle applicazioni a Azure AD consente un accesso più sicuro e un'esperienza amministrativa più semplice per la gestione della Federazione. Azure AD Abilita la configurazione dei controlli di accesso per applicazione granulari, incluso Azure AD Multi-Factor Authentication, usando Azure AD l'accesso condizionale. Azure AD supporta più funzionalità, inclusi i certificati per la firma di token specifici dell'applicazione e le date di scadenza dei certificati configurabili. Queste funzionalità, strumenti e linee guida consentono alle organizzazioni di ritirare i provider di identità locali. Microsoft IT, per un esempio, ha spostato 17.987 applicazioni dal Active Directory Federation Services interno di Microsoft (AD FS) a Azure AD.

![Azure AD Evolution](media/cloud-governed-management-for-on-premises/image5.png)

Per avviare la migrazione di applicazioni federate a Azure AD come provider di identità, fare riferimento a https://aka.ms/migrateapps che include collegamenti a:

* Il white paper la [migrazione delle applicazioni a Azure Active Directory](https://aka.ms/migrateapps/whitepaper), che presenta i vantaggi della migrazione e descrive come pianificare la migrazione in quattro fasi chiaramente delineate: individuazione, classificazione, migrazione e gestione continua. Verrà illustrato come considerare il processo e suddividere il progetto in parti facili da utilizzare. Nel documento vengono forniti collegamenti a importanti risorse di supporto per l'utente durante il processo.

* La guida alla soluzione [che esegue l'autenticazione dell'applicazione da Active Directory Federation Services a Azure Active Directory](../manage-apps/migrate-adfs-apps-to-azure.md) Esplora in modo più dettagliato le stesse quattro fasi della pianificazione e dell'esecuzione di un progetto di migrazione di applicazioni. In questa guida si apprenderà come applicare tali fasi allo scopo specifico di trasferire un'applicazione da Active Directory Federation Services (AD FS) a Azure AD.

* Lo [script di preparazione alla migrazione Active Directory Federation Services](https://aka.ms/migrateapps/adfstools) può essere eseguito su server Active Directory Federation Services (ad FS) locali esistenti per determinare la conformità delle applicazioni per la migrazione a Azure ad.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Gestione degli accessi in corso tra applicazioni cloud e locali

Le organizzazioni necessitano di un processo per gestire l'accesso scalabile. Gli utenti continuano a accumulare i diritti di accesso e a superare gli elementi inizialmente sottoposti a provisioning. Inoltre, le organizzazioni aziendali devono poter essere ridimensionate in modo efficiente per sviluppare e applicare i criteri di accesso e i controlli su base continuativa.

In genere, l'IT delega le decisioni in merito all'approvazione dell'accesso ai decision maker aziendali. Inoltre, l'IT può comprendere gli utenti stessi. Ad esempio, gli utenti che accedono a dati riservati dei clienti nell'applicazione di marketing di un'azienda in Europa devono conoscere i criteri dell'azienda. Anche gli utenti guest potrebbero non essere a conoscenza dei requisiti di gestione per i dati di un'organizzazione a cui sono stati invitati.

Le organizzazioni possono automatizzare il processo del ciclo di vita dell'accesso attraverso tecnologie quali [gruppi dinamici](../enterprise-users/groups-dynamic-membership.md), abbinati al provisioning degli utenti in [applicazioni SaaS](../saas-apps/tutorial-list.md)o [applicazioni integrate con il sistema per la gestione delle identità tra domini (SCIM](../app-provisioning/use-scim-to-provision-users-and-groups.md)). Le organizzazioni possono inoltre controllare quali [utenti Guest hanno accesso alle applicazioni locali](../external-identities/hybrid-cloud-to-on-premises.md). I diritti di accesso possono quindi essere riesaminati regolarmente usando le [verifiche di accesso ricorrenti di Azure AD](../governance/access-reviews-overview.md).

## <a name="future-directions"></a>Direzioni future

Negli ambienti ibridi, la strategia di Microsoft consiste nell'abilitare le distribuzioni in cui il **cloud è il piano di controllo per l'identità** e le directory locali e altri sistemi di identità, ad esempio Active Directory e altre applicazioni locali, rappresentano la destinazione per il provisioning degli utenti con accesso. Questa strategia continuerà a garantire i diritti, le identità e l'accesso in tali applicazioni e carichi di lavoro basati su di essi. A questo stato finale, le organizzazioni saranno in grado di gestire la produttività degli utenti finali interamente dal cloud.

![Architettura di Azure AD](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su come iniziare a usare questo percorso, vedere la Azure AD piani di distribuzione, disponibile all'indirizzo <https://aka.ms/deploymentplans> . Forniscono indicazioni end-to-end su come distribuire le funzionalità di Azure Active Directory (Azure AD). Ogni piano illustra il valore aziendale, le considerazioni sulla pianificazione, la progettazione e le procedure operative necessarie per implementare correttamente le funzionalità di Azure AD comuni. Microsoft aggiorna continuamente i piani di distribuzione con le procedure consigliate apprese dalle distribuzioni dei clienti e da altri commenti quando si aggiungono nuove funzionalità per la gestione dal cloud con Azure AD.