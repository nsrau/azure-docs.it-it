---
title: Rimuovere l&quot;assegnazione di un utente o gruppo da un&quot;app aziendale in anteprima di Azure Active Directory | Documentazione Microsoft
description: Come rimuovere l&quot;assegnazione di accesso di un utente o gruppo da un&quot;app aziendale in anteprima di Azure Active Directory
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 7b2d365b-ae92-477f-9702-353cc6acc5ea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b38a613a1f93d9c36254f7642655f716e048e9e3
ms.lasthandoff: 11/17/2016


---
# <a name="remove-a-user-or-group-assignment-from-an-enterprise-app-in-azure-active-directory-preview"></a>Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale in anteprima di Azure Active Directory
Rimuovere l'assegnazione di accesso di un utente o un gruppo a una delle applicazioni aziendali in anteprima di Azure Active Directory (Azure AD) è un'operazione semplice. [Funzionalità disponibili nell'anteprima](active-directory-preview-explainer.md)  È necessario disporre delle autorizzazioni appropriate per gestire l'app aziendale. Nell'anteprima corrente è necessario essere amministratore globale della directory.

## <a name="how-do-i-remove-a-user-or-group-assignment"></a>Come è possibile rimuovere l'assegnazione di un utente o un gruppo?
1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Altri servizi**, immettere **Azure Active Directory** nella casella di testo e quindi selezionare **Invio**.
3. Nel pannello **Azure Active Directory - *nomedirectory***, vale a dire il pannello Azure AD per la directory che si sta gestendo, selezionare **Applicazioni aziendali**.

    ![Apertura di app aziendali](./media/active-directory-coreapps-remove-assignment-user-azure-portal/open-enterprise-apps.png)
4. Nel pannello **Applicazioni aziendali** selezionare **Tutte le applicazioni**. Verrà visualizzato un elenco di app che è possibile gestire.
5. Nel pannello **Applicazioni aziendali - All applications** (Tutte le applicazioni) selezionare un'app.
6. Nel pannello ***nome app***, ovvero il pannello con il nome dell'app selezionata nel titolo, selezionare **Utenti e gruppi**.

    ![Selezione di utenti o gruppi](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-app-users.png)
7. Nel pannello ***nomeapp*** di **assegnazione utente e gruppo** selezionare uno o più utenti o gruppi e quindi fare clic sul comando **Rimuovi**. Confermare la decisione al prompt dei comandi.

    ![Selezione del comando Rimuovi](./media/active-directory-coreapps-remove-assignment-user-azure-portal/remove-users.png)

## <a name="next-steps"></a>Passaggi successivi
* [Visualizzare tutti i gruppi personali](active-directory-groups-view-azure-portal.md)
* [Assegnare un utente o gruppo a un'app aziendale](active-directory-coreapps-assign-user-azure-portal.md)
* [Disabilitare l'accesso degli utenti per un'app aziendale](active-directory-coreapps-disable-app-azure-portal.md)
* [Modificare il nome o il logo di un'app aziendale](active-directory-coreapps-change-app-logo-user-azure-portal.md)

