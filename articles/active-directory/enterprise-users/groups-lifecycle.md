---
title: Impostare la scadenza per i gruppi di Microsoft 365-Azure Active Directory | Microsoft Docs
description: Come impostare la scadenza per i gruppi di Microsoft 365 in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: enterprise-users
ms.topic: how-to
ms.date: 11/15/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f3544d81059cd4c25c584d8af1b6fcd45a41b8bb
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94650717"
---
# <a name="configure-the-expiration-policy-for-microsoft-365-groups"></a>Configurare i criteri di scadenza per i gruppi di Microsoft 365

In questo articolo viene descritto come gestire il ciclo di vita dei gruppi di Microsoft 365 impostando i criteri di scadenza. È possibile impostare i criteri di scadenza solo per i gruppi di Microsoft 365 in Azure Active Directory (Azure AD).

Dopo l'impostazione della scadenza di un gruppo:

- I gruppi con attività utente vengono rinnovati automaticamente in prossimità della scadenza.
- I proprietari del gruppo ricevono una notifica per rinnovare il gruppo, se il gruppo non viene rinnovato automaticamente.
- Qualsiasi gruppo non rinnovato viene eliminato.
- Qualsiasi gruppo di Microsoft 365 eliminato può essere ripristinato entro 30 giorni dai proprietari del gruppo o dall'amministratore.

Attualmente, è possibile configurare un solo criterio di scadenza per tutti i gruppi di Microsoft 365 in un'organizzazione Azure AD.

> [!NOTE]
> Per la configurazione e l'uso dei criteri di scadenza per i gruppi di Microsoft 365, è necessario disporre delle licenze Azure AD Premium per i membri di tutti i gruppi a cui vengono applicati i criteri di scadenza.

Per informazioni su come scaricare e installare i cmdlet di Azure AD PowerShell, vedere [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) (Azure Active Directory PowerShell per Graph 2.0.0.137).

## <a name="activity-based-automatic-renewal"></a>Rinnovo automatico basato sulle attività

Con Azure AD Intelligence, i gruppi vengono ora rinnovati automaticamente a seconda che siano stati usati di recente. Questa funzionalità Elimina la necessità di azioni manuali da parte dei proprietari del gruppo, perché si basa sull'attività dell'utente in gruppi in Microsoft 365 servizi come Outlook, SharePoint o teams. Se, ad esempio, un proprietario o un membro del gruppo esegue un'operazione come caricare un documento in SharePoint, visitare un canale teams o inviare un messaggio di posta elettronica al gruppo in Outlook, il gruppo viene rinnovato automaticamente e il proprietario non riceve alcuna notifica di rinnovo.

### <a name="activities-that-automatically-renew-group-expiration"></a>Attività che rinnovano automaticamente la scadenza del gruppo

Le azioni dell'utente seguenti provocano il rinnovo automatico del gruppo:

- SharePoint: visualizzare, modificare, scaricare, spostare, condividere o caricare file
- Outlook: gruppo di join, lettura/scrittura del messaggio del gruppo dallo spazio del gruppo, ad esempio un messaggio (in Outlook Accesso Web)
- Teams: visitare un canale Teams

### <a name="auditing-and-reporting"></a>Controllo e creazione di report

Gli amministratori possono ottenere un elenco di gruppi rinnovati automaticamente dai log di controllo delle attività in Azure AD.

