---
title: "Funzionalità di anteprima di scadenza dei gruppi di Office 365 in Azure Active Directory | Microsoft Docs"
description: Come configurare la scadenza dei gruppi di Office 365 in Azure Active Directory (anteprima)
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: 8a43df84fd050d7b4bd8d937b8c55e744cb805d3
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/18/2017
---
# <a name="configure-office-365-groups-expiration-preview"></a>Configurare la scadenza dei gruppi di Office 365 (anteprima)

È ora possibile gestire il ciclo di vita dei gruppi di Office 365 impostando la scadenza per qualsiasi gruppo di Office 365 selezionato. Dopo che la scadenza è stata impostata, ai proprietari dei gruppi viene chiesto di rinnovare il gruppo, se è ancora necessario. Qualsiasi gruppo di Office 365 che non viene rinnovato sarà eliminato. Qualsiasi gruppo di Office 365 eliminato può essere ripristinato entro 30 giorni dall'amministratore o dai proprietari del gruppo.  


> [!NOTE]
> È possibile impostare la scadenza solo per i gruppi di Office 365.
>
> Per impostare la scadenza per i gruppi di Office 365, è necessario che venga assegnata una licenza di Azure AD Premium a
>   - L'amministratore che configura le impostazioni di scadenza per il tenant
>   - Tutti i membri dei gruppi selezionati per questa impostazione

## <a name="set-office-365-groups-expiration"></a>Impostare la scadenza dei gruppi di Office 365

1. Aprire l'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con un account di amministratore globale per il tenant di Azure AD.

2. Aprire Azure AD e selezionare **Utenti e gruppi**.

3. Selezionare **Impostazioni di gruppo** e quindi **Scadenza** per aprire le impostazioni di scadenza.
  
  ![Pannello Scadenza](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. Nel pannello **Scadenza** è possibile:

  * Impostare la durata del gruppo in giorni. È possibile selezionare uno dei valori predefiniti oppure un valore personalizzato (che deve essere di 31 giorni o più). 
  * Specificare un indirizzo e-mail per l'invio delle notifiche di rinnovo e di scadenza quando un gruppo non ha un proprietario. 
  * Selezionare i gruppi di Office 365 che scadono. È possibile abilitare la scadenza per **tutti** i gruppi di Office 365, selezionare alcuni tra i gruppi di Office 365 oppure selezionare **Nessuno** per disabilitare la scadenza per tutti i gruppi.
  * Al termine salvare le impostazioni facendo clic su **Salva**.

Per istruzioni su come scaricare e installare il modulo di Microsoft PowerShell per configurare la scadenza per i gruppi di Office 365 tramite PowerShell, vedere [Azure Active Directory V2 PowerShell Module - Public Preview Release 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) (Modulo di PowerShell per Azure Active Directory V2 - Versione di anteprima pubblica 2.0.0.137).

Le notifiche tramite posta elettronica di questo tipo vengono inviate ai proprietari del gruppo di Office 365 30 giorni, 15 giorni e 1 giorno prima della scadenza del gruppo.

![Notifica di scadenza tramite posta elettronica](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

Il proprietario del gruppo può quindi selezionare **Renew group** (Rinnova gruppo) per rinnovare il gruppo di Office 365 oppure **Go to group** (Vai al gruppo) per visualizzare i membri del gruppo e altri dettagli.

Alla scadenza il gruppo viene eliminato, un giorno dopo la data di scadenza. Viene inviata una notifica tramite posta elettronica simile alla seguente ai proprietari del gruppo di Office 365 per informarli della scadenza e della conseguente eliminazione del gruppo di Office 365.

![Notifica tramite posta elettronica per l'eliminazione del gruppo](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

Il gruppo può essere ripristinato selezionando **Restore group** (Ripristina gruppo) oppure usando i cmdlet di PowerShell, come descritto in [Ripristinare un gruppo di Office 365 eliminato in Azure Active Directory] (https://docs.microsoft.com/azure/active-directory/active-directory-groups-restore-azure-portal).
    
Se il gruppo che si sta ripristinando contiene documenti, siti di SharePoint o altri oggetti persistenti, potrebbero essere necessarie fino a 24 ore per ripristinare completamente il gruppo e il relativo contenuto.

> [!NOTE]
> * Quando si distribuiscono le impostazioni di scadenza, potrebbero esserci alcuni gruppi anteriori all'intervallo di scadenza. Questi gruppi non vengono eliminati immediatamente, ma ne viene impostata la scadenza dopo 30 giorni. La prima notifica di rinnovo viene inviata tramite posta elettronica entro un giorno. Ad esempio, il gruppo A è stato creato da 400 giorni e l'intervallo di scadenza è impostato su 180 giorni. Quando si applicano le impostazioni di scadenza, il gruppo A ha 30 giorni prima di essere eliminato, a meno che il proprietario non lo rinnovi.
> * Quando un gruppo dinamico viene eliminato e ripristinato, viene considerato come un nuovo gruppo e popolato nuovamente in base alla regola. Questo processo può richiedere fino a 24 ore.

## <a name="next-steps"></a>Passaggi successivi
Questi articoli forniscono informazioni aggiuntive sui gruppi di Azure AD.

* [Vedere i gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire i membri di un gruppo](active-directory-groups-members-azure-portal.md)
* [Gestire le appartenenze di un gruppo](active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-membership-azure-portal.md)
