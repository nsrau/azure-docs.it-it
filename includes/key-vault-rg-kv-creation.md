---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 07/20/2020
ms.author: msmbaldwin
ms.openlocfilehash: 2b5533da1e81cf37dfba47bf84bd05f083d62dea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87013112"
---
In questo argomento di avvio rapido si usa un'istanza creata in precedenza di Azure Key Vault. È possibile creare un insieme di credenziali delle chiavi seguendo la procedura descritta negli argomenti di avvio rapido per l'[interfaccia della riga di comando di Azure](/azure/key-vault/general/quick-create-cli), [Azure PowerShell](/azure/key-vault/general/quick-create-powershell) o il [portale di Azure](/azure/key-vault/general/quick-create-portal). 

In alternativa, è possibile eseguire semplicemente i seguenti comandi dell'interfaccia della riga di comando di Azure o di Azure PowerShell.

> [!Important]
> Ogni insieme di credenziali delle chiavi deve avere un nome univoco. Negli esempi seguenti sostituire <your-unique-keyvault-name> con il nome dell'insieme di credenziali delle chiavi in uso.

```azurecli
az group create --name "myResourceGroup" -l "EastUS"

az keyvault create --name <your-unique-keyvault-name> -g "myResourceGroup"
```

```azurepowershell
New-AzResourceGroup -Name myResourceGroup -Location EastUS

New-AzKeyVault -Name <your-unique-keyvault-name> -ResourceGroupName "myResourceGroup" -Location "EastUS"
```