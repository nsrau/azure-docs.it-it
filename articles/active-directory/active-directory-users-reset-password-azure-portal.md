---
title: Reimpostazione delle password in Azure Active Directory | Microsoft Docs
description: L'amministratore ha avviato la reimpostazione della password per un utente in Azure Active Directory
services: active-directory
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
editor: 
ms.assetid: fad5624b-2f13-4abc-b3d4-b347903a8f16
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: joflore
ms.reviewer: sahenry
ms.custom: it-pro
ms.openlocfilehash: bea082081e3f3f52ba78188903a9536fe9de9392
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2017
---
# <a name="reset-the-password-for-a-user-in-azure-active-directory"></a>Reimpostare la password per un utente in Azure Active Directory

Gli amministratori potrebbero dover reimpostare la password se l'utente la dimentica, rimane bloccato o in altri scenari. I passaggi che seguono consentono di eseguire la reimpostazione della password di un utente.

## <a name="how-to-reset-the-password-for-a-user"></a>Come reimpostare la password per un utente

1. Accedere all'[interfaccia di amministrazione di Azure AD](https://aad.portal.azure.com) con un account che disponga delle autorizzazioni di directory per reimpostare le password utente.
2. Selezionare **Azure Active Directory** > **Utenti e gruppi** > **All users (Tutti gli utenti)**.
3. Selezionare l'utente per il quale si intende reimpostare la password.
2. Selezionare **Reimpostazione password** per l'utente selezionato.

    ![Reimpostare la password per un utente da Profilo utente in Azure AD](./media/active-directory-users-reset-password-azure-portal/user-password-reset.png)
    
6. In **Reimposta password** selezionare **Reimposta password**.
7. Viene visualizzata una password temporanea che è quindi possibile fornire all'utente. L'utente dovrà modificare la password al successivo accesso. 

   > [!NOTE]
   > La password temporanea non ha scadenza, pertanto rimarrà valida fino a quando l'utente non eseguirà l'accesso e dovrà cambiarla. 

## <a name="next-steps"></a>Passaggi successivi
* [Aggiungere un utente](active-directory-users-create-azure-portal.md)
* [Assegnare i ruoli di amministratore a un utente](active-directory-users-assign-role-azure-portal.md)
* [Gestire i profili utente](active-directory-users-profile-azure-portal.md)
* [Eliminare un utente in Azure AD](active-directory-users-delete-user-azure-portal.md)