![Rinnovo automatico dei gruppi in base all'attività](./media/groups-lifecycle/audit-logs-autorenew-group.png)

## <a name="roles-and-permissions"></a>Ruoli e autorizzazioni

Di seguito sono riportati i ruoli che consentono di configurare e utilizzare la scadenza per i gruppi di Microsoft 365 in Azure AD.

Ruolo | Autorizzazioni
-------- | --------
Amministratore globale, amministratore del gruppo o amministratore utente | Consente di creare, leggere, aggiornare o eliminare le impostazioni dei criteri di scadenza dei gruppi di Microsoft 365<br>Può rinnovare qualsiasi gruppo di Microsoft 365
Utente | Può rinnovare un gruppo di Microsoft 365 di cui è proprietario<br>Può ripristinare un gruppo di Microsoft 365 di cui è proprietario<br>Questo ruolo consente di leggere le impostazioni dei criteri di scadenza

Per ulteriori informazioni sulle autorizzazioni per ripristinare un gruppo eliminato, vedere [ripristinare un gruppo di Microsoft 365 eliminato in Azure Active Directory](groups-restore-deleted.md).

## <a name="set-group-expiration"></a>Impostare la scadenza dei gruppi

1. Aprire l'interfaccia di [amministrazione di Azure ad](https://aad.portal.azure.com) con un account che sia un amministratore globale nell'organizzazione Azure ad.

2. Selezionare **gruppi**, quindi selezionare **scadenza** per aprire le impostazioni di scadenza.
  
   ![Impostazioni di scadenza per i gruppi](./media/groups-lifecycle/expiration-settings.png)

3. Nella pagina **scadenza** è possibile:

    - Impostare la durata del gruppo in giorni. È possibile selezionare uno dei valori predefiniti o un valore personalizzato (deve avere una o più di 30 giorni).
    - Specificare un indirizzo e-mail per l'invio delle notifiche di rinnovo e di scadenza quando un gruppo non ha un proprietario.
    - Consente di selezionare i gruppi di Microsoft 365 scadono. È possibile impostare la scadenza per:
      - **Tutto** Gruppi di Microsoft 365
      - Elenco di gruppi di Microsoft 365 **selezionati**
      - **Nessuno** per limitare la scadenza per tutti i gruppi
    - Al termine salvare le impostazioni facendo clic su **Salva**.

> [!NOTE]
> - Quando si configura per la prima volta la scadenza, tutti i gruppi antecedenti all'intervallo di scadenza vengono impostati su 35 giorni fino alla scadenza, a meno che il gruppo non venga rinnovato automaticamente o il proprietario lo rinnovi.
> - Quando un gruppo dinamico viene eliminato e ripristinato, viene considerato come un nuovo gruppo e popolato nuovamente in base alla regola. Questo processo può richiedere fino a 24 ore.
> - Le notifiche di scadenza per i gruppi usati nei team vengono visualizzate nel feed dei proprietari dei team.

## <a name="email-notifications"></a>Notifiche di posta elettronica

Se i gruppi non vengono rinnovati automaticamente, le notifiche tramite posta elettronica, ad esempio, vengono inviate ai proprietari del gruppo di Microsoft 365 30 giorni, 15 giorni e 1 giorno prima della scadenza del gruppo. La lingua del messaggio di posta elettronica è determinata dalla lingua preferita del proprietario dei gruppi o dall'impostazione della lingua Azure AD. Se il proprietario del gruppo ha definito una lingua preferita o più proprietari hanno la stessa lingua preferita, viene utilizzata tale lingua. Per tutti gli altri casi, viene utilizzata l'impostazione della lingua Azure AD.

![Notifiche di posta elettronica scadenza](./media/groups-lifecycle/expiration-notification.png)

Dal messaggio di posta elettronica di notifica del **rinnovo del gruppo** , i proprietari del gruppo possono accedere direttamente alla pagina dei dettagli del gruppo nel [Pannello di accesso](https://account.activedirectory.windowsazure.com/r#/applications). In questa area è possibile ottenere altre informazioni sul gruppo, ad esempio la descrizione, la data dell'ultimo rinnovo, la data di scadenza, e anche procedere con il rinnovo del gruppo. La pagina Dettagli gruppo include ora anche collegamenti alle risorse del gruppo di Microsoft 365, in modo che il proprietario del gruppo possa visualizzare comodamente il contenuto e l'attività del gruppo.

Alla scadenza il gruppo viene eliminato, un giorno dopo la data di scadenza. Una notifica di posta elettronica come questa viene inviata ai proprietari del gruppo di Microsoft 365 che li informa sulla scadenza e l'eliminazione successiva del gruppo di Microsoft 365.

![Notifiche di posta elettronica per l'eliminazione del gruppo](./media/groups-lifecycle/deletion-notification.png)

Il gruppo può essere ripristinato entro 30 giorni dalla relativa eliminazione selezionando **Ripristina gruppo** o usando i cmdlet di PowerShell, come descritto in [ripristinare un gruppo di Microsoft 365 eliminato in Azure Active Directory](groups-restore-deleted.md). Si noti che il periodo di ripristino del gruppo di 30 giorni non è personalizzabile.

Se il gruppo che si sta ripristinando contiene documenti, siti di SharePoint o altri oggetti persistenti, potrebbero essere necessarie fino a 24 ore per ripristinare completamente il gruppo e il relativo contenuto.

## <a name="how-to-retrieve-microsoft-365-group-expiration-date"></a>Come recuperare Microsoft 365 data di scadenza del gruppo

Oltre al pannello di accesso in cui gli utenti possono visualizzare i dettagli dei gruppi, tra cui la data di scadenza e l'ultima data di rinnovo, è possibile recuperare la data di scadenza di un gruppo di Microsoft 365 da Microsoft Graph API REST beta. expirationDateTime come proprietà del gruppo è stato abilitato in Microsoft Graph beta. Può essere recuperato con una richiesta GET. Per altri dettagli, vedere [questo esempio](/graph/api/group-get?view=graph-rest-beta#example).

> [!NOTE]
> Per gestire le appartenenze ai gruppi nel pannello di accesso, è necessario impostare "limita l'accesso ai gruppi nel pannello di accesso" su "No" nell'impostazione generale gruppi Azure Active Directory.

## <a name="how-microsoft-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Come funziona la scadenza del gruppo di Microsoft 365 con una cassetta postale in attesa legale

Quando un gruppo scade e viene eliminato, 30 giorni i dati del gruppo presenti in app come Planner, Siti o Teams vengono eliminati definitivamente, mentre la cassetta postale del gruppo che è in blocco a fini giudiziari viene mantenuta. L'amministratore può usare i cmdlet di Exchange per ripristinare la cassetta postale e recuperare i dati.

## <a name="how-microsoft-365-group-expiration-works-with-retention-policy"></a>Funzionamento della scadenza del gruppo Microsoft 365 con i criteri di conservazione

I criteri di conservazione vengono configurati tramite il Centro sicurezza e conformità. Se è stato impostato un criterio di conservazione per i gruppi di Microsoft 365, quando un gruppo scade e viene eliminato, le conversazioni di gruppo nella cassetta postale del gruppo e nei file del sito del gruppo vengono conservati nel contenitore per il numero di giorni specifico definito nei criteri di conservazione. Dopo la scadenza gli utenti non potranno vedere il gruppo o i relativi contenuti, ma potranno recuperare i dati del sito e della cassetta postale tramite e-discovery.

## <a name="powershell-examples"></a>Esempi di PowerShell

Di seguito sono riportati alcuni esempi di come è possibile usare i cmdlet di PowerShell per configurare le impostazioni di scadenza per i gruppi di Microsoft 365 nell'organizzazione Azure AD:

1. Installare il modulo PowerShell v 2.0 e accedere al prompt di PowerShell:

   ``` PowerShell
   Install-Module -Name AzureAD
   Connect-AzureAD
   ```

1. Configurare le impostazioni di scadenza usare il cmdlet New-AzureADMSGroupLifecyclePolicy per impostare la durata di tutti i gruppi di Microsoft 365 nell'organizzazione Azure AD su 365 giorni. Le notifiche di rinnovo per i gruppi di Microsoft 365 senza proprietario verranno inviate a' emailaddress@contoso.com '
  
   ``` PowerShell
   New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
   ```

1. Recuperare i criteri esistenti Get-AzureADMSGroupLifecyclePolicy: questo cmdlet recupera le impostazioni di scadenza del gruppo di Microsoft 365 correnti che sono state configurate. Questo esempio contiene gli elementi seguenti:

   - ID dei criteri
   - La durata di tutti i gruppi di Microsoft 365 nell'organizzazione Azure AD è impostata su 365 giorni
   - Le notifiche di rinnovo per i gruppi di Microsoft 365 senza proprietario verranno inviate a' emailaddress@contoso.com .'
  
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
  
1. Rimuovere il criterio esistente Remove-AzureADMSGroupLifecyclePolicy: questo cmdlet elimina le impostazioni di scadenza del gruppo Microsoft 365, ma richiede l'ID criterio. Questo cmdlet disabilita la scadenza per i gruppi di Microsoft 365.
  
   ```powershell
   Remove-AzureADMSGroupLifecyclePolicy -Id "26fcc232-d1c3-4375-b68d-15c296f1f077"
   ```
  
I cmdlet seguenti possono essere usati per configurare i criteri in modo più dettagliato. Per ulteriori informazioni, vedere la [documentazione di PowerShell](/powershell/module/azuread/?branch=master&view=azureadps-2.0-preview#groups).

- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
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