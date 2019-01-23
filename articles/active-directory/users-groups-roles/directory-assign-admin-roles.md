---
title: Autorizzazioni del ruolo di amministratore in Azure Active Directory | Microsoft Docs
description: Il ruolo di amministratore consente di aggiungere utenti, assegnare ruoli amministrativi, reimpostare le password utente, gestire le licenze utente o gestire i domini.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 01/16/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 50fbafdf714e6071ffac6f9f18d0802ecc1e574d
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2019
ms.locfileid: "54360055"
---
# <a name="administrator-role-permissions-in-azure-active-directory"></a>Autorizzazioni del ruolo di amministratore in Azure Active Directory

Azure Active Directory (Azure AD) consente di designare amministratori diversi per le diverse funzioni. Per eseguire attività come l'aggiunta o la modifica di utenti, l’assegnazione di ruoli amministrativi, la reimpostazione delle password utente, la gestione delle licenze utente e dei nomi di dominio, è possibile designare gli amministratori nel portale di Azure AD.

L'amministratore globale ha accesso a tutte le funzionalità amministrative. Per impostazione predefinita, la persona che effettua l'iscrizione per una sottoscrizione di Azure riceve il ruolo Amministratore globale per la directory. Solo gli amministratori globali e gli amministratori dei ruoli con privilegi possono delegare i ruoli di amministratore.

## <a name="assign-or-remove-administrator-roles"></a>Assegnare o rimuovere ruoli di amministratore

Per informazioni su come assegnare ruoli amministrativi a un utente in Azure Active Directory, vedere [Visualizzare e assegnare i ruoli di amministratore in Azure Active Directory](directory-manage-roles-portal.md).

## <a name="available-roles"></a>Ruoli disponibili

Sono disponibili i ruoli di amministratore seguenti:

* **[Amministratore di applicazioni](#application-administrator)**: gli utenti in questo ruolo possono creare e gestire tutti gli aspetti delle applicazioni aziendali, le registrazioni delle applicazioni e le impostazioni proxy dell'applicazione. Questo ruolo concede inoltre la possibilità consentire le autorizzazioni delegate e le autorizzazioni dell'applicazione escluse Microsoft Graph e Azure AD Graph. I membri di questo ruolo non vengono aggiunti come proprietari durante la creazione di nuove registrazioni di applicazione o nelle applicazioni aziendali.

  <b>Importante</b>: questo ruolo concede la possibilità di gestire le credenziali delle applicazioni. Gli utenti assegnati a questo ruolo possono aggiungere credenziali a un'applicazione e usarle per rappresentare l'identità dell'applicazione. Se all'identità dell'applicazione è concesso l'accesso ad Azure Active Directory, con la conseguente possibilità, ad esempio, di creare o aggiornare utenti o altri oggetti, un utente assegnato a questo ruolo potrà eseguire tali azioni mentre rappresenta l'applicazione. La possibilità di rappresentare l'identità dell'applicazione potrebbe costituire un'elevazione dei privilegi rispetto alle azioni che l'utente può eseguire tramite le assegnazioni dei ruoli in Azure Active Directory. È importante comprendere che assegnare a un utente il ruolo di Amministratore di applicazioni gli concede la possibilità di rappresentare l'identità di un'applicazione.

* **[Sviluppatore di applicazioni](#application-developer)**: gli utenti in questo ruolo possono creare registrazioni di applicazioni quando l'opzione "Gli utenti possono registrare applicazioni" è impostata su No. Questo ruolo consente inoltre ai membri di fornire il consenso per proprio conto quando l'opzione "Users can consent to apps accessing company data on their behalf" (Gli utenti possono consentire alle app l'accesso ai dati della società per loro conto) è impostata su No. I membri di questo ruolo vengono aggiunti come proprietari durante la creazione di nuove registrazioni di applicazione o nelle applicazioni aziendali.

* **[Amministratore fatturazione](#billing-administrator)**: può effettuare acquisti, gestire le sottoscrizioni e i ticket di supporto e monitorare l'integrità dei servizi.

* **[Amministratore applicazione cloud](#cloud-application-administrator)**: gli utenti in questo ruolo hanno le stesse autorizzazioni del ruolo di amministratore di applicazioni, esclusa la possibilità di gestire il proxy dell'applicazione. Questo ruolo concede la possibilità di creare e gestire tutti gli aspetti delle applicazioni aziendali e delle registrazioni dell'applicazione. Questo ruolo concede inoltre la possibilità consentire le autorizzazioni delegate e le autorizzazioni dell'applicazione escluse Microsoft Graph e Azure AD Graph. I membri di questo ruolo non vengono aggiunti come proprietari durante la creazione di nuove registrazioni di applicazione o nelle applicazioni aziendali.

  <b>Importante</b>: questo ruolo concede la possibilità di gestire le credenziali delle applicazioni. Gli utenti assegnati a questo ruolo possono aggiungere credenziali a un'applicazione e usarle per rappresentare l'identità dell'applicazione. Se all'identità dell'applicazione è concesso l'accesso ad Azure Active Directory, con la conseguente possibilità, ad esempio, di creare o aggiornare utenti o altri oggetti, un utente assegnato a questo ruolo potrà eseguire tali azioni mentre rappresenta l'applicazione. La possibilità di rappresentare l'identità dell'applicazione potrebbe costituire un'elevazione dei privilegi rispetto alle azioni che l'utente può eseguire tramite le assegnazioni dei ruoli in Azure Active Directory. È importante comprendere che assegnare a un utente il ruolo di Amministratore applicazione cloud gli concede la possibilità di rappresentare l'identità di un'applicazione.

* **[Amministratore dispositivo cloud](#cloud-device-administrator)**: gli utenti con questo ruolo possono abilitare, disabilitare ed eliminare dispositivi in Azure AD, nonché leggere chiavi BitLocker di Windows 10 (se presenti) nel portale di Azure. Il ruolo non concede le autorizzazioni per gestire eventuali altre proprietà nel dispositivo.

* **[Amministratore di conformità](#compliance-administrator)**: Gli utenti con questo ruolo hanno le autorizzazioni necessarie per gestire le funzionalità relative alla conformità nel Centro conformità di Microsoft 365, nel Centro sicurezza di Microsoft 365, nell'interfaccia di amministrazione di Microsoft 365 e in Azure. Gli utenti possono anche gestire tutte le funzionalità all'interno dell'interfaccia di amministrazione di Exchange, in Compliance Manager, nell'interfaccia di amministrazione di Teams e Skype for Business e creare ticket di supporto per Azure e Microsoft 365. Per altre informazioni, vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d). 


* **[Amministratore dell'accesso condizionale](#conditional-access-administrator)**: gli utenti con questo ruolo possono gestire le impostazioni di accesso condizionale di Azure Active Directory.
  > [!NOTE]
  > Per distribuire il criterio di accesso condizionale di Exchange ActiveSync in Azure, l'utente deve essere anche amministratore globale.
  
* **[Amministratori di dispositivi](#device-administrators)**: questo ruolo è disponibile per l'assegnazione solo come amministratore locale aggiuntivo in [Impostazioni dispositivo](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Gli utenti con questo ruolo diventano amministratori del computer locale in tutti i dispositivi Windows 10 aggiunti ad Azure Active Directory. Non possono gestire gli oggetti di dispositivi in Azure Active Directory. 

* **[Ruoli con autorizzazioni di lettura nella directory](#directory-readers)**: si tratta di un ruolo legacy che deve essere assegnato alle applicazioni che non supportano il [framework di consenso](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Non deve essere assegnato agli utenti.

* **[Account di sincronizzazione della directory](#directory-synchronization-accounts)**: Non usare. Questo ruolo viene assegnato automaticamente al servizio Azure AD Connect e non è progettato o supportato per altri usi.

* **[Ruoli con autorizzazioni di scrittura nella directory](#directory-writers)**: si tratta di un ruolo legacy che deve essere assegnato alle applicazioni che non supportano il [framework di consenso](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Non deve essere assegnato agli utenti.

* **[Amministratore di Dynamics 365/Amministratore di CRM](#dynamics-365-administrator)**: gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Dynamics 365 Online, quando il servizio è presente, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità dei servizi. Altre informazioni sono disponibili in [Utilizzare il ruolo di amministratore del servizio per gestire il tenant](https://docs.microsoft.com/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).
  > [!NOTE] 
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Dynamics 365". È l'"Amministratore di Dynamics 365" nel portale di Azure.

* **[Amministratore di Exchange](#exchange-administrator)**: gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Exchange Online, quando il servizio è presente. Possono inoltre creare e gestire tutti i gruppi di Office 365, gestire i ticket di supporto e monitorare l'integrità del servizio. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Exchange". È l'"Amministratore di Exchange" nel portale di Azure.

* **[Amministratore globale/Amministratore società](#company-administrator)**: gli utenti con questo ruolo hanno accesso a tutte le funzionalità amministrative in Azure Active Directory, nonché ai servizi che usano identità di Azure Active Directory come Exchange Online, SharePoint Online e Skype for Business Online. La persona che effettua l'iscrizione per il tenant di Azure Active Directory diventa amministratore globale. Solo gli amministratori globali possono assegnare altri ruoli di amministratore. In una società possono essere presenti più amministratori globali. Gli amministratori globali possono reimpostare la password per qualsiasi utente e per tutti gli altri amministratori.

  > [!NOTE]
  > In Microsoft API Graph, Azure AD API Graph e Azure AD PowerShell, questo ruolo è identificato come "Amministratore società". È l'"amministratore globale" nel [portale di Azure](https://portal.azure.com).
  >
  >

* **[Mittente dell'invito guest](#guest-inviter)**: gli utenti con questo ruolo possono gestire gli inviti per gli utenti guest di Azure Active Directory B2B quando il valore dell'impostazione utente **I membri possono invitare** è No. Altre informazioni sulla collaborazione B2B in [Informazioni su Collaborazione B2B di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Il ruolo non include altre autorizzazioni.

* **[Amministratore di Information Protection](#information-protection-administrator)**: gli utenti con questo ruolo hanno tutte le autorizzazioni nel servizio Azure Information Protection. Questo ruolo consente di configurare etichette per i criteri di Azure Information Protection, gestire i modelli di protezione e attivare la protezione. Il ruolo tuttavia non concede le autorizzazioni relative a Identity Protection Center, Privileged Identity Management, Centro sicurezza e conformità di Office 365 e al servizio di monitoraggio dell'integrità dei servizi di Office 365.

* **[Amministratore di Intune](#intune-administrator)**: gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Intune Online, quando il servizio è presente. Inoltre questo ruolo implica la possibilità di gestire utenti e dispositivi per associare i criteri, nonché creare e gestire gruppi. Altre informazioni sono disponibili in [Controllo degli accessi in base al ruolo (RBAC) con Microsoft Intune](https://docs.microsoft.com/intune/role-based-access-control)
  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Intune". È l'"Amministratore di Intune" nel portale di Azure.

* **[Amministratore licenze](#license-administrator)**: gli utenti in questo ruolo possono aggiungere, rimuovere e aggiornare le assegnazioni di licenze a utenti, gruppi (usando licenze basate su gruppi) e gestire i percorsi di utilizzo per gli utenti. Il ruolo non garantisce la possibilità di acquistare o gestire sottoscrizioni, creare o gestire gruppi o creare o gestire utenti al di fuori del percorso di utilizzo.

* **[Ruolo con autorizzazioni di lettura per il Centro messaggi](#message-center-reader)**: gli utenti in questo ruolo possono monitorare le notifiche e gli aggiornamenti dello stato di integrità nel [Centro messaggi di Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) per la propria organizzazione sui servizi configurati, ad esempio Exchange, Intune e Microsoft Teams. Il Ruolo con autorizzazioni di lettura per il Centro messaggi riceve settimanalmente digest di posta elettronica di post, aggiornamenti e può condividere i post del centro messaggi in Office 365. In Azure AD, gli utenti assegnati a questo ruolo avranno solo l'accesso di sola lettura ai servizi di Azure AD, ad esempio utenti e gruppi. 

* **[Supporto partner - Livello 1](#partner-tier1-support)**: Non usare. Questo ruolo è deprecato e verrà rimosso da Azure AD in futuro. Il ruolo è riservato a pochi partner Microsoft per la rivendita e non è disponibile per un uso generale.

* **[Supporto partner - Livello 2](#partner-tier2-support)**: Non usare. Questo ruolo è deprecato e verrà rimosso da Azure AD in futuro. Il ruolo è riservato a pochi partner Microsoft per la rivendita e non è disponibile per un uso generale.

* **[Amministratore password/Amministratore supporto tecnico](#helpdesk-administrator)**: gli utenti con questo ruolo possono modificare le password, invalidare i token di aggiornamento, gestire le richieste di servizio e monitorare l'integrità dei servizi. Invalidando un token di aggiornamento si impone all'utente di eseguire di nuovo l'accesso. Gli utenti con ruolo di Amministratore supporto tecnico possono reimpostare le password e invalidare i token di aggiornamento di altri utenti non amministratori o membri soltanto dei ruoli seguenti:
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
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore supporto tecnico". È l'"Amministratore password" nel [portale di Azure](https://portal.azure.com/).
  >
  
* **[Amministratore di Power BI](#power-bi-administrator)**: gli utenti con questo ruolo hanno autorizzazioni globali in Microsoft Power BI, quando è presente il servizio, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità dei servizi. Per altre informazioni, vedere [Informazioni sul ruolo di amministratore di Power BI](https://docs.microsoft.com/power-bi/service-admin-role).
  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Power BI". È l'"Amministratore di Power BI" nel portale di Azure.

* **[Amministratore dei ruoli con privilegi](#privileged-role-administrator)**: gli utenti con questo ruolo possono gestire le assegnazioni di ruolo in Azure Active Directory e in Azure AD Privileged Identity Management. Inoltre, questo ruolo consente la gestione di tutti gli aspetti di Privileged Identity Management.

  <b>Importante</b>: questo ruolo concede la possibilità di gestire l'appartenenza a tutti i ruoli di Azure Active Directory, incluso il ruolo di Amministratore globale. Non prevede altre capacità con privilegi in Azure Active Directory, ad esempio la creazione o l'aggiornamento degli utenti. Tuttavia, gli utenti assegnati a questo ruolo possono concedere a se stessi o ad altri utenti privilegi aggiuntivi, perché possono assegnare ruoli aggiuntivi.

* **[Lettore di report](#reports-reader)**: gli utenti con questo ruolo possono visualizzare i dati dei report sull'utilizzo e il dashboard dei report nell'interfaccia di amministrazione di Office 365 e in Adoption Content Pack in Power BI. Il ruolo consente anche l'accesso ai report e alle attività di accesso in Azure Active Directory e ai dati restituiti dall'API di creazione report di Microsoft Graph. Un utente assegnato al ruolo di lettore di report può accedere solo alle metriche rilevanti per utilizzo e adozione. Questi utenti non hanno le autorizzazioni di amministratore per configurare le impostazioni o accedere alle interfacce di amministrazione specifiche di prodotti come Exchange. 

* **[Amministratore della sicurezza](#security-administrator)**: gli utenti con questo ruolo hanno tutte le autorizzazioni di sola lettura del Ruolo con autorizzazioni di lettura per la sicurezza, oltre alla possibilità di gestire la configurazione per i servizi correlati alla sicurezza: Azure Active Directory Identity Protection, Azure Information Protection e Centro sicurezza e conformità di Office 365. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [Autorizzazioni nel Centro sicurezza e conformità di Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  | In ingresso | Operazione consentita |
  | --- | --- |
  | Centro di Identity Protection |<ul><li>Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza.<li>Inoltre, è possibile eseguire tutte le operazioni IPC, ad eccezione della reimpostazione delle password. |
  | Privileged Identity Management |<ul><li>Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza.<li>**Non consente** di gestire le impostazioni o le appartenenze ai ruoli di Azure AD. |
  | <p>Monitorare l'integrità del servizio Office 365</p><p>Centro sicurezza e conformità di Office 365 |<ul><li>Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza.<li>Consente di configurare tutte le impostazioni della funzionalità di protezione da minacce avanzate (protezione da malware e virus, configurazione URL dannosi, traccia di URL e così via). |
  
* **[Ruolo con autorizzazioni di lettura per la sicurezza](#security-reader)**: gli utenti con questo ruolo hanno un accesso globale in sola lettura, incluse tutte le informazioni in Azure Active Directory, Privileged Identity Management, nonché la possibilità di leggere i report di accesso e i log di controllo di Azure Active Directory. Il ruolo concede inoltre l'autorizzazione di sola lettura nel Centro sicurezza e conformità di Office 365. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [Autorizzazioni nel Centro sicurezza e conformità di Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  | In ingresso | Operazione consentita |
  | --- | --- |
  | Centro di Identity Protection |Leggere tutte le informazioni sulle impostazioni e sui report di sicurezza per le funzionalità di sicurezza<ul><li>Filtro posta indesiderata<li>Crittografia<li>Prevenzione della perdita dei dati<li>Antimalware<li>Protezione avanzata dalle minacce<li>Anti-phishing<li>Regole del flusso di posta |
  | Privileged Identity Management |<p>Ha accesso in sola lettura a tutte le informazioni presentate in Azure AD Privileged Identity Management: criteri e report per le assegnazioni di ruoli in Azure AD, verifiche della sicurezza e in futuro accesso in lettura ai report e ai dati dei criteri per gli scenari in aggiunta all'assegnazione di ruoli in Azure AD.<p>**Non può** eseguire l'iscrizione ad Azure AD PIM o apportarvi modifiche. Nel portale di PIM o tramite PowerShell un utente in questo ruolo può attivare ruoli aggiuntivi, ad esempio amministratore globale o amministratore di ruoli con privilegi, se l'utente è candidato per questi ruoli. |
  | <p>Monitorare l'integrità del servizio Office 365</p><p>Centro sicurezza e conformità di Office 365</p> |<ul><li>Leggere e gestire gli avvisi<li>Leggere i criteri di sicurezza<li>Leggere i dati di intelligence sulle minacce, usare Cloud App Discovery e mettere in quarantena in ricerca e analisi<li>Leggere tutti i report |

* **[Amministratore servizio di supporto](#service-support-administrator)**: gli utenti con questo ruolo possono inviare a Microsoft richieste di supporto per i servizi di Azure e Office 365, nonché visualizzare il dashboard del servizio e il centro messaggi nel portale di Azure e nell'interfaccia di amministrazione di Office 365. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Amministratore di SharePoint](#sharepoint-administrator)**: gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft SharePoint Online, quando il servizio è presente, nonché la possibilità creare e gestire tutti i gruppi di Office 365, di gestire i ticket di supporto e monitorare l'integrità del servizio. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).
  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio SharePoint". È l'"Amministratore di SharePoint" nel portale di Azure.

* **[Amministratore di Skype for Business o Lync](#skype-for-business-administrator)**: gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft Skype for Business, quando il servizio è presente, nonché la possibilità di gestire gli attributi specifici per Skype in Azure Active Directory. Inoltre, questo ruolo garantisce la possibilità di gestire i ticket di supporto e monitorare l'integrità del servizio, oltre ad accedere a Teams e all'interfaccia di amministrazione di Skype for Business. L'account deve anche avere una licenza per Teams. In caso contrario, non può eseguire i cmdlet di PowerShell per Teams. Per altre informazioni, vedere [Amministratore Skype for Business Online](https://support.office.com/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5). Per informazioni sulle licenze per Teams, vedere [Licenze per i componenti aggiuntivi Skype for Business e Microsoft Teams](https://docs.microsoft.com/skypeforbusiness/skype-for-business-and-microsoft-teams-add-on-licensing/skype-for-business-and-microsoft-teams-add-on-licensing)

  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del sevizio Lync". È l'"Amministratore di Skype for Business" nel [portale di Azure](https://portal.azure.com/).

* **[Amministratore comunicazioni Teams](#teams-communications-administrator)**: gli utenti con questo ruolo possono gestire gli aspetti del carico di lavoro Microsoft Teams correlati a voce e telefonia. Sono inclusi gli strumenti di gestione per l'assegnazione di numeri di telefono, i criteri per chiamate vocali e riunioni, nonché l'accesso completo al set di strumenti di analisi delle chiamate.

* **[Tecnico di supporto comunicazioni Teams](#teams-communications-support-engineer)**: gli utenti con questo ruolo possono risolvere i problemi di comunicazione all'interno di Microsoft Teams e Skype for Business tramite gli strumenti di risoluzione dei problemi relativi alle chiamate utente nell'interfaccia di amministrazione di Microsoft Teams e Skype for Business. Gli utenti in questo ruolo possono visualizzare informazioni complete sui record delle chiamate per tutti i partecipanti coinvolti.

* **[Specialista di supporto comunicazioni Teams](#teams-communications-support-specialist)**: gli utenti con questo ruolo possono risolvere i problemi di comunicazione all'interno di Microsoft Teams e Skype for Business tramite gli strumenti di risoluzione dei problemi relativi alle chiamate utente nell'interfaccia di amministrazione di Microsoft Teams e Skype for Business. Gli utenti con questo ruolo possono visualizzare i dettagli dell'utente nella chiamata per l'utente specifico cercato.

* **[Amministratore di Teams](#teams-administrator)**: gli utenti in questo ruolo possono gestire tutti gli aspetti del carico di lavoro Microsoft Teams tramite l'interfaccia di amministrazione di Microsoft Teams e Skype for Business e i rispettivi moduli di PowerShell. Sono inclusi, tra le altre aree, tutti gli strumenti di gestione correlati a telefonia, messaggistica, riunioni e i team stessi. Il ruolo concede anche la possibilità di creare e gestire tutti i gruppi di Office 365, gestire i ticket di supporto e monitorare l'integrità del servizio.
  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del servizio Teams". È l'"Amministratore di Teams" nel portale di Azure.

* **[Amministratore account utente](#user-account-administrator)**: gli utenti con questo ruolo possono creare utenti e gestire tutti gli aspetti degli utenti, con alcune limitazioni (vedere di seguito). Possono anche creare e gestire tutti i gruppi. Il ruolo consente anche di creare e gestire visualizzazioni utente, gestire i ticket di supporto e monitorare l'integrità del servizio.

  | | |
  | --- | --- |
  |Autorizzazioni generiche|<p>Creare utenti e gruppi</p><p>Creare e gestire visualizzazioni utente</p><p>Gestire ticket di supporto di Office|
  |<p>Su tutti gli utenti, inclusi tutti gli amministratori</p>|<p>Gestire licenze</p><p>Gestire tutte le proprietà utente, ad eccezione del Nome dell'entità utente</p>
  |Solo sugli utenti non amministratori o in uno dei seguenti ruoli di amministratore con limitazioni:<ul><li>Ruoli con autorizzazioni di lettura nella directory<li>Mittente dell'invito guest<li>Amministratore del supporto tecnico<li>Ruolo con autorizzazioni di lettura per il Centro messaggi<li>Lettore di report<li>Amministratore account utente|<p>Eliminare e ripristinare</p><p>Disattivare e attivare</p><p>Invalidare i token di aggiornamento</p><p>Gestire tutte le proprietà utente, incluso il Nome dell'entità utente</p><p>Reimposta password</p><p>Aggiornare le chiavi dispositivo (FIDO)</p>
  
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

### <a name="billing-administrator"></a>Amministratore fatturazione
Può eseguire attività comuni relative alla fatturazione, ad esempio aggiornare le informazioni di pagamento.

  > [!NOTE]
  > Questo ruolo dispone di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, vedere la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/organization/basic/update | Aggiornare le proprietà di base sull'organizzazione in Azure Active Directory. |
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Aggiornare la proprietà organization.trustedCAsForPasswordlessAuth in Azure Active Directory. |
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
| microsoft.office365.webPortal/allEntities/basic/read | Leggere le proprietà di base per tutte le risorse in microsoft.office365.webPortal. |
| microsoft.office365.complianceManager/allEntities/allTasks | Gestire tutti gli aspetti di Office 365 Compliance Manager |
| microsoft.office365.desktopAnalytics/allEntities/allTasks | Gestire tutti gli aspetti di Desktop Analytics. |
| microsoft.office365.exchange/allEntities/allTasks | Gestire tutti gli aspetti di Exchange Online. |
| microsoft.office365.lockbox/allEntities/allTasks | Gestire tutti gli aspetti di Office 365 Customer Lockbox |
| microsoft.office365.messageCenter/messages/read | Leggere i messaggi in microsoft.office365.messageCenter. |
| microsoft.office365.messageCenter/securityMessages/read | Leggere i messaggi relativi alla sicurezza in microsoft.office365.messageCenter. |
| microsoft.powerApps.powerBI/allEntities/allTasks | Gestire tutti gli aspetti di Power BI. |
| microsoft.office365.protectionCenter/allEntities/allTasks | Gestire tutti gli aspetti di Centro protezione di Office 365. |
| microsoft.office365.securityComplianceCenter/allEntities/allTasks | Creare ed eliminare tutte le risorse e leggere e aggiornare le proprietà standard in microsoft.office365.securityComplianceCenter. |
| microsoft.office365.serviceHealth/allEntities/allTasks | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.sharepoint/allEntities/allTasks | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in microsoft.office365.sharepoint. |
| microsoft.office365.skypeForBusiness/allEntities/allTasks | Gestire tutti gli aspetti di Skype for Business Online. |
| microsoft.office365.supportTickets/allEntities/allTasks | Creare e gestire ticket di supporto per Office 365. |
| microsoft.office365.usageReports/allEntities/read | Eseguire la lettura dei report sull'utilizzo di Office 365. |
| microsoft.powerApps.dynamics365/allEntities/allTasks | Gestire tutti gli aspetti di Dynamics 365. |
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
I membri di questo ruolo vengono aggiunti al gruppo di amministratori locale nei dispositivi aggiunti ad Azure AD.

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
| microsoft.aad.directory/organization/trustedCAsForPasswordlessAuth/update | Aggiornare la proprietà organization.trustedCAsForPasswordlessAuth in Azure Active Directory. |
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

### <a name="user-account-administrator"></a>Amministratore account utente
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
