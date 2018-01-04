---
title: Scadenza dei gruppi di Office 365 in Azure Active Directory | Microsoft Docs
description: Come configurare la scadenza dei gruppi di Office 365 in Azure Active Directory (anteprima)
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: curtand
ms.reviewer: kairaz.contractor
ms.custom: it-pro
ms.openlocfilehash: d485d2e7d22ea79a87dc52dbc063a811f4a1a2ec
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2017
---
# <a name="configure-expiration-for-office-365-groups-preview"></a>Configurare la scadenza per i gruppi di Office 365 (anteprima)

È ora possibile gestire il ciclo di vita dei gruppi di Office 365 impostando funzionalità di scadenza per questi ultimi. È possibile configurare la scadenza dei soli gruppi di Office 365 in Azure Active Directory (Azure AD). Dopo l'impostazione della scadenza di un gruppo:
-   Quando la scadenza si avvicina, i proprietari del gruppo ricevono la notifica della necessità di rinnovare il gruppo
-   I gruppi che non vengono rinnovati vengono eliminati
-   I gruppi di Office 365 eliminati possono essere ripristinati entro 30 giorni dai proprietari o dall'amministratore.

> [!NOTE]
> L'impostazione della scadenza per i gruppi di Office 365 richiede una licenza Azure AD Premium o Azure AD Basic EDU per tutti i membri dei gruppi a cui sono applicate le impostazioni relative alla scadenza.
> 
> Per configurare questi criteri per la prima volta, i clienti con licenza Azure AD Basic EDU devono usare i cmdlet di Azure Active Directory PowerShell. In seguito è possibile aggiornare le impostazioni relative alla scadenza in PowerShell o nel portale di Azure AD con un account amministratore di account utente o con un account amministratore globale nel tenant di Azure AD.

Per informazioni su come scaricare e installare i cmdlet di Azure AD PowerShell, vedere [Azure Active Directory PowerShell for Graph - Public Preview Release 2.0.0.137](https://www.powershellgallery.com/packages/AzureADPreview/2.0.0.137) (Azure Active Directory PowerShell per Graph: versione di anteprima pubblica 2.0.0.137).

## <a name="set-group-expiration"></a>Impostare la scadenza dei gruppi

1. Aprire l'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con un account di amministratore globale per il tenant di Azure AD.

2. Aprire Azure AD e selezionare **Utenti e gruppi**.

3. Selezionare **Impostazioni di gruppo** e quindi **Scadenza** per aprire le impostazioni di scadenza.
  
  ![Pannello Scadenza](./media/active-directory-groups-lifecycle-azure-portal/expiration-settings.png)

4. Nel pannello **Scadenza** è possibile:

  * Impostare la durata del gruppo in giorni. È possibile selezionare uno dei valori predefiniti oppure un valore personalizzato (che deve essere di 31 giorni o più). 
  * Specificare un indirizzo e-mail per l'invio delle notifiche di rinnovo e di scadenza quando un gruppo non ha un proprietario. 
  * Selezionare i gruppi di Office 365 che scadono. È possibile abilitare la scadenza per **tutti** i gruppi di Office 365, selezionare alcuni tra i gruppi di Office 365 oppure selezionare **Nessuno** per disabilitare la scadenza per tutti i gruppi.
  * Al termine salvare le impostazioni facendo clic su **Salva**.


Le notifiche tramite posta elettronica di questo tipo vengono inviate ai proprietari del gruppo di Office 365 30 giorni, 15 giorni e 1 giorno prima della scadenza del gruppo.

![Notifica di scadenza tramite posta elettronica](./media/active-directory-groups-lifecycle-azure-portal/expiration-notification.png)

Il proprietario del gruppo può quindi selezionare **Renew group** (Rinnova gruppo) per rinnovare il gruppo di Office 365 oppure **Go to group** (Vai al gruppo) per visualizzare i membri del gruppo e altri dettagli.

Alla scadenza il gruppo viene eliminato, un giorno dopo la data di scadenza. Viene inviata una notifica tramite posta elettronica simile alla seguente ai proprietari del gruppo di Office 365 per informarli della scadenza e della conseguente eliminazione del gruppo di Office 365.

![Notifica tramite posta elettronica per l'eliminazione del gruppo](./media/active-directory-groups-lifecycle-azure-portal/deletion-notification.png)

Il gruppo può essere ripristinato selezionando **Restore group** (Ripristina gruppo) oppure usando i cmdlet di PowerShell, come descritto in [Ripristinare un gruppo di Office 365 eliminato in Azure Active Directory] (https://docs.microsoft.com/azure/active-directory/active-directory-groups-restore-azure-portal).
    
Se il gruppo che si sta ripristinando contiene documenti, siti di SharePoint o altri oggetti persistenti, potrebbero essere necessarie fino a 24 ore per ripristinare completamente il gruppo e il relativo contenuto.

> [!NOTE]
> * Quando si configura la scadenza per la prima volta, la scadenza di tutti i gruppi creati prima dell'intervallo di scadenza scelto viene impostata su 30 giorni. La prima notifica di rinnovo viene inviata tramite posta elettronica entro un giorno. 
>   Ad esempio, il gruppo A è stato creato da 400 giorni e l'intervallo di scadenza è impostato su 180 giorni. Quando si applicano le impostazioni di scadenza, il gruppo A ha 30 giorni prima di essere eliminato, a meno che il proprietario non lo rinnovi.
> * Quando un gruppo dinamico viene eliminato e ripristinato, viene considerato come un nuovo gruppo e popolato nuovamente in base alla regola. Questo processo può richiedere fino a 24 ore.

## <a name="next-steps"></a>Passaggi successivi
Questi articoli forniscono informazioni aggiuntive sui gruppi di Azure AD.

* [Vedere i gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire i membri di un gruppo](active-directory-groups-members-azure-portal.md)
* [Gestire le appartenenze di un gruppo](active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-membership-azure-portal.md)
