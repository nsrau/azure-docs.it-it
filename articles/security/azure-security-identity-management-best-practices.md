---
title: Procedure consigliate per la sicurezza con il controllo di accesso e identità di Azure | Documentazione Microsoft
description: Questo articolo illustra una serie di procedure consigliate per il controllo di accesso e la gestione delle identità usando le funzionalità integrate di Azure.
services: security
documentationcenter: na
author: barclayn
manager: mbaldwin
editor: TomSh
ms.assetid: 07d8e8a8-47e8-447c-9c06-3a88d2713bc1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/17/2018
ms.author: barclayn
ms.openlocfilehash: 64d940552f2790c08e8087f279990d0a6c595bac
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245730"
---
# <a name="azure-identity-management-and-access-control-security-best-practices"></a>Procedure consigliate per la sicurezza con il controllo di accesso e la gestione delle identità di Azure

Molti considerano l'identità come il nuovo livello limite per la sicurezza, sostituendo in questo ruolo la prospettiva tradizionale incentrata sulla rete. Questa evoluzione nell'attenzione per la sicurezza e negli investimenti è dovuta al fatto che i perimetri di rete sono diventati sempre più permeabili e la difesa perimetrale non può più essere efficace quanto prima dell'esplosione delle applicazioni cloud e dei dispositivi [BYOD](https://aka.ms/byodcg).

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
* Abilita Single Sign-On
* Attivare l'accesso condizionale
* Abilitare la gestione delle password
* Applicare la verifica a più fattori per gli utenti
* Usare il controllo degli accessi in base al ruolo
* Diminuire l'esposizione degli account con privilegi
* Controllare le posizioni in cui si trovano le risorse

## <a name="treat-identity-as-the-primary-security-perimeter"></a>Trattare l'identità come perimetro di sicurezza primario

