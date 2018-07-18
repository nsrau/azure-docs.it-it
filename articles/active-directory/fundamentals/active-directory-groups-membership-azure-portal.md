---
title: Gestire i gruppi appartenenti a un gruppo in Azure AD | Microsoft Docs
description: I gruppi possono contenere altri gruppi in Azure Active Directory. Di seguito viene illustrato come gestire queste appartenenze.
services: active-directory
documentationcenter: ''
author: eross-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.component: fundamentals
ms.topic: quickstart
ms.date: 10/10/2017
ms.author: lizross
ms.custom: it-pro
ms.reviewer: krbain
ms.openlocfilehash: 8a71677ae3ceb5617f0a817a8eff438d5e3f2774
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2018
ms.locfileid: "37860366"
---
# <a name="manage-to-which-groups-a-group-belongs-in-your-azure-active-directory-tenant"></a>Gestire i gruppi a cui appartiene il proprio gruppo nel tenant di Azure Active Directory
I gruppi possono contenere altri gruppi in Azure Active Directory. Di seguito viene illustrato come gestire queste appartenenze.

## <a name="how-do-i-find-the-groups-of-which-my-group-is-a-member"></a>Come è possibile trovare i gruppi di cui il gruppo dell'utente è un membro?
1. Accedere all'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con un account amministratore globale per la directory.
2. Selezionare **Utenti e gruppi**.

   ![Immagine dell'apertura di Utenti e gruppi](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
1. Selezionare **Tutti i gruppi**.

   ![Immagine della selezione dei gruppi](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
1. Selezionare un gruppo.
2. Selezionare **Appartenenze a gruppi**.

   ![Immagine dell'apertura delle appartenenze a gruppi](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
1. Per aggiungere il gruppo come membro di un altro gruppo, nel pannello **Gruppo - Appartenenza ai gruppi** selezionare il comando **Aggiungi**.
2. Selezionare un gruppo nel pannello **Seleziona gruppo** e quindi fare clic sul pulsante **Seleziona** nella parte inferiore del pannello. È possibile aggiungere il gruppo a un solo gruppo alla volta. La visualizzazione nella casella **Utente** viene filtrata in base alla corrispondenza con l'immissione di una parte di un nome utente o di dispositivo. I caratteri jolly non sono consentiti nella casella.

   ![Aggiungere l'appartenenza a un gruppo](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. Per rimuovere il proprio gruppo come membro di un altro gruppo, nel pannello **Gruppo - Appartenenza ai gruppi** selezionare un gruppo.
9. Selezionare il comando **Rimuovi** e confermare la scelta quando viene richiesto.

   ![Comando Rimuovi appartenenza](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. Al termine della modifica delle appartenenze dei gruppi per il proprio gruppo, selezionare **Salva**.

## <a name="additional-information"></a>Informazioni aggiuntive
Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Vedere i gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Creare un nuovo gruppo e aggiunta di membri](active-directory-groups-create-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire i membri di un gruppo](active-directory-groups-members-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](../users-groups-roles/groups-dynamic-membership.md)
