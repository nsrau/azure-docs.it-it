---
title: Usare un'identità gestita assegnata dall'utente per una macchina virtuale Windows per accedere ad Azure Resource Manager
description: Esercitazione che illustra come usare un'identità gestita assegnata dall'utente in una macchina virtuale Windows per accedere ad Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6fc7e13cf5ea3991f81d53edcabe0980d799cb0
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/12/2019
ms.locfileid: "59520865"
---
# <a name="tutorial-use-a-user-assigned-managed-identity-on-a-windows-vm-to-access-azure-resource-manager"></a>Esercitazione: Usare un'identità gestita assegnata dall'utente in una macchina virtuale Windows per accedere ad Azure Resource Manager

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Questa esercitazione illustra come creare un'identità assegnata dall'utente, assegnarla a una macchina virtuale Windows e quindi usare tale identità per accedere all'API di Azure Resource Manager. Le identità del servizio gestito vengono gestite automaticamente da Azure. Tali identità consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di incorporare le credenziali nel codice. 

Si apprenderà come:

> [!div class="checklist"]
> * Creare un'identità gestita assegnata dall'utente
> * Assegnare l'identità assegnata dall'utente alla macchina virtuale Windows
> * Concedere l'accesso a un gruppo di risorse di Azure Resource Manager all'identità assegnata dall'utente 
> * Ottenere un token di accesso tramite l'identità assegnata dall'utente e usarlo per chiamare Azure Resource Manager 
> * Leggere le proprietà di un gruppo di risorse

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

- [Accedere al portale di Azure](https://portal.azure.com)

- [Creare una macchina virtuale Windows](/azure/virtual-machines/windows/quick-create-portal)

- Per eseguire la procedura delle attività richieste di creazione delle risorse e gestione dei ruoli in questa esercitazione, l'account deve disporre delle autorizzazioni "Proprietario" nell'ambito appropriato (sottoscrizione o gruppo di risorse). Se è necessaria assistenza con l'assegnazione, vedere [Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](/azure/role-based-access-control/role-assignments-portal).
- [Installare la versione più recente del modulo di Azure PowerShell](/powershell/azure/install-az-ps). 
- Eseguire `Connect-AzAccount` per creare una connessione con Azure.
- Installare la [versione più recente di PowerShellGet](/powershell/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).
- Eseguire `Install-Module -Name PowerShellGet -AllowPrerelease` per ottenere la versione non definitiva del modulo `PowerShellGet` (potrebbe essere necessario `Exit` uscire dalla sessione corrente di PowerShell dopo aver eseguito questo comando per installare il modulo `Az.ManagedServiceIdentity`).
- Eseguire `Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease` per installare la versione non definitiva del modulo `Az.ManagedServiceIdentity` per eseguire le operazioni di identità assegnate dall'utente in questo articolo.

## <a name="create-a-user-assigned-identity"></a>Creare un'identità assegnata dall'utente

Un'identità assegnata dall'utente viene creata come risorsa di Azure autonoma. Usando [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/get-azuserassignedidentity), Azure crea un'identità nel tenant di Azure AD che può essere assegnata a una o più istanze del servizio di Azure.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

La risposta contiene i dettagli relativi all'identità assegnata dall'utente creata ed è simile all'esempio seguente. Prendere nota dei valori `Id` e `ClientId` per l'identità assegnata dall'utente perché verranno usati nei passaggi successivi:

```azurepowershell
{
Id: /subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1
ResourceGroupName : myResourceGroupVM
Name: ID1
Location: westus
TenantId: 733a8f0e-ec41-4e69-8ad8-971fc4b533f8
PrincipalId: e591178e-b785-43c8-95d2-1397559b2fb9
ClientId: af825a31-b0e0-471f-baea-96de555632f9
ClientSecretUrl: https://control-westus.identity.azure.net/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1/credentials?tid=733a8f0e-ec41-4e69-8ad8-971fc4b533f8&oid=e591178e-b785-43c8-95d2-1397559b2fb9&aid=af825a31-b0e0-471f-baea-96de555632f9
Type: Microsoft.ManagedIdentity/userAssignedIdentities
}
```

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>Assegnare l'identità assegnata dall'utente a una macchina virtuale Windows

Un'identità assegnata dall'utente può essere usata dai client in più risorse di Azure. Usare i comandi seguenti per assegnare l'identità assegnata dall'utente a una singola macchina virtuale. Usare la proprietà `Id` restituita nel passaggio precedente per il parametro `-IdentityID`.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-identity-access-to-a-resource-group-in-azure-resource-manager"></a>Concedere l'accesso a un gruppo di risorse di Azure Resource Manager all'identità assegnata dall'utente 

Le identità gestite per le risorse di Azure forniscono le identità che il codice può usare per richiedere i token di accesso per l'autenticazione alle API delle risorse che supportano l'autenticazione di Azure AD. In questa esercitazione il codice accede all'API di Azure Resource Manager. 

Prima che il codice possa accedere all'API, è necessario concedere all'identità l'accesso a una risorsa in Azure Resource Manager. In questo caso si tratta del gruppo di risorse in cui è contenuta la macchina virtuale. Aggiornare il valore per `<SUBSCRIPTION ID>` in base all'ambiente in uso.

```azurepowershell-interactive
$spID = (Get-AzUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
```

La risposta contiene i dettagli per l'assegnazione di ruolo creata, in modo analogo all'esempio seguente:

```azurepowershell
RoleAssignmentId: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM/providers/Microsoft.Authorization/roleAssignments/f9cc753d-265e-4434-ae19-0c3e2ead62ac
Scope: /subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/myResourceGroupVM
DisplayName: ID1
SignInName:
RoleDefinitionName: Reader
RoleDefinitionId: acdd72a7-3385-48ef-bd42-f606fba81ae7
ObjectId: e591178e-b785-43c8-95d2-1397559b2fb9
ObjectType: ServicePrincipal
CanDelegate: False
```

## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-call-resource-manager"></a>Ottenere un token di accesso usando l'identità della macchina virtuale e usarlo per chiamare Azure Resource Manager 

Il resto dell'esercitazione prevede che le operazioni vengano svolte dalla macchina virtuale creata in precedenza.

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com)

2. Nel portale passare a **Macchine virtuali**, selezionare la macchina virtuale Windows e in **Panoramica** fare clic su **Connetti**.

3. Immettere il **Nome utente** e la **Password** usati al momento della creazione della macchina virtuale Windows.

4. Ora che si è creata una **connessione Desktop remoto** con la macchina virtuale, aprire **PowerShell** nella sessione remota.

5. Usando `Invoke-WebRequest` di PowerShell, eseguire una richiesta all'endpoint locale delle identità gestite per le risorse di Azure per ottenere un token di accesso per Azure Resource Manager.  `client_id` è il valore restituito durante la [creazione dell'identità gestita assegnata dall'utente](#create-a-user-assigned-identity).

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>Leggere le proprietà di un gruppo di risorse

Usare il token di accesso recuperato nel passaggio precedente per accedere ad Azure Resource Manager e leggere le proprietà del gruppo di risorse a cui si è concesso l'accesso per l'identità assegnata dall'utente. Sostituire `<SUBSCRIPTION ID>` con l'ID sottoscrizione del proprio ambiente.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
La risposta contiene le informazioni sul gruppo di risorse specifico, in modo analogo all'esempio seguente:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/myResourceGroupVM","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come creare un'identità assegnata dall'utente e associarla a una macchina virtuale di Azure per accedere all'API di Azure Resource Manager.  Per altre informazioni su Azure Resource Manager, vedere:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)
