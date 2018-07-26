---
title: Guida introduttiva di Azure - Creare un'istanza di Key Vault con PowerShell | Microsoft Docs
description: ''
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 1126f665-2e6c-4cca-897e-7d61842e8334
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 05/10/2018
ms.author: barclayn
ms.openlocfilehash: 4acd8286cb8635f9a76815c936328a7c441e3115
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187149"
---
# <a name="quickstart-create-an-azure-key-vault-using-powershell"></a>Guida introduttiva: Creare un'istanza di Key Vault usando PowerShell

Azure Key Vault è un servizio cloud che funziona come archivio protetto dei segreti. È possibile archiviare in modo sicuro chiavi, password, certificati e altri segreti. Per altre informazioni su Key Vault, vedere la [panoramica](key-vault-overview.md). In questa guida introduttiva si usa PowerShell per creare un'istanza di Key Vault. Verrà quindi archiviato un segreto nell'insieme di credenziali appena creato.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Se si sceglie di installare e usare PowerShell in locale, per questa esercitazione è necessario il modulo Azure PowerShell 5.1.1 o versione successiva. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Login-AzureRmAccount` per creare una connessione con Azure.

```azurepowershell
Login-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse

Creare un gruppo di risorse di Azure con [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. 

```azurepowershell
New-AzureRmResourceGroup -Name ContosoResourceGroup -Location EastUS
```

## <a name="create-a-key-vault"></a>Creare un insieme di credenziali delle chiavi

Creata quindi un'istanza di Key Vault. Per questo passaggio sono necessarie alcune informazioni:

Anche se si usa "Contoso KeyVault2" come nome dell'istanza di Key Vault in questa guida introduttiva, è necessario usare un nome univoco.

- **Nome dell'insieme di credenziali** Contoso-Vault2.
- **Nome del gruppo di risorse** ContosoResourceGroup.
- **Posizione**: Stati Uniti orientali.

```azurepowershell
New-AzureRmKeyVault -VaultName 'Contoso-Vault2' -ResourceGroupName 'ContosoResourceGroup' -Location 'East US'
```

L'output di questo cmdlet mostra le proprietà dell'istanza di Key Vault appena creata. Prendere nota delle due proprietà elencate di seguito:

* **Nome dell'insieme di credenziali**: nell'esempio corrisponde a **Contoso-Vault2**. Questo nome verrà usato per altri cmdlet di insieme di credenziali delle chiavi.
* **Vault URI** (URI dell'insieme di credenziali): in questo esempio corrisponde a https://contosokeyvault.vault.azure.net/. Le applicazioni che usano l'insieme di credenziali tramite l'API REST devono usare questo URI.

Dopo aver creato l'insieme di credenziali, l'account Azure è l'unico autorizzato a eseguire qualsiasi operazione su questo nuovo insieme di credenziali.

![Output al termine del comando di creazione dell'insieme di credenziali delle chiavi](./media/quick-create-powershell/output-after-creating-keyvault.png)

## <a name="adding-a-secret-to-key-vault"></a>Aggiunta di un segreto all'istanza di Key Vault

Per aggiungere un segreto all'insieme di credenziali, sono sufficienti un paio di passaggi. In questo caso si aggiunge una password che può essere usata da un'applicazione. La password è denominata **ExamplePassword** e al suo interno viene archiviato il valore di **Pa$$w0rd**.

Convertire prima di tutto il valore di Pa$$w0rd in una stringa sicura digitando:

```azurepowershell
$secretvalue = ConvertTo-SecureString 'Pa$$w0rd' -AsPlainText -Force
```

Digitare i comandi di PowerShell seguenti per creare in Key Vault un segreto denominato **ExamplePassword** con il valore **Pa$$w0rd**:

```azurepowershell
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'ExamplePassword' -SecretValue $secretvalue
```

Per visualizzare il valore contenuto nel segreto come testo normale:

```azurepowershell
(Get-AzureKeyVaultSecret -vaultName "Contosokeyvault" -name "ExamplePassword").SecretValueText
```

A questo punto è stata creata un'istanza di Key Vault nella quale è stato archiviato e recuperato un segreto.

## <a name="clean-up-resources"></a>Pulire le risorse

 Altre guide introduttive ed esercitazioni della raccolta si basano su questa. Se si prevede di usare altre guide introduttive ed esercitazioni, è consigliabile non cancellare le risorse create.

Quando non sono più necessari, è possibile usare il comando [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse, l'istanza di Key Vault e tutte le risorse correlate.

```azurepowershell
Remove-AzureRmResourceGroup -Name ContosoResourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stata creata un'istanza di Key Vault nella quale è stata archiviata una chiave software. Per altre informazioni su Key Vault e su come usarlo con le applicazioni, passare all'esercitazione per le applicazioni Web che usano Key Vault.

> [!div class="nextstepaction"]
> Per informazioni su come leggere un segreto da Key Vault in un'applicazione Web usando le identità del servizio gestito, proseguire con l'esercitazione seguente [Configurare un'applicazione Web di Azure per leggere un segreto da Key Vault](tutorial-web-application-keyvault.md).
