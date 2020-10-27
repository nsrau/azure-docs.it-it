---
title: Aggiungere una sottoscrizione di Azure esistente al tenant-Azure AD
description: Informazioni su come aggiungere una sottoscrizione di Azure esistente al tenant di Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 09/01/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18, contperfq4
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ed68211d034a133b923b6a2eec20ad6f1a0ffe2
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/26/2020
ms.locfileid: "92541027"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associare o aggiungere una sottoscrizione di Azure al tenant di Azure Active Directory

Una sottoscrizione di Azure ha una relazione di trust con Azure Active Directory (Azure AD). Una sottoscrizione considera attendibile Azure AD per l'autenticazione di utenti, servizi e dispositivi.

Più sottoscrizioni possono considerare attendibile la stessa directory Azure AD. Ogni sottoscrizione può considerare attendibile una sola directory.

Una o più sottoscrizioni di Azure possono stabilire una relazione di trust con un'istanza di Azure Active Directory (Azure AD) per autenticare e autorizzare le entità di sicurezza e i dispositivi nei servizi di Azure.  Quando una sottoscrizione scade, l'istanza attendibile del servizio Azure AD rimane, ma le entità di sicurezza perdono l'accesso alle risorse di Azure.

Quando un utente si iscrive a un servizio cloud Microsoft, viene creato un nuovo tenant di Azure AD e l'utente viene reso membro del ruolo di amministratore globale. Tuttavia, quando un proprietario di una sottoscrizione partecipa alla propria sottoscrizione a un tenant esistente, il proprietario non viene assegnato al ruolo di amministratore globale.

Tutti gli utenti dispongono di una singola *Home* directory per l'autenticazione. Gli utenti possono anche essere Guest in altre directory. È possibile visualizzare sia la home directory che la directory guest per ogni utente in Azure AD.

> [!Important]
> Quando si associa una sottoscrizione a una directory diversa, gli utenti che dispongono di ruoli assegnati tramite il [controllo degli accessi in base al ruolo di Azure](../../role-based-access-control/role-assignments-portal.md) perdono l'accesso. Anche gli amministratori delle sottoscrizioni classiche, tra cui l'amministratore del servizio e i coamministratori, perdono l'accesso.
>
> Se si trasferisce il cluster di Azure Kubernetes Service (AKS) a una sottoscrizione diversa o se si trasferisce la sottoscrizione proprietaria del cluster a un nuovo tenant, il cluster perderà la funzionalità a causa delle assegnazioni di ruolo perse e dei diritti dell'entità servizio. Per altre informazioni su AKS, vedere [servizio Azure Kubernetes (AKS)](../../aks/index.yml).

## <a name="before-you-begin"></a>Prima di iniziare

Prima di poter associare o aggiungere la sottoscrizione, eseguire le attività seguenti:

- Esaminare l'elenco seguente di modifiche che verranno eseguite dopo aver associato o aggiunto la sottoscrizione e come potrebbe essere interessata:

  - Gli utenti a cui sono stati assegnati i ruoli con il controllo degli accessi in base al ruolo di Azure
  - L'amministratore del servizio e il Co-Administrators perderanno l'accesso
  - Se si dispone di insiemi di credenziali delle chiavi, questi saranno inaccessibili e sarà necessario correggerli dopo l'associazione
  - Se si hanno identità gestite per le risorse, ad esempio le macchine virtuali o le app per la logica, è necessario riabilitarle o ricrearle dopo l'associazione
  - Se si dispone di un Azure Stack registrato, sarà necessario registrarlo di nuovo dopo l'associazione
  - Per altre informazioni, vedere [Trasferire una sottoscrizione di Azure in una directory di Azure AD diversa](../../role-based-access-control/transfer-subscription.md).

- Accedere con un account che:

  - Dispone di un'assegnazione di ruolo [proprietario](../../role-based-access-control/built-in-roles.md#owner) per la sottoscrizione. Per informazioni su come assegnare il ruolo proprietario, vedere [aggiungere o rimuovere assegnazioni di ruolo di Azure usando il portale di Azure](../../role-based-access-control/role-assignments-portal.md).
  - Esiste nella directory corrente e nella nuova directory. La directory corrente è associata alla sottoscrizione. Associare la nuova directory alla sottoscrizione. Per ulteriori informazioni su come ottenere l'accesso a un'altra directory, vedere [aggiungere Azure Active Directory utenti di collaborazione B2B nel portale di Azure](../external-identities/add-users-administrator.md).

