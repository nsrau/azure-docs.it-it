---
title: Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure | Documentazione Microsoft
description: "Informazioni su come gestire il controllo degli accessi in base al ruolo (RBAC) con l'interfaccia della riga di comando di Azure, ad esempio ottenere un elenco dei ruoli e delle relative azioni, nonché assegnare i ruoli nell'ambito della sottoscrizione e dell'applicazione."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 73e3211416a1d110f1714872290a4156f3d194f7
ms.contentlocale: it-it
ms.lasthandoff: 07/21/2017

---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Gestire il controllo degli accessi in base al ruolo con l'interfaccia della riga di comando di Azure
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Interfaccia della riga di comando di Azure](role-based-access-control-manage-access-azure-cli.md)
> * [API REST](role-based-access-control-manage-access-rest.md)


È possibile usare il controllo degli accessi in base al ruolo nel portale di Azure e nell'API di Azure Resource Manager per gestire l'accesso alla sottoscrizione e alle risorse a un livello estremamente specifico. Con questa funzionalità è possibile concedere l'accesso a utenti, gruppi o entità servizio di Active Directory assegnando loro dei ruoli in un determinato ambito.

Per usare l'interfaccia della riga di comando di Azure per gestire il controllo degli accessi in base al ruolo, è necessario disporre dei prerequisiti seguenti:

* Usare la versione 0.8.8 o successiva dell'interfaccia della riga di comando di Azure. Per installare la versione più recente e associarla alla sottoscrizione di Azure, vedere [Installare e configurare l'interfaccia della riga di comando di Azure](../cli-install-nodejs.md).
* Azure Resource Manager nell'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Uso dell'interfaccia della riga di comando di Azure con Resource Manager](../xplat-cli-azure-resource-manager.md)

## <a name="list-roles"></a>Elenco dei ruoli
### <a name="list-all-available-roles"></a>Elencare tutti i ruoli disponibili
Per elencare tutti i ruoli disponibili, usare:

        azure role list

L'esempio seguente mostra l'elenco di *tutti i ruoli disponibili*.

```
azure role list --json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
```

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Elenco di ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

### <a name="list-actions-of-a-role"></a>Elencare le azioni di un ruolo
Per elencare le azioni di un ruolo, usare:

    azure role show "<role name>"

L'esempio seguente mostra le azioni dei ruoli *Collaboratore* e *Collaboratore Macchina virtuale*.

```
azure role show "contributor" --json | jq '.[] | {"Actions":.properties.permissions[0].actions,"NotActions":properties.permissions[0].notActions}'

azure role show "virtual machine contributor" --json | jq '.[] | .properties.permissions[0].actions'
```

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Visualizzazione dei ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

## <a name="list-access"></a>Elencare l'accesso
### <a name="list-role-assignments-effective-on-a-resource-group"></a>Elencare le assegnazioni di ruoli valide per un gruppo di risorse
Per elencare le assegnazioni di ruolo in un gruppo di risorse, usare:

    azure role assignment list --resource-group <resource group name>

L'esempio seguente indica le assegnazioni di ruolo nel gruppo *pharma-sales-projecforcast* .

```
azure role assignment list --resource-group pharma-sales-projecforcast --json | jq '.[] | {"DisplayName":.properties.aADObject.displayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Elenco di assegnazione di ruoli di Azure per gruppo - Schermata](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

### <a name="list-role-assignments-for-a-user"></a>Elencare i ruoli assegnati a un utente
Per avere un elenco dei ruoli assegnati a un determinato utente e delle assegnazioni ai gruppi di utenti, utilizzare:

    azure role assignment list --signInName <user email>

È anche possibile visualizzare le assegnazioni di ruolo ereditate dai gruppi modificando il comando:

    azure role assignment list --expandPrincipalGroups --signInName <user email>

L'esempio seguente indica le assegnazioni di ruolo concesse all'utente *sameert@aaddemo.com* . Include i ruoli assegnati direttamente all'utente, oltre ai ruoli ereditati dai gruppi.

```
azure role assignment list --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'

