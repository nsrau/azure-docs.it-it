---
title: Azure AD Cloud Governed Management for On-Premises Workloads - Azure
description: Questo argomento descrive la gestione gestita dal cloud per i carichi di lavoro locali.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 510a5562740260eb2946ded074a5c37804c55375
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67109515"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>In che modo Azure AD offre la gestione gestita dal cloud per i carichi di lavoro localiHow Azure AD Delivers Cloud Governed Management for On-Premises Workloads

Azure Active Directory (Azure AD) è una soluzione IDaaS (Complete Identity as a Service) usata da milioni di organizzazioni che coprono tutti gli aspetti dell'identità, della gestione degli accessi e della sicurezza. Azure AD contiene più di un miliardo di identità utente e consente agli utenti di accedere e accedere in modo sicuro a entrambi:Azure AD holds more than a billion user identities and helps users sign in and securely access both:

* Risorse esterne, ad esempio Microsoft Office 365, il portale di Azure e migliaia di altre applicazioni Software as-a-Service (SaaS).
* Risorse interne, ad esempio applicazioni nella rete aziendale e intranet di un'organizzazione, insieme a tutte le applicazioni cloud sviluppate da tale organizzazione.

Le organizzazioni possono usare Azure AD se sono 'cloud puro', o come distribuzione 'ibrida' se dispongono di carichi di lavoro locali. Una distribuzione ibrida di Azure AD può essere parte di una strategia per un'organizzazione per eseguire la migrazione delle risorse IT nel cloud o continuare a integrare l'infrastruttura locale esistente insieme a nuovi servizi cloud.

Storicamente, le organizzazioni "ibride" hanno visto Azure AD come un'estensione dell'infrastruttura locale esistente. In queste distribuzioni, l'amministrazione della governance delle identità locale, Windows Server Active Directory o altri sistemi di directory interni, sono i punti di controllo e utenti e gruppi vengono sincronizzati da tali sistemi in una directory cloud come Azure AD. Una volta che tali identità sono nel cloud, possono essere rese disponibili per Office 365, Azure e altre applicazioni.

