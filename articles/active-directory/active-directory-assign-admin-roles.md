---
title: Assegnazione dei ruoli di amministratore in Azure Active Directory | Documentazione Microsoft
description: Illustra i ruoli di amministratore disponibili con Azure Active Directory e come assegnarli.
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7fc27e8e-b55f-4194-9b8f-2e95705fb731
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/31/2016
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: ba3690084439aac83c91a1b4cfb7171b74c814f8
ms.openlocfilehash: 9107ae03c15b607e1d2d867a1e06e654c365cca5


---
# <a name="assigning-administrator-roles-in-azure-active-directory"></a>Assegnazione dei ruoli di amministratore in Azure Active Directory
Azure Active Directory (Azure AD) consente di designare amministratori diversi per le diverse funzioni. Questi amministratori hanno accesso a diverse funzionalità nel portale di Azure o nel portale di Azure classico e, a seconda del ruolo, possono, tra le altre cose, creare o modificare gli utenti, assegnare ruoli amministrativi ad altri, reimpostare le password utente, gestire le licenze utente e gestire i domini. Un utente a cui è assegnato un ruolo amministrativo disporrà delle stesse autorizzazioni in tutti i servizi cloud sottoscritti dall'organizzazione, indipendentemente dal fatto che il ruolo venga assegnato nel portale di Office 365, nel portale di Azure classico oppure usando il modulo di Azure AD per Windows PowerShell.

Sono disponibili i ruoli di amministratore seguenti:

* **Amministratore fatturazione**: effettua acquisti, gestisce le sottoscrizioni, gestisce i ticket di supporto e monitora l'integrità del servizio.
* L'**amministratore globale/amministratore della società** ha accesso a tutte le funzionalità amministrative. La persona che effettua l'iscrizione per l'account di Azure diventa amministratore globale. Solo gli amministratori globali possono assegnare altri ruoli di amministratore. In una società possono essere presenti più amministratori globali.

  > [!NOTE]
  > In Microsoft API Graph, Azure AD API Graph e Azure AD PowerShell, questo ruolo è identificato come "Amministratore società". È l'"amministratore globale" nel [portale di Azure](https://portal.azure.com).
  >
  >
* **Amministratore di conformità**:
* **Amministratore del servizio CRM**: gli utenti con questo ruolo hanno autorizzazioni globali all'interno di Microsoft CRM Online, quando il servizio è presente. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-US&ad=US)".
* **Responsabile approvazione per l'accesso a Customer Lockbox**: quando il servizio LockBox è abilitato, gli utenti con questo ruolo possono approvare le richieste per i tecnici Microsoft per l'accesso alle informazioni aziendali. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-US&ad=US)".
* **Amministratori di dispositivi**: gli utenti con questo ruolo diventano amministratori su tutti i dispositivi Windows 10 che fanno parte di Azure Active Directory".
* **Ruoli con autorizzazioni di lettura nella directory**: si tratta di un ruolo legacy che deve essere assegnato alle applicazioni che non supportano il [framework di consenso](active-directory-integrating-applications.md). Non deve essere assegnato agli utenti.
* **Account di sincronizzazione della directory**: non usare. Questo ruolo viene assegnato automaticamente al servizio Azure AD Connect e non è progettato o supportato per altri usi.
* **Ruoli con autorizzazioni di scrittura nella directory**: si tratta di un ruolo legacy che deve essere assegnato alle applicazioni che non supportano il [framework di consenso](active-directory-integrating-applications.md). Non deve essere assegnato agli utenti.
* **Amministratore del servizio Exchange**: gli utenti con questo ruolo dispongono delle autorizzazioni globali all'interno di Microsoft Exchange Online, quando il servizio è presente. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-US&ad=US)".
* **Amministratore del servizio Intune**: gli utenti con questo ruolo dispongono delle autorizzazioni globali all'interno di Microsoft Intune Online, quando il servizio è presente. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-US&ad=US).
* **Amministratore di Skype for Business**: gli utenti con questo ruolo dispongono delle autorizzazioni globali all'interno di Microsoft Skype for Business, quando il servizio è presente. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-US&ad=US). Questo ruolo era indicato in precedenza come ruolo di **amministratore del servizio Lync** .
* **Mittente dell'invito guest**: gli utenti con questo ruolo possono gestire gli inviti guest. Il ruolo non include altre autorizzazioni.
* **Amministratore password/Amministratore supporto tecnico**: reimposta le password, gestisce le richieste di servizio e monitora l'integrità del servizio. Gli amministratori password possono reimpostare le password solo per gli utenti e gli altri amministratori password.

  > [!NOTE]
  > In Microsoft Graph API, Azure AD Graph API e Azure AD PowerShell, questo ruolo è identificato come "Amministratore supporto tecnico".
  >
  >
