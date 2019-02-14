---
title: Gestire gli account di accesso di emergenza in Azure AD | Microsoft Docs
description: Questo articolo descrive come usare gli account di accesso di emergenza per evitare di rimanere inavvertitamente bloccati fuori dal proprio tenant di Azure Active Directory (Azure AD).
services: active-directory
author: markwahl-msft
ms.author: billmath
ms.date: 12/21/2018
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60d2a47360808c7c1c36d5e82b98582d9f6144fa
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56176952"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>Gestire gli account di accesso di emergenza in Azure AD

È importante evitare di restare accidentalmente bloccati fuori dal proprio tenant di Azure Active Directory (Azure AD) perché non è possibile accedere o attivare l'account di un singolo utente esistente come amministratore. È possibile ridurre l'impatto di una accidentale mancanza di accesso amministrativo creando due o più *account di accesso di emergenza* nel tenant.

Gli account di accesso di emergenza hanno privilegi elevati e non sono assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati a scenari di emergenza critici, in cui non è possibile usare i normali account amministrativi. Le organizzazioni devono mantenere l'impegno di limitare l'uso dell'account di emergenza solo ai casi strettamente necessari.

Questo articolo fornisce indicazioni per la gestione degli account di accesso di emergenza in Azure AD.

## <a name="when-would-you-use-an-emergency-access-account"></a>Quando si usa un account di accesso di emergenza?

Per un'organizzazione può essere necessario usare un account di accesso di emergenza nelle situazioni seguenti:

- Gli account utente sono federati e la federazione non è attualmente disponibile a causa di un'interruzione del funzionamento della rete cellulare o di un problema tecnico del provider di identità. Se ad esempio l'host del provider di identità dell'ambiente è stato disattivato, gli utenti potrebbero non essere in grado di effettuare l'accesso quando Azure AD li reindirizza al provider di identità.
- Gli amministratori vengono registrati tramite Azure Multi-Factor Authentication e tutti i loro dispositivi non sono disponibili oppure il servizio non è disponibile. Gli utenti potrebbero non essere in grado di completare l'autenticazione a più fattori per attivare un ruolo. Ad esempio, un'interruzione del funzionamento della rete cellulare può impedire di rispondere alle chiamate telefoniche o di ricevere SMS, gli unici due meccanismi di autenticazione registrati per il dispositivo.
- L'utente con l'accesso di amministratore globale più recente ha lasciato l'organizzazione. Azure Active Directory impedisce l'eliminazione dell'ultimo account di amministratore globale, ma non impedisce l'eliminazione o la disabilitazione dell'account a livello locale. Entrambi i casi potrebbero rendere impossibile per l'organizzazione ripristinare l'account.
- Circostanze impreviste come una calamità naturale durante la quale la rete cellulare o altre reti potrebbero non essere disponibili. 

## <a name="create-two-cloud-based-emergency-access-accounts"></a>Creare due o più account di accesso di emergenza basati sul cloud

Creare due o più account di accesso di emergenza. Devono essere account solo cloud che usano il dominio \*.onmicrosoft.com e che non sono federati o sincronizzati da un ambiente locale.

Durante la configurazione di questi account, devono essere soddisfatti i requisiti seguenti:

- Gli account di accesso di emergenza non devono essere associati a un utente specifico dell'organizzazione. Assicurarsi che gli account non siano connessi a cellulari forniti ai dipendenti, token hardware che viaggiano con i singoli dipendenti o altre credenziali specifiche del dipendente. Questa precauzione consente di affrontare le situazioni in cui un singolo dipendente è irraggiungibile quando sono necessarie le credenziali. È importante verificare che tutti i dispositivi registrati vengano conservati in una posizione nota e sicura con più mezzi di comunicazione con Azure AD.
- Il meccanismo di autenticazione usato per un account di accesso di emergenza deve essere diverso da quello usato dagli altri account amministrativi, inclusi altri account di accesso di emergenza.  Ad esempio, se l'amministratore usuale accede tramite l'autenticazione a più fattori locale, Azure MFA sarebbe un meccanismo diverso.  Tuttavia, se Azure MFA rappresenta la parte principale dell'autenticazione per gli account amministrativi, è consigliabile adottare un approccio diverso per questi ultimi, ad esempio usare l'accesso condizionale con un provider di autenticazione a più fattori di terze parti.
- Il dispositivo o le credenziali non devono scadere o trovarsi nell'ambito della pulizia automatica per mancanza di utilizzo.  
- È necessario rendere permanente l'assegnazione del ruolo di amministratore globale per gli account di accesso di emergenza. 


### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>Escludere almeno un account dall'autenticazione a più fattori basata su telefono

Per ridurre il rischio di attacchi derivanti da una password compromessa, in Azure AD è consigliabile richiedere l'autenticazione a più fattori per tutti gli utenti singoli. Questo gruppo include gli amministratori e tutti gli altri utenti (ad esempio dirigenti del reparto finanziario) per cui un account compromesso avrebbe un impatto significativo.

Tuttavia, almeno uno degli account di accesso di emergenza deve avere un meccanismo di autenticazione a più fattori diverso rispetto agli altri account non di emergenza. Questo include le soluzioni di autenticazione a più fattori di terze parti. Se si hanno criteri di accesso condizionale per richiedere l'[autenticazione a più fattori per ogni amministratore](../authentication/howto-mfa-userstates.md) per Azure AD e altre app connesse in modalità SaaS, è opportuno escludere gli account di accesso di emergenza da questo requisito e configurare un meccanismo diverso. Inoltre, assicurarsi che gli account non abbiano criteri di autenticazione a più fattori per utente.

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>Escludere almeno un account dai criteri di accesso condizionale

