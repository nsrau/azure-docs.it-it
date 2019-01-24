---
title: Modificare il nome o il logo di un'app aziendale in Azure Active Directory | Microsoft Docs
description: Come modificare il nome o il logo di un'app aziendale personalizzata in Azure Active Directory
services: active-directory
documentationcenter: ''
author: barbkess
manager: daveba
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
ms.openlocfilehash: 293cc161245ed1e674caa1fa37567a3b6f344d37
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54474595"
---
# <a name="change-the-name-or-logo-of-an-enterprise-app-in-azure-active-directory"></a>Modificare il nome o il logo di un'app aziendale in Azure Active Directory
Modificare il nome o il logo per un'applicazione aziendale personalizzata in Azure Active Directory (Azure AD) è un'operazione facile. È necessario disporre delle autorizzazioni appropriate per apportare queste modifiche ed essere l'autore dell'app personalizzata.

## <a name="how-do-i-change-an-enterprise-apps-name-or-logo"></a>Come è possibile modificare il nome o il logo di un'app aziendale?
1. Accedere al [portale di Azure](https://portal.azure.com) con un account di amministratore globale per la directory.
2. Selezionare **Tutti i servizi**, immettere **Azure Active Directory** nella casella di testo e quindi premere **INVIO**.
3. Nel riquadro **Azure Active Directory - *nomedirectory*** , vale a dire il riquadro Azure AD per la directory che si sta gestendo, selezionare **Applicazioni aziendali**.

    ![Apertura di app aziendali](./media/change-name-or-logo-portal/open-enterprise-apps.png)
4. Nel riquadro **Applicazioni aziendali** selezionare **Tutte le applicazioni**. Viene visualizzato un elenco di app che è possibile gestire.
5. Nel riquadro **Applicazioni aziendali - Tutte le applicazioni** selezionare un'app.
6. Nel riquadro ***nomeapp***, vale a dire il riquadro con il nome dell'app selezionata nel titolo, selezionare **Proprietà**.

    ![Selezione del comando Proprietà](./media/change-name-or-logo-portal/select-app.png)
7. Nel riquadro ***nome app*** **- Proprietà** cercare un file da usare come nuovo logo, modificare il nome dell'app oppure eseguire entrambe le operazioni.

    ![Modifica del logo dell'app o del comando nameproperties](./media/change-name-or-logo-portal/change-logo.png)
8. Selezionare il comando **Salva** .

## <a name="next-steps"></a>Passaggi successivi
* [Visualizzare tutti i gruppi personali](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Assegnare un utente o gruppo a un'app aziendale](assign-user-or-group-access-portal.md)
* [Rimuovere l'assegnazione di un utente o un gruppo da un'app aziendale](remove-user-or-group-access-portal.md)
* [Disabilitare l'accesso degli utenti per un'app aziendale](disable-user-sign-in-portal.md)
