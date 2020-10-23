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
ms.openlocfilehash: 54fb11598dc794248c1aae81734b548341c0eee6
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369472"
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

Lo scopo di questo articolo è fornire una roadmap generale per un comportamento di sicurezza più affidabile dopo la distribuzione basata sull'elenco di controllo "[Cinque passaggi per proteggere l'infrastruttura di identità](steps-secure-identity.md)", che illustra funzionalità e servizi di base.

Le opinioni e le tecnologie cambiano nel tempo e questo articolo verrà aggiornato regolarmente per riflettere tali modifiche.

Procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure illustrate in questo articolo:

* Trattare l'identità come perimetro di sicurezza primario
* Centralizzare la gestione delle identità
* Gestire i tenant connessi
* Abilita Single Sign-On
* Abilitare l'accesso condizionale
* Pianificare miglioramenti per la sicurezza di routine
* Abilitare la gestione delle password
* Applicare la verifica a più fattori per gli utenti
* Usare il controllo degli accessi in base al ruolo
* Diminuire l'esposizione degli account con privilegi
* Controllare le posizioni in cui si trovano le risorse
* Usare Azure AD per l'autenticazione dell'archiviazione

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Trattare l'identità come perimetro di sicurezza primario

Molti considerano l'identità come perimetro primario per la sicurezza. Questa strategia rappresenta un cambiamento rispetto al tradizionale ruolo centrale attribuito alla sicurezza della rete. I perimetri di rete diventano sempre più permeabili pertanto la difesa perimetrale non può essere efficace quanto lo era prima dell'esplosione dei dispositivi [BYOD](https://aka.ms/byodcg) e delle applicazioni cloud.

[Azure Active Directory (Azure AD)](../../active-directory/fundamentals/active-directory-whatis.md) è la soluzione di Microsoft Azure per la gestione delle identità e degli accessi. Azure AD è il servizio Microsoft multi-tenant di gestione delle identità e delle directory basato sul cloud. Combina in un'unica soluzione servizi di directory importanti, gestione degli accessi alle applicazioni e protezione delle identità.

Le sezioni seguenti elencano le procedure consigliate per garantire la sicurezza delle identità e degli accessi tramite Azure AD.

**Procedura consigliata**: centrare i controlli di sicurezza e i rilevamenti su identità di utenti e servizi.
**Dettagli**: usare Azure AD per collocare i controlli e le identità.

## <a name="centralize-identity-management"></a>Centralizzare la gestione delle identità

In uno scenario di [soluzione ibrida di gestione delle identità](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) è consigliabile integrare le directory cloud e locali. L'integrazione consente al team IT di gestire gli account da una posizione, indipendentemente da dove è stato creato un account. L'integrazione rende inoltre gli utenti più produttivi, in quanto fornisce un'identità comune per l'accesso alle risorse cloud e locali.

**Procedura consigliata**: definire una singola istanza di Azure AD. L'uniformità e una singola origine autorevole aumenteranno la chiarezza e ridurranno i rischi per la sicurezza dovuti a errori umani e a configurazioni complesse.
**Dettagli**: designare una singola directory di Azure AD come origine autorevole per gli account aziendali e dell'organizzazione.

**Procedura consigliata**: integrare le directory locali con Azure AD.  
**Dettagli**: sincronizzare la directory locale con la directory cloud usando [Azure AD Connect](/azure/active-directory/connect/active-directory-aadconnect).

> [!Note]
> Esistono [fattori che influiscono sulle prestazioni di Azure AD Connect](../../active-directory/hybrid/plan-connect-performance-factors.md). Assicurarsi che Azure AD Connect disponga di una capacità sufficiente per evitare che sistemi con prestazioni scarse creino problemi di sicurezza e produttività. È consigliabile che le organizzazioni complesse o di grandi dimensioni (organizzazioni che effettuano il provisioning di più di 100.000 oggetti) seguano le [raccomandazioni](../../active-directory/hybrid/whatis-hybrid-identity.md) per ottimizzare l'implementazione di Azure AD Connect.

**Procedura consigliata**: non sincronizzare gli account per Azure AD con privilegi elevati nell'istanza di Active Directory esistente.
**Dettagli**: non modificare la [configurazione di Azure AD Connect](../../active-directory/hybrid/how-to-connect-sync-configure-filtering.md) predefinita che esclude questi account. Questa configurazione consente di attenuare il rischio di passaggio degli antagonisti dal cloud alle risorse locali (che potrebbero creare un evento imprevisto importante).

**Procedura consigliata**: attivare la sincronizzazione dell'hash delle password.  
**Dettagli**: la sincronizzazione degli hash delle password è una funzionalità usata per sincronizzare gli hash delle password utente da un'istanza di Active Directory locale a un'istanza di Azure AD basata sul cloud. Questa sincronizzazione consente di evitare la riproduzione di credenziali compromesse da una fuga dovuta ad attacchi precedenti.

Anche se si decide di usare la federazione con Active Directory Federation Services (AD FS) o altri provider di identità, è possibile configurare la sincronizzazione dell'hash delle password come backup in caso di errore o temporanea indisponibilità dei server locali. Questa sincronizzazione consente agli utenti di accedere al servizio usando la stessa password usata per accedere all'istanza di Active Directory locale. Consente inoltre a Identity Protection di rilevare le credenziali compromesse confrontando gli hash sincronizzati delle password con password segnalate come compromesse, se un utente ha usato lo stesso indirizzo di posta elettronica e la stessa password in altri servizi non connessi ad Azure AD.

Per altre informazioni, vedere [Implementare la sincronizzazione dell'hash delle password con il servizio di sincronizzazione Azure AD Connect](/azure/active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization).

**Procedura consigliata**: per lo sviluppo di nuove applicazioni, usare Azure AD per l'autenticazione.
**Dettagli**: usare le funzionalità corrette per supportare l'autenticazione:

  - Azure AD per i dipendenti
  - [Azure AD B2B](../../active-directory/b2b/index.yml) per utenti guest e partner esterni
  - [Azure AD B2C](../../active-directory-b2c/index.yml) per controllare il modo in cui i clienti effettuano l'iscrizione, l'accesso e la gestione dei profili quando usano le applicazioni fornite

Le organizzazioni che non integrano l'identità locale con la propria identità cloud possono subire un sovraccarico maggiore nella gestione degli account. Questo sovraccarico aumenta le probabilità di incorrere in errori e violazioni della sicurezza.

> [!Note]
> È necessario scegliere in quali directory includere gli account critici e se la workstation di amministrazione usata è gestita da nuovi servizi cloud o da processi esistenti. L'uso di processi di provisioning di gestione e identità esistenti può ridurre alcuni rischi, ma può anche creare il rischio che un utente malintenzionato comprometta un account locale e passi al cloud. Potrebbe essere necessario usare una strategia diversa per ruoli diversi, ad esempio amministratori IT e amministratori di business unit. Sono disponibili due opzioni. La prima opzione consiste nel creare account di Azure AD non sincronizzati con l'istanza di Active Directory locale. Aggiungere ad Azure AD la workstation di amministrazione, che può essere gestita e corretta tramite patch usando Microsoft Intune. La seconda opzione consiste nell'usare account amministratore esistenti eseguendo la sincronizzazione con l'istanza di Active Directory locale. Usare le workstation esistenti nel dominio di Active Directory per la gestione e la sicurezza.

## <a name="manage-connected-tenants"></a>Gestire i tenant connessi
L'organizzazione di sicurezza necessita di visibilità per valutare i rischi e determinare se vengono rispettati i criteri dell'organizzazione e gli eventuali requisiti normativi. È necessario assicurarsi che l'organizzazione di sicurezza abbia visibilità su tutte le sottoscrizioni connesse all'ambiente di produzione e alla rete (tramite [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) o la [VPN da sito a sito](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)). Un [amministratore globale/amministratore società](../../active-directory/roles/permissions-reference.md#company-administrator-permissions) in Azure AD può elevare i privilegi per l'accesso al ruolo [Amministratore Accesso utenti](../../role-based-access-control/built-in-roles.md#user-access-administrator) e visualizzare tutte le sottoscrizioni e tutti i gruppi gestiti connessi all'ambiente.

Vedere [Elevare i privilegi di accesso per gestire tutte le sottoscrizioni e i gruppi di gestione di Azure](../../role-based-access-control/elevate-access-global-admin.md) per assicurarsi che l'utente e il gruppo di sicurezza possano visualizzare tutte le sottoscrizioni o i gruppi di gestione connessi all'ambiente. È necessario rimuovere questo accesso con privilegi elevati dopo aver valutato i rischi.

## <a name="enable-single-sign-on"></a>Abilita Single Sign-On

In un mondo incentrato su dispositivi mobili e cloud, è importante poter accedere con Single Sign-On (SSO) a dispositivi, app e servizi da qualsiasi posizione in modo che gli utenti siano produttivi sempre e ovunque. La gestione di più soluzioni di identità rappresenta un problema di amministrazione non solo per il reparto IT, ma anche per gli utenti finali che devono tenere a mente più password.

Utilizzando la stessa soluzione di identità per tutte le applicazioni e risorse, è possibile ottenere l'accesso Single Sign-On. Gli utenti hanno la possibilità di usare lo stesso set di credenziali per accedere alle risorse, indipendentemente dalla dislocazione in locale o nel cloud di tali risorse.

**Procedura consigliata**: abilitare SSO.  
**Dettagli**: Azure AD [estende Active Directory locale](/azure/active-directory/connect/active-directory-aadconnect) al cloud. Gli utenti possono usare il loro account aziendale o dell'istituto di istruzione principale per i dispositivi aggiunti al dominio, per le risorse aziendali e per tutte le applicazioni Web e SaaS necessarie per svolgere le loro attività. Oltre a eliminare la necessità di ricordare diversi set di nomi utente e password, permette anche di effettuare automaticamente il provisioning o il deprovisioning dell'accesso alle applicazioni degli utenti in base all'appartenenza ai gruppi dell'organizzazione e al relativo stato come dipendenti. Permette poi di controllare l'accesso ad app della raccolta o app locali sviluppate e pubblicate tramite il [proxy di applicazione di Azure AD](/azure/active-directory/active-directory-application-proxy-get-started).

Usare SSO per consentire agli utenti di accedere alle [applicazioni SaaS](/azure/active-directory/active-directory-appssoaccess-whatis) in base all'account aziendale o dell'istituto di istruzione in Azure AD. Questo vale non solo per le app SaaS Microsoft, ma anche per altre app come [Google Apps](/azure/active-directory/active-directory-saas-google-apps-tutorial) e [Salesforce](/azure/active-directory/active-directory-saas-salesforce-tutorial). L'applicazione può essere configurata per l'uso di Azure AD come provider di [identità basata su SAML](/azure/active-directory/fundamentals-identity). Come controllo di sicurezza, Azure AD non rilascia all'utente un token per l'accesso all'applicazione a meno che l'accesso non sia stato concesso all'utente con Azure AD. L'accesso può essere concesso direttamente agli utenti o attraverso un gruppo di cui sono membri.

Le organizzazioni che non creano un'identità comune per stabilire l'accesso SSO per utenti e applicazioni sono più esposte a situazioni in cui gli utenti devono utilizzare più password. In queste situazioni aumenta la probabilità di riutilizzare le password o di utilizzare password vulnerabili.

## <a name="turn-on-conditional-access"></a>Abilitare l'accesso condizionale

Gli utenti possono accedere alle risorse dell'organizzazione con una serie di dispositivi e app ovunque si trovino. È compito dell'amministratore IT assicurarsi che i dispositivi soddisfino gli standard di sicurezza e conformità. Tuttavia, non è più sufficiente concentrarsi solo su chi può accedere a una risorsa.

Per ottenere un equilibrio tra sicurezza e produttività, nelle decisioni relative al controllo di accesso è necessario considerare anche il modo in cui si accede a una risorsa. Con l'accesso condizionale di Azure AD è possibile soddisfare questo requisito. Con l'accesso condizionale è possibile prendere decisioni di controllo automatiche per l'accesso alle app cloud in base a determinate condizioni.

**Procedura consigliata**: gestire e controllare l'accesso alle risorse aziendali.  
**Dettagli**: configurare i [criteri di accesso condizionale](../../active-directory/conditional-access/concept-conditional-access-policy-common.md) di Azure AD in base all'importanza di gruppi, posizioni e applicazioni per le app SaaS e le app connesse ad Azure AD.

**Procedura consigliata**: bloccare i protocolli di autenticazione legacy.
**Dettagli**: gli utenti malintenzionati sfruttano ogni giorno i punti deboli nei protocolli meno recenti, in particolare per gli attacchi password spraying. Configurare l'accesso condizionale in modo da [bloccare i protocolli legacy](../../active-directory/conditional-access/howto-conditional-access-policy-block-legacy.md).

## <a name="plan-for-routine-security-improvements"></a>Pianificare miglioramenti per la sicurezza di routine

La sicurezza è sempre in evoluzione ed è importante creare nel framework di gestione delle identità e del cloud un modo per mostrare regolarmente la crescita e individuare nuove soluzioni per proteggere l'ambiente.

Il punteggio di sicurezza delle identità è un set di controlli di sicurezza consigliati pubblicati da Microsoft. Fornisce un punteggio numerico per misurare in modo mirato il comportamento di sicurezza e contribuire a pianificare miglioramenti della sicurezza futuri. È anche possibile visualizzare il punteggio rispetto a quelli di altri settori e alle tendenze della propria organizzazione nel tempo.

**Procedura consigliata**: pianificare verifiche e miglioramenti della sicurezza di routine in base alle procedure consigliate del settore.
**Dettagli**: usare la funzionalità di punteggio di sicurezza delle identità per classificare i miglioramenti nel tempo.

## <a name="enable-password-management"></a>Abilitare la gestione delle password

Se sono presenti più tenant o si vuole consentire agli utenti di [reimpostare la propria password](../../active-directory/user-help/active-directory-passwords-update-your-own-password.md), è importante adottare criteri di sicurezza adatti per prevenire l'uso improprio.

**Procedura consigliata**: configurare la reimpostazione della password self-service per gli utenti.  
**Dettagli**: usare la funzionalità di [reimpostazione della password self-service](/azure/active-directory-b2c/active-directory-b2c-reference-sspr) di Azure AD.

**Procedura consigliata**: monitorare come o se la reimpostazione della password self-service viene effettivamente usata.  
**Dettagli**: monitorare gli utenti che eseguono la registrazione usando il [report Attività di registrazione reimpostazione password](/azure/active-directory/active-directory-passwords-get-insights) di Azure AD. La funzionalità di creazione di report disponibile in Azure AD consente di rispondere a domande specifiche grazie a report predefiniti. Se si dispone di una licenza appropriata, è anche possibile creare query personalizzate.

**Procedura consigliata**: estendere all'infrastruttura locale i criteri per le password basate sul cloud.
**Dettagli**: migliorare i criteri per le password nell'organizzazione eseguendo per le modifiche delle password locali gli stessi controlli eseguiti per le modifiche delle password basate sul cloud. Installare gli agenti di [protezione password di Azure AD](/azure/active-directory/authentication/concept-password-ban-bad) per Windows Server Active Directory in locale per estendere all'infrastruttura esistente gli elenchi di password escluse. Gli utenti e gli amministratori che modificano, impostano o reimpostano le password in locale devono rispettare gli stessi criteri per le password degli utenti che usano solo il cloud.

## <a name="enforce-multi-factor-verification-for-users"></a>Applicare la verifica a più fattori per gli utenti

È consigliabile richiedere la verifica in due passaggi per tutti gli utenti. Questo vale anche per gli amministratori e gli altri utenti dell'organizzazione la cui compromissione dell'account potrebbe avere un impatto significativo, ad esempio, i dirigenti del reparto finanziario.

Sono disponibili vari modi per richiedere la verifica in due passaggi. L'opzione migliore dipende dall'obiettivo che si intende raggiungere, dall'edizione di Azure AD in esecuzione e dal programma di licenza. Vedere [Come richiedere la verifica in due passaggi per un utente](/azure/active-directory/authentication/howto-mfa-userstates) per determinare l'opzione migliore per il proprio ambiente. Altre informazioni sulle licenze e i prezzi sono disponibili nelle pagine relative ai prezzi di [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) e [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

Di seguito sono indicati i vantaggi e le opzioni per l'abilitazione della verifica in due passaggi:

**Opzione 1**: abilitare l'autenticazione MFA per tutti gli utenti e i metodi di accesso con le impostazioni predefinite per la sicurezza Azure AD **Vantaggio**: questa opzione consente di applicare in modo semplice e rapido l'autenticazione MFA per tutti gli utenti dell'ambiente con criteri rigorosi per:

* Verificare gli account amministrativi e i meccanismi di accesso amministrativo
* Richiedere la verifica con autenticazione MFA tramite Microsoft Authenticator per tutti gli utenti
* Bloccare i protocolli di autenticazione legacy

Questo metodo è disponibile per tutti i livelli di licenza, ma non può essere combinato con criteri di accesso condizionale esistenti. Altre informazioni sono disponibili in [Azure ad impostazioni predefinite di sicurezza](/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)

**Opzione 2**: [abilitare l'autenticazione a più fattori modificando lo stato utente](../../active-directory/authentication/howto-mfa-userstates.md).   
**Vantaggio**: questo è il metodo tradizionale per richiedere la verifica in due passaggi. Funziona sia con [Azure Multi-Factor Authentication nel cloud e Server Multi-Factor Authentication](/azure/active-directory/authentication/concept-mfa-whichversion). Con questo metodo gli utenti devono eseguire la verifica in due passaggi ogni volta che eseguono l'accesso e viene eseguito l'override dei criteri di accesso condizionale.

Per determinare dove è necessario abilitare l'autenticazione MFA, vedere [Determinazione della versione di Azure MFA adatta alla propria organizzazione](/azure/active-directory/authentication/concept-mfa-whichversion).

**Opzione 3**: [abilitare l'autenticazione MFA con criteri di accesso condizionale](/azure/active-directory/authentication/howto-mfa-getstarted).
**Vantaggio**: questa opzione consente di richiedere la verifica in due passaggi in determinate condizioni usando l'[accesso condizionale](../../active-directory/conditional-access/concept-conditional-access-policy-common.md). L'accesso di utenti da posizioni diverse, l'uso di dispositivi non attendibili o l'uso di applicazioni considerate rischiose possono essere considerate condizioni specifiche. La definizione di condizioni specifiche in cui si richiede la verifica in due passaggi consente di evitare di chiedere continuamente conferma agli utenti della loro identità, che può risultare spiacevole.

Questo rappresenta il mezzo più flessibile per abilitare la verifica in due passaggi per gli utenti. L'abilitazione mediante criteri di accesso condizionale funziona solo per Azure Multi-Factor Authentication nel cloud ed è una funzionalità premium di Azure AD. Per altre informazioni su questo metodo, vedere [Implementare Azure Multi-Factor Authentication basato su cloud](/azure/active-directory/authentication/howto-mfa-getstarted).

**Opzione 4**: abilitare l'autenticazione MFA con criteri di accesso condizionale valutando i [criteri di accesso condizionale basati sul rischio](../../active-directory/conditional-access/howto-conditional-access-policy-risk.md).   
**Vantaggio**: questa opzione consente di:

* Rilevare le potenziali vulnerabilità per le identità dell'organizzazione.
* Configurare risposte automatizzate alle azioni sospette rilevate in relazione alle identità dell'organizzazione.
* Esaminare gli eventi imprevisti sospetti ed eseguire l'azione appropriata per risolverli.

Questo metodo usa la valutazione del rischio di Azure AD Identity Protection per determinare se la verifica in due passaggi è necessaria in funzione del rischio per l'utente e l'accesso per tutte le applicazioni cloud. Questo metodo richiede una licenza di Azure Active Directory P2. Per altre informazioni su questo metodo, vedere [Azure Active Directory Identity Protection](/azure/active-directory/identity-protection/overview).

> [!Note]
> Opzione 2, abilitazione di Multi-Factor Authentication modificando lo stato utente, esegue l'override dei criteri di accesso condizionale. Poiché le opzioni 3 e 4 usano i criteri di accesso condizionale, non è possibile usare l'opzione 2 con essi.

Le organizzazioni che non aggiungono livelli supplementari di protezione delle identità, come la verifica in due passaggi, sono più vulnerabili agli attacchi con furto di credenziali. Un attacco con furto di credenziali comporta il rischio di compromissione dei dati.

## <a name="use-role-based-access-control"></a>Usare il controllo degli accessi in base al ruolo

La gestione dell'accesso per le risorse cloud è fondamentale per tutte le organizzazioni che usano il cloud. Il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](/azure/role-based-access-control/overview)consente di gestire gli utenti che hanno accesso alle risorse di Azure, le operazioni che possono eseguire con tali risorse e le aree a cui hanno accesso.

La designazione di gruppi o singoli ruoli responsabili di funzioni specifiche in Azure consente di evitare confusioni che possono causare errori umani e di automazione che creano rischi per la sicurezza. Per le organizzazioni che intendono applicare criteri di sicurezza per l'accesso ai dati è fondamentale limitare l'accesso in base a principi di [riservatezza](https://en.wikipedia.org/wiki/Need_to_know) e [privilegi minimi](https://en.wikipedia.org/wiki/Principle_of_least_privilege).

Il team di sicurezza deve avere visibilità sulle risorse di Azure per valutare e correggere i rischi. Se il team di sicurezza ha responsabilità operative, è necessario disporre di autorizzazioni aggiuntive per svolgere il proprio lavoro.

È possibile usare [Controllo degli accessi in base al ruolo](/azure/role-based-access-control/overview) per assegnare autorizzazioni a utenti, gruppi e applicazioni in un determinato ambito. L'ambito di un'assegnazione di ruolo può essere una sottoscrizione, un gruppo di risorse o una singola risorsa.

**Procedura consigliata**: separare i compiti all'interno del team e concedere agli utenti l'accesso solo nella misura necessaria per consentire loro di svolgere il proprio lavoro. Anziché concedere a tutti autorizzazioni senza restrizioni per la sottoscrizione o le risorse di Azure, consentire solo determinate azioni in un particolare ambito.
**Dettagli**: usare i [ruoli predefiniti di Azure](/azure/role-based-access-control/built-in-roles) in Azure per assegnare privilegi agli utenti.

> [!Note]
> Le autorizzazioni specifiche creano complessità e confusione non richieste e si accumulano in una configurazione "legacy" difficile da correggere senza il rischio di danneggiare qualcosa. Evitare di definire autorizzazioni specifiche per le risorse. In alternativa, usare gruppi di gestione per autorizzazioni a livello aziendale e gruppi di risorse per autorizzazioni nell'ambito delle sottoscrizioni. Evitare di definire autorizzazioni specifiche per gli utenti. Assegnare piuttosto l'accesso a gruppi in Azure AD.

**Procedura consigliata**: concedere ai team di sicurezza con responsabilità di Azure l'accesso per visualizzare le risorse di Azure in modo da poter valutare e correggere i rischi.
**Dettagli**: concedere ai team di sicurezza il ruolo Controllo degli accessi in base al ruolo con [autorizzazioni di lettura per la sicurezza](/azure/role-based-access-control/built-in-roles#security-reader). È possibile usare il gruppo di gestione radice o il gruppo di gestione segmenti, a seconda dell'ambito di responsabilità:

* **Gruppo di gestione radice** per i team responsabili di tutte le risorse aziendali
* **Gruppo di gestione segmenti** per i team con ambito limitato (in genere a causa di normative o altri limiti dell'organizzazione)

**Procedura consigliata**: concedere le autorizzazioni appropriate ai team di sicurezza che hanno responsabilità operative dirette.
**Dettagli**: esaminare i ruoli predefiniti Controllo degli accessi in base al ruolo per l'assegnazione di ruolo appropriata. Se i ruoli predefiniti non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare [ruoli personalizzati di Azure](/azure/role-based-access-control/custom-roles). Analogamente ai ruoli predefiniti, è possibile assegnare ruoli personalizzati a utenti, gruppi ed entità servizio negli ambiti di sottoscrizione, gruppo di risorse e risorsa.

**Procedure consigliate**: concedere al Centro sicurezza di Azure l'accesso ai ruoli di sicurezza che lo richiedono. Il Centro sicurezza consente ai team di sicurezza di identificare e correggere rapidamente i rischi.
**Dettagli**: aggiungere team di sicurezza con queste esigenze al ruolo Controllo degli accessi in base al ruolo [Amministratore della sicurezza](/azure/role-based-access-control/built-in-roles#security-admin) in modo che possano visualizzare i criteri e gli stati di sicurezza, modificare i criteri di sicurezza, visualizzare gli avvisi e le raccomandazioni, nonché ignorare gli avvisi e le raccomandazioni. A tale scopo, è possibile usare il gruppo di gestione radice o il gruppo di gestione segmenti, a seconda dell'ambito di responsabilità.

Le organizzazioni che non applicano il controllo di accesso ai dati con funzionalità come Controllo degli accessi in base al ruolo potrebbero concedere più privilegi del necessario ai propri utenti. Questo può causare la compromissione dei dati perché gli utenti potrebbero avere accesso a tipi di dati, come importanti dati aziendali, a cui non dovrebbero poter accedere.

## <a name="lower-exposure-of-privileged-accounts"></a>Diminuire l'esposizione degli account con privilegi

La protezione dell'accesso con privilegi è il primo fondamentale passo per proteggere le risorse aziendali. Riducendo al minimo il numero di utenti che hanno accesso a informazioni o risorse protette riduce le probabilità che un utente malintenzionato acceda al sistema, o che un utente autorizzato comprometta inavvertitamente una risorsa sensibile.

Gli account con privilegi sono gli account che amministrano e gestiscono i sistemi IT. Gli utenti malintenzionati usano questi account per ottenere l'accesso ai dati e ai sistemi di un'organizzazione. Per proteggere l'accesso con privilegi, è necessario isolare gli account e i sistemi dal rischio di esposizione a utenti malintenzionati.

Si consiglia di sviluppare e seguire una roadmap per proteggere l'accesso con privilegi dagli utenti malintenzionati. Per informazioni sulla creazione di una roadmap dettagliata per proteggere le identità e l'accesso gestiti o segnalati in Azure AD, Microsoft Azure, Microsoft 365 e altri servizi cloud, vedere la pagina relativa alla [protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure ad](/azure/active-directory/users-groups-roles/directory-admin-roles-secure).

Di seguito sono riassunte le procedure consigliate disponibili in [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure):

**Procedura consigliata**: gestire, controllare e monitorare l'accesso ad account con privilegi.   
**Dettagli**: attivare [Azure AD Privileged Identity Management](/azure/active-directory/privileged-identity-management/active-directory-securing-privileged-access). Dopo l'attivazione di Privileged Identity Management, si riceveranno messaggi di posta elettronica di notifica in caso di modifiche ai ruoli di accesso con privilegi. Queste notifiche forniscono un avviso tempestivo in caso di aggiunta di ulteriori utenti a ruoli con privilegi elevati nella directory.

**Procedura consigliata**: assicurarsi che tutti gli account amministratore critici siano account di Azure AD gestiti.
**Dettagli**: rimuovere gli account consumer dai ruoli di amministratore critici, ad esempio account Microsoft come hotmail.com, live.com e outlook.com.

**Procedura consigliata**: assicurarsi che tutti i ruoli di amministratore critici dispongano di un account separato per le attività amministrative, in modo da evitare phishing e altri attacchi che possano compromettere i privilegi amministrativi.
**Dettagli**: creare un account amministratore separato a cui assegnare i privilegi necessari per eseguire le attività amministrative. Blocca l'uso di questi account amministrativi per strumenti di produttività giornalieri come Microsoft 365 posta elettronica o l'esplorazione Web arbitraria.

**Procedura consigliata**: identificare e classificare gli account che si trovano in ruoli con privilegi elevati.   
**Dettagli**: dopo aver attivato Azure AD Privileged Identity Management, visualizzare gli utenti con i ruoli di amministratore globale o di amministratore del ruolo con privilegi e con altri ruoli con privilegi elevati. Rimuovere tutti gli account che non sono più necessari in questi ruoli e classificare gli account rimanenti assegnati ai ruoli di amministratore:

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

**Procedura consigliata**: definire un processo di scenario critico in caso di emergenza.
**Dettagli**: seguire la procedura illustrata in [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](/azure/active-directory/users-groups-roles/directory-admin-roles-secure).

**Procedura consigliata**: richiedere che tutti gli account amministratore critici siano senza password (scelta consigliata) o richiedere l'autenticazione MFA.
**Dettagli**: Usare l'[app Microsoft Authenticator](/azure/active-directory/authentication/howto-authentication-phone-sign-in) per accedere a qualsiasi account di Azure AD senza usare una password. Analogamente a [Windows Hello for Business](/windows/security/identity-protection/hello-for-business/hello-identity-verification), Microsoft Authenticator usa l'autenticazione basata su chiavi per abilitare credenziali utente associate a un dispositivo e usa l'autenticazione biometrica o un PIN.

Richiedere Azure Multi-Factor Authentication al momento dell'accesso per tutti i singoli utenti che sono assegnati in modo permanente a uno o più ruoli di amministratore di Azure AD: Amministratore globale, Amministratore del ruolo con privilegi, Amministratore di Exchange Online e Amministratore di SharePoint Online. Abilitare [Multi-Factor Authentication per gli account amministratore](/azure/active-directory/authentication/howto-mfa-userstates) e assicurarsi che tali utenti account amministratore abbiano tutti eseguito la registrazione.

**Procedura consigliata**: per gli account amministratore critici, predisporre una workstation di amministrazione in cui non siano consentite attività di produzione, ad esempio esplorazione e posta elettronica. Questo consentirà di proteggere gli account amministratore da vettori di attacco che usano l'esplorazione e la posta elettronica e di ridurre in modo significativo il rischio di un evento imprevisto grave.
**Dettagli**: usare una workstation di amministrazione. Scegliere un livello di sicurezza della workstation:

- I dispositivi di produzione con sicurezza elevata garantiscono sicurezza avanzata per l'esplorazione e altre attività di produzione.
- Per le attività sensibili, le [workstation dotate di accesso con privilegi (PAW, Privileged Access Workstation)](/windows-server/identity/securing-privileged-access/privileged-access-workstations) forniscono un sistema operativo dedicato, protetto dagli attacchi provenienti da Internet e dai vettori di minacce.

**Procedura consigliata**: effettuare il deprovisioning degli account amministratore quando i dipendenti lasciano l'organizzazione.
**Dettagli**: predisporre un processo che disabilita o elimina gli account amministratore quando i dipendenti lasciano l'organizzazione.

**Procedura consigliata**: eseguire regolarmente test degli account amministratore usando le tecniche di attacco correnti.
**Dettagli**: usare Microsoft 365 simulatore di attacco o un'offerta di terze parti per eseguire scenari di attacco realistici nell'organizzazione. In questo modo è possibile trovare utenti vulnerabili prima che si verifichi un attacco reale.

**Procedura consigliata**: intervenire per mitigare l'effetto delle tecniche di attacco usate più di frequente.  
**Dettagli**: [identificare gli account Microsoft in ruoli amministrativi per cui è necessario passare ad account aziendali o dell'istituto di istruzione](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts).  

[Separare gli account utente e l'inoltro della posta elettronica per gli account amministratore globale](/azure/active-directory/users-groups-roles/directory-admin-roles-secure)  

[Verificare che le password degli account amministrativi siano state modificate di recente](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#ensure-the-passwords-of-administrative-accounts-have-recently-changed)  

[Attivare la sincronizzazione dell'hash delle password](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#turn-on-password-hash-synchronization)  

[Richiedere l'autenticazione a più fattori per gli utenti con tutti i ruoli con privilegi e gli utenti esposti](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#require-multi-factor-authentication-mfa-for-users-in-all-privileged-roles-as-well-as-exposed-users)  

[Ottenere il Punteggio sicuro Microsoft 365 (se si usa Microsoft 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#obtain-your-office-365-secure-score-if-using-office-365)  

[Esaminare le linee guida per la sicurezza Microsoft 365 (se si usa Microsoft 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#review-the-office-365-security-and-compliance-guidance-if-using-office-365)  

[Configurare il monitoraggio dell'attività Microsoft 365 (se si usa Microsoft 365)](/azure/active-directory/users-groups-roles/directory-admin-roles-secure#configure-office-365-activity-monitoring-if-using-office-365)  

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
- Accessi da [dispositivi infetti](/azure/active-directory/active-directory-reporting-sign-ins-from-possibly-infected-devices).
- Indirizzi IP sospetti.

**Dettagli**: usare i [report anomalie](/azure/active-directory/active-directory-view-access-usage-reports) di Azure AD Premium. Predisporre processi e procedure in modo che gli amministratori IT possano eseguire tali report ogni giorno o su richiesta, in genere in uno scenario di risposta a eventi imprevisti.

**Procedura consigliata**: disporre di un sistema di monitoraggio attivo che avvisi dei rischi e il cui livello di rischio (alto, medio o basso) possa essere adattato ai requisiti aziendali.   
**Dettagli**: usare [Azure AD Identity Protection](/azure/active-directory/active-directory-identityprotection), che segnala i rischi correnti nel relativo dashboard e invia notifiche riassuntive giornaliere tramite posta elettronica. Per contribuire a proteggere le identità dell'organizzazione, è possibile configurare criteri basati sul rischio che rispondano automaticamente ai problemi rilevati quando viene raggiunto un livello di rischio specificato.

Le organizzazioni che non monitorano attivamente i sistemi di identità sono esposti al rischio di compromissione delle credenziali utente. Se l'organizzazione non è consapevole dello svolgimento di attività sospette con tali credenziali, non potrà attenuare questo tipo di minaccia.

## <a name="use-azure-ad-for-storage-authentication"></a>Usare Azure AD per l'autenticazione dell'archiviazione
[Archiviazione di Azure](/azure/storage/common/storage-auth-aad) supporta l'autenticazione e l'autorizzazione con Azure AD per archiviazione BLOB e archiviazione code. Con l'autenticazione di Azure AD è possibile usare il controllo degli accessi in base al ruolo di Azure per concedere autorizzazioni specifiche a utenti, gruppi e applicazioni fino all'ambito di un singolo contenitore BLOB o di una coda.

È consigliabile usare [Azure AD per autenticare l'accesso alle risorse di archiviazione](https://azure.microsoft.com/blog/azure-storage-support-for-azure-ad-based-access-control-now-generally-available/).

## <a name="next-step"></a>Passaggio successivo

Per altre procedure consigliate per la sicurezza da usare nella progettazione, la distribuzione e la gestione di soluzioni cloud tramite Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](best-practices-and-patterns.md).
