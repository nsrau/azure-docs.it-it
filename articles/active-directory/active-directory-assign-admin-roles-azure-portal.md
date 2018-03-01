---
title: Assegnazione dei ruoli di amministratore in Azure Active Directory | Documentazione Microsoft
description: Il ruolo di amministratore consente di aggiungere utenti, assegnare ruoli amministrativi, reimpostare le password utente, gestire le licenze utente o gestire i domini. Un utente a cui viene assegnato un ruolo amministrativo ha le stesse autorizzazioni in tutti i servizi cloud sottoscritti dall'organizzazione.
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: 
ms.devlang: 
ms.topic: article
ms.date: 02/07/2018
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.openlocfilehash: 1fed14fc117c7652e677a245625502c1810068ff
ms.sourcegitcommit: 95500c068100d9c9415e8368bdffb1f1fd53714e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2018
---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Assegnazione dei ruoli di amministratore in Azure Active Directory

Azure Active Directory (Azure AD) consente di designare amministratori diversi per le diverse funzioni. Gli amministratori hanno accesso a diverse funzionalità nel portale di Azure e, a seconda del ruolo, possono, tra le altre cose, creare o modificare gli utenti, assegnare ruoli amministrativi ad altri, reimpostare le password utente, gestire le licenze utente e gestire i domini. Un utente a cui è assegnato un ruolo di amministratore avrà le stesse autorizzazioni in tutti i servizi cloud sottoscritti dall'organizzazione, indipendentemente dal fatto che il ruolo venga assegnato nel portale di Office 365, nel portale di Azure oppure usando il modulo di Azure AD per Windows PowerShell.

## <a name="details-about-the-global-administrator-role"></a>Dettagli sul ruolo di amministratore globale
L'amministratore globale ha accesso a tutte le funzionalità amministrative. Per impostazione predefinita, la persona che effettua l'iscrizione per una sottoscrizione di Azure riceve il ruolo di amministratore globale per la directory. Solo gli amministratori globali possono assegnare altri ruoli di amministratore.

## <a name="assign-or-remove-administrator-roles"></a>Assegnare o rimuovere ruoli di amministratore
Per informazioni su come assegnare ruoli amministrativi a un utente in Azure Active Directory, vedere [Assegnare un utente ai ruoli di amministratore in anteprima di Azure Active Directory](active-directory-users-assign-role-azure-portal.md).

## <a name="available-roles"></a>Ruoli disponibili
Sono disponibili i ruoli di amministratore seguenti:

* **Amministratore fatturazione**: effettua acquisti, gestisce le sottoscrizioni, gestisce i ticket di supporto e monitora l'integrità del servizio.

* **Amministratore di conformità**: gli utenti con questo ruolo hanno autorizzazioni di gestione nel Centro sicurezza e conformità di Office 365 e nell'Interfaccia di amministrazione di Exchange. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Amministratore di accesso condizionale**: gli utenti con questo ruolo hanno la possibilità di gestire le impostazioni di accesso condizionale di Azure Active Directory.
  > [!NOTE]
  > Per distribuire il criterio di accesso condizionale di Exchange ActiveSync in Azure, l'utente deve essere anche amministratore globale.
  
* **Amministratore del servizio CRM**: gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft CRM Online, quando il servizio è presente, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità del servizio. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Amministratori di dispositivi**: gli utenti con questo ruolo diventano amministratori di computer locali su tutti i dispositivi Windows 10 che fanno parte di Azure Active Directory. Non possono gestire gli oggetti di dispositivi in Azure Active Directory.

* **Ruoli con autorizzazioni di lettura nella directory**: si tratta di un ruolo legacy che deve essere assegnato alle applicazioni che non supportano il [framework di consenso](active-directory-integrating-applications.md). Non deve essere assegnato agli utenti.

* **Account di sincronizzazione della directory**: non usare. Questo ruolo viene assegnato automaticamente al servizio Azure AD Connect e non è progettato o supportato per altri usi.

* **Ruoli con autorizzazioni di scrittura nella directory**: si tratta di un ruolo legacy che deve essere assegnato alle applicazioni che non supportano il [framework di consenso](active-directory-integrating-applications.md). Non deve essere assegnato agli utenti.

* **Amministratore del servizio Exchange**: gli utenti con questo ruolo dispongono delle autorizzazioni globali all'interno di Microsoft Exchange Online, quando il servizio è presente. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Amministratore globale o amministratore della società**: gli utenti con questo ruolo hanno accesso a tutte le funzionalità amministrative in Azure Active Directory, nonché ai servizi che eseguono la federazione con Azure Active Directory come Exchange Online, SharePoint Online e Skype per Business Online. La persona che effettua l'iscrizione per il tenant di Azure Active Directory diventa amministratore globale. Solo gli amministratori globali possono assegnare altri ruoli di amministratore. In una società possono essere presenti più amministratori globali. Gli amministratori globali possono reimpostare la password per qualsiasi utente e per tutti gli altri amministratori.

  > [!NOTE]
  > In Microsoft API Graph, Azure AD API Graph e Azure AD PowerShell, questo ruolo è identificato come "Amministratore società". È l'"amministratore globale" nel [portale di Azure](https://portal.azure.com).
  >
  >

* **Mittente dell'invito guest**: gli utenti con questo ruolo possono gestire gli inviti utente guest di Azure Active Directory B2B quando l'impostazione dell'utente " 	I membri possono invitare" è impostato su No. Altre informazioni sulla collaborazione B2B in [Informazioni sull'anteprima di Collaborazione B2B di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b). Il ruolo non include altre autorizzazioni.

* **Amministratore di Information Protection (anteprima)**: gli utenti con questo ruolo hanno diritti utente solo nel servizio Azure Information Protection. Non hanno invece diritti di accesso in Identity Protection Center, Privileged Identity Management, Centro sicurezza e conformità di Office 365 e al servizio di monitoraggio dell'integrità dei servizi di Office 365. Possono configurare etichette per i criteri di Azure Information Protection, gestire i modelli di protezione e attivare la protezione.

* **Amministratore del servizio Intune**: gli utenti con questo ruolo dispongono delle autorizzazioni globali all'interno di Microsoft Intune Online, quando il servizio è presente. Inoltre questo ruolo implica la possibilità di gestire utenti e dispositivi per associare i criteri, nonché creare e gestire gruppi.

* **Amministratore della cassetta postale**: questo ruolo viene usato solo nell'ambito del supporto per la posta elettronica di Exchange Online per dispositivi RIM Blackberry. Se l'organizzazione non usa la posta elettronica di Exchange Online in dispositivi RIM Blackberry, non usare questo ruolo.

* **Partner Tier 1 Support** (Supporto di livello 1 partner): non usare. Questo ruolo è deprecato e verrà rimosso da Azure AD in futuro. Il ruolo è riservato a pochi partner Microsoft per la rivendita e non è disponibile per un uso generale.

* **Partner Tier 2 Support** (Supporto di livello 2 partner): non usare. Questo ruolo è deprecato e verrà rimosso da Azure AD in futuro. Il ruolo è riservato a pochi partner Microsoft per la rivendita e non è disponibile per un uso generale.

* **Amministratore password/Amministratore supporto tecnico**: gli utenti con questo ruolo possono modificare le password, gestire le richieste di servizio e monitorare l'integrità del servizio. Gli amministratori del supporto tecnico possono modificare le password solo per gli utenti e gli altri amministratori del supporto tecnico. 

  > [!NOTE]
  > In Microsoft Graph API, Azure AD Graph API e Azure AD PowerShell, questo ruolo è identificato come "Amministratore supporto tecnico". È l'"Amministratore password" nel [portale di Azure](https://portal.azure.com/).
  >
  >
  
* **Amministratore del servizio Power BI**: gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft Power BI, quando il servizio è presente, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità del servizio. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-001&ad=US).

* **Amministratore del ruolo con privilegi**: gli utenti con questo ruolo possono gestire le assegnazioni dei ruoli in Azure Active Directory e in Azure AD Privileged Identity Management. Inoltre, questo ruolo consente la gestione di tutti gli aspetti di Privileged Identity Management.

* **Lettore di report**: gli utenti con questo ruolo possono visualizzare i dati sui report di utilizzo e il dashboard dei report nell'interfaccia di amministrazione di Office 365 e in Adoption Content Pack in Power BI. Il ruolo consente inoltre l'accesso ai report e alle attività di accesso in Azure AD e ai dati restituiti dall'API di creazione report di Microsoft Graph. Un utente assegnato al ruolo di lettore di report può accedere solo alle metriche rilevanti per utilizzo e adozione. Questi utenti non hanno le autorizzazioni di amministratore per configurare le impostazioni o accedere alle interfacce di amministrazione specifiche dei prodotti come Exchange. 

