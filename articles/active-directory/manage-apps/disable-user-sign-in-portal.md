---
title: Disabilitare gli accessi degli utenti per un'app aziendale in Azure Active Directory | Microsoft Docs
description: Come disabilitare un'applicazione aziendale in modo che gli utenti non possano accedervi in Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/28/2017
ms.author: barbkess
ms.reviewer: asteen
ms.custom: it-pro
ms.openlocfilehash: 39e926a392cbd87eff23e25d9708792ec7c40a2c
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2018
ms.locfileid: "39368709"
---
# <a name="disable-user-sign-ins-for-an-enterprise-app-in-azure-active-directory"></a>Disabilitare gli accessi utente per un'app aziendale in Azure Active Directory
Disabilitare un'applicazione aziendale in modo da impedirne l'accesso da parte degli utenti in Azure Active Directory (Azure AD) è un'operazione facile. È necessario disporre delle autorizzazioni appropriate per gestire l'app aziendale ed essere amministratore globale della directory.

## <a name="how-do-i-disable-user-sign-ins"></a>Come è possibile disabilitare l'accesso degli utenti?
1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Tutti i servizi**, immettere **Azure Active Directory** nella casella di testo e quindi premere **INVIO**.
3. Nel riquadro **Azure Active Directory** -  ***nomedirectory***, vale a dire il riquadro Azure AD per la directory che si sta gestendo, selezionare **Applicazioni aziendali**.

    ![Apertura di app aziendali](./media/disable-user-sign-in-portal/open-enterprise-apps.png)
4. Nel riquadro **Applicazioni aziendali** selezionare **Tutte le applicazioni**. Viene visualizzato un elenco di app che è possibile gestire.
5. Nel riquadro **Applicazioni aziendali - Tutte le applicazioni** selezionare un'app.
6. Nel riquadro ***nomeapp***, vale a dire il riquadro con il nome dell'app selezionata nel titolo, selezionare **Proprietà**.

    ![Selezione del comando All applications (Tutte le applicazioni)](./media/disable-user-sign-in-portal/select-app.png)
7. Nel riquadro ***nomeapp*** - **Proprietà** selezionare **No** per **Abilitata per l'accesso degli utenti?**.
8. Selezionare il comando **Salva** .

## <a name="next-steps"></a>Passaggi successivi
* [Visualizzare tutti i gruppi personali](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Assegnare un utente o gruppo a un'app aziendale](assign-user-or-group-access-portal.md)
* [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](remove-user-or-group-access-portal.md)
* [Modificare il nome o il logo di un'app aziendale](change-name-or-logo-portal.md)
