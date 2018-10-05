---
title: Creare una macchina virtuale Linux con l'API REST di Azure | Microsoft Docs
description: Informazioni su come creare una macchina virtuale Linux in Azure che usa l'autenticazione SSH e Managed Disks tramite l'API REST di Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
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
ms.author: cynthn
ms.openlocfilehash: 3eeaee9bc6320231f10aa85227e2f43756181806
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/28/2018
ms.locfileid: "47433481"
---
# <a name="create-a-linux-virtual-machine-that-uses-ssh-authentication-with-the-rest-api"></a>Creare una macchina virtuale Linux che usa l'autenticazione SSH con l'API REST

Una macchina virtuale Linux (VM) in Azure è costituita da varie risorse come dischi e interfacce di rete ed è in grado di definire parametri come posizione, dimensioni e immagine del sistema operativo, nonché le impostazioni di autenticazione.

È possibile creare una macchina virtuale Linux tramite il portale di Azure, l'interfaccia della riga di comando di Azure 2.0, diversi SDK Azure, modelli di Azure Resource Manager e altri strumenti di terze parti come Ansible o Terraform. Tutti questi strumenti utilizzano le API REST per creare la macchina virtuale Linux.

Questo articolo illustra come usare l'API REST per creare una macchina virtuale con Linux Ubuntu 18.04-LTS in esecuzione che usa i dischi gestiti e l'autenticazione SSH.

## <a name="before-you-start"></a>Prima di iniziare

Prima di creare e inviare la richiesta, è necessario:

* Il `{subscription-id}` per l’abbonamento
  * Se sono disponibili più abbonamenti, vedere [Uso di più sottoscrizioni](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest#working-with-multiple-subscriptions)
* Un `{resourceGroupName}` creato in anticipo
* Un’[interfaccia di rete virtuale](../../virtual-network/virtual-network-network-interface.md) nello stesso gruppo di risorse
* Una coppia di chiavi SSH (è possibile [generarne una nuova](mac-create-ssh-keys.md) se non se ne dispone di una)

## <a name="request-basics"></a>Nozioni di base della richiesta

Per creare o aggiornare una macchina virtuale, usare l'operazione *PUT* seguente:

``` http
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}?api-version=2017-12-01
```

Oltre ai parametri `{subscription-id}` e `{resourceGroupName}`, è necessario specificare il parametro `{vmName}` (`api-version` è opzionale, ma questo articolo è stato testato con `api-version=2017-12-01`)

Gli argomenti seguenti sono obbligatori:

| Intestazione della richiesta   | DESCRIZIONE |
|------------------|-----------------|
| *Content-Type:*  | Richiesto. Impostare su `application/json`. |
| *Authorization:* | Richiesto. Impostare su un [token di accesso](https://docs.microsoft.com/rest/api/azure/#authorization-code-grant-interactive-clients) `Bearer` valido. |

Per informazioni generali sul lavoro con le operazioni con API REST, vedere [Componenti di una richiesta/risposta dell'API REST](/rest/api/azure/#components-of-a-rest-api-requestresponse).

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

Un esempio di corpo di richiesta è riportato di seguito. Assicurarsi di specificare il nome della macchina virtuale nei parametri `{computerName}` e `{name}`, il nome dell'interfaccia di rete creata in `networkInterfaces`, il nome utente in `adminUsername` e `path`, e la parte *pubblica* della coppia di chiavi SSH (che si trova, per esempio, in `~/.ssh/id_rsa.pub`) in `keyData`. Altri parametri che possono essere modificati includono `location` e `vmSize`.  

```json
{
  "location": "eastus",
  "name": "{vmName}",
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
      "computerName": "{vmName}",
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
          "id": "/subscriptions/{subscription-id}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkInterfaces/{existing-nic-name}",
          "properties": {
            "primary": true
          }
        }
      ]
    }
  }
}
```

Per un elenco completo delle definizioni disponibili nel corpo della richiesta, vedere le [definizioni per il corpo della richiesta di creazione o aggiornamento macchine virtuali](/rest/api/compute/virtualmachines/createorupdate#definitions).

## <a name="sending-the-request"></a>Invio della richiesta

È possibile utilizzare il proprio client preferito per l'invio di questa richiesta HTTP. Si può anche usare uno [strumento integrato nel browser](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate) facendo clic sul pulsante **Prova**.

### <a name="responses"></a>Risposte

Esistono due risposte che indicano l'esito positivo dell'operazione di creazione o aggiornamento di una macchina virtuale:

| NOME        | type                                                                              | DESCRIZIONE |
|-------------|-----------------------------------------------------------------------------------|-------------|
| 200 - OK      | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | OK          |
| 201 Creato | [VirtualMachine](/rest/api/compute/virtualmachines/createorupdate#virtualmachine) | Data di creazione     |

Una risposta condensata *201 Creato* dall'esempio di corpo della richiesta precedente per la creazione di una macchina virtuale mostra che è stato assegnato un *vmId* e che *provisioningState* è *Creating*:

```json
{
    "vmId": "e0de9b84-a506-4b95-9623-00a425d05c90",
    "provisioningState": "Creating"
}
```

Per altre informazioni sulle risposte dell'API REST, vedere [Process the response message](/rest/api/azure/#process-the-response-message) (Elaborare il messaggio di risposta).

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle API REST di Azure o su altri strumenti di gestione, ad esempio l'interfaccia della riga di comando di Azure o Azure PowerShell, vedere gli argomenti seguenti:

- [API REST del provider Calcolo di Azure](/rest/api/compute/)
- [Introduzione all'API REST di Azure](/rest/api/azure/)
- [Interfaccia della riga di comando di Azure](/cli/azure/)
- [Modulo di Azure PowerShell](/powershell/azure/overview)
