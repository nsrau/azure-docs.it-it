---
title: Usare un'identità del servizio gestito assegnata dall'utente di macchina virtuale di Windows per accedere ad Azure Resource Manager
description: Questa esercitazione illustra come usare un'identità del servizio gestito assegnata dall'utente su una macchina virtuale Windows per accedere ad Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: arluca
ms.openlocfilehash: 57455c5abf8c566f3935ece73d0b7470863936f8
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2018
ms.locfileid: "34699150"
---
# <a name="tutorial-use-a-user-assigned-managed-service-identity-msi-on-a-windows-vm-to-access-azure-resource-manager"></a>Esercitazione: Usare un'identità del servizio gestita assegnata dall'utente su una macchina virtuale Windows per accedere ad Azure Resource Manager

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Questa esercitazione illustra come creare un'identità, assegnarla a una macchina virtuale Windows e quindi usare tale identità per accedere all'API Azure Resource Manager. Le identità del servizio gestito vengono gestite automaticamente da Azure. Tali identità consentono di eseguire l'autenticazione ai servizi che supportano l'autenticazione di Azure AD senza la necessità di incorporare le credenziali nel codice. 

Si apprenderà come:

> [!div class="checklist"]
> * Creare un'app Windows 
> * Creare un'identità assegnata dall'utente
> * Assegnare alla macchina virtuale Windows l'identità assegnata dall'utente
> * Concedere all'identità assegnata dall'utente l'accesso a un gruppo di risorse in Azure Resource Manager 
> * Ottenere un token di accesso tramite l'identità dall'utente e usarlo per chiamare Azure Resource Manager 
> * Leggere le proprietà di un gruppo di risorse

## <a name="prerequisites"></a>prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere le [differenze tra identità assegnata dal sistema e identità assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire la proceduta delle attività richieste di creazione delle risorse e gestione dei ruoli in questa esercitazione, l'account deve disporre delle autorizzazioni "Proprietario" nell'ambito appropriato (sottoscrizione o gruppo di risorse). Se è necessaria assistenza con l'assegnazione, vedere [Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](/azure/role-based-access-control/role-assignments-portal).

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell 5.7 o versione successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.

## <a name="create-resource-group"></a>Creare un gruppo di risorse

Nell'esempio seguente viene creato un gruppo di risorse denominato *myResourceGroupVM* nell'area *EastUS*.

```azurepowershell-interactive
New-AzureRmResourceGroup -ResourceGroupName "myResourceGroupVM" -Location "EastUS"
```

## <a name="create-virtual-machine"></a>Crea macchina virtuale

Dopo aver creato il gruppo di risorse, creare una macchina virtuale Windows.

Impostare il nome utente e la password necessari per l'account amministratore della macchina virtuale con [Get-Credential](https://msdn.microsoft.com/powershell/reference/5.1/microsoft.powershell.security/Get-Credential):

```azurepowershell-interactive
$cred = Get-Credential
```
Creare la macchina virtuale con [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm).

```azurepowershell-interactive
New-AzureRmVm `
    -ResourceGroupName "myResourceGroupVM" `
    -Name "myVM" `
    -Location "East US" `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -Credential $cred
```

## <a name="create-a-user-assigned-identity"></a>Creare un'identità assegnata dall'utente

Un'identità assegnata dall'utente viene creata come risorsa di Azure autonoma. Usando [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity), Azure crea un'identità nel tenant di Azure AD che può essere assegnata a una o più istanze del servizio di Azure.

[!INCLUDE[ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1
```

La risposta contiene i dettagli relativi all'identità assegnata dall'utente creata ed è simile all'esempio seguente. Prendere nota del valore `Id` dell'identità assegnata dall'utente perché verrà usato nel passaggio successivo:

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

## <a name="assign-the-user-assigned-identity-to-a-windows-vm"></a>Assegnare a una macchina virtuale Windows l'identità assegnata dall'utente

Un'identità assegnata dall'utente può essere usata dai client in più risorse di Azure. Usare i comandi seguenti per assegnare a una singola macchina virtuale l'identità assegnata dall'utente. Usare la proprietà `Id` restituita nel passaggio precedente per il parametro `-IdentityID`.

```azurepowershell-interactive
$vm = Get-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzureRmVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-your-user-assigned-msi-access-to-a-resource-group-in-azure-resource-manager"></a>Concedere all'identità del servizio gestito a un gruppo di risorse in Azure Resource Manager 

L'Identità del servizio gestita (MSI) fornisce le identità che il codice può usare per richiedere i token di accesso per l'autenticazione alle API di risorsa che supporta l'autenticazione di Azure Active Directory. In questa esercitazione il codice accede all'API di Azure Resource Manager. 

Prima che il codice possa accedere all'API, è necessario concedere all'identità l'accesso a una risorsa in Azure Resource Manager. In questo caso si tratta del gruppo di risorse in cui è contenuta la macchina virtuale. Aggiornare il valore per `<SUBSCRIPTION ID>` in base all'ambiente in uso.

```azurepowershell-interactive
$spID = (Get-AzureRmUserAssignedIdentity -ResourceGroupName myResourceGroupVM -Name ID1).principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/"
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

3. Inserire il **Nome utente** e la **Password** usati al momento della creazione della macchina virtuale Windows.

4. Ora che si è creata una **connessione Desktop remoto** con la macchina virtuale, aprire **PowerShell** nella sessione remota.

5. Con `Invoke-WebRequest` di PowerShell, creare una richiesta all'endpoint locale di identità del servizio gestito per ottenere un token di accesso per Azure Resource Manager.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=73444643-8088-4d70-9532-c3a0fdc190fz&resource=https://management.azure.com' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

## <a name="read-the-properties-of-a-resource-group"></a>Leggere le proprietà di un gruppo di risorse

Usare il token di accesso recuperato nel passaggio precedente per accedere ad Azure Resource Manager e leggere le proprietà del gruppo di risorse a cui è stato concesso l'accesso per l'identità assegnata dall'utente. Sostituire <SUBSCRIPTION ID> con l'ID sottoscrizione del proprio ambiente.

```azurepowershell
(Invoke-WebRequest -Uri https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/myResourceGroupVM?api-version=2016-06-01 -Method GET -ContentType "application/json" -Headers @{Authorization ="Bearer $ArmToken"}).content
```
La risposta contiene le informazioni sul gruppo di risorse specifico, in modo analogo all'esempio seguente:

```json
{"id":"/subscriptions/<SUBSCRIPTIONID>/resourceGroups/TestRG","name":"myResourceGroupVM","location":"eastus","properties":{"provisioningState":"Succeeded"}}
```

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come creare un'identità e come assegnarla a una macchina virtuale Azure per accedere all'API Azure Resource Manager.  Per altre informazioni su Azure Resource Manager, vedere:

> [!div class="nextstepaction"]
>[Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview)