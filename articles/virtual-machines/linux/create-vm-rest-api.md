---
title: Creare una macchina virtuale Linux con l'API REST di Azure | Microsoft Docs
description: Informazioni su come creare una macchina virtuale Linux in Azure che usa l'autenticazione SSH e Managed Disks tramite l'API REST di Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/05/2018
ms.author: iainfou
ms.openlocfilehash: e3f41bea26e9a5ff45b31ae9d9a2e5955317ad7a
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2018
ms.locfileid: "34826223"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Creare una macchina virtuale Linux che usa l'autenticazione SSH con l'API REST

Una macchina virtuale (VM) in Azure è definita da vari parametri, ad esempio percorso, dimensioni hardware, immagine del sistema operativo e credenziali di accesso. Questo articolo illustra come usare l'API REST per creare una macchina virtuale Linux che usa l'autenticazione SSH.

Per creare o aggiornare una macchina virtuale, usare l'operazione *PUT* seguente:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

## <a name="create-a-request"></a>Creare una richiesta

Per creare la richiesta *PUT* è necessario il `{subscription-id}`. Se sono disponibili più sottoscrizioni, vedere [Uso di più sottoscrizioni](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions). Si definiscono `{resourceGroupName}` e `{vmName}` per le risorse, insieme al parametro `api-version`. Questo articolo usa `api-version=2017-12-01`.

Gli argomenti seguenti sono obbligatori:

| Intestazione della richiesta   | DESCRIZIONE |
|------------------|-----------------|
| *Content-Type:*  | Richiesto. Impostare su `application/json`. |
| *Authorization:* | Richiesto. Impostare su un [token di accesso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer` valido. |

Per altre informazioni su come creare la richiesta, vedere [Componenti di una richiesta/risposta dell'API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

## <a name="create-the-request-body"></a>Creare il corpo della richiesta

Per compilare un corpo della richiesta vengono usate le definizioni comuni seguenti:

| NOME                       | Obbligatoria | type                                                                                | DESCRIZIONE  |
|----------------------------|----------|-------------------------------------------------------------------------------------|--------------|
| location                   | True      | stringa                                                                              | Percorso della risorsa. |
| name                       |          | stringa                                                                              | Nome della macchina virtuale. |
| properties.hardwareProfile |          | [HardwareProfile](/rest/api/compute/virtualmachines/createorupdate#hardwareprofile) | Specifica le impostazioni hardware per la macchina virtuale. |
| properties.storageProfile  |          | [StorageProfile](/rest/api/compute/virtualmachines/createorupdate#storageprofile)   | Specifica le impostazioni di archiviazione per i dischi della macchina virtuale. |
| properties.osProfile       |          | [OSProfile](/rest/api/compute/virtualmachines/createorupdate#osprofile)             | Specifica le impostazioni del sistema operativo per la macchina virtuale. |
| properties.networkProfile  |          | [NetworkProfile](/rest/api/compute/virtualmachines/createorupdate#networkprofile)   | Specifica le interfacce di rete della macchina virtuale. |

Per un elenco completo delle definizioni disponibili nel corpo della richiesta, vedere le [definizioni per il corpo della richiesta di creazione o aggiornamento macchine virtuali](/rest/api/compute/virtualmachines/createorupdate#definitions).

### <a name="example-request-body"></a>Esempio di corpo della richiesta

L'esempio di corpo della richiesta seguente definisce un'immagine Ubuntu 18.04-LTS che usa Managed Disks Premium. Viene usata l'autenticazione con chiave pubblica SSH e la VM usa una scheda di interfaccia di rete virtuale esistente [creata in precedenza](../../virtual-network/virtual-network-network-interface.md). Specificare la chiave pubblica SSH nel campo *osProfile.linuxConfiguration.ssh.publicKeys.keyData*. Se necessario, è possibile [generare una coppia di chiavi SSH](mac-create-ssh-keys.md).

```json
{
  "location": "eastus",
  "properties": {
    "hardwareProfile": {
      "vmSize": "Standard_DS1_v2"
    },
    "storageProfile": {
      "imageReference": {
        "sku": "18.04-LTS",
        "publisher": "Canonical",
        "version": "latest",
        "offer": "UbuntuServer"
      },
      "osDisk": {
        "caching": "ReadWrite",
        "managedDisk": {
          "storageAccountType": "Premium_LRS"
        },
        "name": "myVMosdisk",
        "createOption": "FromImage"
      }
    },
    "osProfile": {
      "adminUsername": "{your-username}",
      "computerName": "myVM",
      "linuxConfiguration": {
        "ssh": {
          "publicKeys": [
            {
              "path": "/home/{your-username}/.ssh/authorized_keys",
              "keyData": "ssh-rsa AAAAB3NzaC1{snip}mf69/J1"
            }
          ]
        },
        "disablePasswordAuthentication": true
      }
    },
    "networkProfile": {
      "networkInterfaces": [
        {
          "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  },
  "name": "myVM"
}
```

## <a name="responses"></a>Risposte

Esistono due risposte che indicano l'esito positivo dell'operazione di creazione o aggiornamento di una macchina virtuale:

| NOME        | type                                                                              | DESCRIZIONE |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 - OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Creato | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Data di creazione     |

Per altre informazioni sulle risposte dell'API REST, vedere [Process the response message](/rest/api/azure/#process-the-response-message) (Elaborare il messaggio di risposta).

### <a name="example-response"></a>Risposta di esempio

Una risposta condensata *201 Creato* dall'esempio di corpo della richiesta precedente per la creazione di una macchina virtuale mostra che è stato assegnato un *vmId* e che *provisioningState* è *Creating*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle API REST di Azure o su altri strumenti di gestione, ad esempio l'interfaccia della riga di comando di Azure 2.0 o Azure PowerShell, vedere gli argomenti seguenti:

- [API REST del provider Calcolo di Azure](/rest/api/compute/)
- [Introduzione all'API REST di Azure](/rest/api/azure/)
- [Interfaccia della riga di comando di Azure 2.0](/cli/azure/)
- [Modulo di Azure PowerShell](/powershell/azure/overview)
