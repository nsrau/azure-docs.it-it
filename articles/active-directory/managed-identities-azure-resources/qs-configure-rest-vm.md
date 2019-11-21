---
title: Configure managed identities on Azure VM using REST - Azure AD
description: Istruzioni dettagliate per configurare identità gestite di sistema e assegnate dall'utente in una macchina virtuale di Azure usando CURL per effettuare le chiamate API REST.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/25/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eadbad5332147add9a1b30a25b9ad2403f1a108
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224582"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-rest-api-calls"></a>Configurare le identità gestite per risorse di Azure in una macchina virtuale di Azure usando le chiamate API REST

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità di sistema gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza inserire le credenziali nel codice. 

Questo articolo illustra come eseguire queste operazioni di identità gestite per risorse di Azure in una macchina virtuale di Azure usando CURL per effettuare le chiamate all'endpoint REST di Azure Resource Manager:

- Abilitare e disabilitare l'identità gestita assegnata dal sistema in una macchina virtuale di Azure
- Aggiungere e rimuovere un'identità gestita assegnata dall'utente in una macchina virtuale di Azure

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#how-does-it-work)** .
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Se si usa Windows, installare il [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about) oppure utilizzare [Azure Cloud Shell](../../cloud-shell/overview.md) nel portale di Azure.
- [Installare la console locale di interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli), se si utilizzano i [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about) o una [distribuzione Linux del sistema operativo](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Se si usa la console locale dell'interfaccia della riga di comando di Azure, accedere ad Azure tramite `az login` con un account associato alla sottoscrizione di Azure di cui si intende gestire le identità gestite assegnate dal sistema o dall'utente.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Identità gestita assegnata dal sistema

Questa sezione illustra come abilitare e disattivare le identità gestite assegnate dal sistema in una macchina virtuale di Azure usando CURL per effettuare le chiamate all'endpoint REST di Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm"></a>Abilitare l'identità gestita assegnata dal sistema durante la creazione di una macchina virtuale di Azure

Per creare una macchina virtuale di Azure con l'identità gestita assegnata dal sistema abilitata, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Creare un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md#terminology) per il contenuto e la distribuzione della macchina virtuale e le risorse correlate, usando [az group create](/cli/azure/group/#az-group-create). Se si dispone già di un gruppo di risorse da usare, è possibile ignorare questo passaggio:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Creare un'[interfaccia di rete](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) per la macchina virtuale:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Recuperare un bearer token di accesso, che sarà usato nel passaggio successivo nell'intestazione dell'autorizzazione per creare una macchina virtuale con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Creare una macchina virtuale usando CURL per chiamare l'endpoint REST di Azure Resource Manager. L'esempio seguente crea una macchina virtuale denominata *myVM* con un'identità gestita assegnata dal sistema, come indicato nel corpo della richiesta dal valore `"identity":{"type":"SystemAssigned"}`. Sostituire `<ACCESS TOKEN>` con il valore ricevuto nel passaggio precedente relativo alla richiesta di un token bearer di accesso e il valore `<SUBSCRIPTION ID>` appropriato per l'ambiente.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"SystemAssigned"},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"<SECURE PASSWORD STRING>"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Intestazioni della richiesta**
   
   |Intestazione della richiesta  |Description  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido.        | 
   
   **Corpo della richiesta**

   ```JSON
     {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }  
   ```

### <a name="enable-system-assigned-identity-on-an-existing-azure-vm"></a>Abilitare l'identità assegnata dal sistema in una macchina virtuale di Azure esistente