Durante un'emergenza, non è desiderabile che un criterio possa potenzialmente bloccare l'accesso necessario per risolvere un problema. Almeno un account di accesso di emergenza deve essere escluso da tutti i criteri di accesso condizionale. Se è stato abilitato un [criterio di base](../conditional-access/baseline-protection.md), è necessario escludere gli account di accesso di emergenza.

## <a name="additional-guidance-for-hybrid-customers"></a>Indicazioni aggiuntive per i clienti con ambienti ibridi

Un'altra possibilità, per le organizzazioni che usano Azure AD Domain Services e AD FS o un provider di identità simile per eseguire la federazione con Azure AD, consiste nel configurare un account di accesso di emergenza la cui attestazione MFA può essere fornita dal provider.  Ad esempio, l'account di accesso di emergenza potrebbe essere supportato da una coppia di certificato e chiave archiviata su una smart card.  Quando l'utente viene autenticato in Active Directory, AD FS può fornire un'attestazione ad Azure AD per indicare che l'utente ha soddisfatto i requisiti MFA.  Anche con questo approccio, le organizzazioni devono comunque disporre di account di accesso di emergenza basati sul cloud nel caso in cui non sia possibile stabilire la federazione. 

## <a name="store-devices-and-credentials-in-a-safe-location"></a>Archiviare dispositivi e credenziali in un luogo sicuro

Le organizzazioni devono garantire che le credenziali per gli account di accesso di emergenza siano sempre conservate in modo sicuro e siano note solo a coloro che sono autorizzati a usarle. Alcuni clienti usano una smart card, mentre altri usano password. Una password per un account di accesso di emergenza in genere è suddivisa in due o tre parti, scritte su pezzi di carta separati e archiviate in casseforti a prova di fuoco in luoghi sicuri e separati.

Se si usano password, assicurarsi che gli account abbiano password complesse senza scadenza. Idealmente, le password devono contenere almeno 16 caratteri generati in modo casuale.


## <a name="monitor-sign-in-and-audit-logs"></a>Monitorare i log di accesso e di controllo

Monitorare i [log di accesso e di controllo di Azure AD](../reports-monitoring/concept-sign-ins.md) per controllare eventuali accessi e attività degli account di accesso di emergenza. In genere questi account non dovrebbero accedere e apportare modifiche, pertanto il loro uso è probabilmente anomalo e richiede un'analisi di sicurezza.

## <a name="validate-accounts-at-regular-intervals"></a>Convalidare gli account a intervalli regolari

Per formare i membri del personale per l'uso degli account di accesso di emergenza e convalidare gli account di accesso di emergenza, eseguire le operazioni minime seguenti a intervalli regolari:

- Assicurarsi che il personale che monitora la sicurezza sia informato che l'attività di verifica degli account è in corso.
- Assicurarsi che il processo per gli scenari di emergenza per usare questi account sia documentato e corrente.
- Assicurarsi che gli amministratori e i responsabili della sicurezza che potrebbero dover eseguire queste procedure durante un'emergenza siano formati sul processo.
- Aggiornare le credenziali dell'account, in particolare le password, per gli account di accesso di emergenza e quindi verificare che gli account di accesso di emergenza possano accedere ed eseguire attività amministrative.
- Verificare che gli utenti non abbiano l'autenticazione a più fattori o la reimpostazione della password self-service (SSPR) per qualsiasi dispositivo utente o per le informazioni personali. 
- Se gli account sono registrati per l'autenticazione a più fattori per un dispositivo da usare durante l'accesso o l'attivazione del ruolo, assicurarsi che il dispositivo sia accessibile a tutti gli amministratori che potrebbero aver bisogno di usarlo durante un'emergenza. Verificare inoltre che il dispositivo possa comunicare tramite almeno due percorsi di rete che non condividono una modalità di errore comune. Ad esempio, il dispositivo può comunicare con Internet tramite la rete wireless di una struttura e una rete cellulare.

Questi passaggi devono essere eseguiti a intervalli regolari e per le modifiche chiave:

- Almeno ogni 90 giorni
- In caso di modifica recente del personale IT, ad esempio per un cambio di mansione, una partenza o una nuova assunzione
- Quando le sottoscrizioni di Azure AD dell'organizzazione sono cambiate

## <a name="next-steps"></a>Passaggi successivi

- [Protezione dell'accesso con privilegi per le distribuzioni ibride e cloud in Azure AD](directory-admin-roles-secure.md)
- [Aggiungere utenti tramite Azure AD](../fundamentals/add-users-azure-active-directory.md) e [assegnare il nuovo utente al ruolo di amministratore globale](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [Iscriversi a Azure AD Premium](../fundamentals/active-directory-get-started-premium.md), se non è ancora stato fatto
- [Come richiedere la verifica in due passaggi per un utente](../authentication/howto-mfa-userstates.md)
- [Configurare protezioni aggiuntive per gli amministratori globali in Office 365](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts), se si usa Office 365
- [Avviare una verifica di accesso per gli amministratori globali](../privileged-identity-management/pim-how-to-start-security-review.md) e [spostare gli amministratori globali esistenti in ruoli di amministratore più specifici](directory-assign-admin-roles.md)
