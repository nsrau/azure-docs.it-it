---
title: Creazione e configurazione di un insieme di credenziali delle chiavi per la crittografia dischi di Azure
description: Questo articolo illustra la procedura per creare e configurare un insieme di credenziali delle chiavi per l'uso con crittografia dischi di Azure
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 30fa6b910c0241621c2b2cdae9bb9a164f27cedb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81454544"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Creazione e configurazione di un insieme di credenziali delle chiavi per la crittografia dischi di Azure

Crittografia dischi di Azure usa Azure Key Vault per controllare e gestire le chiavi e i segreti di crittografia del disco.  Per altre informazioni sugli insiemi di credenziali delle chiavi, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md) e [Proteggere l'insieme di credenziali delle chiavi](../key-vault/general/secure-your-key-vault.md).

La creazione e la configurazione di un insieme di credenziali delle chiavi per l'uso con crittografia dischi di Azure prevede tre passaggi:

1. Creazione di un gruppo di risorse, se necessario.
2. Creazione di un insieme di credenziali delle chiavi. 
3. Impostazione di criteri di accesso avanzati di Key Vault.

Questi passaggi sono illustrati nelle guide introduttive seguenti:

È anche possibile generare o importare una chiave di crittografia della chiave (KEK).

## <a name="install-tools-and-connect-to-azure"></a>Installare gli strumenti e connettersi ad Azure

I passaggi descritti in questo articolo possono essere completati con l'interfaccia della riga di comando di [Azure](/cli/azure/), il [Azure PowerShell AZ module](/powershell/azure/overview)o l' [portale di Azure](https://portal.azure.com).

### <a name="connect-to-your-azure-account"></a>Connettersi all'account di Azure

Prima di usare l'interfaccia della riga di comando di Azure o Azure PowerShell, è necessario prima di tutto connettersi alla sottoscrizione di Azure. A tale scopo, [accedere con l'interfaccia](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)della riga di comando di Azure, [accedere con Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)o fornire le credenziali al portale di Azure quando richiesto.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Passaggi successivi

- [Informazioni su Crittografia dischi di Azure](disk-encryption-overview.md)
- [Crittografare set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure](disk-encryption-cli.md)
- [Crittografare set di scalabilità di macchine virtuali con Azure PowerShell](disk-encryption-powershell.md)