azure role assignment list --expandPrincipalGroups --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Elenco di assegnazione di ruoli per utente - Schermata](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

## <a name="grant-access"></a>Concedere l'accesso
Per concedere l'accesso dopo aver individuato il ruolo da assegnare, usare:

    azure role assignment create

### <a name="assign-a-role-to-group-at-the-subscription-scope"></a>Assegnare un ruolo a un gruppo nell'ambito della sottoscrizione
Per assegnare un ruolo a un gruppo nell'ambito della sottoscrizione, usare:

    azure role assignment create --objectId  <group object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

L'esempio seguente assegna il ruolo *Reader* (Lettore) a *Christine Koch Team* nell'ambito *subscriptions*.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Assegnazione di ruoli di Azure creata per gruppo - Schermata](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Assegnare un ruolo a un'applicazione nell'ambito della sottoscrizione
Per assegnare un ruolo a un'applicazione nell'ambito della sottoscrizione, usare:

    azure role assignment create --objectId  <applications object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

L'esempio seguente assegna il ruolo *Contributor* (Collaboratore) a un'applicazione *Azure AD* nella sottoscrizione selezionata.

 ![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Assegnazione di ruoli creata per applicazione](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Assegnare un ruolo a un utente nell'ambito di un gruppo di risorse
Per assegnare un ruolo a un utente nell'ambito di un gruppo di risorse, usare:

    azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>

L'esempio seguente assegna il ruolo *Virtual Machine Contributor* (Collaboratore Macchina virtuale) all'utente *samert@aaddemo.com* nell'ambito del gruppo di risorse *Pharma-Sales-ProjectForcast*.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Assegnazione di ruoli di Azure creata per utente - Schermata](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Assegnare un ruolo a un gruppo nell'ambito delle risorse
Per assegnare un ruolo a un gruppo nell'ambito di un gruppo di risorse, usare:

    azure role assignment create --objectId <group id> --role "<name of role>" --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

L'esempio seguente assegna il ruolo *Virtual Machine Contributor* (Collaboratore Macchina virtuale) a un gruppo *Azure AD* in una *subnet*.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Assegnazione di ruoli di Azure creata per gruppo - Schermata](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

## <a name="remove-access"></a>Rimuovere un accesso
Per rimuovere un'assegnazione di ruolo, usare:

    azure role assignment delete --objectId <object id to from which to remove role> --roleName "<role name>"

L'esempio seguente rimuove l'assegnazione del ruolo *Virtual Machine Contributor* (Collaboratore Macchina virtuale) dall'utente *sammert@aaddemo.com* nel gruppo di risorse *Pharma-Sales-ProjectForcast*.
Nell'esempio viene quindi rimossa l'assegnazione del ruolo da un gruppo nella sottoscrizione.

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Eliminazione dell'assegnazione di ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato
Per creare un ruolo personalizzato, usare:

    azure role create --inputfile <file path>

Nell'esempio seguente viene creato il ruolo personalizzato denominato *Operatore macchina virtuale*. Questo ruolo personalizzato concede l'accesso a tutte le operazioni di lettura dei provider di risorse *Microsoft.Compute*, *Microsoft.Storage* e *Microsoft.Network* e concede l'accesso per avviare, riavviare e monitorare le macchine virtuali. Questo ruolo personalizzato può essere usato in due sottoscrizioni. In questo esempio viene usato un file JSON come input.

![JSON - Definizione di ruolo personalizzato - Schermata](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Creazione di ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## <a name="modify-a-custom-role"></a>Modificare un ruolo personalizzato
Per modificare un ruolo personalizzato, usare il comando `azure role show` per recuperare la definizione di ruolo. Successivamente, apportare le modifiche desiderate al file di definizione del ruolo. Usare infine `azure role set` per salvare la definizione del ruolo modificata.

    azure role set --inputfile <file path>

L'esempio seguente aggiunge l'operazione *Microsoft.Insights/diagnosticSettings/* alle **Azioni** e una sottoscrizione di Azure ad **AssignableScopes** del ruolo personalizzato Operatore macchina virtuale.

![JSON - Modifica della definizione di ruolo personalizzata - Schermata](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Impostazione dei ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## <a name="delete-a-custom-role"></a>Eliminare un ruolo personalizzato
Per eliminare un ruolo personalizzato, usare prima di tutto il comando `azure role show` per determinare l' **ID** del ruolo. Usare quindi il comando `azure role delete` per eliminare il ruolo specificando l' **ID**.

Nell'esempio seguente viene rimosso il ruolo personalizzato *Operatore macchina virtuale* .

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Eliminazione dei ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## <a name="list-custom-roles"></a>Elencare ruoli personalizzati
Per elencare i ruoli disponibili per l'assegnazione a un ambito, usare il comando `azure role list` .

Il comando seguente elenca tutti i ruoli disponibili per l'assegnazione nella sottoscrizione selezionata.

```
azure role list --json | jq '.[] | {"name":.properties.roleName, type:.properties.type}'
```

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Elenco di ruoli di Azure - Schermata](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

Nell'esempio seguente il ruolo personalizzato *Virtual Machine Operator* (Operatore macchina virtuale) non è disponibile nella sottoscrizione *Production4* perché la sottoscrizione non è inclusa in **AssignableScopes** per il ruolo.

```
azure role list --json | jq '.[] | if .properties.type == "CustomRole" then .properties.roleName else empty end'
```

![Riga di comando di Controllo degli accessi in base al ruolo di Azure - Elenco dei ruoli di Azure per i ruoli personalizzati - Schermata](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]


