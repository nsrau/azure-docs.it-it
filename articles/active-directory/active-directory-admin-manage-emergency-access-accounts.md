---
title: Gestire gli account amministrativi di accesso di emergenza in Azure AD | Microsoft Docs
description: Questo articolo descrive come usare gli account di accesso di emergenza per consentire alle organizzazioni di limitare l'accesso con privilegi in un ambiente di Azure Active Directory esistente.
services: active-directory
keywords: Non aggiungere o modificare parole chiave senza consultare l'esperto SEO.
author: markwahl-msft
ms.author: billmath
ms.date: 12/13/2017
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.openlocfilehash: 1545fb9a89794a74efbb855c4480040973c3308e
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/12/2018
---
# <a name="manage-emergency-access-administrative-accounts-in-azure-ad"></a>Gestire gli account amministrativi di accesso di emergenza in Azure AD 

Per la maggior parte delle attività quotidiane, non sono necessari i diritti di *amministratore globale* per gli utenti. Questo ruolo non deve essere assegnato agli utenti in modo permanente, poiché potrebbero inavvertitamente eseguire attività che richiedono autorizzazioni più elevate rispetto a quelle di cui devono disporre. Quando gli utenti non devono operare come amministratori globali, devono attivare il ruolo assegnato usando Azure Active Directory (Azure AD) Privileged Identity Management (PIM) per il proprio account o per un account di amministratore alternativo.

Oltre all'aggiunta dei diritti di accesso amministrativi per se stessi, è necessario impedire che l'utente venga inavvertitamente bloccato per l'amministrazione del tenant di Azure AD perché non è possibile accedere né attivare l'account di un singolo utente esistente in qualità di amministratore. È possibile ridurre l'impatto di una accidentale mancanza di accesso amministrativo archiviando due o più *account di accesso di emergenza* nel tenant.

Gli account di accesso di emergenza consentono alle organizzazioni di limitare l'accesso con privilegi in un ambiente di Azure Active Directory esistente. Si tratta di account con privilegi elevati non assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati agli scenari di emergenza, situazioni in cui non è possibile usare i normali account amministrativi. Le organizzazioni devono mantenere l'impegno di limitare l'uso dell'account emergenza solo per il tempo strettamente necessario.

Per un'organizzazione può essere necessario usare un account di accesso di emergenza nelle situazioni seguenti:

 - Gli account utente sono federati e la federazione non è attualmente disponibile a causa di un'interruzione del funzionamento della rete cellulare o di un problema tecnico del provider di identità. Se ad esempio l'host del provider di identità dell'ambiente è stato disattivato, gli utenti potrebbero non essere in grado di effettuare l'accesso quando Azure AD li reindirizza al provider di identità. 
 - Gli amministratori vengono registrati tramite Azure Multi-Factor Authentication e tutti i loro dispositivi non sono disponibili. Gli utenti potrebbero non essere in grado di completare l'autenticazione a più fattori per attivare un ruolo. Ad esempio, un'interruzione del funzionamento della rete cellulare può impedire di rispondere alle chiamate telefoniche o di ricevere SMS, gli unici due meccanismi di autenticazione registrati per il dispositivo. 
 - L'utente con l'accesso amministrativo globale più recente ha lasciato l'organizzazione. Azure Active Directory impedisce l'eliminazione dell'ultimo account di *amministratore globale*, ma non impedisce l'eliminazione o la disabilitazione dell'account a livello locale. Entrambi i casi potrebbero rendere impossibile per l'organizzazione ripristinare l'account.

## <a name="initial-configuration"></a>Configurazione iniziale

Creare due o più account di accesso di emergenza. Devono essere account solo cloud che usano il dominio \*.onmicrosoft.com e che non sono federati o sincronizzati da un ambiente locale. 

Gli account non devono essere associati a un utente specifico dell'organizzazione. Le organizzazioni devono garantire che le credenziali per questi account siano sempre conservate in modo sicuro e siano note solo a coloro che sono autorizzati a usarle. 

> [!NOTE]
> Una password per un account di accesso di emergenza in genere è suddivisa in due o tre parti, scritte su pezzi di carta separati e archiviate in casseforti a prova di fuoco in luoghi sicuri e separati. 
>
> Assicurarsi che gli account di accesso di emergenza non siano connessi a cellulari forniti ai dipendenti, token hardware che viaggiano con i singoli dipendenti o altre credenziali specifiche del dipendente. Questa precauzione consente di affrontare le situazioni in cui un singolo dipendente è irraggiungibile quando sono necessarie le credenziali. 

### <a name="initial-configuration-with-permanent-assignments"></a>Configurazione iniziale con assegnazioni permanenti

Una soluzione è impostare gli utenti come membri permanenti del ruolo *amministratore globale*. Questa opzione potrebbe essere appropriata per le organizzazioni che non dispongono di sottoscrizioni di Azure AD Premium P2.

