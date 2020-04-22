---
title: 'Avvio rapido: Impostare e recuperare una chiave da Azure Key Vault'
description: Avvio rapido che illustra come impostare e recuperare una chiave da Azure Key Vault con Azure PowerShell
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.date: 03/30/2020
ms.author: mbaldwin
ms.openlocfilehash: c407c10327a80de6b3df18a3db3978468c9f8da0
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81420275"
---
# <a name="quickstart-set-and-retrieve-a-key-from-azure-key-vault-using-azure-powershell"></a>Avvio rapido: Impostare e recuperare una chiave da Azure Key Vault con Azure PowerShell

In questa guida di avvio rapido viene creato un insieme di credenziali delle chiavi in Azure Key Vault con Azure PowerShell. Azure Key Vault è un servizio cloud che funziona come archivio protetto dei segreti. È possibile archiviare in modo sicuro chiavi, password, certificati e altri segreti. Per altre informazioni su Key Vault, vedere la [panoramica](../general/overview.md). Azure PowerShell viene usato per creare e gestire le risorse di Azure con comandi o script. Successivamente verrà archiviata una chiave.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell 1.0.0 o versione successiva. Digitare `$PSVersionTable.PSVersion` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzAccount` per creare una connessione con Azure.

```azurepowershell-interactive
Login-AzAccount
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

```azurepowershell-interactive
New-AzResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Creata quindi un'istanza di Key Vault. Per questo passaggio sono necessarie alcune informazioni:

Anche se si usa "Contoso KeyVault2" come nome dell'istanza di Key Vault in questa guida di avvio rapido, è necessario usare un nome univoco.

- **Nome dell'insieme di credenziali** Contoso-Vault2.
- **Nome del gruppo di risorse** ContosoResourceGroup.
- **Posizione**: Stati Uniti orientali.

```azurepowershell-interactive
New-AzKeyVault -Name 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

L'output di questo cmdlet mostra le proprietà dell'istanza di Key Vault appena creata. Prendere nota delle due proprietà elencate di seguito:

* **Vault Name**: nell'esempio corrisponde a **Contoso-Vault2**. Questo nome verrà usato per altri cmdlet di insieme di credenziali delle chiavi.
* **Vault URI** (URI dell'insieme di credenziali): in questo esempio corrisponde a https://Contoso-Vault2.vault.azure.net/. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

Dopo aver creato l'insieme di credenziali, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

![Output al termine del comando di creazione dell'insieme di credenziali delle chiavi](../media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="add-a-key-to-key-vault"></a>Aggiungere una chiave a Key Vault

Per aggiungere una chiave a Key Vault, sono sufficienti un paio di passaggi aggiuntivi. Questa chiave può essere usata da un'applicazione. 

Digitare i comandi seguenti per creare una chiave denominata **ExampleKey**:

```azurepowershell-interactive
Add-AzKeyVaultKey -VaultName 'Contoso-Vault2' -Name 'ExampleKey' -Destination 'Software'
```

È ora possibile fare riferimento a questa chiave aggiunta ad Azure Key Vault usando il relativo URI. Usare **https://Contoso-Vault2.vault.azure.net/keys/ExampleKey** per ottenere la versione corrente. 

Per visualizzare la chiave archiviata in precedenza:

```azurepowershell-interactive
Get-AzKeyVaultKey -VaultName 'Contoso-Vault2' -KeyName 'ExampleKey'
```

A questo punto è stata creata un'istanza di Key Vault nella quale è stata archiviata e recuperata una chiave.

## <a name="clean-up-resources"></a>Pulire le risorse

Altre guide di avvio rapido ed esercitazioni della raccolta si basano su questa. Se si prevede di usare le guide di avvio rapido e le esercitazioni successive, è consigliabile non cancellare le risorse create.
Quando non servono più, è possibile usare il comando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse correlate. È possibile eliminare le risorse in questo modo:

```azurepowershell-interactive
Remove-AzResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida di avvio rapido è stata creata un'istanza di Key Vault in cui è stato archiviato un certificato. Per altre informazioni sul servizio Key Vault e su come integrarlo nelle applicazioni, continuare con gli articoli seguenti.

- Leggere una [panoramica di Azure Key Vault](../general/overview.md)
- Vedere le informazioni di riferimento sui [cmdlet di Azure PowerShell Key Vault](/powershell/module/az.keyvault/)
- Esaminare le [procedure consigliate per Azure Key Vault](../general/best-practices.md)
