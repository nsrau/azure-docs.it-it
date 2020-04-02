---
title: Procedure consigliate per la sicurezza con il controllo di accesso e identità di Azure | Documentazione Microsoft
description: Questo articolo illustra una serie di procedure consigliate per il controllo di accesso e la gestione delle identità usando le funzionalità integrate di Azure.
services: security
documentationcenter: na
author: terrylanfear
manager: RKarlin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/28/2019
ms.author: terrylan
ms.openlocfilehash: ffd9919092cdf2481767e58f10ba6525d56ca4a8
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548449"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure

Questo articolo illustra una serie di procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure. Le procedure consigliate si basano sull'esperienza di tecnici e clienti con [Azure AD](../../active-directory/fundamentals/active-directory-whatis.md).

Per ogni procedura consigliata verrà illustrato:

* Qual è la procedura consigliata
* Il motivo per cui si vuole abilitare tale procedura consigliata
* Quale potrebbe essere il risultato se non fosse possibile abilitare la procedura consigliata
* Alternative possibili alla procedura consigliata
* Come imparare ad abilitare la procedura consigliata

Questo articolo sulle procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure si basa su pareri condivisi, nonché sulle capacità e sui set di funzionalità della piattaforma Azure esistenti quando l'articolo è stato scritto.

L'intenzione di scrivere questo articolo è fornire una guida di orientamento generale a una posizione di sicurezza più affidabile dopo la distribuzione guidata dall'elenco di controllo "[5 passaggi per proteggere l'infrastruttura](steps-secure-identity.md)di identità ", che illustra alcune delle nostre funzionalità e servizi principali.

Le opinioni e le tecnologie cambiano nel tempo e questo articolo verrà aggiornato regolarmente per riflettere tali modifiche.

Procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure illustrate in questo articolo:

* Trattare l'identità come perimetro di sicurezza primario
* Centralizzare la gestione delle identità
* Gestire i tenant connessi
* Abilita Single Sign-On
* Attivare l'accesso condizionale
* Pianificare miglioramenti della sicurezza di routine
* Abilitare la gestione delle password
* Applicare la verifica a più fattori per gli utenti
* Usare il controllo degli accessi in base al ruolo
* Diminuire l'esposizione degli account con privilegi
* Controllare le posizioni in cui si trovano le risorse
* Usare Azure AD per l'autenticazione di archiviazioneUse Azure AD for storage authentication

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Trattare l'identità come perimetro di sicurezza primario

