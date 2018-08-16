---
title: Assegnazione dei ruoli di amministratore in Azure Active Directory | Microsoft Docs
description: Il ruolo di amministratore consente di aggiungere utenti, assegnare ruoli amministrativi, reimpostare le password utente, gestire le licenze utente o gestire i domini. Un utente a cui viene assegnato un ruolo amministrativo ha le stesse autorizzazioni in tutti i servizi cloud sottoscritti dall'organizzazione.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: users-groups-roles
ms.topic: article
ms.date: 07/25/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: c6c388bb98d189d91703c0ce82971b3ec4da4150
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2018
ms.locfileid: "39505512"
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Assegnazione dei ruoli di amministratore in Azure Active Directory

Azure Active Directory (Azure AD) consente di designare amministratori diversi per le diverse funzioni. Per eseguire attività come l'aggiunta o la modifica di utenti, l’assegnazione di ruoli amministrativi, la reimpostazione delle password utente, la gestione delle licenze utente e dei nomi di dominio, è possibile designare gli amministratori nel portale di Azure AD.

## <a name="details-about-the-global-administrator-role"></a>Dettagli sul ruolo di amministratore globale

L'amministratore globale ha accesso a tutte le funzionalità amministrative. Per impostazione predefinita, la persona che effettua l'iscrizione per una sottoscrizione di Azure riceve il ruolo di amministratore globale per la directory. Solo gli amministratori globali possono assegnare altri ruoli di amministratore.

## <a name="assign-or-remove-administrator-roles"></a>Assegnare o rimuovere ruoli di amministratore

Per informazioni su come assegnare ruoli amministrativi a un utente in Azure Active Directory, vedere [Assegnare un utente ai ruoli di amministratore in anteprima di Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md).

## <a name="available-roles"></a>Ruoli disponibili

Sono disponibili i ruoli di amministratore seguenti:

