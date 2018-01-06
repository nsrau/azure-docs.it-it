---
title: Gestire gli account amministrativi di accesso di emergenza in Azure AD | Documenti Microsoft
description: In questo articolo viene descritto come utilizzare gli account di accesso di emergenza per consentire alle organizzazioni di limitare l'accesso con privilegi in un ambiente di Azure Active Directory esistente.
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
ms.openlocfilehash: 039012b8ba0b83f6338128a2200d1232ae6467f3
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="manage-emergency-access-administrative-accounts-in-azure-ad"></a>Gestire gli account amministrativi di accesso di emergenza in Azure AD 

Per la maggior parte delle attività quotidiane, *amministratore globale* dagli utenti non sono necessari i diritti. Gli utenti non devono essere in modo permanente assegnati al ruolo, poiché potrebbe inavvertitamente eseguono un'attività che richiede autorizzazioni più elevate rispetto a cui devono disporre. Quando gli utenti non devono fungere da un amministratore globale, attivano l'assegnazione di ruolo usando Azure Active Directory (Azure AD) Privileged Identity Management (PIM), nel proprio account o un account amministratore alternativo.

Oltre ai diritti di accesso amministrativi l'aggiunta degli utenti per se stessi, è necessario impedire viene inavvertitamente bloccato per l'amministrazione del tenant di Azure AD perché non è possibile accedere né attivare l'account di un singolo utente esistente come un amministratore. È possibile ridurre l'impatto di accidentale mancanza di accesso amministrativo archiviando due o più *gli account di accesso di emergenza* nel tenant.

Gli account di accesso di emergenza consente alle organizzazioni di limitare l'accesso con privilegi in un ambiente di Azure Active Directory esistente. Questi account hanno privilegi elevati e non sono assegnati a utenti specifici. Gli account di accesso di emergenza sono limitati a emergenze o scenari "break glass", in cui non è possibile usare i normali account amministrativi. Le organizzazioni devono mantenere un obiettivo di limitare l'utilizzo dell'account emergenza solo il tempo durante i quali è necessario.

Un'organizzazione potrebbe essere necessario utilizzare un account di accesso di emergenza nelle situazioni seguenti:

 - Gli account utente sono federati e federazione non è attualmente disponibile a causa di un'interruzione di rete di cella o di un'interruzione di provider di identità. Ad esempio, se l'host del provider di identità nell'ambiente è stato disattivato, gli utenti potrebbero essere non è possibile accedere quando Azure AD Reindirizza al provider di identità. 
 - Gli amministratori vengono registrati tramite Azure multi-Factor Authentication e non sono disponibili tutti i singoli dispositivi. Gli utenti potrebbero essere Impossibile completare l'autenticazione a più fattori per attivare un ruolo. Ad esempio, un'interruzione di rete della cella impedisce di rispondere alle chiamate telefoniche o la ricezione di messaggi di testo, i meccanismi di autenticazione solo due che siano registrati per il dispositivo. 
 - L'utente con l'accesso amministrativo globale più recente ha lasciato l'organizzazione. Azure Active Directory impedisce l'ultima *amministratore globale* account vengano eliminati, ma non impedisce l'account venga eliminato o disabilitato in locale. Entrambi i casi potrebbero rendere l'organizzazione non è possibile ripristinare l'account.

## <a name="initial-configuration"></a>Configurazione iniziale

Creare due o più account di accesso di emergenza. Queste devono essere account solo cloud che usano il \*. dominio onmicrosoft.com e che non sono federati o sincronizzati da un ambiente locale. 

Gli account non devono essere associati a qualsiasi utente nell'organizzazione. Le organizzazioni devono garantire che le credenziali per questi account siano sempre sicura e noti solo a coloro che è autorizzati a usarle. 

> [!NOTE]
> Password dell'account per un account di accesso di emergenza in genere è suddivisa in due o tre parti, scritta separate su carta e archiviate in luogo protetta casseforti che si trovano in località sicuro e separato. 
>
> Assicurarsi che gli account di accesso di emergenza non connessi a qualsiasi dipendente ha fornito telefoni cellulari, hardware token inviati con i singoli dipendenti o altre credenziali specifici del dipendente. Questa precauzione vengono illustrate le istanze in cui un singolo dipendente è raggiungibile quando sono necessarie le credenziali. 

### <a name="initial-configuration-with-permanent-assignments"></a>Configurazione iniziale con assegnazioni permanenti

È possibile impostare gli utenti membri permanenti del *amministratore globale* ruolo. Questa opzione potrebbe essere appropriata per le organizzazioni che non dispone di sottoscrizioni di Azure AD Premium P2.

Per ridurre il rischio di attacchi derivanti da una compromessa password, Azure AD è consigliabile richiedere l'autenticazione a più fattori per tutti gli utenti singoli. Questo gruppo deve includere gli amministratori e tutti gli altri utenti (ad esempio, dirigenti finanziari) il cui account compromesso avrebbe un impatto significativo. 