* **Amministratore di SharePoint Services**: gli utenti con questo ruolo dispongono delle autorizzazioni globali all'interno di Microsoft SharePoint Online, quando il servizio è presente. Per altre informazioni vedere [Informazioni sui ruoli di amministratore di Office 365](https://support.office.com/article/About-Office-365-admin-roles-da585eea-f576-4f55-a1e0-87090b6aaa9d?ui=en-US&rs=en-US&ad=US).
* **Amministratore del servizio**: gestisce le richieste di servizio e monitora l'integrità del servizio.

  > [!NOTE]
  > Per assegnare il ruolo di amministratore del servizio a un utente, è prima necessario che l’amministratore globale assegni le autorizzazioni amministrative all’utente nel servizio, come Exchange Online, e poi che assegni il ruolo di amministratore del servizio all’utente nel portale di Azure classico.
  >
  >
* **User Account Administrator**: reimposta le password, monitora l'integrità del servizio e gestisce gli account utente, i gruppi di utenti e le richieste di servizio. Alle autorizzazioni di un amministratore Gestione utenti si applicano alcune limitazioni. Ad esempio, non possono eliminare un amministratore globale o creare altri amministratori. Non possono inoltre reimpostare le password per gli amministratori fatturazione, globali e del servizio.
* **Ruolo con autorizzazioni di lettura per la sicurezza**: accesso in sola lettura a numerose funzionalità di sicurezza di Identity Protection Center, Privileged Identity Management, Monitoraggio dell'integrità del servizio di Office 365 e Centro sicurezza e conformità di Office 365.
* **Amministratore della sicurezza**: tutte le autorizzazioni di sola lettura del **ruolo con autorizzazioni di lettura per la sicurezza**, oltre ad alcune autorizzazioni amministrative aggiuntive per gli stessi servizi: Identity Protection Center, Privileged Identity Management, Monitoraggio dell'integrità del servizio di Office 365 e Centro sicurezza e conformità di Office 365.

## <a name="administrator-permissions"></a>Autorizzazioni degli amministratori
### <a name="billing-administrator"></a>Amministratore fatturazione
| Operazione consentita | Operazione non consentita |
| --- | --- |
| <p>Visualizzare informazioni aziendali e sugli utenti</p><p>Gestire ticket di supporto di Office</p><p>Eseguire operazioni di fatturazione e acquisto per prodotti Office</p> |<p>Reimpostare le password utente</p><p>Creare e gestire visualizzazioni utente</p><p>Creare, modificare ed eliminare utenti e gruppi e gestire licenze utente</p><p>Gestire domini</p><p>Gestire informazioni aziendali</p><p>Delegare ruoli amministrativi ad altri</p><p>Usare la sincronizzazione delle directory</p><p>Visualizzazione dei report</p> |

