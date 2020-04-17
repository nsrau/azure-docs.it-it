---
title: Aggiungere una sottoscrizione di Azure esistente al tenant - Azure ADAdd an existing Azure subscription to your tenant - Azure AD
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
ms.openlocfilehash: 104bf51fb03d88ab0e5efd25ebebb0e3060bc264
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457927"
---
# <a name="associate-or-add-an-azure-subscription-to-your-azure-active-directory-tenant"></a>Associare o aggiungere una sottoscrizione di Azure al tenant di Azure Active Directory

Una sottoscrizione di Azure ha una relazione di trust con Azure Active Directory (Azure AD). Una sottoscrizione considera attendibile Azure AD per autenticare utenti, servizi e dispositivi.

Più sottoscrizioni possono considerare attendibile la stessa directory di Azure AD. Ogni sottoscrizione può considerare attendibile solo una singola directory.

Se la sottoscrizione scade, non sarà più possibile accedere alle altre risorse associate alla sottoscrizione. Tuttavia, la directory di Azure AD rimane in Azure.However, the Azure AD directory remains in Azure. È possibile associare e gestire la directory usando una sottoscrizione di Azure diversa.

Tutti gli utenti dispongono di un'unica *home* directory per l'autenticazione. Gli utenti possono anche essere guest in altre directory. È possibile visualizzare sia la home directory che la directory guest per ogni utente in Azure AD.

> [!Important]
> Quando si associa una sottoscrizione a una directory diversa, gli utenti a cui sono assegnati ruoli tramite il controllo degli [accessi in base](../../role-based-access-control/role-assignments-portal.md) al ruolo perdono l'accesso. Anche gli amministratori delle sottoscrizioni classiche, tra cui l'amministratore del servizio e i coamministratori, perdono l'accesso.
>
> Anche le assegnazioni dei criteri vengono rimosse da una sottoscrizione quando la sottoscrizione è associata a una directory diversa.
>
> Se si sposta il cluster del servizio Azure Kubernetes Service (AKS) in una sottoscrizione diversa o si sposta la sottoscrizione proprietaria del cluster in un nuovo tenant, il cluster perde funzionalità a causa della perdita delle assegnazioni di ruolo e dei diritti dell'entità servizio. Per altre informazioni su AKS, vedere [Servizio Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/).


## <a name="before-you-begin"></a>Prima di iniziare

Prima di associare o aggiungere la sottoscrizione, eseguire le attività seguenti:Before you can associate or add your subscription, do the following tasks:

- Esaminare il seguente elenco di modifiche e come si potrebbe essere interessati:

  - Gli utenti a cui sono stati assegnati ruoli tramite il controllo degli accessi in base al ruolo perderanno l'accesso
  - L'amministratore del servizio e i coamministratori perderanno l'accesso
  - Se si dispone di insiemi di credenziali delle chiavi, saranno inaccessibili e sarà necessario correggerli dopo l'associazione
  - Se si dispone di identità gestite per risorse quali Macchine virtuali o App per la logica, è necessario riabilitarle o ricrearle dopo l'associazione
  - Se si dispone di uno stack di Azure registrato, sarà necessario registrarlo nuovamente dopo l'associazioneIf you have a registered Azure Stack, you'll have to re-register it after association

- Accedere con un account che:

  - Dispone di [un'assegnazione](../../role-based-access-control/built-in-roles.md#owner) di ruolo Proprietario per la sottoscrizione. Per informazioni su come assegnare il ruolo Proprietario, vedere [Gestire l'accesso alle risorse](../../role-based-access-control/role-assignments-portal.md)di Azure tramite RBAC e il portale di Azure.For information about how to assign the Owner role, see Manage access to Azure resources using RBAC and the Azure portal.
  - Esiste sia nella directory corrente che nella nuova directory. La directory corrente è associata alla sottoscrizione. La nuova directory verrà associata alla sottoscrizione. Per altre informazioni su come ottenere l'accesso a un'altra directory, vedere Aggiungere utenti di Collaborazione B2B di Azure nel portale di Azure.For more information about getting access to another directory, see [Add Azure Active Directory B2B collaboration users in the Azure portal.](../b2b/add-users-administrator.md)

- Assicurarsi di non usare una sottoscrizione provider di servizi cloud di Azure (MS-AZR-0145P, MS-AZR-0146P, MS-AZR-159P), una sottoscrizione interna Microsoft (MS-AZR-0015P) o una sottoscrizione Microsoft Imagine (MS-AZR-0144P).

