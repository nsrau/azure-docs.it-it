---
title: Condividere dashboard del portale di Azure tramite il controllo degli accessi in base al ruoloShare Azure portal dashboards by using Role-Based Access Control
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
ms.date: 03/23/2020
ms.author: mblythe
ms.openlocfilehash: 711e8a7ae31888c9754252d88404d90f24e8030e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131987"
---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Condividere i dashboard di Azure tramite il Controllo degli accessi in base al ruolo

Dopo aver configurato un dashboard, è possibile pubblicarlo e condividerlo con altri utenti nell'organizzazione. Consentire ad altri utenti di visualizzare il dashboard usando il controllo degli accessi in base al ruolo di Azure.You allow others to view your dashboard by using Azure [Role-Based Access Control](../role-based-access-control/role-assignments-portal.md) (RBAC). Assegnare un utente o un gruppo di utenti a un ruolo. Tale ruolo definisce se tali utenti possono visualizzare o modificare il dashboard pubblicato.

Tutti i dashboard pubblicati vengono implementati come risorse di Azure.All published dashboards are implemented as Azure resources. Esistono come elementi gestibili all'interno della sottoscrizione e sono contenuti in un gruppo di risorse. Dal punto di vista del controllo di accesso, i dashboard non sono diversi da altre risorse, ad esempio una macchina virtuale o un account di archiviazione.

> [!TIP]
> I singoli riquadri del dashboard applicano requisiti di controllo di accesso personalizzati in base alle risorse che visualizzano. È possibile condividere un dashboard su vasta scala proteggendo i dati nei singoli riquadri.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Informazioni sul controllo di accesso per i dashboard

Con il Controllo degli accessi in base al ruolo è possibile assegnare utenti ai ruoli a tre diversi livelli di ambito:

* sottoscrizione
* gruppo di risorse
* resource

Le autorizzazioni assegnate ereditano dalla sottoscrizione fino alla risorsa. Il dashboard pubblicato è una risorsa. È possibile che gli utenti siano già assegnati ai ruoli per la sottoscrizione che si applicano al dashboard pubblicato.

Si supponga di avere una sottoscrizione di Azure e che ai vari membri del team siano stati assegnati i ruoli di *proprietario*, *collaboratore* o *lettore* della sottoscrizione. Gli utenti proprietari o collaboratori possono elencare, visualizzare, creare, modificare o eliminare dashboard all'interno della sottoscrizione. Gli utenti che sono lettori possono elencare e visualizzare i dashboard, ma non possono modificarli o eliminarli. Gli utenti con accesso in lettura possono apportare modifiche locali a un dashboard pubblicato, ad esempio durante la risoluzione di un problema, ma non possono pubblicare tali modifiche nel server. Possono creare una copia privata del dashboard per se stessi.

È anche possibile assegnare autorizzazioni al gruppo di risorse che contiene diversi dashboard o a un singolo dashboard. Ad esempio, si potrebbe decidere che un gruppo di utenti deve avere autorizzazioni limitate per la sottoscrizione, ma un accesso più ampio per un determinato dashboard. Assegnare tali utenti a un ruolo per il dashboard.

## <a name="publish-dashboard"></a>Pubblicare dashboard

Si supponga di configurare un dashboard che si desidera condividere con un gruppo di utenti nella sottoscrizione. La procedura seguente illustra come condividere un dashboard con un gruppo denominato Storage Managers.The following steps show how to share a dashboard to a group called Storage Managers. Puoi nominare il tuo gruppo come vuoi. Per altre informazioni, vedere [Gestione dei gruppi in Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).For more information, see Managing groups in Azure Active Directory .

Prima di assegnare l'accesso, è necessario pubblicare il dashboard.

1. Nel dashboard selezionare **Condividi**.

    ![selezionare la condivisione per il dashboard](./media/azure-portal-dashboard-share-access/share-dashboard-for-access-control.png)

1. In **Condivisione e controllo di accesso**selezionare **Pubblica**.

    ![pubblicare il dashboard](./media/azure-portal-dashboard-share-access/publish-dashboard-for-access-control.png)

     Per impostazione predefinita, la condivisione pubblica il dashboard in un gruppo di risorse denominato **dashboard .** Per selezionare un gruppo di risorse diverso, deselezionare la casella di controllo.

Il dashboard viene pubblicato. Se le autorizzazioni ereditate dalla sottoscrizione sono adatte, non è necessario eseguire altre operazione. Gli altri utenti dell'organizzazione possono accedere e modificare il dashboard in base al ruolo a livello di sottoscrizione.

## <a name="assign-access-to-a-dashboard"></a>Assegnare l'accesso a un dashboard

È possibile assegnare un gruppo di utenti a un ruolo per tale dashboard.

1. Dopo aver pubblicato il dashboard, selezionare l'opzione **Condividi** o **Annulla condivisione** per accedere a **Condivisione e controllo di accesso**.

1. In **Condivisione e controllo di accesso**selezionare Gestisci **utenti**.

    ![gestire gli utenti per un dashboard](./media/azure-portal-dashboard-share-access/manage-users-for-access-control.png)

1. Selezionare **Assegnazioni di** ruolo per visualizzare gli utenti esistenti a cui è già stato assegnato un ruolo per il dashboard.

1. Per aggiungere un nuovo utente o gruppo, selezionare **Aggiungi,** quindi **Aggiungi assegnazione di ruolo**.

    ![aggiungere un utente per l'accesso al dashboard](./media/azure-portal-dashboard-share-access/manage-users-existing-users.png)

1. Selezionare il ruolo che rappresenta le autorizzazioni da concedere. Per questo esempio selezionare **Collaboratore**.

1. Selezionare l'utente o il gruppo da assegnare al ruolo. Se l'utente o il gruppo che si sta cercando non è visualizzato nell'elenco, utilizzare la casella di ricerca. L'elenco dei gruppi disponibili dipende dai gruppi creati in Active Directory.

1. Al termine dell'aggiunta di utenti o gruppi, selezionare **Salva**.

## <a name="next-steps"></a>Passaggi successivi

* Per un elenco dei ruoli, vedere Ruoli predefiniti per le risorse di Azure.For a list of roles, see [Built-in roles for Azure resources](../role-based-access-control/built-in-roles.md).
* Per altre informazioni sulla gestione delle risorse, vedere Gestire le risorse di Azure tramite il portale di Azure.To learn about managing resources, see [Manage Azure resources by using the Azure portal.](resource-group-portal.md)