### <a name="global-administrator"></a>Amministratore globale
| Operazione consentita | Operazione non consentita |
| --- | --- |
| <p>Visualizzare informazioni aziendali e sugli utenti</p><p>Gestire ticket di supporto di Office</p><p>Eseguire operazioni di fatturazione e acquisto per prodotti Office</p> <p>Reimpostare le password utente</p><p>Creare e gestire visualizzazioni utente</p><p>Creare, modificare ed eliminare utenti e gruppi e gestire licenze utente</p><p>Gestire domini</p><p>Gestire informazioni aziendali</p><p>Delegare ruoli amministrativi ad altri</p><p>Usare la sincronizzazione delle directory</p><p>Abilitare o disabilitare l'autenticazione a più fattori</p><p>Visualizzazione dei report</p> |N/D |

### <a name="password-administrator"></a>Amministratore password
| Operazione consentita | Operazione non consentita |
| --- | --- |
| <p>Visualizzare informazioni aziendali e sugli utenti</p><p>Gestire ticket di supporto di Office</p><p>Reimpostare le password utente</p> |<p>Eseguire operazioni di fatturazione e acquisto per prodotti Office</p><p>Creare e gestire visualizzazioni utente</p><p>Creare, modificare ed eliminare utenti e gruppi e gestire licenze utente</p><p>Gestire domini</p><p>Gestire informazioni aziendali</p><p>Delegare ruoli amministrativi ad altri</p><p>Usare la sincronizzazione delle directory</p><p>Visualizzazione dei report</p> |

### <a name="service-administrator"></a>Amministratore del servizio
| Operazione consentita | Operazione non consentita |
| --- | --- |
| <p>Visualizzare informazioni aziendali e sugli utenti</p><p>Gestire ticket di supporto di Office</p> |<p>Reimpostare le password utente</p><p>Eseguire operazioni di fatturazione e acquisto per prodotti Office</p><p>Creare e gestire visualizzazioni utente</p><p>Creare, modificare ed eliminare utenti e gruppi e gestire licenze utente</p><p>Gestire domini</p><p>Gestire informazioni aziendali</p><p>Delegare ruoli amministrativi ad altri</p><p>Usare la sincronizzazione delle directory</p><p>Visualizzazione dei report</p> |

### <a name="user-administrator"></a>Amministratore utenti
| Operazione consentita | Operazione non consentita |
| --- | --- |
| <p>Visualizzare informazioni aziendali e sugli utenti</p><p>Gestire ticket di supporto di Office</p><p>Reimpostare le password utente, con alcune limitazioni. Può reimpostare le password per gli amministratori fatturazione, globali e del servizio.</p><p>Creare e gestire visualizzazioni utente</p><p>Creare, modificare ed eliminare utenti e gruppi, nonché gestire le licenze utente, con alcune limitazioni. Non può eliminare un amministratore globale o creare altri amministratori.</p> |<p>Eseguire operazioni di fatturazione e acquisto per prodotti Office</p><p>Gestire domini</p><p>Gestire informazioni aziendali</p><p>Delegare ruoli amministrativi ad altri</p><p>Usare la sincronizzazione delle directory</p><p>Abilitare o disabilitare l'autenticazione a più fattori</p><p>Visualizzazione dei report</p> |

### <a name="security-reader"></a>Ruolo con autorizzazioni di lettura per la sicurezza
| In | Operazione consentita |
| --- | --- |
| Centro di Identity Protection |Leggere tutte le informazioni sulle impostazioni e sui report di sicurezza per le funzionalità di sicurezza<ul><li>Filtro posta indesiderata<li>Crittografia<li>Prevenzione della perdita dei dati<li>Antimalware<li>Protezione avanzata dalle minacce<li>Anti-phishing<li>Regole del flusso di posta |
| Privileged Identity Management |<p>Ha accesso in sola lettura a tutte le informazioni presentate in Azure AD PIM: criteri e report per le assegnazioni di ruoli in Azure AD, verifiche della sicurezza e in futuro accesso in lettura ai report e ai dati dei criteri per gli scenari in aggiunta all'assegnazione di ruoli in Azure AD.<p>**Non può** eseguire l'iscrizione ad Azure AD PIM o apportarvi modifiche. Nel portale di PIM o tramite PowerShell un utente in questo ruolo può attivare ruoli aggiuntivi, ad esempio amministratore globale o amministratore di ruoli con privilegi, se l'utente è candidato per questi ruoli. |
| <p>Monitorare l'integrità del servizio Office 365</p><p>Centro sicurezza e conformità di Office 365</p> |<ul><li>Leggere e gestire gli avvisi<li>Leggere i criteri di sicurezza<li>Leggere i dati di intelligence sulle minacce, usare Cloud App Discovery e mettere in quarantena in ricerca e analisi<li>Leggere tutti i report |

