---
title: Aggiungere una sottoscrizione di Azure esistente al tenant - Azure Active Directory | Microsoft Docs
description: Informazioni su come aggiungere una sottoscrizione di Azure esistente al tenant di Azure Active Directory.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a116355c8140d30f8297cde067a82f37f72e02a
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56165859"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associare o aggiungere una sottoscrizione di Azure al tenant di Azure Active Directory
La sottoscrizione di Azure ha una relazione di trust con Azure Active Directory (Azure AD), ovvero considera Azure AD attendibile per autenticare gli utenti, i servizi e i dispositivi. Più sottoscrizioni possono considerare attendibile la stessa directory di Azure AD, ma una sottoscrizione può considerare attendibile una sola directory.

Se la sottoscrizione scade, non sarà più possibile accedere alle altre risorse associate alla sottoscrizione. Tuttavia, la directory di Azure AD rimane in Azure e può essere associata o gestita con una sottoscrizione di Azure diversa.

Tutti gli utenti hanno una singola "home"directory per l'autenticazione. Tuttavia, gli utenti possono anche essere guest in altre directory. È possibile visualizzare sia la home directory che la directory guest per ogni utente in Azure AD.

>[!Important]
>Tutti gli utenti con [controllo degli accessi in base al ruolo](../../role-based-access-control/role-assignments-portal.md) a cui è assegnato l'accesso e tutti gli amministratori della sottoscrizione perdono l'accesso quando la directory della sottoscrizione cambia. Se si hanno insiemi di credenziali delle chiavi, vengono anch'essi interessati dallo spostamento della sottoscrizione. Per risolvere questo problema, è necessario [modificare l'ID tenant dell'insieme di credenziali delle chiavi](../../key-vault/key-vault-subscription-move-fix.md) prima di riprendere le operazioni.


## <a name="before-you-begin"></a>Prima di iniziare
Prima di poter associare o aggiungere la sottoscrizione, è necessario eseguire le attività seguenti:

- Accedere con un account che:
    - Abbia l'accesso **RBAC Owner** (Proprietario controllo degli accessi in base al ruolo) alla sottoscrizione.

    - Sia presente sia nella directory corrente associata alla sottoscrizione che nella nuova directory in cui si vuole associare la sottoscrizione in seguito. Per altre informazioni su come ottenere l'accesso a un'altra directory, vedere [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](../b2b/add-users-administrator.md).

- Assicurarsi di non usare una sottoscrizione provider di servizi cloud di Azure (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), una sottoscrizione interna Microsoft (MS-AZR-0015P) o una sottoscrizione Microsoft Imagine (MS-AZR-0144P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Per associare una sottoscrizione esistente alla directory di Azure AD
1. Eseguire l'accesso e selezionare una sottoscrizione dalla [pagina Sottoscrizioni del portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Selezionare **Cambia directory**.

    ![Pagina delle sottoscrizioni con l'opzione Cambia directory evidenziata](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Esaminare eventuali avvisi che vengono visualizzati e selezionare **Cambia**.

    ![Pagina di modifica della directory con la nuova directory visualizzata](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    La directory è stata modificata per la sottoscrizione e viene visualizzato un messaggio di operazione riuscita.

    ![Messaggio di operazione riuscita](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)    

4. Usare la selezione di directory per passare alla nuova directory. Per la visualizzazione corretta di tutti gli elementi potrebbero essere necessari fino a 10 minuti.

    ![Pagina di selezione della directory](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

La modifica della directory della sottoscrizione è un'operazione a livello di servizio, pertanto non influisce sulla proprietà della fatturazione della sottoscrizione. L'amministratore dell'account può comunque modificare l'amministratore del servizio dal [Centro account](https://account.azure.com/subscriptions). Per eliminare la directory originale, è necessario trasferire la proprietà della fatturazione della sottoscrizione a un nuovo amministratore dell'account. Per altre informazioni sul trasferimento della proprietà della fatturazione, vedere [Trasferire la proprietà di una sottoscrizione di Azure a un altro account](../../billing/billing-subscription-transfer.md). 

## <a name="next-steps"></a>Passaggi successivi

- Per creare un nuovo tenant di Azure AD, vedere [Access Azure Active Directory to create a new tenant](active-directory-access-create-new-tenant.md) (Accedere ad Azure Active Directory per creare un nuovo tenant)

- Per altre informazioni sul modo in cui l'accesso alle risorse viene controllato in Microsoft Azure, vedere [Informazioni sull'accesso alle risorse in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Per altre informazioni su come assegnare i ruoli in Azure AD, vedere [Come assegnare ruoli della directory agli utenti con Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