Per ridurre il rischio di attacchi derivanti da una password compromessa, in Azure AD è consigliabile richiedere l'autenticazione a più fattori per tutti gli utenti singoli. Questo gruppo deve includere gli amministratori e tutti gli altri utenti (ad esempio dirigenti del reparto finanziario) per cui un account compromesso avrebbe un impatto significativo. 

Tuttavia, se l'organizzazione non dispone di dispositivi condivisi, l'autenticazione a più fattori potrebbe non risultare possibile per questi account di accesso di emergenza. Se si configurano criteri di accesso condizionale per richiedere la [registrazione con autenticazione a più fattori per ogni amministratore](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) per Azure AD e altre app connesse in modalità SaaS, potrebbe essere necessario configurare le esclusioni dei criteri per escludere gli account di accesso di emergenza da questo requisito.

### <a name="initial-configuration-with-approvals"></a>Configurazione iniziale con approvazioni

Un'altra opzione consiste nel configurare gli utenti come idonei e responsabili dell'approvazione per attivare il ruolo di *amministratore globale*. Questa soluzione richiede che l'organizzazione disponga di sottoscrizioni di Azure AD Premium P2. Richiede inoltre un'opzione di applicazione dell'autenticazione a più fattori adatta per la condivisione tra più utenti e l'ambiente di rete. Questi requisiti si applicano perché l'attivazione del ruolo di *amministratore globale* richiede che gli utenti abbiano precedentemente eseguito l'autenticazione a più fattori. Per altre informazioni, vedere [Come richiedere l'autenticazione a più fattori in Azure AD Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa).

Non è consigliabile usare l'autenticazione a più fattori associata ai dispositivi personali per gli account di accesso di emergenza. In caso di emergenza effettiva, la persona che necessita dell'accesso a un dispositivo registrato con l'autenticazione a più fattori potrebbe non essere quella che ha il dispositivo personale. 

Considerare anche le possibili minacce. Ad esempio, potrebbe verificarsi una circostanza imprevista come una calamità naturale durante la quale la rete cellulare o altre reti potrebbero non essere disponibili. È importante verificare che tutti i dispositivi registrati vengano conservati in una posizione nota e sicura con più mezzi di comunicazione con Azure AD.

## <a name="ongoing-monitoring"></a>Monitoraggio continuo

Monitorare i [log di accesso e di controllo di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins) per controllare eventuali accessi e attività degli account di accesso di emergenza. In genere tali account non dovrebbero accedere e apportare modifiche, pertanto l'uso di questi account è probabilmente anomalo e richiede un'analisi di sicurezza.

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>La convalida della verifica degli account deve avvenire a intervalli regolari

Per convalidare l'account, eseguire almeno la procedura seguente:
- Ogni 90 giorni.
- In caso di modifica recente del personale IT, ad esempio per un cambio di mansione, una partenza o una nuova assunzione.
- Quando le sottoscrizioni di Azure AD dell'organizzazione sono cambiate.

Per formare i membri del personale per l'uso degli account di accesso di emergenza, eseguire le operazioni seguenti:

* Assicurarsi che il personale che monitora la sicurezza sia informato che l'attività di verifica degli account è in corso.
* Verificare che gli account utente cloud possano accedere ai ruoli e attivarli e che gli utenti che potrebbero dover eseguire questi passaggi durante un'emergenza vengano formati sul processo.
* Verificare che essi non abbiano l'autenticazione a più fattori o la reimpostazione della password self-service (SSPR) per qualsiasi dispositivo utente o per le informazioni personali. 
* Se gli account sono registrati per l'autenticazione a più fattori per un dispositivo da usare durante l'attivazione del ruolo, assicurarsi che il dispositivo sia accessibile a tutti gli amministratori che potrebbero aver bisogno di usarlo durante un'emergenza. Verificare inoltre che il dispositivo sia registrato con almeno due meccanismi che non condividono una modalità di errore comune. Ad esempio, il dispositivo può comunicare con Internet tramite la rete wireless di una struttura e una rete cellulare.
* Aggiornare le credenziali dell'account.

## <a name="next-steps"></a>Passaggi successivi
- [Aggiungere un utente basato sul cloud](add-users-azure-active-directory.md) e [assegnare il nuovo utente al ruolo di amministratore globale](active-directory-users-assign-role-azure-portal.md).
- [Iscriversi a Azure Active Directory Premium](active-directory-get-started-premium.md), se non è ancora stato fatto.
- [Richiedere Azure Multi-Factor Authentication per i singoli utenti assegnati come amministratori](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states).
- [Configurare protezioni aggiuntive per gli amministratori globali in Office 365](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560), se si utilizza Office 365.
- [Eseguire una verifica di accesso degli amministratori globali](active-directory-privileged-identity-management-how-to-start-security-review.md) e [spostare gli amministratori globali esistenti in ruoli di amministratore più specifici](active-directory-assign-admin-roles-azure-portal.md).