Tuttavia, se l'organizzazione non dispone di dispositivi condivisi, multi-Factor Authentication potrebbe non essere possibili per questi account di accesso di emergenza. Se si configura un criterio di accesso condizionale per richiedere [registrazione a multi-Factor Authentication per ogni amministratore](https://docs.microsoft.com/en-us/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states) per Azure AD e l'altra connessa software come un servizio (SaaS) di App, potrebbe essere necessario configurare i criteri esclusioni per escludere gli account di accesso di emergenza questo requisito.

### <a name="initial-configuration-with-approvals"></a>Configurazione iniziale con approvazioni

Un'altra opzione consiste nel configurare gli utenti idonei e ai responsabili approvazione di attivare il *amministratore globale* ruolo. Questa opzione richiede l'organizzazione sottoscrizioni di Azure AD Premium P2. Richiede inoltre un'opzione di multi-Factor Authentication adatta per la condivisione tra più utenti e l'ambiente di rete. Questi requisiti sono validi perché l'attivazione del *amministratore globale* ruolo richiede agli utenti di avere precedentemente eseguito multi-Factor Authentication. Per ulteriori informazioni, vedere [come richiedere l'autenticazione a più fattori in Azure AD Privileged Identity Management](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa).

Non è consigliabile utilizzare multi-Factor Authentication che ha associato con dispositivi personali per gli account di accesso di emergenza. In caso di emergenza effettiva, la persona che richiede l'accesso a un dispositivo registrato a multi-Factor Authentication potrebbe non essere quello che contiene il dispositivo personale. 

Considerare anche il panorama delle minacce. Ad esempio, potrebbe verificarsi una circostanza imprevista, ad esempio una calamità naturale emergenza durante il quale un telefono cellulare o ad altre reti potrebbero non essere disponibile. È importante verificare che tutti i dispositivi registrati vengono mantenuti in un percorso noto e sicuro con più di mezzo di comunicazione con Azure AD.

## <a name="ongoing-monitoring"></a>Monitoraggio in corso

Monitoraggio di [Accedi AD Azure e controllo Registra](https://docs.microsoft.com/en-us/azure/active-directory/active-directory-reporting-activity-sign-ins) per eventuali accessi e controllare l'attività dall'account di accesso di emergenza. In genere tali account non devono essere Accedi e devono prendere le modifiche, pertanto l'uso di essi è destinato a essere anomali e richiedono l'analisi di sicurezza.

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>La convalida di verifica account deve avvenire a intervalli regolari

Per convalidare l'account, eseguire la procedura seguente almeno:
- Ogni 90 giorni.
- Quando si verifica una modifica recente il personale IT, ad esempio una modifica del processo, esiste una forte differenza o una nuova assunzione.
- Quando vengono modificate le sottoscrizioni di Azure AD dell'organizzazione.

Per eseguire il training membri del personale per utilizzare gli account di accesso di emergenza, eseguire le operazioni seguenti:

* Assicurarsi che il personale di monitoraggio di sicurezza vengano informato che l'attività di verifica account è in corso.
* Verificare che l'account utente del cloud possano accedere e attivare i ruoli e che gli utenti che potrebbe essere necessario eseguire questi passaggi durante un'emergenza vengono eseguito il training del processo.
* Verificare che essi non sono registrati multi-Factor Authentication o la reimpostazione della password self-service (SSPR) a qualsiasi singolo utente dispositivo o informazioni personali. 
* Se gli account sono registrati per l'autenticazione a più fattori per un dispositivo, da utilizzare durante l'attivazione del ruolo, assicurarsi che il dispositivo sia accessibile a tutti gli amministratori che desiderano utilizzarlo durante un'emergenza. Verificare inoltre che il dispositivo sia registrato almeno due meccanismi che non condividono una modalità di errore comuni. Ad esempio, il dispositivo può comunicare con internet tramite rete wireless di una funzionalità di gestione e una rete di provider di cella.
* Aggiornare le credenziali dell'account.

## <a name="next-steps"></a>Passaggi successivi
- [Aggiungere un utente basato su cloud](add-users-azure-active-directory.md) e [assegnare il nuovo utente al ruolo amministratore globale](active-directory-users-assign-role-azure-portal.md).
- [Iscriversi a Azure Active Directory Premium](active-directory-get-started-premium.md), se si è ancora iscritti già.
- [Richiedi multi-Factor Authentication di Azure per i singoli utenti assegnati come amministratori](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states).
- [Configurare le protezioni aggiuntive per gli amministratori globali in Office 365](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560), se si utilizza Office 365.
- [Eseguire una verifica di accesso di amministratori globali](active-directory-privileged-identity-management-how-to-start-security-review.md) e [transizione amministratori globali esistenti per più specifici ruoli di amministratore](active-directory-assign-admin-roles-azure-portal.md).


