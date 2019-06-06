---
title: Cloud di Azure AD è regolato gestione per i carichi di lavoro On-Premises - Azure
description: In questo argomento viene descritta la gestione cloud regolati per carichi di lavoro in locale.
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
ms.openlocfilehash: 3b7e54f6acfe1cbbe6e46fe92d132ebdaa91ff33
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66742349"
---
# <a name="how-azure-ad-delivers-cloud-governed-management-for-on-premises-workloads"></a>Come Azure AD offre Cloud disciplinato gestione per i carichi di lavoro in locale

Azure Active Directory (Azure AD) è un'identità completa come soluzione di servizio (IDaaS) usata da milioni di organizzazioni che si estendono su tutti gli aspetti di identità, gestione degli accessi e sicurezza. Contiene le identità degli utenti oltre un miliardo e consente agli utenti di accedere e accedere in modo sicuro sia di Azure AD:

* Risorse esterne, ad esempio il portale di Azure, Microsoft Office 365 e migliaia di altre applicazioni Software-as-a-Service (SaaS).
* Risorse interne, ad esempio le applicazioni nella rete aziendale e intranet, insieme a tutte le applicazioni cloud sviluppato da tale organizzazione di un'organizzazione.

Se sono 'cloud pura' o come un "ibrido" distribuzione se dispongono di un'istanza locale i carichi di lavoro, le organizzazioni possono usare Azure AD. Una distribuzione ibrida di Azure Active Directory può far parte di una strategia per un'organizzazione, la migrazione nel cloud gli asset IT o continuare integrare l'infrastruttura locale esistente con nuovi servizi cloud.

In passato, le organizzazioni "ibrido" hanno assistito AD Azure come un'estensione della propria infrastruttura locale. In queste distribuzioni, l'amministrazione della governance delle identità in locale, Windows Server Active Directory o altri sistemi di directory interne sono i punti di controllo e gli utenti e gruppi sincronizzati da questi sistemi in una directory cloud come Azure AD. Dopo le identità sono nel cloud, si può essere reso disponibile in Office 365, Azure e altre applicazioni.

