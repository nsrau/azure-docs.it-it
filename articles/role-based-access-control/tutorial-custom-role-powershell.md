---
title: 'Esercitazione: creare un ruolo personalizzato con Azure PowerShell | Microsoft Docs'
description: Introduzione alla creazione di un ruolo personalizzato con Azure PowerShell.
services: active-directory
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/12/2018
ms.author: rolyon
ms.openlocfilehash: 1e3043a6053625f8363e9035e59b53275714a827
ms.sourcegitcommit: 1fb353cfca800e741678b200f23af6f31bd03e87
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2018
ms.locfileid: "43300610"
---
# <a name="tutorial-create-a-custom-role-using-azure-powershell"></a>Esercitazione: creare un ruolo personalizzato con Azure PowerShell

Se i [ruoli predefiniti](built-in-roles.md) non soddisfano le esigenze specifiche dell'organizzazione, è possibile creare ruoli personalizzati. Per questa esercitazione viene creato un ruolo personalizzato denominato Reader Support Tickets con Azure PowerShell. Il ruolo personalizzato consente all'utente di visualizzare tutti gli elementi nella sottoscrizione e anche di aprire i ticket di supporto.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un ruolo personalizzato
> * Elencare ruoli personalizzati
> * Aggiornare un ruolo personalizzato
> * Eliminare un ruolo personalizzato

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione, è necessario:

