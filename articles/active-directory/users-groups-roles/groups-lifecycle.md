---
title: Impostare la scadenza dei gruppi di Office 365 - Azure Active Directory | Microsoft Docs
description: Come configurare la scadenza dei gruppi di Office 365 in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/13/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 562b551bc8a46a45135bf6a9a8e328b4b0e74f98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80048254"
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Configurare i criteri di scadenza per i gruppi di Office 365

In questo articolo viene illustrato come gestire il ciclo di vita dei gruppi di Office 365 impostando un criterio di scadenza per essi. È possibile impostare criteri di scadenza solo per i gruppi di Office 365 in Azure Active Directory (Azure AD).

Dopo l'impostazione della scadenza di un gruppo:

- I gruppi con attività degli utenti vengono rinnovati automaticamente all'avvicinarsi della scadenza.
- Se il gruppo non viene rinnovato automaticamente, i proprietari del gruppo vengono avvisati del rinnovo.
- Qualsiasi gruppo che non viene rinnovato viene eliminato.
- Qualsiasi gruppo di Office 365 eliminato può essere ripristinato entro 30 giorni dai proprietari del gruppo o dall'amministratore.

Attualmente, è possibile configurare un solo criterio di scadenza per tutti i gruppi di Office 365 in un'organizzazione di Azure AD.

> [!NOTE]
> La configurazione e l'utilizzo dei criteri di scadenza per i gruppi di Office 365 richiedono di possedere, ma non necessariamente assegnare licenze di Azure AD Premium per i membri di tutti i gruppi a cui viene applicato il criterio di scadenza.

Per informazioni su come scaricare e installare i cmdlet di Azure AD PowerShell, vedere [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) (Azure Active Directory PowerShell per Graph 2.0.0.137).

## <a name="activity-based-automatic-renewal"></a>Rinnovo automatico basato sulle attività

Con l'intelligence di Azure AD, i gruppi vengono rinnovati automaticamente in base all'uso di recente. Questa funzionalità elimina la necessità di un'azione manuale da parte dei proprietari dei gruppi, perché si basa sull'attività degli utenti nei gruppi nei servizi di Office 365 come Outlook, SharePoint o Teams. Ad esempio, se un proprietario o un membro del gruppo esegue un'operazione come caricare un documento in SharePoint, visitare un canale Di Teams o inviare un messaggio di posta elettronica al gruppo in Outlook, il gruppo viene rinnovato automaticamente e il proprietario non riceve alcuna notifica di rinnovo.

### <a name="activities-that-automatically-renew-group-expiration"></a>Attività che rinnovano automaticamente la scadenza del gruppo

Le seguenti azioni utente causano il rinnovo automatico del gruppo:

- SharePoint: visualizzare, modificare, scaricare, spostare, condividere o caricare file
- Outlook: partecipare a un messaggio di gruppo, di lettura/scrittura di un gruppo dallo spazio del gruppo, Come un messaggio (in Outlook Web Access)
- Squadre: visita il canale di Teams

### <a name="auditing-and-reporting"></a>Controllo e creazione di report

Gli amministratori possono ottenere un elenco di gruppi rinnovati automaticamente dai log di controllo delle attività in Azure AD.