Molti considerano l'identità come perimetro primario per la sicurezza. Questa strategia rappresenta un cambiamento rispetto al tradizionale ruolo centrale attribuito alla sicurezza della rete. I perimetri di rete diventano sempre più permeabili pertanto la difesa perimetrale non può essere efficace quanto lo era prima dell'esplosione dei dispositivi [BYOD](https://aka.ms/byodcg) e delle applicazioni cloud.

[Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) è la soluzione di Microsoft Azure per la gestione delle identità e degli accessi. Azure AD è il servizio Microsoft multi-tenant di gestione delle identità e delle directory basato sul cloud. Combina in un'unica soluzione servizi di directory importanti, gestione degli accessi alle applicazioni e protezione delle identità.

Le sezioni seguenti elencano le procedure consigliate per garantire la sicurezza delle identità e degli accessi tramite Azure AD.

**Procedura consigliata:** controlli di sicurezza e rilevamenti del centro relativi alle identità di utenti e servizi.
**Dettaglio**: Usare Azure AD per collocare i controlli e le identità.

## <a name="centralize-identity-management"></a>Centralizzare la gestione delle identità

In uno scenario di [soluzione ibrida di gestione delle identità](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) è consigliabile integrare le directory cloud e locali. L'integrazione consente al team IT di gestire gli account da un'unica posizione, indipendentemente dalla posizione in cui viene creato un account. L'integrazione consente inoltre agli utenti di essere più produttivi fornendo un'identità comune per l'accesso alle risorse cloud e locali.

**Procedura consigliata:** stabilire una singola istanza di Azure AD. La coerenza e le singole fonti autorevoli aumenteranno la chiarezza e ridurranno i rischi per la sicurezza derivanti da errori umani e complessità della configurazione.
**Dettaglio**: Designare una singola directory di Azure AD come origine autorevole per gli account aziendali e dell'organizzazione.

**Procedura consigliata**: integrare le directory locali con Azure AD.  
**Dettagli**: sincronizzare la directory locale con la directory cloud usando [Azure AD Connect](/azure/active-directory/connect/active-directory-aadconnect).

> [!Note]
> Esistono [fattori che influiscono sulle prestazioni di Azure AD Connect](../../active-directory/hybrid/plan-connect-performance-factors.md). Assicurati che Azure AD Connect disponga di capacità sufficiente per impedire ai sistemi con prestazioni insufficienti di rimanere ostacolati dalla sicurezza e dalla produttività. Le organizzazioni di grandi dimensioni o complesse (organizzazioni che hanno eseguito il provisioning di più di 100.000 oggetti) devono seguire i consigli per ottimizzare l'implementazione di Azure AD Connect.Large or complex organizations (organizations provisioning more than 100,000 objects) should follow the [recommendations](../../active-directory/hybrid/whatis-hybrid-identity.md) to optimize their Azure AD Connect implementation.

**Procedura consigliata:** non sincronizzare gli account con Azure AD con privilegi elevati nell'istanza di Active Directory esistente.
**Dettaglio:** non modificare la configurazione predefinita di [Azure AD Connect](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) che esclude questi account. Questa configurazione riduce il rischio che gli avversari si spostino dal cloud agli asset locali (che potrebbero creare un incidente grave).

**Procedura consigliata**: attivare la sincronizzazione dell'hash delle password.  
**Dettaglio:** la sincronizzazione dell'hash delle password è una funzionalità usata per sincronizzare gli hash delle password degli utenti da un'istanza di Active Directory locale a un'istanza di Azure AD basata su cloud. Questa sincronizzazione consente di proteggere dalle credenziali trapelate riprodotte da attacchi precedenti.

Anche se si decide di usare la federazione con Active Directory Federation Services (AD FS) o altri provider di identità, è possibile configurare la sincronizzazione dell'hash delle password come backup in caso di errore o temporanea indisponibilità dei server locali. Questa sincronizzazione consente agli utenti di accedere al servizio utilizzando la stessa password usata per accedere all'istanza di Active Directory locale. Consente inoltre a Identity Protection di rilevare le credenziali compromesse confrontando gli errori delle password sincronizzati con le password note per essere compromesse, se un utente ha usato lo stesso indirizzo di posta elettronica e la stessa password in altri servizi non connessi ad Azure AD.

Per altre informazioni, vedere Implementare la [sincronizzazione dell'hash delle password con](/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization)la sincronizzazione di Azure AD Connect.For more information, see Implement password hash synchronization with Azure AD Connect sync .

**Procedura consigliata:** per lo sviluppo di nuove applicazioni, usare Azure AD per l'autenticazione.
**Dettaglio**: Utilizzare le funzionalità corrette per supportare l'autenticazione:

  - Azure AD per i dipendenti
  - [B2B di Azure AD](../../active-directory/b2b/index.yml) per utenti guest e partner esterniAzure AD B2B for guest users and external partners
  - [Azure AD B2C](../../active-directory-b2c/index.yml) per controllare il modo in cui i clienti si iscrivono, accedono e gestiscono i propri profili quando usano le applicazioni

Le organizzazioni che non integrano l'identità locale con la propria identità cloud possono subire un sovraccarico maggiore nella gestione degli account. Questo sovraccarico aumenta le probabilità di incorrere in errori e violazioni della sicurezza.

> [!Note]
> È necessario scegliere in quali directory risiederanno gli account critici e se la workstation di amministrazione utilizzata è gestita da nuovi servizi cloud o processi esistenti. L'utilizzo di processi di gestione e provisioning delle identità esistenti può ridurre alcuni rischi, ma può anche creare il rischio che un utente malintenzionato comprometta un account locale e si pivotle nel cloud. È possibile utilizzare una strategia diversa per ruoli diversi, ad esempio amministratori IT e amministratori di Business Unit. Sono disponibili due opzioni. La prima opzione consiste nel creare account di Azure AD che non sono sincronizzati con l'istanza di Active Directory locale. Aggiungere la workstation di amministrazione ad Azure AD, che è possibile gestire e applicare patch tramite Microsoft Intune.Join your admin workstation to Azure AD, which you can manage and patch by using Microsoft Intune. La seconda opzione consiste nell'utilizzare gli account amministratore esistenti sincronizzandosi con l'istanza di Active Directory locale. Utilizzare le workstation esistenti nel dominio di Active Directory per la gestione e la sicurezza.

## <a name="manage-connected-tenants"></a>Gestire i tenant connessi
L'organizzazione di sicurezza ha bisogno di visibilità per valutare i rischi e determinare se i criteri dell'organizzazione e gli eventuali requisiti normativi vengono seguiti. È necessario assicurarsi che l'organizzazione di sicurezza abbia visibilità su tutte le sottoscrizioni connesse all'ambiente di produzione e alla rete (tramite [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) o una VPN da [sito a sito).](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) Un [amministratore globale/amministratore aziendale](../../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) in Azure AD può elevare l'accesso al ruolo Amministratore accesso [utenti](../../role-based-access-control/built-in-roles.md#user-access-administrator) e visualizzare tutte le sottoscrizioni e i gruppi gestiti connessi all'ambiente.

Vedere [elevare l'accesso per gestire tutte le sottoscrizioni di Azure e i gruppi](../../role-based-access-control/elevate-access-global-admin.md) di gestione per garantire che l'utente e il gruppo di sicurezza possano visualizzare tutte le sottoscrizioni o i gruppi di gestione connessi all'ambiente. È necessario rimuovere questo accesso elevato dopo aver valutato i rischi.

## <a name="enable-single-sign-on"></a>Abilita Single Sign-On

In un mondo incentrato su dispositivi mobili e cloud, è importante poter accedere con Single Sign-On (SSO) a dispositivi, app e servizi da qualsiasi posizione in modo che gli utenti siano produttivi sempre e ovunque. La gestione di più soluzioni di identità rappresenta un problema di amministrazione non solo per il reparto IT, ma anche per gli utenti finali che devono tenere a mente più password.

Utilizzando la stessa soluzione di identità per tutte le applicazioni e risorse, è possibile ottenere l'accesso Single Sign-On. Gli utenti hanno la possibilità di usare lo stesso set di credenziali per accedere alle risorse, indipendentemente dalla dislocazione in locale o nel cloud di tali risorse.

**Procedura consigliata**: abilitare SSO.  
**Dettagli**: Azure AD [estende Active Directory locale](/azure/active-directory/connect/active-directory-aadconnect) al cloud. Gli utenti possono usare il loro account aziendale o dell'istituto di istruzione principale per i dispositivi aggiunti al dominio, per le risorse aziendali e per tutte le applicazioni Web e SaaS necessarie per svolgere le loro attività. Oltre a eliminare la necessità di ricordare diversi set di nomi utente e password, permette anche di effettuare automaticamente il provisioning o il deprovisioning dell'accesso alle applicazioni degli utenti in base all'appartenenza ai gruppi dell'organizzazione e al relativo stato come dipendenti. Permette poi di controllare l'accesso ad app della raccolta o app locali sviluppate e pubblicate tramite il [proxy di applicazione di Azure AD](/azure/active-directory/active-directory-application-proxy-get-started).

Usare SSO per consentire agli utenti di accedere alle [applicazioni SaaS](/azure/active-directory/active-directory-appssoaccess-whatis) in base all'account aziendale o dell'istituto di istruzione in Azure AD. Questo vale non solo per le app SaaS Microsoft, ma anche per altre app come [Google Apps](/azure/active-directory/active-directory-saas-google-apps-tutorial) e [Salesforce](/azure/active-directory/active-directory-saas-salesforce-tutorial). L'applicazione può essere configurata per l'uso di Azure AD come provider di [identità basata su SAML](/azure/active-directory/fundamentals-identity). Come controllo di sicurezza, Azure AD non rilascia all'utente un token per l'accesso all'applicazione a meno che l'accesso non sia stato concesso all'utente con Azure AD. L'accesso può essere concesso direttamente agli utenti o attraverso un gruppo di cui sono membri.

Le organizzazioni che non creano un'identità comune per stabilire l'accesso SSO per utenti e applicazioni sono più esposte a situazioni in cui gli utenti devono utilizzare più password. In queste situazioni aumenta la probabilità di riutilizzare le password o di utilizzare password vulnerabili.

## <a name="turn-on-conditional-access"></a>Attivare l'accesso condizionale

Gli utenti possono accedere alle risorse dell'organizzazione con una serie di dispositivi e app ovunque si trovino. Gli amministratori IT devono assicurarsi che questi dispositivi soddisfino gli standard di sicurezza e conformità. Tuttavia, non è più sufficiente concentrarsi solo su chi può accedere a una risorsa.

Per bilanciare sicurezza e produttività, è necessario considerare come si accede a una risorsa prima di poter prendere una decisione sul controllo degli accessi. Con l'accesso condizionale di Azure AD è possibile soddisfare questo requisito. Con l'accesso condizionale, è possibile prendere decisioni automatizzate sul controllo degli accessi in base alle condizioni per l'accesso alle app cloud.

**Procedura consigliata**: gestire e controllare l'accesso alle risorse aziendali.  
**Dettaglio:** configurare [l'accesso condizionale](/azure/active-directory/active-directory-conditional-access-azure-portal) di Azure AD in base a un gruppo, una posizione e una sensibilità delle applicazioni per le app SaaS e le app connesse ad Azure AD.

**Procedura consigliata**: Bloccare i protocolli di autenticazione legacy.
**Dettaglio**: Gli aggressori sfruttano ogni giorno i punti deboli nei protocolli più vecchi, in particolare per gli attacchi di spray password. Configurare l'accesso condizionale per bloccare i protocolli legacy. Per altre informazioni, vedere il video [Azure AD: Cosa fare e cosa non fare.](https://www.youtube.com/watch?v=wGk0J4z90GI)

## <a name="plan-for-routine-security-improvements"></a>Pianificare miglioramenti della sicurezza di routine

La sicurezza è in continua evoluzione ed è importante creare nel framework di gestione delle identità e del cloud un modo per mostrare regolarmente la crescita e scoprire nuovi modi per proteggere l'ambiente.

Identity Secure Score è un set di controlli di sicurezza consigliati che Microsoft pubblica che funziona per fornire un punteggio numerico per misurare oggettivamente la sicurezza e pianificare i futuri miglioramenti della sicurezza. È inoltre possibile visualizzare il punteggio in confronto a quelli di altri settori, nonché le proprie tendenze nel tempo.

**Best practice**: Pianificare revisioni e miglioramenti di sicurezza di routine in base alle procedure consigliate del settore.
**Dettaglio**: Utilizzare la funzione Identity Secure Score per classificare i miglioramenti nel tempo.

## <a name="enable-password-management"></a>Abilitare la gestione delle password

Se sono presenti più tenant o si vuole consentire agli utenti di [reimpostare la propria password](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md), è importante adottare criteri di sicurezza adatti per prevenire l'uso improprio.

**Procedura consigliata**: configurare Reimpostazione password self-service per gli utenti.  
**Dettagli**: usare la funzionalità [Reimpostazione password self-service](/azure/active-directory-b2c/active-directory-b2c-reference-sspr) di Azure AD.

**Procedura consigliata**: monitorare se e come Reimpostazione password self-service è effettivamente usata.  
**Dettagli**: monitorare gli utenti che eseguono la registrazione usando il report [Attività di registrazione reimpostazione password](/azure/active-directory/active-directory-passwords-get-insights) di Azure AD. La funzionalità di creazione di report disponibile in Azure AD consente di rispondere a domande specifiche grazie a report predefiniti. Se si dispone di una licenza appropriata, è anche possibile creare query personalizzate.

**Procedura consigliata:** estendere i criteri password basati su cloud all'infrastruttura locale.
**Dettaglio**: Migliorare i criteri password nell'organizzazione eseguendo gli stessi controlli per le modifiche delle password locali come per le modifiche delle password basate su cloud. Installare la [protezione con password](/azure/active-directory/authentication/concept-password-ban-bad) di Azure AD per gli agenti di Active Directory di Windows Server in locale per estendere gli elenchi di password non aperte all'infrastruttura esistente. Gli utenti e gli amministratori che modificano, impostano o reimpostano le password in locale devono rispettare gli stessi criteri password degli utenti solo cloud.

## <a name="enforce-multi-factor-verification-for-users"></a>Applicare la verifica a più fattori per gli utenti

È consigliabile richiedere la verifica in due passaggi per tutti gli utenti. Questo vale anche per gli amministratori e gli altri utenti dell'organizzazione la cui compromissione dell'account potrebbe avere un impatto significativo, ad esempio, i dirigenti del reparto finanziario.

Sono disponibili vari modi per richiedere la verifica in due passaggi. L'opzione migliore dipende dall'obiettivo che si intende raggiungere, dall'edizione di Azure AD in esecuzione e dal programma di licenza. Vedere [Come richiedere la verifica in due passaggi per un utente](/azure/active-directory/authentication/howto-mfa-userstates) per determinare l'opzione migliore per il proprio ambiente. Altre informazioni sulle licenze e i prezzi sono disponibili nelle pagine relative ai prezzi di [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) e [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

Di seguito sono indicati i vantaggi e le opzioni per l'abilitazione della verifica in due passaggi:

**Opzione 1:** abilitare l'autenticazione a più fattori per tutti gli utenti e i metodi di accesso con il vantaggio delle impostazioni predefinite di sicurezza di Azure AD: questa opzione consente di applicare facilmente e rapidamente l'autenticazione a più fattori per tutti gli utenti dell'ambiente con criteri rigorosi per:Option 1 : Enable MFA for all users and login methods with Azure AD Security Defaults **Benefit**: This option enables you to easily and quickly quickly and quickly enforce MFA for all users in your environment with a stringent policy to:

* Contestare gli account amministrativi e i meccanismi di accesso amministrativo
* Richiedere la richiesta di autenticazione a più fattori tramite Microsoft Authenticator per tutti gli utentiRequire MFA challenge via Microsoft Authenticator for all users
* Limitare i protocolli di autenticazione legacy.

Questo metodo è disponibile per tutti i livelli di licenza, ma non può essere miscelato con i criteri di accesso condizionale esistenti. È possibile trovare altre informazioni in Impostazioni predefinite di sicurezza di Azure ADYou can find more information In Azure AD Security Defaults

**Opzione 2:** [abilitare Multi-Factor Authentication modificando lo stato utente.](../../active-directory/authentication/howto-mfa-userstates.md)   
**Vantaggio** questo è il metodo tradizionale per richiedere la verifica in due passaggi. Funziona sia con [Azure Multi-Factor Authentication nel cloud e Server Multi-Factor Authentication](/azure/active-directory/authentication/concept-mfa-whichversion). L'utilizzo di questo metodo richiede agli utenti di eseguire la verifica in due passaggi ogni volta che accedono ed esegue l'override dei criteri di accesso condizionale.

Per determinare dove deve essere abilitata l'autenticazione a più fattori, vedere [Quale versione di Azure MFA è giusta per l'organizzazione.](/azure/active-directory/authentication/concept-mfa-whichversion)

**Opzione 3:** [Abilitare Multi-Factor Authentication con criteri di accesso condizionale](/azure/active-directory/authentication/howto-mfa-getstarted).
**Vantaggio:** questa opzione consente di richiedere la verifica in due passaggi in condizioni specifiche utilizzando [Accesso condizionale](/azure/active-directory/active-directory-conditional-access-azure-portal). L'accesso di utenti da posizioni diverse, l'uso di dispositivi non attendibili o l'uso di applicazioni considerate rischiose possono essere considerate condizioni specifiche. La definizione di condizioni specifiche in cui si richiede la verifica in due passaggi consente di evitare di chiedere continuamente conferma agli utenti della loro identità, che può risultare spiacevole.

Questo rappresenta il mezzo più flessibile per abilitare la verifica in due passaggi per gli utenti. L'abilitazione di criteri di accesso condizionale funziona solo per Azure Multi-Factor Authentication nel cloud ed è una funzionalità premium di Azure AD. Per altre informazioni su questo metodo, vedere [Implementare Azure Multi-Factor Authentication basato su cloud](/azure/active-directory/authentication/howto-mfa-getstarted).

**Opzione 4:** abilitare l'autenticazione a più fattori con criteri di accesso condizionale valutando il rischio di accesso e utente di [Azure AD Identity Protection](/azure/active-directory/authentication/tutorial-risk-based-sspr-mfa).   
**Vantaggio**: questa opzione consente di:

* Rilevare le potenziali vulnerabilità per le identità dell'organizzazione.
* Configurare risposte automatizzate alle azioni sospette rilevate in relazione alle identità dell'organizzazione.
* Esaminare gli eventi imprevisti sospetti ed eseguire l'azione appropriata per risolverli.

Questo metodo usa la valutazione del rischio di Azure AD Identity Protection per determinare se la verifica in due passaggi è necessaria in funzione del rischio per l'utente e l'accesso per tutte le applicazioni cloud. Questo metodo richiede una licenza di Azure Active Directory P2. Per altre informazioni su questo metodo, vedere [Azure Active Directory Identity Protection](/azure/active-directory/identity-protection/overview).

> [!Note]
> L'opzione 1, che abilita Multi-Factor Authentication modificando lo stato utente, esegue l'override dei criteri di accesso condizionale. Poiché le opzioni 2 e 3 utilizzano criteri di accesso condizionale, non è possibile utilizzare le opzioni 1 con esse.

Le organizzazioni che non aggiungono livelli supplementari di protezione delle identità, come la verifica in due passaggi, sono più vulnerabili agli attacchi con furto di credenziali. Un attacco con furto di credenziali comporta il rischio di compromissione dei dati.

## <a name="use-role-based-access-control"></a>Usare il controllo degli accessi in base al ruolo

La gestione degli accessi per le risorse cloud è fondamentale per qualsiasi organizzazione che usa il cloud. Il controllo degli [accessi in base](/azure/role-based-access-control/overview)al ruolo consente di gestire gli utenti che hanno accesso alle risorse di Azure, le operazioni che possono eseguire con tali risorse e le aree a cui hanno accesso.

La designazione di gruppi o singoli ruoli responsabili di funzioni specifiche in Azure consente di evitare confusione che può causare errori umani e di automazione che creano rischi per la sicurezza. Per le organizzazioni che intendono applicare criteri di sicurezza per l'accesso ai dati è fondamentale limitare l'accesso in base a principi di [riservatezza](https://en.wikipedia.org/wiki/Need_to_know) e [privilegi minimi](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

Il team di sicurezza ha bisogno di visibilità nelle risorse di Azure per valutare e correggere i rischi. Se il team di sicurezza ha responsabilità operative, ha bisogno di autorizzazioni aggiuntive per svolgere il proprio lavoro.

È possibile utilizzare [il controllo degli accessi in](/azure/role-based-access-control/overview) base al ruolo per assegnare autorizzazioni a utenti, gruppi e applicazioni in un determinato ambito. L'ambito di un'assegnazione di ruolo può essere una sottoscrizione, un gruppo di risorse o una singola risorsa.

**Best practice**: Separare i compiti all'interno del team e concedere solo la quantità di accesso agli utenti di cui hanno bisogno per svolgere il proprio lavoro. Invece di concedere a tutti le autorizzazioni senza restrizioni nella sottoscrizione o nelle risorse di Azure, consentire solo determinate azioni in un determinato ambito.
**Dettaglio:** usare [i ruoli RBAC predefiniti in](/azure/role-based-access-control/built-in-roles) Azure per assegnare privilegi agli utenti.

> [!Note]
> Autorizzazioni specifiche creano complessità e confusione non necessarie, accumulandosi in una configurazione "legacy" difficile da risolvere senza paura di rompere qualcosa. Evitare di definire autorizzazioni specifiche per le risorse. Utilizzare invece i gruppi di gestione per le autorizzazioni a livello aziendale e i gruppi di risorse per le autorizzazioni all'interno delle sottoscrizioni. Evitare autorizzazioni specifiche dell'utente. In alternativa, assegnare l'accesso a gruppi in Azure AD.

**Procedura consigliata:** concedere ai team di sicurezza con responsabilità di Azure l'accesso per visualizzare le risorse di Azure in modo che possano valutare e correggere i rischi.
**Dettaglio:** Concedere ai team di sicurezza il ruolo [lettore di sicurezza](/azure/role-based-access-control/built-in-roles#security-reader) RBAC. È possibile utilizzare il gruppo di gestione radice o il gruppo di gestione dei segmenti, a seconda dell'ambito delle responsabilità:You can use the root management group or the segment management group, depending on the scope of responsibilities:

* **Gruppo di gestione radice** per i team responsabili di tutte le risorse dell'organizzazione
* **Segmentare** il gruppo di gestione per i team con ambito limitato (comunemente a causa di confini normativi o di altri limiti organizzativi)Segment management group for teams with limited scope (commonly due to regulatory or other organizational boundaries)

**Procedura consigliata:** concedere le autorizzazioni appropriate ai team di sicurezza con responsabilità operative dirette.
**Dettaglio**: Esaminare i ruoli predefiniti RBAC per l'assegnazione di ruolo appropriata. Se i ruoli predefiniti non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare ruoli personalizzati per le risorse di [Azure.](/azure/role-based-access-control/custom-roles) Come per i ruoli predefiniti, è possibile assegnare ruoli personalizzati a utenti, gruppi ed entità servizio in corrispondenza di ambiti di sottoscrizione, gruppo di risorse e risorse.

**Procedure consigliate:** concedere a Centro sicurezza di Azure l'accesso ai ruoli di sicurezza che ne hanno bisogno. Il Centro sicurezza consente ai team di sicurezza di identificare e correggere rapidamente i rischi.
**Dettaglio:** aggiungere i team di sicurezza con queste esigenze al ruolo [di amministratore](/azure/role-based-access-control/built-in-roles#security-admin) della sicurezza RBAC in modo che possano visualizzare i criteri di sicurezza, visualizzare gli stati di sicurezza, modificare i criteri di sicurezza, visualizzare avvisi e suggerimenti e ignorare avvisi e suggerimenti. A tale scopo, è possibile usare il gruppo di gestione radice o il gruppo di gestione dei segmenti, a seconda dell'ambito delle responsabilità.

Le organizzazioni che non applicano il controllo dell'accesso ai dati utilizzando funzionalità come il controllo degli accessi in base al ruolo potrebbero fornire più privilegi del necessario agli utenti. Ciò può portare a una compromissione dei dati consentendo agli utenti di accedere a tipi di dati (ad esempio, un impatto aziendale elevato) che non dovrebbero avere.

## <a name="lower-exposure-of-privileged-accounts"></a>Diminuire l'esposizione degli account con privilegi

La protezione dell'accesso con privilegi è il primo fondamentale passo per proteggere le risorse aziendali. Riducendo al minimo il numero di utenti che hanno accesso a informazioni o risorse protette riduce le probabilità che un utente malintenzionato acceda al sistema, o che un utente autorizzato comprometta inavvertitamente una risorsa sensibile.

Gli account con privilegi sono gli account che amministrano e gestiscono i sistemi IT. Gli utenti malintenzionati usano questi account per ottenere l'accesso ai dati e ai sistemi di un'organizzazione. Per proteggere l'accesso con privilegi, è necessario isolare gli account e i sistemi dal rischio di esposizione a utenti malintenzionati.

Si consiglia di sviluppare e seguire una roadmap per proteggere l'accesso con privilegi dagli utenti malintenzionati. Per informazioni sulla creazione di una roadmap dettagliata per proteggere le identità e gli accessi che sono gestiti o segnalati in Azure AD, Microsoft Azure, Office 365 e altri servizi cloud, vedere [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure).

Di seguito sono riassunte le procedure consigliate disponibili in [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure):

**Procedura consigliata**: gestire, controllare e monitorare l'accesso ad account con privilegi.   
**Dettagli**: attivare [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access). Dopo l'attivazione di Privileged Identity Management, si riceveranno messaggi di posta elettronica di notifica in caso di modifiche ai ruoli di accesso con privilegi. Queste notifiche forniscono un avviso tempestivo in caso di aggiunta di ulteriori utenti a ruoli con privilegi elevati nella directory.

**Procedura consigliata:** verificare che tutti gli account amministratore critici siano account di Azure AD gestiti.
**Dettaglio**: Rimuovere tutti gli account consumer dai ruoli di amministratore critici (ad esempio, account Microsoft come hotmail.com, live.com e outlook.com).

**Procedura consigliata**: Assicurarsi che tutti i ruoli di amministratore critici dispongano di un account separato per le attività amministrative al fine di evitare phishing e altri attacchi per compromettere i privilegi amministrativi.
**Dettaglio**: Creare un account amministratore separato a cui sono assegnati i privilegi necessari per eseguire le attività amministrative. Bloccare l'utilizzo di questi account amministrativi per strumenti di produttività giornalieri come la posta elettronica di Microsoft Office 365 o l'esplorazione arbitraria del Web.

**Procedura consigliata**: identificare e classificare gli account associati a ruoli con privilegi elevati.   
**Dettagli**: dopo avere attivato Azure AD Privileged Identity Management, visualizzare gli utenti con i ruoli di amministratore globale, amministratore del ruolo con privilegi e ruolo con privilegi elevati. Rimuovere tutti gli account che non sono più necessari in questi ruoli e classificare gli account rimanenti assegnati ai ruoli di amministratore:

* Singolarmente assegnati a utenti amministratori e che possono essere usati per scopi non amministrativi, ad esempio, posta elettronica personale
* Singolarmente assegnati a utenti amministratori e designati esclusivamente per scopi amministrativi
* Condivisi tra più utenti
* Per scenari di accesso di emergenza
* Per script automatizzati
* Per utenti esterni

**Procedura consigliata**: implementare l'accesso "just in time" (JIT) per ridurre ulteriormente il tempo di esposizione dei privilegi e aumentare la visibilità sull'uso degli account con privilegi.   
**Dettagli**: Azure AD Privileged Identity Management consente di effettuare le operazioni seguenti:

* Limitare gli utenti a prendere solo il proprio accesso con privilegi JIT.
* Assegnare i ruoli per un periodo di tempo ridotto sapendo che i privilegi vengono revocati automaticamente.

**Procedura consigliata**: definire almeno due account di accesso di emergenza.   
**Dettagli**: gli account di accesso di emergenza consentono alle organizzazioni di limitare l'accesso con privilegi in un ambiente Azure Active Directory esistente. Si tratta di account con privilegi elevati non assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati a scenari in cui non è possibile usare i normali account amministrativi. Le organizzazioni devono limitare l'utilizzo degli account di emergenza per la sola quantità di tempo necessaria.

Valutare gli account assegnati o idonei per il ruolo di amministratore globale. Se non sono presenti account solo cloud con il dominio `*.onmicrosoft.com` (destinati all'accesso di emergenza), crearli. Per altre informazioni, vedere [Gestire gli account amministrativi di accesso di emergenza in Azure AD](/azure/active-directory/users-groups-roles/directory-emergency-access).

**Best practice**: Avere un processo di "rottura del vetro" in caso di emergenza.
**Dettaglio:** seguire i passaggi descritti in [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD.](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)

**Procedura consigliata**: Richiedere che tutti gli account amministratore critici siano senza password (preferito) o che richiedano l'autenticazione a più fattori.
**Dettagli :** Usare [l'app Microsoft Authenticator](/azure/active-directory/authentication/howto-authentication-phone-sign-in) per accedere a qualsiasi account Azure AD senza usare una password. Come [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator utilizza l'autenticazione basata su chiave per abilitare una credenziale utente legata a un dispositivo e che utilizza l'autenticazione biometrica o un PIN.

Richiedere Azure Multi-Factor Authentication all'accesso per tutti i singoli utenti assegnati in modo permanente a uno o più ruoli di amministratore di Azure AD: Amministratore globale, Amministratore con privilegi, Amministratore di Exchange Online e Amministratore di SharePoint Online. Abilitare [Multi-Factor Authentication per gli account amministratore](/azure/active-directory/authentication/howto-mfa-userstates) e assicurarsi che gli utenti dell'account amministratore si siano registrati.

**Procedura consigliata:** per gli account amministratore critici, disporre di una workstation di amministrazione in cui le attività di produzione non sono consentite, ad esempio esplorazione e posta elettronica. Questo proteggerà i tuoi account amministratore dai vettori di attacco che utilizzano la navigazione e la posta elettronica e ridurrà significativamente il rischio di incidenti gravi.
**Dettaglio**: Utilizzare una workstation amministrativa. Scegliere un livello di sicurezza della workstation:

- I dispositivi di produttività altamente sicuri offrono sicurezza avanzata per la navigazione e altre attività di produttività.
- Le workstation con [accesso privilegiato (PAW, Privileged Access Workstation)](/windows-server/identity/securing-privileged-access/privileged-access-workstations) forniscono un sistema operativo dedicato protetto da attacchi Internet e vettori di minacce per le attività sensibili.

**Procedura consigliata:** deprovisioning degli account amministratore quando i dipendenti lasciano l'organizzazione.
**Dettaglio**: Disporre un processo che disabilita o elimina gli account amministratore quando i dipendenti lasciano l'organizzazione.

**Procedura consigliata**: Testare regolarmente gli account amministratore utilizzando le tecniche di attacco correnti.
**Dettaglio**: Usare Office 365 Attack Simulator o un'offerta di terze parti per eseguire scenari di attacco realistici nell'organizzazione. Questo può aiutare a trovare gli utenti vulnerabili prima che si verifichi un attacco reale.

**Procedura consigliata**: intervenire per mitigare l'effetto delle tecniche di attacco usate più di frequente.  
**Dettagli**: [identificare gli account Microsoft in ruoli amministrativi che devono essere spostati in account aziendali o dell'istituto di istruzione](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

[Separare gli account utente e l'inoltro della posta elettronica per gli account amministratore globale](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)  

[Verificare che le password degli account amministrativi siano state modificate di recente](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Attivare la sincronizzazione dell'hash delle password](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)  

[Richiedere l'autenticazione a più fattori per gli utenti con tutti i ruoli con privilegi e gli utenti esposti](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Ottenere Office 365 Secure Score (se si usa Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#obtain-your-office-365-secure-score-if-using-office-365)  

[Esaminare le indicazioni di sicurezza e conformità di Office 365 (se si usa Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Configurare Monitoraggio attività di Office 365 (se si usa Office 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#configure-office-365-activity-monitoring-if-using-office-365)  

[Stabilire i proprietari del piano di risposta a eventi imprevisti ed emergenze](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#establish-incidentemergency-response-plan-owners)  

[Proteggere gli account amministrativi con privilegi locali](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)

Se non si protegge l'accesso con privilegi, si noterà che troppi utenti hanno ruoli con privilegi elevati che sono più vulnerabili agli attacchi. Attori dannosi, come gli utenti malintenzionati, prendono spesso di mira gli account amministratore e altri elementi di accesso con privilegi per accedere ai dati sensibili e ai sistemi usando attacchi con furto di credenziali.

## <a name="control-locations-where-resources-are-created"></a>Controllare le posizioni in cui vengono create le risorse

Permettere agli operatori cloud di eseguire attività senza infrangere convenzioni necessarie per la gestione delle risorse dell'organizzazione è molto importante. Per controllare i percorsi in cui le risorse vengono create, è consigliabile impostarli come hardcoded a livello dell'organizzazione.

Usare [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview) per creare criteri di sicurezza con definizioni che descrivono le risorse o le azioni negate in modo specifico. Le definizioni dei criteri vengono assegnate all'ambito desiderato, ad esempio la sottoscrizione, un gruppo di risorse o una singola risorsa.

> [!NOTE]
> I criteri di sicurezza non corrispondono al controllo degli accessi in base al ruolo. In realtà, usano il controllo degli accessi in base al ruolo per autorizzare gli utenti a creare tali risorse.
>
>

Le organizzazioni che non controllano le modalità di creazione delle risorse sono più soggette a un uso improprio del servizio da parte degli utenti, che potrebbero creare più risorse del necessario. Il rafforzamento del processo di creazione delle risorse è un passaggio importante per la protezione di uno scenario multi-tenant.

## <a name="actively-monitor-for-suspicious-activities"></a>Monitorare attivamente le attività sospette

Un sistema di monitoraggio delle identità attive può rilevare rapidamente comportamenti sospetti e attivare un avviso per un'analisi approfondita. Nella tabella seguente sono elencate due funzionalità di Azure AD che consentono alle organizzazioni di monitorare le identità:

**Procedura consigliata**: predisporre un metodo per individuare:

- Tentativi di accedere [senza tracciamento](/azure/active-directory/active-directory-reporting-sign-ins-from-unknown-sources).
- Attacchi di [forza bruta](/azure/active-directory/active-directory-reporting-sign-ins-after-multiple-failures) contro un account specifico.
- Tentativi di accedere da più posizioni.
- Gli invii da [dispositivi infetti](/azure/active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices).
- Indirizzi IP sospetti.

**Dettagli**: usare i [report anomalie](/azure/active-directory/active-directory-view-access-usage-reports) di Azure AD Premium. Predisporre processi e procedure in modo che gli amministratori IT possano eseguire tali report ogni giorno o su richiesta, in genere in uno scenario di risposta a eventi imprevisti.

**Procedura consigliate**: disporre di un sistema di monitoraggio attivo che avvisi dei rischi e il cui livello di rischio (alto, medio o basso) possa essere adattato ai requisiti aziendali.   
**Dettagli**: usare [Azure AD Identity Protection](/azure/active-directory/active-directory-identityprotection), che segnala i rischi correnti nel relativo dashboard e invia notifiche riassuntive giornaliere tramite posta elettronica. Per contribuire a proteggere le identità dell'organizzazione, è possibile configurare criteri basati sul rischio che rispondano automaticamente ai problemi rilevati quando viene raggiunto un livello di rischio specificato.

Le organizzazioni che non monitorano attivamente i sistemi di identità sono esposti al rischio di compromissione delle credenziali utente. Se l'organizzazione non è consapevole dello svolgimento di attività sospette con tali credenziali, non potrà attenuare questo tipo di minaccia.

## <a name="use-azure-ad-for-storage-authentication"></a>Usare Azure AD per l'autenticazione di archiviazioneUse Azure AD for storage authentication
[Archiviazione di Azure](/azure/storage/common/storage-auth-aad) supporta l'autenticazione e l'autorizzazione con Azure AD per l'archiviazione BLOB e l'archiviazione delle code. Con l'autenticazione di Azure AD è possibile usare il controllo degli accessi in base al ruolo di Azure per concedere autorizzazioni specifiche a utenti, gruppi e applicazioni fino all'ambito di un singolo contenitore BLOB o coda.

È consigliabile usare [Azure AD per l'autenticazione dell'accesso all'archiviazione.](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/)

## <a name="next-step"></a>Passaggio successivo

Per altre procedure consigliate per la sicurezza da usare nella progettazione, la distribuzione e la gestione di soluzioni cloud tramite Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](best-practices-and-patterns.md).
