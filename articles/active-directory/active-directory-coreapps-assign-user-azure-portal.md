---
title: Assegnare un utente o un gruppo a un&quot;app aziendale nell&quot;anteprima di Azure Active Directory | Documentazione Microsoft
description: Come selezionare un&quot;app aziendale a cui assegnare un utente o gruppo in anteprima di Azure Active Directory
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 5817ad48-d916-492b-a8d0-2ade8c50a224
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 18330aa5e0fe8e3bbad6c266f823a4969b9b8b6c
ms.lasthandoff: 11/17/2016


---
# <a name="assign-a-user-or-group-to-an-enterprise-app-in-azure-active-directory-preview"></a>Assegnare un utente o un gruppo a un'app aziendale in anteprima di Azure Active Directory
È facile assegnare un utente o un gruppo alle applicazioni aziendali in anteprima di Azure Active Directory (Azure AD). [Cosa c'è nell'anteprima?](active-directory-preview-explainer.md)  È necessario disporre delle autorizzazioni appropriate per gestire l'app aziendale. Nell'anteprima corrente è necessario essere amministratore globale della directory.

## <a name="how-do-i-assign-user-access-to-an-enterprise-app"></a>Come si assegna l'accesso utente a un'app aziendale?
1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Altri servizi**, immettere Azure Active Directory nella casella di testo e quindi premere **INVIO**.
3. Nel pannello **Azure Active Directory - *nomedirectory***, vale a dire il pannello Azure AD per la directory che si sta gestendo, selezionare **Applicazioni aziendali**.

    ![Apertura di app aziendali](./media/active-directory-coreapps-assign-user-azure-portal/open-enterprise-apps.png)
4. Nel pannello **Applicazioni aziendali** selezionare **Tutte le applicazioni**. Verrà visualizzato un elenco di app che è possibile gestire.
5. Nel pannello **Applicazioni aziendali - All applications** (Tutte le applicazioni) selezionare un'app.
6. Nel pannello ***nome app***, ovvero il pannello con il nome dell'app selezionata nel titolo, selezionare **Utenti e gruppi**.

    ![Selezione del comando All applications (Tutte le applicazioni)](./media/active-directory-coreapps-assign-user-azure-portal/select-app-users.png)
7. Nel pannello ***nome app*** **- Assegnazione di utenti e gruppi** selezionare il comando **Aggiungi**.
8. Nel pannello **Aggiungi assegnazione** selezionare **Utenti e gruppi**.

    ![Assegnare un utente o gruppo all'app](./media/active-directory-coreapps-assign-user-azure-portal/assign-users.png)
9. Nel pannello **Utenti e gruppi** selezionare uno o più utenti o gruppi dall'elenco e fare clic sul pulsante **Seleziona** nella parte inferiore del pannello.
10. Nel pannello **Aggiungi assegnazione** selezionare **Ruolo**. Nel pannello **Selezionare un ruolo** scegliere il ruolo da applicare ai gruppi o utenti selezionati, quindi fare clic su **OK** nella parte inferiore del pannello.
11. Nella parte inferiore del pannello **Aggiungi assegnazione** fare clic sul pulsante **Assegna**. Gli utenti o i gruppi assegnati avranno le autorizzazioni definite dal ruolo selezionato per questa app aziendale.

## <a name="next-steps"></a>Passaggi successivi
* [Visualizzare tutti i gruppi personali](active-directory-groups-view-azure-portal.md)
* [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](active-directory-coreapps-remove-assignment-azure-portal.md)
* [Disabilitare l'accesso degli utenti per un'app aziendale](active-directory-coreapps-disable-app-azure-portal.md)
* [Modificare il nome o il logo di un'app aziendale](active-directory-coreapps-change-app-logo-user-azure-portal.md)