Molti considerano l'identità come perimetro primario per la sicurezza. Questa strategia rappresenta un cambiamento rispetto al tradizionale ruolo centrale attribuito alla sicurezza della rete. I perimetri di rete diventano sempre più permeabili pertanto la difesa perimetrale non può essere efficace quanto lo era prima dell'esplosione dei dispositivi [BYOD](https://aka.ms/byodcg) e delle applicazioni cloud.
[Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) è la soluzione di Microsoft Azure per la gestione delle identità e degli accessi. Azure AD è il servizio Microsoft multi-tenant di gestione delle identità e delle directory basato sul cloud. Combina in un'unica soluzione servizi di directory importanti, gestione degli accessi alle applicazioni e protezione delle identità.

Le sezioni seguenti elencano le procedure consigliate per garantire la sicurezza delle identità e degli accessi tramite Azure AD.

## <a name="centralize-identity-management"></a>Centralizzare la gestione delle identità

In uno scenario di [soluzione ibrida di gestione delle identità](https://resources.office.com/ww-landing-M365E-EMS-IDAM-Hybrid-Identity-WhitePaper.html?) è consigliabile integrare le directory cloud e locali. L'integrazione consente al team IT di gestire gli account da un'unica posizione, indipendentemente da dove un account è stato creato. L'integrazione rende inoltre gli utenti più produttivi, in quanto fornisce un'identità comune per accedere alle risorse cloud e locali.


**Procedura consigliata**: integrare le directory locali con Azure AD.  
**Dettagli**: sincronizzare la directory locale con la directory cloud usando [Azure AD Connect](../active-directory/connect/active-directory-aadconnect.md).

**Procedura consigliata**: attivare la sincronizzazione dell'hash delle password.  
**Dettagli**: la sincronizzazione dell'hash delle password è una funzionalità usata per sincronizzare gli hash delle password utente da un'istanza di Active Directory locale a un'istanza di Azure AD basata sul cloud.

Anche se si decide di usare la federazione con Active Directory Federation Services (AD FS) o altri provider di identità, è possibile configurare la sincronizzazione dell'hash delle password come backup in caso di errore o temporanea indisponibilità dei server locali. In questo modo, gli utenti potranno accedere al servizio usando la stessa password usata per accedere all'istanza di Active Directory locale. Identity Protection può inoltre rilevare le credenziali compromesse confrontando gli hash delle password con password segnalate come compromesse, se un utente ha usato lo stesso indirizzo di posta elettronica e la stessa password in altri servizi non connessi ad Azure AD.

Per altre informazioni, vedere [Implementare la sincronizzazione dell'hash delle password con il servizio di sincronizzazione Azure AD Connect](../active-directory/connect/active-directory-aadconnectsync-implement-password-hash-synchronization.md).

Le organizzazioni che non integrano l'identità locale con la propria identità cloud possono subire un sovraccarico maggiore nella gestione degli account. Questo sovraccarico aumenta le probabilità di incorrere in errori e violazioni della sicurezza.

## <a name="enable-single-sign-on"></a>Abilita Single Sign-On

In un mondo incentrato su dispositivi mobili e cloud, è importante poter accedere con Single Sign-On (SSO) a dispositivi, app e servizi da qualsiasi posizione in modo che gli utenti siano produttivi sempre e ovunque. La gestione di più soluzioni di identità rappresenta un problema di amministrazione non solo per il reparto IT, ma anche per gli utenti finali che devono tenere a mente più password.

Utilizzando la stessa soluzione di identità per tutte le applicazioni e risorse, è possibile ottenere l'accesso Single Sign-On. Gli utenti hanno la possibilità di usare lo stesso set di credenziali per accedere alle risorse, indipendentemente dalla dislocazione in locale o nel cloud di tali risorse.

**Procedura consigliata**: abilitare SSO.  
**Dettagli**: Azure AD [estende Active Directory locale](../active-directory/connect/active-directory-aadconnect.md) al cloud. Gli utenti possono usare il loro account aziendale o dell'istituto di istruzione principale per i dispositivi aggiunti al dominio, per le risorse aziendali e per tutte le applicazioni Web e SaaS necessarie per svolgere le loro attività. Oltre a eliminare la necessità di ricordare diversi set di nomi utente e password, permette anche di effettuare automaticamente il provisioning o il deprovisioning dell'accesso alle applicazioni degli utenti in base all'appartenenza ai gruppi dell'organizzazione e al relativo stato come dipendenti. Permette poi di controllare l'accesso ad app della raccolta o app locali sviluppate e pubblicate tramite il [proxy di applicazione di Azure AD](../active-directory/active-directory-application-proxy-get-started.md).

Usare SSO per consentire agli utenti di accedere alle [applicazioni SaaS](../active-directory/active-directory-appssoaccess-whatis.md) in base all'account aziendale o dell'istituto di istruzione in Azure AD. Questo vale non solo per le app SaaS Microsoft, ma anche per altre app come [Google Apps](../active-directory/active-directory-saas-google-apps-tutorial.md) e [Salesforce](../active-directory/active-directory-saas-salesforce-tutorial.md). L'applicazione può essere configurata per l'uso di Azure AD come provider di [identità basata su SAML](../active-directory/fundamentals-identity.md). Come controllo di sicurezza, Azure AD non rilascia all'utente un token per l'accesso all'applicazione a meno che l'accesso non sia stato concesso all'utente con Azure AD. L'accesso può essere concesso direttamente agli utenti o attraverso un gruppo di cui sono membri.

Le organizzazioni che non creano un'identità comune per stabilire l'accesso SSO per utenti e applicazioni sono più esposte a situazioni in cui gli utenti devono utilizzare più password. In queste situazioni aumenta la probabilità di riutilizzare le password o di utilizzare password vulnerabili.

## <a name="turn-on-conditional-access"></a>Attivare l'accesso condizionale

Gli utenti possono accedere alle risorse dell'organizzazione con una serie di dispositivi e app ovunque si trovino. È compito dell'amministratore IT assicurarsi che i dispositivi soddisfino gli standard di sicurezza e conformità. Tuttavia, non è più sufficiente concentrarsi solo su chi può accedere a una risorsa.

Per ottenere un equilibrio tra produttività e sicurezza, nelle decisioni relative al controllo di accesso è necessario considerare anche il modo in cui si accede a una risorsa. Con l'accesso condizionale di Azure AD è possibile soddisfare questo requisito. Con l'accesso condizionale è possibile prendere decisioni di controllo automatiche per l'accesso alle app cloud in base a determinate condizioni.

**Procedura consigliata**: gestire e controllare l'accesso alle risorse aziendali.  
**Dettagli**: configurare l'[accesso condizionale](../active-directory/active-directory-conditional-access-azure-portal.md) di Azure AD basato sull'importanza di gruppi, posizioni e applicazioni per le app SaaS e le app connesse di Azure AD.

## <a name="enable-password-management"></a>Abilitare la gestione delle password

Se sono presenti più tenant o si vuole consentire agli utenti di [reimpostare la propria password](../active-directory/active-directory-passwords-update-your-own-password.md), è importante adottare criteri di sicurezza adatti per prevenire l'uso improprio.

**Procedura consigliata**: configurare Reimpostazione password self-service per gli utenti.  
**Dettagli**: usare la funzionalità [Reimpostazione password self-service](../active-directory-b2c/active-directory-b2c-reference-sspr.md) di Azure AD.

**Procedura consigliata**: monitorare se e come Reimpostazione password self-service è effettivamente usata.  
**Dettagli**: monitorare gli utenti che eseguono la registrazione usando il report [Attività di registrazione reimpostazione password](../active-directory/active-directory-passwords-get-insights.md) di Azure AD. La funzionalità di creazione di report disponibile in Azure AD consente di rispondere a domande specifiche grazie a report predefiniti. Se si dispone di una licenza appropriata, è anche possibile creare query personalizzate.

## <a name="enforce-multi-factor-verification-for-users"></a>Applicare la verifica a più fattori per gli utenti

È consigliabile richiedere la verifica in due passaggi per tutti gli utenti. Questo vale anche per gli amministratori e gli altri utenti dell'organizzazione la cui compromissione dell'account potrebbe avere un impatto significativo, ad esempio, i dirigenti del reparto finanziario.

Sono disponibili vari modi per richiedere la verifica in due passaggi. L'opzione migliore dipende dall'obiettivo che si intende raggiungere, dall'edizione di Azure AD in esecuzione e dal programma di licenza. Vedere [Come richiedere la verifica in due passaggi per un utente](../active-directory/authentication/howto-mfa-userstates.md) per determinare l'opzione migliore per il proprio ambiente. Altre informazioni sulle licenze e i prezzi sono disponibili nelle pagine relative ai prezzi di [Azure AD](https://azure.microsoft.com/pricing/details/active-directory/) e [Multi-Factor Authentication](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

Di seguito sono indicati i vantaggi e le opzioni per l'abilitazione della verifica in due passaggi:

**Opzione 1**: [Abilitare Azure MFA modificando lo stato utente](../active-directory/authentication/howto-mfa-userstates.md).   
**Vantaggio** questo è il metodo tradizionale per richiedere la verifica in due passaggi. Funziona sia con [Azure Multi-Factor Authentication nel cloud e Server Multi-Factor Authentication](../active-directory/authentication/concept-mfa-whichversion.md). Con questo metodo gli utenti devono eseguire la verifica in due passaggi ogni volta che eseguono l'accesso e vengono ignorati i criteri di accesso condizionale.

**Opzione 2**: [abilitare Multi-Factor Authentication con i criteri di accesso condizionale](../active-directory/authentication/howto-mfa-getstarted.md#enable-multi-factor-authentication-with-conditional-access).   
**Vantaggio**: questa opzione consente di richiedere la verifica in due passaggi in determinate condizioni usando l'[accesso condizionale](../active-directory/active-directory-conditional-access-azure-portal.md). L'accesso di utenti da posizioni diverse, l'uso di dispositivi non attendibili o l'uso di applicazioni considerate rischiose possono essere considerate condizioni specifiche. La definizione di condizioni specifiche in cui si richiede la verifica in due passaggi consente di evitare di chiedere continuamente conferma agli utenti della loro identità, che può risultare spiacevole.

Questo rappresenta il mezzo più flessibile per abilitare la verifica in due passaggi per gli utenti. L'abilitazione mediante criteri di accesso condizionale funziona solo per Azure Multi-Factor Authentication nel cloud ed è una funzione Premium di Azure AD. Per altre informazioni su questo metodo, vedere [Implementare Azure Multi-Factor Authentication basato su cloud](../active-directory/authentication/howto-mfa-getstarted.md).

**Opzione 3**: abilitare Multi-Factor Authentication con criteri di accesso condizionale valutando il rischio per l'utente e l'accesso di [Azure AD Identity Protection](../active-directory/authentication/tutorial-risk-based-sspr-mfa.md).   
**Vantaggio**: questa opzione consente di:

- Rilevare le potenziali vulnerabilità per le identità dell'organizzazione.
- Configurare risposte automatizzate alle azioni sospette rilevate in relazione alle identità dell'organizzazione.
- Esaminare gli eventi imprevisti sospetti ed eseguire l'azione appropriata per risolverli.

Questo metodo usa la valutazione del rischio di Azure AD Identity Protection per determinare se la verifica in due passaggi è necessaria in funzione del rischio per l'utente e l'accesso per tutte le applicazioni cloud. Questo metodo richiede una licenza di Azure Active Directory P2. Per altre informazioni su questo metodo, vedere [Azure Active Directory Identity Protection](../active-directory/identity-protection/overview.md).

> [!Note]
> L'opzione 1, l'abilitazione di Multi-Factor Authentication modificando lo stato utente, sostituisce i criteri di accesso condizionale. Dal momento che le opzioni 2 e 3 usano criteri di accesso condizionale, l'opzione 1 è incompatibile con le altre due.

Le organizzazioni che non aggiungono livelli supplementari di protezione delle identità, come la verifica in due passaggi, sono più vulnerabili agli attacchi con furto di credenziali. Un attacco con furto di credenziali comporta il rischio di compromissione dei dati.

## <a name="use-role-based-access-control-rbac"></a>Usare il controllo degli accessi in base al ruolo

Per le organizzazioni che intendono applicare criteri di sicurezza per l'accesso ai dati è fondamentale limitare l'accesso in base a principi di [riservatezza](https://en.wikipedia.org/wiki/Need_to_know) e [privilegi minimi](https://en.wikipedia.org/wiki/Principle_of_least_privilege). Usare il [controllo degli accessi in base al ruolo](../role-based-access-control/overview.md) per assegnare autorizzazioni a utenti, gruppi e applicazioni in un determinato ambito. L'ambito di un'assegnazione di ruolo può essere una sottoscrizione, un gruppo di risorse o una singola risorsa.

Si possono sfruttare i ruoli [predefiniti del controllo degli accessi in base al ruolo](../role-based-access-control/built-in-roles.md) in Azure per assegnare privilegi agli utenti. Le organizzazioni che non applicano il controllo di accesso ai dati con funzionalità come Controllo degli accessi in base al ruolo potrebbero concedere più privilegi del necessario ai propri utenti. Questo può causare la compromissione dei dati perché gli utenti potrebbero avere accesso a tipi di dati, come importanti dati aziendali, a cui non dovrebbero accedere.

## <a name="lower-exposure-of-privileged-accounts"></a>Diminuire l'esposizione degli account con privilegi

La protezione dell'accesso con privilegi è il primo fondamentale passo per proteggere le risorse aziendali. Riducendo al minimo il numero di utenti che hanno accesso a informazioni o risorse protette riduce le probabilità che un utente malintenzionato acceda al sistema, o che un utente autorizzato comprometta inavvertitamente una risorsa sensibile.

Gli account con privilegi sono gli account che amministrano e gestiscono i sistemi IT. Gli utenti malintenzionati usano questi account per ottenere l'accesso ai dati e ai sistemi di un'organizzazione. Per proteggere l'accesso con privilegi, è necessario isolare gli account e i sistemi dal rischio di esposizione a utenti malintenzionati.

Si consiglia di sviluppare e seguire una roadmap per proteggere l'accesso con privilegi dagli utenti malintenzionati. Per informazioni sulla creazione di una roadmap dettagliata per proteggere le identità e gli accessi che sono gestiti o segnalati in Azure AD, Microsoft Azure, Office 365 e altri servizi cloud, vedere [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md).

Di seguito sono riassunte le procedure consigliate disponibili in [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](../active-directory/users-groups-roles/directory-admin-roles-secure.md):

**Procedura consigliata**: gestire, controllare e monitorare l'accesso ad account con privilegi.   
**Dettagli**: attivare [Azure AD Privileged Identity Management](../active-directory/privileged-identity-management/active-directory-securing-privileged-access.md). Dopo l'attivazione di Privileged Identity Management, si riceveranno messaggi di posta elettronica di notifica in caso di modifiche ai ruoli di accesso con privilegi. Queste notifiche forniscono un avviso tempestivo in caso di aggiunta di ulteriori utenti a ruoli con privilegi elevati nella directory.

**Procedura consigliata**: identificare e classificare gli account associati a ruoli con privilegi elevati.   
**Dettagli**: dopo avere attivato Azure AD Privileged Identity Management, visualizzare gli utenti con i ruoli di amministratore globale, amministratore del ruolo con privilegi e ruolo con privilegi elevati. Rimuovere tutti gli account che non sono più necessari in questi ruoli e classificare gli account rimanenti assegnati ai ruoli di amministratore:

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

Valutare gli account assegnati o idonei per il ruolo di amministratore globale. Se non sono presenti account solo cloud con il dominio `*.onmicrosoft.com` (destinati all'accesso di emergenza), crearli. Per altre informazioni, vedere Gestire gli account amministrativi di accesso di emergenza in Azure AD.

**Procedura consigliata**: attivare Multi-Factor Authentication e registrare tutti gli altri account amministratore con privilegi elevati non federati per utente singolo.  
**Dettagli**: richiedere l'autenticazione a più fattori di Azure all'accesso per tutti i singoli utenti a cui sono assegnati in modo permanente uno o più ruoli di amministratore di Azure AD: amministratore globale, amministratore del ruolo con privilegi, amministratore di Exchange Online e amministratore di SharePoint Online. Usare la guida per abilitare [Multi-Factor Authentication (MFA) per gli account amministratore](../active-directory/authentication/howto-mfa-userstates.md) e assicurarsi che tali utenti abbiano tutti eseguito la [registrazione](https://aka.ms/mfasetup).

**Procedura consigliata**: intervenire per mitigare l'effetto delle tecniche di attacco usate più di frequente.  
**Dettagli**: [identificare gli account Microsoft in ruoli amministrativi che devono essere spostati in account aziendali o dell'istituto di istruzione](../active-directory/users-groups-roles/directory-admin-roles-secure.md#identify-microsoft-accounts-in-administrative-roles-that-need-to-be-switched-to-work-or-school-accounts)  

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

**Procedura consigliate**: disporre di un sistema di monitoraggio attivo che avvisi dei rischi e il cui livello di rischio (alto, medio o basso) possa essere adattato ai requisiti aziendali.   
**Dettagli**: usare [Azure AD Identity Protection](../active-directory/active-directory-identityprotection.md), che segnala i rischi correnti nel relativo dashboard e invia notifiche riassuntive giornaliere tramite posta elettronica. Per contribuire a proteggere le identità dell'organizzazione, è possibile configurare criteri basati sul rischio che rispondano automaticamente ai problemi rilevati quando viene raggiunto un livello di rischio specificato.

Le organizzazioni che non monitorano attivamente i sistemi di identità sono esposti al rischio di compromissione delle credenziali utente. Se l'organizzazione non è consapevole dello svolgimento di attività sospette con tali credenziali, non potrà attenuare questo tipo di minaccia.

## <a name="next-step"></a>Passaggio successivo

Per altre procedure consigliate per la sicurezza da usare nella progettazione, la distribuzione e la gestione di soluzioni cloud tramite Azure, vedere [Procedure consigliate e modelli per la sicurezza di Azure](security-best-practices-and-patterns.md).
