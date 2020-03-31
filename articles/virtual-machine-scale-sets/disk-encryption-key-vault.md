---
title: Creazione e configurazione di un insieme di credenziali delle chiavi per la crittografia dischi di Azure
description: Questo articolo illustra i passaggi per la creazione e la configurazione di un insieme di credenziali delle chiavi da usare con Crittografia disco di AzureThis article provides steps for creating and configuring a key vault for use with Azure Disk Encryption
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
author: msmbaldwin
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: eec5b42da709ab5e79da42f11600f6ffc81d247e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76279008"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Creazione e configurazione di un insieme di credenziali delle chiavi per la crittografia dischi di Azure

Crittografia disco di Azure usa Archiviazione chiavi di Azure per controllare e gestire le chiavi e i segreti di crittografia del disco.  Per altre informazioni sugli insiemi di credenziali delle chiavi, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](../key-vault/key-vault-get-started.md) e [Proteggere l'insieme di credenziali delle chiavi](../key-vault/key-vault-secure-your-key-vault.md).

La creazione e la configurazione di un insieme di credenziali delle chiavi per l'utilizzo con Crittografia disco di Azure prevede tre passaggi:Creating and configuring a key vault for use with Azure Disk Encryption involves three steps:

1. Creazione di un gruppo di risorse, se necessario.
2. Creazione di un insieme di credenziali delle chiavi. 
3. Impostazione dei criteri di accesso avanzato dell'insieme di credenziali delle chiavi.

Questi passaggi sono illustrati nelle guide introduttive seguenti:These steps are illustrated in the following quickstarts:

È inoltre possibile, se lo si desidera, generare o importare una chiave di crittografia a chiave (KEK).

## <a name="install-tools-and-connect-to-azure"></a>Installare gli strumenti e connettersi ad AzureInstall tools and connect to Azure

I passaggi descritti in questo articolo possono essere completati con l'interfaccia della riga di comando di [Azure,](/cli/azure/)il [modulo Azure PowerShell Az](/powershell/azure/overview)o il portale di [Azure.](https://portal.azure.com)

### <a name="connect-to-your-azure-account"></a>Connettersi all'account di Azure

Prima di usare l'interfaccia della riga di comando di Azure o Azure PowerShell, è necessario connettersi alla sottoscrizione di Azure.Before using the Azure CLI or Azure PowerShell, you must first connect to your Azure subscription. A tale [scopo, accedere con l'interfaccia della riga](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)di comando di Azure, [accedere con Azure Powershell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)o fornire le credenziali al portale di Azure quando richiesto.

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