Per abilitare l'identità gestita assegnata dal sistema in una macchina virtuale di cui originariamente è stato effettuato il provisioning senza tale identità, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Recuperare un bearer token di accesso, che sarà usato nel passaggio successivo nell'intestazione dell'autorizzazione per creare una macchina virtuale con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Usare il comando CURL seguente per chiamare l'endpoint REST di Azure Resource Manager per abilitare l'identità gestita assegnata dal sistema nella macchina virtuale, come indicato dal valore `{"identity":{"type":"SystemAssigned"}` nel corpo della richiesta per una macchina virtuale denominata *myVM*.  Sostituire `<ACCESS TOKEN>` con il valore ricevuto nel passaggio precedente relativo alla richiesta di un token bearer di accesso e il valore `<SUBSCRIPTION ID>` appropriato per l'ambiente.
   
   > [!IMPORTANT]
   > Per accertarsi di non eliminare identità gestite assegnate dall'utente associate alla macchina virtuale, è necessario elencare le identità assegnate dall'utente tramite questo comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se sono presenti identità gestite assegnate dall'utente associate alla macchina virtuale come indicato nel valore `identity` della risposta, andare al passaggio 3 che spiega come conservare le identità gestite assegnate dall'utente quando si abilita l'identità gestita assegnata dal sistema nella macchina virtuale.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Intestazioni della richiesta**

   |Intestazione della richiesta  |Description  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido.        | 
   
   **Corpo della richiesta**
    
   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned"
       }
    }
   ```

3. Per abilitare l'identità gestita assegnata dal sistema in una macchina virtuale con identità gestite assegnate dall'utente, è necessario aggiungere `SystemAssigned` al valore `type`.  
   
   Se ad esempio la macchina virtuale include le identità gestite assegnate dall'utente `ID1` e `ID2` e si intende aggiungervi un'identità gestita assegnata dal sistema, usare la seguente chiamata di CURL. Sostituire `<ACCESS TOKEN>` e `<SUBSCRIPTION ID>` con i valori appropriati all'ambiente.

   La versione dell'API `2018-06-01` archivia le identità gestite assegnate dall'utente nel valore `userAssignedIdentities` in formato dizionario anziché nel valore `identityIds` in formato matrice usato nella versione dell'API `2017-12-01`.
   
   **VERSIONE API 01/06/2018**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Intestazioni della richiesta**

   |Intestazione della richiesta  |Description  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido.        | 

   **Corpo della richiesta**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{  
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{  
    
             }
          }
       }
    }
   ```

   **VERSIONE API 01-12-2017**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
    
   **Intestazioni della richiesta**

   |Intestazione della richiesta  |Description  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido.        | 

   **Corpo della richiesta**

   ```JSON
    {  
       "identity":{  
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[  
             "/subscriptions/<<SUBSCRIPTION ID>>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="disable-system-assigned-managed-identity-from-an-azure-vm"></a>Disabilitare un'identità gestita assegnata dal sistema in una macchina virtuale di Azure

Per disabilitare l'identità gestita assegnata dal sistema in una macchina virtuale, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).  Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Recuperare un bearer token di accesso, che sarà usato nel passaggio successivo nell'intestazione dell'autorizzazione per creare una macchina virtuale con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aggiornare la macchina virtuale usando CURL per chiamare l'endpoint REST di Azure Resource Manager e disabilitare l'identità gestita assegnata dal sistema.  L'esempio seguente disabilita l'identità gestita assegnata dal sistema, come indicato dal valore `{"identity":{"type":"None"}}` nel corpo della richiesta da una macchina virtuale denominata *myVM*.  Sostituire `<ACCESS TOKEN>` con il valore ricevuto nel passaggio precedente relativo alla richiesta di un token bearer di accesso e il valore `<SUBSCRIPTION ID>` appropriato per l'ambiente.

   > [!IMPORTANT]
   > Per accertarsi di non eliminare identità gestite assegnate dall'utente associate alla macchina virtuale, è necessario elencare le identità assegnate dall'utente tramite questo comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se sono presenti identità gestite assegnate dall'utente associate alla macchina virtuale come indicato nel valore `identity` della risposta, andare al passaggio 3 che spiega come conservare le identità gestite assegnate dall'utente quando si disabilita l'identità gestita assegnata dal sistema nella macchina virtuale.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Intestazioni della richiesta**

   |Intestazione della richiesta  |Description  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido.        | 

   **Corpo della richiesta**

   ```JSON
    {  
       "identity":{  
          "type":"None"
       }
    }
   ```

   Per rimuovere l'identità gestita assegnata dal sistema da una macchina virtuale con identità gestite assegnate dall'utente, rimuovere `SystemAssigned` dal valore `{"identity":{"type:" "}}` mantenendo il valore `UserAssigned` e i valori di dizionario `userAssignedIdentities`, se si usa la **versione dell'API 01/06/2018**. Se invece si usa la **versione dell'API 01/12/2017** o precedenti, mantenere la matrice `identityIds`.

## <a name="user-assigned-managed-identity"></a>Identità gestita assegnata dall'utente

Questa sezione illustra come aggiungere e rimuovere identità gestite assegnate dall'utente in una macchina virtuale di Azure usando CURL per effettuare le chiamate all'endpoint REST di Azure Resource Manager.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-an-azure-vm"></a>Assegnare un'identità gestita assegnata dall'utente durante la creazione di una macchina virtuale di Azure

Per assegnare un'identità assegnata dall'utente a una macchina virtuale, all'account devono essere assegnati i ruoli [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [Operatore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Recuperare un bearer token di accesso, che sarà usato nel passaggio successivo nell'intestazione dell'autorizzazione per creare una macchina virtuale con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Creare un'[interfaccia di rete](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) per la macchina virtuale:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3. Recuperare un bearer token di accesso, che sarà usato nel passaggio successivo nell'intestazione dell'autorizzazione per creare una macchina virtuale con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Creare un'identità gestita assegnata dall'utente usando le istruzioni disponibili qui: [Creare un'identità gestita assegnata dall'utente](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Creare una macchina virtuale usando CURL per chiamare l'endpoint REST di Azure Resource Manager. L'esempio seguente crea una macchina virtuale denominata *myVM* nel gruppo di risorse *myResourceGroup*, con un'identità gestita assegnata dall'utente `ID1`, come indicato nel corpo della richiesta dal valore `"identity":{"type":"UserAssigned"}`. Sostituire `<ACCESS TOKEN>` con il valore ricevuto nel passaggio precedente relativo alla richiesta di un token bearer di accesso e il valore `<SUBSCRIPTION ID>` appropriato per l'ambiente.
 
   **VERSIONE API 01/06/2018**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
    
   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |Description  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido.        | 

   **Corpo della richiesta**

   ```JSON
    {  
       "location":"westus",
       "name":"myVM",
       "identity":{  
          "type":"UserAssigned",
          "identityIds":[  
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{  
          "hardwareProfile":{  
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{  
             "imageReference":{  
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{  
                "caching":"ReadWrite",
                "managedDisk":{  
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[  
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {  
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{  
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{  
             "networkInterfaces":[  
                {  
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{  
                      "primary":true
                   }
                }
             ]
          }
       }
    }

   ```
  
   **VERSIONE API 01-12-2017**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PUT -d '{"location":"westus","name":"myVM","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"hardwareProfile":{"vmSize":"Standard_D2_v2"},"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"name":"myVM3osdisk","createOption":"FromImage"},"dataDisks":[{"diskSizeGB":1023,"createOption":"Empty","lun":0},{"diskSizeGB":1023,"createOption":"Empty","lun":1}]},"osProfile":{"adminUsername":"azureuser","computerName":"myVM","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaces":[{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic","properties":{"primary":true}}]}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |Description  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido.        | 

   **Corpo della richiesta**

   ```JSON
    {
       "location":"westus",
       "name":"myVM",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "hardwareProfile":{
             "vmSize":"Standard_D2_v2"
          },
          "storageProfile":{
             "imageReference":{
                "sku":"2016-Datacenter",
                "publisher":"MicrosoftWindowsServer",
                "version":"latest",
                "offer":"WindowsServer"
             },
             "osDisk":{
                "caching":"ReadWrite",
                "managedDisk":{
                   "storageAccountType":"Standard_LRS"
                },
                "name":"myVM3osdisk",
                "createOption":"FromImage"
             },
             "dataDisks":[
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":0
                },
                {
                   "diskSizeGB":1023,
                   "createOption":"Empty",
                   "lun":1
                }
             ]
          },
          "osProfile":{
             "adminUsername":"azureuser",
             "computerName":"myVM",
             "adminPassword":"myPassword12"
          },
          "networkProfile":{
             "networkInterfaces":[
                {
                   "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/myNic",
                   "properties":{
                      "primary":true
                   }
                }
             ]
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-vm"></a>Assegnare un'identità gestita assegnata dall'utente a una macchina virtuale di Azure esistente

Per assegnare un'identità assegnata dall'utente a una macchina virtuale, all'account devono essere assegnati i ruoli [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) e [Operatore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-operator). Non sono necessarie altre assegnazioni di ruoli di Azure Active Directory.

1. Recuperare un bearer token di accesso, che sarà usato nel passaggio successivo nell'intestazione dell'autorizzazione per creare una macchina virtuale con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Creare un'identità gestita assegnata dall'utente usando le istruzioni disponibili qui: [Creare un'identità gestita assegnata dall'utente](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Per accertarsi di non eliminare identità gestite assegnate dall'utente o dal sistema già associate alla macchina virtuale, è necessario elencare i tipi di identità assegnati alla macchina virtuale tramite il seguente comando CURL. Se sono state gestite identità assegnate al set di scalabilità di macchine virtuali, vengono elencate di seguito nel valore `identity`.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```
   **Intestazioni della richiesta**

   |Intestazione della richiesta  |Description  |
   |---------|---------|
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido.

    If you have any user or system-assigned managed identities assigned to the VM as identified in the `identity` value in the response, skip to step 5 that shows you how to retain the system-assigned managed identity while adding a user-assigned managed identity on your VM.

4. Se non si dispone di identità gestite assegnate dall'utente associate alla macchina virtuale, usare il comando CURL seguente per chiamare all'endpoint REST di Azure Resource Manager e assegnare la prima identità gestita assegnata dall'utente alla macchina virtuale.

   L'esempio seguente assegna un'identità gestita assegnata dall'utente `ID1` a una macchina virtuale denominata *myVM* nel gruppo di risorse *myResourceGroup*.  Sostituire `<ACCESS TOKEN>` con il valore ricevuto nel passaggio precedente relativo alla richiesta di un token bearer di accesso e il valore `<SUBSCRIPTION ID>` appropriato per l'ambiente.

   **VERSIONE API 01/06/2018**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   **Intestazioni della richiesta**

   |Intestazione della richiesta  |Description  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido.        |
 
   **Corpo della richiesta**

   ```JSON
    {
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ```

   **VERSIONE API 01-12-2017**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```
   
   **Intestazioni della richiesta**

   |Intestazione della richiesta  |Description  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido.        | 

   **Corpo della richiesta**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

5. Se nella macchina virtuale è già presente un'identità gestita assegnata dal sistema o dall'utente:
   
   **VERSIONE API 01/06/2018**

   Aggiungere l'identità gestita assegnata dall'utente al valore di dizionario `userAssignedIdentities`.
    
   Se ad esempio la macchina virtuale dispone già di identità gestite assegnate dal sistema e dell'identità gestita assegnata dall'utente `ID1` e si intende aggiungervi l'identità gestita assegnata dall'utente `ID2`:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```
   
   **Intestazioni della richiesta**

   |Intestazione della richiesta  |Description  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido.        | 

   **Corpo della richiesta**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             },
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{
    
             }
          }
       }
    }
   ```

   **VERSIONE API 01-12-2017**

   Quando si aggiunge la nuova identità gestita assegnata dall'utente, mantenere le identità gestite assegnate dall'utente desiderate nel valore di matrice `identityIds`.

   Se ad esempio la macchina virtuale dispone già di identità gestite assegnate dal sistema e dell'identità gestita assegnata dall'utente `ID1` e si intende aggiungervi l'identità gestita assegnata dall'utente `ID2`: 

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |Description  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido.        | 

   **Corpo della richiesta**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```   

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Rimuovere un'identità gestita assegnata dall'utente da una macchina virtuale di Azure

