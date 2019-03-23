---
title: Descrizioni e autorizzazioni dei ruoli di amministratore - Azure Active Directory | Microsoft Docs
description: Il ruolo di amministratore consente di aggiungere utenti, assegnare ruoli amministrativi, reimpostare le password utente, gestire le licenze utente o gestire i domini.
services: active-directory
author: curtand
manager: mtillman
search.appverid: MET150
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/12/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9793ad76859fdd14b3194dfeb77e68905ff58fe5
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58362068"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Autorizzazioni del ruolo di amministratore in Azure Active Directory

Usa Azure Active Directory (Azure AD), è possibile designare limitato agli amministratori di gestire le attività di identità nei ruoli con meno privilegi. Gli amministratori possono essere assegnati per tali finalità come aggiungendo o modificando gli utenti, assegnare ruoli amministrativi, la reimpostazione delle password utente, gestione delle licenze utente e la gestione di nomi di dominio. Le autorizzazioni utente predefinite possono essere modificate solo nelle impostazioni utente in Azure AD.

L'amministratore globale ha accesso a tutte le funzionalità amministrative. Per impostazione predefinita, la persona che effettua l'iscrizione per una sottoscrizione di Azure riceve il ruolo Amministratore globale per la directory. Solo gli amministratori globali e gli amministratori dei ruoli con privilegi possono delegare i ruoli di amministratore. Per ridurre il rischio per l'azienda, è consigliabile assegnare questo ruolo solo ad alcune persone della società.


## <a name="assign-or-remove-administrator-roles"></a>Assegnare o rimuovere ruoli di amministratore

