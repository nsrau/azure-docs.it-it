---
title: Gestire il controllo degli accessi in base al ruolo con Azure PowerShell | Documentazione Microsoft
description: Come gestire il controllo degli accessi in base al ruolo con Azure PowerShell, come elencare ruoli, assegnare ruoli ed eliminare assegnazioni di ruoli.
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
editor: 
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/22/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 45f1716d7520981845fbfb96cfaf24cde9dd5c5d
ms.openlocfilehash: 8b906c402dde8d2bbaa2354a370a775058c146a7
ms.lasthandoff: 02/15/2017


---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>Gestire il controllo degli accessi in base al ruolo con Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [Interfaccia della riga di comando di Azure](role-based-access-control-manage-access-azure-cli.md)
> * [API REST](role-based-access-control-manage-access-rest.md)
> 
> 

È possibile usare il controllo degli accessi in base al ruolo (RBAC) nel portale di Azure e nell'API di Azure Resource Manager per gestire con estrema precisione l'accesso alla propria sottoscrizione. Con questa funzionalità è possibile concedere l'accesso a utenti, gruppi o entità servizio di Active Directory assegnando loro dei ruoli in un determinato ambito.

Per poter usare PowerShell per gestire il controllo degli accessi in base al ruolo, è necessario avere i programmi seguenti:

