---
title: Gestione degli account amministrativi di accesso di emergenza in Azure AD | Documenti Microsoft
description: Viene descritto come utilizzare gli account di accesso di emergenza per consentire alle organizzazioni di limitare l'accesso con privilegi in un ambiente di Azure Active Directory esistente.
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
ms.openlocfilehash: 75ea8e445c890e7af5ab14f4fc4c53cfb1af4568
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="managing-emergency-access-administrative-accounts-in-azure-ad"></a>Gestione di account amministrativi di accesso di emergenza in Azure AD 

Per la maggior parte delle attività quotidiane, non sono necessari diritti di amministratore globale.  Gli utenti non devono essere in modo permanente assegnati al ruolo, come l'utente potrebbe inavvertitamente eseguire un'attività con autorizzazioni più elevate rispetto a quelle necessarie. Quando un utente deve agire come un amministratore globale, attivano l'assegnazione di ruolo utilizzando Azure AD PIM, nell'account in uso o in un account amministratore alternativo.

Oltre agli utenti l'aggiunta dei diritti di accesso di per se stessi, è necessario preparare per assicurarsi che non venga applicato in una situazione in cui si potrebbe inavvertitamente bloccati dal amministrazione del loro tenant di Azure AD a causa della loro impossibilità di accedere ai clienti in o attivare un esistente singoli account utente come amministratore.  È possibile ridurre l'impatto di accidentale mancanza di accesso amministrativo tramite l'archiviazione al proprio tenant di due o più *gli account di accesso di emergenza*.

Gli account di accesso di emergenza consentono alle organizzazioni di limitare l'accesso con privilegi in un ambiente di Azure Active Directory esistente. Questi account hanno privilegi elevati e non sono assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati a emergenza per gli scenari con "break glass" in cui non è possibile utilizzare gli account amministrativi normale.  Le organizzazioni devono garantire lo scopo di controllo e la riduzione dell'utilizzo dell'account emergenza solo quel tempo per cui è necessario.

Scenari in cui un'organizzazione potrebbe essere necessario apportare utilizzare di un account di accesso di emergenza:

 - Gli account utente sono federati e la federazione è disponibile a causa di un'interruzione di rete o un'interruzione del servizio del provider di identità.  Ad esempio, se l'host del provider di identità nell'ambiente del cliente è stato disattivato, quindi un utente potrebbe non essere in grado di accedere quando Azure AD Reindirizza al provider di identità. 
 - Gli amministratori vengono registrati tramite l'autenticazione a più fattori e non sono disponibili tutti i singoli dispositivi.  È possibile che gli utenti siano non in grado di completare l'autenticazione a più fattori per attivare un ruolo, se ad esempio un'interruzione di rete cella può impedire agli utenti la possibilità di rispondere a chiamate telefoniche o ricevere messaggi di testo e che è l'unico meccanismo che registrato per il dispositivo. 
 - Ultima persona che hanno accesso amministrativo globale lasciato l'organizzazione.  Azure Active Directory impedisce l'eliminazione l'ultimo account di amministratore globale, ma non impedisce l'account venga eliminato o disabilitato in locale, causando una situazione in cui è in grado di ripristinare l'account dell'organizzazione.

## <a name="initial-configuration"></a>Configurazione iniziale

Creare due o più account di accesso di emergenza.  Queste devono essere account solo cloud utilizzando il *. c o m dominio, non federati o sincronizzati da un ambiente locale.  

Non deve essere associati a tutti gli utenti singoli dell'organizzazione.  Le organizzazioni devono verificare che le credenziali per questi account sono mantenute protetta e noti solo a coloro che è autorizzati a usarle. 

Nota: In genere, la password dell'account per un account di accesso di emergenza è suddivisa in due o tre parti e quindi scritta separate su carta e archiviata nel protette, luogo casseforti che sono in posizioni diverse sicura. Assicurarsi che gli account di accesso di emergenza non connessi a qualsiasi dipendente ha fornito telefoni cellulari, hardware token inviati con i singoli dipendenti o altre credenziali specifici del dipendente, nel caso in cui tale singolo dipendente non è raggiungibile al momento sono necessarie le credenziali. 

### <a name="initial-configuration-with-permanent-assignments"></a>Configurazione iniziale con assegnazioni permanenti

È possibile assegnare agli utenti come membri permanenti del ruolo amministratore globale.  Questo sarebbe ideale per le organizzazioni che non dispone di sottoscrizioni di Azure AD Premium P2.