![Ciclo di vita delle identità](media/cloud-governed-management-for-on-premises//image1.png)

Quando le organizzazioni spostano informazioni della propria infrastruttura IT con le loro applicazioni nel cloud, molti cercano le migliorate funzionalità di sicurezza e gestione semplificata della gestione delle identità come servizio. Le funzionalità IDaaS fornita dal cloud di Azure AD accelerare la transizione a cloud management regolamentati, fornendo le funzionalità che consentono alle organizzazioni di adottare rapidamente e spostare più la gestione di identità dal tradizionale in locale e soluzioni sistemi in Azure AD, pur continuando a supportare le applicazioni esistenti, nonché nuove.

Questo documento descrive la strategia Microsoft per la gestione ibrida IDaaS e descrive come le organizzazioni possono usare Azure AD per le applicazioni esistenti.

## <a name="the-azure-ad-approach-to-cloud-governed-identity-management"></a>L'approccio di Azure AD per il cloud di gestione delle identità regolamentati

Transizione di organizzazioni nel cloud, sono necessarie garanzie che abbiano i controlli sul proprio ambiente completo - più visibilità all'attività, supportato da automazione e proattivi informazioni dettagliate e maggiore sicurezza. "**Cloud disciplinato gestione**" viene descritto come le organizzazioni la gestione e determinano i loro utenti, applicazioni, gruppi e dispositivi dal cloud.

In questo mondo moderno, le organizzazioni devono essere in grado di gestire in modo efficace su larga scala, a causa la proliferazione di applicazioni SaaS e il ruolo sempre crescente di collaborazione e le identità esterne. Il nuovo panorama dei rischi del cloud significa che un'organizzazione deve essere più reattiva, un attore malintenzionato che compromette un utente cloud influiscono sulle applicazioni cloud e locali.

In particolare, ibrido, le organizzazioni devono essere in grado di delegare e automatizzare le attività, che in passato IT ha manualmente. Per automatizzare le attività, sono necessarie le API e i processi che Orchestra il ciclo di vita delle risorse correlate a identità diverse (utenti, gruppi, applicazioni, i dispositivi), pertanto possono delegare la gestione quotidiana di tali risorse a più persone all'esterno di IL personale IT di base. Azure Active Directory affronta questi requisiti con la gestione degli account utente e l'autenticazione nativa per gli utenti senza richiedere identità infrastruttura locale. Non la creazione di infrastruttura on-premises possono sfruttare le organizzazioni che dispongono di nuove Comunità di utenti, ad esempio i partner commerciali, che non ha avuto origine nella propria directory locale, ma la cui gestione di accesso è fondamentale per ottenere i risultati aziendali.

Inoltre, gestione non viene completata senza supervisione--- e governance in questo nuovo mondo è parte integrante del sistema di identità anziché un componente aggiuntivo. Governance delle identità offre alle organizzazioni la possibilità di gestire l'identità e del ciclo di vita di accesso tra i dipendenti, partner aziendali e fornitori e servizi e applicazioni.

Incorporazione di governance delle identità rende più semplice consentire all'organizzazione di transizione al cloud governato management, consente di IT per la scalabilità, risponde alle nuove sfide con gli utenti Guest e consente più profondo insights e l'automazione a quali clienti hanno riscontrato con infrastruttura locale. Governance in questo nuovo assetto significa la possibilità per organizzazioni che hanno la trasparenza, visibilità e controlli appropriati per l'accesso alle risorse all'interno dell'organizzazione. Con Azure AD, operazioni di sicurezza e controllo team hanno accesso visibilità chi--- e quali utenti devono disporre - per le risorse nell'organizzazione (su quali dispositivi), stanno con tale accesso, gli utenti e che l'organizzazione abbia e Usa appropriato controlli per rimuovere o limitare l'accesso in base ai criteri normativi o aziendali.

Il nuovo modello di gestione è vantaggiosa per le organizzazioni con le applicazioni e SaaS line-of-business (LOB), così come sono più facilmente in grado di gestire e proteggere l'accesso a tali applicazioni. Grazie all'integrazione di applicazioni con Azure AD, le organizzazioni saranno in grado di usare e gestire l'accesso tra cloud e locale ha avuto origine le identità in modo coerente. Gestione del ciclo di vita dell'applicazione diventa più automatizzata e Azure AD fornisce informazioni dettagliate avanzate all'utilizzo dell'applicazione che non era facile ottenere nella gestione delle identità in locale. Tramite Azure AD, gruppi di Office 365 e funzionalità self-service, i team le organizzazioni con facilità possono creare gruppi per la gestione degli accessi e la collaborazione e aggiungere o rimuovere gli utenti nel cloud per la collaborazione e gestione dei requisiti di accesso.

Se si seleziona le funzionalità di Azure AD a destra per cloud management governato varia a seconda delle applicazioni da usare e come tali applicazioni saranno integrate con Azure AD. Le sezioni seguenti descrivono gli approcci per richiedere per le applicazioni integrata in Active Directory e le applicazioni che usano protocolli di federazione (ad esempio, SAML, OAuth o OpenID Connect).

## <a name="cloud-governed-management-for-ad-integrated-applications"></a>Gestione del cloud regolati per le applicazioni integrate in AD

Azure AD consente di migliorare la gestione per in locale un'organizzazione le applicazioni integrate in Active Directory tramite accesso remoto sicuro e l'accesso condizionale alle applicazioni in questione. Inoltre, Azure AD offre anche gestione del ciclo di vita degli account e gestione delle credenziali per l'account AD esistente dell'utente, tra cui:

* **Accesso remoto sicuro e l'accesso condizionale per applicazioni locali**

Per molte organizzazioni, il primo passaggio nella gestione dell'accesso dal cloud per il web integrata in Active Directory in locale e le applicazioni basate su desktop remote consiste nel distribuire il [proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy) davanti a tali applicazioni per fornire protezione accesso remoto.

Dopo un accesso singolo (SSO) ad Azure AD, gli utenti possono accedere sia alle applicazioni nel cloud che a quelle locali tramite un URL esterno o un portale per le applicazioni interno. Ad esempio, il Proxy di applicazione fornisce accesso remoto e single sign-on per Desktop remoto, SharePoint, nonché App, ad esempio Tableau e Qlik e applicazioni line of business (LOB). Inoltre, i criteri di accesso condizionale possono includere la visualizzazione di [le condizioni d'uso](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou) e [garantire che l'utente ha acconsentito a tali](https://docs.microsoft.com/azure/active-directory/conditional-access/require-tou) prima di poter accedere a un'applicazione.

![Architettura del Proxy di App](media/cloud-governed-management-for-on-premises/image2.png)

* **Gestione del ciclo di vita automatica per gli account di Active Directory**

Governance delle identità consente alle organizzazioni di raggiungere un equilibrio tra *produttività* ---rapidità con cui una persona può avere accesso alle risorse necessarie, ad esempio quando questi vengono aggiunti all'organizzazione? --- e *sicurezza* ---deve il loro accesso variazione nel tempo, ad esempio quando cambia lo stato di occupazione della persona? La gestione del ciclo di vita delle identità è la base per la governance delle identità e per una governance efficace su larga scala occorre modernizzare l'infrastruttura di gestione del ciclo di vita delle identità per le applicazioni.

Per molte organizzazioni, ciclo di vita di identità per dipendenti è associato alla rappresentazione dell'utente in un sistema di gestione risorse umane (HCM). Per le organizzazioni che usano Workday come proprio sistema di gestione connessione ibrida, Azure AD può verificare gli account utente in Active Directory siano [automaticamente il provisioning e deprovisioning per gli utenti in Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial). In tal caso lead per la produttività dell'utente migliorata grazie all'automazione di birthright account e gestisce i rischi per garantire l'applicazione l'accesso viene aggiornato automaticamente quando un utente cambia i ruoli o lascia l'organizzazione. Il provisioning utenti Workday-driven [piano di distribuzione](https://aka.ms/WorkdayDeploymentPlan) costituisce una Guida dettagliata che descrive le organizzazioni tramite l'implementazione di procedure ottimo di Workday per la soluzione di Provisioning utenti Active Directory in un processo in cinque passaggi.

Azure AD Premium include anche Microsoft Identity Manager, che consente di importare i record da altri sistemi di gestione connessione ibrida in locale, ad esempio SAP, Oracle e-business e Oracle PeopleSoft.

Collaborazione Business-to-business richiede sempre più la concessione dell'accesso a utenti esterni all'organizzazione. [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) collaborazione consente alle organizzazioni di condividere in modo sicuro alle applicazioni e servizi con gli utenti guest e partner esterni, mantenendo il controllo sui propri dati aziendali.

Azure AD possono [crea automaticamente account in Active Directory per gli utenti guest](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises) in base alle esigenze, consentendo agli utenti guest aziendali per accedere a integrate in AD applicazioni locali senza la necessità di un'altra password. Le organizzazioni possono configurare [i criteri di autenticazione a più fattori (MFA) per l'utente guest](https://docs.microsoft.com/azure/active-directory/b2b/conditional-access)s in modo che MFA controlli vengono eseguiti durante l'autenticazione proxy di applicazione. Inoltre, qualsiasi [verifiche di accesso](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews) che vengono eseguite nel cloud B2B gli utenti si applicano agli utenti in locale. Se l'utente cloud viene eliminato tramite i criteri di gestione del ciclo di vita, ad esempio, viene eliminato anche l'utente locale.

**Credenziali di gestione per gli account di Active Directory** di Azure AD self-service la reimpostazione della password consente agli utenti che hanno dimenticato la password per essere autenticata e reimpostare le password, con le password modificate [scritti in Active Directory locale](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-writeback). Il processo di reimpostazione della password è anche possibile usare i criteri password di Active Directory locale: Quando un utente Reimposta la password, viene controllato per verificare che soddisfi i criteri di Active Directory in locale prima di eseguirne il commit a tale directory. Reimpostazione della password self-service [piano di distribuzione](https://aka.ms/deploymentplans/sspr) descrive le procedure consigliate per implementare self-service la reimpostazione della password agli utenti tramite esperienze integrate di Windows e web.

![Architettura di Azure AD SSPR](media/cloud-governed-management-for-on-premises/image3.png)

Infine, per le organizzazioni che consentono agli utenti di modificare la password in Active Directory, Active Directory può essere configurato per usare gli stessi criteri di password come l'organizzazione Usa in Azure AD tramite il [funzionalità di protezione di Azure AD password](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad-on-premises), attualmente in versione di anteprima pubblica.

Quando un'organizzazione è pronta per lo spostamento del sistema operativo che ospita l'applicazione in Azure, spostare un'applicazione integrata in Active Directory nel cloud [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-overview) offre servizi di dominio compatibile con Active Directory (ad esempio aggiunta a un dominio, criteri di gruppo, LDAP e Kerberos/NTLM, autenticazione). Azure Active Directory Domain Services si integra con il tenant Azure AD esistente dell'organizzazione, rendendo possibile per gli utenti di accedere usando le proprie credenziali aziendali. Inoltre, i gruppi esistenti e gli account utente sono utilizzabile per proteggere l'accesso alle risorse, garantendo un più uniforme "lift-and-shift' delle risorse locali a servizi di infrastruttura di Azure.

![Servizi di dominio Azure Active Directory](media/cloud-governed-management-for-on-premises/image4.png)

## <a name="cloud-governed-management-for-on-premises-federation-based-applications"></a>Gestione del cloud regolati per applicazioni locali basata su federazione

Per un'organizzazione che usa già un provider di identità in locale, spostare le applicazioni in Azure AD consente l'accesso più sicuro e un'esperienza amministrativa semplificata per la gestione della federazione. Azure AD consente la configurazione di controlli di accesso per applicazione granulari, tra cui Azure multi-Factor Authentication, usando l'accesso condizionale di Azure AD. Azure AD supporta altre funzionalità, tra cui certificati di firma del token di specifiche dell'applicazione e date di scadenza dei certificati configurabili. Queste funzionalità, strumenti e linee guida consentono alle organizzazioni di ritirare i provider di identità in locale. Di Microsoft IT, per un esempio, è stato spostato 17,987 applicazioni da Microsoft interna Active Directory Federation Services (ADFS) in Azure AD.

![Evoluzione di Azure AD](media/cloud-governed-management-for-on-premises/image5.png)

Per iniziare la migrazione delle applicazioni federate con Azure AD come provider di identità, fare riferimento a https://aka.ms/migrateapps che include collegamenti a:

* Il white paper [Migrating Your Applications ad Azure Active Directory](https://aka.ms/migrateapps/whitepaper), che presenta i vantaggi della migrazione e viene descritto come pianificare la migrazione in quattro fasi evidenziati chiaramente: individuazione, classificazione, la migrazione, e gestione continuativa. Segui le istruzioni considerare il processo e suddividere il progetto in parti facile da usare. Nel documento vengono forniti collegamenti a importanti risorse di supporto per l'utente durante il processo.

* La Guida alla soluzione [migrazione dell'applicazione di autenticazione da Active Directory Federation Services ad Azure Active Directory](https://aka.ms/migrateapps/adfssolutionguide) illustra in dettaglio lo stesso quattro fasi di pianificazione ed esecuzione di un progetto di migrazione dell'applicazione . In questa Guida verrà illustrato come applicare tali fasi per l'obiettivo specifico dello spostamento di un'applicazione da Active Directory Federation Services (ADFS) in Azure AD.

* Il [Script di preparazione di Active Directory Federation Services migrazione](https://aka.ms/migrateapps/adfstools) può essere eseguito nei server di Active Directory Federation Services (ADFS) in locale esistenti per determinare la conformità delle applicazioni per la migrazione ad Azure AD.

## <a name="ongoing-access-management-across-cloud-and-on-premises-applications"></a>Gestione continuativa degli accessi nelle applicazioni cloud e locali

Le organizzazioni necessitano di un processo per gestire l'accesso che è scalabile. Gli utenti continuano a accumulare i diritti di accesso e finire con oltre ciò che inizialmente è stato effettuato il provisioning per loro. Inoltre, le organizzazioni enterprise devono essere in grado di scalare in modo efficace per sviluppare e applicare criteri di accesso e i controlli in modo continuativo.

In genere, l'IT delega le decisioni in merito all'approvazione dell'accesso ai decision maker aziendali. Inoltre, l'IT può comprendere gli utenti stessi. Ad esempio, gli utenti che accedono a dati riservati dei clienti nell'applicazione di marketing di un'azienda in Europa devono conoscere i criteri dell'azienda. Gli utenti guest possono anche essere a conoscenza dei requisiti di gestione per i dati in un'organizzazione a cui sei stato invitati.

Le organizzazioni è possono automatizzare il processo di ciclo di vita accesso grazie alle tecnologie, ad esempio [gruppi dinamici](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership), insieme per il provisioning utenti [applicazioni SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list), o [applicazioni integrato con il System for Cross-Domain Identity Management (SCIM](https://docs.microsoft.com/azure/active-directory/manage-apps/use-scim-to-provision-users-and-groups)) standard. Le organizzazioni anche possono controllare quali [gli utenti guest hanno accesso alle applicazioni locali](https://docs.microsoft.com/azure/active-directory/b2b/hybrid-cloud-to-on-premises). I diritti di accesso possono quindi essere riesaminati regolarmente usando le [verifiche di accesso ricorrenti di Azure AD](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview).

## <a name="future-directions"></a>Sviluppi futuri

In ambienti ibridi, è la strategia Microsoft per consentire le distribuzioni in cui il **cloud è il piano di controllo per l'identità**e le directory locali e altri sistemi di identità, ad esempio Active Directory e altri in locale le applicazioni, vengono usati come destinazione per il provisioning degli utenti con accesso. Questa strategia continua a garantire i diritti, identità e accesso in tali applicazioni e carichi di lavoro che si basano su di esse. In questo stato finale, organizzazioni sarà in grado di incrementare la produttività degli utenti finali interamente dal cloud.

![Architettura di Azure AD](media/cloud-governed-management-for-on-premises/image6.png)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come iniziare a usare questo percorso, vedere i piani di distribuzione di Azure AD, che si trova in <https://aka.ms/deploymentplans> . Che offrono indicazioni end-to-end su come distribuire le funzionalità di Azure Active Directory (Azure AD). Ogni piano viene spiegato il valore di business, pianificazione considerazioni sulla progettazione e le procedure operative necessarie per distribuire correttamente le funzionalità comuni di Azure AD. Microsoft aggiorna continuamente i piani di distribuzione con le procedure consigliate apprese le distribuzioni dei clienti e altri commenti e suggerimenti quando si aggiungono nuove funzionalità per la gestione dal cloud con Azure AD.