* **Amministratore della sicurezza**: gli utenti con questo ruolo hanno tutte le autorizzazioni di sola lettura del Ruolo con autorizzazioni di lettura per la sicurezza, oltre alla possibilità di gestire la configurazione per i servizi correlati alla sicurezza: Azure Active Directory Identity Protection, Azure Information Protection, Privileged Identity Management e Centro sicurezza e conformità di Office 365 . Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [Autorizzazioni nel Centro sicurezza e conformità di Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Ruolo con autorizzazioni di lettura per la sicurezza**: gli utenti con questo ruolo hanno un accesso globale in sola lettura, incluse tutte le informazioni in Azure Active Directory, Privileged Identity Management, nonché la possibilità di leggere i report di accesso e i log di controllo di Azure Active Directory. Il ruolo concede inoltre l'autorizzazione di sola lettura nel Centro sicurezza e conformità di Office 365. Altre informazioni sulle autorizzazioni di Office 365 sono disponibili in [Autorizzazioni nel Centro sicurezza e conformità di Office 365](https://support.office.com/article/Permissions-in-the-Office-365-Security-Compliance-Center-d10608af-7934-490a-818e-e68f17d0e9c1).

* **Amministratore servizio di supporto**: gli utenti con questo ruolo possono aprire le richieste di assistenza con Microsoft per i servizi di Azure e Office 365 e visualizzare la dashboard del servizio e il centro messaggi nel portale di Azure e il portale di amministrazione di Office 365. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Amministratore del servizio SharePoint.**: gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft SharePoint Online, quando il servizio è presente, nonché la possibilità di gestire i ticket di supporto e monitorare l'integrità del servizio. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

* **Amministratore di Skype for Business o del servizio Lync**: gli utenti con questo ruolo dispongono delle autorizzazioni globali all'interno di Microsoft Skype for Business, quando il servizio è presente, nonché della possibilità di gestire gli attributi specifici per Skype in Azure Active Directory. Inoltre, questo ruolo garantisce la possibilità di gestire i ticket di supporto e l'integrità del servizio di monitoraggio. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d).

  > [!NOTE]
  > In Microsoft Graph API, Azure AD Graph API e Azure AD PowerShell, questo ruolo è identificato come "Amministratore del sevizio Lync". Nel [portale di Azure](https://portal.azure.com/) è l'"Amministratore di Skype for Business".
  >
  >

* **Amministratore account utente**: gli utenti con questo ruolo possono creare e gestire tutti gli aspetti degli utenti e dei gruppi. Inoltre, questo ruolo include la possibilità di gestire i ticket di supporto e monitorare l'integrità del servizio. Si applicano alcune restrizioni. Ad esempio, questo ruolo non consente l'eliminazione di un amministratore globale. Gli amministratori degli account utente possono modificare solo le password degli utenti, degli amministratori del supporto tecnico e di altri amministratori degli account utente.

## <a name="administrator-permissions"></a>Autorizzazioni degli amministratori

### <a name="billing-administrator"></a>Amministratore fatturazione

| Operazione consentita | Operazione non consentita |
| --- | --- |
|<p>Visualizzare informazioni aziendali e sugli utenti</p><p>Gestire ticket di supporto di Office</p><p>Eseguire operazioni di fatturazione e acquisto per prodotti Office</p> |<p>Reimpostare le password utente</p><p>Creare e gestire visualizzazioni utente</p><p>Creare, modificare ed eliminare utenti e gruppi e gestire licenze utente</p><p>Gestire domini</p><p>Gestire informazioni aziendali</p><p>Delegare ruoli amministrativi ad altri</p><p>Usare la sincronizzazione delle directory</p><p>Visualizzare i log di controllo</p>|

### <a name="conditional-access-administrator"></a>Amministratore di accesso condizionale

| Operazione consentita | Operazione non consentita |
| --- | --- |
|<p>Visualizzare informazioni aziendali e sugli utenti</p><p>Gestire le impostazioni di accesso condizionale</p> |<p>Reimpostare le password utente</p><p>Creare e gestire visualizzazioni utente</p><p>Creare, modificare ed eliminare utenti e gruppi e gestire licenze utente</p><p>Gestire domini</p><p>Gestire informazioni aziendali</p><p>Delegare ruoli amministrativi ad altri</p><p>Usare la sincronizzazione delle directory</p><p>Visualizzare i log di controllo</p>|

### <a name="global-administrator"></a>Amministratore globale
| Operazione consentita | Operazione non consentita |
| --- | --- |
|<p>Visualizzare informazioni aziendali e sugli utenti</p><p>Gestire ticket di supporto di Office</p><p>Eseguire operazioni di fatturazione e acquisto per prodotti Office</p><p>Reimpostare le password utente</p><p>Reimpostare le password di altri amministratori</p> <p>Creare e gestire visualizzazioni utente</p><p>Creare, modificare ed eliminare utenti e gruppi e gestire licenze utente</p><p>Gestire domini</p><p>Gestire informazioni aziendali</p><p>Delegare ruoli amministrativi ad altri</p><p>Usare la sincronizzazione delle directory</p><p>Abilitare o disabilitare l'autenticazione a più fattori</p><p>Visualizzare i log di controllo</p> |N/D |

### <a name="password-administrator--helpdesk-administrator"></a>Amministratore password / Amministratore supporto tecnico
| Operazione consentita | Operazione non consentita |
| --- | --- |
| <p>Visualizzare informazioni aziendali e sugli utenti</p><p>Gestire ticket di supporto di Office</p><p>Modificare le password solo per gli utenti e altri amministratori del supporto tecnico</p>|<p>Eseguire operazioni di fatturazione e acquisto per prodotti Office</p><p>Creare e gestire visualizzazioni utente</p><p>Creare, modificare ed eliminare utenti e gruppi e gestire licenze utente</p><p>Gestire domini</p><p>Gestire informazioni aziendali</p><p>Delegare ruoli amministrativi ad altri</p><p>Usare la sincronizzazione delle directory</p><p>Visualizzazione dei report</p>|

### <a name="information-protection-administrator-preview"></a>Amministratore di Information Protection (anteprima)
In ingresso | Operazione consentita
-------- | ---------
Azure Information Protection | <li>Configurare etichette e impostazioni in criteri con ambito globale<li>Configurare e gestire modelli di protezione<li>Attivare o disattivare la protezione--
 
### <a name="reports-reader"></a>Lettore di report 
Operazione consentita | Operazione non consentita
------ | ----------
Visualizzare i report di accesso e i log di controllo di Azure AD<br>Visualizzare informazioni aziendali e sugli utenti<br>Accedere al dashboard di utilizzo di Office 365 | Creare e gestire visualizzazioni utente<br>Creare, modificare ed eliminare utenti e gruppi e gestire licenze utente<br>Delegare ruoli amministrativi ad altri<br>Gestire informazioni aziendali

### <a name="security-reader"></a>Ruolo con autorizzazioni di lettura per la sicurezza
| In ingresso | Operazione consentita |
| --- | --- |
| Centro di Identity Protection |Leggere tutte le informazioni sulle impostazioni e sui report di sicurezza per le funzionalità di sicurezza<ul><li>Filtro posta indesiderata<li>Crittografia<li>Prevenzione della perdita dei dati<li>Antimalware<li>Protezione avanzata dalle minacce<li>Anti-phishing<li>Regole del flusso di posta |
| Privileged Identity Management |<p>Ha accesso in sola lettura a tutte le informazioni presentate in Azure AD PIM: criteri e report per le assegnazioni di ruoli in Azure AD, verifiche della sicurezza e in futuro accesso in lettura ai report e ai dati dei criteri per gli scenari in aggiunta all'assegnazione di ruoli in Azure AD.<p>**Non può** eseguire l'iscrizione ad Azure AD PIM o apportarvi modifiche. Nel portale di PIM o tramite PowerShell un utente in questo ruolo può attivare ruoli aggiuntivi, ad esempio amministratore globale o amministratore di ruoli con privilegi, se l'utente è candidato per questi ruoli. |
| <p>Monitorare l'integrità del servizio Office 365</p><p>Centro sicurezza e conformità di Office 365</p> |<ul><li>Leggere e gestire gli avvisi<li>Leggere i criteri di sicurezza<li>Leggere i dati di intelligence sulle minacce, usare Cloud App Discovery e mettere in quarantena in ricerca e analisi<li>Leggere tutti i report |

### <a name="security-administrator"></a>Amministratore della sicurezza
| In ingresso | Operazione consentita |
| --- | --- |
| Centro di Identity Protection |<ul><li>Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza.<li>Inoltre, è possibile eseguire tutte le operazioni IPC, ad eccezione della reimpostazione delle password. |
| Privileged Identity Management |<ul><li>Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza.<li>**Non consente** di gestire le impostazioni o le appartenenze ai ruoli di Azure AD. |
| <p>Monitorare l'integrità del servizio Office 365</p><p>Centro sicurezza e conformità di Office 365 |<ul><li>Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza.<li>Consente di configurare tutte le impostazioni della funzionalità di protezione da minacce avanzate (protezione da malware e virus, configurazione URL dannosi, traccia di URL e così via). |

### <a name="service-administrator"></a>Amministratore del servizio
| Operazione consentita | Operazione non consentita |
| --- | --- |
| <p>Visualizzare informazioni aziendali e sugli utenti</p><p>Gestire ticket di supporto di Office</p> |<p>Reimpostare le password utente</p><p>Eseguire operazioni di fatturazione e acquisto per prodotti Office</p><p>Creare e gestire visualizzazioni utente</p><p>Creare, modificare ed eliminare utenti e gruppi e gestire licenze utente</p><p>Gestire domini</p><p>Gestire informazioni aziendali</p><p>Delegare ruoli amministrativi ad altri</p><p>Usare la sincronizzazione delle directory</p><p>Visualizzare i log di controllo</p> |

### <a name="user-account-administrator"></a>Amministratore degli account utente
| Operazione consentita | Operazione non consentita |
| --- | --- |
| <p>Visualizzare informazioni aziendali e sugli utenti</p><p>Gestire ticket di supporto di Office</p><p>Modificare solo le password degli utenti, degli amministratori del supporto tecnico e di altri amministratori degli account utente</p><p>Creare e gestire visualizzazioni utente</p><p>Creare, modificare ed eliminare utenti e gruppi, nonché gestire le licenze utente, con alcune limitazioni. Non può eliminare un amministratore globale o creare altri amministratori.</p> |<p>Eseguire operazioni di fatturazione e acquisto per prodotti Office</p><p>Gestire domini</p><p>Gestire informazioni aziendali</p><p>Delegare ruoli amministrativi ad altri</p><p>Usare la sincronizzazione delle directory</p><p>Abilitare o disabilitare l'autenticazione a più fattori</p><p>Visualizzare i log di controllo</p> |

### <a name="to-add-a-colleague-as-a-global-administrator"></a>Per aggiungere un collega come amministratore globale

1. Accedere all'[interfaccia di amministrazione di Azure Active Directory](https://aad.portal.azure.com) con un account di amministratore globale per la directory del tenant.

   ![Apertura dell'interfaccia di amministrazione di Azure AD](./media/active-directory-assign-admin-roles-azure-portal/active-directory-admin-center.png)

2. Selezionare **Users and groups (Utenti e gruppi) &gt; All users** (Tutti gli utenti)

3. Trovare l'utente che si desidera designare come amministratore globale e aprire il pannello per tale utente.

4. Nel pannello dell'utente selezionare **Ruolo della directory**.
 
5. Nel pannello del ruolo della directory selezionare il ruolo **Amministratore globale** e salvare.

## <a name="deprecated-roles"></a>Ruoli deprecati

I ruoli seguenti non devono essere usati. Questi ruoli sono stati deprecati e verranno rimossi da Azure AD in futuro.

* Amministratore di licenze ad hoc
* Autore di utenti verificati tramite posta elettronica
* Aggiunta di dispositivi
* Gestione dispositivi
* Utenti di dispositivi
* Aggiunta di dispositivi all'area di lavoro

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni su come cambiare gli amministratori per una sottoscrizione di Azure, vedere [Come aggiungere o modificare i ruoli di amministratore di Azure](../billing-add-change-azure-subscription-administrator.md)
* Per altre informazioni sul modo in cui l'accesso alle risorse viene controllato in Microsoft Azure, vedere [Informazioni sull'accesso alle risorse in Azure](active-directory-understanding-resource-access.md)
* Per altre informazioni sul modo in cui Azure Active Directory è correlato alla sottoscrizione di Azure, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gestire gli utenti](active-directory-create-users.md)
* [Gestire le password](active-directory-manage-passwords.md)
* [Gestire i gruppi](active-directory-manage-groups.md)
