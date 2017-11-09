---
title: Come aggiungere una sottoscrizione di Azure esistente alla directory di Azure AD | Microsoft Docs
description: Come aggiungere una sottoscrizione esistente alla directory di Azure AD
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: bc4773c2-bc4a-4d21-9264-2267065f0aea
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/17/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.openlocfilehash: abf207a3ceec708a828170936f7dc7948ccf34a9
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2017
---
# <a name="how-to-add-an-azure-subscription-to-azure-active-directory"></a>Come aggiungere una sottoscrizione di Azure ad Azure Active Directory
Questo articolo contiene informazioni sulla relazione tra una sottoscrizione di Azure e Azure Active Directory (Azure AD) e su come aggiungere una sottoscrizione esistente alla directory di Azure AD. La sottoscrizione di Azure ha una relazione di trust con Azure AD, ovvero considera attendibile la directory per autenticare gli utenti, i servizi e i dispositivi. Più sottoscrizioni possono considerare attendibile la stessa directory, ma ogni sottoscrizione considera attendibile una sola directory. 

La relazione di trust tra la sottoscrizione e la directory è diversa dalla relazione con le altre risorse in Azure, ad esempio siti Web, database e così via. Se la sottoscrizione scade, non sarà più possibile accedere alle altre risorse associate alla sottoscrizione. La directory di Azure AD rimane però in Azure, sarà quindi possibile associarla a una sottoscrizione diversa e continuare a gestire la directory con la nuova sottoscrizione.

Tutti gli utenti dispongono di una singola home directory che li autentica, ma poi possono essere utenti guest in altre directory. In Azure AD è possibile visualizzare solo le directory di cui l'account utente è membro o guest.

## <a name="to-add-an-existing-subscription-to-your-azure-ad-directory"></a>Per aggiungere una sottoscrizione esistente alla directory di Azure AD
È necessario eseguire l'accesso con un account che esiste sia nella directory corrente a cui è associata la sottoscrizione che nella directory a cui la si vuole aggiungere. 

1. Accedere al [Centro account di Azure](https://account.azure.com/Subscriptions) con un account amministratore della sottoscrizione di cui si vuole trasferire la proprietà.
2. Assicurarsi che l'utente che si vuole impostare come proprietario della sottoscrizione sia incluso nella directory di destinazione.
3. Fare clic su **Trasferisci sottoscrizione**.
4. Specificare il destinatario. Il destinatario riceve automaticamente un messaggio e-mail con un link per l'accettazione.
5. Il destinatario deve fare clic sul link e seguire le istruzioni, inclusa l'immissione delle informazioni sul pagamento. Quando il destinatario ha esito positivo, la sottoscrizione viene trasferita. 
6. La directory predefinita della sottoscrizione viene modificata nella directory in cui si trova l'utente.

Per altre informazioni, vedere [Trasferimento della proprietà di una sottoscrizione di Azure a un altro account](../billing/billing-subscription-transfer.md).

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su come cambiare gli amministratori per una sottoscrizione di Azure, vedere [Trasferire la proprietà di una sottoscrizione di Azure a un altro account](../billing/billing-subscription-transfer.md)
* Per altre informazioni sul modo in cui l'accesso alle risorse viene controllato in Microsoft Azure, vedere [Informazioni sull'accesso alle risorse in Azure](active-directory-understanding-resource-access.md)
* Per altre informazioni su come assegnare i ruoli in Azure AD, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG
