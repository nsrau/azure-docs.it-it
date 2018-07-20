---
title: Come configurare identità assegnate dal sistema e dall'utente in una macchina virtuale di Azure mediante REST
description: Istruzioni dettagliate per configurare identità assegnate dal sistema e dall'utente in una macchina virtuale di Azure usando CURL per effettuare chiamate API REST.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/25/2018
ms.author: daveba
ms.openlocfilehash: 9cc645d91bebf07ed7e657ed39c2454254958959
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902888"
---
# <a name="configure-managed-identity-on-an-azure-vm-using-rest-api-calls"></a>Configurare l'identità gestita in una macchina virtuale di Azure usando chiamate API REST

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Identità gestita offre servizi di Azure con un'identità gestita automaticamente dal sistema in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come eseguire le seguenti operazioni di identità gestita in una macchina virtuale di Azure, usando CURL per effettuare chiamate all'endpoint REST di Azure Resource Manager:

- Abilitare e disabilitare l'identità assegnata dal sistema in una macchina virtuale di Azure
- Aggiungere e rimuovere un'identità assegnata dall'utente in una macchina virtuale di Azure

## <a name="prerequisites"></a>prerequisiti

- Se non si ha familiarità con l'identità del servizio gestita, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Se si usa Windows, installare il [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about) oppure utilizzare [Azure Cloud Shell](../../cloud-shell/overview.md) nel portale di Azure.
- [Installare la console locale di interfaccia della riga di comando di Azure](/azure/install-azure-cli), se si utilizzano i [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about) o una [distribuzione Linux del sistema operativo](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Se si usa la console locale di interfaccia della riga di comando di Azure, accedere ad Azure tramite `az login` con un account associato alla sottoscrizione Azure di cui si intende gestire le identità assegnate dal sistema o dall'utente.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-identity"></a>Identità assegnata dal sistema

Questa sezione illustra come attivare e disattivare le identità assegnate dal sistema in una macchina virtuale di Azure, usando CURL per effettuare chiamate all'endpoint REST di Azure Resource Manager.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm"></a>Abilitare l'identità assegnata dal sistema durante la creazione di una macchina virtuale di Azure

Per creare una VM di Azure con identità assegnata dal sistema abilitata, è necessario creare una macchina virtuale e recuperare un token di accesso per usare CURL per chiamare l'endpoint di Gestione risorse con il tipo di valore dell'identità assegnata dal sistema.

1. Creare un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md#terminology) per il contenuto e la distribuzione della macchina virtuale e le risorse correlate, usando [az group create](/cli/azure/group/#az_group_create). Se si dispone già di un gruppo di risorse da usare, è possibile ignorare questo passaggio:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Creare un'[interfaccia di rete](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) per la macchina virtuale:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Recuperare un token bearer di accesso, necessario nel passaggio successivo nell'intestazione dell'autorizzazione per creare una macchina virtuale con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Creare una macchina virtuale usando CURL per chiamare l'endpoint REST di Azure Resource Manager. L'esempio seguente crea una macchina virtuale denominata *myVM* con un'identità assegnata dal sistema, come indicato nel corpo della richiesta dal valore `"identity":{"type":"SystemAssigned"}`. Sostituire `<ACCESS TOKEN>` con il valore ricevuto nel passaggio precedente relativo alla richiesta di un token bearer di accesso e il valore `<SUBSCRIPTION ID>` appropriato per l'ambiente.
 
    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"TestVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
    ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Abilitare l'identità assegnata dal sistema in una macchina virtuale di Azure esistente

Per abilitare l'identità assegnata dal sistema su una macchina virtuale esistente, è necessario acquisire un token di accesso e quindi usare CURL per chiamare l'endpoint REST di Gestione risorse per aggiornare il tipo di identità.

1. Recuperare un token bearer di accesso, necessario nel passaggio successivo nell'intestazione dell'autorizzazione per creare una macchina virtuale con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Usare il comando CURL seguente per chiamare l'endpoint REST di Azure Resource Manager per abilitare l'identità assegnata dal sistema sulla macchina virtuale, come indicato dal valore `{"identity":{"type":"SystemAssigned"}` nel corpo della richiesta per una macchina virtuale denominata myVM.  Sostituire `<ACCESS TOKEN>` con il valore ricevuto nel passaggio precedente relativo alla richiesta di un token bearer di accesso e il valore `<SUBSCRIPTION ID>` appropriato per l'ambiente.
   
   > [!IMPORTANT]
   > Per accertarsi di non eliminare tutte le identità gestite assegnate dall'utente che sono associate alla macchina virtuale, è necessario elencare le identità assegnate dall'utente tramite questo comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se vi sono eventuali identità assegnate dall'utente associate alla macchina virtuale come indicato nel valore `identity` della risposta, passare al passaggio 3 dove viene illustrato come mantenere le identità assegnate dall'utente durante l'abilitazione dell'identità assegnata dal sistema sulla macchina virtuale.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Per abilitare l'identità assegnata dal sistema su una macchina virtuale con le identità assegnate dall'utente presenti, è necessario aggiungere `SystemAssigned` al valore`type`.  
   
   Ad esempio, se la macchina virtuale presenta identità assegnate dall'utente `ID1` e `ID2` assegnate ad essa e si desidera aggiungere un'identità assegnata dal sistema alla macchina virtuale, usare la seguente chiamata di CURL. Sostituire `<ACCESS TOKEN>` e `<SUBSCRIPTION ID>` con i valori appropriati all'ambiente.
   
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="disable-system-assigned-identity-from-an-azure-vm"></a>Disabilitare un'identità assegnata dal sistema in una macchina virtuale di Azure

Per disabilitare un'identità assegnata dal sistema in una macchina virtuale esistente, è necessario acquisire un token di accesso e quindi usare CURL per chiamare l'endpoint REST di Gestione risorse per aggiornare il tipo di identità a `None`.

1. Recuperare un token bearer di accesso, necessario nel passaggio successivo nell'intestazione dell'autorizzazione per creare una macchina virtuale con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aggiornare la macchina virtuale usando CURL per chiamare l'endpoint REST di Azure Resource Manager e disabilitare l'identità assegnata dal sistema.  L'esempio seguente disabilita un'identità assegnata dal sistema, come indicato nel corpo della richiesta dal valore `{"identity":{"type":"None"}}` da una macchina virtuale denominata *myVM*.  Sostituire `<ACCESS TOKEN>` con il valore ricevuto nel passaggio precedente relativo alla richiesta di un token bearer di accesso e il valore `<SUBSCRIPTION ID>` appropriato per l'ambiente.

   > [!IMPORTANT]
   > Per accertarsi di non eliminare tutte le identità gestite assegnate dall'utente che sono associate alla macchina virtuale, è necessario elencare le identità assegnate dall'utente tramite questo comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se vi sono eventuali identità assegnate dall'utente associate alla macchina virtuale come indicato nel valore `identity` della risposta, passare al passaggio 3 dove viene illustrato come mantenere le identità assegnate dall'utente durante la disattivazione dell'identità assegnata dal sistema sulla macchina virtuale.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

3. Per rimuovere l'identità assegnata dal sistema da una macchina che presenta identità assegnate dall'utente, rimuovere `SystemAssigned` dal valore `{"identity":{"type:" "}}` mantenendo il valore `UserAssigned` e la matrice `identityIds` che definisce quali identità assegnate dall'utente vengono associate alla macchina virtuale.

## <a name="user-assigned-identity"></a>Identità assegnata dall'utente

Questa sezione illustra come aggiungere e rimuovere le identità assegnate dall'utente in una macchina virtuale di Azure, usando CURL per effettuare chiamate all'endpoint REST di Azure Resource Manager.

### <a name="assign-a-user-assigned-identity-during-the-creation-of-an-azure-vm"></a>Assegnare un'identità assegnata dall'utente durante la creazione di una macchina virtuale di Azure

1. Recuperare un token bearer di accesso, necessario nel passaggio successivo nell'intestazione dell'autorizzazione per creare una macchina virtuale con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Creare un'[interfaccia di rete](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) per la macchina virtuale:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Recuperare un token bearer di accesso, necessario nel passaggio successivo nell'intestazione dell'autorizzazione per creare una macchina virtuale con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Creare un'identità assegnata dall'utente usando le istruzioni disponibili qui: [Creare un'identità gestita assegnata dall'utente](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Creare una macchina virtuale usando CURL per chiamare l'endpoint REST di Azure Resource Manager. L'esempio seguente crea una macchina virtuale denominata *myVM* nel gruppo risorse *myResourceGroup*, con un'identità assegnata dall'utente`ID1`, come indicato nel corpo della richiesta dal valore `"identity":{"type":"UserAssigned"}`. Sostituire `<ACCESS TOKEN>` con il valore ricevuto nel passaggio precedente relativo alla richiesta di un token bearer di accesso e il valore `<SUBSCRIPTION ID>` appropriato per l'ambiente.
 
   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM",{"identity":{"type":"UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"TestVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

### <a name="assign-a-user-assigned-identity-to-an-existing-azure-vm"></a>Assegnare un'identità assegnata dall'utente a una VM di Azure esistente

1. Recuperare un token bearer di accesso, necessario nel passaggio successivo nell'intestazione dell'autorizzazione per creare una macchina virtuale con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Creare un'identità assegnata dall'utente usando le istruzioni disponibili qui: [Creare un'identità gestita assegnata dall'utente](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3.  Per accertarsi di non eliminare le identità esistenti assegnate dall'utente o dal sistema che sono associate alla macchina virtuale, è necessario elencare i tipi di identità assegnati alla macchina virtuale tramite il seguente comando CURL. Se sono state gestite identità assegnate al set di scalabilità di macchine virtuali, vengono elencate di seguito nel valore `identity`.

    ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```

    Se vi sono eventuali identità assegnate dall'utente o dal sistema associate alla macchina virtuale come indicato nel valore `identity` della risposta, passare al passaggio 5 dove viene illustrato come mantenere le identità assegnate dall'utente durante l'abilitazione dell'identità assegnata dal sistema sulla macchina virtuale.

4. Se non si dispone di identità assegnate dall'utente associate alla macchina virtuale, usare il comando CURL seguente per chiamare l'endpoint REST di Azure Resource Manager e assegnare la prima identità assegnata dall'utente alla macchina virtuale.  Se si dispone di una o più identità assegnate dall'utente alla macchina virtuale, andare al passaggio successivo che illustra come aggiungere più identità assegnate dall'utente a una macchina virtuale.

   L'esempio seguente assegna un'identità assegnate dall'utente `ID1` a una macchina virtuale denominata *myVM* nel gruppo risorse *myResourceGroup*.  Sostituire `<ACCESS TOKEN>` con il valore ricevuto nel passaggio precedente relativo alla richiesta di un token bearer di accesso e il valore `<SUBSCRIPTION ID>` appropriato per l'ambiente.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

5. Se si dispone di identità assegnate dal sistema o dall'utente associate alla macchina virtuale, è necessario aggiungere la nuova identità assegnata dall'utente alla matrice `identityIDs`, mantenendo anche il nome utente e le identità assegnata dal sistema che sono attualmente assegnate alla macchina virtuale.

   Per esempio, se si dispone di identità assegnate dal sistema e di identità assegnata dall'utente `ID1` attualmente associate alla macchina virtuale e si desidera aggiungere l'identità dell'utente `ID2`, usare il comando CURL seguente. Sostituire `<ACCESS TOKEN>` con il valore ricevuto nei passaggi relativi alla richiesta di un token bearer di accesso e il valore `<SUBSCRIPTION ID>` appropriato per l'ambiente.

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

### <a name="remove-a-user-assigned-identity-from-an-azure-vm"></a>Rimuovere un'identità assegnata dall'utente da una macchina virtuale di Azure

1. Recuperare un token bearer di accesso, necessario nel passaggio successivo nell'intestazione dell'autorizzazione per creare una macchina virtuale con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Per accertarsi di non eliminare nessuna identità gestita assegnata dall'utente che si desidera mantenere associata alla macchina virtuale o di non rimuovere le identità assegnate dal sistema, è necessario elencare i tipi di identità assegnati alla macchina virtuale tramite il seguente comando CURL: 
 
   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2017-12-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   Se sono state gestite identità assegnate alla macchina virtuale, sono elencate nella risposta nel valore `identity`.

   Ad esempio, se si dispone di identità assegnata dall'utente `ID1` e `ID2` assegnate alla macchina virtuale e si desidera mantenerne assegnate soltanto `ID1` e conservare l'identità assegnata dal sistema, si utilizzerà lo stesso comando CURL come assegnazione di un'identità gestita dall'utente a una macchina virtuale, mantenendo solo il valore `ID1` e il valore `SystemAssigned`. Questa operazione rimuove la `ID2` identità assegnata dall'utente dalla macchina virtuale, conservando l'identità assegnata dal sistema.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned","UserAssigned", "identityIds":["/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourcegroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

Se la macchina virtuale ha identità sia assegnate dal sistema che assegnate dall'utente, è possibile rimuovere tutte le identità assegnate dall'utente iniziando a usare solo identità assegnate dal sistema, usando il comando seguente:

```bash
curl 'https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/TestVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
    
Se la macchina virtuale presenta solo identità assegnate dall'utente e si desidera rimuoverle tutte, usare il comando seguente:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```
## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come creare, elencare o eliminare l'utente assegnato tramite REST, vedere:

- [Creare, elencare ed eliminare un'identità assegnata dall'utente usando le chiamate API REST](how-to-manage-ua-identity-rest.md)