### <a name="security-administrator"></a>Amministratore della sicurezza
| In | Operazione consentita |
| --- | --- |
| Centro di Identity Protection |<ul><li>Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza.<li>Inoltre, è possibile eseguire tutte le operazioni IPC, ad eccezione della reimpostazione delle password. |
| Privileged Identity Management |<ul><li>Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza.<li>**Non consente** di gestire le impostazioni o le appartenenze ai ruoli di Azure AD. |
| <p>Monitorare l'integrità del servizio Office 365</p><p>Centro sicurezza e conformità di Office 365 |<ul><li>Tutte le autorizzazioni del ruolo con autorizzazioni di lettura per la sicurezza.<li>Consente di configurare tutte le impostazioni della funzionalità di protezione da minacce avanzate (protezione da malware e virus, configurazione URL dannosi, traccia di URL e così via). |

## <a name="details-about-the-global-administrator-role"></a>Dettagli sul ruolo di amministratore globale
L'amministratore globale ha accesso a tutte le funzionalità amministrative. Per impostazione predefinita, la persona che effettua l'iscrizione per una sottoscrizione di Azure riceve il ruolo di amministratore globale per la directory. Solo gli amministratori globali possono assegnare altri ruoli di amministratore.

## <a name="assign-or-remove-administrator-roles"></a>Assegnare o rimuovere ruoli di amministratore
1. Nel [portale di Azure classico](https://manage.windowsazure.com)fare clic su **Active Directory**e poi sul nome della directory dell'organizzazione.
2. Nella pagina **Utenti** fare clic sul nome visualizzato dell'utente da modificare.
3. Nell'elenco **Ruolo aziendale** scegliere il ruolo di amministratore da assegnare all'utente oppure selezionare **Utente** per rimuovere un ruolo di amministratore esistente.
4. Nella casella **Indirizzo di posta elettronica alternativo** digitare un indirizzo di posta elettronica. Questo indirizzo viene usato per notifiche importanti, inclusa la reimpostazione self-service della password, pertanto l'utente deve poter accedere all'account di posta elettronica indipendentemente dal fatto di poter accedere o meno ad Azure.
5. Selezionare **Consenti** o **Blocca** per specificare se l'utente è autorizzato o meno a effettuare l'accesso e ad accedere ai servizi.
6. Specificare una località nell'elenco a discesa **Località di utilizzo** .
7. Al termine, fare clic su **Salva**.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su come cambiare gli amministratori per una sottoscrizione di Azure, vedere [Come aggiungere o modificare i ruoli di amministratore di Azure](../billing-add-change-azure-subscription-administrator.md)
* Per altre informazioni sul modo in cui l'accesso alle risorse viene controllato in Microsoft Azure, vedere [Informazioni sull'accesso alle risorse in Azure](active-directory-understanding-resource-access.md)
* Per altre informazioni sul modo in cui Azure Active Directory è correlato alla sottoscrizione di Azure, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Gestire gli utenti](active-directory-create-users.md)
* [Gestire le password](active-directory-manage-passwords.md)
* [Gestire i gruppi](active-directory-manage-groups.md)



<!--HONumber=Nov16_HO3-->


