---
title: Creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure
description: Questo articolo illustra la procedura per creare e configurare un insieme di credenziali delle chiavi per l'uso con crittografia dischi di Azure
ms.service: virtual-machines
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 7c3d70610f8b26af17c5117896f4654a175473d2
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245240"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure

Crittografia dischi di Azure usa Azure Key Vault per controllare e gestire le chiavi e i segreti di crittografia del disco.  Per altre informazioni sugli insiemi di credenziali delle chiavi, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](../../key-vault/key-vault-get-started.md) e [Proteggere l'insieme di credenziali delle chiavi](../../key-vault/key-vault-secure-your-key-vault.md). 

> [!WARNING]
> - Se in precedenza è stato usato crittografia dischi di Azure con Azure AD per crittografare una macchina virtuale, è necessario continuare a usare questa opzione per crittografare la macchina virtuale. Per informazioni dettagliate, vedere [creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure con Azure ad (versione precedente)](disk-encryption-key-vault-aad.md) .

La creazione e la configurazione di un insieme di credenziali delle chiavi per l'uso con crittografia dischi di Azure prevede tre passaggi:

1. Creazione di un gruppo di risorse, se necessario.
2. Creazione di un insieme di credenziali delle chiavi. 
3. Impostazione di criteri di accesso avanzati di Key Vault.

Questi passaggi sono illustrati nelle guide introduttive seguenti:

- [Creare e crittografare una macchina virtuale Windows con l'interfaccia della riga di comando di Azure](disk-encryption-cli-quickstart.md)
- [Creare e crittografare una macchina virtuale Windows con Azure PowerShell](disk-encryption-cli-quickstart.md)

È anche possibile generare o importare una chiave di crittografia della chiave (KEK).

> [!Note]
> I passaggi descritti in questo articolo sono automatizzati nello script dell'interfaccia della riga di comando dei [prerequisiti di crittografia dischi di Azure](https://github.com/ejarvi/ade-cli-getting-started) e nello script di PowerShell per i [prerequisiti di crittografia dischi di Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).

## <a name="install-tools-and-connect-to-azure"></a>Installare gli strumenti e connettersi ad Azure

I passaggi descritti in questo articolo possono essere completati con l'interfaccia della riga di comando di [Azure](/cli/azure/), il [Azure PowerShell AZ module](/powershell/azure/overview)o l' [portale di Azure](https://portal.azure.com).

Mentre il portale è accessibile tramite il browser, l'interfaccia della riga di comando di Azure e Azure PowerShell richiedono l'installazione locale; per informazioni dettagliate, vedere [crittografia dischi di Azure per Windows: installare gli strumenti](disk-encryption-windows.md#install-tools-and-connect-to-azure) .

### <a name="connect-to-your-azure-account"></a>Connettersi all'account di Azure

Prima di usare l'interfaccia della riga di comando di Azure o Azure PowerShell, è necessario prima di tutto connettersi alla sottoscrizione di Azure. A tale scopo, [accedere con l'interfaccia](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)della riga di comando di Azure, [accedere con Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)o fornire le credenziali al portale di Azure quando richiesto.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Passaggi successivi

- [Script dell'interfaccia della riga di comando dei prerequisiti di crittografia dischi di Azure](https://github.com/ejarvi/ade-cli-getting-started)
- [Script di PowerShell per prerequisiti di crittografia dischi di Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Informazioni [sugli scenari di crittografia dischi di Azure nelle macchine virtuali Windows](disk-encryption-windows.md)
- Informazioni su come [risolvere i problemi di crittografia dischi di Azure](disk-encryption-troubleshooting.md)
- Leggere gli [script di esempio di crittografia dischi di Azure](disk-encryption-sample-scripts.md)