![Rinnovo automatico dei gruppi in base all'attività](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Ruoli e autorizzazioni

Di seguito sono elencati i ruoli che possono configurare e usare la scadenza per i gruppi di Office 365 in Azure AD.

Ruolo | Autorizzazioni
-------- | --------
Amministratore globale, amministratore di gruppo o amministratore utente | Questo ruolo consente di creare, leggere, aggiornare o eliminare le impostazioni dei criteri di scadenza per i gruppi di Office 365<br>Questo ruolo consente di rinnovare qualsiasi gruppo di Office 365
Utente | Questo ruolo consente di rinnovare un gruppo di Office 365 di cui è proprietario<br>Questo ruolo consente di ripristinare un gruppo di Office 365 di cui è proprietario<br>Questo ruolo consente di leggere le impostazioni dei criteri di scadenza

Per altre informazioni sulle autorizzazioni per ripristinare un gruppo eliminato, vedere [Ripristinare un gruppo di Office 365 eliminato in Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Impostare la scadenza dei gruppi

1. Aprire [l'interfaccia](https://aad.portal.azure.com) di amministrazione di Azure AD con un account che sia un amministratore globale nell'organizzazione di Azure AD.

2. Selezionare **Gruppi**, quindi **Selezionare Scadenza** per aprire le impostazioni di scadenza.
  
   ![Impostazioni di scadenza per i gruppi](./media/groups-lifecycle/expiration-settings.png)

3. Nella pagina **Scadenza** è possibile:

    - Impostare la durata del gruppo in giorni. È possibile selezionare uno dei valori predefiniti oppure un valore personalizzato (che deve essere di 31 giorni o più).
    - Specificare un indirizzo e-mail per l'invio delle notifiche di rinnovo e di scadenza quando un gruppo non ha un proprietario.
    - Selezionare i gruppi di Office 365 che scadono. È possibile impostare la scadenza per:
      - **Tutti i** Gruppi di Office 365
      - Elenco dei gruppi di Office 365 **selezionati**
      - Nessuno per **limitare** la scadenza per tutti i gruppi
    - Al termine salvare le impostazioni facendo clic su **Salva**.

> [!NOTE]
> - Quando si imposta la scadenza per la prima volta, tutti i gruppi precedenti all'intervallo di scadenza vengono impostati su 35 giorni prima della scadenza, a meno che il gruppo non venga rinnovato automaticamente o che il proprietario lo rinnovi.
> - Quando un gruppo dinamico viene eliminato e ripristinato, viene visualizzato come un nuovo gruppo e ripopolato in base alla regola. Questo processo può richiedere fino a 24 ore.
> - Le notifiche di scadenza per i gruppi utilizzati in Team vengono visualizzate nel feed Proprietari team.

## <a name="email-notifications"></a>Notifiche di posta elettronica

Se i gruppi non vengono rinnovati automaticamente, le notifiche di posta elettronica come questa vengono inviate ai proprietari del gruppo di Office 365 30 giorni, 15 giorni e 1 giorno prima della scadenza del gruppo. La lingua del messaggio di posta elettronica è determinata dalla lingua preferita del proprietario dei gruppi o dall'impostazione della lingua di Azure AD. Se il proprietario del gruppo ha definito una lingua preferita o più proprietari hanno la stessa lingua preferita, viene utilizzata tale lingua. Per tutti gli altri casi, viene usata l'impostazione della lingua di Azure AD.

![Notifiche e-mail di scadenza](./media/groups-lifecycle/expiration-notification.png)

Dall'e-mail di notifica di **rinnovo** gruppo, i proprietari dei gruppi possono accedere direttamente alla pagina dei dettagli del gruppo nel [pannello di accesso](https://account.activedirectory.windowsazure.com/r#/applications). In questa area è possibile ottenere altre informazioni sul gruppo, ad esempio la descrizione, la data dell'ultimo rinnovo, la data di scadenza, e anche procedere con il rinnovo del gruppo. La pagina dei dettagli del gruppo include anche collegamenti alle risorse del gruppo di Office 365, che consentono al proprietario del gruppo di visualizzare facilmente il contenuto e l'attività del gruppo.

Alla scadenza il gruppo viene eliminato, un giorno dopo la data di scadenza. Viene inviata una notifica tramite posta elettronica simile alla seguente ai proprietari del gruppo di Office 365 per informarli della scadenza e della conseguente eliminazione del gruppo di Office 365.

![Notifiche e-mail di eliminazione dei gruppi](./media/groups-lifecycle/deletion-notification.png)

Il gruppo può essere ripristinato entro 30 giorni dalla relativa eliminazione selezionando **Ripristina gruppo** oppure usando i cmdlet di PowerShell, come descritto in [Ripristinare un gruppo di Office 365 eliminato in Azure Active Directory](groups-restore-deleted.md). Si noti che il periodo di ripristino del gruppo di 30 giorni non è personalizzabile.

Se il gruppo che si sta ripristinando contiene documenti, siti di SharePoint o altri oggetti persistenti, potrebbero essere necessarie fino a 24 ore per ripristinare completamente il gruppo e il relativo contenuto.

## <a name="how-to-retrieve-office-365-group-expiration-date"></a>Come recuperare la data di scadenza del gruppo di Office 365

Oltre al pannello di accesso in cui gli utenti possono visualizzare i dettagli del gruppo, tra cui la data di scadenza e la data dell'ultimo rinnovo, la data di scadenza di un gruppo di Office 365 può essere recuperata da Microsoft Graph REST API Beta. expirationDateTime come proprietà del gruppo è stata abilitata in Microsoft Graph Beta. Può essere recuperato con una richiesta GET. Per ulteriori dettagli, fare riferimento a [questo esempio](https://docs.microsoft.com/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> Per gestire le appartenenze ai gruppi nel Pannello di accesso, "Limita l'accesso ai gruppi nel pannello di accesso" deve essere impostato su "No" nelle impostazioni generali dei gruppi di Azure Active Directory.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Come funziona la scadenza dei gruppi di Office 365 con una cassetta postale in blocco a fini giudiziari

Quando un gruppo scade e viene eliminato, 30 giorni i dati del gruppo presenti in app come Planner, Siti o Teams vengono eliminati definitivamente, mentre la cassetta postale del gruppo che è in blocco a fini giudiziari viene mantenuta. L'amministratore può usare i cmdlet di Exchange per ripristinare la cassetta postale e recuperare i dati.

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Come funziona la scadenza dei gruppi di Office 365 con i criteri di conservazione

I criteri di conservazione vengono configurati tramite il Centro sicurezza e conformità. Se per i gruppi di Office 365 sono stati configurati criteri di conservazione, quando un gruppo scade e viene eliminato le conversazioni presenti nella cassetta postale del gruppo e i file presenti nel sito del gruppo vengono mantenuti nell'apposito contenitore per uno specifico numero di giorni definito nei criteri di conservazione. Dopo la scadenza gli utenti non potranno vedere il gruppo o i relativi contenuti, ma potranno recuperare i dati del sito e della cassetta postale tramite e-discovery.

## <a name="powershell-examples"></a>Esempi di PowerShell

Ecco alcuni esempi di come è possibile usare i cmdlet di PowerShell per configurare le impostazioni di scadenza per i gruppi di Office 365 nell'organizzazione di Azure AD:

1. Installare il modulo PowerShell v2.0 ed eseguire l'accesso al prompt di PowerShell:Install the PowerShell v2.0 module and sign in at the PowerShell prompt:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Configurare le impostazioni di scadenza Usare il cmdlet New-AzureADMSGroupLifecyclePolicy per impostare la durata di tutti i gruppi di Office 365 nell'organizzazione di Azure AD su 365 giorni. Le notifiche di rinnovo per i gruppi diemailaddress@contoso.comOffice 365 senza proprietari verranno inviate a ' '
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Recuperare i criteri esistenti Get-AzureADMSGroupLifecyclePolicy. Questo cmdlet recupera le impostazioni di scadenza correnti dei gruppi di Office 365 che sono state configurate. Questo esempio contiene gli elementi seguenti:

   - ID dei criteri
   - La durata per tutti i gruppi di Office 365 nell'organizzazione di Azure AD è impostata su 365 giorni
   - Le notifiche di rinnovo per i gruppi diemailaddress@contoso.comOffice 365 senza proprietari verranno inviate a ' .
  
   ```powershell
   Get-AzureADMSGroupLifecyclePolicy
  
   ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
   --                                    ------------------- ----------------- ---------------------------
   26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
   ```

1. Aggiornare i criteri esistenti Set-AzureADMSGroupLifecyclePolicy. Questo cmdlet consente di aggiornare un criterio esistente. Nell'esempio seguente la durata del gruppo nei criteri esistenti viene modificata da 365 giorni a 180 giorni.
  
   ```powershell
   Set-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
   ```
  
1. Aggiungere gruppi specifici al criterio Add-AzureADMSLifecyclePolicyGroup. Questo cmdlet aggiunge un gruppo ai criteri del ciclo di vita. Ad esempio:
  
   ```powershell
   Add-AzureADMSLifecyclePolicyGroup -Id "26fcc232-d1c3-4375-b68d-15c296f1f077" -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
   ```
  
1. Rimuovere il criterio esistente Remove-AzureADMSGroupLifecyclePolicy. Questo cmdlet elimina le impostazioni di scadenza dei gruppi di Office 365, ma richiede l'ID di tali criteri. Questo cmdlet disabilita la scadenza per i gruppi di Office 365.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
I cmdlet seguenti possono essere usati per configurare i criteri in modo più dettagliato. Per altre informazioni, vedere la documentazione di PowerShell .For more information, see [PowerShell documentation](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Get-AzureADMSGroupLifecyclePolicy
- Set-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Passaggi successivi

Questi articoli forniscono informazioni aggiuntive sui gruppi di Azure AD.

- [Vedere i gruppi esistenti](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Gestire le impostazioni di un gruppo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Gestire i membri di un gruppo](../fundamentals/active-directory-groups-members-azure-portal.md)
- [Gestire le appartenenze di un gruppo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Gestire le regole dinamiche per gli utenti in un gruppo](groups-dynamic-membership.md)
