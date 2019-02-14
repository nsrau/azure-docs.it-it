---
title: Come configurare identità gestite di sistema o assegnate dall'utente in un set di scalabilità di macchine virtuali di Azure tramite REST
description: Istruzioni dettagliate per configurare identità gestite di sistema e assegnate dall'utente in un set di scalabilità di macchine virtuali di Azure usando CURL per eseguire le chiamate API REST.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/25/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 10d208cab8b39b777fea7809be418613f17751b1
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170287"
---
# <a name="configure-managed-identities-for-azure-resources-on-a-virtual-machine-scale-set-using-rest-api-calls"></a>Configurare identità gestite per le risorse di Azure in un set di scalabilità di macchine virtuali tramite le chiamate API REST

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Le identità gestite per le risorse di Azure offrono ai servizi di Azure un'identità di sistema gestita automaticamente in Azure Active Directory. È possibile usare questa identità per l'autenticazione a qualsiasi servizio che supporti l'autenticazione di Azure AD senza dover inserire le credenziali nel codice. 

Questo articolo illustra come eseguire queste operazioni di identità gestite per risorse di Azure in un set di scalabilità di macchine virtuali di Azure usando CURL per eseguire le chiamate all'endpoint REST di Azure Resource Manager:

- Abilitare e disabilitare l'identità gestita assegnata dal sistema in un set di scalabilità di macchine virtuali di Azure
- Aggiungere e rimuovere un'identità gestita assegnata dall'utente in un set di scalabilità di macchine virtuali di Azure

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha familiarità con le identità gestite per le risorse di Azure, vedere la [sezione sulla panoramica](overview.md). **Assicurarsi di conoscere la [differenza tra identità assegnata dal sistema e identità gestita assegnata dall'utente](overview.md#how-does-it-work)**.
- Se non si ha un account Azure, [registrarsi per ottenere un account gratuito](https://azure.microsoft.com/free/) prima di continuare.
- Per eseguire le operazioni di gestione illustrate in questo articolo, l'account deve avere le seguenti assegnazioni di controllo degli accessi in base al ruolo:

    > [!NOTE]
    > Non sono necessarie altre assegnazioni di ruoli della directory di Azure AD.

    - [Collaboratore macchina virtuale](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) per creare un set di scalabilità di macchine virtuali e abilitare e rimuovere da un set di scalabilità di macchine virtuali l'identità gestita assegnata dal sistema e/o dall'utente.
    - [Collaboratore di identità gestite](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) per creare un'identità gestita assegnata dall'utente.
    - [Operatore identità gestita](/azure/role-based-access-control/built-in-roles#managed-identity-operator) per assegnare e rimuovere un'identità assegnata dall'utente da e verso un set di scalabilità di macchine virtuali.
- Se si usa Windows, installare il [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about) oppure usare [Azure Cloud Shell](../../cloud-shell/overview.md) nel portale di Azure.
- [Installare la console locale di interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli), se si utilizzano i [sottosistema Windows per Linux](https://msdn.microsoft.com/commandline/wsl/about) o una [distribuzione Linux del sistema operativo](/cli/azure/install-azure-cli-apt?view=azure-cli-latest).
- Se si usa la console locale dell'interfaccia della riga di comando di Azure, accedere ad Azure tramite `az login` con un account associato alla sottoscrizione di Azure di cui si intende gestire le identità gestite assegnate dal sistema o dall'utente.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="system-assigned-managed-identity"></a>Identità gestita assegnata dal sistema

Questa sezione illustra come attivare e disattivare le identità gestite assegnate dal sistema in set di scalabilità di macchine virtuali, usando CURL per eseguire chiamate all'endpoint REST di Azure Resource Manager.

### <a name="enable-system-assigned-managed-identity-during-creation-of-a-virtual-machine-scale-set"></a>Abilitare l'identità gestita assegnata dal sistema durante la creazione di un set di scalabilità di macchine virtuali

Per creare un set di scalabilità di macchine virtuali con identità gestita assegnata dal sistema abilitata, è necessario creare un set di scalabilità di macchine virtuali e recuperare un token di accesso per usare CURL per chiamare l'endpoint di Gestione risorse con il tipo di valore dell'identità gestita assegnata dal sistema.

1. Creare un [gruppo di risorse](../../azure-resource-manager/resource-group-overview.md#terminology) per il contenuto e la distribuzione del set di scalabilità di macchine virtuali e delle risorse correlate, usando [az group create](/cli/azure/group/#az-group-create). Se si dispone già di un gruppo di risorse da usare, è possibile ignorare questo passaggio:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

2. Creare [un'interfaccia di rete](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) per il set di scalabilità di macchine virtuali:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Recuperare un bearer token di accesso, necessario nel passaggio successivo nell'intestazione dell'autorizzazione per creare un set di scalabilità di macchine virtuali con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Creare un set di scalabilità di macchine virtuali usando CURL per chiamare l'endpoint REST di Azure Resource Manager. L'esempio seguente crea un set di scalabilità di macchine virtuali denominato *myVMSS* in *myResourceGroup*, con un'identità gestita assegnata dal sistema, come indicato nel corpo della richiesta dal valore `"identity":{"type":"SystemAssigned"}`. Sostituire `<ACCESS TOKEN>` con il valore ricevuto nel passaggio precedente relativo alla richiesta di un token bearer di accesso e il valore `<SUBSCRIPTION ID>` appropriato per l'ambiente.

   ```bash   
  curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"SystemAssigned"},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
  ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

  **Intestazioni della richiesta**

  |Intestazione della richiesta  |DESCRIZIONE  |
  |---------|---------|
  |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
  |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido. | 

  **Corpo della richiesta**

  ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"SystemAssigned"
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }  
  ```  

### <a name="enable-system-assigned-managed-identity-on-a-existing-virtual-machine-scale-set"></a>Abilitare l'identità gestita assegnata dal sistema in un set di scalabilità di macchine virtuali esistente

Per abilitare l'identità gestita assegnata dal sistema in un set di scalabilità di macchine virtuali esistente, è necessario acquisire un token di accesso e quindi usare CURL per chiamare l'endpoint REST di Gestione risorse per aggiornare il tipo di identità.

1. Recuperare un bearer token di accesso, necessario nel passaggio successivo nell'intestazione dell'autorizzazione per creare un set di scalabilità di macchine virtuali con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Usare il comando CURL seguente per chiamare l'endpoint REST di Azure Resource Manager per abilitare l'identità gestita assegnata dal sistema sul set di scalabilità di macchine virtuali, come indicato dal valore `{"identity":{"type":"SystemAssigned"}` nel corpo della richiesta per un set di scalabilità di macchine virtuali denominato *myVMSS*.  Sostituire `<ACCESS TOKEN>` con il valore ricevuto nel passaggio precedente relativo alla richiesta di un token bearer di accesso e il valore `<SUBSCRIPTION ID>` appropriato per l'ambiente.
   
   > [!IMPORTANT]
   > Per verificare di non eliminare alcuna identità gestita assegnata dall'utente associata al set di scalabilità di macchine virtuali, è necessario elencare le identità gestite assegnate dall'utente tramite questo comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se vi sono eventuali identità gestite assegnate dall'utente associate al set di scalabilità di macchine virtuali come indicato nel valore `identity` della risposta, passare al passaggio 3 dove viene illustrato come mantenere le identità gestite assegnate dall'utente durante l'abilitazione dell'identità gestita assegnata dal sistema sul set di scalabilità di macchine virtuali.

   ```bash
    curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |DESCRIZIONE  |
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

3. Per abilitare l'identità gestita assegnata dal sistema su un set di scalabilità di macchine virtuali con le identità gestite assegnate dall'utente presenti, è necessario aggiungere `SystemAssigned` al valore`type`.  
   
   Se, ad esempio, il set di scalabilità di macchine virtuali presenta identità gestite assegnate dall'utente `ID1` e `ID2` assegnate al set stesso e si intende aggiungere un'identità gestita assegnata dal sistema al set di scalabilità di macchine virtuali, usare la chiamata di CURL seguente. Sostituire `<ACCESS TOKEN>` e `<SUBSCRIPTION ID>` con i valori appropriati all'ambiente.

   La versione dell'API `2018-06-01` archivia le identità gestite assegnate dall'utente nel valore `userAssignedIdentities` in formato dizionario anziché nel valore `identityIds` in formato matrice usato nella versione dell'API `2017-12-01`.
   
   **VERSIONE API 01/06/2018**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |DESCRIZIONE  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido. |
 
   **Corpo della richiesta**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
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

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |DESCRIZIONE  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido. | 

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

### <a name="disable-system-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Disabilitare un'identità gestita assegnata dal sistema da un set di scalabilità di macchine virtuali

Per disabilitare l'identità assegnata dal sistema in un set di scalabilità di macchine virtuali esistente, è necessario acquisire un token di accesso e quindi usare CURL per chiamare l'endpoint REST di Gestione risorse per aggiornare il tipo di identità a `None`.

1. Recuperare un bearer token di accesso, necessario nel passaggio successivo nell'intestazione dell'autorizzazione per creare un set di scalabilità di macchine virtuali con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Aggiornare il set di scalabilità di macchine virtuali usando CURL per chiamare l'endpoint REST di Azure Resource Manager e disabilitare l'identità gestita assegnata dal sistema.  L'esempio seguente disabilita un'identità gestita assegnata dal sistema, come indicato nel corpo della richiesta dal valore `{"identity":{"type":"None"}}` da un set di scalabilità di macchine virtuali denominato *myVMSS*.  Sostituire `<ACCESS TOKEN>` con il valore ricevuto nel passaggio precedente relativo alla richiesta di un token bearer di accesso e il valore `<SUBSCRIPTION ID>` appropriato per l'ambiente.

   > [!IMPORTANT]
   > Per verificare di non eliminare alcuna identità gestita assegnata dall'utente associata al set di scalabilità di macchine virtuali, è necessario elencare le identità gestite assegnate dall'utente tramite questo comando CURL: `curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"`. Se sono presenti eventuali identità gestite assegnate dall'utente associate al set di scalabilità di macchine virtuali, passare al passaggio 3 dove viene illustrato come mantenere le identità gestite assegnate dall'utente durante la rimozione dell'identità gestita assegnata dal sistema sul set di scalabilità di macchine virtuali.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |DESCRIZIONE  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido. | 

   **Corpo della richiesta**

   ```JSON
    {
       "identity":{
          "type":"None"
       }
    }
   ```

   Per rimuovere l'identità gestita assegnata dal sistema da un set di scalabilità di macchine virtuali con identità gestite assegnate dall'utente, rimuovere `SystemAssigned` dal valore `{"identity":{"type:" "}}` mantenendo il valore `UserAssigned` e i valori di dizionario `userAssignedIdentities`, se si usa la **versione dell'API 01/06/2018**. Se invece si usa la **versione dell'API 01/12/2017** o precedenti, mantenere la matrice `identityIds`.

## <a name="user-assigned-managed-identity"></a>Identità gestita assegnata dall'utente

Questa sezione illustra come aggiungere e rimuovere le identità gestite assegnate dall'utente in un set di scalabilità di macchine virtuali, usando CURL per effettuare chiamate all'endpoint REST di Azure Resource Manager.

### <a name="assign-a-user-assigned-managed-identity-during-the-creation-of-a-virtual-machine-scale-set"></a>Assegnare un'identità gestita assegnata dall'utente durante la creazione di un set di scalabilità di macchine virtuali

1. Recuperare un bearer token di accesso, necessario nel passaggio successivo nell'intestazione dell'autorizzazione per creare un set di scalabilità di macchine virtuali con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Creare [un'interfaccia di rete](/cli/azure/network/nic?view=azure-cli-latest#az-network-nic-create) per il set di scalabilità di macchine virtuali:

   ```azurecli-interactive
    az network nic create -g myResourceGroup --vnet-name myVnet --subnet mySubnet -n myNic
   ```

3.  Recuperare un bearer token di accesso, necessario nel passaggio successivo nell'intestazione dell'autorizzazione per creare un set di scalabilità di macchine virtuali con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ``` 

4. Creare un'identità gestita assegnata dall'utente usando le istruzioni disponibili qui: [Creare un'identità gestita assegnata dall'utente](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

5. Creare un set di scalabilità di macchine virtuali usando CURL per chiamare l'endpoint REST di Azure Resource Manager. L'esempio seguente crea un set di scalabilità di macchine virtuali denominato *myVMSS* nel gruppo di risorse *myResourceGroup*, con un'identità gestita assegnata dall'utente €`ID1`, come indicato nel corpo della richiesta dal valore `"identity":{"type":"UserAssigned"}`. Sostituire `<ACCESS TOKEN>` con il valore ricevuto nel passaggio precedente relativo alla richiesta di un token bearer di accesso e il valore `<SUBSCRIPTION ID>` appropriato per l'ambiente.
 
   **VERSIONE API 01/06/2018**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |DESCRIZIONE  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido. | 

   **Corpo della richiesta**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```   

   **VERSIONE API 01-12-2017**

   ```bash   
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PUT -d '{"sku":{"tier":"Standard","capacity":3,"name":"Standard_D1_v2"},"location":"eastus","identity":{"type":"UserAssigned","identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]},"properties":{"overprovision":true,"virtualMachineProfile":{"storageProfile":{"imageReference":{"sku":"2016-Datacenter","publisher":"MicrosoftWindowsServer","version":"latest","offer":"WindowsServer"},"osDisk":{"caching":"ReadWrite","managedDisk":{"storageAccountType":"Standard_LRS"},"createOption":"FromImage"}},"osProfile":{"computerNamePrefix":"myVMSS","adminUsername":"azureuser","adminPassword":"myPassword12"},"networkProfile":{"networkInterfaceConfigurations":[{"name":"myVMSS","properties":{"primary":true,"enableIPForwarding":true,"ipConfigurations":[{"name":"myVMSS","properties":{"subnet":{"id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"}}}]}}]}},"upgradePolicy":{"mode":"Manual"}}}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |DESCRIZIONE  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido. |
 
   **Corpo della richiesta**

   ```JSON
    {
       "sku":{
          "tier":"Standard",
          "capacity":3,
          "name":"Standard_D1_v2"
       },
       "location":"eastus",
       "identity":{
          "type":"UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       },
       "properties":{
          "overprovision":true,
          "virtualMachineProfile":{
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
                   "createOption":"FromImage"
                }
             },
             "osProfile":{
                "computerNamePrefix":"myVMSS",
                "adminUsername":"azureuser",
                "adminPassword":"myPassword12"
             },
             "networkProfile":{
                "networkInterfaceConfigurations":[
                   {
                      "name":"myVMSS",
                      "properties":{
                         "primary":true,
                         "enableIPForwarding":true,
                         "ipConfigurations":[
                            {
                               "name":"myVMSS",
                               "properties":{
                                  "subnet":{
                                     "id":"/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/mySubnet"
                                  }
                               }
                            }
                         ]
                      }
                   }
                ]
             }
          },
          "upgradePolicy":{
             "mode":"Manual"
          }
       }
    }
   ```

### <a name="assign-a-user-assigned-managed-identity-to-an-existing-azure-virtual-machine-scale-set"></a>Assegnare un'identità gestita assegnata dall'utente a un set di scalabilità di macchine virtuali di Azure esistente

1. Recuperare un bearer token di accesso, necessario nel passaggio successivo nell'intestazione dell'autorizzazione per creare un set di scalabilità di macchine virtuali con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2.  Creare un'identità gestita assegnata dall'utente usando le istruzioni disponibili qui: [Creare un'identità gestita assegnata dall'utente](how-to-manage-ua-identity-rest.md#create-a-user-assigned-managed-identity).

3. Per verificare di non eliminare le identità esistenti assegnate dall'utente o dal sistema associate al set di scalabilità di macchine virtuali, è necessario elencare i tipi di identità assegnati al set di scalabilità di macchine virtuali tramite il comando CURL seguente. Se sono state gestite identità assegnate al set di scalabilità di macchine virtuali, queste vengono elencate di seguito nel valore `identity`.

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |DESCRIZIONE  |
   |---------|---------|
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido. |   
 

4. Se non si dispone di identità gestite assegnate dall'utente o dal sistema associate al set di scalabilità di macchine virtuali, usare il comando CURL seguente per chiamare l'endpoint REST di Azure Resource Manager e assegnare la prima identità gestita assegnata dall'utente al set di scalabilità di macchine virtuali.  Se sono presenti eventuali identità gestite assegnate dall'utente o dal sistema associate al set di scalabilità di macchine virtuali, passare al passaggio 5 dove viene illustrato come aggiungere le identità gestite assegnate dall'utente a un set di scalabilità di macchine virtuali, conservando allo stesso tempo l'identità gestita assegnata dal sistema.

   L'esempio seguente assegna un'identità gestita assegnate dall'utente `ID1` a un set di scalabilità di macchine virtuali denominato *myVMSS* nel gruppo di risorse *myResourceGroup*.  Sostituire `<ACCESS TOKEN>` con il valore ricevuto nel passaggio precedente relativo alla richiesta di un token bearer di accesso e il valore `<SUBSCRIPTION ID>` appropriato per l'ambiente.

   **VERSIONE API 01/06/2018**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-12-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |DESCRIZIONE  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido. | 

   **Corpo della richiesta**

   ```JSON
    {
       "identity":{
          "type":"userAssigned",
          "userAssignedIdentities":{
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{
    
             }
          }
       }
    }
   ``` 
    
   **VERSIONE API 01-12-2017**

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"userAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |DESCRIZIONE  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido. | 

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

5. Se si dispone di un'identità gestita assegnata dall'utente o dal sistema associata al set di scalabilità di macchine virtuali:
   
   **VERSIONE API 01/06/2018**

   Aggiungere l'identità gestita assegnata dall'utente al valore di dizionario `userAssignedIdentities`.

   Se ad esempio il set di scalabilità di macchine virtuali dispone già di identità gestite assegnate dal sistema e assegnate dall'utente `ID1` e si intende aggiungervi l'identità gestita assegnata dall'utente `ID2`:

   ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1":{},"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":{}}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |DESCRIZIONE  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido. | 

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

   Se ad esempio il set di scalabilità di macchine virtuali dispone già di identità assegnate dal sistema e assegnate dall'utente `ID1` e si intende aggiungervi l'identità gestita assegnata dall'utente `ID2`:

    ```bash
   curl  'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1","/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

    **Intestazioni della richiesta**

   |Intestazione della richiesta  |DESCRIZIONE  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido. | 

   **Corpo della richiesta**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned, UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1",
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2"
          ]
       }
    }
   ```

### <a name="remove-a-user-assigned-managed-identity-from-a-virtual-machine-scale-set"></a>Rimuovere un'identità gestita assegnata dall'utente da un set di scalabilità di macchine virtuali

1. Recuperare un bearer token di accesso, necessario nel passaggio successivo nell'intestazione dell'autorizzazione per creare un set di scalabilità di macchine virtuali con un'identità gestita assegnata dal sistema.

   ```azurecli-interactive
   az account get-access-token
   ```

2. Per verificare di non eliminare alcuna identità gestita assegnata dall'utente che si intende mantenere associata al set di scalabilità di macchine virtuali o di non rimuovere le identità gestite assegnate dal sistema, è necessario elencare i tipi di identità assegnati alla macchina virtuale tramite il comando CURL seguente:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01' -H "Authorization: Bearer <ACCESS TOKEN>" 
   ```

   ```HTTP
   GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachineScaleSets/<VMSS NAME>?api-version=2018-06-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |DESCRIZIONE  |
   |---------|---------|
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido. |
   
   Se sono state gestite identità assegnate alla macchina virtuale, sono elencate nella risposta nel valore `identity`. 
    
   Se ad esempio si dispone di identità gestite assegnate dall'utente `ID1` e `ID2` assegnate al set di scalabilità di macchine virtuali e si vuole solamente mantenere assegnato `ID1` e mantenere l'identità gestita assegnata dal sistema:

   **VERSIONE API 01/06/2018**

   Aggiungere `null` all'identità gestita assegnata dall'utente che si intende rimuovere:

   ```bash
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned, UserAssigned", "userAssignedIdentities":{"/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID2":null}}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |DESCRIZIONE  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido. | 

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
   curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01' -X PATCH -d '{"identity":{"type":"SystemAssigned,UserAssigned", "identityIds":["/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"]}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
   ```   

   ```HTTP
   PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2017-12-01 HTTP/1.1
   ```

   **Intestazioni della richiesta**

   |Intestazione della richiesta  |DESCRIZIONE  |
   |---------|---------|
   |*Content-Type*     | Richiesto. Impostare su `application/json`.        |
   |*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido. | 

   **Corpo della richiesta**

   ```JSON
    {
       "identity":{
          "type":"SystemAssigned,UserAssigned",
          "identityIds":[
             "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/ID1"
          ]
       }
    }
   ```

Se il set di scalabilità di macchine virtuali ha identità gestite sia assegnate dal sistema che assegnate dall'utente, è possibile rimuovere tutte le identità gestite assegnate dall'utente iniziando a usare solo identità assegnate dal sistema, usando il comando seguente:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"SystemAssigned"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Intestazioni della richiesta**

|Intestazione della richiesta  |DESCRIZIONE  |
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
    
Se il set di scalabilità di macchine virtuali presenta solo identità gestite assegnate dall'utente e si intende rimuoverle tutte, usare il comando seguente:

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01' -X PATCH -d '{"identity":{"type":"None"}}' -H "Content-Type: application/json" -H Authorization:"Bearer <ACCESS TOKEN>"
```

```HTTP
PATCH https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/myVMSS?api-version=2018-06-01 HTTP/1.1
```

**Intestazioni della richiesta**

|Intestazione della richiesta  |DESCRIZIONE  |
|---------|---------|
|*Content-Type*     | Richiesto. Impostare su `application/json`.        |
|*autorizzazioni*     | Richiesto. Impostare su un token di accesso `Bearer` valido. | 

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

- [Creare, elencare ed eliminare un'identità gestita assegnata dall'utente mediante chiamate dell'API REST](how-to-manage-ua-identity-rest.md)
