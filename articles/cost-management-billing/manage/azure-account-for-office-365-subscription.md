---
title: Iscriversi a Office 365 con un account Azure | Microsoft Docs
description: Informazioni su come creare un abbonamento a Office 365 con un account Azure
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2020
ms.author: banders
ms.openlocfilehash: b38800a990056d7de0b6978573ddc19c80503a1d
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77199926"
---
# <a name="sign-up-for-an-office-365-subscription-with-your-azure-account"></a>Iscriversi a un abbonamento a Office 365 con il proprio account Azure
Se si è un sottoscrittore di Azure, è possibile usare l'account Azure per iscriversi a un abbonamento a Office 365. Se si fa parte di un'organizzazione che dispone di una sottoscrizione di Azure, è possibile creare abbonamenti a Office 365 per gli utenti nell'istanza esistente di Azure Active Directory (Azure AD). Iscriversi a Office 365 tramite un account che dispone dell'autorizzazione Amministratore globale o Amministratore fatturazione nel tenant di Azure Active Directory. Per altre informazioni, vedere [Controllare le autorizzazioni dell'account in Azure AD](#RoleInAzureAD) e [Assegnazione dei ruoli di amministratore in Azure Active Directory](../../active-directory/users-groups-roles/directory-assign-admin-roles.md).

Se si dispone già sia di un account di Office 365 sia di una sottoscrizione di Azure, vedere [Associare un tenant di Office 365 a una sottoscrizione di Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-office-365-subscription-by-using-your-azure-account"></a>Ottenere un abbonamento a Office 365 tramite il proprio account Azure

1. Andare alla [pagina del prodotto Office 365](https://products.office.com/business) e selezionare un piano.
2. Fare clic su **Accedi** nell'angolo superiore destro della pagina.

    ![screenshot della pagina della versione di valutazione di Office 365](./media/azure-account-for-office-365-subscription/12-office-365-trial-page.png)
3. Accedere con le credenziali dell'account Azure. Se si crea un abbonamento per la propria organizzazione, usare l'account Azure membro del ruolo della directory Amministratore globale o Amministratore fatturazione nel tenant di Azure Active Directory.

    ![Screenshot della pagina di accesso a Office 365](./media/azure-account-for-office-365-subscription/13-office-365-sign-in.png)
4. Fare clic su **Prova adesso**.

    ![Screenshot di conferma dell'ordine per Office 365.](./media/azure-account-for-office-365-subscription/14-office-365-confirm-your-order.png)
5. Nella pagina di conferma dell'ordine fare clic su **Continua**.

    ![Screenshot di ricezione dell'ordine di Office 365](./media/azure-account-for-office-365-subscription/15-office-365-order-receipt.png)

Le impostazioni sono state completate.
Se l'abbonamento a Office 365 è stato creato per la propria organizzazione, seguire questa procedura per controllare che gli utenti di Azure AD siano ora disponibili in Office 365.

1. Aprire l'interfaccia di amministrazione di Microsoft 365.
2. Espandere **UTENTI** e quindi fare clic su **Utenti attivi**.

    ![Screenshot degli utenti dell'interfaccia di amministrazione di Microsoft 365](./media/azure-account-for-office-365-subscription/16-microsoft-365-admin-center-users.png)

Dopo avere eseguito l'iscrizione, l'abbonamento a Office 365 viene aggiunto alla stessa istanza di Azure Active Directory a cui appartiene la sottoscrizione di Azure. Per altre informazioni, vedere [Altre informazioni su sottoscrizioni di Azure e abbonamenti a Office 365](office-365-account-for-azure-subscription.md#more-about-subs) e [Associare le sottoscrizioni di Azure ad Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a id="RoleInAzureAD"></a>Controllare le autorizzazioni dell'account Microsoft in Azure AD
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Tutti i servizi** e quindi cercare **Active Directory**.

    ![Screenshot di Active Directory nel portale di Azure](./media/azure-account-for-office-365-subscription/billing-more-services-active-directory.png)
3. Fare clic su **Utenti e gruppi** > **Tutti gli utenti**.
4. Selezionare il nome utente.

    ![Screenshot che mostra gli utenti di Azure Active Directory](./media/azure-account-for-office-365-subscription/billing-users-groups.png)

5. Fare clic su **Ruolo directory**.

    ![Screenshot che mostra il ruolo di directory del portale di Azure](./media/azure-account-for-office-365-subscription/billing-user-directory-role.png)
6.  È necessario il ruolo **Amministratore globale** o **Amministratore con limitazioni** > **Amministratore fatturazione** per creare un abbonamento a Office 365 per gli utenti nell'istanza di Azure Active Directory esistente.

    ![Screenshot che mostra il ruolo di directory del portale di Azure Amministratore fatturazione](./media/azure-account-for-office-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).