![Ciclo di vita delle identità](media/cloud-governed-management-for-on-premises//image1.png)

Man mano che le organizzazioni spostano una maggiore infrastruttura IT insieme alle applicazioni nel cloud, molte sono alla ricerca di funzionalità di sicurezza e gestione semplificate migliorate della gestione delle identità come servizio. Le funzionalità IDaaS fornite nel cloud in Azure AD accelerano la transizione alla gestione gestita dal cloud fornendo le soluzioni e le funzionalità che consentono alle organizzazioni di adottare e spostare rapidamente la gestione delle identità dall'ambiente locale tradizionale Azure AD, pur continuando a supportare applicazioni esistenti e nuove.

Questo documento descrive la strategia di Microsoft per IDaaS ibrido e descrive come le organizzazioni possono usare Azure AD per le applicazioni esistenti.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>Approccio di Azure AD alla gestione delle identità gestita dal cloudThe Azure AD approach to cloud governed identity management

Man mano che le organizzazioni passano al cloud, hanno bisogno di garanzie di avere controlli sull'intero ambiente: maggiore sicurezza e maggiore visibilità sulle attività, supportate dall'automazione e informazioni proattive. "**Cloud governato management**" descrive come le organizzazioni gestiscono e governano i loro utenti, applicazioni, gruppi e dispositivi dal cloud.

In questo mondo moderno, le organizzazioni devono essere in grado di gestire in modo efficace su larga scala, a causa della proliferazione delle applicazioni SaaS e del crescente ruolo della collaborazione e delle identità esterne. Il nuovo panorama dei rischi del cloud significa che un'organizzazione deve essere più reattiva: un attore malintenzionato che compromette un utente cloud potrebbe influire sulle applicazioni cloud e locali.

In particolare, le organizzazioni ibride devono essere in grado di delegare e automatizzare le attività, come storicamente l'IT ha fatto manualmente. Per automatizzare le attività, sono necessarie API e processi che orchestrano il ciclo di vita delle diverse risorse correlate all'identità (utenti, gruppi, applicazioni, dispositivi), in modo da poter delegare la gestione quotidiana di tali risorse a più utenti al di fuori di personale IT di base. Azure AD soddisfa questi requisiti tramite la gestione degli account utente e l'autenticazione nativa per gli utenti senza richiedere l'infrastruttura di identità locale. Non creare un'infrastruttura locale può essere vantaggioso per le organizzazioni che dispongono di nuove comunità di utenti, ad esempio partner commerciali, che non hanno avuto origine nella directory locale, ma la cui gestione degli accessi è fondamentale per ottenere risultati aziendali.

Inoltre, la gestione non è completa senza governance --- e la governance in questo nuovo mondo è una parte integrata del sistema di identità piuttosto che un componente aggiuntivo. La governance delle identità offre alle organizzazioni la possibilità di gestire il ciclo di vita di identità e accesso tra dipendenti, partner commerciali e fornitori, servizi e applicazioni.

L'integrazione della governance delle identità semplifica la possibilità per l'organizzazione di passare alla gestione gestita dal cloud, consente all'IT di scalare nuove sfide con gli ospiti e fornisce informazioni e automazione più approfonditi rispetto a quelli che i clienti avevano con dell'infrastruttura locale. Governance in questo nuovo mondo significa la capacità per un'organizzazione di avere trasparenza, visibilità e controlli adeguati sull'accesso alle risorse all'interno dell'organizzazione. Con Azure AD, le operazioni di sicurezza e i team di controllo hanno visibilità su chi ha --- e chi deve avere: accesso alle risorse dell'organizzazione (su quali dispositivi), quali utenti stanno facendo con tale accesso e se l'organizzazione ha e usa appropriati per rimuovere o limitare l'accesso in conformità con le politiche aziendali o normative.

Il nuovo modello di gestione offre vantaggi alle organizzazioni con applicazioni SaaS e line-of-business (LOB), in quanto sono più facilmente in grado di gestire e proteggere l'accesso a tali applicazioni. Integrando le applicazioni con Azure AD, le organizzazioni saranno in grado di usare e gestire l'accesso tra le identità di origine sia cloud che locali in modo coerente. La gestione del ciclo di vita delle applicazioni diventa più automatizzata e Azure AD offre informazioni dettagliate sull'utilizzo delle applicazioni che non sono facilmente raggiungibili nella gestione delle identità locale. Tramite Azure AD, i gruppi di Office 365 e le funzionalità self-service di Teams possono creare facilmente gruppi per la gestione degli accessi e la collaborazione e aggiungere o rimuovere utenti nel cloud per abilitare i requisiti di collaborazione e gestione degli accessi.

La selezione delle funzionalità di Azure AD corrette per la gestione gestita dal cloud dipende dalle applicazioni da usare e dal modo in cui tali applicazioni verranno integrate con Azure AD. Nelle sezioni seguenti vengono descritti gli approcci da adottare per le applicazioni integrate in Active Directory e le applicazioni che utilizzano protocolli federativi, ad esempio SAML, OAuth o OpenID Connect.

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Gestione gestita dal cloud per le applicazioni integrate in ACTIVE Directory

Azure AD migliora la gestione per le applicazioni integrate in Active Directory locali di un'organizzazione tramite l'accesso remoto sicuro e l'accesso condizionale a tali applicazioni. Azure AD offre inoltre la gestione del ciclo di vita degli account e la gestione delle credenziali per gli account AD esistenti dell'utente, tra cui:In addition, Azure AD also provides account lifecycle management and credential management for the user's existing AD accounts, including:

* **Accesso remoto sicuro e accesso condizionale per le applicazioni localiSecure remote access and Conditional Access for on-premises applications**

Per molte organizzazioni, il primo passaggio nella gestione dell'accesso dal cloud per le applicazioni Web e remote desktop integrate in locale consiste nel distribuire il proxy di [applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) davanti a tali applicazioni per fornire l'accesso remoto sicuro.

Dopo un accesso singolo (SSO) ad Azure AD, gli utenti possono accedere sia alle applicazioni nel cloud che a quelle locali tramite un URL esterno o un portale per le applicazioni interno. Ad esempio, il proxy di applicazione fornisce l'accesso remoto e l'accesso Single Sign-On a Desktop remoto, SharePoint, nonché app come Tableau e Qlik e applicazioni line-of-business (LOB). Inoltre, i criteri di accesso condizionale possono includere la visualizzazione [delle condizioni per l'utilizzo](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) e [la garanzia che l'utente abbia accettato](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou) di utilizzarli prima di poter accedere a un'applicazione.

![Architettura del proxy dell'app](media/cloud-governed-management-for-on-premises/image2.png)

* **Gestione automatica del ciclo di vita per gli account di Active Directory**

La governance delle identità consente alle organizzazioni di raggiungere un equilibrio tra *la produttività* --- quanto velocemente una persona può avere accesso alle risorse di cui ha bisogno, ad esempio quando si unisce all'organizzazione? --- e *sicurezza* --- come dovrebbe cambiare il loro accesso nel tempo, ad esempio quando cambia lo stato di impiego di quella persona? La gestione del ciclo di vita delle identità è la base per la governance delle identità e per una governance efficace su larga scala occorre modernizzare l'infrastruttura di gestione del ciclo di vita delle identità per le applicazioni.

Per molte organizzazioni, il ciclo di vita delle identità per i dipendenti è legato alla rappresentazione di tale utente in un sistema di gestione del capitale umano (HCM). Per le organizzazioni che usano Workday come sistema HCM, Azure AD può garantire il provisioning automatico e il deprovisioning degli account utente in Active Directory [per i lavoratori in Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial). In questo modo si migliora la produttività degli utenti attraverso l'automazione degli account dei diritti di nascita e si gestisce il rischio garantendo che l'accesso alle applicazioni venga aggiornato automaticamente quando un utente cambia ruolo o lascia l'organizzazione. Il piano di [distribuzione](https://aka.ms/WorkdayDeploymentPlan) del provisioning degli utenti basato su Workday è una guida dettagliata che illustra le organizzazioni attraverso l'implementazione delle procedure consigliate di Workday fino alla soluzione di provisioning degli utenti di Active Directory in un processo in cinque fasi.

Azure AD Premium include anche Microsoft Identity Manager, che può importare record da altri sistemi HCM locali, inclusi SAP, Oracle eBusiness e Oracle PeopleSoft.

La collaborazione business-to-business richiede sempre più la concessione dell'accesso a persone esterne all'organizzazione. La collaborazione [B2B](https://docs.microsoft.com/azure/active-directory/b2b/) di Azure AD consente alle organizzazioni di condividere in modo sicuro le applicazioni e i servizi con gli utenti guest e i partner esterni, mantenendo il controllo sui propri dati aziendali.

Azure AD può [creare automaticamente gli account in Active Directory per gli utenti guest in](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) base alle esigenze, consentendo ai guest aziendali di accedere alle applicazioni integrate in Locale senza bisogno di un'altra password. Le organizzazioni possono impostare criteri di autenticazione a più fattori [(MFA) per gli utenti guest](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)s in modo che i controlli di autenticazione a più fattori vengono eseguiti durante l'autenticazione proxy di applicazione. Inoltre, tutte [le verifiche](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) di accesso eseguite sugli utenti B2B cloud si applicano agli utenti locali. Ad esempio, se l'utente cloud viene eliminato tramite i criteri di gestione del ciclo di vita, viene eliminato anche l'utente locale.

**Gestione delle credenziali per gli account** di Active Directory La reimpostazione della password self-service di Azure AD consente agli utenti che hanno dimenticato le password di essere autenticati e reimpostare le password, con le password modificate [scritte in Active Directory locale.](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback) Il processo di reimpostazione della password può anche utilizzare i criteri password di Active Directory locali: quando un utente reimposta la password, viene controllato per verificare che soddisfi i criteri di Active Directory locali prima di eseguirne il commit in tale directory. Il piano di [distribuzione](https://aka.ms/deploymentplans/sspr) per la reimpostazione della password self-service descrive le procedure consigliate per implementare la reimpostazione della password self-service agli utenti tramite esperienze Web e integrate in Windows.The self-service password reset deployment plan outlines best practices to deploy out self-service password reset to users via web and Windows-integrated experiences.

![Architettura SSPR di Azure ADAzure AD SSPR architecture](media/cloud-governed-management-for-on-premises/image3.png)

Infine, per le organizzazioni che consentono agli utenti di modificare le password in Active Directory, Active Directory può essere configurato per utilizzare gli stessi criteri password utilizzati dall'organizzazione in Azure AD tramite la funzionalità di protezione con password di [Azure AD,](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises)attualmente in anteprima pubblica.

Quando un'organizzazione è pronta a spostare un'applicazione integrata con Active Directory nel cloud spostando il sistema operativo che ospita l'applicazione in Azure, [Servizi di dominio Azure AD](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) offre servizi di dominio compatibili con Active Directory (ad esempio aggiunta a un dominio, criteri di gruppo, LDAP e autenticazione Kerberos/NTLM). Servizi di dominio Azure AD si integra con il tenant di Azure AD esistente dell'organizzazione, consentendo agli utenti di accedere usando le credenziali aziendali. Inoltre, i gruppi esistenti e gli account utente possono essere usati per proteggere l'accesso alle risorse, garantendo un "lift-and-shift" più agevole delle risorse locali ai servizi di infrastruttura di Azure.Additionally, existing groups and user accounts can be used to secure access to resources, ensuring a smomore 'lift-and-shift' delle risorse locali ai servizi di infrastruttura di Azure.Additionally, existing groups and user accounts can be used to secure access to resources, ensuring a smomore'" delle risorse locali ai servizi di infrastruttura di Azure.Additionally

![Servizi di dominio Azure Active Directory](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Gestione gestita dal cloud per le applicazioni basate sulla federazione locale

Per un'organizzazione che usa già un provider di identità locale, lo spostamento delle applicazioni in Azure AD consente un accesso più sicuro e un'esperienza amministrativa più semplice per la gestione della federazione. Azure AD consente di configurare controlli di accesso granulari per applicazione, tra cui Azure Multi-Factor Authentication, usando l'accesso condizionale di Azure AD. Azure AD supporta più funzionalità, inclusi i certificati per la firma di token specifici dell'applicazione e le date di scadenza dei certificati configurabili. Queste funzionalità, strumenti e linee guida consentono alle organizzazioni di ritirare i provider di identità locali. L'IT di Microsoft, ad esempio, ha spostato 17.987 applicazioni da Active Directory Federation Services (ADFS) interno di Microsoft ad Azure AD.

![Evoluzione di Azure ADAzure AD evolution](media/cloud-governed-management-for-on-premises/image5.png)

Per iniziare la migrazione di applicazioni federate ad https://aka.ms/migrateapps Azure AD come provider di identità, fare riferimento a che include collegamenti a:To begin migrating federated applications to Azure AD as the identity provider, refer to that includes links to:

* Il white paper [Migrazione delle applicazioni ad Azure Active Directory](https://aka.ms/migrateapps/whitepaper), che presenta i vantaggi della migrazione e descrive come pianificare la migrazione in quattro fasi chiaramente delineate: individuazione, classificazione, migrazione e gestione continuativa. Sarete guidati attraverso come pensare al processo e abbattere il progetto in pezzi facili da consumare. Nel documento vengono forniti collegamenti a importanti risorse di supporto per l'utente durante il processo.

* La guida alla soluzione [Migrating Application Authentication from Active Directory Federation Services to Azure Active Directory](https://aka.ms/migrateapps/adfssolutionguide) esplora in modo più dettagliato le stesse quattro fasi della pianificazione e dell'esecuzione di un progetto di migrazione delle applicazioni. In questa guida verrà illustrato come applicare tali fasi all'obiettivo specifico di spostare un'applicazione da Active Directory Federation Services (ADFS) ad Azure AD.

* Lo script di preparazione alla migrazione di [Active Directory Federation Services](https://aka.ms/migrateapps/adfstools) può essere eseguito nei server Active Directory Federation Services (ADFS) locali per determinare la disponibilità delle applicazioni per la migrazione ad Azure AD.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Gestione continua degli accessi nelle applicazioni cloud e locali

Le organizzazioni hanno bisogno di un processo per gestire l'accesso scalabile. Gli utenti continuano ad accumulare i diritti di accesso e finiscono per al di là di ciò che è stato inizialmente eseguito il provisioning per loro. Inoltre, le organizzazioni aziendali devono essere in grado di scalare in modo efficiente per sviluppare e applicare i criteri di accesso e i controlli su base continuativa.

In genere, l'IT delega le decisioni in merito all'approvazione dell'accesso ai decision maker aziendali. Inoltre, l'IT può comprendere gli utenti stessi. Ad esempio, gli utenti che accedono a dati riservati dei clienti nell'applicazione di marketing di un'azienda in Europa devono conoscere i criteri dell'azienda. Gli utenti guest potrebbero anche non essere a conoscenza dei requisiti di gestione per i dati in un'organizzazione a cui sono stati invitati.

Le organizzazioni possono automatizzare il processo del ciclo di vita di accesso tramite tecnologie quali [gruppi dinamici,](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)insieme al provisioning degli utenti per [le applicazioni SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)o alle applicazioni integrate utilizzando lo standard [SCIM (System for Cross-Domain Identity Management).](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups) Le organizzazioni possono inoltre controllare quali [utenti guest hanno accesso alle applicazioni locali.](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) I diritti di accesso possono quindi essere riesaminati regolarmente usando le [verifiche di accesso ricorrenti di Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview).

## <a name="future-directions"></a>Indicazioni future

Negli ambienti ibridi, la strategia di Microsoft consiste nell'abilitare le distribuzioni in cui il cloud è il piano di **controllo per l'identità**e le directory locali e altri sistemi di identità, ad esempio Active Directory e altre applicazioni locali, sono la destinazione per il provisioning degli utenti con accesso. Questa strategia continuerà a garantire i diritti, le identità e l'accesso in tali applicazioni e carichi di lavoro che si basano su di essi. A questo scopo, le organizzazioni saranno in grado di aumentare la produttività degli utenti finali interamente dal cloud.

![Architettura di Azure AD](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come iniziare questo percorso, vedere i <https://aka.ms/deploymentplans> piani di distribuzione di Azure AD, disponibili all'indirizzo . Forniscono indicazioni end-to-end su come distribuire le funzionalità di Azure Active Directory (Azure AD). Ogni piano illustra il valore aziendale, le considerazioni sulla pianificazione, la progettazione e le procedure operative necessarie per implementare correttamente le funzionalità comuni di Azure AD. Microsoft aggiorna continuamente i piani di distribuzione con le procedure consigliate apprese dalle distribuzioni dei clienti e altri commenti e suggerimenti quando aggiungiamo nuove funzionalità alla gestione dal cloud con Azure AD.
