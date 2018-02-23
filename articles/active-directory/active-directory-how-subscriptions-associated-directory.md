---
title: Come aggiungere una sottoscrizione di Azure esistente alla directory di Azure AD | Microsoft Docs
description: Come aggiungere una sottoscrizione esistente alla directory di Azure AD
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/12/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: e063e6a46b6b99c4bbe749347e6887a930adb882
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2018
---
# <a name="how-to-associate-or-add-an-azure-subscription-to-azure-active-directory"></a>Come associare o aggiungere una sottoscrizione di Azure ad Azure Active Directory

Questo articolo contiene informazioni sulla relazione tra una sottoscrizione di Azure e Azure Active Directory (Azure AD) e su come aggiungere una sottoscrizione esistente alla directory di Azure AD. La sottoscrizione di Azure ha una relazione di trust con Azure AD, ovvero considera attendibile la directory per autenticare gli utenti, i servizi e i dispositivi. Più sottoscrizioni possono considerare attendibile la stessa directory, ma ogni sottoscrizione considera attendibile una sola directory. 

La relazione di trust tra la sottoscrizione e la directory è diversa dalla relazione con le altre risorse in Azure, ad esempio siti Web, database e così via. Se la sottoscrizione scade, non sarà più possibile accedere alle altre risorse associate alla sottoscrizione. La directory di Azure AD rimane però in Azure, sarà quindi possibile associarla a una sottoscrizione diversa e continuare a gestire la directory con la nuova sottoscrizione.

Tutti gli utenti dispongono di una singola home directory che li autentica, ma poi possono essere utenti guest in altre directory. In Azure AD è possibile visualizzare solo le directory di cui l'account utente è membro o guest.

## <a name="before-you-begin"></a>Prima di iniziare

* È necessario accedere con l'account con accesso di proprietario di controllo degli accessi in base al ruolo alla sottoscrizione.
* È necessario eseguire l'accesso con un account che esiste sia nella directory corrente a cui è associata la sottoscrizione che nella directory a cui la si vuole aggiungere. Per altre informazioni su come ottenere l'accesso a un'altra directory, vedere [Procedura di aggiunta di utenti di Collaborazione B2B ad Azure Active Directory da parte degli amministratori](active-directory-b2b-admin-add-users.md).

## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Per associare una sottoscrizione esistente alla directory di Azure AD

1. Eseguire l'accesso e selezionare una sottoscrizione dalla [pagina Sottoscrizioni del portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).
2. Fare clic su **Cambia directory**.

    ![Screenshot che illustra il pulsante Cambia directory](./media/active-directory-how-subscriptions-associated-directory/edit-directory-button.PNG)
3. Esaminare gli avvisi. Tutti gli utenti con [controllo degli accessi in base al ruolo](role-based-access-control-configure.md) a cui è assegnato l'accesso e tutti gli amministratori della sottoscrizione perdono l'accesso quando la directory della sottoscrizione cambia.
4. Selezionare una directory.

    ![Screenshot che illustra l'interfaccia utente per cambiare la directory](./media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.PNG)
5. Fare clic su **Modifica**.
6. Completamento della procedura Passare alla nuova directory usando l'apposito controllo. Per la visualizzazione corretta di tutti gli elementi potrebbero essere necessari fino a 10 minuti.

    ![Screenshot che illustra la notifica dell'avvenuto cambio di directory](./media/active-directory-how-subscriptions-associated-directory/edit-directory-success.PNG)

    ![Screenshot che illustra il controllo per cambiare directory](./media/active-directory-how-subscriptions-associated-directory/directory-switcher.PNG)


La modifica della directory della sottoscrizione è un'operazione a livello di servizio. Non ha effetto sulla proprietà della fatturazione della sottoscrizione e l'amministratore account può continuare a modificare l'amministratore del servizio usando il [Centro account](https://account.azure.com/subscriptions). Per eliminare la directory originale, è necessario trasferire la proprietà della fatturazione della sottoscrizione a un nuovo amministratore account. Per altre informazioni sul trasferimento della proprietà della fatturazione, vedere [Trasferire la proprietà di una sottoscrizione di Azure a un altro account](../billing/billing-subscription-transfer.md). 

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla creazione di una nuova directory di Azure AD gratuita, vedere [Come ottenere un tenant di Azure Active Directory](develop/active-directory-howto-tenant.md)
* Per altre informazioni sul trasferimento della proprietà della fatturazione di una proprietario di Azure, vedere [Trasferire la proprietà di una sottoscrizione di Azure a un altro account](../billing/billing-subscription-transfer.md)
* Per altre informazioni sul modo in cui l'accesso alle risorse viene controllato in Microsoft Azure, vedere [Informazioni sull'accesso alle risorse in Azure](active-directory-understanding-resource-access.md)
* Per altre informazioni su come assegnare i ruoli in Azure AD, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
