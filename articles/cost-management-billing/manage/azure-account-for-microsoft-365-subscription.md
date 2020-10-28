---
title: Iscriversi a Microsoft 365 con l'account Azure
description: Informazioni su come creare una sottoscrizione di Microsoft 365 con un account Azure. È anche possibile associare gli account esistenti di Azure e Microsoft 365.
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: db9c8d18d975661edf4a120a2e82b073c884d2a9
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92369676"
---
# <a name="sign-up-for-a-microsoft-365-subscription-with-your-azure-account"></a>Iscriversi a una sottoscrizione di Microsoft 365 con l'account Azure

Se si è un sottoscrittore di Azure, è possibile usare l'account Azure per iscriversi a una sottoscrizione di Microsoft 365. Se si fa parte di un'organizzazione che ha una sottoscrizione di Azure, è possibile creare sottoscrizioni di Microsoft 365 per gli utenti nell'istanza esistente di Azure Active Directory (Azure AD). Iscriversi a Microsoft 365 tramite un account che ha l'autorizzazione Amministratore globale o Amministratore fatturazione nel tenant di Azure Active Directory. Per altre informazioni, vedere [Controllare le autorizzazioni dell'account in Azure AD](#RoleInAzureAD) e [Assegnazione dei ruoli di amministratore in Azure Active Directory](../../active-directory/roles/permissions-reference.md).

Se si ha già sia un account di Microsoft 365 sia una sottoscrizione di Azure, è possibile [Associare un tenant di Microsoft 365 a una sottoscrizione di Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-a-microsoft-365-subscription-by-using-your-azure-account"></a>Ottenere una sottoscrizione di Microsoft 365 tramite il proprio account Azure

1. Andare alla [pagina del prodotto Microsoft 365](https://www.microsoft.com/microsoft-365/business/all-business) e selezionare un piano.
2. Fare clic su **Accedi** nell'angolo superiore destro della pagina.

    ![Screenshot della pagina della versione di valutazione di Microsoft 365](./media/azure-account-for-microsoft-365-subscription/12-office-365-trial-page.png)
3. Accedere con le credenziali dell'account Azure. Se si crea un abbonamento per la propria organizzazione, usare l'account Azure membro del ruolo della directory Amministratore globale o Amministratore fatturazione nel tenant di Azure Active Directory.

    ![Screenshot dell'accesso a Microsoft](./media/azure-account-for-microsoft-365-subscription/13-office-365-sign-in.png)
4. Fare clic su **Prova adesso** .

    ![Screenshot di conferma dell'ordine per Microsoft 365.](./media/azure-account-for-microsoft-365-subscription/14-office-365-confirm-your-order.png)
5. Nella pagina di conferma dell'ordine fare clic su **Continua** .

    ![Screenshot di ricezione dell'ordine di Microsoft 365](./media/azure-account-for-microsoft-365-subscription/15-office-365-order-receipt.png)

Le impostazioni sono state completate. Se la sottoscrizione di Microsoft 365 è stata creata per la propria organizzazione, seguire questa procedura per controllare che gli utenti di Azure AD siano ora disponibili in Microsoft 365.

1. Aprire l'interfaccia di amministrazione di Microsoft 365.
2. Espandere **UTENTI** e quindi fare clic su **Utenti attivi** .

    ![Screenshot degli utenti dell'interfaccia di amministrazione di Microsoft 365](./media/azure-account-for-microsoft-365-subscription/16-microsoft-365-admin-center-users.png)

Dopo avere eseguito l'iscrizione, la sottoscrizione di Microsoft 365 viene aggiunta alla stessa istanza di Azure Active Directory a cui appartiene la sottoscrizione di Azure. Per altre informazioni, vedere [Altre informazioni su sottoscrizioni di Azure e sottoscrizioni di Microsoft 365](microsoft-365-account-for-azure-subscription.md#more-about-subs) e [Associare le sottoscrizioni di Azure ad Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="check-my-account-permissions-in-azure-ad"></a><a id="RoleInAzureAD"></a>Controllare le autorizzazioni dell'account Microsoft in Azure AD
1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Fare clic su **Tutti i servizi** e quindi cercare **Active Directory** .

    ![Screenshot di Active Directory nel portale di Azure](./media/azure-account-for-microsoft-365-subscription/billing-more-services-active-directory.png)
3. Fare clic su **Utenti e gruppi** > **Tutti gli utenti** .
4. Selezionare il nome utente.

    ![Screenshot che mostra gli utenti di Azure Active Directory](./media/azure-account-for-microsoft-365-subscription/billing-users-groups.png)

5. Fare clic su **Ruolo directory** .

    ![Screenshot che mostra il ruolo di directory del portale di Azure](./media/azure-account-for-microsoft-365-subscription/billing-user-directory-role.png)
6.  È necessario il ruolo **Amministratore globale** o **Amministratore con limitazioni** > **Amministratore fatturazione** per creare una sottoscrizione di Microsoft 365 per gli utenti nell'istanza di Azure Active Directory esistente.

    ![Screenshot che mostra il ruolo di directory del portale di Azure Amministratore fatturazione](./media/azure-account-for-microsoft-365-subscription/billing-directoryrole-limited.png)

## <a name="need-help-contact-us"></a>Richiesta di assistenza Contattaci.

In caso di domande o per assistenza, [creare una richiesta di supporto](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Passaggi successivi

- [Associare un tenant di Microsoft 365 a una sottoscrizione di Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)
