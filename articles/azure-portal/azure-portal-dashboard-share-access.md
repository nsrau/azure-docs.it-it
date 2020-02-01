---
title: Condividere i dashboard portale di Azure usando il controllo degli accessi in base al ruolo
description: Questo articolo illustra come condividere un dashboard nel portale di Azure tramite il Controllo degli accessi in base al ruolo.
services: azure-portal
documentationcenter: ''
author: mgblythe
manager: mtillman
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: azure-portal
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 01/29/2020
ms.author: mblythe
ms.openlocfilehash: e8d251cef9e67cb8fc0c11df8ce546383f75a679
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76900827"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Condividere i dashboard di Azure tramite il Controllo degli accessi in base al ruolo

Dopo aver configurato un dashboard, è possibile pubblicarlo e condividerlo con altri utenti nell'organizzazione. Si consente ad altri utenti di visualizzare il dashboard usando il [controllo degli accessi in base al ruolo](../role-based-access-control/role-assignments-portal.md) (RBAC) di Azure. Assegnare un utente o un gruppo di utenti a un ruolo. Tale ruolo definisce se gli utenti possono visualizzare o modificare il dashboard pubblicato.

Tutti i dashboard pubblicati vengono implementati come risorse di Azure. Esistono come elementi gestibili all'interno della sottoscrizione e sono contenuti in un gruppo di risorse. Dal punto di vista del controllo di accesso, i dashboard non sono diversi da altre risorse, ad esempio una macchina virtuale o un account di archiviazione.

> [!TIP]
> I singoli riquadri del dashboard applicano requisiti di controllo di accesso personalizzati in base alle risorse che visualizzano. È possibile condividere un dashboard su larga scala proteggendo i dati nei singoli riquadri.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Informazioni sul controllo di accesso per i dashboard

Con il Controllo degli accessi in base al ruolo è possibile assegnare utenti ai ruoli a tre diversi livelli di ambito:

* sottoscrizione
* gruppo di risorse
* resource

Le autorizzazioni assegnate ereditano dalla sottoscrizione alla risorsa. Il dashboard pubblicato è una risorsa. È possibile che gli utenti siano già assegnati ai ruoli per la sottoscrizione che si applicano al dashboard pubblicato.

Si supponga di avere una sottoscrizione di Azure e che ai vari membri del team siano stati assegnati i ruoli di *proprietario*, *collaboratore* o *lettore* della sottoscrizione. Gli utenti proprietari o collaboratori possono elencare, visualizzare, creare, modificare o eliminare Dashboard nella sottoscrizione. Gli utenti che sono lettori possono elencare e visualizzare i dashboard, ma non modificarli o eliminarli. Gli utenti con accesso in lettura possono apportare modifiche locali a un dashboard pubblicato, ad esempio durante la risoluzione di un problema, ma non possono pubblicare nuovamente tali modifiche nel server. Possono creare una copia privata del dashboard per se stessi.

È anche possibile assegnare autorizzazioni al gruppo di risorse che contiene diversi dashboard o a un singolo dashboard. Ad esempio, si potrebbe decidere che un gruppo di utenti deve avere autorizzazioni limitate per la sottoscrizione, ma un accesso più ampio per un determinato dashboard. Assegnare gli utenti a un ruolo per il dashboard.

## <a name="publish-dashboard"></a>Pubblicare dashboard

Si supponga di configurare un dashboard che si vuole condividere con un gruppo di utenti nella sottoscrizione. I passaggi seguenti illustrano come condividere un dashboard con un gruppo denominato storage managers. È possibile assegnare un nome al gruppo in qualsiasi modo. Per ulteriori informazioni, vedere [gestione dei gruppi in Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Prima di assegnare l'accesso, è necessario pubblicare il dashboard.

1. Nel dashboard selezionare **Condividi**.

    ![Selezionare la condivisione per il dashboard](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. In **condivisione e controllo di accesso**selezionare **pubblica**.

    ![pubblicare il dashboard](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Per impostazione predefinita, la condivisione pubblica il dashboard in un gruppo di risorse denominato **Dashboard**.

Il dashboard viene pubblicato. Se le autorizzazioni ereditate dalla sottoscrizione sono appropriate, non è necessario eseguire altre operazioni. Gli altri utenti dell'organizzazione possono accedere e modificare il dashboard in base al relativo ruolo a livello di sottoscrizione.

## <a name="assign-access-to-a-dashboard"></a>Assegnare l'accesso a un dashboard

È possibile assegnare un gruppo di utenti a un ruolo per tale dashboard.

1. Dopo la pubblicazione del dashboard, in **condivisione e controllo di accesso**selezionare **Gestisci utenti**.

    ![gestire gli utenti per un dashboard](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

    Per accedere a **condivisione e controllo di accesso** da un dashboard, selezionare l'opzione **Condividi** o non **condividere** .

1. Selezionare **assegnazioni di ruolo** per visualizzare gli utenti esistenti a cui è già stato assegnato un ruolo per questo dashboard.

1. Per aggiungere un nuovo utente o gruppo, selezionare **Aggiungi**.

    ![aggiungere un utente per l'accesso al dashboard](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Consente di selezionare il ruolo che rappresenta le autorizzazioni da concedere. Per questo esempio selezionare **Collaboratore**.

1. Consente di selezionare l'utente o il gruppo da assegnare al ruolo. Se l'utente o il gruppo che si sta cercando non viene visualizzato nell'elenco, usare la casella di ricerca. L'elenco dei gruppi disponibili dipende dai gruppi creati in Active Directory.

1. Al termine dell'operazione di aggiunta di utenti o gruppi, scegliere **OK**.

    La nuova assegnazione viene aggiunta all'elenco di utenti. L' **accesso** viene elencato come **assegnato** anziché **ereditato**.

    ![Ruoli assegnati](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Passaggi successivi

* Per un elenco dei ruoli, vedere [ruoli predefiniti per le risorse di Azure](../role-based-access-control/built-in-roles.md).
* Per informazioni sulla gestione delle risorse, vedere [gestire le risorse di Azure usando il portale di Azure](resource-group-portal.md).

