---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: ccb69b4f0dc6090cc96849b09f7830c05aa77b73
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026607"
---
In questo argomento di avvio rapido si usa un'istanza creata in precedenza di Azure Key Vault. È possibile creare un insieme di credenziali delle chiavi seguendo la procedura descritta negli argomenti di avvio rapido per l'[interfaccia della riga di comando di Azure](../articles/key-vault/general/quick-create-cli.md), [Azure PowerShell](../articles/key-vault/general/quick-create-powershell.md) o il [portale di Azure](../articles/key-vault/general/quick-create-portal.md). 

In alternativa, è possibile eseguire semplicemente i seguenti comandi dell'interfaccia della riga di comando di Azure o di Azure PowerShell.

> [!Important]
> Ogni insieme di credenziali delle chiavi deve avere un nome univoco. Negli esempi seguenti sostituire <your-unique-keyvault-name> con il nome dell'insieme di credenziali delle chiavi in uso.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name "<your-unique-keyvault-name>" -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "EastUS"
```