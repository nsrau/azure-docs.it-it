---
title: Procedure consigliate per la sicurezza con il controllo di accesso e identità di Azure | Documentazione Microsoft
description: Questo articolo illustra una serie di procedure consigliate per il controllo di accesso e la gestione delle identità usando le funzionalità integrate di Azure.
services: security
documentationcenter: na
author: barclayn
manager: barbkess
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/03/2019
ms.author: barclayn
ms.openlocfilehash: 2b57ec7727e8f5b648bcb97e5fae26c63724411c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67127199"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure
Questo articolo illustra una serie di procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure. Le procedure consigliate si basano sull'esperienza di tecnici e clienti con [Azure AD](../active-directory/fundamentals/active-directory-whatis.md).

Per ogni procedura consigliata verrà illustrato:

* Qual è la procedura consigliata
* Il motivo per cui si vuole abilitare tale procedura consigliata
* Quale potrebbe essere il risultato se non fosse possibile abilitare la procedura consigliata
* Alternative possibili alla procedura consigliata
* Come imparare ad abilitare la procedura consigliata

Questo articolo sulle procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure si basa su pareri condivisi, nonché sulle capacità e sui set di funzionalità della piattaforma Azure esistenti quando l'articolo è stato scritto. Le opinioni e le tecnologie cambiano nel tempo e questo articolo verrà aggiornato regolarmente per riflettere tali modifiche.

Procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure illustrate in questo articolo:

* Trattare l'identità come perimetro di sicurezza primario
* Centralizzare la gestione delle identità
* Gestire i tenant connesso
* Abilita Single Sign-On
* Abilitare l'accesso condizionale
* Abilitare la gestione delle password
* Applicare la verifica a più fattori per gli utenti
* Usare il controllo degli accessi in base al ruolo
* Diminuire l'esposizione degli account con privilegi
* Controllare le posizioni in cui si trovano le risorse
* Usare Azure AD per l'autenticazione di archiviazione

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Trattare l'identità come perimetro di sicurezza primario

