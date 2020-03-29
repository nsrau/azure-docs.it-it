---
title: Creare e gestire macchine virtuali in DevTest Labs con interfaccia della riga di comando di AzureCreate and manage virtual machines in DevTest Labs with Azure CLI
description: Informazioni su come usare Azure DevTest Labs per creare e gestire le macchine virtuali con l'interfaccia della riga di comando di Azure
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: d3cd104e36cb407e9b1b833335869cac2c69d0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167060"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Creare e gestire macchine virtuali con DevTest Labs tramite l'interfaccia della riga di comando di Azure
Questa guida introduttiva ti guiderà attraverso la creazione, l'avvio, la connessione, l'aggiornamento e la pulizia di un computer di sviluppo nel tuo laboratorio. 

Prima di iniziare:

* Se il lab non è stato creato, le istruzioni sono disponibili [qui](devtest-lab-create-lab.md).

* [Installare l'interfaccia della riga di comando di Azure.](/cli/azure/install-azure-cli) Per iniziare, eseguire az login per creare una connessione con Azure. 

## <a name="create-and-verify-the-virtual-machine"></a>Creare e verificare la macchina virtuale 
Prima di eseguire i comandi correlati di DevTest `az account set` Labs, impostare il contesto di Azure appropriato usando il comando:Before you execute DevTest Labs related commands, set the appropriate Azure context by using the command:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

Il comando per creare una `az lab vm create`macchina virtuale è: . Il gruppo di risorse per il lab, il nome del lab e il nome della macchina virtuale sono tutti obbligatori. Il resto degli argomenti cambia a seconda del tipo di macchina virtuale.

Il comando seguente crea un'immagine basata su Windows da Azure Market Place.The following command creates a Windows-based image from Azure Market Place. Il nome dell'immagine è lo stesso che si vedrebbe quando si crea una macchina virtuale usando il portale di Azure.The name of the image is the same as you would see when creating a virtual machine using the Azure portal. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

Il comando seguente crea una macchina virtuale basata su un'immagine personalizzata disponibile nel lab:The following command creates a virtual machine based on a custom image available in the lab:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

**L'argomento di tipo immagine** è stato modificato da **raccolta** a **personalizzato.** Il nome dell'immagine corrisponde a quello visualizzato se si crea la macchina virtuale nel portale di Azure.The name of the image matches what you see if you were create the virtual machine in the Azure portal.

Il comando seguente crea una macchina virtuale da un'immagine del marketplace con autenticazione ssh:The following command creates a VM from a marketplace image with ssh authentication:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

È inoltre possibile creare macchine virtuali in base alle formule impostando il parametro **image-type** su **formula**. Se è necessario scegliere una rete virtuale specifica per la macchina virtuale, usare i parametri **vnet-name** e **subnet.** Per ulteriori informazioni, vedere [az lab vm create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Verificare che la VM sia disponibile.
Usare `az lab vm show` il comando per verificare che la macchina virtuale sia disponibile prima di avviarla e connettersi. 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>Avviare e connettersi alla macchina virtuale
Il comando di esempio seguente avvia una macchina virtuale:The following example command starts a VM:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Connettersi a una VM: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) o [Desktop remoto](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>Aggiornare la macchina virtuale
Il comando di esempio seguente applica gli elementi a una macchina virtuale:The following sample command applies artifacts to a VM:

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

### <a name="list-artifacts-available-in-the-lab"></a>Elencare gli elementi disponibili nel labList artifacts available in the lab

Per elencare gli elementi disponibili in una macchina virtuale in un lab, eseguire i comandi seguenti.

**Cloud Shell - PowerShell**: notare\`l'uso del backtick ( ) prima del segno di $expand (ad esempio '$expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell - Bash**: si\\noti l'uso del carattere barra ( ) davanti al carattere s nel comando. 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

Output di esempio: 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>Arrestare ed eliminare la macchina virtuale    
Il comando di esempio seguente arresta una macchina virtuale.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Eliminare una VM.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi
Vedere il contenuto seguente: [documentazione dell'interfaccia della](/cli/azure/lab?view=azure-cli-latest)riga di comando di Azure per Azure DevTest Labs. 