* Azure PowerShell 0.8.8 o versione successiva. Per installare la versione più recente e associarla alla sottoscrizione di Azure, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).
* Cmdlet di Azure Resource Manager Installare i [cmdlet di Azure Resource Manager](https://msdn.microsoft.com/library/mt125356.aspx) in PowerShell.

## <a name="list-roles"></a>Elenco dei ruoli
### <a name="list-all-available-roles"></a>Elencare tutti i ruoli disponibili
Per elencare i ruoli del controllo degli accessi in base al ruolo disponibili per l'assegnazione e controllare le operazioni a cui concedono l'accesso, usare `Get-AzureRmRoleDefinition`.

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![Controllo degli accessi in base al ruolo di PowerShell - Get-AzureRmRoleDefinition - Schermata](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### <a name="list-actions-of-a-role"></a>Elencare le azioni di un ruolo
Per elencare le azioni per un ruolo specifico, usare `Get-AzureRmRoleDefinition <role name>`.

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![Controllo degli accessi in base al ruolo di PowerShell - Get-AzureRmRoleDefinition per un ruolo specifico - Schermata](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## <a name="see-who-has-access"></a>Assegnazioni di accesso
Per elencare le assegnazioni di accesso al controllo degli accessi in base al ruolo, usare `Get-AzureRmRoleAssignment`.

### <a name="list-role-assignments-at-a-specific-scope"></a>Elencare le assegnazioni di ruolo in un ambito specifico
È possibile elencare le assegnazioni di accesso in base a sottoscrizione, gruppo di risorse o risorsa specificati. Ad esempio, per elencare tutte le assegnazioni attive per un gruppo di risorse, usare `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`.

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![Controllo degli accessi in base al ruolo di PowerShell - Get-AzureRmRoleDefinition per un gruppo di risorse - Schermata](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### <a name="list-roles-assigned-to-a-user"></a>Elencare i ruoli assegnati ad un utente
Per elencare tutti i ruoli assegnati a un utente specifico e i ruoli assegnati ai gruppi a cui appartiene l'utente, usare `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`.

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![Controllo degli accessi in base al ruolo di PowerShell - Get-AzureRmRoleDefinition per un utente - Schermata](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>Elencare le assegnazioni di ruoli per l'amministratore e i coamministratori del servizio classici
Per elencare le assegnazioni dell'accesso per l'amministratore e i coamministratori della sottoscrizione classici, usare:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## <a name="grant-access"></a>Concedere l'accesso
### <a name="search-for-object-ids"></a>Cercare gli ID oggetto
Per assegnare un ruolo, è necessario identificare l'oggetto (utente, gruppo o applicazione) e l'ambito.

Se non si conosce l'ID sottoscrizione, è possibile reperire tale informazione nel pannello **Sottoscrizioni** nel portale di Azure. Per informazioni su come eseguire una query per l'ID della sottoscrizione,vedere [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) (Get-AzureSubscription) in MSDN.

Per ottenere l'ID oggetto per un gruppo di Azure AD, usare:

    Get-AzureRmADGroup -SearchString <group name in quotes>

Per ottenere l'ID oggetto per un'entità servizio di Azure AD o applicazione, usare:

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Assegnare un ruolo a un'applicazione nell'ambito della sottoscrizione
Per concedere l'accesso a un'applicazione nell'ambito della sottoscrizione, usare:

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![Controllo degli accessi in base al ruolo di PowerShell - New-AzureRmRoleAssignment - Schermata](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Assegnare un ruolo a un utente nell'ambito di un gruppo di risorse
Per concedere l'accesso a un utente nell'ambito di un gruppo di risorse, usare:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![Controllo degli accessi in base al ruolo di PowerShell - New-AzureRmRoleAssignment - Schermata](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Assegnare un ruolo a un gruppo nell'ambito delle risorse
Per concedere l'accesso a un gruppo nell'ambito delle risorse, usare:

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![Controllo degli accessi in base al ruolo di PowerShell - New-AzureRmRoleAssignment - Schermata](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## <a name="remove-access"></a>Rimuovere un accesso
Per rimuovere l'accesso per utenti, gruppi e applicazioni, usare:

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![Controllo degli accessi in base al ruolo di PowerShell - Remove-AzureRmRoleAssignment - Schermata](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato
Per creare un ruolo personalizzato, usare il comando ```New-AzureRmRoleDefinition``` . Esistono due metodi per strutturare il ruolo, usare PSRoleDefinitionObject o un modello JSON. 

## <a name="get-actions-from-particular-resource-provider"></a>Ottenere azioni da un determinato provider di risorse
Quando si creano ruoli personalizzati da zero, è importante conoscere tutte le operazioni possibili dei provider di risorse.
È possibile farlo mediante il comando ```Get-AzureRMProviderOperation```. Ad esempio, se si desidera controllare tutte le operazioni disponibili per la macchina virtuale, il comando sarà il seguente:

```Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation , Description -AutoSize```


### <a name="create-role-with-psroledefinitionobject"></a>Creare un ruolo con PSRoleDefinitionObject
Quando si crea un ruolo personalizzato con PowerShell, è possibile iniziare da zero o utilizzare uno dei [ruoli predefiniti](role-based-access-built-in-roles.md) come punto di partenza, quest'ultimo viene usato in questo esempio. Modificare gli attributi e aggiungere gli attributi *Actions*, *notActions* o *scopes* desiderati e quindi salvare le modifiche come nuovo ruolo.

L'esempio seguente inizia con il ruolo *Virtual Machine Contributor* e lo usa per creare un ruolo personalizzato denominato *Virtual Machine Operator*. Il nuovo ruolo concede l'accesso a tutte le operazioni di lettura dei provider di risorse *Microsoft.Compute*, *Microsoft.Storage* e *Microsoft.Network* e concede l'accesso per avviare, riavviare e monitorare le macchine virtuali. Il ruolo personalizzato può essere usato in due sottoscrizioni.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e")
$role.AssignableScopes.Add("/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624")
New-AzureRmRoleDefinition -Role $role
```

![Controllo degli accessi in base al ruolo di PowerShell - Get-AzureRmRoleDefinition - Schermata](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

### <a name="create-role-with-json-template"></a>Creare un ruolo con il modello JSON
È possibile usare un modello JSON come definizione di origine per il ruolo personalizzato. Nell'esempio seguente viene creato un ruolo personalizzato che consente di accedere in lettura all'archiviazione e alle risorse di calcolo, di accedere come supporto e di aggiungere tale ruolo a due sottoscrizioni. Creare un nuovo file `C:\CustomRoles\customrole1.json` con il contenuto seguente. Si noti che l'ID deve essere impostato su `null` all'inizio della creazione del ruolo, quando viene generato un nuovo ID. 

```
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```
Per aggiungere il ruolo alle sottoscrizioni, eseguire il comando PowerShell seguente:
```
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="modify-a-custom-role"></a>Modificare un ruolo personalizzato
Così come per la creazione di un ruolo personalizzato, è possibile modificare un ruolo personalizzato esistente usando PSRoleDefinitionObject o un modello JSON.

### <a name="modify-role-with-psroledefinitionobject"></a>Modificare un ruolo con PSRoleDefinitionObject
Per modificare un ruolo personalizzato, usare innanzitutto il comando `Get-AzureRmRoleDefinition` per recuperare la definizione di ruolo. Successivamente, apportare le modifiche desiderate alla definizione del ruolo. Infine, usare il comando `Set-AzureRmRoleDefinition` per salvare la definizione del ruolo modificata.

Nell'esempio seguente viene aggiunta l'operazione `Microsoft.Insights/diagnosticSettings/*` al ruolo personalizzato *Operatore macchina virtuale* .

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

![Controllo degli accessi in base al ruolo di PowerShell - Set-AzureRmRoleDefinition - Schermata](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

Nell'esempio seguente viene aggiunta una sottoscrizione di Azure agli ambiti assegnabili del ruolo personalizzato *Virtual Machine Operator* .

```
Get-AzureRmSubscription - SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2")
Set-AzureRmRoleDefinition -Role $role
```

![Controllo degli accessi in base al ruolo di PowerShell - Set-AzureRmRoleDefinition - Schermata](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

### <a name="modify-role-with-json-template"></a>Modificare un ruolo con il modello JSON
Usando il modello JSON precedente, è possibile modificare un ruolo personalizzato esistente per aggiungere o rimuovere le azioni. Aggiornare il modello JSON e aggiungere l'azione read per la rete, come illustrato di seguito. Si noti che le definizioni riportate nel modello non vengono applicate in modo cumulativo a una definizione esistente, vale a dire che il ruolo verrà visualizzato esattamente come specificato nel modello. Si noti inoltre che è anche necessario aggiornare l'ID con l'ID del ruolo. Se non si è certi di quale sia questo valore, è possibile utilizzare il cmdlet `Get-AzureRmRoleDefinition` per ottenere queste informazioni.

```
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

Per aggiornare il ruolo esistente, eseguire il comando PowerShell seguente:
```
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Eliminare un ruolo personalizzato
Per eliminare un ruolo personalizzato, usare il comando `Remove-AzureRmRoleDefinition` .

Nell'esempio seguente viene rimosso il ruolo personalizzato *Operatore macchina virtuale* .

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![Controllo degli accessi in base al ruolo di PowerShell - Remove-AzureRmRoleDefinition - Schermata](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## <a name="list-custom-roles"></a>Elencare ruoli personalizzati
Per elencare i ruoli disponibili per l'assegnazione a un ambito, usare il comando `Get-AzureRmRoleDefinition` .

L'esempio seguente elenca tutti i ruoli disponibili per l'assegnazione nella sottoscrizione selezionata.

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![Controllo degli accessi in base al ruolo di PowerShell - Get-AzureRmRoleDefinition - Schermata](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

Nell'esempio seguente il ruolo personalizzato *Virtual Machine Operator* non è disponibile nella sottoscrizione *Production4* perché la sottoscrizione non è inclusa in **AssignableScopes** per il ruolo.

![Controllo degli accessi in base al ruolo di PowerShell - Get-AzureRmRoleDefinition - Schermata](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## <a name="see-also"></a>Vedere anche
* [Using Azure PowerShell with Azure Resource Manager](../powershell-azure-resource-manager.md)
  [!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)] (Uso di Azure PowerShell con Azure Resource Manager)


