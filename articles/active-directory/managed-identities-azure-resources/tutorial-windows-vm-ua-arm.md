---
title: Esercitazione`:` Usare un'identità gestita per accedere ad Azure Resource Manager - Windows - Azure AD
description: Esercitazione che illustra come usare un'identità gestita assegnata dall'utente in una macchina virtuale Windows per accedere ad Azure Resource Manager.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: d26c7f544c9754f455b67aadf9e923344cda3fdf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "90968692"
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

- [Creare una macchina virtuale Windows](../../virtual-machines/windows/quick-create-portal.md)

- Per eseguire la procedura delle attività richieste di creazione delle risorse e gestione dei ruoli in questa esercitazione, l'account deve disporre delle autorizzazioni "Proprietario" nell'ambito appropriato (sottoscrizione o gruppo di risorse). Se è necessaria assistenza con l'assegnazione, vedere [Usare il controllo degli accessi in base al ruolo per gestire l'accesso alle risorse della sottoscrizione di Azure](../../role-based-access-control/role-assignments-portal.md).

- Per eseguire gli script di esempio, sono disponibili due opzioni:
    - Usare [Azure Cloud Shell](../../cloud-shell/overview.md), che è possibile aprire usando il pulsante **Prova** nell'angolo in alto a destra dei blocchi di codice.
    - Eseguire gli script in locale con Azure PowerShell, come descritto nella sezione successiva.

### <a name="configure-azure-powershell-locally"></a>Configurare Azure PowerShell in locale

Per usare Azure PowerShell in locale per questo articolo (invece di usare Cloud Shell), completare i passaggi seguenti:

1. Installare [la versione più recente di Azure PowerShell](/powershell/azure/install-az-ps), se non è già installata.

1. Accedere ad Azure:

    ```azurepowershell
    Connect-AzAccount
    ```

1. Installare la [versione più recente di PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    Potrebbe essere necessario uscire (`Exit`) dalla sessione corrente di PowerShell dopo aver eseguito questo comando per il passaggio successivo.

1. Installare la versione non definitiva del modulo `Az.ManagedServiceIdentity` per eseguire le operazioni di identità gestite assegnate dall'utente in questo articolo:

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="enable"></a>Abilita

Per uno scenario basato su un'identità assegnata dall'utente, è necessario eseguire i passaggi seguenti:

- Creare un'identità
- Assegnare l'identità appena creata

### <a name="create-identity"></a>Creare l'identità

Questa sezione illustra come creare un'identità assegnata dall'utente. Un'identità assegnata dall'utente viene creata come risorsa di Azure autonoma. Usando [New-AzUserAssignedIdentity](/powershell/module/az.managedserviceidentity/get-azuserassignedidentity), Azure crea un'identità nel tenant di Azure AD che può essere assegnata a una o più istanze del servizio di Azure.

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

### <a name="assign-identity"></a>Assegnare l'identità

Questa sezione illustra come assegnare l'identità assegnata dall'utente a una macchina virtuale Windows. Un'identità assegnata dall'utente può essere usata dai client in più risorse di Azure. Usare i comandi seguenti per assegnare l'identità assegnata dall'utente a una singola macchina virtuale. Usare la proprietà `Id` restituita nel passaggio precedente per il parametro `-IdentityID`.

```azurepowershell-interactive
$vm = Get-AzVM -ResourceGroupName myResourceGroup -Name myVM
Update-AzVM -ResourceGroupName TestRG -VM $vm -IdentityType "UserAssigned" -IdentityID "/subscriptions/<SUBSCRIPTIONID>/resourcegroups/myResourceGroupVM/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
```

## <a name="grant-access"></a>Concedere l'accesso 

Questa sezione illustra come concedere all'identità assegnata dall'utente l'accesso a un gruppo di risorse di Azure Resource Manager. Le identità gestite per le risorse di Azure forniscono le identità che il codice può usare per richiedere i token di accesso per l'autenticazione alle API delle risorse che supportano l'autenticazione di Azure AD. In questa esercitazione il codice accede all'API di Azure Resource Manager. 

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

## <a name="access-data"></a>Accedere ai dati

### <a name="get-an-access-token"></a>Ottenere un token di accesso 

Il resto dell'esercitazione prevede che le operazioni vengano svolte dalla macchina virtuale creata in precedenza.

1. Accedere al portale di Azure all'indirizzo [https://portal.azure.com](https://portal.azure.com)

2. Nel portale passare a **Macchine virtuali**, selezionare la macchina virtuale Windows e in **Panoramica** fare clic su **Connetti**.

3. Immettere il **Nome utente** e la **Password** usati al momento della creazione della macchina virtuale Windows.

4. Ora che si è creata una **connessione Desktop remoto** con la macchina virtuale, aprire **PowerShell** nella sessione remota.

5. Usando `Invoke-WebRequest` di PowerShell, eseguire una richiesta all'endpoint locale delle identità gestite per le risorse di Azure per ottenere un token di accesso per Azure Resource Manager.  `client_id` è il valore restituito durante la creazione dell'identità gestita assegnata dall'utente.

    ```azurepowershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=af825a31-b0e0-471f-baea-96de555632f9&resource=https://management.azure.com/' -Method GET -Headers @{Metadata="true"}
    $content = $response.Content | ConvertFrom-Json
    $ArmToken = $content.access_token
    ```

### <a name="read-properties"></a>Leggere le proprietà

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
>[Azure Resource Manager](../../azure-resource-manager/management/overview.md)
