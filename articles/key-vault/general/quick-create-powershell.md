---
title: Avvio rapido - Creare un'istanza di Azure Key Vault con Azure PowerShell
description: Argomento di avvio rapido che mostra come creare un'istanza di Azure Key Vault con Azure PowerShell
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: quickstart
ms.date: 11/08/2019
ms.author: mbaldwin
ms.openlocfilehash: ace6d21d2928eac1fb91903c5e395f1e3479a3ff
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/07/2020
ms.locfileid: "91823448"
---
# <a name="quickstart-create-a-key-vault-using-powershell"></a>Avvio rapido: Creare un insieme di credenziali delle chiavi con PowerShell

Azure Key Vault è un servizio cloud che offre uno spazio di archiviazione protetto per [chiavi](../keys/index.yml), [segreti](../secrets/index.yml) e [certificati](../certificates/index.yml). Per altre informazioni sul servizio Key Vault, vedere [Informazioni su Azure Key Vault](overview.md). Per altre informazioni sugli elementi che è possibile archiviare in un insieme di credenziali delle chiavi, vedere [Informazioni su chiavi, segreti e certificati](about-keys-secrets-certificates.md).

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

In questo argomento di avvio rapido viene creato un insieme di credenziali delle chiavi con [Azure PowerShell](/powershell/azure/). Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell 1.0.0 o versione successiva. Digitare `$PSVersionTable.PSVersion` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzAccount` per creare una connessione con Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

```azurepowershell-interactive
New-AzResourceGroup -Name 'myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Creare un insieme di credenziali delle chiavi nel gruppo di risorse generato nel passaggio precedente. Sarà necessario specificare alcune informazioni:

- Nome dell'insieme di credenziali delle chiavi: Stringa composta da 3 a 24 caratteri che può contenere solo numeri (0-9), lettere (a-z, A-Z) e trattini (-)

  > [!Important]
  > Ogni insieme di credenziali delle chiavi deve avere un nome univoco. Negli esempi seguenti sostituire <your-unique-keyvault-name> con il nome dell'insieme di credenziali delle chiavi in uso.

- Nome del gruppo di risorse: **myResourceGroup**.
- Posizione: **EastUS**.

```azurepowershell-interactive
New-AzKeyVault -Name "<your-unique-key-vault-name>" -ResourceGroupName "myResourceGroup" -Location "East US"
```

L'output di questo cmdlet mostra le proprietà dell'istanza di Key Vault appena creata. Prendere nota delle due proprietà elencate di seguito:

- **Vault Name**: Il nome specificato per il parametro --name precedente.
- **Vault URI** (URI dell'insieme di credenziali): nell'esempio è https://<your-unique-keyvault-name>.vault.azure.net/. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

A questo punto, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide introduttive ed esercitazioni della raccolta si basano su questa. Se si prevede di usare altre guide introduttive ed esercitazioni, è consigliabile non cancellare le risorse create.

Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) di Azure PowerShell per rimuovere il gruppo di risorse e tutte le risorse correlate.

```azurepowershell-interactive
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido è stata creata un'istanza di Key Vault in cui è stato archiviato un segreto. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](overview.md)
- Vedere le informazioni di riferimento sui [cmdlet di Azure PowerShell Key Vault](/powershell/module/az.keyvault/)
- Esaminare le [procedure consigliate per Azure Key Vault](best-practices.md)
