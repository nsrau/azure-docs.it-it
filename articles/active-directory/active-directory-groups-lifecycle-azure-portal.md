---
title: Scadenza dei gruppi di Office 365 in Azure Active Directory | Microsoft Docs
description: Come configurare la scadenza dei gruppi di Office 365 in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 95593eaacd73316ab527ffda8f977fbf0eb15558
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="configure-the-expiration-policy-for-office-365-groups"></a>Configurare i criteri di scadenza per i gruppi di Office 365

È ora possibile gestire il ciclo di vita dei gruppi di Office 365 impostando specifici criteri di scadenza. È possibile impostare i criteri di scadenza dei soli gruppi di Office 365 in Azure Active Directory (Azure AD). 

Dopo l'impostazione della scadenza di un gruppo:
-   Quando la scadenza si avvicina, i proprietari del gruppo ricevono la notifica della necessità di rinnovare il gruppo
-   I gruppi che non vengono rinnovati vengono eliminati
-   I gruppi di Office 365 eliminati possono essere ripristinati entro 30 giorni dai proprietari o dall'amministratore.

> [!NOTE]
> Per configurare e usare i criteri di scadenza per i gruppi di Office 365, è necessario disporre di licenze di Azure AD Premium per tutti i membri dei gruppi a cui vengono applicati tali criteri.

Per informazioni su come scaricare e installare i cmdlet di Azure AD PowerShell, vedere [Azure Active Directory PowerShell for Graph 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) (Azure Active Directory PowerShell per Graph 2.0.0.137).

## <a name="roles-and-permissions"></a>Ruoli e autorizzazioni
Di seguito sono elencati i ruoli che possono configurare e usare la scadenza per i gruppi di Office 365 in Azure AD.

Ruolo | Autorizzazioni
-------- | --------
Amministratore globale o amministratore account utente | Questo ruolo consente di creare, leggere, aggiornare o eliminare le impostazioni dei criteri di scadenza per i gruppi di Office 365<br>Questo ruolo consente di rinnovare qualsiasi gruppo di Office 365
Utente | Questo ruolo consente di rinnovare un gruppo di Office 365 di cui è proprietario<br>Questo ruolo consente di ripristinare un gruppo di Office 365 di cui è proprietario<br>Questo ruolo consente di leggere le impostazioni dei criteri di scadenza

Per altre informazioni sulle autorizzazioni per ripristinare un gruppo eliminato, vedere [Ripristinare un gruppo di Office 365 eliminato in Azure Active Directory](active-directory-groups-restore-azure-portal.md).

## <a name="set-group-expiration"></a>Impostare la scadenza dei gruppi

1. Aprire l'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con un account di amministratore globale per il tenant di Azure AD.