Per informazioni su come assegnare ruoli amministrativi a un utente in Azure Active Directory, vedere [Visualizzare e assegnare i ruoli di amministratore in Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Ruoli disponibili

Sono disponibili i ruoli di amministratore seguenti:

* **[Amministratore di applicazioni](#application-administrator)**: gli utenti in questo ruolo possono creare e gestire tutti gli aspetti delle applicazioni aziendali, le registrazioni delle applicazioni e le impostazioni proxy dell'applicazione. Questo ruolo concede inoltre la possibilità consentire le autorizzazioni delegate e le autorizzazioni dell'applicazione escluse Microsoft Graph e Azure AD Graph. Gli utenti assegnati a questo ruolo non vengono aggiunti come proprietari durante la creazione di nuove registrazioni per l'applicazione o le applicazioni aziendali.

  <b>Importante</b>: questo ruolo concede la possibilità di gestire le credenziali delle applicazioni. Gli utenti assegnati a questo ruolo possono aggiungere credenziali a un'applicazione e usarle per rappresentare l'identità dell'applicazione. Se all'identità dell'applicazione è concesso l'accesso ad Azure Active Directory, con la conseguente possibilità, ad esempio, di creare o aggiornare utenti o altri oggetti, un utente assegnato a questo ruolo potrà eseguire tali azioni mentre rappresenta l'applicazione. La possibilità di rappresentare l'identità dell'applicazione potrebbe costituire un'elevazione dei privilegi rispetto alle azioni che l'utente può eseguire tramite le assegnazioni dei ruoli in Azure Active Directory. È importante comprendere che assegnare a un utente il ruolo di Amministratore di applicazioni gli concede la possibilità di rappresentare l'identità di un'applicazione.

* **[Sviluppatore di applicazioni](#application-developer)**: gli utenti in questo ruolo possono creare registrazioni di applicazioni quando l'opzione "Gli utenti possono registrare applicazioni" è impostata su No. Questo ruolo concede inoltre l'autorizzazione per fornire il consenso per conto di proprie quando la "Gli utenti possono fornire il consenso alle App l'accesso ai dati aziendali per loro conto" è impostato su No. Gli utenti assegnati a questo ruolo vengono aggiunti come proprietari durante la creazione di nuove registrazioni per l'applicazione o le applicazioni aziendali.

* **[Amministratore dell'autenticazione](#authentication-administrator)**: Gli utenti con questo ruolo possono impostare o reimpostare le credenziali diverse dalle password. Gli amministratori di autenticazione possono richiedere agli utenti di registrare di nuovo con le credenziali della password non esistenti (ad esempio, autenticazione a più fattori o FIDO) e revocarli **memorizzazione di MFA nel dispositivo**, che richiede autenticazione a più fattori nel successivo accesso al posto degli utenti che sono utenti non amministratori o assegnati solo i ruoli seguenti:
  * Amministratore dell'autenticazione
  * Ruoli con autorizzazioni di lettura nella directory
  * Mittente dell'invito guest
  * Ruolo con autorizzazioni di lettura per il Centro messaggi
  * Lettore di report

  Il ruolo di amministratore di autenticazione è attualmente in anteprima pubblica. Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

  <b>Importante</b>: gli utenti con questo ruolo possono modificare le credenziali di utenti che possono avere accesso a dati sensibili, informazioni private o configurazioni critiche sia all'interno che all'esterno di Azure Active Directory. La modifica delle credenziali di un utente può implicare la possibilità di assumere l'identità e le autorizzazioni di quell'utente. Ad esempio: 

  * Proprietari di Registrazione dell'applicazione e Applicazione aziendale, che possono gestire le credenziali delle applicazioni di loro proprietà. Tali applicazioni potrebbero avere autorizzazioni con privilegi in Azure Active Directory e altrove non concesse agli amministratori dell'autenticazione. Tramite questo percorso di un amministratore di autenticazione potrebbe essere in grado di assumere l'identità del proprietario di un'applicazione e quindi ulteriormente assumere l'identità di un'applicazione con privilegi, aggiornare le credenziali per l'applicazione.
  * Proprietari di sottoscrizioni Azure, che potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche di Azure.
  * Proprietari di gruppi di sicurezza e di gruppi di Office 365, che possono gestire l'appartenenza al gruppo. Tali gruppi potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche in Azure Active Directory o altrove.
  * Amministratori in altri servizi all'esterno di Azure Active Directory, ad esempio Exchange Online, Centro sicurezza e conformità di Office e sistemi di gestione delle risorse umane.
  * Non amministratori come dirigenti, addetti degli uffici legali e dipendenti delle risorse umane che possono avere accesso a dati sensibili o informazioni riservate.

* **[Amministratore fatturazione](#billing-administrator)**: può effettuare acquisti, gestire le sottoscrizioni e i ticket di supporto e monitorare l'integrità dei servizi.

* **[Amministratore applicazione cloud](#cloud-application-administrator)**: gli utenti in questo ruolo hanno le stesse autorizzazioni del ruolo di amministratore di applicazioni, esclusa la possibilità di gestire il proxy dell'applicazione. Questo ruolo concede la possibilità di creare e gestire tutti gli aspetti delle applicazioni aziendali e delle registrazioni dell'applicazione. Questo ruolo concede inoltre la possibilità consentire le autorizzazioni delegate e le autorizzazioni dell'applicazione escluse Microsoft Graph e Azure AD Graph. Gli utenti assegnati a questo ruolo non vengono aggiunti come proprietari durante la creazione di nuove registrazioni per l'applicazione o le applicazioni aziendali.

  <b>Importante</b>: questo ruolo concede la possibilità di gestire le credenziali delle applicazioni. Gli utenti assegnati a questo ruolo possono aggiungere credenziali a un'applicazione e usarle per rappresentare l'identità dell'applicazione. Se all'identità dell'applicazione è concesso l'accesso ad Azure Active Directory, con la conseguente possibilità, ad esempio, di creare o aggiornare utenti o altri oggetti, un utente assegnato a questo ruolo potrà eseguire tali azioni mentre rappresenta l'applicazione. La possibilità di rappresentare l'identità dell'applicazione potrebbe costituire un'elevazione dei privilegi rispetto alle azioni che l'utente può eseguire tramite le assegnazioni dei ruoli in Azure Active Directory. È importante comprendere che assegnare a un utente il ruolo di Amministratore applicazione cloud gli concede la possibilità di rappresentare l'identità di un'applicazione.

* **[Amministratore dispositivo cloud](#cloud-device-administrator)**: gli utenti con questo ruolo possono abilitare, disabilitare ed eliminare dispositivi in Azure AD, nonché leggere chiavi BitLocker di Windows 10 (se presenti) nel portale di Azure. Il ruolo non concede le autorizzazioni per gestire eventuali altre proprietà nel dispositivo.

* **[Amministratore di conformità](#compliance-administrator)**: gli utenti con questo ruolo hanno le autorizzazioni necessarie per gestire le funzionalità relative alla conformità nel Centro conformità Microsoft 365, nell'interfaccia di amministrazione di Microsoft 365, in Azure e nel Centro sicurezza e conformità di Office 365. Gli utenti possono anche gestire tutte le funzionalità all'interno dell'interfaccia di amministrazione di Exchange e nell'interfaccia di amministrazione di Teams e Skype for Business e creare ticket di supporto per Azure e Microsoft 365. Per altre informazioni, vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  In ingresso | Operazione consentita
  ----- | ----------
  [Centro conformità Microsoft 365](https://protection.microsoft.com) | Proteggere e gestire i dati dell'organizzazione in tutti i servizi di Microsoft 365<br>Gestire gli avvisi di conformità
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Monitorare, assegnare e verificare le attività di conformità alle normative dell'organizzazione
  [Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gestire la governance dei dati<br>Eseguire analisi sui dati e di carattere legale<br>Gestire le richieste di soggetti interessati
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visualizzare tutti i dati di controllo di Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Disporre di autorizzazioni di sola lettura e gestire gli avvisi<br>Creare e modificare i criteri di file e consentire azioni di governance sui file<br> Visualizzare tutti i report predefiniti in Gestione dati

<!--* **[Compliance Data Administrator](#compliance-data-administrator)**: Users with this role have permissions to protect and track data in the Microsoft 365 compliance center, Microsoft 365 admin center, and Azure. Users can also manage all features within the Exchange admin center, Compliance Manager, and Teams & Skype for Business admin center and create support tickets for Azure and Microsoft 365.

  In | Can do
  ----- | ----------
  [Microsoft 365 compliance center](https://protection.microsoft.com) | Monitor compliance-related policies across Microsoft 365 services<br>Manage compliance alerts
  [Compliance Manager](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud) | Track, assign, and verify your organization's regulatory compliance activities
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Manage data governance<br>Perform legal and data investigation<br>Manage Data Subject Request
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | View all Intune audit data
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Has read-only permissions and can manage alerts<br>Can create and modify file policies and allow file governance actions<br> Can view all the built-in reports under Data Management
-->
* **[Amministratore dell'accesso condizionale](#conditional-access-administrator)**: gli utenti con questo ruolo possono gestire le impostazioni di accesso condizionale di Azure Active Directory.
  > [!NOTE]
  > Per distribuire il criterio di accesso condizionale di Exchange ActiveSync in Azure, l'utente deve essere anche amministratore globale.
  
* **[Responsabile approvazione per l'accesso a Customer Lockbox](#customer-lockbox-access-approver)**: gestisce le [richieste Customer Lockbox](https://docs.microsoft.com/office365/admin/manage/customer-lockbox-requests) nell'organizzazione. Gli utenti con questo ruolo ricevono notifiche di posta elettronica per le richieste Customer Lockbox e possono approvare e rifiutare le richieste dall'interfaccia di amministrazione di Microsoft 365. Possono anche attivare o disattivare la funzionalità Customer Lockbox. Solo gli amministratori globali possono reimpostare le password degli utenti assegnati a questo ruolo.
  <!--  This was announced in August of 2018. https://techcommunity.microsoft.com/t5/Security-Privacy-and-Compliance/Customer-Lockbox-Approver-Role-Now-Available/ba-p/223393-->

* **[Amministratori di dispositivi](#device-administrators)**: questo ruolo è disponibile per l'assegnazione solo come amministratore locale aggiuntivo in [Impostazioni dispositivo](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Gli utenti con questo ruolo diventano amministratori del computer locale in tutti i dispositivi Windows 10 aggiunti ad Azure Active Directory. Non possono gestire gli oggetti di dispositivi in Azure Active Directory. 

* **[Ruoli con autorizzazioni di lettura nella directory](#directory-readers)**: si tratta di un ruolo legacy che deve essere assegnato alle applicazioni che non supportano il [framework di consenso](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Non deve essere assegnato agli utenti.

* **[Account di sincronizzazione della directory](#directory-synchronization-accounts)**: Non usare. Questo ruolo viene assegnato automaticamente al servizio Azure AD Connect e non è progettato o supportato per altri usi.

* **[Ruoli con autorizzazioni di scrittura nella directory](#directory-writers)**: si tratta di un ruolo legacy che deve essere assegnato alle applicazioni che non supportano il [framework di consenso](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Non deve essere assegnato agli utenti.

* **[Amministratore di Dynamics 365/Amministratore di CRM](#crm-service-administrator)**: gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Dynamics 365 Online, quando il servizio è presente, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità dei servizi. Altre informazioni sono disponibili in [Utilizzare il ruolo di amministratore del servizio per gestire il tenant](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).
  > [!NOTE] 
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "amministratore del servizio Dynamics 365". È l'"Amministratore di Dynamics 365" nel [portale di Azure](https://portal.azure.com).

* **[Amministratore di Exchange](#exchange-service-administrator)**: gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Exchange Online, quando il servizio è presente. Possono inoltre creare e gestire tutti i gruppi di Office 365, gestire i ticket di supporto e monitorare l'integrità del servizio. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Exchange". È l'"Amministratore di Exchange" nel [portale di Azure](https://portal.azure.com). È l'"amministratore di Exchange Online" nell'[interfaccia di amministrazione di Exchange](https://go.microsoft.com/fwlink/p/?LinkID=529144). 


* **[Amministratore globale/Amministratore società](#company-administrator)**: gli utenti con questo ruolo hanno accesso a tutte le funzionalità amministrative in Azure Active Directory, nonché ai servizi che usano identità di Azure Active Directory come Centro sicurezza Microsoft 365, Centro conformità Microsoft 365, Exchange Online, SharePoint Online e Skype for Business Online. La persona che effettua l'iscrizione per il tenant di Azure Active Directory diventa amministratore globale. Solo gli amministratori globali possono assegnare altri ruoli di amministratore. In una società possono essere presenti più amministratori globali. Gli amministratori globali possono reimpostare la password per qualsiasi utente e per tutti gli altri amministratori.

  > [!NOTE]
  > In Microsoft API Graph, Azure AD API Graph e Azure AD PowerShell, questo ruolo è identificato come "Amministratore società". È l'"amministratore globale" nel [portale di Azure](https://portal.azure.com).
  >
  >

* **[Mittente dell'invito guest](#guest-inviter)**: gli utenti con questo ruolo possono gestire gli inviti per gli utenti guest di Azure Active Directory B2B quando il valore dell'impostazione utente **I membri possono invitare** è No. Altre informazioni sulla collaborazione B2B in [Informazioni su Collaborazione B2B di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Il ruolo non include altre autorizzazioni.

* **[Amministratore di Information Protection](#information-protection-administrator)**: gli utenti con questo ruolo hanno tutte le autorizzazioni nel servizio Azure Information Protection. Questo ruolo consente di configurare etichette per i criteri di Azure Information Protection, gestire i modelli di protezione e attivare la protezione. Il ruolo tuttavia non concede le autorizzazioni relative a Identity Protection Center, Privileged Identity Management, Centro sicurezza e conformità di Office 365 e al servizio di monitoraggio dell'integrità dei servizi di Office 365.

* **[Amministratore di Intune](#intune-service-administrator)**: gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Intune Online, quando il servizio è presente. Inoltre questo ruolo implica la possibilità di gestire utenti e dispositivi per associare i criteri, nonché creare e gestire gruppi. Altre informazioni sono disponibili in [Controllo degli accessi in base al ruolo (RBAC) con Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)
  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Intune". È l'"Amministratore di Intune" nel [portale di Azure](https://portal.azure.com).

* **[Amministratore licenze](#license-administrator)**: gli utenti in questo ruolo possono aggiungere, rimuovere e aggiornare le assegnazioni di licenze a utenti, gruppi (usando licenze basate su gruppi) e gestire i percorsi di utilizzo per gli utenti. Il ruolo non garantisce la possibilità di acquistare o gestire sottoscrizioni, creare o gestire gruppi o creare o gestire utenti al di fuori del percorso di utilizzo. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

* **[Ruolo con autorizzazioni di lettura per il Centro messaggi](#message-center-reader)**: gli utenti in questo ruolo possono monitorare le notifiche e gli aggiornamenti dello stato di integrità nel [Centro messaggi di Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) per la propria organizzazione sui servizi configurati, ad esempio Exchange, Intune e Microsoft Teams. Il Ruolo con autorizzazioni di lettura per il Centro messaggi riceve settimanalmente digest di posta elettronica di post, aggiornamenti e può condividere i post del centro messaggi in Office 365. In Azure AD, gli utenti assegnati a questo ruolo avranno solo l'accesso di sola lettura ai servizi di Azure AD, ad esempio utenti e gruppi. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

* **[Supporto partner - Livello 1](#partner-tier1-support)**: Non usare. Questo ruolo è deprecato e verrà rimosso da Azure AD in futuro. Il ruolo è riservato a pochi partner Microsoft per la rivendita e non è disponibile per un uso generale.

* **[Supporto partner - Livello 2](#partner-tier2-support)**: Non usare. Questo ruolo è deprecato e verrà rimosso da Azure AD in futuro. Il ruolo è riservato a pochi partner Microsoft per la rivendita e non è disponibile per un uso generale.

* **[Amministratore supporto tecnico (Password)](#helpdesk-administrator)**: gli utenti con questo ruolo possono modificare le password, invalidare i token di aggiornamento, gestire le richieste di servizio e monitorare l'integrità dei servizi. Invalidando un token di aggiornamento si impone all'utente di eseguire di nuovo l'accesso. Gli amministratori dell'help desk possono reimpostare le password e invalida i token di aggiornamento di altri utenti non amministratori o assegnati solo i ruoli seguenti:
  * Ruoli con autorizzazioni di lettura nella directory
  * Mittente dell'invito guest
  * Amministratore del supporto tecnico
  * Ruolo con autorizzazioni di lettura per il Centro messaggi
  * Lettore di report
  
  <b>Importante</b>: gli utenti con questo ruolo possono modificare le password di utenti che possono accedere a dati sensibili, informazioni riservate o configurazioni critiche sia dall'interno che dall'esterno di Azure Active Directory. Modificare la password di un utente può implicare la possibilità di assumere l'identità e le autorizzazioni di quell'utente. Ad esempio: 
  * Proprietari di Registrazione dell'applicazione e Applicazione aziendale, che possono gestire le credenziali delle applicazioni di loro proprietà. Tali applicazioni potrebbero disporre di autorizzazioni con privilegi in Azure Active Directory e altrove non concesse agli utenti con ruolo di Amministratore supporto tecnico. Ciò significa che un Amministratore supporto tecnico potrebbe assumere l'identità del proprietario di un'applicazione e quindi quella di un'applicazione con privilegi aggiornando le credenziali dell'applicazione.
  * Proprietari di sottoscrizioni Azure, che potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche di Azure.
  * Proprietari di gruppi di sicurezza e di gruppi di Office 365, che possono gestire l'appartenenza al gruppo. Tali gruppi potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche in Azure Active Directory o altrove.
  * Amministratori in altri servizi all'esterno di Azure Active Directory, ad esempio Exchange Online, Centro sicurezza e conformità di Office e sistemi di gestione delle risorse umane.
  * Non amministratori come dirigenti, addetti degli uffici legali e dipendenti delle risorse umane che possono avere accesso a dati sensibili o informazioni riservate.

  > [!NOTE]
  > Questo ruolo è stato precedentemente chiamato "amministratore Password" [portale di Azure](https://portal.azure.com/). Si sta modificando il nome di "Amministratore supporto tecnico" in modo che corrisponda il nome in Azure AD PowerShell, API di Azure AD Graph e API Microsoft Graph. Per un breve periodo di tempo, si modificherà il nome di "Amministratore supporto tecnico (Password)" nel portale di Azure prima di cambiare "Amministratore supporto tecnico".
  >
  
* **[Amministratore di Power BI](#power-bi-service-administrator)**: gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Power BI, quando è presente il servizio, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità dei servizi. Per altre informazioni, vedere [Informazioni sul ruolo di amministratore di Power BI](https://docs.microsoft.com/power-bi/service-admin-role).
  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Power BI". È l'"Amministratore di Power BI" nel [portale di Azure](https://portal.azure.com).

* **[L'autenticazione di amministratore con privilegi](#privileged-authentication-administrator)**: Gli utenti con questo ruolo possono impostare o reimpostare le credenziali non-password per tutti gli utenti, inclusi gli amministratori globali. Gli amministratori con privilegi di autenticazione può imporre agli utenti di registrare di nuovo con le credenziali della password non esistenti (ad esempio, autenticazione a più fattori, FIDO) e revocare 'tenere presente che MFA nel dispositivo' richiesta per l'autenticazione a più fattori all'accesso successivo di tutti gli utenti. Gli amministratori con privilegi di autenticazione è possibile:
  * Imporre agli utenti di registrare di nuovo con le credenziali della password non esistenti (ad esempio, autenticazione a più fattori, FIDO)
  * Revocare 'tenere presente che MFA nel dispositivo' richiesta per l'autenticazione a più fattori all'accesso successivo

* **[Amministratore dei ruoli con privilegi](#privileged-role-administrator)**: gli utenti con questo ruolo possono gestire le assegnazioni di ruolo in Azure Active Directory e in Azure AD Privileged Identity Management. Inoltre, questo ruolo consente la gestione di tutti gli aspetti di Privileged Identity Management.

  <b>Importante</b>: Questo ruolo concede la possibilità di gestire le assegnazioni per tutti i ruoli di Azure AD, incluso il ruolo di amministratore globale. Non prevede altre capacità con privilegi in Azure Active Directory, ad esempio la creazione o l'aggiornamento degli utenti. Tuttavia, gli utenti assegnati a questo ruolo possono concedere a se stessi o ad altri utenti privilegi aggiuntivi, perché possono assegnare ruoli aggiuntivi.

* **[Lettore di report](#reports-reader)**: Gli utenti con questo ruolo possono visualizzare i dati dei report di utilizzo e il dashboard dei report nell'interfaccia di amministrazione di Microsoft 365 e in Adoption pack in Power BI. Il ruolo consente anche l'accesso ai report e alle attività di accesso in Azure Active Directory e ai dati restituiti dall'API di creazione report di Microsoft Graph. Un utente assegnato al ruolo di lettore di report può accedere solo alle metriche rilevanti per utilizzo e adozione. Questi utenti non hanno le autorizzazioni di amministratore per configurare le impostazioni o accedere alle interfacce di amministrazione specifiche di prodotti come Exchange. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

* **[Amministratore della sicurezza](#security-administrator)**: gli utenti con questo ruolo hanno le autorizzazioni necessarie per gestire le funzionalità relative alla sicurezza nel Centro sicurezza Microsoft 365, in Azure Active Directory Identity Protection, in Azure Information Protection e nel Centro sicurezza e conformità di Office 365. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [Autorizzazioni nel Centro sicurezza e conformità di Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  In ingresso | Operazione consentita
  --- | ---
  [Centro sicurezza Microsoft 365](https://protection.microsoft.com) | Monitorare i criteri correlati alla sicurezza in tutti i servizi di Microsoft 365<br>Gestire gli avvisi e le minacce alla sicurezza<br>Visualizzazione dei report
  Centro di Identity Protection | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>Inoltre, eseguire tutte le operazioni di Identity Protection Center, tranne la reimpostazione delle password
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza<br>**Non è possibile** gestire le impostazioni o le assegnazioni di ruolo di Azure AD
  [Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Gestire i criteri di sicurezza<br>Visualizzare, analizzare e rispondere alle minacce alla sicurezza<br>Visualizzazione dei report
  Azure Advanced Threat Protection | Monitorare e rispondere alle attività sospette dal punto di vista della sicurezza
  Windows Defender ATP ed EDR | Assegnare ruoli<br>Gestire i gruppi di computer<br>Configurare il rilevamento delle minacce agli endpoint e le funzionalità automatizzate di correzione<br>Visualizzare, analizzare e rispondere agli avvisi
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visualizzare le informazioni relative a utenti, dispositivi e applicazioni e i dati di registrazione e configurazione<br>Non è consentito apportare modifiche a Intune
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Aggiungere amministratori, criteri e impostazioni, caricare i log ed eseguire azioni di governance
  [Centro sicurezza di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | è possibile visualizzare i criteri di sicurezza e gli stati di sicurezza, modificare i criteri di sicurezza, visualizzare gli avvisi e le raccomandazioni, ignorare gli avvisi e le raccomandazioni
  [Integrità dei servizi di Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visualizzare l'integrità dei servizi di Office 365

<!--* **[Security operator](#security-operator)**: Users with this role can manage alerts and have global read-only access on security-related feature, including all information in Microsoft 365 security center, Azure Active Directory, Identity Protection, Privileged Identity Management, as well as the ability to read Azure Active Directory sign-in reports and audit logs, and in Office 365 Security & Compliance Center.

  In | Can do
  --- | ---
  [Microsoft 365 security center](https://protection.microsoft.com) | All permissions of the Security Reader role<br>View, investigate, and respond to security threats alerts
  Identity Protection Center | All permissions of the Security Reader role<br>Additionally, the ability to perform all Identity Protection Center operations except for resetting passwords
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | All permissions of the Security Reader role
  [Office 365 Security & Compliance Center](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | All permissions of the Security Reader role<br>View, investigate, and respond to security alerts
  Windows Defender ATP and EDR | All permissions of the Security Reader role<br>View, investigate, and respond to security alerts
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | All permissions of the Security Reader role
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | All permissions of the Security Reader role
  [Office 365 service health](https://docs.microsoft.com/office365/enterprise/view-service-health) | View the health of Office 365 services
-->
* **[Ruolo con autorizzazioni di lettura per la sicurezza](#security-reader)**: gli utenti con questo ruolo hanno accesso globale di sola lettura alle funzionalità relative alla sicurezza, incluse tutte le informazioni nel Centro sicurezza Microsoft 365, in Azure Active Directory, Identity Protection e Privileged Identity Management, oltre alla possibilità di accedere in lettura ai log di controllo e ai report di accesso di Azure Active Directory, e nel Centro sicurezza e conformità di Office 365. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [Autorizzazioni nel Centro sicurezza e conformità di Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  In ingresso | Operazione consentita
  --- | ---
  [Centro sicurezza Microsoft 365](https://protection.microsoft.com) | Visualizzare i criteri correlati alla sicurezza in tutti i servizi di Microsoft 365<br>Visualizzare gli avvisi e le minacce alla sicurezza<br>Visualizzazione dei report
  Centro di Identity Protection | Leggere tutte le informazioni sulle impostazioni e sui report di sicurezza per le funzionalità di sicurezza<br><ul><li>Filtro posta indesiderata<li>Crittografia<li>Prevenzione della perdita dei dati<li>Antimalware<li>Protezione avanzata dalle minacce<li>Anti-phishing<li>Regole del flusso di posta
  [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure) | Ha accesso in sola lettura a tutte le informazioni presentate in Azure AD Privileged Identity Management: criteri e report per le assegnazioni di ruoli in Azure AD, verifiche della sicurezza e in futuro accesso in lettura ai report e ai dati dei criteri per gli scenari in aggiunta all'assegnazione di ruoli in Azure AD.<br>**Non può** eseguire l'iscrizione ad Azure AD PIM o apportarvi modifiche. Nel portale di PIM o tramite PowerShell, un utente di questo ruolo può attivare ruoli aggiuntivi (ad esempio amministratore globale o amministratore con privilegi di ruolo), se l'utente è idoneo per loro.
  [Centro sicurezza e conformità di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d) | Visualizzare i criteri di sicurezza<br>Visualizzare e analizzare le minacce alla sicurezza<br>Visualizzazione dei report
  Windows Defender ATP ed EDR | Visualizzare e analizzare gli avvisi
  [Intune](https://docs.microsoft.com/intune/role-based-access-control) | Visualizzare le informazioni relative a utenti, dispositivi e applicazioni e i dati di registrazione e configurazione. Non è consentito apportare modifiche a Intune.
  [Cloud App Security](https://docs.microsoft.com/cloud-app-security/manage-admins) | Disporre di autorizzazioni di sola lettura e gestire gli avvisi
  [Centro sicurezza di Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles) | è possibile visualizzare raccomandazioni, avvisi, criteri di sicurezza e stati di sicurezza, ma non è possibile apportare modifiche
  [Integrità dei servizi di Office 365](https://docs.microsoft.com/office365/enterprise/view-service-health) | Visualizzare l'integrità dei servizi di Office 365

* **[Amministratore servizio di supporto](#service-support-administrator)**: Gli utenti con questo ruolo possono aprire richieste di supporto con Microsoft per servizi di Azure e Office 365 e il dashboard del servizio e il centro messaggi nelle viste di [portale di Azure](https://portal.azure.com) e [interfaccia di amministrazione di Microsoft 365](https://admin.microsoft.com). Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore servizio di supporto". È il "Amministratore del servizio" nel [portale di Azure](https://portal.azure.com), il [interfaccia di amministrazione di Microsoft 365](https://admin.microsoft.com)e il portale di Intune.

* **[Amministratore di SharePoint](#sharepoint-service-administrator)**: gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft SharePoint Online, quando il servizio è presente, nonché la possibilità creare e gestire tutti i gruppi di Office 365, di gestire i ticket di supporto e monitorare l'integrità del servizio. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio SharePoint". È l'"Amministratore di SharePoint" nel [portale di Azure](https://portal.azure.com).

* **[Amministratore di Skype for Business o Lync](#lync-service-administrator)**: gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft Skype for Business, quando il servizio è presente, nonché la possibilità di gestire gli attributi specifici per Skype in Azure Active Directory. Inoltre, questo ruolo garantisce la possibilità di gestire i ticket di supporto e monitorare l'integrità del servizio, oltre ad accedere a Teams e all'interfaccia di amministrazione di Skype for Business. L'account deve anche avere una licenza per Teams. In caso contrario, non può eseguire i cmdlet di PowerShell per Teams. Per altre informazioni, vedere [Amministratore Skype for Business Online](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5). Per informazioni sulle licenze per Teams, vedere [Licenze per i componenti aggiuntivi Skype for Business e Microsoft Teams](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del sevizio Lync". È l'"Amministratore di Skype for Business" nel [portale di Azure](https://portal.azure.com/).

* **[Amministratore di Teams](#teams-service-administrator)**: gli utenti in questo ruolo possono gestire tutti gli aspetti del carico di lavoro Microsoft Teams tramite l'interfaccia di amministrazione di Microsoft Teams e Skype for Business e i rispettivi moduli di PowerShell. Sono inclusi, tra le altre aree, tutti gli strumenti di gestione correlati a telefonia, messaggistica, riunioni e i team stessi. Il ruolo concede anche la possibilità di creare e gestire tutti i gruppi di Office 365, gestire i ticket di supporto e monitorare l'integrità del servizio.
  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Teams". È l'"Amministratore di Teams" nel [portale di Azure](https://portal.azure.com).

* **[Amministratore comunicazioni Teams](#teams-communications-administrator)**: gli utenti con questo ruolo possono gestire gli aspetti del carico di lavoro Microsoft Teams correlati a voce e telefonia. Sono inclusi gli strumenti di gestione per l'assegnazione di numeri di telefono, i criteri per chiamate vocali e riunioni, nonché l'accesso completo al set di strumenti di analisi delle chiamate.

* **[Tecnico di supporto comunicazioni Teams](#teams-communications-support-engineer)**: gli utenti con questo ruolo possono risolvere i problemi di comunicazione all'interno di Microsoft Teams e Skype for Business tramite gli strumenti di risoluzione dei problemi relativi alle chiamate utente nell'interfaccia di amministrazione di Microsoft Teams e Skype for Business. Gli utenti in questo ruolo possono visualizzare informazioni complete sui record delle chiamate per tutti i partecipanti coinvolti. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

* **[Specialista di supporto comunicazioni Teams](#teams-communications-support-specialist)**: gli utenti con questo ruolo possono risolvere i problemi di comunicazione all'interno di Microsoft Teams e Skype for Business tramite gli strumenti di risoluzione dei problemi relativi alle chiamate utente nell'interfaccia di amministrazione di Microsoft Teams e Skype for Business. Gli utenti con questo ruolo possono visualizzare i dettagli dell'utente nella chiamata per l'utente specifico cercato. Questo ruolo non ha accesso alle funzionalità per visualizzare, creare o gestire i ticket di supporto.

* **[L'utente amministratore](#user-account-administrator)**: Gli utenti con questo ruolo possono creano utenti e gestire tutti gli aspetti di utenti con alcune restrizioni (vedere sotto) e possono aggiornare i criteri di scadenza delle password. Possono anche creare e gestire tutti i gruppi. Il ruolo consente anche di creare e gestire visualizzazioni utente, gestire i ticket di supporto e monitorare l'integrità del servizio.

  | | |
  | --- | --- |
  |Autorizzazioni generiche|<p>Creare utenti e gruppi</p><p>Creare e gestire visualizzazioni utente</p><p>Gestire ticket di supporto di Office<p>Aggiornare i criteri di scadenza password|
  |<p>Su tutti gli utenti, inclusi tutti gli amministratori</p>|<p>Gestire licenze</p><p>Gestire tutte le proprietà utente, ad eccezione del Nome dell'entità utente</p>
  |Solo sugli utenti non amministratori o in uno dei seguenti ruoli di amministratore con limitazioni:<ul><li>Ruoli con autorizzazioni di lettura nella directory<li>Mittente dell'invito guest<li>Amministratore del supporto tecnico<li>Ruolo con autorizzazioni di lettura per il Centro messaggi<li>Lettore di report<li>Amministratore utenti|<p>Eliminare e ripristinare</p><p>Disattivare e attivare</p><p>Invalidare i token di aggiornamento</p><p>Gestire tutte le proprietà utente, incluso il Nome dell'entità utente</p><p>Reimposta password</p><p>Aggiornare le chiavi dispositivo (FIDO)</p>
  
  <b>Importante</b>: gli utenti con questo ruolo possono modificare le password di utenti che possono accedere a dati sensibili, informazioni riservate o configurazioni critiche sia dall'interno che dall'esterno di Azure Active Directory. Modificare la password di un utente può implicare la possibilità di assumere l'identità e le autorizzazioni di quell'utente. Ad esempio: 
  * Proprietari di Registrazione dell'applicazione e Applicazione aziendale, che possono gestire le credenziali delle applicazioni di loro proprietà. Tali applicazioni potrebbero avere autorizzazioni con privilegi in Azure Active Directory e altrove non concesse agli utenti con ruolo di amministratore degli utenti. Ciò significa che un amministratore degli utenti potrebbe assumere l'identità del proprietario di un'applicazione e quindi quella di un'applicazione con privilegi aggiornando le credenziali dell'applicazione.
  * Proprietari di sottoscrizioni Azure, che potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche di Azure.
  * Proprietari di gruppi di sicurezza e di gruppi di Office 365, che possono gestire l'appartenenza al gruppo. Tali gruppi potrebbero avere accesso a dati sensibili, informazioni riservate o configurazioni critiche in Azure Active Directory o altrove.
  * Amministratori in altri servizi all'esterno di Azure Active Directory, ad esempio Exchange Online, Centro sicurezza e conformità di Office e sistemi di gestione delle risorse umane.
  * Non amministratori come dirigenti, addetti degli uffici legali e dipendenti delle risorse umane che possono avere accesso a dati sensibili o informazioni riservate.

## <a name="role-permissions"></a>Autorizzazioni dei ruoli
Nelle tabelle seguenti vengono descritte le autorizzazioni specifiche assegnate a ogni ruolo in Azure Active Directory. Alcuni ruoli potrebbero avere altre autorizzazioni per i servizi Microsoft al di fuori di Azure Active Directory.

### <a name="application-administrator"></a>Amministratore di applicazioni
Può creare e gestire tutti gli aspetti delle registrazioni di app e delle app aziendali.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Aggiornare la proprietà applications.audience in Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Aggiornare la proprietà applications.authentication in Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Aggiornare le proprietà di base per le applicazioni in Azure Active Directory. |
| microsoft.aad.directory/applications/create | Creare applicazioni in Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Aggiornare la proprietà applications.credentials in Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Eliminare applicazioni in Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Aggiornare la proprietà applications.owners in Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Aggiornare la proprietà applications.permissions in Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Aggiornare la proprietà applications.policies in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Creare appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/read | Eseguire la lettura di appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Aggiornare appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Eliminare appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Eseguire la lettura della proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Aggiornare la proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Creare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Eseguire la lettura della proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Aggiornare la proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Eseguire la lettura della proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare la proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Aggiornare la proprietà servicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Aggiornare la proprietà servicePrincipals.audience in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Aggiornare la proprietà servicePrincipals.authentication in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Aggiornare le proprietà di base su servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Creare servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Aggiornare la proprietà servicePrincipals.credentials in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Eliminare servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Aggiornare la proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Aggiornare la proprietà servicePrincipals.permissions in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Aggiornare la proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="application-developer"></a>Sviluppatore di applicazioni
Può creare registrazioni di applicazioni indipendentemente dall'impostazione 'Gli utenti possono registrare applicazioni'.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/applications/createAsOwner | Creare applicazioni in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/appRoleAssignments/createAsOwner | Creare appRoleAssignments in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/oAuth2PermissionGrants/createAsOwner | Creare oAuth2PermissionGrants in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/servicePrincipals/createAsOwner | Creare servicePrincipals in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |

### <a name="authentication-administrator"></a>Amministratore dell'autenticazione
È autorizzato a visualizzare, configurare e reimpostare le informazioni sul metodo di autenticazione per qualsiasi utente non amministratore.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Aggiornare le proprietà dell'autenticazione avanzata, ad esempio le informazioni sulle credenziali MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="billing-administrator"></a>Amministratore fatturazione
Può eseguire attività comuni relative alla fatturazione, ad esempio aggiornare le informazioni di pagamento.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/organization/basic/update | Aggiornare le proprietà di base sull'organizzazione in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gestire tutti gli aspetti della fatturazione di Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="desktop-analytics-administrator"></a>Amministratore di Desktop Analytics
Può accedere a servizi e strumenti di gestione desktop, ad esempio Intune, e gestirli.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gestire tutti gli aspetti di Desktop Analytics. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="cloud-application-administrator"></a>Amministratore applicazione cloud
Può creare e gestire tutti gli aspetti delle registrazioni di app e delle app aziendali, ad eccezione del Proxy di applicazione.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/applications/audience/update | Aggiornare la proprietà applications.audience in Azure Active Directory. |
| microsoft.aad.directory/applications/authentication/update | Aggiornare la proprietà applications.authentication in Azure Active Directory. |
| microsoft.aad.directory/applications/basic/update | Aggiornare le proprietà di base per le applicazioni in Azure Active Directory. |
| microsoft.aad.directory/applications/create | Creare applicazioni in Azure Active Directory. |
| microsoft.aad.directory/applications/credentials/update | Aggiornare la proprietà applications.credentials in Azure Active Directory. |
| microsoft.aad.directory/applications/delete | Eliminare applicazioni in Azure Active Directory. |
| microsoft.aad.directory/applications/owners/update | Aggiornare la proprietà applications.owners in Azure Active Directory. |
| microsoft.aad.directory/applications/permissions/update | Aggiornare la proprietà applications.permissions in Azure Active Directory. |
| microsoft.aad.directory/applications/policies/update | Aggiornare la proprietà applications.policies in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/create | Creare appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Aggiornare appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Eliminare appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/create | Creare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/read | Eseguire la lettura della proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/basic/update | Aggiornare la proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/read | Eseguire la lettura della proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/owners/update | Aggiornare la proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/policies/applicationConfiguration/policyAppliedTo/read | Eseguire la lettura della proprietà policies.applicationConfiguration in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare la proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Aggiornare la proprietà servicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Aggiornare la proprietà servicePrincipals.audience in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Aggiornare la proprietà servicePrincipals.authentication in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Aggiornare le proprietà di base su servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Creare servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Aggiornare la proprietà servicePrincipals.credentials in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/delete | Eliminare servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Aggiornare la proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Aggiornare la proprietà servicePrincipals.permissions in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Aggiornare la proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="cloud-device-administrator"></a>Amministratore dispositivo cloud
Accesso completo per la gestione dei dispositivi in Azure AD.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.aad.directory/devices/delete | Eliminare dispositivi in Azure Active Directory. |
| microsoft.aad.directory/devices/disable | Disabilitare i dispositivi in Azure Active Directory. |
| microsoft.aad.directory/devices/enable | Abilitare i dispositivi in Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="company-administrator"></a>Amministratore società
Può gestire tutti gli aspetti di Azure AD e dei servizi Microsoft che usano identità di Azure AD.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.cloudAppSecurity/allEntities/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare le proprietà standard in microsoft.aad.cloudAppSecurity. |
| microsoft.aad.directory/administrativeUnits/allProperties/allTasks | Creare ed eliminare administrativeUnits, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/applications/allProperties/allTasks | Creare ed eliminare applicazioni, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/allProperties/allTasks | Creare ed eliminare appRoleAssignments, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.aad.directory/contacts/allProperties/allTasks | Creare ed eliminare contatti, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/contracts/allProperties/allTasks | Creare ed eliminare contratti, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/devices/allProperties/allTasks | Creare ed eliminare dispositivi, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/allProperties/allTasks | Creare ed eliminare directoryRoles, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/directoryRoleTemplates/allProperties/allTasks | Creare ed eliminare directoryRoleTemplates, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/domains/allProperties/allTasks | Creare ed eliminare domini, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/groups/allProperties/allTasks | Creare ed eliminare gruppi, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/groupSettings/allProperties/allTasks | Creare ed eliminare groupSettings, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/allProperties/allTasks | Creare ed eliminare groupSettingsTemplates, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/loginTenantBranding/allProperties/allTasks | Creare ed eliminare loginTenantBranding, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/allProperties/allTasks | Creare ed eliminare oAuth2PermissionGrants, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/organization/allProperties/allTasks | Creare ed eliminare organizzazioni, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/policies/allProperties/allTasks | Creare ed eliminare criteri, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/allProperties/allTasks | Creare ed eliminare roleAssignments, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/allProperties/allTasks | Creare ed eliminare roleDefinitions, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/scopedRoleMemberships/allProperties/allTasks | Creare ed eliminare scopedRoleMemberships, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/serviceAction/activateService | Può eseguire l'azione del servizio Activateservice in Azure Active Directory |
| microsoft.aad.directory/serviceAction/disableDirectoryFeature | Può eseguire l'azione del servizio Disabledirectoryfeature in Azure Active Directory |
| microsoft.aad.directory/serviceAction/enableDirectoryFeature | Può eseguire l'azione del servizio Enabledirectoryfeature in Azure Active Directory |
| microsoft.aad.directory/serviceAction/getAvailableExtentionProperties | Può eseguire l'azione del servizio Getavailableextentionproperties in Azure Active Directory |
| microsoft.aad.directory/servicePrincipals/allProperties/allTasks | Creare ed eliminare servicePrincipals, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/allProperties/allTasks | Creare ed eliminare subscribedSkus, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/users/allProperties/allTasks | Creare ed eliminare utenti, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Eseguire tutte le azioni in Azure AD Connect. |
| microsoft.aad.identityProtection/allEntities/allTasks | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Eseguire tutte le risorse in microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.advancedThreatProtection/allEntities/read | Leggere tutte le risorse in microsoft.azure.advancedThreatProtection. |
| microsoft.azure.informationProtection/allEntities/allTasks | Gestisce tutti gli aspetti di Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.commerce.billing/allEntities/allTasks | Gestire tutti gli aspetti della fatturazione di Office 365. |
| microsoft.intune/allEntities/allTasks | Gestire tutti gli aspetti di Intune. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gestire tutti gli aspetti di Office 365 Compliance Manager |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gestire tutti gli aspetti di Desktop Analytics. |
| microsoft.office365.exchange/allEntities/allTasks | Gestire tutti gli aspetti di Exchange Online. |
| microsoft.office365.lockbox/allEntities/allTasks | Gestire tutti gli aspetti di Office 365 Customer Lockbox |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Leggere i messaggi relativi alla sicurezza in microsoft.office365.messageCenter. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Gestire tutti gli aspetti di Centro protezione di Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare le proprietà standard in microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gestire tutti gli aspetti di Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gestire tutti gli aspetti di Dynamics 365. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gestire tutti gli aspetti di Power BI. |
| microsoft.windows.defenderAdvancedThreatProtection/allEntities/read | Leggere tutte le risorse in microsoft.windows.defenderAdvancedThreatProtection. |

### <a name="compliance-administrator"></a>Amministratore di conformità
Può eseguire la lettura e gestire i report e la configurazione di conformità in Azure AD e Office 365.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gestire tutti gli aspetti di Office 365 Compliance Manager |
| microsoft.office365.exchange/allEntities/allTasks | Gestire tutti gli aspetti di Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gestire tutti gli aspetti di Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="conditional-access-administrator"></a>Amministratore di accesso condizionale
Può gestire le funzionalità di accesso condizionale.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/policies/conditionalAccess/basic/read | Eseguire la lettura della proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/basic/update | Aggiornare la proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/create | Creare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/read | Eseguire la lettura della proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/owners/update | Aggiornare la proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/policiesAppliedTo/read | Eseguire la lettura della proprietà policies.conditionalAccess in Azure Active Directory. |
| microsoft.aad.directory/policies/conditionalAccess/tenantDefault/update | Aggiornare la proprietà policies.conditionalAccess in Azure Active Directory. |

### <a name="crm-service-administrator"></a>Amministratore del servizio CRM
Può gestire tutti gli aspetti del prodotto Dynamics 365.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gestire tutti gli aspetti di Dynamics 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="customer-lockbox-access-approver"></a>Responsabile approvazione per l'accesso a Customer Lockbox
Può approvare le richieste di supporto Microsoft per l'accesso ai dati aziendali dei clienti.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.lockbox/allEntities/allTasks | Gestire tutti gli aspetti di Office 365 Customer Lockbox |

### <a name="device-administrators"></a>Amministratori di dispositivi
Gli utenti assegnati a questo ruolo vengono aggiunti al gruppo administrators locale in dispositivi aggiunti AD Azure.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/groupSettings/basic/read | Eseguire la lettura delle proprietà di base su groupSettings in Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Eseguire la lettura delle proprietà di base su groupSettingTemplates in Azure Active Directory. |

### <a name="directory-readers"></a>Ruoli con autorizzazioni di lettura nella directory
Può leggere le informazioni base della directory. Per concedere l'accesso alle applicazioni, non destinato agli utenti.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/administrativeUnits/basic/read | Eseguire la lettura delle proprietà di base su administrativeUnits in Azure Active Directory. |
| microsoft.aad.directory/administrativeUnits/members/read | Eseguire la proprietà administrativeUnits.members in Azure Active Directory. |
| microsoft.aad.directory/applications/basic/read | Eseguire la lettura delle proprietà di base sulle applicazioni in Azure Active Directory. |
| microsoft.aad.directory/applications/owners/read | Eseguire la lettura della proprietà applications.owners in Azure Active Directory. |
| microsoft.aad.directory/applications/policies/read | Eseguire la lettura della proprietà applications.policies in Azure Active Directory. |
| microsoft.aad.directory/contacts/basic/read | Eseguire la lettura delle proprietà di base sui contatti in Azure Active Directory. |
| microsoft.aad.directory/contacts/memberOf/read | Eseguire la lettura della proprietà contacts.memberOf in Azure Active Directory. |
| microsoft.aad.directory/contracts/basic/read | Eseguire la lettura delle proprietà di base sui contratti in Azure Active Directory. |
| microsoft.aad.directory/devices/basic/read | Eseguire la lettura delle proprietà di base sui dispositivi in Azure Active Directory. |
| microsoft.aad.directory/devices/memberOf/read | Eseguire la proprietà devices.memberOf in Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/read | Eseguire la proprietà devices.registeredOwners in Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/read | Eseguire la lettura della proprietà devices.registeredUsers in Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/basic/read | Eseguire la lettura delle proprietà di base su directoryRoles in Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/eligibleMembers/read | Eseguire la lettura della proprietà directoryRoles.eligibleMembers in Azure Active Directory. |
| microsoft.aad.directory/directoryRoles/members/read | Eseguire la lettura della proprietà directoryRoles.members in Azure Active Directory. |
| microsoft.aad.directory/domains/basic/read | Eseguire la lettura delle proprietà di base sui domini in Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/read | Eseguire la lettura della proprietà groups.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/groups/basic/read | Eseguire la lettura della proprietà sui gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/memberOf/read | Eseguire la lettura della proprietà groups.memberOf in Azure Active Directory. |
| microsoft.aad.directory/groups/members/read | Eseguire la lettura della proprietà groups.members in Azure Active Directory. |
| microsoft.aad.directory/groups/owners/read | Eseguire la lettura della proprietà groups.owners in Azure Active Directory. |
| microsoft.aad.directory/groups/settings/read | Eseguire la lettura della proprietà groups.settings in Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/read | Eseguire la lettura delle proprietà di base su groupSettings in Azure Active Directory. |
| microsoft.aad.directory/groupSettingTemplates/basic/read | Eseguire la lettura delle proprietà di base su groupSettingTemplates in Azure Active Directory. |
| microsoft.aad.directory/oAuth2PermissionGrants/basic/read | Eseguire la lettura delle proprietà di base su oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.aad.directory/organization/basic/read | Eseguire la lettura delle proprietà di base sull'organizzazione in Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/read | Eseguire la lettura della proprietà organization.trustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.aad.directory/roleAssignments/basic/read | Eseguire la lettura delle proprietà di base su roleAssignments in Azure Active Directory. |
| microsoft.aad.directory/roleDefinitions/basic/read | Eseguire la lettura delle proprietà di base su roleDefinitions in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Eseguire la lettura della proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Eseguire la lettura della proprietà servicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Eseguire la lettura delle proprietà di base su servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Eseguire la lettura della proprietà servicePrincipals.memberOf in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Eseguire la lettura della proprietà servicePrincipals.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Eseguire la lettura della proprietà servicePrincipals.ownedObjects in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Eseguire la lettura della proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Eseguire la lettura della proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.aad.directory/subscribedSkus/basic/read | Eseguire la lettura delle proprietà di base su subscribedSkus in Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/read | Eseguire la lettura della proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Eseguire la lettura delle proprietà di base sugli utenti in Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Eseguire la lettura della proprietà users.directReports in Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Eseguire la lettura della proprietà users.manager in Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Eseguire la lettura della proprietà users.memberOf in Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Eseguire la lettura della proprietà users.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Eseguire la lettura della proprietà users.ownedDevices in Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Eseguire la lettura della proprietà users.ownedObjects in Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Eseguire la lettura della proprietà users.registeredDevices in Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Account di sincronizzazione della directory
Usata solo dal servizio di Azure AD Connect.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/organization/dirSync/update | Aggiornare la proprietà organization.dirSync in Azure Active Directory. |
| microsoft.aad.directory/policies/create | Creare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/basic/read | Eseguire la lettura delle proprietà di base sui criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Aggiornare le proprietà di base sui criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/owners/read | Eseguire la lettura della proprietà policies.owners in Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Aggiornare la proprietà policies.owners in Azure Active Directory. |
| microsoft.aad.directory/policies/policiesAppliedTo/read | Eseguire la lettura della proprietà policies.policiesAppliedTo in Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Aggiornare la proprietà policies.tenantDefault in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/read | Eseguire la lettura della proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignedTo/update | Aggiornare la proprietà servicePrincipals.appRoleAssignedTo in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/read | Eseguire la lettura della proprietà servicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/appRoleAssignments/update | Aggiornare la proprietà servicePrincipals.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/audience/update | Aggiornare la proprietà servicePrincipals.audience in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/authentication/update | Aggiornare la proprietà servicePrincipals.authentication in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/read | Eseguire la lettura delle proprietà di base su servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/basic/update | Aggiornare le proprietà di base su servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/create | Creare servicePrincipals in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/credentials/update | Aggiornare la proprietà servicePrincipals.credentials in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/memberOf/read | Eseguire la lettura della proprietà servicePrincipals.memberOf in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/oAuth2PermissionGrants/basic/read | Eseguire la lettura della proprietà servicePrincipals.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/read | Eseguire la lettura della proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/owners/update | Aggiornare la proprietà servicePrincipals.owners in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/ownedObjects/read | Eseguire la lettura della proprietà servicePrincipals.ownedObjects in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/permissions/update | Aggiornare la proprietà servicePrincipals.permissions in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/read | Eseguire la lettura della proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Aggiornare la proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.aad.directorySync/allEntities/allTasks | Eseguire tutte le azioni in Azure AD Connect. |

### <a name="directory-writers"></a>Writer di directory
Può leggere e scrivere le informazioni base della directory. Per concedere l'accesso alle applicazioni, non destinato agli utenti.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/groups/create | Creare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Aggiornare la proprietà groups.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Aggiornare le proprietà di base sui gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Aggiornare la proprietà groups.settings in Azure Active Directory. |
| microsoft.aad.directory/groupSettings/basic/update | Aggiornare le proprietà di base su groupSettings in Azure Active Directory. |
| microsoft.aad.directory/groupSettings/create | Creare groupSettings in Azure Active Directory. |
| microsoft.aad.directory/groupSettings/delete | Eliminare groupSettings in Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Aggiornare la proprietà users.userPrincipalName in Azure Active Directory. |

### <a name="exchange-service-administrator"></a>Amministratore del servizio Exchange
Può gestire tutti gli aspetti del prodotto Exchange.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Aggiornare la proprietà groups.unified in Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Aggiornare le proprietà di base dei gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/create | Creare gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/delete | Eliminare gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Aggiornare l'appartenenza ai gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Aggiornare la proprietà dei gruppi di Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.exchange/allEntities/allTasks | Gestire tutti gli aspetti di Exchange Online. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="guest-inviter"></a>Mittente dell'invito guest
Può invitare utenti guest indipendentemente dall'impostazione 'I membri possono invitare utenti guest'.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/users/appRoleAssignments/read | Eseguire la lettura della proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/users/basic/read | Eseguire la lettura delle proprietà di base sugli utenti in Azure Active Directory. |
| microsoft.aad.directory/users/directReports/read | Eseguire la lettura della proprietà users.directReports in Azure Active Directory. |
| microsoft.aad.directory/users/inviteGuest | Invitare utenti guest in Azure Active Directory. |
| microsoft.aad.directory/users/manager/read | Eseguire la lettura della proprietà users.manager in Azure Active Directory. |
| microsoft.aad.directory/users/memberOf/read | Eseguire la lettura della proprietà users.memberOf in Azure Active Directory. |
| microsoft.aad.directory/users/oAuth2PermissionGrants/basic/read | Eseguire la lettura della proprietà users.oAuth2PermissionGrants in Azure Active Directory. |
| microsoft.aad.directory/users/ownedDevices/read | Eseguire la lettura della proprietà users.ownedDevices in Azure Active Directory. |
| microsoft.aad.directory/users/ownedObjects/read | Eseguire la lettura della proprietà users.ownedObjects in Azure Active Directory. |
| microsoft.aad.directory/users/registeredDevices/read | Eseguire la lettura della proprietà users.registeredDevices in Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Amministratore del supporto tecnico
Può reimpostare le password per gli utenti non amministratori e gli amministratori supporto tecnico.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="information-protection-administrator"></a>Amministratore di Information Protection
Può gestire tutti gli aspetti del prodotto Azure Information Protection.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.informationProtection/allEntities/allTasks | Gestisce tutti gli aspetti di Azure Information Protection. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="intune-service-administrator"></a>Amministratore del servizio Intune
Può gestire tutti gli aspetti del prodotto Intune.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Aggiornare le proprietà di base sui contatti in Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Creare contatti in Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Eliminare contatti in Azure Active Directory. |
| microsoft.aad.directory/devices/basic/update | Aggiornare le proprietà di base sui dispositivi in Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.aad.directory/devices/create | Creare dispositivi in Azure Active Directory. |
| microsoft.aad.directory/devices/delete | Eliminare dispositivi in Azure Active Directory. |
| microsoft.aad.directory/devices/registeredOwners/update | Aggiornare la proprietà devices.registeredOwners in Azure Active Directory. |
| microsoft.aad.directory/devices/registeredUsers/update | Aggiornare la proprietà devices.registeredUsers in Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Aggiornare la proprietà groups.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Aggiornare le proprietà di base sui gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/create | Creare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/groups/delete | Eliminare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Eseguire la lettura della proprietà groups.hiddenMembers in Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Ripristinare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Aggiornare la proprietà groups.settings in Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.intune/allEntities/allTasks | Gestire tutti gli aspetti di Intune. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |

### <a name="license-administrator"></a>Amministratore licenze
Possono gestire licenze dei prodotti per utenti e gruppi.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.aad.directory/users/usageLocation/update | Aggiornare la proprietà Users.UsageLocation in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="lync-service-administrator"></a>Amministratore del servizio Lync
Può gestire tutti gli aspetti del prodotto Skype for Business.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gestire tutti gli aspetti di Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="message-center-reader"></a>Ruolo con autorizzazioni di lettura per il Centro messaggi
Può eseguire la lettura di messaggi e aggiornamenti per la propria organizzazione solo nel Centro messaggi di Office 365. 

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |

### <a name="partner-tier1-support"></a>Supporto partner - Livello 1
Non usare: non destinato all'uso generale.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Aggiornare le proprietà di base sui contatti in Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Creare contatti in Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Eliminare contatti in Azure Active Directory. |
| microsoft.aad.directory/groups/create | Creare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/groups/members/update | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.aad.directory/users/delete | Eliminare utenti in Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.aad.directory/users/restore | Ripristinare gli utenti eliminati in Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Aggiornare la proprietà users.userPrincipalName in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="partner-tier2-support"></a>Supporto partner - Livello 2
Non usare: non destinato all'uso generale.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/contacts/basic/update | Aggiornare le proprietà di base sui contatti in Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Creare contatti in Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Eliminare contatti in Azure Active Directory. |
| microsoft.aad.directory/domains/allTasks | Creare ed eliminare domini, eseguire la lettura e aggiornare le proprietà standard in Azure Active Directory. |
| microsoft.aad.directory/groups/create | Creare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/delete | Eliminare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Ripristinare gruppi in Azure Active Directory. |
| microsoft.aad.directory/organization/basic/update | Aggiornare le proprietà di base sull'organizzazione in Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.aad.directory/users/delete | Eliminare utenti in Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.aad.directory/users/restore | Ripristinare gli utenti eliminati in Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Aggiornare la proprietà users.userPrincipalName in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="power-bi-service-administrator"></a>Amministratore del servizio Power BI
Può gestire tutti gli aspetti del prodotto Power BI.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gestire tutti gli aspetti di Power BI. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="privileged-authentication-administrator"></a>Amministratore dell'autenticazione con privilegi
È autorizzato a visualizzare, configurare e reimpostare le informazioni sul metodo di autenticazione per qualsiasi utente (amministratore o non amministratore).

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/users/strongAuthentication/update | Aggiornare le proprietà dell'autenticazione avanzata, ad esempio le informazioni sulle credenziali MFA. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="privileged-role-administrator"></a>Amministratore dei ruoli con privilegi
Può gestire le assegnazioni di ruolo in Azure Active Directory e tutti gli aspetti di Privileged Identity Management.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/directoryRoles/update | Aggiornare directoryRoles in Azure Active Directory. |
| microsoft.aad.privilegedIdentityManagement/allEntities/allTasks | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in microsoft.aad.privilegedIdentityManagement. |

### <a name="reports-reader"></a>Lettore di report
Può eseguire la lettura dei report di accesso e di controllo.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |

### <a name="security-administrator"></a>Amministratore della sicurezza
Può eseguire la lettura dei report e delle informazioni di sicurezza e gestire la configurazione in Azure Active Directory e Office 365.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/applications/policies/update | Aggiornare la proprietà applications.policies in Azure Active Directory. |
| microsoft.aad.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.aad.directory/policies/basic/update | Aggiornare le proprietà di base sui criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/create | Creare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/delete | Eliminare criteri in Azure Active Directory. |
| microsoft.aad.directory/policies/owners/update | Aggiornare la proprietà policies.owners in Azure Active Directory. |
| microsoft.aad.directory/policies/tenantDefault/update | Aggiornare la proprietà policies.tenantDefault in Azure Active Directory. |
| microsoft.aad.directory/servicePrincipals/policies/update | Aggiornare la proprietà servicePrincipals.policies in Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Eseguire la lettura di tutte le risorse in microsoft.aad.identityProtection. |
| microsoft.aad.identityProtection/allEntities/update | Aggiornare tutte le risorse in microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Eseguire tutte le risorse in microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Eseguire la lettura di tutti gli aspetti del Centro protezione Office 365. |
| microsoft.office365.protectionCenter/allEntities/update | Aggiornare tutte le risorse in microsoft.office365.protectionCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="security-reader"></a>Ruolo con autorizzazioni di lettura per la sicurezza
Può eseguire la lettura dei report e delle informazioni di sicurezza in Azure AD e Office 365.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/auditLogs/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per auditLogs in Azure Active Directory. |
| microsoft.aad.directory/devices/bitLockerRecoveryKeys/read | Leggere la proprietà devices.bitLockerRecoveryKeys in Azure Active Directory. |
| microsoft.aad.directory/signInReports/allProperties/read | Leggere tutte le proprietà (incluse quelle con privilegi) per signInReports in Azure Active Directory. |
| microsoft.aad.identityProtection/allEntities/read | Eseguire la lettura di tutte le risorse in microsoft.aad.identityProtection. |
| microsoft.aad.privilegedIdentityManagement/allEntities/read | Eseguire tutte le risorse in microsoft.aad.privilegedIdentityManagement. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.protectionCenter/allEntities/read | Eseguire la lettura di tutti gli aspetti del Centro protezione Office 365. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="service-support-administrator"></a>Amministratore del supporto servizio
Può eseguire la lettura delle informazioni di integrità dei servizi e gestire i ticket di supporto.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="sharepoint-service-administrator"></a>Amministratore del servizio SharePoint
Può gestire tutti gli aspetti del servizio SharePoint.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Aggiornare la proprietà groups.unified in Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Aggiornare le proprietà di base dei gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/create | Creare gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/delete | Eliminare gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Aggiornare l'appartenenza ai gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Aggiornare la proprietà dei gruppi di Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in microsoft.office365.sharepoint. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

### <a name="teams-communications-administrator"></a>Amministratore comunicazioni Teams
Può gestire le funzionalità per chiamate e riunioni all'interno del servizio Microsoft Teams.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |

### <a name="teams-communications-support-engineer"></a>Tecnico di supporto comunicazioni Teams
Può risolvere i problemi di comunicazione all'interno di Teams tramite strumenti avanzati.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="teams-communications-support-specialist"></a>Specialista di supporto comunicazioni Teams
Può risolvere i problemi di comunicazione all'interno di Teams tramite strumenti di base.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="teams-service-administrator"></a>Amministratore del servizio Teams
Può gestire il servizio Microsoft Teams.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/groups/hiddenMembers/read | Eseguire la lettura della proprietà groups.hiddenMembers in Azure Active Directory. |
| microsoft.aad.directory/groups/unified/appRoleAssignments/update | Aggiornare la proprietà groups.unified in Azure Active Directory. |
| microsoft.aad.directory/groups/unified/basic/update | Aggiornare le proprietà di base dei gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/create | Creare gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/delete | Eliminare gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/members/update | Aggiornare l'appartenenza ai gruppi di Office 365. |
| microsoft.aad.directory/groups/unified/owners/update | Aggiornare la proprietà dei gruppi di Office 365. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |

### <a name="user-administrator"></a>Amministratore utenti
Può gestire tutti gli aspetti di utenti e gruppi, inclusa la reimpostazione delle password per gli amministratori con limitazioni.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/appRoleAssignments/create | Creare appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/delete | Eliminare appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/appRoleAssignments/update | Aggiornare appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/contacts/basic/update | Aggiornare le proprietà di base sui contatti in Azure Active Directory. |
| microsoft.aad.directory/contacts/create | Creare contatti in Azure Active Directory. |
| microsoft.aad.directory/contacts/delete | Eliminare contatti in Azure Active Directory. |
| microsoft.aad.directory/groups/appRoleAssignments/update | Aggiornare la proprietà groups.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/groups/basic/update | Aggiornare le proprietà di base sui gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/create | Creare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/createAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/groups/delete | Eliminare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/hiddenMembers/read | Eseguire la lettura della proprietà groups.hiddenMembers in Azure Active Directory. |
| microsoft.aad.directory/groups/members/update | Aggiornare la proprietà groups.members in Azure Active Directory. |
| microsoft.aad.directory/groups/owners/update | Aggiornare la proprietà groups.owners in Azure Active Directory. |
| microsoft.aad.directory/groups/restore | Ripristinare gruppi in Azure Active Directory. |
| microsoft.aad.directory/groups/settings/update | Aggiornare la proprietà groups.settings in Azure Active Directory. |
| microsoft.aad.directory/users/appRoleAssignments/update | Aggiornare la proprietà users.appRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/users/assignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.aad.directory/users/basic/update | Aggiornare le proprietà di base su utenti in Azure Active Directory. |
| microsoft.aad.directory/users/create | Creare utenti in Azure Active Directory. |
| microsoft.aad.directory/users/delete | Eliminare utenti in Azure Active Directory. |
| microsoft.aad.directory/users/invalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/users/manager/update | Aggiornare la proprietà users.manager in Azure Active Directory. |
| microsoft.aad.directory/users/password/update | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.aad.directory/users/restore | Ripristinare gli utenti eliminati in Azure Active Directory. |
| microsoft.aad.directory/users/userPrincipalName/update | Aggiornare la proprietà users.userPrincipalName in Azure Active Directory. |
| microsoft.azure.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Azure. |
| microsoft.azure.supportTickets/allEntities/allTasks | Creare e gestire i ticket di supporto tecnico di Azure. |
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |

## <a name="role-template-ids"></a>ID di modello di ruolo

ID di modello di ruolo vengono utilizzati soprattutto dagli utenti di API Graph o PowerShell.

DisplayName di Graph | Nome visualizzato del portale di Azure | directoryRoleTemplateId
----------------- | ------------------------- | -------------------------
Amministratore di applicazioni | Amministratore di applicazioni | 9B895D92-2CD3-44C7-9D02-A6AC2D5EA5C3
Sviluppatore di applicazioni | Sviluppatore di applicazioni | CF1C38E5-3621-4004-A7CB-879624DCED7C
Amministratore fatturazione | Amministratore fatturazione | b0f54661-2d74-4c50-afa3-1ec803f12efe
Amministratore di Desktop Analytics | Amministratore di Desktop Analytics | 38a96431-2bdf-4b4c-8b6e-5d3d8abac1a4
Amministratore applicazione cloud | Amministratore di applicazioni cloud | 158c047a-c907-4556-b7ef-446551a6b5f7
Amministratore dispositivo cloud | Amministratore dispositivo cloud | 7698a772-787b-4ac8-901f-60d6b08affd2
Amministratore società | Amministratore globale | 62e90394-69f5-4237-9190-012177145e10
Amministratore di conformità | Amministratore di conformità | 17315797-102d-40b4-93e0-432062caca18
Amministratore di accesso condizionale | Amministratore di accesso condizionale | b1be1c3e-b65d-4f19-8427-f6fa0d97feb9
Amministratore del servizio CRM | Amministratore di Dynamics 365 | 44367163-eba1-44c3-98af-f5787879f96a
Responsabile approvazione per l'accesso a Customer Lockbox | Responsabile approvazione per l'accesso a Customer Lockbox | 5c4f9dcd-47dc-4cf7-8c9a-9e4207cbfc91
Amministratori di dispositivi | Amministratori di dispositivi | 9f06204d-73c1-4d4c-880a-6edb90606fd8
Aggiunta di dispositivi | Aggiunta di dispositivi | 9c094953-4995-41c8-84c8-3ebb9b32c93f
Gestione dispositivi | Gestione dispositivi | 2b499bcd-da44-4968-8aec-78e1674fa64d
Utenti di dispositivi | Utenti dei dispositivi | d405c6df-0af8-4e3b-95e4-4d06e542189e
Ruoli con autorizzazioni di lettura nella directory | Ruoli con autorizzazioni di lettura nella directory | 88d8e3e3-8f55-4a1e-953a-9b9898b8876b
Account di sincronizzazione della directory | Account di sincronizzazione della directory | d29b2b05-8046-44ba-8758-1e26182fcf32
Writer di directory | Writer di directory | 9360feb5-f418-4baa-8175-e2a00bac4301
Amministratore del servizio Exchange | Amministratore di Exchange | 29232cdf-9323-42fd-ade2-1d097af3e4de
Mittente dell'invito guest | Mittente dell'invito guest | 95e79109-95c0-4d8e-aee3-d01accf2d47b
Amministratore del supporto tecnico | Amministratore password | 729827e3-9c14-49f7-bb1b-9608f156bbb8
Amministratore di Information Protection | Amministratore di Information Protection | 7495fdc4-34c4-4d15-a289-98788ce399fd
Amministratore del servizio Intune | Amministratore di Intune | 3a2c62db-5318-420d-8d74-23affee5d9d5
Amministratore licenze | Amministratore licenze | 4d6ac14f-3453-41d0-bef9-a3e0c569773a
Amministratore del servizio Lync | Amministratore di Skype for Business | 75941009-915a-4869-abe7-691bff18279e
Ruolo con autorizzazioni di lettura per il Centro messaggi | Ruolo con autorizzazioni di lettura per il Centro messaggi | 790c1fb9-7f7d-4f88-86a1-ef1f95c05c1b
Supporto partner - Livello 1 | Supporto partner - Livello 1 | 4ba39ca4-527c-499a-b93d-d9b492c50246
Supporto partner - Livello 2 | Supporto partner - Livello 2 | e00e864a-17c5-4a4b-9c06-f5b95a8d5bd8
Amministratore del servizio Power BI | Amministratore di Power BI | a9ea8996-122f-4c74-9520-8edcd192826c
Amministratore dei ruoli con privilegi | Amministratore dei ruoli con privilegi | e8611ab8-c189-46e8-94e1-60213ab1f814
Lettore di report | Lettore di report | 4a5d8f65-41da-4de4-8968-e035b65339cf
Amministratore della sicurezza | Amministratore della sicurezza | 194ae4cb-b126-40b2-bd5b-6091b380977d
Ruolo con autorizzazioni di lettura per la sicurezza | Ruolo con autorizzazioni di lettura per la sicurezza | 5d6b6bb7-de71-4623-b4af-96380a352509
Amministratore del supporto servizio | Amministratore del servizio | f023fd81-a637-4b56-95fd-791ac0226033
Amministratore del servizio SharePoint | Amministratore di SharePoint | f28a1f50-f6e7-4571-818b-6a12f2af6b6c
Amministratore comunicazioni Teams | Amministratore comunicazioni Teams | baf37b3a-610e-45da-9e62-d9d1e5e8914b
Tecnico di supporto comunicazioni Teams | Tecnico di supporto comunicazioni Teams | f70938a0-fc10-4177-9e90-2178f8765737
Specialista di supporto comunicazioni Teams | Specialista di supporto comunicazioni Teams | fcf91098-03e3-41a9-b5ba-6f0ec8188a12
Amministratore del servizio Teams | Amministratore del servizio Teams | 69091246-20e8-4a56-aa4d-066075b2a7a8
Utente | Utente | a0b1b346-4d3e-4e8b-98f8-753987be4970
Amministratore account utente | Amministratore utenti | fe930be7-5e62-47db-91af-98c3a49a38b1
Aggiunta di dispositivi all'area di lavoro | Aggiunta di dispositivi | c34f683f-4d5a-4403-affd-6615e00e3a7f


## <a name="deprecated-roles"></a>Ruoli deprecati

I ruoli seguenti non devono essere usati. Questi ruoli sono stati deprecati e verranno rimossi da Azure Active Directory in futuro.

* Amministratore di licenze ad hoc
* Aggiunta di dispositivi
* Gestione dispositivi
* Utenti di dispositivi
* Autore di utenti verificati tramite posta elettronica
* Amministratore della cassetta postale
* Aggiunta di dispositivi all'area di lavoro

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su come assegnare un utente come amministratore di una sottoscrizione di Azure, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../../role-based-access-control/role-assignments-portal.md)
* Per altre informazioni sul modo in cui l'accesso alle risorse viene controllato in Microsoft Azure, vedere [Informazioni sull'accesso alle risorse in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Per altre informazioni sul modo in cui Azure Active Directory è correlato alla sottoscrizione di Azure, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
