---
title: Associare le sottoscrizioni di Azure ad Azure Active Directory | Microsoft Docs
description: Accesso a Microsoft Azure e problemi correlati, come la relazione tra una sottoscrizione di Azure e Azure Active Directory.
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
ms.date: 08/22/2017
ms.author: curtand
ms.reviewer: jeffsta
ms.custom: oldportal;it-pro;
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: b2f8cc774b6cc245a4000e5c6924a8bb817707bc
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="how-azure-subscriptions-are-associated-with-azure-active-directory"></a>Associare le sottoscrizioni di Azure ad Azure Active Directory
Questo articolo contiene informazioni sulla relazione tra una sottoscrizione di Azure e Azure Active Directory (Azure AD).

## <a name="your-azure-subscriptions-relationship-to-azure-ad"></a>Relazione della sottoscrizione di Azure con Azure AD
La sottoscrizione di Azure ha una relazione di trust con Azure AD, ovvero considera attendibile la directory per autenticare gli utenti, i servizi e i dispositivi. Più sottoscrizioni possono considerare attendibile la stessa directory, ma ogni sottoscrizione considera attendibile una sola directory. 

La relazione di trust tra la sottoscrizione e la directory è diversa dalla relazione con le altre risorse in Azure, ad esempio siti Web, database e così via. Se la sottoscrizione scade, non sarà più possibile accedere alle altre risorse associate alla sottoscrizione anche se la directory rimane in Azure, quindi sarà possibile associarvi una sottoscrizione diversa e continuare a gestire gli utenti della directory.

![Diagramma che illustra come vengono associate le sottoscrizioni](./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png)

Azure AD non funziona come gli altri servizi nella sottoscrizione di Azure. Gli altri servizi di Azure sono subordinati alla sottoscrizione di Azure, ma ciò che viene visualizzato in Azure AD non dipende dalla sottoscrizione. L'accesso alle directory viene consentito in base all'utente connesso.

Tutti gli utenti dispongono di una singola home directory che li autentica, ma poi possono essere utenti guest in altre directory. In Azure AD è possibile visualizzare solo le directory di cui l'account utente è membro. Una directory può anche essere sincronizzata con Active Directory locale.

## <a name="azure-ad-and-cloud-service-subscriptions"></a>Azure AD e sottoscrizioni di servizi cloud
Azure AD fornisce le principali funzionalità di gestione delle identità e di directory alla base della maggior parte dei servizi cloud Microsoft, tra cui:

* Azure
* Microsoft Office 365
* Microsoft Dynamics CRM Online
* Microsoft Intune

Quando si effettua l'iscrizione a uno di questi servizi cloud Microsoft, si ottiene gratuitamente il servizio Azure AD. È possibile aggiungere un'altra sottoscrizione di Azure a una directory di Azure AD, solo se si è connessi con un account Microsoft. Se ad esempio ci si iscrive ad Azure usando un account Microsoft e ci si iscrive anche a un altro servizio cloud Microsoft usando un account aziendale o dell'istituto di istruzione, si avranno due istanze di Azure AD:
1. La directory predefinita per una sottoscrizione di Azure. È possibile aggiungere un'altra sottoscrizione di Azure a questa o ad altre directory create se si è connessi con un account Microsoft perché è possibile essere autenticati da Azure.
2. La home directory dell'account aziendale o dell'istituto di istruzione. Se si è connessi ad Azure con un account aziendale o dell'istituto di istruzione, non è possibile aggiungere una sottoscrizione di Azure a una directory esistente perché l'account aziendale o dell'istituto di istruzione non può essere autenticato direttamente da Azure. 
 
Per altre informazioni su come cambiare gli amministratori per una sottoscrizione di Azure, vedere [Trasferire la proprietà di una sottoscrizione di Azure a un altro account](../billing/billing-subscription-transfer.md)

## <a name="suggestions-to-manage-both-a-subscription-and-a-directory"></a>Suggerimenti per gestire sia una sottoscrizione che una directory
Le risorse associate a una sottoscrizione di Azure vengono gestite dai ruoli amministrativi della sottoscrizione stessa. Questa sezione illustra le differenze tra gli amministratori di una sottoscrizione di Azure e gli amministratori di una directory di Azure AD. I ruoli amministrativi e gli altri suggerimenti su come usarli per gestire la sottoscrizione sono descritti in [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles.md).

Per impostazione predefinita, al momento dell'iscrizione all'utente viene assegnato il ruolo di amministratore del servizio. Se altri utenti devono accedere ai servizi con la stessa sottoscrizione, è possibile aggiungerli come coamministratori. 

In Azure AD è invece disponibile un set di ruoli amministrativi diverso per gestire la directory e le funzionalità relative alle identità. Ad esempio, l'amministratore globale di una directory può aggiungere utenti e gruppi alla directory o richiedere l'autenticazione a più fattori per gli utenti. All'utente che crea una directory viene assegnato il ruolo di amministratore globale, che, a sua volta, potrà assegnare ruoli amministrativi ad altri utenti. I ruoli amministrativi di Azure AD vengono anche usati da altri servizi, ad esempio Office 365 e Microsoft Intune. 

Gli amministratori della sottoscrizione di Azure e gli amministratori della directory di Azure AD sono due ruoli distinti. 
* Gli amministratori della sottoscrizione di Azure possono gestire le risorse in Azure e usare Azure AD nel portale di Azure, perché il portale di Azure stesso è una risorsa di Azure. 
* Gli amministratori della directory possono gestire le proprietà solo nella directory di Azure AD.

Una persona può rivestire entrambi i ruoli, ma non è obbligatorio. A un utente può essere assegnato il ruolo di amministratore globale della directory ma non quello di amministratore del servizio o di coamministratore di una sottoscrizione di Azure. Senza essere un amministratore della sottoscrizione, l'utente può accedere al portale di Azure, ma non può gestire le directory per tale sottoscrizione nel portale. Questo utente può gestire le directory usando altri strumenti, ad esempio Azure AD PowerShell o l'interfaccia di amministrazione di Office 365.

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su come cambiare gli amministratori per una sottoscrizione di Azure, vedere [Trasferire la proprietà di una sottoscrizione di Azure a un altro account](../billing/billing-subscription-transfer.md)
* Per altre informazioni sul modo in cui l'accesso alle risorse viene controllato in Microsoft Azure, vedere [Informazioni sull'accesso alle risorse in Azure](active-directory-understanding-resource-access.md)
* Per altre informazioni su come assegnare i ruoli in Azure AD, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](active-directory-assign-admin-roles-azure-portal.md)

<!--Image references-->
[1]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_PassThruAuth.png
[2]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_OrgAccountSubscription.png
[3]: ./media/active-directory-how-subscriptions-associated-directory/WAAD_SignInDisambiguation.PNG