* **[Amministratore di applicazioni](#application-administrator)**: gli utenti in questo ruolo possono creare e gestire tutti gli aspetti di applicazioni aziendali, le registrazioni delle applicazioni e le impostazioni proxy dell'applicazione. Questo ruolo concede inoltre la possibilità consentire le autorizzazioni delegate e le autorizzazioni dell'applicazione escluse Microsoft Graph e Azure AD Graph. I membri di questo ruolo non vengono aggiunti come proprietari durante la creazione di nuove registrazioni di applicazione o nelle applicazioni aziendali.

* **[Sviluppatore applicazioni](#application-developer)**: gli utenti in questo ruolo possono creare registrazioni di applicazioni quando l'opzione "Users can register applications" (Gli utenti possono registrare applicazioni) è impostata su No. Questo ruolo consente inoltre ai membri di fornire il consenso per proprio conto quando l'opzione "Users can consent to apps accessing company data on their behalf" (Gli utenti possono consentire alle app l'accesso ai dati della società per loro conto) è impostata su No. I membri di questo ruolo vengono aggiunti come proprietari durante la creazione di nuove registrazioni di applicazione o nelle applicazioni aziendali.

* **[Amministratore fatturazione](#billing-administrator)**: effettua acquisti, gestisce le sottoscrizioni, gestisce i ticket di supporto e monitora l'integrità del servizio.

* **[Amministratore applicazione cloud](#cloud-application-administrator)**: gli utenti in questo ruolo hanno le stesse autorizzazioni del ruolo di amministratore di applicazioni, esclusa la possibilità di gestire il proxy dell'applicazione. Questo ruolo concede la possibilità di creare e gestire tutti gli aspetti delle applicazioni aziendali e delle registrazioni dell'applicazione. Questo ruolo concede inoltre la possibilità consentire le autorizzazioni delegate e le autorizzazioni dell'applicazione escluse Microsoft Graph e Azure AD Graph. I membri di questo ruolo non vengono aggiunti come proprietari durante la creazione di nuove registrazioni di applicazione o nelle applicazioni aziendali.

* **[Amministratore di conformità](#compliance-administrator)**: gli utenti con questo ruolo hanno autorizzazioni di gestione nel Centro sicurezza e conformità di Office 365 e nell'Interfaccia di amministrazione di Exchange. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Amministratore di accesso condizionale](#conditional-access-administrator)**: gli utenti con questo ruolo hanno la possibilità di gestire le impostazioni di accesso condizionale di Azure Active Directory.
  > [!NOTE]
  > Per distribuire il criterio di accesso condizionale di Exchange ActiveSync in Azure, l'utente deve essere anche amministratore globale.
  
* **[Amministratori dispositivo](#device-administrators)**: questo ruolo è disponibile per l'assegnazione solo come amministratore locale aggiuntivo in [Impostazioni dispositivo](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/DevicesMenuBlade/DeviceSettings/menuId/). Gli utenti con questo ruolo diventano amministratori del computer locale in tutti i dispositivi Windows 10 aggiunti ad Azure Active Directory. Non possono gestire gli oggetti di dispositivi in Azure Active Directory. 

* **[Ruoli con autorizzazioni di lettura nella directory](#directory-readers)**: si tratta di un ruolo legacy che deve essere assegnato alle applicazioni che non supportano il [framework di consenso](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Non deve essere assegnato agli utenti.

* **[Account di sincronizzazione della directory](#directory-synchronization-accounts)**: non usare. Questo ruolo viene assegnato automaticamente al servizio Azure AD Connect e non è progettato o supportato per altri usi.

* **[Ruoli con autorizzazioni di scrittura nella directory](#directory-writers)**: si tratta di un ruolo legacy che deve essere assegnato alle applicazioni che non supportano il [framework di consenso](../develop/quickstart-v1-integrate-apps-with-azure-ad.md). Non deve essere assegnato agli utenti.

* **[Amministratore del servizio Dynamics 365 / Amministratore del servizio CRM](#dynamics-365-service-administrator)**: gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft Dynamics 365 Online, quando il servizio è presente, e possono gestire i ticket di supporto e monitorare l'integrità del servizio. Altre informazioni sono disponibili in [Utilizzare il ruolo di amministratore del servizio per gestire il tenant](https://docs.microsoft.com/en-us/dynamics365/customer-engagement/admin/use-service-admin-role-manage-tenant).

* **[Amministratore del servizio Exchange](#exchange-service-administrator)**: gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft Exchange Online, quando il servizio è presente. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Amministratore globale o amministratore della società](#company-administrator)**: gli utenti con questo ruolo hanno accesso a tutte le funzionalità amministrative in Azure Active Directory, nonché ai servizi che usano identità di Azure Active Directory come Exchange Online, SharePoint Online e Skype per Business Online. La persona che effettua l'iscrizione per il tenant di Azure Active Directory diventa amministratore globale. Solo gli amministratori globali possono assegnare altri ruoli di amministratore. In una società possono essere presenti più amministratori globali. Gli amministratori globali possono reimpostare la password per qualsiasi utente e per tutti gli altri amministratori.

  > [!NOTE]
  > In Microsoft API Graph, Azure AD API Graph e Azure AD PowerShell, questo ruolo è identificato come "Amministratore società". È l'"amministratore globale" nel [portale di Azure](https://portal.azure.com).
  >
  >

* **[Mittente dell'invito guest](#guest-inviter)**: gli utenti con questo ruolo possono gestire gli inviti per gli utenti guest di Azure Active Directory B2B quando il valore dell'impostazione utente **I membri possono invitare** è No. Altre informazioni sulla collaborazione B2B in [Informazioni su Collaborazione B2B di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Il ruolo non include altre autorizzazioni.

* **[Amministratore di Information Protection](#information-protection-administrator)**: gli utenti con questo ruolo hanno tutte le autorizzazioni nel servizio Azure Information Protection. Questo ruolo consente di configurare etichette per i criteri di Azure Information Protection, gestire i modelli di protezione e attivare la protezione. Il ruolo tuttavia non concede le autorizzazioni relative a Identity Protection Center, Privileged Identity Management, Centro sicurezza e conformità di Office 365 e al servizio di monitoraggio dell'integrità dei servizi di Office 365.

* **[Amministratore del servizio Intune](#intune-service-administrator)**: gli utenti con questo ruolo usufruiscono di autorizzazioni globali all'interno di Microsoft Intune Online, quando il servizio è presente. Inoltre questo ruolo implica la possibilità di gestire utenti e dispositivi per associare i criteri, nonché creare e gestire gruppi. Altre informazioni sono disponibili in [Controllo degli accessi in base al ruolo (RBAC) con Microsoft Intune](https://docs.microsoft.com/en-us/intune/role-based-access-control)

* **[Ruolo con autorizzazioni di lettura per il Centro messaggi](#message-center-reader)**: gli utenti in questo ruolo possono monitorare le notifiche e gli aggiornamenti dello stato di integrità nel [Centro messaggi di Office 365](https://support.office.com/article/Message-center-in-Office-365-38FB3333-BFCC-4340-A37B-DEDA509C2093) per la propria organizzazione sui servizi configurati, ad esempio Exchange, Intune e Microsoft Teams. Il Ruolo con autorizzazioni di lettura per il Centro messaggi riceve settimanalmente digest di posta elettronica di post, aggiornamenti e può condividere i post del centro messaggi in Office 365. In Azure AD, gli utenti assegnati a questo ruolo avranno solo l'accesso di sola lettura ai servizi di Azure AD, ad esempio utenti e gruppi. 

* **[Supporto di livello 1 partner](#partner-tier1-support)**: non usare. Questo ruolo è deprecato e verrà rimosso da Azure AD in futuro. Il ruolo è riservato a pochi partner Microsoft per la rivendita e non è disponibile per un uso generale.

* **[Supporto di livello 2 partner](#partner-tier2-support)**: non usare. Questo ruolo è deprecato e verrà rimosso da Azure AD in futuro. Il ruolo è riservato a pochi partner Microsoft per la rivendita e non è disponibile per un uso generale.

* **[Amministratore password/Amministratore supporto tecnico](#helpdesk-administrator)**: gli utenti con questo ruolo possono modificare le password, gestire le richieste di servizio e monitorare l'integrità del servizio. Gli amministratori del supporto tecnico possono modificare le password solo per gli utenti e gli altri amministratori del supporto tecnico. 

  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore supporto tecnico". È l'"Amministratore password" nel [portale di Azure](https://portal.azure.com/).
  >
  >
  
* **[Amministratore del servizio Power BI](#power-bi-service-administrator)**: gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft Power BI, quando il servizio è presente, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità del servizio. Per altre informazioni, vedere [Informazioni sul ruolo di amministratore di Power BI](https://docs.microsoft.com/en-us/power-bi/service-admin-role).

* **[Amministratore del ruolo con privilegi](#privileged-role-administrator)**: gli utenti con questo ruolo possono gestire le assegnazioni dei ruoli in Azure Active Directory e in Azure AD Privileged Identity Management. Inoltre, questo ruolo consente la gestione di tutti gli aspetti di Privileged Identity Management.

* **[Lettore di report](#reports-reader)**: gli utenti con questo ruolo possono visualizzare i dati sui report di utilizzo e il dashboard dei report nell'interfaccia di amministrazione di Office 365 e in Adoption Content Pack in Power BI. Il ruolo consente inoltre l'accesso ai report e alle attività di accesso in Azure AD e ai dati restituiti dall'API di creazione report di Microsoft Graph. Un utente assegnato al ruolo di lettore di report può accedere solo alle metriche rilevanti per utilizzo e adozione. Questi utenti non hanno le autorizzazioni di amministratore per configurare le impostazioni o accedere alle interfacce di amministrazione specifiche dei prodotti come Exchange. 

* **[Amministratore della sicurezza](#security-administrator)**: gli utenti con questo ruolo hanno tutte le autorizzazioni di sola lettura del Ruolo con autorizzazioni di lettura per la sicurezza, oltre alla possibilità di gestire la configurazione per i servizi correlati alla sicurezza: Azure Active Directory Identity Protection, Azure Information Protection, Privileged Identity Management e Centro sicurezza e conformità di Office 365. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [Autorizzazioni nel Centro sicurezza e conformità di Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).
  
  | In ingresso | Operazione consentita |
  | --- | --- |
  | Centro di Identity Protection |<ul><li>Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza.<li>Inoltre, è possibile eseguire tutte le operazioni IPC, ad eccezione della reimpostazione delle password. |
  | Privileged Identity Management |<ul><li>Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza.<li>**Non consente** di gestire le impostazioni o le appartenenze ai ruoli di Azure AD. |
  | <p>Monitorare l'integrità del servizio Office 365</p><p>Centro sicurezza e conformità di Office 365 |<ul><li>Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza.<li>Consente di configurare tutte le impostazioni della funzionalità di protezione da minacce avanzate (protezione da malware e virus, configurazione URL dannosi, traccia di URL e così via). |
  
* **[Ruolo con autorizzazioni di lettura per la sicurezza](#security-reader)**: gli utenti con questo ruolo hanno un accesso globale in sola lettura, incluse tutte le informazioni in Azure Active Directory, Privileged Identity Management, nonché la possibilità di leggere i report di accesso e i log di controllo di Azure Active Directory. Il ruolo concede inoltre l'autorizzazione di sola lettura nel Centro sicurezza e conformità di Office 365. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [Autorizzazioni nel Centro sicurezza e conformità di Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

  | In ingresso | Operazione consentita |
  | --- | --- |
  | Centro di Identity Protection |Leggere tutte le informazioni sulle impostazioni e sui report di sicurezza per le funzionalità di sicurezza<ul><li>Filtro posta indesiderata<li>Crittografia<li>Prevenzione della perdita dei dati<li>Antimalware<li>Protezione avanzata dalle minacce<li>Anti-phishing<li>Regole del flusso di posta |
  | Privileged Identity Management |<p>Ha accesso in sola lettura a tutte le informazioni presentate in Azure AD PIM: criteri e report per le assegnazioni di ruoli in Azure AD, verifiche della sicurezza e in futuro accesso in lettura ai report e ai dati dei criteri per gli scenari in aggiunta all'assegnazione di ruoli in Azure AD.<p>**Non può** eseguire l'iscrizione ad Azure AD PIM o apportarvi modifiche. Nel portale di PIM o tramite PowerShell un utente in questo ruolo può attivare ruoli aggiuntivi, ad esempio amministratore globale o amministratore di ruoli con privilegi, se l'utente è candidato per questi ruoli. |
  | <p>Monitorare l'integrità del servizio Office 365</p><p>Centro sicurezza e conformità di Office 365</p> |<ul><li>Leggere e gestire gli avvisi<li>Leggere i criteri di sicurezza<li>Leggere i dati di intelligence sulle minacce, usare Cloud App Discovery e mettere in quarantena in ricerca e analisi<li>Leggere tutti i report |

* **[Amministratore servizio di supporto](#service-support-administrator)**: gli utenti con questo ruolo possono aprire le richieste di assistenza con Microsoft per i servizi di Azure e Office 365 e visualizzare la dashboard del servizio e il centro messaggi nel portale di Azure e il portale di amministrazione di Office 365. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Amministratore del servizio SharePoint](#sharepoint-service-administrator)**: gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft SharePoint Online, quando il servizio è presente, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità del servizio. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **[Amministratore di Skype for Business o del servizio Lync](#lync-service-administrator)**: gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft Skype for Business, quando il servizio è presente, nonché la possibilità di gestire gli attributi specifici per Skype in Azure Active Directory. Inoltre, questo ruolo garantisce la possibilità di gestire i ticket di supporto e l'integrità del servizio di monitoraggio. Per maggiori dettagli, vedere [Informazioni sul ruolo di amministratore in Skype for Business](https://support.office.com/en-us/article/about-the-skype-for-business-admin-role-aeb35bda-93fc-49b1-ac2c-c74fbeb737b5).

  > [!NOTE]
  > Nell'API Microsoft Graph, nell'API Graph di Azure AD e in Azure AD PowerShell questo ruolo è identificato come "Amministratore del sevizio Lync". Nel [portale di Azure](https://portal.azure.com/) è l'"Amministratore di Skype for Business".
  >
  >

* **[Amministratore account utente](#user-account-administrator)**: gli utenti con questo ruolo possono creare e gestire tutti gli aspetti degli utenti e dei gruppi. Possono inoltre gestire i ticket di supporto e monitorare l'integrità del servizio. Si applicano alcune restrizioni. Ad esempio, questo ruolo non consente l'eliminazione di un amministratore globale. Gli amministratori degli account utente possono modificare solo le password degli utenti, degli amministratori del supporto tecnico e di altri amministratori degli account utente.

| Operazione consentita | Operazione non consentita |
| --- | --- |
| <p>Visualizzare informazioni aziendali e sugli utenti</p><p>Gestire ticket di supporto di Office</p><p>Modificare solo le password degli utenti, degli amministratori del supporto tecnico e di altri amministratori degli account utente</p><p>Creare e gestire visualizzazioni utente</p><p>Creare, modificare ed eliminare utenti e gruppi, nonché gestire le licenze utente, con alcune limitazioni. Non può eliminare un amministratore globale o creare altri amministratori.</p> |<p>Eseguire operazioni di fatturazione e acquisto per prodotti Office</p><p>Gestire domini</p><p>Gestire informazioni aziendali</p><p>Delegare ruoli amministrativi ad altri</p><p>Usare la sincronizzazione delle directory</p><p>Abilitare o disabilitare l'autenticazione a più fattori</p><p>Visualizzare i log di controllo</p> |

## <a name="deprecated-roles"></a>Ruoli deprecati

I ruoli seguenti non devono essere usati. Questi ruoli sono stati deprecati e verranno rimossi da Azure AD in futuro.

* Amministratore di licenze ad hoc
* Aggiunta di dispositivi
* Gestione dispositivi
* Utenti di dispositivi
* Autore di utenti verificati tramite posta elettronica
* Amministratore della cassetta postale
* Aggiunta di dispositivi all'area di lavoro


### <a name="to-add-a-colleague-as-a-global-administrator"></a>Per aggiungere un collega come amministratore globale

1. Accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) con un account di amministratore globale o di amministratore con privilegi per la directory del tenant.

   ![Apertura dell'interfaccia di amministrazione di Azure AD](./media/directory-assign-admin-roles/active-directory-admin-center.png)

2. Selezionare **Utenti**.

3. Trovare l'utente che si desidera designare come amministratore globale e aprire il pannello per tale utente.

4. Nel pannello dell'utente selezionare **Ruolo della directory**.
 
5. Nel pannello del ruolo della directory selezionare il ruolo **Amministratore globale** e salvare.

## <a name="detailed-azure-active-directory-permissions"></a>Autorizzazioni dettagliate di Azure Active Directory
Nelle tabelle seguenti vengono descritte le autorizzazioni specifiche assegnate a ogni ruolo in Azure Active Directory. Alcuni ruoli, come quello di amministratore globale, potrebbero contenere ulteriori autorizzazioni per i servizi Microsoft al di fuori di Azure Active Directory.


### <a name="application-administrator"></a>Amministratore di applicazioni
Può creare e gestire tutti gli aspetti delle registrazioni di app e delle app aziendali.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Creare applicazioni in Azure Active Directory. |
| microsoft.aad.directory/Application/Delete | Eliminare applicazioni in Azure Active Directory. |
| microsoft.aad.directory/Application/Update | Aggiornare le proprietà standard per le applicazioni in Azure Active Directory. |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Aggiornare la proprietà Applications.DefaultPolicy in Azure Active Directory. |
| microsoft.aad.directory/Application/Update/Owners | Aggiornare la proprietà Applications.Owners in Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Create | Creare assegnazioni di ruolo delle app in Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Eliminare assegnazioni di ruolo delle app in Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Aggiornare le proprietà standard nelle assegnazioni di ruolo delle app in Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Creare criteri in Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Eliminare criteri in Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Aggiornare le proprietà standard per i criteri in Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Aggiornare la proprietà Policies.Owners in Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | Può garantire il consenso per conto di tutti gli utenti a tutte le risorse tranne Azure Active Directory (Azure AD Graph e Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/Create | Creare entità servizio in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Delete | Eliminare entità servizio in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Aggiornare le proprietà standard per le entità servizio in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Aggiornare la proprietà ServicePrincipals.AppRoleAssignedTo in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Aggiornare la proprietà ServicePrincipals.AppRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Aggiornare la proprietà ServicePrincipals.DefaultPolicy in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Aggiornare la proprietà ServicePrincipals.Owners in Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.aad.reports/AllEntities/Read | Eseguire la lettura dei report di Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Creare e gestire ticket di supporto per Office 365. |

### <a name="application-developer"></a>Sviluppatore di applicazioni
Può creare registrazioni di applicazioni indipendentemente dall'impostazione **Gli utenti possono registrare applicazioni**.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/Application/CreateAsOwner | Creare applicazioni in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/AppRoleAssignment/CreateAsOwner | Creare assegnazioni di ruolo delle app in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/OAuth2PermissionGrant/CreateAsOwner | Creare concessioni di autorizzazioni OAuth2 in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/ServicePrincipal/CreateAsOwner | Creare entità servizio in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |

### <a name="billing-administrator"></a>Amministratore fatturazione
Può eseguire attività comuni relative alla fatturazione, ad esempio aggiornare le informazioni di pagamento.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, consultare la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Eseguire la lettura della proprietà Organizations.TrustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.aad.directory/Organization/Update | Aggiornare le proprietà standard nelle organizzazioni in Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Aggiornare la proprietà Organizations.TrustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Controllo di accesso di Azure. |
| microsoft.aad.billing/AllEntities/AllActions | Gestire tutti gli aspetti della fatturazione di Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Creare e gestire ticket di supporto per Office 365. |

### <a name="cloud-application-administrator"></a>Amministratore applicazione cloud
Può creare e gestire tutti gli aspetti delle registrazioni di app e delle app aziendali, ad eccezione del Proxy di applicazione.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/Application/Create | Creare applicazioni in Azure Active Directory. |
| microsoft.aad.directory/Application/Delete | Eliminare applicazioni in Azure Active Directory. |
| microsoft.aad.directory/Application/Update | Aggiornare le proprietà standard per le applicazioni in Azure Active Directory. |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Aggiornare la proprietà Applications.DefaultPolicy in Azure Active Directory. |
| microsoft.aad.directory/Application/Update/Owners | Aggiornare la proprietà Applications.Owners in Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Create | Creare assegnazioni di ruolo delle app in Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Eliminare assegnazioni di ruolo delle app in Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Aggiornare le proprietà standard nelle assegnazioni di ruolo delle app in Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Creare criteri in Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Eliminare criteri in Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Aggiornare le proprietà standard per i criteri in Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Aggiornare la proprietà Policies.Owners in Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllNoDirectory | Può garantire il consenso per conto di tutti gli utenti a tutte le risorse tranne Azure Active Directory (Azure AD Graph e Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/Create | Creare entità servizio in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Delete | Eliminare entità servizio in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Aggiornare le proprietà standard per le entità servizio in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Aggiornare la proprietà ServicePrincipals.AppRoleAssignedTo in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Aggiornare la proprietà ServicePrincipals.AppRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Aggiornare la proprietà ServicePrincipals.DefaultPolicy in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Aggiornare la proprietà ServicePrincipals.Owners in Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.aad.reports/AllEntities/Read | Eseguire la lettura dei report di Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Creare e gestire ticket di supporto per Office 365. |

### <a name="company-administrator"></a>Amministratore società
Può gestire tutti gli aspetti di Azure AD e dei servizi Microsoft che usano identità di Azure AD. In Microsoft API Graph, Azure AD API Graph e Azure AD PowerShell, questo ruolo è identificato come "Amministratore società". È l'"amministratore globale" nel [portale di Azure](https://portal.azure.com).

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, consultare la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/AllActions/AllProperties | Creare ed eliminare unità amministrative, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/Application/AllActions/AllProperties | Creare ed eliminare applicazioni, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/AllActions/AllProperties | Creare ed eliminare assegnazioni di ruolo delle app, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/AllActions/AllProperties | Creare ed eliminare spazi di collaborazione, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/Contact/AllActions/AllProperties | Creare ed eliminare contatti, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/Device/AllActions/AllProperties | Creare ed eliminare dispositivi, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/AllActions/AllProperties | Creare ed eliminare ruoli della directory, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/DirectoryRoleTemplate/AllActions/AllProperties | Creare ed eliminare modelli di ruolo della directory, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/AllActions/AllProperties | Creare ed eliminare impostazioni della directory, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/DirectorySettingTemplate/AllActions/AllProperties | Creare ed eliminare modelli di impostazione della directory, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/Domain/AllActions/AllProperties | Creare ed eliminare domini, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/Group/AllActions/AllProperties | Creare ed eliminare gruppi, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/LoginTenantBranding/AllActions/AllProperties | Creare ed eliminare personalizzazioni dell'accesso al tenant, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/OAuth2PermissionGrant/AllActions/AllProperties | Creare ed eliminare concessioni di autorizzazioni OAuth2, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/Policy/AllActions/AllProperties | Creare ed eliminare criteri, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/ServiceAction/ConsentOnBehalfOfAllWithDirectory | Può garantire il consenso per conto di tutti gli utenti a tutte le risorse e ad Azure Active Directory (Azure AD Graph e Microsoft Graph). |
| microsoft.aad.directory/ServicePrincipal/AllActions/AllProperties | Creare ed eliminare entità servizio, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/Organization/AllActions/AllProperties | Creare ed eliminare organizzazioni, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.directory/User/AllActions/AllProperties | Creare ed eliminare utenti, eseguire la lettura e aggiornare tutte le proprietà in Azure Active Directory. |
| microsoft.aad.aadconnect/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in microsoft.aad.aadconnect. |
| microsoft.aad.accessservice/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Controllo di accesso di Azure. |
| microsoft.aad.billing/AllEntities/AllActions | Gestire tutti gli aspetti della fatturazione di Office 365. |
| microsoft.aad.compliance/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Centro conformità. |
| microsoft.aad.directorysync/AllEntities/AllActions | Eseguire tutte le azioni in Azure AD Connect. |
| microsoft.aad.lockbox/AllEntities/AllActions | Gestire tutti gli aspetti del servizio Lockbox. |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | Gestire tutti gli aspetti del servizio di gestione dei ruoli con privilegi. |
| microsoft.aad.reports/AllEntities/AllActions | Eseguire la lettura e configurare i report di Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Creare e gestire ticket di supporto per Office 365. |
| microsoft.crm/AllEntities/AllActions | Gestire tutti gli aspetti di Dynamics 365. |
| microsoft.exchange/AllEntities/AllActions | Gestire tutti gli aspetti di Exchange Online. |
| microsoft.aad.informationprotection/AllEntities/AllActions | Gestire tutti gli aspetti di Information Protection. |
| microsoft.intune/AllEntities/AllActions | Gestire tutti gli aspetti di Intune. |
| microsoft.powerbi/AllEntities/AllActions | Gestire tutti gli aspetti di Power BI. |
| microsoft.protectioncenter/AllEntities/AllActions | Gestire il Centro protezione Office 365. |
| microsoft.sharepoint/AllEntities/AllActions | Gestire SharePoint Online. |
| microsoft.skypeforbusiness/AllEntities/AllActions | Gestire Skype for Business Online. |

### <a name="compliance-administrator"></a>Amministratore di conformità
Può eseguire la lettura e gestire i report e la configurazione di conformità in Azure AD e Office 365.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, consultare la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Controllo di accesso di Azure. |
| microsoft.aad.compliance/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Centro conformità. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Creare e gestire ticket di supporto per Office 365. |
| microsoft.exchange/Compliance/AllActions | Gestire la conformità in Exchange Online. |
| microsoft.sharepoint/Compliance/AllActions | Gestire la conformità in SharePoint Online. |
| microsoft.skypeforbusiness/Compliance/AllActions | Gestire la conformità in Skype for Business Online. |

### <a name="conditional-access-administrator"></a>Amministratore di accesso condizionale
Può gestire le funzionalità di accesso condizionale.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/ConditionalAccessPolicy/Create | Creare criteri di accesso condizionale in Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Delete | Eliminare criteri di accesso condizionale in Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read | Eseguire la lettura delle proprietà standard nei criteri di accesso condizionale in Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/Owners | Eseguire la lettura della proprietà ConditionalAccessPolicys.Owners in Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Read/PolicyAppliedTo | Eseguire la lettura della proprietà ConditionalAccessPolicys.PolicyAppliedTo in Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Update | Aggiornare le proprietà standard nei criteri di accesso condizionale in Azure Active Directory. |
| microsoft.aad.directory/ConditionalAccessPolicy/Update/Owners | Aggiornare la proprietà ConditionalAccessPolicys.Owners in Azure Active Directory. |

### <a name="device-administrators"></a>Amministratori di dispositivi

Gli utenti con questo ruolo diventano amministratori del computer locale in tutti i dispositivi Windows 10 aggiunti ad Azure Active Directory. Non possono gestire gli oggetti di dispositivi in Azure Active Directory.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

### <a name="directory-readers"></a>Ruoli con autorizzazioni di lettura nella directory
Può leggere le informazioni base della directory. Per concedere l'accesso alle applicazioni.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/AdministrativeUnit/Read | Eseguire la lettura delle proprietà standard nelle unità amministrative in Azure Active Directory. |
| microsoft.aad.directory/AdministrativeUnit/Read/Members | Eseguire la lettura della proprietà AdministrativeUnits.Members in Azure Active Directory. |
| microsoft.aad.directory/Application/Read | Eseguire la lettura delle proprietà standard nelle applicazioni in Azure Active Directory. |
| microsoft.aad.directory/Application/Read/Owners | Eseguire la lettura della proprietà Applications.Owners in Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/Read | Eseguire la lettura delle proprietà standard negli spazi di collaborazione in Azure Active Directory. |
| microsoft.aad.directory/CollaborationSpace/Read/Owners | Eseguire la lettura della proprietà CollaborationSpaces.Owners in Azure Active Directory. |
| microsoft.aad.directory/Contact/Read | Eseguire la lettura delle proprietà standard nei contatti in Azure Active Directory. |
| microsoft.aad.directory/Contact/Read/MemberOf | Eseguire la lettura della proprietà Contacts.MemberOf in Azure Active Directory. |
| microsoft.aad.directory/Device/Read | Eseguire la lettura delle proprietà standard nei dispositivi in Azure Active Directory. |
| microsoft.aad.directory/Device/Read/MemberOf | Eseguire la lettura della proprietà Devices.MemberOf in Azure Active Directory. |
| microsoft.aad.directory/Device/Read/RegisteredOwners | Eseguire la lettura della proprietà Devices.RegisteredOwners in Azure Active Directory. |
| microsoft.aad.directory/Device/Read/RegisteredUsers | Eseguire la lettura della proprietà Devices.RegisteredUsers in Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read | Eseguire la lettura delle proprietà standard nei ruoli della directory in Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read/EligibleMembers | Eseguire la lettura della proprietà DirectoryRoles.EligibleMembers in Azure Active Directory. |
| microsoft.aad.directory/DirectoryRole/Read/Members | Eseguire la lettura della proprietà DirectoryRoles.Members in Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Read | Eseguire la lettura delle proprietà standard nelle impostazioni della directory in Azure Active Directory. |
| microsoft.aad.directory/DirectorySettingTemplate/Read | Eseguire la lettura delle proprietà standard nei modelli di impostazione della directory in Azure Active Directory. |
| microsoft.aad.directory/Domain/Read | Eseguire la lettura delle proprietà standard nei domini in Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Eseguire la lettura delle proprietà standard nei gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/Read/AppRoleAssignments | Eseguire la lettura della proprietà Groups.AppRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/Group/Read/MemberOf | Eseguire la lettura della proprietà Groups.MemberOf in Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Members | Eseguire la lettura della proprietà Groups.Members in Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Owners | Eseguire la lettura della proprietà Groups.Owners in Azure Active Directory. |
| microsoft.aad.directory/Group/Read/Settings | Eseguire la lettura della proprietà Groups.Settings in Azure Active Directory. |
| microsoft.aad.directory/OAuth2PermissionGrant/Read | Eseguire la lettura delle proprietà standard nelle concessioni di autorizzazioni OAuth2 in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read | Eseguire la lettura delle proprietà standard nelle entità servizio in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | Eseguire la lettura della proprietà ServicePrincipals.AppRoleAssignedTo in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | Eseguire la lettura della proprietà ServicePrincipals.AppRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | Eseguire la lettura della proprietà ServicePrincipals.DefaultPolicy in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | Eseguire la lettura della proprietà ServicePrincipals.MemberOf in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | Eseguire la lettura della proprietà ServicePrincipals.OAuth2PermissionGrants in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | Eseguire la lettura della proprietà ServicePrincipals.Owners in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | Eseguire la lettura della proprietà ServicePrincipals.OwnedObjects in Azure Active Directory. |
| microsoft.aad.directory/Organization/Read | Eseguire la lettura delle proprietà standard nelle organizzazioni in Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Eseguire la lettura della proprietà Organizations.TrustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.aad.directory/User/Read | Eseguire la lettura delle proprietà standard negli utenti in Azure Active Directory. |
| microsoft.aad.directory/User/Read/AppRoleAssignments | Eseguire la lettura della proprietà Users.AppRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/User/Read/DirectReports | Eseguire la lettura della proprietà Users.DirectReports in Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedBy | Eseguire la lettura della proprietà Users.InvitedBy in Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedUsers | Eseguire la lettura della proprietà Users.InvitedUsers in Azure Active Directory. |
| microsoft.aad.directory/User/Read/Manager | Eseguire la lettura della proprietà Users.Manager in Azure Active Directory. |
| microsoft.aad.directory/User/Read/MemberOf | Eseguire la lettura della proprietà Users.MemberOf in Azure Active Directory. |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | Eseguire la lettura della proprietà Users.OAuth2PermissionGrants in Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedDevices | Eseguire la lettura della proprietà Users.OwnedDevices in Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedObjects | Eseguire la lettura della proprietà Users.OwnedObjects in Azure Active Directory. |
| microsoft.aad.directory/User/Read/RegisteredDevices | Eseguire la lettura della proprietà Users.RegisteredDevices in Azure Active Directory. |

### <a name="directory-synchronization-accounts"></a>Account di sincronizzazione della directory
Usata solo dal servizio di Azure AD Connect.

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/Policy/Create | Creare criteri in Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Eliminare criteri in Azure Active Directory. |
| microsoft.aad.directory/Policy/Read | Eseguire la lettura delle proprietà standard nei criteri in Azure Active Directory. |
| microsoft.aad.directory/Policy/Read/Owners | Eseguire la lettura della proprietà Policies.Owners in Azure Active Directory. |
| microsoft.aad.directory/Policy/Read/PolicyAppliedTo | Eseguire la lettura della proprietà Policies.PolicyAppliedTo in Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Aggiornare le proprietà standard per i criteri in Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Aggiornare la proprietà Policies.Owners in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Create | Creare entità servizio in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read | Eseguire la lettura delle proprietà standard nelle entità servizio in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignedTo | Eseguire la lettura della proprietà ServicePrincipals.AppRoleAssignedTo in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/AppRoleAssignments | Eseguire la lettura della proprietà ServicePrincipals.AppRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/DefaultPolicy | Eseguire la lettura della proprietà ServicePrincipals.DefaultPolicy in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/MemberOf | Eseguire la lettura della proprietà ServicePrincipals.MemberOf in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OAuth2PermissionGrants | Eseguire la lettura della proprietà ServicePrincipals.OAuth2PermissionGrants in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/Owners | Eseguire la lettura della proprietà ServicePrincipals.Owners in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Read/OwnedObjects | Eseguire la lettura della proprietà ServicePrincipals.OwnedObjects in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update | Aggiornare le proprietà standard per le entità servizio in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignedTo | Aggiornare la proprietà ServicePrincipals.AppRoleAssignedTo in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/AppRoleAssignments | Aggiornare la proprietà ServicePrincipals.AppRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Aggiornare la proprietà ServicePrincipals.DefaultPolicy in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/Owners | Aggiornare la proprietà ServicePrincipals.Owners in Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/DirSync | Aggiornare la proprietà Organizations.DirSync in Azure Active Directory. |
| microsoft.aad.directorysync/AllEntities/AllActions | Eseguire tutte le azioni in Azure AD Connect. |

### <a name="directory-writer"></a>Ruolo con autorizzazioni di scrittura nella directory
Può leggere e scrivere le informazioni base della directory. Per concedere l'accesso alle applicazioni

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/DirectorySetting/Create | Creare impostazioni della directory in Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Delete | Eliminare impostazioni della directory in Azure Active Directory. |
| microsoft.aad.directory/DirectorySetting/Update | Aggiornare le proprietà standard per le impostazioni della directory in Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Creare gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/Group/Read | Eseguire la lettura delle proprietà standard nei gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/Update | Aggiornare le proprietà standard nei gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Aggiornare la proprietà Groups.AppRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Aggiornare la proprietà Groups.Members in Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Aggiornare la proprietà Groups.Owners in Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Aggiornare la proprietà Groups.Settings in Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Eseguire la lettura della proprietà Organizations.TrustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/User/Update | Aggiornare le proprietà standard negli utenti in Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Aggiornare la proprietà Users.AppRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Aggiornare la proprietà Users.Manager in Azure Active Directory. |

### <a name="dynamics-365-service-administrator"></a>Amministratore del servizio Dynamics 365
Può gestire tutti gli aspetti del prodotto Dynamics 365.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, consultare la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Eseguire la lettura della proprietà Organizations.TrustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Controllo di accesso di Azure. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Creare e gestire ticket di supporto per Office 365. |
| microsoft.crm/AllEntities/AllActions | Gestire tutti gli aspetti di Dynamics 365. |

### <a name="exchange-service-administrator"></a>Amministratore del servizio Exchange
Può gestire tutti gli aspetti del prodotto Exchange.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, consultare la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Controllo di accesso di Azure. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Creare e gestire ticket di supporto per Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.exchange/AllEntities/AllActions | Gestire tutti gli aspetti di Exchange Online. |

### <a name="guest-inviter"></a>Mittente dell'invito guest
Può invitare utenti guest indipendentemente dall'impostazione **I membri possono invitare utenti guest**.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal ruolo di guest.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/User/InviteGuest | Invitare utenti guest in Azure Active Directory. |
| microsoft.aad.directory/User/Read | Eseguire la lettura delle proprietà standard negli utenti in Azure Active Directory. |
| microsoft.aad.directory/User/Read/AppRoleAssignments | Eseguire la lettura della proprietà Users.AppRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/User/Read/DirectReports | Eseguire la lettura della proprietà Users.DirectReports in Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedBy | Eseguire la lettura della proprietà Users.InvitedBy in Azure Active Directory. |
| microsoft.aad.directory/User/Read/InvitedUsers | Eseguire la lettura della proprietà Users.InvitedUsers in Azure Active Directory. |
| microsoft.aad.directory/User/Read/Manager | Eseguire la lettura della proprietà Users.Manager in Azure Active Directory. |
| microsoft.aad.directory/User/Read/MemberOf | Eseguire la lettura della proprietà Users.MemberOf in Azure Active Directory. |
| microsoft.aad.directory/User/Read/OAuth2PermissionGrants | Eseguire la lettura della proprietà Users.OAuth2PermissionGrants in Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedDevices | Eseguire la lettura della proprietà Users.OwnedDevices in Azure Active Directory. |
| microsoft.aad.directory/User/Read/OwnedObjects | Eseguire la lettura della proprietà Users.OwnedObjects in Azure Active Directory. |
| microsoft.aad.directory/User/Read/RegisteredDevices | Eseguire la lettura della proprietà Users.RegisteredDevices in Azure Active Directory. |

### <a name="helpdesk-administrator"></a>Amministratore del supporto tecnico
Può reimpostare le password per gli utenti non amministratori e gli amministratori supporto tecnico.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Eseguire la lettura della proprietà Organizations.TrustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordHelpdeskScope | Aggiornare le password per gli amministratori limitati e altri amministratori supporto tecnico in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.aad.accessservice/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Controllo di accesso di Azure. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Creare e gestire ticket di supporto per Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="information-protection-administrator"></a>Amministratore di Information Protection
Può gestire tutti gli aspetti del prodotto Azure Information Protection.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, consultare la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/Group/Read | Eseguire la lettura delle proprietà standard nei gruppi in Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Eseguire la lettura della proprietà Organizations.TrustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.aad.informationprotection/AllEntities/AllActions | Gestire tutti gli aspetti di Information Protection. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Creare e gestire ticket di supporto per Office 365. |

### <a name="intune-service-administrator"></a>Amministratore del servizio Intune
Può gestire tutti gli aspetti del prodotto Intune.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, consultare la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/Contact/Create | Creare contatti in Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Eliminare contatti in Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Aggiornare le proprietà standard nei contatti in Azure Active Directory. |
| microsoft.aad.directory/Device/Create | Creare dispositivi in Azure Active Directory. |
| microsoft.aad.directory/Device/Delete | Eliminare dispositivi in Azure Active Directory. |
| microsoft.aad.directory/Device/Update | Aggiornare le proprietà standard nei dispositivi in Azure Active Directory. |
| microsoft.aad.directory/Device/Update/RegisteredOwners | Aggiornare la proprietà Devices.RegisteredOwners in Azure Active Directory. |
| microsoft.aad.directory/Device/Update/RegisteredUsers | Aggiornare la proprietà Devices.RegisteredUsers in Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Creare gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato verrà conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/Group/Delete | Eliminare gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Eseguire la lettura delle proprietà standard nei gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/Read/HiddenMembers | Eseguire la lettura della proprietà Groups.HiddenMembers in Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Ripristinare gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/Update | Aggiornare le proprietà standard nei gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Aggiornare la proprietà Groups.AppRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Aggiornare la proprietà Groups.Members in Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Aggiornare la proprietà Groups.Owners in Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Aggiornare la proprietà Groups.Settings in Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Eseguire la lettura della proprietà Organizations.TrustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.aad.directory/User/Update | Aggiornare le proprietà standard negli utenti in Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Aggiornare la proprietà Users.AppRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Aggiornare la proprietà Users.Manager in Azure Active Directory. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Creare e gestire ticket di supporto per Office 365. |
| microsoft.intune/AllEntities/AllActions | Gestire tutti gli aspetti di Intune. |

### <a name="lync-service-administrator"></a>Amministratore del servizio Lync
Può gestire tutti gli aspetti del prodotto Skype for Business.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, consultare la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Controllo di accesso di Azure. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Creare e gestire ticket di supporto per Office 365. |
| microsoft.skypeforbusiness/AllEntities/AllActions | Gestire Skype for Business Online. |

### <a name="message-center-reader"></a>Ruolo con autorizzazioni di lettura per il Centro messaggi
Può eseguire la lettura di messaggi e aggiornamenti per la propria organizzazione solo nel Centro messaggi di Office 365. 

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal ruolo con autorizzazioni di lettura nella directory.
  >
  >

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, consultare la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/Group/Read | Eseguire la lettura delle proprietà standard nei gruppi in Azure Active Directory. |
| microsoft.aad.accessmessagecenter/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Centro messaggi. |
| microsoft.aad.accessservice/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Controllo di accesso di Azure. |

### <a name="partner-tier1-support"></a>Supporto partner - Livello 1
Non usare: non destinato all'uso generale.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, consultare la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Controllo di accesso di Azure. |
| microsoft.aad.directory/Contact/Create | Creare contatti in Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Eliminare contatti in Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Aggiornare le proprietà standard nei contatti in Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Creare gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato viene conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/Group/Read | Eseguire la lettura delle proprietà standard nei gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Aggiornare la proprietà Groups.Members in Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Aggiornare la proprietà Groups.Owners in Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Eseguire la lettura della proprietà Organizations.TrustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Eliminare utenti in Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Ripristinare gli utenti eliminati in Azure Active Directory. |
| microsoft.aad.directory/User/Update | Aggiornare le proprietà standard negli utenti in Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Aggiornare la proprietà Users.AppRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Aggiornare la proprietà Users.Manager in Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordUserScope | Aggiornare le password degli utenti non amministratori in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Creare e gestire ticket di supporto per Office 365. |

### <a name="partner-tier2-support"></a>Supporto partner - Livello 2
Non usare: non destinato all'uso generale.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, consultare la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Controllo di accesso di Azure. |
| microsoft.aad.directory/Contact/Create | Creare contatti in Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Eliminare contatti in Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Aggiornare le proprietà standard nei contatti in Azure Active Directory. |
| microsoft.aad.directory/Domain/AllActions | Creare ed eliminare domini, eseguire la lettura e aggiornare le proprietà standard in Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Creare gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/Delete | Eliminare gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Eseguire la lettura delle proprietà standard nei gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Ripristinare gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Aggiornare la proprietà Groups.Members in Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Eseguire la lettura della proprietà Organizations.TrustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.aad.directory/Organization/Update | Aggiornare le proprietà standard nelle organizzazioni in Azure Active Directory. |
| microsoft.aad.directory/Organization/Update/TrustedCAsForPasswordlessAuth | Aggiornare la proprietà Organizations.TrustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Eliminare utenti in Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Ripristinare gli utenti eliminati in Azure Active Directory. |
| microsoft.aad.directory/User/Update | Aggiornare le proprietà standard negli utenti in Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Aggiornare la proprietà Users.AppRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Aggiornare la proprietà Users.Manager in Azure Active Directory. |
| microsoft.aad.directory/User/Update/Password | Aggiornare le password per tutti gli utenti in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Creare e gestire ticket di supporto per Office 365. |

### <a name="power-bi-service-administrator"></a>Amministratore del servizio Power BI
Può gestire tutti gli aspetti del prodotto Power BI.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, consultare la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Eseguire la lettura della proprietà Organizations.TrustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Controllo di accesso di Azure. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Creare e gestire ticket di supporto per Office 365. |
| microsoft.powerbi/AllEntities/AllActions | Gestire tutti gli aspetti di Power BI. |

### <a name="privileged-role-administrator"></a>Amministratore dei ruoli con privilegi
Può gestire le assegnazioni dei ruoli in Azure AD

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, consultare la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/DirectoryRole/Update | Aggiornare le proprietà standard per i ruoli della directory in Azure Active Directory. |
| microsoft.aad.privilegedrolemanagement/AllEntities/AllActions | Gestire tutti gli aspetti del servizio di gestione dei ruoli con privilegi. |

### <a name="reports-reader"></a>Lettore di report
Può eseguire la lettura dei report di accesso e di controllo.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal ruolo con autorizzazioni di lettura nella directory.
  >
  >

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, consultare la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.reports/AllEntities/Read | Eseguire la lettura dei report di Azure AD. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.office365.usagereports/AllEntities/Read | Eseguire la lettura dei report sull'utilizzo di Office 365. |

### <a name="security-administrator"></a>Amministratore della sicurezza
Può eseguire la lettura dei report e delle informazioni di sicurezza

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, consultare la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/Application/Update/DefaultPolicy | Aggiornare la proprietà Applications.DefaultPolicy in Azure Active Directory. |
| microsoft.aad.directory/Policy/Create | Creare criteri in Azure Active Directory. |
| microsoft.aad.directory/Policy/Delete | Eliminare criteri in Azure Active Directory. |
| microsoft.aad.directory/Policy/Update | Aggiornare le proprietà standard per i criteri in Azure Active Directory. |
| microsoft.aad.directory/Policy/Update/Owners | Aggiornare la proprietà Policies.Owners in Azure Active Directory. |
| microsoft.aad.directory/ServicePrincipal/Update/DefaultPolicy | Aggiornare la proprietà ServicePrincipals.DefaultPolicy in Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Eseguire la lettura della proprietà Organizations.TrustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Controllo di accesso di Azure. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.aad.privilegedrolemanagement/AllEntities/Read | Eseguire la lettura di tutti gli aspetti di Privileged Identity Management. |
| microsoft.protectioncenter/AllEntities/Read | Eseguire la lettura di tutti gli aspetti del Centro protezione Office 365. |
| microsoft.protectioncenter/AllEntities/Update | Gestire il Centro protezione Office 365. |

### <a name="security-reader"></a>Ruolo con autorizzazioni di lettura per la sicurezza
Può eseguire la lettura dei report e delle informazioni di sicurezza in Azure AD e Office 365.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal ruolo con autorizzazioni di lettura nella directory.
  >
  >

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, consultare la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Eseguire la lettura della proprietà Organizations.TrustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Controllo di accesso di Azure. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.privilegedidentitymanagement/AllEntities/Read | Eseguire la lettura di tutti gli aspetti di Privileged Identity Management. |
| microsoft.protectioncenter/AllEntities/Read | Eseguire la lettura di tutti gli aspetti del Centro protezione Office 365. |

### <a name="service-support-administrator"></a>Amministratore del supporto servizio
Può eseguire la lettura delle informazioni di integrità dei servizi e gestire i ticket di supporto.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, consultare la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Eseguire la lettura della proprietà Organizations.TrustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.aad.accessservice/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Controllo di accesso di Azure. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Creare e gestire ticket di supporto per Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

### <a name="sharepoint-service-administrator"></a>Amministratore del servizio SharePoint
Può gestire tutti gli aspetti del servizio SharePoint.

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

  > [!NOTE]
  > Questo ruolo usufruisce di autorizzazioni aggiuntive all'esterno di Azure Active Directory. Per altre informazioni, consultare la descrizione dei ruoli sopra riportata.
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.accessservice/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Controllo di accesso di Azure. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Creare e gestire ticket di supporto per Office 365. |
| microsoft.sharepoint/AllEntities/AllActions | Gestire SharePoint Online. |

### <a name="user-account-administrator"></a>Amministratore account utente
Può gestire tutti gli aspetti di utenti e gruppi

  > [!NOTE]
  > Questo ruolo eredita autorizzazioni aggiuntive dal [ruolo di utente](https://docs.microsoft.com/en-us/azure/active-directory/users-default-permissions).
  >
  >

| **Actions** | **Descrizione** |
| --- | --- |
| microsoft.aad.directory/AppRoleAssignment/Create | Creare assegnazioni di ruolo delle app in Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Delete | Eliminare assegnazioni di ruolo delle app in Azure Active Directory. |
| microsoft.aad.directory/AppRoleAssignment/Update | Aggiornare le proprietà standard nelle assegnazioni di ruolo delle app in Azure Active Directory. |
| microsoft.aad.directory/Contact/Create | Creare contatti in Azure Active Directory. |
| microsoft.aad.directory/Contact/Delete | Eliminare contatti in Azure Active Directory. |
| microsoft.aad.directory/Contact/Update | Aggiornare le proprietà standard nei contatti in Azure Active Directory. |
| microsoft.aad.directory/Group/Create | Creare gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/CreateAsOwner | Creare gruppi in Azure Active Directory. L'autore viene aggiunto come primo proprietario e l'oggetto creato verrà conteggiato ai fini della quota di 250 oggetti creati dell'autore. |
| microsoft.aad.directory/Group/Delete | Eliminare gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/Read | Eseguire la lettura delle proprietà standard nei gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/Read/HiddenMembers | Eseguire la lettura della proprietà Groups.HiddenMembers in Azure Active Directory. |
| microsoft.aad.directory/Group/Restore | Ripristinare gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/Update | Aggiornare le proprietà standard nei gruppi in Azure Active Directory. |
| microsoft.aad.directory/Group/Update/AppRoleAssignments | Aggiornare la proprietà Groups.AppRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Members | Aggiornare la proprietà Groups.Members in Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Owners | Aggiornare la proprietà Groups.Owners in Azure Active Directory. |
| microsoft.aad.directory/Group/Update/Settings | Aggiornare la proprietà Groups.Settings in Azure Active Directory. |
| microsoft.aad.directory/Organization/Read/TrustedCAsForPasswordlessAuth | Eseguire la lettura della proprietà Organizations.TrustedCAsForPasswordlessAuth in Azure Active Directory. |
| microsoft.aad.directory/User/AssignLicense | Gestire le licenze per gli utenti in Azure Active Directory. |
| microsoft.aad.directory/User/Create | Creare utenti in Azure Active Directory. |
| microsoft.aad.directory/User/Delete | Eliminare utenti in Azure Active Directory. |
| microsoft.aad.directory/User/InvalidateAllRefreshTokens | Invalidare tutti i token di aggiornamento utente in Azure Active Directory. |
| microsoft.aad.directory/User/Restore | Ripristinare gli utenti eliminati in Azure Active Directory. |
| microsoft.aad.directory/User/Update | Aggiornare le proprietà standard negli utenti in Azure Active Directory. |
| microsoft.aad.directory/User/Update/AppRoleAssignments | Aggiornare la proprietà Users.AppRoleAssignments in Azure Active Directory. |
| microsoft.aad.directory/User/Update/Manager | Aggiornare la proprietà Users.Manager in Azure Active Directory. |
| microsoft.aad.directory/User/Update/PasswordUserAcctAdminScope | Aggiornare le password per gli amministratori limitati, gli amministratori del supporto tecnico e altri amministratori di account utente in Azure Active Directory. Per informazioni dettagliate, vedere la documentazione online. |
| microsoft.aad.accessservice/AllEntities/AllActions | Creare ed eliminare tutte le risorse, eseguire la lettura e aggiornare le proprietà standard in Controllo di accesso di Azure. |
| microsoft.aad.supporttickets/AllEntities/AllActions | Creare e gestire ticket di supporto per Office 365. |
| microsoft.aad.servicehealth/AllEntities/AllActions | Eseguire la lettura e configurare l'integrità dei servizi di Office 365. |

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su come cambiare gli amministratori per una sottoscrizione di Azure, vedere [Come aggiungere o modificare i ruoli di amministratore di Azure](../../billing/billing-add-change-azure-subscription-administrator.md)
* Per altre informazioni sul modo in cui l'accesso alle risorse viene controllato in Microsoft Azure, vedere [Informazioni sull'accesso alle risorse in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)
* Per altre informazioni sul modo in cui Azure Active Directory è correlato alla sottoscrizione di Azure, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