- Avere le autorizzazioni per creare ruoli personalizzati, ad esempio [Proprietario](built-in-roles.md#owner) o [Amministratore Accesso utenti](built-in-roles.md#user-access-administrator)
- Avere [Azure PowerShell](/powershell/azure/install-azurerm-ps) installato in locale

## <a name="sign-in-to-azure-powershell"></a>Accedere ad Azure PowerShell

Accedere ad [Azure PowerShell](/powershell/azure/authenticate-azureps).

## <a name="create-a-custom-role"></a>Creare un ruolo personalizzato

Il modo più semplice per creare un ruolo personalizzato è quello di iniziare con un ruolo predefinito, modificarlo e quindi creare un nuovo ruolo.

1. In PowerShell usare il comando [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) per ottenere l'elenco delle operazioni del provider di risorse Microsoft.Support. È utile conoscere le operazioni disponibili per creare le autorizzazioni. È anche possibile visualizzare un elenco di tutte le operazioni in [Operazioni dei provider di risorse di Azure Resource Manager](resource-provider-operations.md#microsoftsupport).

    ```azurepowershell
    Get-AzureRMProviderOperation "Microsoft.Support/*" | FT Operation, Description -AutoSize
    ```
    
    ```Output
    Operation                              Description
    ---------                              -----------
    Microsoft.Support/register/action      Registers to Support Resource Provider
    Microsoft.Support/supportTickets/read  Gets Support Ticket details (including status, severity, contact ...
    Microsoft.Support/supportTickets/write Creates or Updates a Support Ticket. You can create a Support Tic...
    ```

1. Usare il comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) per ottenere il ruolo [Lettore](built-in-roles.md#reader) in formato JSON.

    ```azurepowershell
    Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole.json
    ```

1. Aprire il file **ReaderSupportRole.json** in un editor.

    Di seguito è riportato l'output JSON. Per informazioni sulle diverse proprietà, vedere [Ruoli personalizzati](custom-roles.md).

    ```json
    {
        "Name":  "Reader",
        "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
        "IsCustom":  false,
        "Description":  "Lets you view everything, but not make any changes.",
        "Actions":  [
                        "*/read"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/"
                             ]
    }
    ```
    
1. Modificare il file JSON per aggiungere l'operazione `"Microsoft.Support/*"` alla proprietà `Actions`. Assicurarsi di includere una virgola dopo l'operazione di lettura. Questa azione consente all'utente di creare ticket di supporto.

1. Ottenere l'ID della sottoscrizione usando il comando [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription).

    ```azurepowershell
    Get-AzureRmSubscription
    ```

1. In `AssignableScopes` aggiungere l'ID sottoscrizione con il formato seguente: `"/subscriptions/00000000-0000-0000-0000-000000000000"`

    È necessario aggiungere ID sottoscrizione espliciti. In caso contrario non sarà possibile importare il ruolo nella sottoscrizione.

1. Eliminare la riga della proprietà `Id` e modificare la proprietà `IsCustom` con `true`.

1. Modificare le proprietà `Name` e `Description` con "Reader Support Tickets" e "View everything in the subscription and also open support tickets".

    Il file JSON deve apparire come segue:

    ```json
    {
        "Name":  "Reader Support Tickets",
        "IsCustom":  true,
        "Description":  "View everything in the subscription and also open support tickets.",
        "Actions":  [
                        "*/read",
                        "Microsoft.Support/*"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/subscriptions/00000000-0000-0000-0000-000000000000"
                             ]
    }
    ```
    
1. Per creare il nuovo ruolo personalizzato, usare il comando [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) e specificare il file di definizione del ruolo JSON.

    ```azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
        
    Il nuovo ruolo personalizzato è ora disponibile nel portale di Azure e può essere assegnato a utenti, gruppi o entità servizio come i ruoli predefiniti.

## <a name="list-custom-roles"></a>Elencare ruoli personalizzati

- Per elencare tutti i ruoli personalizzati, usare il comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition).

    ```azurepowershell
    Get-AzureRmRoleDefinition | ? {$_.IsCustom -eq $true} | FT Name, IsCustom
    ```

    ```Output
    Name                   IsCustom
    ----                   --------
    Reader Support Tickets     True
    ```
    
    È anche possibile visualizzare il ruolo personalizzato nel portale di Azure.

    ![Screenshot del ruolo personalizzato importato nel portale di Azure](./media/tutorial-custom-role-powershell/custom-role-reader-support-tickets.png)

## <a name="update-a-custom-role"></a>Aggiornare un ruolo personalizzato

Per aggiornare il ruolo personalizzato, è possibile aggiornare il file JSON o usare l'oggetto `PSRoleDefinition`.

1. Per aggiornare il file JSON, usare il comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) per ottenere il ruolo personalizzato in formato JSON.

    ```azurepowershell
    Get-AzureRmRoleDefinition -Name "Reader Support Tickets" | ConvertTo-Json | Out-File C:\CustomRoles\ReaderSupportRole2.json
    ```

1. Aprire il file in un editor.

1. In `Actions` aggiungere l'operazione per creare e gestire le distribuzioni di gruppi di risorse `"Microsoft.Resources/deployments/*"`.

    Il file JSON aggiornato deve apparire come segue:

    ```json
    {
        "Name":  "Reader Support Tickets",
        "Id":  "22222222-2222-2222-2222-222222222222",
        "IsCustom":  true,
        "Description":  "View everything in the subscription and also open support tickets.",
        "Actions":  [
                        "*/read",
                        "Microsoft.Support/*",
                        "Microsoft.Resources/deployments/*"
                    ],
        "NotActions":  [
    
                       ],
        "DataActions":  [
    
                        ],
        "NotDataActions":  [
    
                           ],
        "AssignableScopes":  [
                                 "/subscriptions/00000000-0000-0000-0000-000000000000"
                             ]
    }
    ```
        
1. Per aggiornare il ruolo personalizzato, usare il comando [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) e specificare il file JSON aggiornato.

    ```azurepowershell
    Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\ReaderSupportRole2.json"
    ```

    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```

1. Per aggiornare il ruolo personalizzato con l'oggetto `PSRoleDefintion`, usare innanzitutto il comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) per ottenere il ruolo.

    ```azurepowershell
    $role = Get-AzureRmRoleDefinition "Reader Support Tickets"
    ```
    
1. Chiamare il metodo `Add` per aggiungere l'operazione alle impostazioni di diagnostica di lettura.

    ```azurepowershell
    $role.Actions.Add("Microsoft.Insights/diagnosticSettings/*/read")
    ```

1. Usare il comando [Set-AzureRmRoleDefinition](/powershell/module/azurerm.resources/set-azurermroledefinition) per aggiornare il ruolo.

    ```azurepowershell
    Set-AzureRmRoleDefinition -Role $role
    ```
    
    ```Output
    Name             : Reader Support Tickets
    Id               : 22222222-2222-2222-2222-222222222222
    IsCustom         : True
    Description      : View everything in the subscription and also open support tickets.
    Actions          : {*/read, Microsoft.Support/*, Microsoft.Resources/deployments/*,
                       Microsoft.Insights/diagnosticSettings/*/read}
    NotActions       : {}
    DataActions      : {}
    NotDataActions   : {}
    AssignableScopes : {/subscriptions/00000000-0000-0000-0000-000000000000}
    ```
    
## <a name="delete-a-custom-role"></a>Eliminare un ruolo personalizzato

1. Usare il comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) per ottenere l'ID del ruolo personalizzato.

    ```azurepowershell
    Get-AzureRmRoleDefinition "Reader Support Tickets"
    ```

1. Usare il comando [Remove-AzureRmRoleDefinition](/powershell/module/azurerm.resources/remove-azurermroledefinition) e specificare l'ID ruolo per eliminare il ruolo personalizzato.

    ```azurepowershell
    Remove-AzureRmRoleDefinition -Id "22222222-2222-2222-2222-222222222222"
    ```

    ```Output
    Confirm
    Are you sure you want to remove role definition with id '22222222-2222-2222-2222-222222222222'.
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
    ```

1. Quando viene chiesto di confermare, digitare **Y**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare ruoli personalizzati con PowerShell](custom-roles-powershell.md)