- Assicurarsi di non usare una sottoscrizione provider di servizi cloud di Azure (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), una sottoscrizione interna Microsoft (MS-AZR-0015P) o una sottoscrizione Microsoft Imagine (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Associare una sottoscrizione a una directory<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Per associare una sottoscrizione esistente alla directory di Azure AD, attenersi alla seguente procedura:

1. Accedere e selezionare la sottoscrizione che si vuole usare [nella pagina sottoscrizioni in portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Selezionare **Cambia directory** .

   ![Pagina delle sottoscrizioni con l'opzione Cambia directory evidenziata](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Esaminare gli eventuali avvisi visualizzati, quindi selezionare **Cambia** .

   ![Pagina di modifica della directory con la nuova directory visualizzata](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

   Una volta modificata la directory per la sottoscrizione, verrà ricevuto un messaggio di operazione completata.

1. Selezionare **Cambia directory** nella pagina sottoscrizione per passare alla nuova directory.

   ![Pagina Switcher directory con informazioni di esempio](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

   La visualizzazione corretta di tutti gli elementi può richiedere diverse ore. Se sembra richiedere troppo tempo, controllare il **filtro della sottoscrizione globale** . Assicurarsi che la sottoscrizione spostata non sia nascosta. Potrebbe essere necessario disconnettersi dal portale di Azure ed eseguire di nuovo l'accesso per visualizzare la nuova directory.

La modifica della directory della sottoscrizione è un'operazione a livello di servizio, pertanto non influisce sulla proprietà della fatturazione della sottoscrizione. Per eliminare la directory originale, è necessario trasferire la proprietà di fatturazione della sottoscrizione a un nuovo amministratore account. Per altre informazioni sul trasferimento della proprietà della fatturazione, vedere [trasferire la proprietà di una sottoscrizione di Azure a un altro account](../../cost-management-billing/manage/billing-subscription-transfer.md).

## <a name="post-association-steps"></a>Passaggi post-associazione

Dopo aver associato una sottoscrizione a una directory diversa, potrebbe essere necessario eseguire le attività seguenti per riprendere le operazioni:

- Se sono presenti insiemi di credenziali delle chiavi, è necessario modificare l'ID tenant dell'insieme di credenziali delle chiavi. Per altre informazioni, vedere [modificare un ID tenant di Key Vault dopo uno spostamento della sottoscrizione](../../key-vault/general/move-subscription.md).

- Se sono state usate identità gestite assegnate dal sistema per le risorse, è necessario riabilitare tali identità. Se sono state usate identità gestite assegnate dall'utente, è necessario ricreare tali identità. Dopo aver riattivato o ricreato le identità gestite, è necessario ristabilire le autorizzazioni assegnate a tali identità. Per altre informazioni, vedere [Informazioni sulle identità gestite per le risorse di Azure](../managed-identities-azure-resources/overview.md).

- Se è stata registrata una Azure Stack usando questa sottoscrizione, è necessario ripetere la registrazione. Per altre informazioni, vedere [registrare Azure stack con Azure](/azure-stack/operator/azure-stack-registration).

- Per altre informazioni, vedere [Trasferire una sottoscrizione di Azure in una directory di Azure AD diversa](../../role-based-access-control/transfer-subscription.md).

## <a name="next-steps"></a>Passaggi successivi

- Per creare un nuovo tenant di Azure AD, vedere [Guida introduttiva: creare un nuovo tenant in Azure Active Directory](active-directory-access-create-new-tenant.md).

- Per altre informazioni su come Microsoft Azure controlla l'accesso alle risorse, vedere ruoli di [amministratore della sottoscrizione classica, ruoli di Azure e ruoli di amministratore Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md).

- Per altre informazioni su come assegnare i ruoli in Azure AD, vedere [assegnare ruoli di amministratore e non amministratore agli utenti con Azure Active Directory](active-directory-users-assign-role-azure-portal.md).
