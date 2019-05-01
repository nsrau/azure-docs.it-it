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
ms.date: 03/13/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06b6fdf7a3d21a6b7dc84c4347824d4eaeac046f
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64918404"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associare o aggiungere una sottoscrizione di Azure al tenant di Azure Active Directory

Una sottoscrizione di Azure ha una relazione di trust con Azure Active Directory (Azure AD), il che significa che la sottoscrizione considera attendibile Azure AD per autenticare gli utenti, servizi e dispositivi. Più sottoscrizioni possono considerare attendibile la stessa directory di Azure AD, ma una sottoscrizione può considerare attendibile una sola directory.

Se la sottoscrizione scade, non sarà più possibile accedere alle altre risorse associate alla sottoscrizione. Tuttavia, la directory di Azure AD rimane in Azure e può essere associata o gestita con una sottoscrizione di Azure diversa.

Tutti gli utenti avere un'unica *domestica* directory per l'autenticazione. Tuttavia, gli utenti possono anche essere guest in altre directory. È possibile visualizzare sia la home directory che la directory guest per ogni utente in Azure AD.

> [!Important]
> Quando si associa una sottoscrizione a un'altra directory, gli utenti che hanno ruoli assegnati tramite [controllo di accesso basato sui ruoli (RBAC)](../../role-based-access-control/role-assignments-portal.md) perderanno l'accesso. Amministratore sottoscrizione classico (amministratore del servizio e coamministratori) anche perderanno l'accesso.
> 
> Inoltre, lo spostamento del cluster Azure Kubernetes Service (AKS) per una sottoscrizione diversa o Sposta la sottoscrizione che possiede cluster in un nuovo tenant, fa sì che il cluster a una perdita di funzionalità a causa di diritti di identità del servizio e le assegnazioni di ruolo persi. Per altre informazioni sul servizio contenitore di AZURE, vedere [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/).

## <a name="before-you-begin"></a>Prima di iniziare

Prima di poter associare o aggiungere la sottoscrizione, è necessario eseguire le attività seguenti:

1. Esaminare l'elenco delle modifiche e come si potrebbero riscontrare seguente:

    - Gli utenti che sono stati assegnati ruoli tramite RBAC perderanno l'accesso
    - Amministratore del servizio e coamministratori perderanno l'accesso
    - Se si dispone di tutti gli insiemi di credenziali chiave, sarà inaccessibile e sarà necessario correggere gli errori dopo l'associazione
    - Se si dispone di uno Stack di Azure registrati, sarà necessario registrare nuovamente dopo l'associazione

1. Accedere con un account che:
    - È un' [proprietario](../../role-based-access-control/built-in-roles.md#owner) assegnazione di ruolo per la sottoscrizione. Per informazioni su come assegnare il ruolo di proprietario, vedere [gestire l'accesso alle risorse di Azure usando il portale di Azure e RBAC](../../role-based-access-control/role-assignments-portal.md).
    - Sia presente sia nella directory corrente associata alla sottoscrizione che nella nuova directory in cui si vuole associare la sottoscrizione in seguito. Per altre informazioni su come ottenere l'accesso a un'altra directory, vedere [Aggiungere utenti di Collaborazione B2B di Azure Active Directory nel portale di Azure](../b2b/add-users-administrator.md).

1. Assicurarsi di non usare una sottoscrizione provider di servizi cloud di Azure (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), una sottoscrizione interna Microsoft (MS-AZR-0015P) o una sottoscrizione Microsoft Imagine (MS-AZR-0144P).
    
## <a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>Per associare una sottoscrizione esistente alla directory di Azure AD

1. Eseguire l'accesso e selezionare una sottoscrizione dalla [pagina Sottoscrizioni del portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

2. Selezionare **Cambia directory**.

    ![Pagina delle sottoscrizioni con l'opzione Cambia directory evidenziata](media/active-directory-how-subscriptions-associated-directory/change-directory-button.png)

3. Esaminare eventuali avvisi che vengono visualizzati e selezionare **Cambia**.

    ![Pagina di modifica della directory con la nuova directory visualizzata](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    La directory è stata modificata per la sottoscrizione e viene visualizzato un messaggio di operazione riuscita.

    ![Messaggio di operazione riuscita sulla modifica della directory](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)    
4. Usare la **apposito controllo** per passare alla nuova directory. Per la visualizzazione corretta di tutti gli elementi potrebbero essere necessari fino a 10 minuti.

    ![Pagina di selezione directory, con le informazioni di esempio](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

La modifica della directory della sottoscrizione è un'operazione a livello di servizio, pertanto non influisce sulla proprietà della fatturazione della sottoscrizione. L'amministratore dell'account può comunque modificare l'amministratore del servizio dal [Centro account](https://account.azure.com/subscriptions). Per eliminare la directory originale, è necessario trasferire la proprietà della fatturazione della sottoscrizione a un nuovo amministratore dell'account. Per altre informazioni sul trasferimento della proprietà della fatturazione, vedere [Trasferire la proprietà di una sottoscrizione di Azure a un altro account](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Associazione passaggi successivi
Dopo aver associato una sottoscrizione a un'altra directory, potrebbero essere presenti altri passaggi che è necessario eseguire per riprendere le operazioni.

1. Se si dispone di tutti gli insiemi di credenziali chiave, è necessario modificare l'ID insieme di credenziali delle chiavi tenant. Per altre informazioni, vedere [modificare l'ID tenant per un insieme di credenziali delle chiavi dopo lo spostamento di una sottoscrizione](../../key-vault/key-vault-subscription-move-fix.md).

2. Se è stato registrato usando questa sottoscrizione di Azure Stack, è necessario registrare nuovamente. Per altre informazioni, vedere [registrare Azure Stack con Azure](/azure-stack/operator/azure-stack-registration).



## <a name="next-steps"></a>Passaggi successivi

- Per creare un nuovo tenant di Azure AD, vedere [Access Azure Active Directory to create a new tenant](active-directory-access-create-new-tenant.md) (Accedere ad Azure Active Directory per creare un nuovo tenant)

- Per altre informazioni sul modo in cui l'accesso alle risorse viene controllato in Microsoft Azure, vedere [Informazioni sull'accesso alle risorse in Azure](../../role-based-access-control/rbac-and-directory-admin-roles.md)

- Per altre informazioni su come assegnare i ruoli in Azure AD, vedere [Come assegnare ruoli della directory agli utenti con Azure Active Directory](active-directory-users-assign-role-azure-portal.md)