Molti considerano l'identità come perimetro primario per la sicurezza. Questa strategia rappresenta un cambiamento rispetto al tradizionale ruolo centrale attribuito alla sicurezza della rete. I perimetri di rete diventano sempre più permeabili pertanto la difesa perimetrale non può essere efficace quanto lo era prima dell'esplosione dei dispositivi [BYOD](https://aka.ms/byodcg) e delle applicazioni cloud.

[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) è la soluzione di Microsoft Azure per la gestione delle identità e degli accessi. Azure AD è il servizio Microsoft multi-tenant di gestione delle identità e delle directory basato sul cloud. Combina in un'unica soluzione servizi di directory importanti, gestione degli accessi alle applicazioni e protezione delle identità.

Le sezioni seguenti elencano le procedure consigliate per garantire la sicurezza delle identità e degli accessi tramite Azure AD.

## <a name="centralize-identity-management"></a>Centralizzare la gestione delle identità

In uno scenario di [soluzione ibrida di gestione delle identità](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) è consigliabile integrare le directory cloud e locali. Integrazione consente al team IT di gestire gli account da un'unica posizione, indipendentemente dal fatto in cui viene creato un account. L'integrazione consente inoltre agli utenti di essere più produttivi fornendo un'identità comune per l'accesso alle risorse sia cloud che locali.

**Procedura consigliata**: Stabilire una singola istanza di Azure AD. Coerenza e un singolo origini autorevoli aumenterà chiarezza e ridurre i rischi di protezione da errori umani e la complessità della configurazione.
**Dettagli**: Designare un singolo di Azure AD directory come origine autorevole per gli account aziendali e dell'organizzazione.

**Procedura consigliata**: integrare le directory locali con Azure AD.  
**Dettagli**: sincronizzare la directory locale con la directory cloud usando [Azure AD Connect](../active-directory/connect/active-directory-aadconnect.md).

> [!Note]
> Esistono [fattori che influiscono sulle prestazioni di Azure AD Connect](../active-directory/hybrid/plan-connect-performance-factors.md). Verificare che Azure AD Connect ha una capacità sufficiente per mantenere i sistemi da produttività e sicurezza impedendo di biglietti. Alle organizzazioni di grandi o complesse (organizzazioni il provisioning di più di 100.000 oggetti) devono seguire le [raccomandazioni](../active-directory/hybrid/whatis-hybrid-identity.md) per ottimizzare l'implementazione di Azure AD Connect.

**Procedura consigliata**: Non sincronizzare gli account Azure ad con privilegi elevati nell'istanza di Active Directory esistente.
**Dettagli**: Non modificare il valore predefinito [configurazione di Azure AD Connect](../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) che esclude questi account. Questa configurazione riduce il rischio che eventuali antagonisti siano pivot dal cloud agli asset locali (che è stato possibile creare una richiesta di intervento principali).

**Procedura consigliata**: attivare la sincronizzazione dell'hash delle password.  
**Dettagli**: Sincronizzazione dell'hash delle password è una funzionalità usata per sincronizzare gli hash delle password utente da un'istanza di Active Directory locale a Azure basato su cloud istanza di AD. La sincronizzazione consente di proteggersi da credenziali perse riprodurle dagli attacchi precedenti.

Anche se si decide di usare la federazione con Active Directory Federation Services (AD FS) o altri provider di identità, è possibile configurare la sincronizzazione dell'hash delle password come backup in caso di errore o temporanea indisponibilità dei server locali. La sincronizzazione consente agli utenti di accedere al servizio usando la stessa password che usano per accedere all'istanza di Active Directory locale. Consente inoltre di rilevare le credenziali compromesse confrontando gli hash delle password sincronizzati con password segnalate come compromesse, se un utente ha usato l'indirizzo di posta elettronica e password in altri servizi connessi ad Azure AD Identity Protection.

Per altre informazioni, vedere [Implementare la sincronizzazione dell'hash delle password con il servizio di sincronizzazione Azure AD Connect](../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

**Procedura consigliata**: Per i nuovi sviluppi dell'applicazione, usare Azure AD per l'autenticazione.
**Dettagli**: Usare le funzionalità corrette per supportare l'autenticazione:

  - Azure AD per i dipendenti
  - [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/) per gli utenti guest e partner esterni
  - [Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/) per controllare come i clienti di effettuare l'iscrizione, accedere e gestire i propri profili quando usano le applicazioni

Le organizzazioni che non integrano l'identità locale con la propria identità cloud possono subire un sovraccarico maggiore nella gestione degli account. Questo sovraccarico aumenta le probabilità di incorrere in errori e violazioni della sicurezza.

> [!Note]
> È necessario scegliere quali directory critici account si troverà nella ed elabora indica se la workstation amministrativa utilizzata è gestito dai servizi cloud nuovo o esistente. Usando identità i processi di provisioning e gestione esistente può ridurre alcuni rischi, ma può anche creare il rischio di un utente malintenzionato di compromettere un account locale e la trasformazione tramite pivot nel cloud. Si potrebbe voler usare una strategia diversa per ruoli diversi (ad esempio, gli amministratori IT e agli amministratori delle unità aziendali). Sono disponibili due opzioni. Prima opzione consiste nel creare gli account di Azure AD che non sono sincronizzati con l'istanza di Active Directory locale. Aggiungere la workstation amministrativa ad Azure AD, che è possibile gestire e applicare patch con Microsoft Intune. Seconda opzione consiste nell'usare gli account di amministratore esistente mediante la sincronizzazione per l'istanza di Active Directory locale. Usare workstation esistenti nel dominio Active Directory per la gestione e sicurezza.

## <a name="manage-connected-tenants"></a>Gestire i tenant connesso
L'organizzazione di sicurezza necessita di visibilità per valutare i rischi e determinare se i criteri dell'organizzazione e ai requisiti normativi, sono stati seguiti. È necessario assicurarsi che l'organizzazione di sicurezza dispone di visibilità in tutte le sottoscrizioni connesse a reti e ambiente di produzione (tramite [Azure ExpressRoute](../expressroute/expressroute-introduction.md) oppure [VPN site-to-site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)). Oggetto [amministratore globale/aziendale amministratore](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator) in Azure AD possono elevare l'accesso al [amministratore accesso utenti](../role-based-access-control/built-in-roles.md#user-access-administrator) ruolo e visualizzare tutte le sottoscrizioni e gestiti i gruppi connessi all'ambiente.

Visualizzare [elevare l'accesso per gestire tutte le sottoscrizioni di Azure e i gruppi di gestione](../role-based-access-control/elevate-access-global-admin.md) per garantire che il gruppo di sicurezza e possono visualizzare tutte le sottoscrizioni o gruppi di gestione connesso al proprio ambiente. Dopo aver valutato i rischi, è necessario rimuovere l'accesso con privilegi elevato.

## <a name="enable-single-sign-on"></a>Abilita Single Sign-On

In un mondo incentrato su dispositivi mobili e cloud, è importante poter accedere con Single Sign-On (SSO) a dispositivi, app e servizi da qualsiasi posizione in modo che gli utenti siano produttivi sempre e ovunque. La gestione di più soluzioni di identità rappresenta un problema di amministrazione non solo per il reparto IT, ma anche per gli utenti finali che devono tenere a mente più password.

Utilizzando la stessa soluzione di identità per tutte le applicazioni e risorse, è possibile ottenere l'accesso Single Sign-On. Gli utenti hanno la possibilità di usare lo stesso set di credenziali per accedere alle risorse, indipendentemente dalla dislocazione in locale o nel cloud di tali risorse.

**Procedura consigliata**: abilitare SSO.  
**Dettagli**: Azure AD [estende Active Directory locale](../active-directory/connect/active-directory-aadconnect.md) al cloud. Gli utenti possono usare il loro account aziendale o dell'istituto di istruzione principale per i dispositivi aggiunti al dominio, per le risorse aziendali e per tutte le applicazioni Web e SaaS necessarie per svolgere le loro attività. Oltre a eliminare la necessità di ricordare diversi set di nomi utente e password, permette anche di effettuare automaticamente il provisioning o il deprovisioning dell'accesso alle applicazioni degli utenti in base all'appartenenza ai gruppi dell'organizzazione e al relativo stato come dipendenti. Permette poi di controllare l'accesso ad app della raccolta o app locali sviluppate e pubblicate tramite il [proxy di applicazione di Azure AD](../active-directory/active-directory-application-proxy-get-started.md).

Usare SSO per consentire agli utenti di accedere alle [applicazioni SaaS](../active-directory/active-directory-appssoaccess-whatis.md) in base all'account aziendale o dell'istituto di istruzione in Azure AD. Questo vale non solo per le app SaaS Microsoft, ma anche per altre app come [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) e [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). L'applicazione può essere configurata per l'uso di Azure AD come provider di [identità basata su SAML](../active-directory/fundamentals-identity.md). Come controllo di sicurezza, Azure AD non rilascia all'utente un token per l'accesso all'applicazione a meno che l'accesso non sia stato concesso all'utente con Azure AD. L'accesso può essere concesso direttamente agli utenti o attraverso un gruppo di cui sono membri.

Le organizzazioni che non creano un'identità comune per stabilire l'accesso SSO per utenti e applicazioni sono più esposte a situazioni in cui gli utenti devono utilizzare più password. In queste situazioni aumenta la probabilità di riutilizzare le password o di utilizzare password vulnerabili.

## <a name="turn-on-conditional-access"></a>Abilitare l'accesso condizionale

Gli utenti possono accedere alle risorse dell'organizzazione con una serie di dispositivi e app ovunque si trovino. Gli amministratori IT, si desidera assicurarsi che i dispositivi soddisfino gli standard di sicurezza e conformità. Tuttavia, non è più sufficiente concentrarsi solo su chi può accedere a una risorsa.

Per bilanciare la sicurezza e la produttività, è necessario pensare al modo in cui si accede a una risorsa prima è possibile prendere una decisione sul controllo degli accessi. Grazie all'accesso condizionale di Azure AD, è possibile soddisfare questo requisito. Grazie all'accesso condizionale, è possibile prendere decisioni di controllo di accesso automatizzato in base alle condizioni per l'accesso alle App cloud.

**Procedura consigliata**: gestire e controllare l'accesso alle risorse aziendali.  
**Dettagli**: Configurare Azure AD [accesso condizionale](../active-directory/active-directory-conditional-access-azure-portal.md) basato su un gruppo, posizione e alla sensibilità dell'applicazione per le app SaaS e le app connesse AD Azure.

**Procedura consigliata**: Bloccare i protocolli di autenticazione legacy.
**Dettagli**: Gli utenti malintenzionati deboli protocolli meno recenti ogni giorno, in particolare per gli attacchi spray password. Configurare l'accesso condizionale per bloccare protocolli legacy. Vedere il video [Azure AD: Operazioni consigliate e sconsigliate](https://www.youtube.com/watch?v=wGk0J4z90GI) per altre informazioni.

## <a name="enable-password-management"></a>Abilitare la gestione delle password

Se sono presenti più tenant o si vuole consentire agli utenti di [reimpostare la propria password](../active-directory/active-directory-passwords-update-your-own-password.md), è importante adottare criteri di sicurezza adatti per prevenire l'uso improprio.

**Procedura consigliata**: configurare la reimpostazione della password self-service per gli utenti.  
**Dettagli**: usare la funzionalità di [reimpostazione della password self-service](../active-directory-b2c/active-directory-b2c-reference-sspr.md) di Azure AD.

**Procedura consigliata**: monitorare come o se la reimpostazione della password self-service viene effettivamente usata.  
**Dettagli**: monitorare gli utenti che eseguono la registrazione usando il [report Attività di registrazione reimpostazione password](../active-directory/active-directory-passwords-get-insights.md) di Azure AD. La funzionalità di creazione di report disponibile in Azure AD consente di rispondere a domande specifiche grazie a report predefiniti. Se si dispone di una licenza appropriata, è anche possibile creare query personalizzate.

**Procedura consigliata**: Estendere i criteri di password basati su cloud all'infrastruttura locale.
**Dettagli**: Ottimizzare i criteri password all'interno dell'organizzazione eseguendo gli stessi controlli per la modifica della password in locale, come avviene per la modifica della password basata sul cloud. Installare [protezione tramite password di Azure AD](../active-directory/authentication/concept-password-ban-bad.md) per Windows Server Active Directory gli agenti in locale estendere gli elenchi delle password da escludere nell'infrastruttura esistente. Gli utenti e amministratori che modificano, impostare o reimpostare le password in locale sono necessaria per la conformità con gli stessi criteri di password come utenti solo cloud.

## <a name="enforce-multi-factor-verification-for-users"></a>Applicare la verifica a più fattori per gli utenti

È consigliabile richiedere la verifica in due passaggi per tutti gli utenti. Questo vale anche per gli amministratori e gli altri utenti dell'organizzazione la cui compromissione dell'account potrebbe avere un impatto significativo, ad esempio, i dirigenti del reparto finanziario.

Sono disponibili vari modi per richiedere la verifica in due passaggi. L'opzione migliore dipende dall'obiettivo che si intende raggiungere, dall'edizione di Azure AD in esecuzione e dal programma di licenza. Vedere [Come richiedere la verifica in due passaggi per un utente](../active-directory/authentication/howto-mfa-userstates.md) per determinare l'opzione migliore per il proprio ambiente. Altre informazioni sulle licenze e i prezzi sono disponibili nelle pagine relative ai prezzi di [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) e [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

Di seguito sono indicati i vantaggi e le opzioni per l'abilitazione della verifica in due passaggi:

**Opzione 1**: [abilitare l'autenticazione a più fattori modificando lo stato utente](../active-directory/authentication/howto-mfa-userstates.md).   
**Vantaggio**: questo è il metodo tradizionale per richiedere la verifica in due passaggi. Funziona sia con [Azure Multi-Factor Authentication nel cloud e Server Multi-Factor Authentication](../active-directory/authentication/concept-mfa-whichversion.md). Con questo metodo richiede agli utenti di eseguire la verifica in due passaggi ogni volta che accedono ed esegue l'override di criteri di accesso condizionale.

Per determinare dove è necessario abilitare multi-Factor Authentication, vedere [quale versione di Azure MFA sia corretta per la mia organizzazione?](../active-directory/authentication/concept-mfa-whichversion.md).

**Opzione 2**: [Abilitare multi-Factor Authentication con criteri di accesso condizionale](../active-directory/authentication/howto-mfa-getstarted.md).
**Vantaggio**: Questa opzione consente di per la richiesta di verifica in due passaggi in condizioni specifiche usando [accesso condizionale](../active-directory/active-directory-conditional-access-azure-portal.md). L'accesso di utenti da posizioni diverse, l'uso di dispositivi non attendibili o l'uso di applicazioni considerate rischiose possono essere considerate condizioni specifiche. La definizione di condizioni specifiche in cui si richiede la verifica in due passaggi consente di evitare di chiedere continuamente conferma agli utenti della loro identità, che può risultare spiacevole.

Questo rappresenta il mezzo più flessibile per abilitare la verifica in due passaggi per gli utenti. L'abilitazione di un criterio di accesso condizionale funziona solo per Azure multi-Factor Authentication nel cloud ed è una funzionalità premium di Azure AD. Per altre informazioni su questo metodo, vedere [Implementare Azure Multi-Factor Authentication basato su cloud](../active-directory/authentication/howto-mfa-getstarted.md).

**Opzione 3**: Abilitare multi-Factor Authentication con criteri di accesso condizionale per la valutazione di rischio utente e l'accesso di [Azure AD Identity Protection](../active-directory/authentication/tutorial-risk-based-sspr-mfa.md).   
**Vantaggio**: questa opzione consente di:

- Rilevare le potenziali vulnerabilità per le identità dell'organizzazione.
- Configurare risposte automatizzate alle azioni sospette rilevate in relazione alle identità dell'organizzazione.
- Esaminare gli eventi imprevisti sospetti ed eseguire l'azione appropriata per risolverli.

Questo metodo usa la valutazione del rischio di Azure AD Identity Protection per determinare se la verifica in due passaggi è necessaria in funzione del rischio per l'utente e l'accesso per tutte le applicazioni cloud. Questo metodo richiede una licenza di Azure Active Directory P2. Per altre informazioni su questo metodo, vedere [Azure Active Directory Identity Protection](../active-directory/identity-protection/overview.md).

> [!Note]
> Opzione 1, l'abilitazione di multi-Factor Authentication modificando lo stato utente, esegue l'override di criteri di accesso condizionale. Poiché le opzioni 2 e 3 usano criteri di accesso condizionale, è possibile usare l'opzione 1 con essi.

Le organizzazioni che non aggiungono livelli supplementari di protezione delle identità, come la verifica in due passaggi, sono più vulnerabili agli attacchi con furto di credenziali. Un attacco con furto di credenziali comporta il rischio di compromissione dei dati.

## <a name="use-role-based-access-control"></a>Usare il controllo degli accessi in base al ruolo
Gestione degli accessi per le risorse cloud è fondamentale per qualsiasi organizzazione che usa il cloud. [Controllo di accesso basato sui ruoli (RBAC)](../role-based-access-control/overview.md) consente di gestire chi ha accesso alle risorse di Azure, ciò che avviene con tali risorse e sulle aree che hanno accesso.

Designazione di gruppi o singoli ruoli di responsabili a specifiche funzioni in Azure consente di evitare la confusione che può condurre a risorse umane e gli errori di automazione che creano rischi di sicurezza. Per le organizzazioni che intendono applicare criteri di sicurezza per l'accesso ai dati è fondamentale limitare l'accesso in base a principi di [riservatezza](https://en.wikipedia.org/wiki/Need_to_know) e [privilegi minimi](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

Il team di sicurezza deve visibilità sulle risorse di Azure per poter valutare e correggere il rischio. Se il team di protezione ha responsabilità operativa, sono necessarie autorizzazioni aggiuntive per svolgere il proprio lavoro.

È possibile usare [RBAC](../role-based-access-control/overview.md) per assegnare autorizzazioni a utenti, gruppi e applicazioni in un determinato ambito. L'ambito di un'assegnazione di ruolo può essere una sottoscrizione, un gruppo di risorse o una singola risorsa.

**Procedura consigliata**: Separare i compiti all'interno del team e concedere solo il livello di accesso agli utenti che hanno bisogno per svolgere il proprio lavoro. Anziché concedere a tutti autorizzazioni senza restrizioni di sottoscrizione di Azure o le risorse, di consentire solo determinate azioni in un determinato ambito.
**Dettagli**: Uso [ruoli RBAC predefiniti](../role-based-access-control/built-in-roles.md) in Azure per assegnare privilegi agli utenti.

> [!Note]
> Autorizzazioni specifiche creano inutili complessità e la confusione, accumulando in una configurazione "legacy" che è difficile da correggere senza timore di compromettere il funzionamento qualcosa. Evitare le autorizzazioni specifiche delle risorse. Usare invece i gruppi di gestione per le autorizzazioni a livello aziendale e i gruppi di risorse per le autorizzazioni all'interno delle sottoscrizioni. Evitare le autorizzazioni specifiche dell'utente. Al contrario, assegnare l'accesso ai gruppi in Azure AD.

**Procedura consigliata**: Concedere ai team di sicurezza con accesso di responsabilità di Azure per visualizzare le risorse di Azure in modo da poter valutare e correggere il rischio.
**Dettagli**: Concedere ai team di sicurezza di RBAC [lettura per la sicurezza](../role-based-access-control/built-in-roles.md#security-reader) ruolo. È possibile usare il gruppo di gestione radice o il gruppo di gestione di segmento, a seconda dell'ambito delle responsabilità:

- **Gruppo di gestione radice** per i team responsabili di tutte le risorse aziendali
- **Gruppo di gestione di segmenti** per i team con ambito limitato (comunemente a causa dei limiti dell'organizzazione normativi o ad altri)

**Procedura consigliata**: Concedere le autorizzazioni appropriate per i team di sicurezza responsabili operativi diretto.
**Dettagli**: Esaminare i ruoli RBAC predefiniti per l'assegnazione di ruolo appropriate. Se i ruoli predefiniti non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare [ruoli personalizzati per le risorse di Azure](../role-based-access-control/custom-roles.md). Come con ruoli predefiniti, è possibile assegnare ruoli personalizzati per utenti, gruppi e le entità servizio nella sottoscrizione, gruppo di risorse e gli ambiti di risorse.

**Procedure consigliate**: Accesso di Centro sicurezza di Azure concedere ai ruoli di sicurezza che ne hanno necessità. Il Centro sicurezza consente ai team di sicurezza identificare rapidamente e correggere i rischi.
**Dettagli**: Aggiungere i team di sicurezza con queste esigenze di RBAC [amministratore della sicurezza](../role-based-access-control/built-in-roles.md#security-admin) ruolo affinché possano visualizzare i criteri di sicurezza, gli stati di sicurezza, modificare i criteri di sicurezza, visualizzare gli avvisi e raccomandazioni e ignorare gli avvisi e raccomandazioni. È possibile farlo tramite il gruppo di gestione radice o il gruppo di gestione di segmento, a seconda dell'ambito delle responsabilità.

Organizzazioni che non applicano il controllo di accesso dati usando le funzionalità, ad esempio RBAC potrebbero concedere più privilegi del necessario agli utenti. Questo può causare compromissione dei dati, consentendo agli utenti di accedere a tipi di dati (ad esempio, alto impatto aziendale) che non dovrebbero disporre.

## <a name="lower-exposure-of-privileged-accounts"></a>Diminuire l'esposizione degli account con privilegi

La protezione dell'accesso con privilegi è il primo fondamentale passo per proteggere le risorse aziendali. Riducendo al minimo il numero di utenti che hanno accesso a informazioni o risorse protette riduce le probabilità che un utente malintenzionato acceda al sistema, o che un utente autorizzato comprometta inavvertitamente una risorsa sensibile.

Gli account con privilegi sono gli account che amministrano e gestiscono i sistemi IT. Gli utenti malintenzionati usano questi account per ottenere l'accesso ai dati e ai sistemi di un'organizzazione. Per proteggere l'accesso con privilegi, è necessario isolare gli account e i sistemi dal rischio di esposizione a utenti malintenzionati.

Si consiglia di sviluppare e seguire una roadmap per proteggere l'accesso con privilegi dagli utenti malintenzionati. Per informazioni sulla creazione di una roadmap dettagliata per proteggere le identità e gli accessi che sono gestiti o segnalati in Azure AD, Microsoft Azure, Office 365 e altri servizi cloud, vedere [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

Di seguito sono riassunte le procedure consigliate disponibili in [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md):

**Procedura consigliata**: gestire, controllare e monitorare l'accesso ad account con privilegi.   
**Dettagli**: attivare [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/active-directory-securing-privileged-access.md). Dopo l'attivazione di Privileged Identity Management, si riceveranno messaggi di posta elettronica di notifica in caso di modifiche ai ruoli di accesso con privilegi. Queste notifiche forniscono un avviso tempestivo in caso di aggiunta di ulteriori utenti a ruoli con privilegi elevati nella directory.

**Procedura consigliata**: Verificare che tutti gli account amministrativi critici vengono gestiti gli account Azure AD.
**Dettagli**: Rimuovere tutti gli account utente dai ruoli di amministratore critici (ad esempio, gli account Microsoft come outlook.com, hotmail.com e live.com).

**Procedura consigliata**: Verificare che tutti i ruoli di amministratore critiche hanno un account separato per le attività amministrative per evitare di phishing e altri attacchi per danneggiare i privilegi amministrativi.
**Dettagli**: Creare un account amministrativo separato che è assegnato ai privilegi necessari per eseguire le attività amministrative. Blocca l'uso di questi account amministrativi per gli strumenti di produttività giornaliera come posta elettronica di Microsoft Office 365 o l'esplorazione web arbitrari.

**Procedura consigliata**: identificare e classificare gli account che si trovano in ruoli con privilegi elevati.   
**Dettagli**: dopo aver attivato Azure AD Privileged Identity Management, visualizzare gli utenti con i ruoli di amministratore globale o di amministratore del ruolo con privilegi e con altri ruoli con privilegi elevati. Rimuovere tutti gli account che non sono più necessari in questi ruoli e classificare gli account rimanenti assegnati ai ruoli di amministratore:

- Singolarmente assegnati a utenti amministratori e che possono essere usati per scopi non amministrativi, ad esempio, posta elettronica personale
- Singolarmente assegnati a utenti amministratori e designati esclusivamente per scopi amministrativi
- Condivisi tra più utenti
- Per scenari di accesso di emergenza
- Per script automatizzati
- Per utenti esterni

**Procedura consigliata**: implementare l'accesso "just in time" (JIT) per ridurre ulteriormente il tempo di esposizione dei privilegi e aumentare la visibilità sull'uso degli account con privilegi.   
**Dettagli**: Azure AD Privileged Identity Management consente di effettuare le operazioni seguenti:

- Limitare gli utenti a prendere solo il proprio accesso con privilegi JIT.
- Assegnare i ruoli per un periodo di tempo ridotto sapendo che i privilegi vengono revocati automaticamente.

**Procedura consigliata**: definire almeno due account di accesso di emergenza.   
**Dettagli**: gli account di accesso di emergenza consentono alle organizzazioni di limitare l'accesso con privilegi in un ambiente Azure Active Directory esistente. Si tratta di account con privilegi elevati non assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati a scenari in cui non è possibile usare i normali account amministrativi. Le organizzazioni devono limitare l'utilizzo degli account di emergenza per la sola quantità di tempo necessaria.

Valutare gli account assegnati o idonei per il ruolo di amministratore globale. Se non sono presenti account solo cloud con il dominio `*.onmicrosoft.com` (destinati all'accesso di emergenza), crearli. Per altre informazioni, vedere [Gestire gli account amministrativi di accesso di emergenza in Azure AD](../active-directory/users-groups-roles/directory-emergency-access.md).

**Procedura consigliata**: Predisporre un processo "break glass" in caso di emergenza.
**Dettagli**: Seguire i passaggi descritti in [accesso con privilegi di protezione per le distribuzioni ibride e cloud di Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

**Procedura consigliata**: Richiedi tutti gli account amministrativi critici sia senza password (scelta consigliata) o richiedere multi-Factor Authentication.
**Dettagli**: Usare la [app Microsoft Authenticator](../active-directory/authentication/howto-authentication-phone-sign-in.md) per accedere a qualsiasi account di Azure AD senza usare una password. Ad esempio [Windows Hello for Business](https://docs.microsoft.com/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator utilizza l'autenticazione basata su chiavi per abilitare una credenziale utente che è associata a un dispositivo e Usa l'autenticazione biometrica o un PIN.

Richiedi Azure multi-Factor Authentication al momento dell'accesso per tutti i singoli utenti che sono assegnati in modo permanente uno o più ruoli di amministratore di Azure AD: Amministratore globale, amministratore dei ruoli con privilegi, amministratore di Exchange Online e amministratore di SharePoint Online. Abilitare [multi-Factor Authentication per gli account amministratore](../active-directory/authentication/howto-mfa-userstates.md) e assicurarsi che gli utenti dell'account amministratore sono registrati.

**Procedura consigliata**: Per gli account amministrativi critici, avere una workstation amministrativa in cui le attività di produzione non sono consentite (ad esempio esplorazione e l'indirizzo di posta elettronica). Questo verrà proteggere gli account amministratore da vettori di attacco che utilizzano l'esplorazione e l'indirizzo di posta elettronica e di ridurre significativamente il rischio di una richiesta di intervento principali.
**Dettagli**: Usare una workstation amministrativa. Scegliere un livello di protezione delle workstation:

- I dispositivi a sicurezza elevata produttività offrono sicurezza avanzata per l'esplorazione e altre attività di produttività.
- [Privileged Access sistemi Paw (workstation)](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations) garantiscono un sistema operativo dedicato che è protetto da attacchi da internet e dai vettori di minacce per le attività sensibili.

**Procedura consigliata**: Quando un dipendente lascia l'organizzazione, eseguire il deprovisioning degli account amministratore.
**Dettagli**: Avere un processo che disabilita o Elimina gli account amministrativi, quando un dipendente lascia l'organizzazione.

**Procedura consigliata**: Testare regolarmente gli account amministratore tramite tecniche di attacco corrente.
**Dettagli**: Usare un simulatore di attacco di Office 365 o di terze parti che offrono per l'esecuzione di scenari di attacco realistici all'interno dell'organizzazione. Ciò consente di trovare gli utenti vulnerabili prima che si verifichi un attacco reale.

**Procedura consigliata**: intervenire per mitigare l'effetto delle tecniche di attacco usate più di frequente.  
**Dettagli**: [identificare gli account Microsoft in ruoli amministrativi per cui è necessario passare ad account aziendali o dell'istituto di istruzione](../active-directory/users-groups-roles/directory-admin-roles-secure.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts).  

[Separare gli account utente e l'inoltro della posta elettronica per gli account amministratore globale](../active-directory/users-groups-roles/directory-admin-roles-secure.md)  

[Verificare che le password degli account amministrativi siano state modificate di recente](../active-directory/users-groups-roles/directory-admin-roles-secure.md#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Attivare la sincronizzazione dell'hash delle password](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)  

[Richiedere l'autenticazione a più fattori per gli utenti con tutti i ruoli con privilegi e gli utenti esposti](../active-directory/users-groups-roles/directory-admin-roles-secure.md#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Ottenere Office 365 Secure Score (se si usa Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#obtain-your-office-365-secure-score-if-using-office-365)  

[Esaminare le indicazioni di sicurezza e conformità di Office 365 (se si usa Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Configurare Monitoraggio attività di Office 365 (se si usa Office 365)](../active-directory/users-groups-roles/directory-admin-roles-secure.md#configure-office-365-activity-monitoring-if-using-office-365)  

[Stabilire i proprietari del piano di risposta a eventi imprevisti ed emergenze](../active-directory/users-groups-roles/directory-admin-roles-secure.md#establish-incidentemergency-response-plan-owners)  

[Proteggere gli account amministrativi con privilegi locali](../active-directory/users-groups-roles/directory-admin-roles-secure.md#turn-on-password-hash-synchronization)

Se non si protegge l'accesso con privilegi, si noterà che troppi utenti hanno ruoli con privilegi elevati che sono più vulnerabili agli attacchi. Attori dannosi, come gli utenti malintenzionati, prendono spesso di mira gli account amministratore e altri elementi di accesso con privilegi per accedere ai dati sensibili e ai sistemi usando attacchi con furto di credenziali.

## <a name="control-locations-where-resources-are-created"></a>Controllare le posizioni in cui vengono create le risorse

Permettere agli operatori cloud di eseguire attività senza infrangere convenzioni necessarie per la gestione delle risorse dell'organizzazione è molto importante. Per controllare i percorsi in cui le risorse vengono create, è consigliabile impostarli come hardcoded a livello dell'organizzazione.

Usare [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) per creare criteri di sicurezza con definizioni che descrivono le risorse o le azioni negate in modo specifico. Le definizioni dei criteri vengono assegnate all'ambito desiderato, ad esempio la sottoscrizione, un gruppo di risorse o una singola risorsa.

> [!NOTE]
> I criteri di sicurezza non corrispondono al controllo degli accessi in base al ruolo. In realtà, usano il controllo degli accessi in base al ruolo per autorizzare gli utenti a creare tali risorse.
>
>

Le organizzazioni che non controllano le modalità di creazione delle risorse sono più soggette a un uso improprio del servizio da parte degli utenti, che potrebbero creare più risorse del necessario. Il rafforzamento del processo di creazione delle risorse è un passaggio importante per la protezione di uno scenario multi-tenant.

## <a name="actively-monitor-for-suspicious-activities"></a>Monitorare attivamente le attività sospette

Un sistema di monitoraggio delle identità attive può rilevare rapidamente comportamenti sospetti e attivare un avviso per un'analisi approfondita. Nella tabella seguente sono elencate due funzionalità di Azure AD che consentono alle organizzazioni di monitorare le identità:

**Procedura consigliata**: predisporre un metodo per individuare:

- Tentativi di accedere [senza tracciamento](../active-directory/active-directory-reporting-sign-ins-from-unknown-sources.md).
- Attacchi di [forza bruta](../active-directory/active-directory-reporting-sign-ins-after-multiple-failures.md) contro un account specifico.
- Tentativi di accedere da più posizioni.
- Accessi da [dispositivi infetti](../active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices.md).
- Indirizzi IP sospetti.

**Dettagli**: usare i [report anomalie](../active-directory/active-directory-view-access-usage-reports.md) di Azure AD Premium. Predisporre processi e procedure in modo che gli amministratori IT possano eseguire tali report ogni giorno o su richiesta, in genere in uno scenario di risposta a eventi imprevisti.

**Procedura consigliata**: disporre di un sistema di monitoraggio attivo che avvisi dei rischi e il cui livello di rischio (alto, medio o basso) possa essere adattato ai requisiti aziendali.   
**Dettagli**: usare [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md), che segnala i rischi correnti nel relativo dashboard e invia notifiche riassuntive giornaliere tramite posta elettronica. Per contribuire a proteggere le identità dell'organizzazione, è possibile configurare criteri basati sul rischio che rispondano automaticamente ai problemi rilevati quando viene raggiunto un livello di rischio specificato.

Le organizzazioni che non monitorano attivamente i sistemi di identità sono esposti al rischio di compromissione delle credenziali utente. Se l'organizzazione non è consapevole dello svolgimento di attività sospette con tali credenziali, non potrà attenuare questo tipo di minaccia.

## <a name="use-azure-ad-for-storage-authentication"></a>Usare Azure AD per l'autenticazione di archiviazione
[Archiviazione di Azure](../storage/common/storage-auth-aad.md) supporta l'autenticazione e autorizzazione con Azure AD per l'archiviazione Blob e archiviazione code. Con l'autenticazione di Azure AD, è possibile usare il controllo degli accessi in base al ruolo di Azure per concedere autorizzazioni specifiche per gli utenti, gruppi e applicazioni down l'ambito di una coda o un contenitore blob singolo.

È consigliabile usare [Azure AD per autenticare l'accesso all'archiviazione](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Passaggio successivo

Per altre procedure consigliate per la sicurezza da usare nella progettazione, la distribuzione e la gestione di soluzioni cloud tramite Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](security-best-practices-and-patterns.md).
