---

title: Gestire i gruppi appartenenti a un gruppo in Azure Active Directory | Microsoft Docs
description: I gruppi possono contenere altri gruppi in Azure Active Directory. Di seguito viene illustrato come gestire queste appartenenze.
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e785c2d0-7724-47d4-a56e-c58280c08a14
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/04/2017
ms.author: curtand
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 08e04a6590176c4084ca47b4bd6cbb22500eca2d
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017


---
# <a name="manage-to-which-groups-a-group-belongs-in-your-azure-active-directory-tenant"></a>Gestire i gruppi a cui appartiene il proprio gruppo nel tenant di Azure Active Directory
I gruppi possono contenere altri gruppi in Azure Active Directory. Di seguito viene illustrato come gestire queste appartenenze.

## <a name="how-do-i-find-the-groups-my-group-is-a-member-of"></a>Come è possibile trovare i gruppi di cui il gruppo dell'utente è un membro?
1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Altri servizi**, immettere **Utenti e gruppi** nella casella di testo e quindi premere **INVIO**.

   ![Apertura di Gestione utenti](./media/active-directory-groups-membership-azure-portal/search-user-management.png)
3. Nel pannello **Utenti e gruppi** selezionare **Tutti i gruppi**.

   ![Apertura del pannello Gruppi](./media/active-directory-groups-membership-azure-portal/view-groups-blade.png)
4. Nel pannello **Utenti e gruppi - Tutti i gruppi** selezionare un gruppo.
5. Nel pannello **Gruppo - *nomegruppo*** selezionare **Appartenenza a gruppi**.

   ![Apertura del pannello Appartenenza ai gruppi](./media/active-directory-groups-membership-azure-portal/group-membership-blade.png)
6. Per aggiungere il gruppo come membro di un altro gruppo, nel pannello **Gruppo - Appartenenza ai gruppi** selezionare il comando **Aggiungi**.
7. Selezionare un gruppo nel pannello **Seleziona gruppo** e quindi fare clic sul pulsante **Seleziona** nella parte inferiore del pannello. È possibile aggiungere il gruppo a un solo gruppo alla volta. La visualizzazione nella casella **Utente** viene filtrata in base alla corrispondenza con l'immissione di una parte di un nome utente o di dispositivo. I caratteri jolly non sono consentiti nella casella.

   ![Aggiungere l'appartenenza a un gruppo](./media/active-directory-groups-membership-azure-portal/add-group-membership.png)
8. Per rimuovere il proprio gruppo come membro di un altro gruppo, nel pannello **Gruppo - Appartenenza ai gruppi** selezionare un gruppo.
9. Nel pannello ***nomegruppo*** selezionare il comando **Rimuovi** e confermare la scelta quando viene richiesto.

   ![Comando Rimuovi appartenenza](./media/active-directory-groups-membership-azure-portal/remove-group-membership.png)
10. Al termine della modifica delle appartenenze dei gruppi per il proprio gruppo, selezionare **Salva**.

## <a name="additional-information"></a>Informazioni aggiuntive
Questi articoli forniscono informazioni aggiuntive su Azure Active Directory.

* [Vedere i gruppi esistenti](active-directory-groups-view-azure-portal.md)
* [Creare un nuovo gruppo e aggiunta di membri](active-directory-groups-create-azure-portal.md)
* [Gestire le impostazioni di un gruppo](active-directory-groups-settings-azure-portal.md)
* [Gestire i membri di un gruppo](active-directory-groups-members-azure-portal.md)
* [Gestire le regole dinamiche per gli utenti in un gruppo](active-directory-groups-dynamic-membership-azure-portal.md)

