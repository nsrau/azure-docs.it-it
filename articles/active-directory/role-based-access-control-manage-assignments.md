---
title: Visualizzare le assegnazioni di accesso alle risorse di Azure | Microsoft Docs
description: Visualizzare e gestire tutte le assegnazioni di controllo degli accessi in base al ruolo per qualsiasi utente o gruppo nel portale di Azure
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
editor: jeffsta
ms.assetid: e6f9e657-8ee3-4eec-a21c-78fe1b52a005
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2017
ms.author: andredm
ms.translationtype: HT
ms.sourcegitcommit: 5b6c261c3439e33f4d16750e73618c72db4bcd7d
ms.openlocfilehash: 72c695d08bdf5de003d51ffb0768184e1e4d00ba
ms.contentlocale: it-it
ms.lasthandoff: 08/28/2017

---
# <a name="view-access-assignments-for-users-and-groups-in-the-azure-portal"></a>Visualizzare le assegnazioni dell'accesso per utenti e gruppi nel portale di Azure
> [!div class="op_single_selector"]
> * [Gestire l'accesso per utente o gruppo](role-based-access-control-manage-assignments.md)
> * [Gestire l'accesso per risorsa](role-based-access-control-configure.md)

Il controllo degli accessi in base al ruolo in Azure Active Directory (Azure AD) permette di gestire l'accesso alle risorse di Azure. 

L'accesso assegnato tramite RBAC è granulare in quanto offre due modalità per limitare le autorizzazioni:

* **Ambito:** le assegnazioni di ruolo RBAC hanno un ambito riferito a una sottoscrizione, una risorsa o un gruppo di risorse specifici. Un utente al quale è stato concesso l'accesso a una singola risorsa non può accedere alle altre risorse nella stessa sottoscrizione.
* **Ruolo:** all'interno dell'ambito dell'assegnazione, l'accesso è ulteriormente limitato con l'assegnazione di un ruolo. I ruoli possono essere di livello superiore, ad esempio quello di proprietario, o specifici, ad esempio quello di lettore di macchina virtuale.

I ruoli possono essere assegnati solo all'interno della sottoscrizione, del gruppo di risorse o della risorsa che rientra nell'ambito dell'assegnazione. È tuttavia possibile visualizzare in un'unica posizione tutte le assegnazioni di accesso di un determinato utente o gruppo. È possibile avere fino a 2000 assegnazioni di ruolo in ogni sottoscrizione. 

Altre informazioni su come [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](role-based-access-control-configure.md).

## <a name="view-access-assignments"></a>Visualizzare le assegnazioni di accesso
Per individuare le assegnazioni di accesso di un singolo utente o gruppo, avviare Azure Active Directory nel [portale di Azure](http://portal.azure.com).

1. Selezionare **Azure Active Directory**. Se questa opzione non è visibile nell'elenco di navigazione, selezionare **Altri servizi** e scorrere fino ad **Azure Active Directory**.
2. Selezionare **Utenti e gruppi** e **Tutti gli utenti** o **Tutti i gruppi**. Questo esempio si incentra sui singoli utenti.
    ![Gestire utenti e gruppi in Azure Active Directory - Schermata](./media/role-based-access-control-manage-assignments/rbac_users_groups.png)
3. Cercare l'utente in base al nome o al nome utente.
4. Selezionare **Risorse di Azure** nel pannello dell'utente. Vengono visualizzate tutte le assegnazioni di accesso dell'utente.

### <a name="read-permissions-to-view-assignments"></a>Autorizzazioni di lettura per la visualizzazione delle assegnazioni
Questa pagina mostra solo le assegnazioni di accesso per le quali si dispone dell'autorizzazione di lettura. Ad esempio, si ha accesso in lettura alla sottoscrizione A e si apre il pannello delle risorse di Azure per controllare le assegnazioni di un utente. Vengono visualizzate le assegnazioni di accesso per la sottoscrizione A, ma non sarà possibile vedere che l'utente dispone di assegnazioni di accesso per la sottoscrizione B.

## <a name="delete-access-assignments"></a>Eliminare le assegnazioni di accesso
Da questo pannello è possibile eliminare le assegnazioni di accesso assegnate direttamente a un utente o gruppo. Se l'assegnazione di accesso è stata ereditato da un gruppo padre, è necessario passare alla risorsa o alla sottoscrizione e gestire da qui l'assegnazione.

1. Dall'elenco di tutte le assegnazioni di accesso di un utente o gruppo, selezionare quella da eliminare.
2. Selezionare **Rimuovi** e **Sì** per confermare.
    ![Rimuovere un'assegnazione di accesso - Schermata](./media/role-based-access-control-manage-assignments/delete_assignment.png)

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione al controllo degli accessi in base al ruolo, vedere [Usare le assegnazioni di ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](role-based-access-control-configure.md)
* Vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](role-based-access-built-in-roles.md)