## <a name="associate-a-subscription-to-a-directory"></a>Associare una sottoscrizione a una directory<a name="to-associate-an-existing-subscription-to-your-azure-ad-directory"></a>

Per associare una sottoscrizione esistente alla directory di Azure AD, attenersi alla seguente procedura:

1. Eseguire l'accesso e selezionare una sottoscrizione dalla [pagina Sottoscrizioni del portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

1. Selezionare **Cambia directory**.

    ![Pagina delle sottoscrizioni con l'opzione Cambia directory evidenziata](media/active-directory-how-subscriptions-associated-directory/change-directory-in-azure-subscriptions.png)

1. Esaminare eventuali avvisi che vengono visualizzati e selezionare **Cambia**.

    ![Pagina di modifica della directory con la nuova directory visualizzata](media/active-directory-how-subscriptions-associated-directory/edit-directory-ui.png)

    La directory è stata modificata per la sottoscrizione e viene visualizzato un messaggio di operazione riuscita.

    ![Messaggio di operazione riuscita relativo alla modifica della directory](media/active-directory-how-subscriptions-associated-directory/edit-directory-success.png)

Utilizzare **Cambia directory** per passare alla nuova directory. Possono volerci diverse ore perché tutto si presenti correttamente. Se sembra che stia impiegando troppo tempo, controllare il **filtro di sottoscrizione globale**. Assicurarsi che la sottoscrizione spostata non sia nascosta. Potrebbe essere necessario disconnettersi dal portale di Azure e accedere di nuovo per visualizzare la nuova directory.

![Pagina del programma di commutazione di directory, con informazioni di esempio](media/active-directory-how-subscriptions-associated-directory/directory-switcher.png)

La modifica della directory della sottoscrizione è un'operazione a livello di servizio, pertanto non influisce sulla proprietà della fatturazione della sottoscrizione. L'amministratore dell'account può comunque modificare l'amministratore del servizio dal [Centro account](https://account.azure.com/subscriptions). Per eliminare la directory originale, è necessario trasferire la proprietà della fatturazione dell'abbonamento a un nuovo amministratore dell'account. Per altre informazioni sul trasferimento della proprietà della fatturazione, vedere Trasferire la proprietà di una [sottoscrizione di Azure a un altro account.](../../cost-management-billing/manage/billing-subscription-transfer.md)

## <a name="post-association-steps"></a>Passaggi post-associazione

Dopo aver associato una sottoscrizione a una directory diversa, potrebbe essere necessario eseguire le attività seguenti per riprendere le operazioni:After you associate a subscription to a different directory, you might need to do the following tasks to resume operations:

- Se sono presenti insiemi di credenziali delle chiavi, è necessario modificare l'ID tenant dell'insieme di credenziali delle chiavi. Per ulteriori informazioni, vedere [Modificare un ID tenant dell'insieme](../../key-vault/general/subscription-move-fix.md)di credenziali delle chiavi dopo uno spostamento della sottoscrizione.

- Se sono state utilizzate identità gestite assegnate dal sistema per le risorse, è necessario riabilitare queste identità. Se sono state utilizzate identità gestite assegnate dall'utente, è necessario ricreare queste identità. Dopo aver riabilitato o ricreato le identità gestite, è necessario ristabilire le autorizzazioni assegnate a tali identità. Per altre informazioni, vedere [Che cos'è l'identità gestita per le risorse](../managed-identities-azure-resources/overview.md)di Azure? .

- Se è stato registrato uno stack di Azure usando questa sottoscrizione, è necessario eseguire nuovamente la registrazione. Per altre informazioni, vedere [Registrare Azure Stack con Azure.For](/azure-stack/operator/azure-stack-registration)more information, see Register Azure Stack with Azure.

## <a name="next-steps"></a>Passaggi successivi

- Per creare un nuovo tenant di Azure AD, vedere [Guida introduttiva: Creare un nuovo tenant in Azure Active Directory.](active-directory-access-create-new-tenant.md)

- Per altre informazioni su come Microsoft Azure controlla l'accesso alle risorse, vedere Ruoli di amministratore della [sottoscrizione classica, ruoli di controllo degli accessi in base](../../role-based-access-control/rbac-and-directory-admin-roles.md)al ruolo di Azure e ruoli di amministratore di Azure AD.

- Per altre informazioni su come assegnare ruoli in Azure AD, vedere [Assegnare ruoli di amministratore e non amministratori agli utenti con Azure Active Directory.](active-directory-users-assign-role-azure-portal.md)