Azure AD è consigliabile richiedere l'autenticazione a più fattori (MFA) per tutti gli utenti singoli, compresi gli amministratori e tutti gli altri utenti che avrebbero un impatto significativo se il proprio account è stato compromesso (ad esempio finanziaria). Ciò consente di ridurre il rischio di attacchi causato da una password compromessa. Tuttavia, se l'organizzazione non dispone di dispositivi condivisi, quindi MFA potrebbe non avvenire per questi account di accesso di emergenza.  Se si configura un criterio di accesso condizionale per richiedere [registrazione autenticazione a più fattori per ogni amministratore](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) per Azure AD e l'altra connessa App SaaS, potrebbe essere necessario configurare le esclusioni di criteri per escludere gli account di accesso di emergenza da questo requisito.

### <a name="initial-configuration-with-approvals"></a>Configurazione iniziale con approvazioni

Un'altra opzione consiste nel configurare tali utenti il diritto e i responsabili approvazione per attivare il ruolo di amministratore globale.  Ciò richiede all'organizzazione di disporre di sottoscrizioni di Azure AD Premium P2, nonché un'opzione di autenticazione a più fattori adatta per la condivisione tra più utenti e l'ambiente di rete.  In questo modo l'attivazione del ruolo amministratore globale richiede all'utente di avere precedentemente eseguito l'autenticazione a più fattori.  Altre informazioni, vedere [come richiedere l'autenticazione a più fattori in Azure AD Privileged Identity Management](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa).

Utilizzando l'autenticazione a più fattori associate ai dispositivi personali non sono consigliate per gli account di accesso di emergenza, perché in una reale emergenza una persona che deve avere accesso a un dispositivo registrato MFA potrebbe non essere quello che contiene il dispositivo personale.  Prendere in considerazione il panorama delle minacce, anche se a causa di circostanze impreviste telefono cellulare o altre reti non sono disponibili durante un'emergenza naturale di emergenza.  Pertanto, è importante verificare che tutti i dispositivi registrati vengono mantenuti in un percorso noto e sicuro con più di mezzo di comunicazione con Azure AD.

## <a name="ongoing-monitoring"></a>Monitoraggio in corso

Monitoraggio di [Azure AD accedere e log di controllo](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-activity-sign-ins) per eventuali accessi e controllare l'attività dagli account di accesso di emergenza.  In genere tali account non devono essere Accedi e devono prendere le modifiche, pertanto l'uso di essi è destinato a essere anomali e richiedono l'analisi di sicurezza.

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>La convalida di controllo dell'account deve ripetere a intervalli regolari

Eseguire almeno i passaggi seguenti:
 - Ogni 90 giorni
 - Quando si verifica una modifica recente personale IT: un processo di modifica, partenza o nuova assunzione
 - Quando vengono modificate le sottoscrizioni di Azure AD dell'organizzazione

Per assicurarsi che il personale viene eseguito il training in come usare gli account di accesso di emergenza:

1.  Verificare il personale di monitoraggio di sicurezza siano consapevoli che l'attività di controllo dell'account è in corso.
2.  Verificare che l'account utente del cloud possano accedere e attivare i ruoli e che gli utenti che potrebbero essere necessario eseguire questi passaggi durante un'emergenza vengono eseguito il training del processo.
3.  Verificare che essi non sono stati registrati per l'autenticazione a più fattori o SSPR a dispositivi di qualsiasi singolo utente o informazioni personali.  
4. Se gli account sono registrati per l'autenticazione a più fattori per un dispositivo, da utilizzare durante l'attivazione del ruolo, verificare che il dispositivo non è accessibile a tutti gli amministratori che desiderano utilizzarlo durante un'emergenza.  Verificare inoltre che il dispositivo sia registrato almeno due meccanismi che non condividono una modalità di errore comuni (ad esempio, il dispositivo è in grado di comunicare a Internet tramite rete wireless a di una funzionalità nonché tramite una rete di provider di cella).
5.  Aggiornare le credenziali.

## <a name="next-steps"></a>Passaggi successivi
- [Aggiungere un utente basato su cloud](add-users-azure-active-directory.md) e [assegnare il nuovo utente al ruolo amministratore globale](active-directory-users-assign-role-azure-portal.md)
- [Iscriversi a Azure Active Directory Premium](active-directory-get-started-premium.md), se non hai già
- [Richiedere autenticazione a più fattori di Azure per i singoli utenti assegnati come amministratori](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states)
- [Configurare le protezioni aggiuntive per gli amministratori globali in Office 365](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560), se si utilizza Office 365
- [Eseguire una verifica di accesso di amministratori globali](active-directory-privileged-identity-management-how-to-start-security-review.md) e [transizione amministratori globali esistenti per più specifici ruoli di amministratore](active-directory-assign-admin-roles-azure-portal.md)

