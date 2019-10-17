---
title: Kit di adozione dell'accesso condizionale-Azure Active Directory
description: Adozione Azure AD l'accesso condizionale per l'accesso alle risorse
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
ms.openlocfilehash: c34f59c3e9f679adf8ae410f648cb7de6dba6447
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72430047"
---
#  <a name="adopting-azure-ad-conditional-access"></a>Adozione Azure AD l'accesso condizionale

In un mondo per dispositivi mobili e cloud, gli utenti possono accedere alle risorse dell'organizzazione da qualsiasi luogo usando diversi tipi di dispositivi e app. Di conseguenza, concentrarsi solo su chi può accedere a una risorsa non è più sufficiente. È possibile controllare chi ha accesso e identificare dove si trova l'utente e quale dispositivo viene usato e molto altro ancora.

Per fornire questo controllo, l' **accesso condizionale Azure Active Directory (ad)** consente di specificare le condizioni che devono essere soddisfatte da qualsiasi utente per l'accesso a un'applicazione, ad esempio multi-factor authentication (multi-factor authentication). L'uso di criteri di accesso condizionale controlla come gli utenti autorizzati (utenti a cui è stato concesso l'accesso a un'app Cloud) accedono alle app cloud in condizioni specifiche. Per ulteriori informazioni, vedere l'articolo relativo all' [accesso condizionale in Azure Active Directory](overview.md) .

## <a name="key-benefits"></a>Vantaggi principali

I vantaggi principali dell'uso di Azure AD l'accesso condizionale sono:

* **Aumentare la produttività:** I criteri di accesso condizionale consentono di specificare come destinazione il punto in cui agli utenti viene richiesto di usare l'autenticazione a più fattori, avere accesso bloccato o essere necessari per usare un dispositivo attendibile. Ad esempio, è possibile impostare criteri come richiedere solo agli utenti di eseguire l'autenticazione a più fattori in un'applicazione quando si è fuori dalla rete aziendale. La riduzione delle richieste di autenticazione a più fattori consente agli utenti di aumentare la produttività rispetto a ogni volta che eseguono l'accesso. Inoltre, Azure AD accesso condizionale consente di specificare i criteri per ogni utente e creare anche criteri specifici dell'app.
* **Gestisci i rischi:** L'abilitazione dei criteri di accesso condizionale offre protezione delle identità a livello di cloud, funzionalità di controllo degli accessi in base al rischio e supporto nativo per l'autenticazione a più fattori. L'accoppiamento dell'accesso condizionale con Identity Protection consente di definire quando l'accesso a un'applicazione è bloccato o controllato.
* **Conformità e governance degli indirizzi:** Il controllo delle richieste di accesso e delle approvazioni per l'applicazione e la comprensione dell'utilizzo complessivo delle applicazioni è più semplice con Azure AD perché supporta i log di controllo nativi per ogni richiesta di accesso alle applicazioni eseguita. Il controllo include identità del richiedente, data richiesta, motivazione aziendale, stato di approvazione e identità del responsabile approvazione. Questi dati sono disponibili anche da un'API, che consentirà l'importazione di questi dati in un sistema di monitoraggio degli eventi imprevisti e di sicurezza (SIEM).
* **Gestione dei costi:** Lo stato di trasferimento dei criteri di accesso a Azure AD riduce la dipendenza da soluzioni personalizzate o locali come Active Directory Federation Services (ADFS) per l'accesso condizionale, riducendo il costo di esecuzione di tale infrastruttura.

## <a name="customer-case-studies"></a>Case study dei clienti

Scopri in che modo la maggior parte delle organizzazioni USA Azure AD accesso condizionale per definire e implementare le decisioni di controllo di accesso automatico per accedere alle app cloud in base alle condizioni. Le storie in primo piano seguenti illustrano come vengono soddisfatte le esigenze dei clienti.

