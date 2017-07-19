---
title: 'Esempio di script dell''interfaccia della riga di comando di Azure: crittografare una VM Linux | Microsoft Docs'
description: 'Esempio di script dell''interfaccia della riga di comando di Azure: crittografare una VM Linux'
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/02/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 7948c99b7b60d77a927743c7869d74147634ddbf
ms.openlocfilehash: 9388bce04e37d049301521f808cd8494c327e335
ms.contentlocale: it-it
ms.lasthandoff: 06/20/2017

---

# <a name="encrypt-a-linux-virtual-machine-in-azure"></a>Crittografare una macchina virtuale Linux in Azure

Questo script crea un insieme di credenziali delle chiavi di Azure sicuro, le chiavi di crittografia, un'entità servizio di Azure Active Directory e una macchina virtuale (VM) Linux. La VM viene quindi crittografata usando la chiave di crittografia di Key Vault e le credenziali dell'entità servizio.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-azurecli-interactive[main](../../../cli_scripts/virtual-machine/encrypt-disks/encrypt_vm.sh "Crittografare i dischi delle VM")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```azurecli
az group delete --name myResourceGroup
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, un insieme di credenziali delle chiavi di Azure, un'entità servizio, la macchina virtuale e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [az group create](https://docs.microsoft.com/cli/azure/group#create) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [az keyvault create](https://docs.microsoft.com/cli/azure/keyvault#create) | Crea un insieme di credenziali delle chiavi di Azure per archiviare i dati protetti, ad esempio le chiavi di crittografia. |
| [az keyvault key create](https://docs.microsoft.com/cli/azure/keyvault/key#create) | Crea una chiave di crittografia in Key Vault. |
| [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp#create-for-rbac) | Crea un'entità servizio di Azure Active Directory per autenticare in modo sicuro e controllare l'accesso alle chiavi di crittografia. |
| [az keyvault set-policy](https://docs.microsoft.com/cli/azure/keyvault#set-policy) | Imposta le autorizzazioni in Key Vault per concedere l'accesso dell'entità servizio alle chiavi di crittografia. |
| [az vm create](https://docs.microsoft.com/cli/azure/vm#create) | Consente di creare la macchina virtuale e la connette alla scheda di rete, alla rete virtuale, alla subnet e al gruppo di sicurezza di rete. Questo comando specifica anche l'immagine della macchina virtuale da usare e le credenziali di amministrazione.  |
| [az vm encryption enable](https://docs.microsoft.com/cli/azure/vm/encryption#enable) | Abilita la crittografia in una VM usando le credenziali dell'entità servizio e la chiave di crittografia. |
| [az vm encryption show](https://docs.microsoft.com/cli/azure/vm/encryption#show) | Mostra lo stato del processo di crittografia della VM. |
| [az group delete](https://docs.microsoft.com/cli/azure/vm/extension#set) | Consente di eliminare un gruppo di risorse incluse tutte le risorse annidate. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [documentazione sull'interfaccia della riga di comando di Azure](https://docs.microsoft.com/cli/azure/overview).

Altri esempi di script dell'interfaccia della riga di comando della macchina virtuale sono reperibili nella [documentazione della VM Linux di Azure](../linux/cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

