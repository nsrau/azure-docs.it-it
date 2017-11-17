---
title: Creare un gruppo per utenti in Azure Active Directory | Microsoft Docs
description: 'Procedura: Creare un gruppo in Azure Active Directory e aggiungere membri al gruppo'
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: cc5f232a-1e77-45c2-b28b-1fcb4621725c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/04/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c36ffd0a567b665d6ea0d02d2aa0171965d1b8b
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2017
---
# <a name="create-a-group-and-add-members-in-azure-active-directory"></a>Creare un gruppo e aggiungere membri in Azure Active Directory
> [!div class="op_single_selector"]
> * [Portale di Azure](active-directory-groups-create-azure-portal.md)
> * [PowerShell](active-directory-accessmanagement-groups-settings-v2-cmdlets.md)
>
>

Questo articolo descrive come creare e popolare un nuovo gruppo in Azure Active Directory. Usare un gruppo per eseguire attività di gestione come l'assegnazione di licenze o autorizzazioni per diversi utenti o dispositivi contemporaneamente.

## <a name="how-do-i-create-a-group"></a>Come si crea un gruppo?
1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Altri servizi**, immettere **Utenti e gruppi** nella casella di testo e quindi selezionare **Invio**.

   ![Apertura di Gestione utenti](./media/active-directory-groups-create-azure-portal/search-user-management.png)
3. Nel pannello **Utenti e gruppi** selezionare **Tutti i gruppi**.

   ![Apertura del pannello Gruppi](./media/active-directory-groups-create-azure-portal/view-groups-blade.png)
4. Nel pannello **Utenti e gruppi - Tutti i gruppi** selezionare il comando **Aggiungi**.

   ![Selezione del comando Aggiungi](./media/active-directory-groups-create-azure-portal/add-group-command.png)
5. Nel pannello **Gruppo** aggiungere un nome e una descrizione per il gruppo.
6. Per selezionare i membri da aggiungere al gruppo, selezionare **Assegnato** nella casella **Tipo di appartenenza** e quindi selezionare **Membri**. Per altre informazioni su come gestire l'appartenenza di un gruppo in modo dinamico, vedere [Utilizzo degli attributi per creare regole avanzate per l'appartenenza al gruppo](active-directory-groups-dynamic-membership-azure-portal.md).

   ![Selezione dei membri da aggiungere](./media/active-directory-groups-create-azure-portal/select-members.png)
7. Nel pannello **Membri** selezionare uno o più utenti o dispositivi da aggiungere al gruppo e fare clic sul pulsante **Seleziona** nella parte inferiore del pannello per aggiungerli al gruppo. La visualizzazione nella casella **Utente** viene filtrata in base alla corrispondenza con l'immissione di una parte di un nome utente o di dispositivo. I caratteri jolly non sono consentiti nella casella.
8. Dopo avere aggiunto i membri al gruppo, selezionare **Crea** nel pannello **Gruppo**.    

   ![Creare una conferma del gruppo](./media/active-directory-groups-create-azure-portal/create-group-confirmation.png)


## <a name="next-steps"></a>Passaggi successivi
Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Vedere i gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire i membri di un gruppo](active-directory-groups-members-azure-portal.md)
* [Gestire le appartenenze di un gruppo](active-directory-groups-membership-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-membership-azure-portal.md)