* [**Wipro** guida la produttività dei dispositivi mobili con gli strumenti di sicurezza cloud Microsoft per migliorare il coinvolgimento dei clienti.](https://customers.microsoft.com/story/wipro-professional-services-enterprise-mobility-security) I criteri di accesso condizionale in Azure AD hanno permesso alla società di condividere documenti, risorse e applicazioni con entità esterne attendibili---che possono usare le proprie credenziali---mantenendo al tempo stesso il controllo sui propri dati aziendali.
* [**Accenture** protegge il suo passaggio al cloud con Microsoft cloud app Security](https://customers.microsoft.com/story/accenture-professional-services-cloud-app-security) Accenture sta valutando la funzionalità [controllo app per l'accesso condizionale](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad) di cloud app Security, che usa Azure Active Directory l'accesso condizionale a Gate accesso all'applicazione in base a determinate condizioni. LePenske afferma che questa funzionalità può essere utile, ad esempio, per abilitare l'accesso in sola lettura ai file durante il divieto di download.
* [ **Aramex** recapito limitato: la società di trasporto e logistica globale crea uffici connessi al cloud con una soluzione di gestione delle identità e degli accessi](https://customers.microsoft.com/story/aramex-azure-active-directory-travel-transportation-united-arab-emirates-en). Garantire l'accesso protetto era particolarmente difficile con i dipendenti remoti di Aramex. L'azienda ora sta applicando l'accesso condizionale per consentire a questi dipendenti remoti di accedere alle applicazioni SaaS dall'esterno della rete. La regola di accesso condizionale decide se applicare Multi-Factor Authentication, concedendo solo agli utenti appropriati il diritto di accesso.

Per altre informazioni sulle esperienze dei clienti e dei partner sull'accesso condizionale Azure AD, visitare: [vedere le straordinarie attività svolte dagli utenti con Azure](https://azure.microsoft.com/case-studies/?service=active-directory).

## <a name="announcements"></a>Annunci

Azure AD viene regolarmente migliorato. Per rimanere sempre aggiornati sugli sviluppi più recenti, vedere Novità [di Azure Active Directory](../fundamentals/whats-new.md)

Blog recenti della community di tecnologia e della divisione di identità Microsoft:

* 24 settembre 2018, [Azure Active Directory l'accesso condizionale in Azure Databricks](https://azure.microsoft.com/updates/azure-active-directory-conditional-access-in-azure-databricks/)
* 21 settembre 2018, [Azure ad i controlli personalizzati dell'accesso condizionale sono disponibili in anteprima pubblica](https://azure.microsoft.com/updates/azure-ad-conditional-access-custom-controls-are-in-public-preview/)
* 21 settembre 2018, [Azure ad il supporto dell'accesso condizionale per l'accesso limitato con Microsoft cloud app Security è ora disponibile](https://azure.microsoft.com/updates/azure-ad-conditional-access-support-for-limited-access-with-microsoft-cloud-app-security-is-now-available/)
* 21 settembre 2018, [Azure ad l'accesso condizionale: supporto per Managed browser per piattaforme iOS/Android ora in anteprima](https://azure.microsoft.com/updates/azure-ad-conditional-access-managed-browser-support-for-ios-android-platforms-now-in-preview/)
* 21 settembre 2018, [Azure ad l'accesso condizionale per i codici paese è in versione di anteprima pubblica](https://azure.microsoft.com/updates/azure-ad-conditional-access-for-country-codes-is-in-public-preview/)
* 21 settembre 2018, [Azure ad le condizioni di utilizzo sono ora disponibili](https://azure.microsoft.com/updates/azure-ad-terms-of-use-now-available/)

## <a name="learning-resources"></a>Risorse di formazione

Per una panoramica su come Azure AD funzioni di accesso condizionale, seguire i collegamenti seguenti.

* Informazioni sull'[accesso condizionale in Azure Active Directory](overview.md)
* Conoscere "[quali sono le condizioni in Azure Active Directory l'accesso condizionale?](conditions.md)"
* Conoscere "[Qual è la condizione di posizione in Azure Active Directory l'accesso condizionale?](location-condition.md)"
* Conoscere "[quali sono i controlli di accesso in Azure Active Directory l'accesso condizionale?](controls.md)"
* Individuare "[Qual è lo strumento di simulazione di simulazione in Azure Active Directory l'accesso condizionale?"](what-if-tool.md)
* Seguire le [procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md)

Inoltre, fare riferimento ai collegamenti seguenti per indicazioni su come proteggere l'accesso a tutti i servizi integrati con Azure Active Directory.

* [Configurazioni di identità e accesso ai dispositivi](https://docs.microsoft.com/microsoft-365/enterprise/microsoft-365-policies-configurations). Viene descritto come configurare l'accesso sicuro ai servizi cloud tramite Enterprise Mobility + Security prodotti implementando un ambiente e una configurazione consigliati, incluso un set di criteri di accesso condizionale e funzionalità correlate.
* Informazioni di [riferimento sulle impostazioni di accesso condizionale Azure Active Directory](technical-reference.md). Imparare
   * Quali app usano l'accesso condizionale?
   * Quali servizi sono abilitati con l'accesso condizionale?
* [Abilitare l'accesso condizionale Azure Active Directory per l'accesso sicuro agli utenti](https://www.youtube.com/watch?v=eLAYBwjCGoA). Guardare questo video per informazioni su come l'accesso condizionale svolge un ruolo in altri carichi di lavoro di Enterprise e Mobility Suite.

### <a name="training-videos"></a>Video relativi alla formazione

**Accesso condizionale in Enterprise Mobility + Security**
   > [!VIDEO https://www.youtube.com/embed/A7IrxAH87wc]

**Accesso condizionale basato su dispositivo**
   > [!VIDEO https://www.youtube.com/embed/AdM0zYB-3WQ]

**Abilitare Azure Active Directory per l'accesso condizionale per l'accesso utente sicuro**
   > [!VIDEO https://www.youtube.com/embed/eLAYBwjCGoA]

### <a name="online-courses"></a>Corsi online

Vedere i corsi di accesso condizionale e altre informazioni su [pluralsight.com](https://www.pluralsight.com/):

* Pluralsight.com: [progettazione della gestione delle identità in Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-identity-management-design)
   * "Questo corso guida l'utente attraverso gli elementi chiave che è necessario comprendere per progettare la soluzione di gestione delle identità con Azure AD". Azure AD l'accesso condizionale viene trattato nel modulo "uso di ruoli e controllo di accesso con Azure AD".

* Pluralsight.com: [autenticazione di progettazione per Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authentication-design)
   * "Questo corso illustra come usare Azure AD per risolvere tutti i requisiti di autenticazione cloud". Azure AD l'accesso condizionale viene trattato nel modulo "requisiti di autenticazione per diversi scenari".

* Pluralsight.com: [autorizzazione di progettazione per Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-authorization-design)
   * "Questo corso insegna le opzioni di autorizzazione disponibili con Azure e Azure AD". Azure AD l'accesso condizionale viene trattato nel modulo "autorizzazione con Azure Resource Manager e Azure AD".

### <a name="books"></a>Documentazione

* Reilly- [implementazione di soluzioni di Azure-seconda edizione.](https://www.oreilly.com/library/view/implementing-azure-solutions/9781789343045/b7ead3db-eb1c-4ace-897e-86ee25ea86be.xhtml)
   * "È possibile iniziare a usare i servizi di Azure per apprendere come implementarli nella propria organizzazione. Azure AD l'accesso condizionale viene trattato nel capitolo [distribuzione e sincronizzazione Azure Active Directory](https://learning.oreilly.com/library/view/implementing-azure-solutions/9781789343045/02ca8bba-08cf-4691-a7d0-1b96e286e7ea.xhtml). "

* Wiley- [mastering Microsoft Azure servizi infrastruttura](https://www.wiley.com/Mastering+Microsoft+Azure+Infrastructure+Services-p-9781119003298)
   * "Ecco tutto quello che ti serve per comprendere, valutare, distribuire e gestire gli ambienti che usano Microsoft Azure".

## <a name="white-papers"></a>White paper

* Pubblicata il 18 dicembre 2018, [creare una strategia di gestione del controllo di accesso resiliente con Azure Active Directory](../authentication/concept-resilient-controls.md)
   * In questo documento vengono fornite indicazioni sulle strategie che un'organizzazione può adottare per fornire resilienza per ridurre il rischio di blocco durante le rotture impreviste.

* Pubblicato il 18 settembre 2018, [risorse per la migrazione di applicazioni a Azure Active Directory](../manage-apps/migration-resources.md)
   * Questo white paper include un elenco di risorse che consentono di eseguire la migrazione dell'accesso alle applicazioni e dell'autenticazione a Azure Active Directory (Azure AD).

* Pubblicato il 12 luglio 2018 [progetto di sicurezza e conformità di Azure: hosting di applicazioni Web PaaS per carichi di lavoro ufficiali del Regno Unito](../../security/blueprints/ukofficial-paaswa-overview.md)
   * I progetti di Azure sono costituiti da documenti con linee guida e modelli di automazione che consentono di distribuire architetture basate sul cloud per offrire soluzioni per scenari con requisiti di accreditamento o di conformità.

## <a name="guidance-for-it-administrators"></a>Linee guida per gli amministratori IT

Accedere al [portale di Azure](https://portal.azure.com/) come amministratore globale, amministratore della sicurezza o amministratore dell'accesso condizionale. Fare riferimento alle [autorizzazioni del ruolo amministratore in Azure Active Directory.](../users-groups-roles/directory-assign-admin-roles.md)

In qualità di amministratore IT, usare [Azure ad accesso condizionale](overview.md) per richiedere agli utenti di eseguire l'autenticazione con multi-factor authentication di Azure, accedere da una rete attendibile o da un dispositivo attendibile.

Di seguito sono riportati i collegamenti utili per iniziare:

* [Procedure consigliate per l'accesso condizionale in Azure Active Directory](best-practices.md)
* [Usare le verifiche di accesso Azure AD per gestire gli utenti che sono stati esclusi dai criteri di accesso condizionale](../governance/conditional-access-exclusion.md)
* [Procedura: pianificare la distribuzione dell'accesso condizionale in Azure Active Directory](plan-conditional-access.md)
* [Guida introduttiva: richiedere l'autenticazione a più fattori per app specifiche con accesso condizionale Azure Active Directory](app-based-mfa.md)
* [Guida introduttiva: richiedere l'accettazione delle condizioni per l'utilizzo prima di accedere alle app Cloud](require-tou.md)
* [Guida introduttiva: bloccare l'accesso quando viene rilevato un rischio di sessione con Azure Active Directory l'accesso condizionale](app-sign-in-risk.md)
* [Domande frequenti sull'accesso condizionale Azure AD](faqs.md)
   * Per altre domande, è anche possibile visualizzare il [Forum MSDN](https://social.msdn.microsoft.com/Forums/home?forum=WindowsAzureAD&sort=relevancedesc&brandIgnore=True&searchTerm=password+reset+azure).
   * Se non si riesce a trovare la risposta a un problema, i team di supporto sono sempre disponibili per assistenza. Utilizzare [contattare il supporto tecnico Microsoft](../authentication/active-directory-passwords-troubleshoot.md#contact-microsoft-support).

### <a name="tutorials"></a>Esercitazioni

* [**Guida introduttiva: richiedere l'autenticazione a più fattori per app specifiche con accesso condizionale Azure Active Directory**](app-based-mfa.md)
   * Questa Guida introduttiva illustra come configurare un Azure AD criteri di accesso condizionale che richiedono l'autenticazione a più fattori per un'app Cloud selezionata nell'ambiente in uso.

* [**Guida introduttiva: richiedere l'accettazione delle condizioni per l'utilizzo prima di accedere alle app Cloud**](require-tou.md)
   * Questa Guida introduttiva illustra come configurare un criterio di accesso condizionale Azure AD che richiede l'accettazione di una ToU per un'app Cloud selezionata nell'ambiente in uso.

* [**Guida introduttiva: bloccare l'accesso quando viene rilevato un rischio di sessione con Azure Active Directory l'accesso condizionale**](app-sign-in-risk.md)
   * Questa Guida introduttiva illustra come configurare un criterio di accesso condizionale che blocca l'accesso quando è stato rilevato un livello di rischio di accesso configurato.

* [Esercitazione: **eseguire la migrazione di criteri classici che richiedono l'autenticazione a più fattori nel portale di Azure**](policy-migration-mfa.md)
   * Questa esercitazione illustra come eseguire la migrazione di criteri classici che richiedono l'autenticazione a più fattori per un'app cloud.

## <a name="end-user-readiness-and-communication"></a>Conformità e comunicazione per gli utenti finali

L'accesso condizionale usa altre funzionalità Azure AD che possono influire sull'esperienza dell'utente finale. Ad esempio, è possibile usare l'autenticazione a più fattori di Azure per abilitare l'autenticazione avanzata per gli utenti. In tal caso, si utilizzeranno i modelli dell'utente finale di autenticazione a più fattori di Azure.

## <a name="next-steps"></a>Passaggi successivi

* Avviare la distribuzione con la [documentazione relativa alla pianificazione della distribuzione dell'accesso condizionale](plan-conditional-access.md).