2. Selezionare **Gruppi** e quindi **Scadenza** per aprire le impostazioni di scadenza.
  
  ![Pannello Scadenza](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. Nel pannello **Scadenza** è possibile:

  * Impostare la durata del gruppo in giorni. È possibile selezionare uno dei valori predefiniti oppure un valore personalizzato (che deve essere di 31 giorni o più). 
  * Specificare un indirizzo e-mail per l'invio delle notifiche di rinnovo e di scadenza quando un gruppo non ha un proprietario. 
  * Selezionare i gruppi di Office 365 che scadono. È possibile abilitare la scadenza per **Tutti** i gruppi di Office 365, scegliere di abilitare solo i gruppi di Office 365 **Selezionati** oppure selezionare **Nessuno** per disabilitare la scadenza per tutti i gruppi.
  * Al termine salvare le impostazioni facendo clic su **Salva**.


Le notifiche tramite posta elettronica di questo tipo vengono inviate ai proprietari del gruppo di Office 365 30 giorni, 15 giorni e 1 giorno prima della scadenza del gruppo.

![Notifica di scadenza tramite posta elettronica](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

Dalla notifica di posta elettronica **Rinnova il gruppo** i proprietari del gruppo possono accedere direttamente alla pagina dei dettagli del gruppo nel Riquadro di accesso. In questa area è possibile ottenere altre informazioni sul gruppo, ad esempio la descrizione, la data dell'ultimo rinnovo, la data di scadenza, e anche procedere con il rinnovo del gruppo. La pagina dei dettagli del gruppo include anche collegamenti alle risorse del gruppo di Office 365, che consentono al proprietario del gruppo di visualizzare facilmente il contenuto e l'attività del gruppo.

Alla scadenza il gruppo viene eliminato, un giorno dopo la data di scadenza. Viene inviata una notifica tramite posta elettronica simile alla seguente ai proprietari del gruppo di Office 365 per informarli della scadenza e della conseguente eliminazione del gruppo di Office 365.

![Notifica tramite posta elettronica per l'eliminazione del gruppo](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

Il gruppo può essere ripristinato entro 30 giorni dalla relativa eliminazione selezionando **Ripristina gruppo** oppure usando i cmdlet di PowerShell, come descritto in [Ripristinare un gruppo di Office 365 eliminato in Azure Active Directory](active-directory-groups-restore-azure-portal.md).
    
Se il gruppo che si sta ripristinando contiene documenti, siti di SharePoint o altri oggetti persistenti, potrebbero essere necessarie fino a 24 ore per ripristinare completamente il gruppo e il relativo contenuto.

> [!NOTE]
> * Quando si configura la scadenza per la prima volta, la scadenza di tutti i gruppi creati prima dell'intervallo di scadenza scelto viene impostata su 30 giorni. La prima notifica di rinnovo viene inviata tramite posta elettronica entro un giorno. 
>   Ad esempio, il gruppo A è stato creato da 400 giorni e l'intervallo di scadenza è impostato su 180 giorni. Quando si applicano le impostazioni di scadenza, il gruppo A ha 30 giorni prima di essere eliminato, a meno che il proprietario non lo rinnovi.
> * Attualmente può essere configurato un solo criterio di scadenza per i gruppi di Office 365 di un tenant.
> * Quando un gruppo dinamico viene eliminato e ripristinato, viene considerato come un nuovo gruppo e popolato nuovamente in base alla regola. Questo processo può richiedere fino a 24 ore.

## <a name="how-office-365-group-expiration-works-with-a-mailbox-on-legal-hold"></a>Come funziona la scadenza dei gruppi di Office 365 con una cassetta postale in blocco a fini giudiziari
Quando un gruppo scade e viene eliminato, 30 giorni i dati del gruppo presenti in app come Planner, Siti o Teams vengono eliminati definitivamente, mentre la cassetta postale del gruppo che è in blocco a fini giudiziari viene mantenuta. L'amministratore può usare i cmdlet di Exchange per ripristinare la cassetta postale e recuperare i dati. 

## <a name="how-office-365-group-expiration-works-with-retention-policy"></a>Come funziona la scadenza dei gruppi di Office 365 con i criteri di conservazione
I criteri di conservazione vengono configurati tramite il Centro sicurezza e conformità. Se per i gruppi di Office 365 sono stati configurati criteri di conservazione, quando un gruppo scade e viene eliminato le conversazioni presenti nella cassetta postale del gruppo e i file presenti nel sito del gruppo vengono mantenuti nell'apposito contenitore per uno specifico numero di giorni definito nei criteri di conservazione. Dopo la scadenza gli utenti non potranno vedere il gruppo o i relativi contenuti, ma potranno recuperare i dati del sito e della cassetta postale tramite e-discovery.

## <a name="powershell-examples"></a>Esempi di PowerShell
Di seguito sono riportati alcuni esempi di come è possibile usare i cmdlet di PowerShell per configurare le impostazioni di scadenza per i gruppi di Office 365 nel tenant:

1. Installare il modulo di anteprima PowerShell v2.0 (2.0.0.137) e accedere al prompt di PowerShell:
  ````
  Install-Module -Name AzureADPreview
  connect-azuread 
  ````
2. Configurare le impostazioni di scadenza New-AzureADMSGroupLifecyclePolicy. Questo cmdlet imposta la durata di tutti i gruppi di Office 365 del tenant su 365 giorni. Le notifiche di rinnovo per i gruppi di Office 365 senza proprietari verranno inviate a emailaddress@contoso.com.
  
  ````
  New-AzureADMSGroupLifecyclePolicy -GroupLifetimeInDays 365 -ManagedGroupTypes All -AlternateNotificationEmails emailaddress@contoso.com
  ````
3. Recuperare i criteri esistenti Get-AzureADMSGroupLifecyclePolicy. Questo cmdlet recupera le impostazioni di scadenza correnti dei gruppi di Office 365 che sono state configurate. Questo esempio contiene gli elementi seguenti:
  * ID dei criteri 
  * Durata di tutti i gruppi di Office 365 del tenant impostata su 365 giorni
  * Notifiche di rinnovo per i gruppi di Office 365 senza proprietari che verranno inviate a emailaddress@contoso.com
  
  ````
  Get-AzureADMSGroupLifecyclePolicy
  
  ID                                    GroupLifetimeInDays ManagedGroupTypes AlternateNotificationEmails
  --                                    ------------------- ----------------- ---------------------------
  26fcc232-d1c3-4375-b68d-15c296f1f077  365                 All               emailaddress@contoso.com
  ```` 
   
4. Aggiornare i criteri esistenti Set-AzureADMSGroupLifecyclePolicy. Questo cmdlet consente di aggiornare un criterio esistente. Nell'esempio seguente la durata del gruppo nei criteri esistenti viene modificata da 365 giorni a 180 giorni. 
  
  ````
  Set-AzureADMSGroupLifecyclePolicy -Id “26fcc232-d1c3-4375-b68d-15c296f1f077”   -GroupLifetimeInDays 180 -AlternateNotificationEmails "emailaddress@contoso.com"
  ````
  
5. Aggiungere gruppi specifici al criterio Add-AzureADMSLifecyclePolicyGroup. Questo cmdlet aggiunge un gruppo ai criteri del ciclo di vita. Ad esempio: 
  
  ````
  Add-AzureADMSLifecyclePolicyGroup -Id “26fcc232-d1c3-4375-b68d-15c296f1f077” -groupId "cffd97bd-6b91-4c4e-b553-6918a320211c"
  ````
  
6. Rimuovere il criterio esistente Remove-AzureADMSGroupLifecyclePolicy. Questo cmdlet elimina le impostazioni di scadenza dei gruppi di Office 365, ma richiede l'ID di tali criteri. Verrà così disabilitata la scadenza per i gruppi di Office 365. 
  
  ````
  Remove-AzureADMSGroupLifecyclePolicy -Id “26fcc232-d1c3-4375-b68d-15c296f1f077”
  ````
  
I cmdlet seguenti possono essere usati per configurare i criteri in modo più dettagliato. Per altre informazioni, vedere la [documentazione di PowerShell](https://docs.microsoft.com/en-us/powershell/module/azuread/?view=azureadps-2.0-preview&branch=master#groups).

* Get-AzureADMSGroupLifecyclePolicy
* New-AzureADMSGroupLifecyclePolicy
* Get-AzureADMSGroupLifecyclePolicy
* Set-AzureADMSGroupLifecyclePolicy
* Remove-AzureADMSGroupLifecyclePolicy
* Add-AzureADMSLifecyclePolicyGroup
* Remove-AzureADMSLifecyclePolicyGroup
* Reset-AzureADMSLifeCycleGroup   
* Get-AzureADMSLifecyclePolicyGroup

## <a name="next-steps"></a>Passaggi successivi
Questi articoli forniscono informazioni aggiuntive sui gruppi di Azure AD.

* [Vedere i gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire i membri di un gruppo](active-directory-groups-members-azure-portal.md)
* [Gestire le appartenenze di un gruppo](active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-membership-azure-portal.md)
