---
title: Aggiungere una sottoscrizione di Azure esistente al tenant - Azure Active Directory | Microsoft Docs
description: Informazioni su come aggiungere una sottoscrizione di Azure esistente al tenant di Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f2f61792235d13ef801cffb3a69c32df54f058a9
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073594"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associare o aggiungere una sottoscrizione di Azure al tenant di Azure Active Directory

Una sottoscrizione di Azure ha una relazione di trust con Azure Active Directory (Azure AD). Una sottoscrizione considera attendibile Azure AD per l'autenticazione di utenti, servizi e dispositivi.

Più sottoscrizioni possono considerare attendibile la stessa directory Azure AD. Ogni sottoscrizione può considerare attendibile una sola directory.

Se la sottoscrizione scade, non sarà più possibile accedere alle altre risorse associate alla sottoscrizione. Tuttavia, il Azure AD directory rimane in Azure. È possibile associare e gestire la directory usando una sottoscrizione di Azure diversa.

Tutti gli utenti dispongono di una singola *Home* directory per l'autenticazione. Gli utenti possono anche essere Guest in altre directory. È possibile visualizzare sia la home directory che la directory guest per ogni utente in Azure AD.

> [!Important]
> Quando si associa una sottoscrizione a una directory diversa, gli utenti che dispongono di ruoli assegnati tramite il [controllo degli accessi in base al ruolo (RBAC)](../../role-based-access-control/role-assignments-portal.md) perdono l'accesso. Anche gli amministratori delle sottoscrizioni classiche, tra cui l'amministratore del servizio e i coamministratori, perdono l'accesso.
>
> Anche le assegnazioni dei criteri vengono rimosse da una sottoscrizione quando la sottoscrizione è associata a una directory diversa.
>
> Se si trasferisce il cluster di Azure Kubernetes Service (AKS) a una sottoscrizione diversa o se si trasferisce la sottoscrizione proprietaria del cluster a un nuovo tenant, il cluster perderà la funzionalità a causa delle assegnazioni di ruolo perse e dei diritti dell'entità servizio. Per altre informazioni su AKS, vedere [servizio Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/).


## <a name="before-you-begin"></a>Prima di iniziare

Prima di poter associare o aggiungere la sottoscrizione, eseguire le attività seguenti:

- Esaminare l'elenco di modifiche seguente e la relativa modalità di impatto:

  - Gli utenti a cui sono stati assegnati ruoli con RBAC perderanno l'accesso
  - L'amministratore del servizio e i coamministratori perderanno l'accesso
  - Se si dispone di insiemi di credenziali delle chiavi, questi saranno inaccessibili e sarà necessario correggerli dopo l'associazione
  - Se si hanno identità gestite per le risorse, ad esempio le macchine virtuali o le app per la logica, è necessario riabilitarle o ricrearle dopo l'associazione
  - Se si dispone di un Azure Stack registrato, sarà necessario registrarlo di nuovo dopo l'associazione

- Accedere con un account che:

  - Dispone di un'assegnazione di ruolo [proprietario](../../role-based-access-control/built-in-roles.md#owner) per la sottoscrizione. Per informazioni su come assegnare il ruolo proprietario, vedere [gestire l'accesso alle risorse di Azure usando RBAC e il portale di Azure](../../role-based-access-control/role-assignments-portal.md).
  - Esiste nella directory corrente e nella nuova directory. La directory corrente è associata alla sottoscrizione. Associare la nuova directory alla sottoscrizione. Per ulteriori informazioni su come ottenere l'accesso a un'altra directory, vedere [aggiungere Azure Active Directory utenti di collaborazione B2B nel portale di Azure](../b2b/add-users-administrator.md).

- Assicurarsi di non usare una sottoscrizione provider di servizi cloud di Azure (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), una sottoscrizione interna Microsoft (MS-AZR-0015P) o una sottoscrizione Microsoft Imagine (MS-AZR-0144P).

## Associare una sottoscrizione a una directory<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Per associare una sottoscrizione esistente alla directory di Azure AD, attenersi alla seguente procedura:

1. Eseguire l'accesso e selezionare una sottoscrizione dalla [pagina Sottoscrizioni del portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Selezionare **Cambia directory**.

    ![Pagina delle sottoscrizioni con l'opzione Cambia directory evidenziata](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Esaminare eventuali avvisi che vengono visualizzati e selezionare **Cambia**.

    ![Pagina di modifica della directory con la nuova directory visualizzata](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    La directory è stata modificata per la sottoscrizione e viene visualizzato un messaggio di operazione riuscita.

    ![Messaggio di operazione completata sulla modifica della directory](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

Usare **Switch directory** per passare alla nuova directory. La visualizzazione corretta di tutti gli elementi può richiedere diverse ore. Se sembra richiedere troppo tempo, controllare il **filtro della sottoscrizione globale**. Assicurarsi che la sottoscrizione spostata non sia nascosta. Potrebbe essere necessario disconnettersi dal portale di Azure ed eseguire di nuovo l'accesso per visualizzare la nuova directory.

![Pagina Switcher directory con informazioni di esempio](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

La modifica della directory della sottoscrizione è un'operazione a livello di servizio, pertanto non influisce sulla proprietà della fatturazione della sottoscrizione. L'amministratore dell'account può comunque modificare l'amministratore del servizio dal [Centro account](https://account.azure.com/subscriptions). Per eliminare la directory originale, è necessario trasferire la proprietà di fatturazione della sottoscrizione a un nuovo amministratore account. Per altre informazioni sul trasferimento della proprietà della fatturazione, vedere [trasferire la proprietà di una sottoscrizione di Azure a un altro account](../../billing/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Passaggi post-associazione

Dopo aver associato una sottoscrizione a una directory diversa, potrebbe essere necessario eseguire le attività seguenti per riprendere le operazioni:

- Se sono presenti insiemi di credenziali delle chiavi, è necessario modificare l'ID tenant dell'insieme di credenziali delle chiavi. Per altre informazioni, vedere [modificare un ID tenant di Key Vault dopo uno spostamento della sottoscrizione](../../key-vault/key-vault-subscription-move-fix.md).

- Se sono state usate identità gestite assegnate dal sistema per le risorse, è necessario riabilitare tali identità. Se sono state usate identità gestite assegnate dall'utente, è necessario ricreare tali identità. Dopo aver riattivato o ricreato le identità gestite, è necessario ristabilire le autorizzazioni assegnate a tali identità. Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](../managed-identities-azure-resources/overview.md).

- Se è stata registrata una Azure Stack usando questa sottoscrizione, è necessario ripetere la registrazione. Per altre informazioni, vedere [registrare Azure stack con Azure](/azure-stack/operator/azure-stack-registration).

## <a name="next-steps"></a>Passaggi successivi

- Per creare un nuovo tenant di Azure AD, vedere [Guida introduttiva: creare un nuovo tenant in Azure Active Directory](active-directory-access-create-new-tenant.md).

- Per altre informazioni su come Microsoft Azure controlla l'accesso alle risorse, vedere ruoli di [amministratore della sottoscrizione classica, ruoli RBAC di Azure e ruoli di amministratore Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Per altre informazioni su come assegnare i ruoli in Azure AD, vedere [assegnare ruoli di amministratore e non amministratore agli utenti con Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
