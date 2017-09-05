---
title: Condividere i dashboard del portale di Azure tramite l'uso di RBAC | Microsoft Docs
description: Questo articolo illustra come condividere un dashboard nel portale di Azure tramite il controllo degli accessi basato sul ruolo.
services: azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 8908a6ce-ae0c-4f60-a0c9-b3acfe823365
ms.service: multiple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 08/01/2016
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 25e4506cc2331ee016b8b365c2e1677424cf4992
ms.openlocfilehash: 927ec9251b5231a5d9a0e8278f5dc46e9abdde0e
ms.contentlocale: it-it
ms.lasthandoff: 08/24/2017

---
# <a name="share-azure-dashboards-by-using-role-based-access-control"></a>Condividere i dashboard di Azure tramite il controllo degli accessi basato sul ruolo
Dopo aver configurato un dashboard, è possibile pubblicarlo e condividerlo con altri utenti nell'organizzazione. Si consente ad altri utenti di visualizzare il proprio dashboard tramite il [Controllo degli accessi basato sul ruolo](../active-directory/role-based-access-control-configure.md) di Azure. Si assegna un utente o gruppo di utenti a un ruolo e questo ruolo definisce se gli utenti possono visualizzare o modificare il dashboard pubblicato. 

Tutti i dashboard pubblicati vengono implementati come risorse di Azure, di conseguenza costituiscono elementi gestibili all'interno della sottoscrizione e sono contenuti in un gruppo di risorse.  Dal punto di vista del controllo di accesso, i dashboard non sono diversi da altre risorse, ad esempio una macchina virtuale o un account di archiviazione.

> [!TIP]
> I singoli riquadri del dashboard applicano requisiti di controllo di accesso personalizzati in base alle risorse che visualizzano.  Perciò è possibile progettare un dashboard condiviso più ampiamente, continuando comunque a proteggere i dati nei singoli riquadri.
> 
> 

## <a name="understanding-access-control-for-dashboards"></a>Informazioni sul controllo di accesso per i dashboard
Con il controllo degli accessi in base al ruolo è possibile assegnare utenti ai ruoli a tre diversi livelli di ambito:

* subscription
* gruppo di risorse
* resource

Le autorizzazioni assegnate vengono ereditate dalla sottoscrizione fino alla risorsa. Il dashboard pubblicato è una risorsa. È quindi possibile che esistano già utenti assegnati a ruoli per la sottoscrizione, che funzionano anche con il dashboard pubblicato. 

Di seguito è fornito un esempio.  Si supponga di avere una sottoscrizione di Azure e che ai vari membri del team siano stati assegnati i ruoli di **proprietario**, **collaboratore** o **lettore** della sottoscrizione. Gli utenti con il ruolo di proprietario o collaboratore possono elencare, visualizzare, creare, modificare o eliminare dashboard nella sottoscrizione.  Gli utenti con il ruolo di lettore possono elencare e visualizzare i dashboard, ma non modificarli o eliminarli.  Gli utenti con accesso in lettura possono apportare modifiche locali a un dashboard pubblicato, ad esempio per la risoluzione di un problema, ma non pubblicarle nel server.  Hanno comunque la possibilità di creare una copia privata del dashboard per se.

È tuttavia possibile assegnare autorizzazioni anche al gruppo di risorse che contiene diversi dashboard o a un singolo dashboard. Ad esempio, si potrebbe decidere che un gruppo di utenti deve avere autorizzazioni limitate per la sottoscrizione, ma un accesso più ampio per un determinato dashboard. Assegnare gli utenti a un ruolo per il dashboard. 

## <a name="publish-dashboard"></a>Pubblicare dashboard
Si supponga di che aver completato la configurazione di un dashboard che si vuole condividere con un gruppo di utenti nella sottoscrizione. I passaggi seguenti illustrano un gruppo personalizzato denominato Storage Managers, o gestori di archiviazione, ma è possibile assegnare al gruppo qualsiasi altro nome. Per informazioni sulla creazione di un gruppo di Active Directory e sull'aggiunta di utenti a tale gruppo, vedere [Gestione dei gruppi in Azure Active Directory](../active-directory/active-directory-accessmanagement-manage-groups.md).

1. Nel dashboard selezionare **Condividi**.
   
     ![Selezionare Condividi](./media/azure-portal-dashboard-share-access/select-share.png)
2. Prima di assegnare l'accesso, è necessario pubblicare il dashboard. Per impostazione predefinita, il dashboard verrà pubblicato in un gruppo di risorse denominato **dashboard**. Selezionare **Pubblica**.
   
     ![Pubblica](./media/azure-portal-dashboard-share-access/publish.png)

Il dashboard viene pubblicato. Se le autorizzazioni ereditate dalla sottoscrizione sono appropriate, non è necessario eseguire altre operazioni. Altri utenti nell'organizzazione potranno accedere e modificare il dashboard in base al proprio ruolo a livello di sottoscrizione. Tuttavia, per questa esercitazione è possibile assegnare un gruppo di utenti a un ruolo per quel dashboard.

## <a name="assign-access-to-a-dashboard"></a>Assegnare l'accesso a un dashboard
1. Dopo aver pubblicato il dashboard, selezionare **Gestisci utenti**.
   
     ![Gestisci utenti](./media/azure-portal-dashboard-share-access/manage-users.png)
2. Verrà visualizzato un elenco di utenti esistenti già stati assegnati a un ruolo per questo dashboard. L'elenco di utenti esistenti è diverso da quello nell'immagine seguente. È probabile che le assegnazioni vengono ereditate dalla sottoscrizione. Per aggiungere un nuovo utente o gruppo, selezionare **Aggiungi**.
   
     ![Aggiungi utente](./media/azure-portal-dashboard-share-access/existing-users.png)
3. Selezionare il ruolo che rappresenta le autorizzazioni da concedere. Per questo esempio selezionare **Collaboratore**.
   
     ![selezionare il ruolo](./media/azure-portal-dashboard-share-access/select-role.png)
4. Selezionare l'utente o il gruppo che si vuole assegnare al ruolo. Se l'utente o il gruppo desiderato non è visualizzato nell'elenco, usare la casella di ricerca. L'elenco dei gruppi disponibili dipenderà dai gruppi creati in Active Directory.
   
     ![Seleziona utente](./media/azure-portal-dashboard-share-access/select-user.png) 
5. Al termine dell'operazione di aggiunta di utenti o gruppi, scegliere **OK**. 
6. La nuova assegnazione viene aggiunta all'elenco di utenti. Si noti che il relativo **Accesso** è elencato come **Assegnato** anziché **Ereditato**.
   
     ![Ruoli assegnati](./media/azure-portal-dashboard-share-access/assigned-roles.png)

## <a name="next-steps"></a>Passaggi successivi
* Per un elenco di ruoli, vedere [Controllo degli accessi in base al ruolo: ruoli predefiniti](../active-directory/role-based-access-built-in-roles.md).
* Per altre informazioni sulla gestione delle risorse, vedere [Gestire le risorse di Azure mediante il portale](resource-group-portal.md).