Per rimuovere un'identità assegnata dall'utente da una macchina virtuale, all'account deve essere assegnato il ruolo [Collaboratore Macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor).

1. Recuperare un bearer token di accesso, che sarà usato nel passaggio successivo nell'intestazione dell'autorizzazione per creare una macchina virtuale con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Per accertarsi di non eliminare identità gestite assegnate dall'utente che si intende mantenere associate alla macchina virtuale o di non rimuovere l'identità gestita assegnata dal sistema, è necessario elencare le identità gestite usando il seguente comando CURL: 

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```
 
   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |Description  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido.
 
   Se sono state gestite identità assegnate alla macchina virtuale, sono elencate nella risposta nel valore `identity`.

   Se ad esempio alla macchina virtuale sono associate le identità gestite assegnate dall'utente `ID1` e `ID2` e si vuole mantenere solo `ID1` e l'identità gestita assegnata dal sistema:
   
   **VERSIONE API 01/06/2018**

   Aggiungere `null` all'identità gestita assegnata dall'utente che si intende rimuovere:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |Description  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido.        | 

   **Corpo della richiesta**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null
          }
       }
    }
   ```

   **VERSIONE API 01-12-2017**

   Mantenere solo le identità gestite assegnate dall'utente desiderate nella matrice `identityIds`:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2017-12-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |Description  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido.        | 

   **Corpo della richiesta**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Se la macchina virtuale include sia identità gestite assegnate dal sistema che identità gestite assegnate dall'utente, è possibile rimuovere tutte le identità gestite assegnate dall'utente passando a usare solo quelle assegnate dal sistema tramite il comando seguente:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Intestazioni della richiesta**

|Intestazione della richiesta  |Description  |
|---------|---------|
|*Content-Type*     | Richiesto. Impostare su `application/json`.        |
|*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido. | 

**Corpo della richiesta**

```JSON
{
   "identity":{
      "type":"SystemAssigned"
   }
}
```
    
Se la macchina virtuale include solo identità gestite assegnate dall'utente e si intende rimuoverle tutte, usare il comando seguente:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM?api-version=2018-06-01 HTTP/1.1
```

**Intestazioni della richiesta**

|Intestazione della richiesta  |Description  |
|---------|---------|
|*Content-Type*     | Richiesto. Impostare su `application/json`.        |
|*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido.| 

**Corpo della richiesta**

```JSON
{
   "identity":{
      "type":"None"
   }
}
```

## <a name="next-steps"></a>Passaggi successivi

Per informazioni su come creare, elencare o eliminare le identità gestite assegnate dall'utente usando REST, vedere:

- [Create, list or delete a user-assigned managed identities using REST API calls](how-to-manage-ua-identity-rest.md) (Creare, elencare o eliminare un'identità gestita assegnata dall'utente tramite le chiamate API REST)
