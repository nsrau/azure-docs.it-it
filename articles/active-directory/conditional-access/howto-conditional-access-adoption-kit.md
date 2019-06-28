---
title: Kit adozione di accesso condizionale - Azure Active Directory
description: Adozione di accesso condizionale di Azure AD per accedere alle risorse
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cc4ff5fb528760be8c910f3da7d5691a6aae0d8
ms.sourcegitcommit: a7ea412ca4411fc28431cbe7d2cc399900267585
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/25/2019
ms.locfileid: "67387573"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Adozione di accesso condizionale di Azure AD

In un mondo incentrato su dispositivi mobili e cloud-first, gli utenti possono accedere alle risorse aziendali da qualsiasi posizione tramite diversi tipi di dispositivi e App. Di conseguenza, concentrarsi solo su chi può accedere a una risorsa non è più sufficiente. È possibile controllare chi può accedere e identificare dove l'utente è e quali dispositivi sono in uso e molto altro ancora.

Per questo controllo, fornire **accesso condizionale di Azure Active Directory (AD)** consente di specificare le condizioni di tutti gli utenti devono soddisfare per l'accesso a un'applicazione, ad esempio multi-Factor Authentication (MFA). Usando i criteri di accesso condizionale consente di controllare gli utenti autorizzati come (gli utenti che hanno ottenuti l'accesso a un'app cloud) accedere alle App cloud in condizioni specifiche. Fare riferimento a [qual è l'accesso condizionale in Azure Active Directory](overview.md#conditional-access-policies) per altre informazioni.

## <a name="key-benefits"></a>Vantaggi principali

Vantaggi principali dell'uso di accesso condizionale di Azure AD sono:

* **Per aumentare la produttività:** I criteri di accesso (CA) condizionale consentono di assegnare il punto in cui gli utenti vengono richiesto di utilizzare autenticazione a più fattori, hanno accesso bloccato o sono necessari per usare un dispositivo attendibile. Ad esempio, è possibile impostare i criteri, ad esempio richiedendo solo agli utenti di MFA in un'applicazione quando si è connessi alla rete aziendale. Riducendo le richieste di autenticazione a più fattori consente di mantenere gli utenti più produttiva rispetto al se hanno autenticazione a più fattori ogni volta che effettuano l'accesso. Inoltre, l'accesso condizionale di Azure AD consente di specificare i criteri per ogni singolo utente e crea anche i criteri specifici dell'app.
* **Gestire i rischi:** Abilitazione dei criteri di accesso condizionale offre la protezione dell'identità con scalabilità cloud, funzionalità di controllo di accesso basato sui rischi e supporto per l'autenticazione a più fattori nativa. Accoppiamento di accesso condizionale con identity protection consente di definire quando si è bloccato o controllato l'accesso a un'applicazione.
* **Governance e conformità di indirizzo:** Il controllo delle richieste di accesso e delle approvazioni per l'applicazione e informazioni sull'utilizzo di applicazione nel suo complesso è più semplice con Azure AD, perché supporta i log di controllo nativo per ogni richiesta di accesso dell'applicazione eseguita. Il controllo include l'identità del richiedente, data richiesta, una motivazione aziendale, lo stato di approvazione e identità del responsabile approvazione. Questi dati sono inoltre disponibili da un'API, in modo da consentire l'importazione dei dati in un evento imprevisto della sicurezza e il sistema di monitoraggio di eventi (SIEM) preferito.
* **Gestire i costi:** Lo spostamento di criteri di accesso ad Azure AD consente di ridurre affidamento personalizzate o soluzioni locali, ad esempio Active Directory Federation Services (ADFS) per l'accesso condizionale, riducendo i costi dell'esecuzione di tale infrastruttura.

## <a name="customer-case-studies"></a>Casi di studio sui clienti

Scopri come la maggior parte delle organizzazioni usano l'accesso condizionale di Azure AD per definire e implementare il controllo di accesso automatizzato più accedere alle App cloud in base alle condizioni. Le storie in primo piano seguenti viene illustrato come vengono soddisfatte le esigenze dei clienti.

* [**Wipro** contribuiscono alla produttività per dispositivi mobili con gli strumenti di sicurezza cloud Microsoft per migliorare impegni dei clienti.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) I criteri di accesso condizionale in Azure AD hanno consentito all'azienda di condividere documenti, risorse e le applicazioni con le entità esterne attendibili---che può usare le proprie credenziali---mantenendo il controllo sui propri dati aziendali.
* [**Accenture** protegge il passaggio al cloud con Microsoft Cloud App security](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security) Accenture sta valutando il [Conditional Access App Control](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) funzionalità di Cloud App Security, che usa Azure Active Accesso condizionale di directory per controllare l'accesso dell'applicazione in base a determinate condizioni. LePenske afferma che questa funzionalità può essere utile, ad esempio, abilitare l'accesso ai file di sola lettura mentre impedisce l'impiego di download.
* [**Aramex** recapito limitato - aziendali globali di trasporti e logistica crea connesse al cloud di office con soluzione di gestione delle identità e degli accessi](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Per garantire l'accesso sicuro è stata particolarmente difficile con i dipendenti remoti dell'Aramex. La società è ora l'accesso condizionale per consentire a questi dipendenti remoti di accedere alle applicazioni SaaS dall'esterno della rete. La regola di accesso condizionale verrà decidere se imporre multi-Factor Authentication, che concede solo le persone autorizzate all'accesso a destra.

Per altre informazioni sulle esperienze dei clienti e partner sull'accesso condizionale di Azure AD, vedere - [vedere le grandi cose che stanno usando Azure](https://azure.microsoft.com/case-studies/?service=active-directory).

## <a name="announcements"></a>Annunci

Azure AD viene regolarmente migliorato. Per rimanere aggiornati con gli sviluppi più recenti, vedere [novità in Azure Active Directory?](../fundamentals/whats-new.md)

Blog recenti dal Tech Community e Microsoft Identity Division:

* 24 settembre 2018 [accesso condizionale di Azure Active Directory in Azure Databricks](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 21 settembre 2018 [accesso condizionale di Azure AD, i controlli personalizzati sono in anteprima pubblica](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 21 settembre 2018 [supporto dell'accesso condizionale di Azure AD per l'accesso limitato con Microsoft Cloud App Security è ora disponibile](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 21 settembre 2018 [l'accesso condizionale di Azure AD: Supporto browser per le piattaforme iOS/Android ora disponibile in anteprima gestiti](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 21 settembre 2018 [l'accesso condizionale di Azure AD per i codici paese è disponibile in anteprima pubblica](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 21 settembre 2018 [AD Azure le condizioni d'uso è ora disponibile](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>Risorse di formazione

Seguire i collegamenti seguenti per ottenere una panoramica delle funzioni di accesso condizionale AD Azure.

* Informazioni su "[qual è l'accesso condizionale in Azure Active Directory?](overview.md)"
* Conoscere "[quali sono le condizioni in Active Directory accesso condizionale di Azure?](conditions.md)"
* Conoscere "[qual è la condizione della posizione in Active Directory accesso condizionale di Azure?](location-condition.md)"
* Conoscere "[quali sono l'accesso consente di controllare in Azure Active Directory condizionale Access?](controls.md)"
* Trovare "[che cos'è la cosa se strumento in Azure Active Directory l'accesso condizionale?"](what-if-tool.md)
* Seguire [procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md)

Inoltre, vedere i collegamenti seguenti per informazioni aggiuntive proteggere l'accesso a tutti i servizi che vengono integrate con Azure Active Directory.

* [Che cos'è la protezione di base (anteprima)?](baseline-protection.md) Protezione di base garantisce che almeno il livello di base di sicurezza abilitato Azure Active Directory nell'ambiente in uso.
* [Identità e dispositivo di accedere alle configurazioni](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations). Viene descritto come configurare l'accesso sicuro ai servizi cloud tramite Enterprise Mobility + prodotti per la sicurezza mediante l'implementazione di un ambiente consigliato e la configurazione, incluso un set prestabilito di criteri di accesso condizionale e le funzionalità correlate.
* [Riferimento alle impostazioni di accesso condizionale di Active Directory Azure](technical-reference.md). Informazioni su:
   * Quali App usano l'accesso condizionale?
   * Quali servizi sono abilitati con l'accesso condizionale?
* [Abilitare l'accesso condizionale di Azure Active Directory per l'accesso sicuro](https://www.youtube.com/watch?v=eLAYBwjCGoA). Guarda questo video per scoprire come svolge un ruolo di accesso condizionale nei carichi di lavoro di altri Enterprise Mobility Suite.

### <a name="training-videos"></a>Video relativi alla formazione

**Accesso condizionale in Enterprise Mobility + Security**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**Accesso condizionale basato su dispositivo**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**Abilitare Azure Active Directory per l'accesso condizionale per l'accesso sicuro**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>Corsi online

Vedere i seguenti corsi di accesso condizionale e altro ancora su [Pluralsight.com](https://www.pluralsight.com/):

* Sito Pluralsight.com: [Struttura Identity Management in Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * "Questo corso illustra gli elementi chiave che devi sapere per progettare la soluzione di gestione identità con Azure AD." Accesso condizionale di Azure AD viene descritta in "Using ruoli e controllo di accesso con Azure AD" modulo.

* Sito Pluralsight.com: [Autenticazione di progettazione per Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * "Questo corso illustra come usare Azure AD per risolvere tutti i requisiti dell'autenticazione cloud." Accesso condizionale di Azure AD è illustrata nel modulo "Requisiti per diversi scenari di autenticazione".

* Sito Pluralsight.com: [Autorizzazione di progettazione per Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * "Questo corso illustra le opzioni di autorizzazione disponibili con Azure e Azure AD." Accesso condizionale di Azure AD è illustrata nel modulo di "Autorizzazione con Azure AD e Azure Resource Manager".

### <a name="books"></a>Documentazione

* O ' Reilly - [implementazione di soluzioni di Azure - Second Edition.](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * "Ottieni operatività con servizi di Azure e Scopri come implementarle nella propria organizzazione. Accesso condizionale di Azure AD viene descritta nel capitolo [alla distribuzione e la sincronizzazione di Azure Active Directory](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml). "

* Wiley- [Mastering servizi infrastruttura di Microsoft Azure](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * "Here is tutto quello che devi comprendere, valutare, distribuire e gestire ambienti che utilizzano Microsoft Azure."

## <a name="white-papers"></a>White paper

* Pubblicato 18 dicembre 2018 [creare una strategia di gestione di controllo di accesso resiliente con Azure Active Directory](../authentication/concept-resilient-controls.md)
   * Questo documento fornisce materiale sussidiario sulle strategie di un'organizzazione potrebbe adottare per garantire la resilienza per ridurre il rischio di errori di blocco durante interruzioni impreviste.

* Pubblicato il 18 settembre 2018 [risorse per la migrazione delle applicazioni ad Azure Active Directory](../manage-apps/migration-resources.md)
   * Questo white paper include un elenco di risorse che consentono di eseguire la migrazione di accesso alle applicazioni e l'autenticazione ad Azure Active Directory (Azure AD).

* Pubblicato il 12 luglio 2018 [Azure Security and Compliance Blueprint: Applicazione Web PaaS di Hosting per i carichi di lavoro ufficiale nel Regno Unito](../../security/blueprints/ukofficial-paaswa-overview.md)
   * I progetti di Azure sono costituiti da documenti con linee guida e modelli di automazione che consentono di distribuire architetture basate sul cloud per offrire soluzioni per scenari con requisiti di accreditamento o di conformità.

## <a name="guidance-for-it-administrators"></a>Linee guida per gli amministratori IT

Accedi per il [portale di Azure](https://portal.azure.com/) come un amministratore globale, amministratore della sicurezza o amministratore di accesso condizionale. Fare riferimento a [autorizzazioni del ruolo amministratore in Azure Active Directory.](../users-groups-roles/directory-assign-admin-roles.md)

Gli amministratori IT, utilizzare [accesso condizionale di Azure AD](overview.md) per richiedere agli utenti di eseguire l'autenticazione tramite Azure multi-Factor Authentication, accedere da una rete attendibile, o un dispositivo attendibile.

Ecco i collegamenti utili che consentono di iniziare:

* [Le procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md)
* [Verifiche di accesso di Usa Azure AD per gestire gli utenti che sono stati esclusi dai criteri di accesso condizionale](../governance/conditional-access-exclusion.md)
* [Procedure: Pianificare la distribuzione di accesso condizionale in Azure Active Directory](plan-conditional-access.md)
* [Avvio rapido: Richiedere l'autenticazione MFA per App specifiche con Azure Active Directory l'accesso condizionale](app-based-mfa.md)
* [Avvio rapido: Richiedere l'accettazione delle condizioni per l'utilizzo prima dell'accesso alle app cloud](require-tou.md)
* [Avvio rapido: Bloccare l'accesso quando viene rilevato un rischio per la sessione con Azure Active Directory l'accesso condizionale](app-sign-in-risk.md)
* [Domande frequenti sull'accesso condizionale di Azure AD](faqs.md)
   * Per ulteriori domande, è anche possibile visualizzare il [forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure).
   * Se non si trova la risposta a un problema, i team di supporto sono sempre disponibili per offrire maggiore assistenza. Uso [supporto tecnico Microsoft di contatto](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support).

### <a name="tutorials"></a>Esercitazioni

* [**Avvio rapido: Richiedere l'autenticazione MFA per App specifiche con Azure Active Directory l'accesso condizionale**](app-based-mfa.md)
   * Questa Guida introduttiva illustra come configurare un criterio di accesso condizionale di Azure AD che richiede l'autenticazione a più fattori per un'app cloud selezionate nell'ambiente in uso.

* [**Avvio rapido: Richiedere le condizioni d'uso da accettare prima di accedere all'App cloud**](require-tou.md)
   * Questa Guida introduttiva illustra come configurare un criterio di accesso condizionale di Azure AD che richiede un ToU venga accettata per un'app cloud selezionate nell'ambiente in uso.

* [**Avvio rapido: Bloccare l'accesso quando viene rilevato un rischio per la sessione con Azure Active Directory l'accesso condizionale**](app-sign-in-risk.md)
   * Questa Guida introduttiva illustra come configurare un criterio di accesso condizionale che blocca l'accesso quando viene rilevato un livello di rischio di accesso configurati.

* [Esercitazione: **Eseguire la migrazione di criteri classici che richiedono l'autenticazione a più fattori nel portale di Azure**](policy-migration-mfa.md)
   * Questa esercitazione illustra come eseguire la migrazione di un criterio classico che richiede l'autenticazione a più fattori (MFA) per un'app cloud.

## <a name="end-user-readiness-and-communication"></a>Comunicazione e la conformità degli utenti finali

Accesso condizionale Usa altre funzionalità di Azure AD che potrebbero incidere sull'esperienza dell'utente finale. Ad esempio, è possibile usare Azure multi-factor authentication per abilitare l'autenticazione avanzata per gli utenti. In tal caso, si utilizzerà i modelli di dati gestito dall'utente di Azure MFA.

## <a name="next-steps"></a>Passaggi successivi

* Avviare la distribuzione con il [distribuzione di accesso condizionale documentazione sulla pianificazione](plan-conditional-access.md).
