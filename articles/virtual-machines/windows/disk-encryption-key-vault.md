---
title: Creazione e configurazione di un insieme di credenziali delle chiavi per la crittografia dischi di Azure
description: Questo articolo illustra i passaggi per la creazione e la configurazione di un insieme di credenziali delle chiavi da usare con Crittografia disco di AzureThis article provides steps for creating and configuring a key vault for use with Azure Disk Encryption
ms.service: virtual-machines
ms.subservice: security
ms.topic: article
author: msmbaldwin
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: f1317989434cca9375c612c8af0525323c6dda3c
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081660"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption"></a>Creazione e configurazione di un insieme di credenziali delle chiavi per la crittografia dischi di Azure

Crittografia disco di Azure usa Archiviazione chiavi di Azure per controllare e gestire le chiavi e i segreti di crittografia del disco.  Per altre informazioni sugli insiemi di credenziali delle chiavi, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](../../key-vault/key-vault-get-started.md) e [Proteggere l'insieme di credenziali delle chiavi](../../key-vault/general/secure-your-key-vault.md). 

> [!WARNING]
> - Se in precedenza è stato usato Crittografia disco di Azure con Azure AD per crittografare una macchina virtuale, è necessario continuare a usare questa opzione per crittografare la macchina virtuale. Per informazioni dettagliate, vedere Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia disco di [Azure con Azure AD (versione precedente).](disk-encryption-key-vault-aad.md)

La creazione e la configurazione di un insieme di credenziali delle chiavi per l'utilizzo con Crittografia disco di Azure prevede tre passaggi:Creating and configuring a key vault for use with Azure Disk Encryption involves three steps:

1. Creazione di un gruppo di risorse, se necessario.
2. Creazione di un insieme di credenziali delle chiavi. 
3. Impostazione dei criteri di accesso avanzato dell'insieme di credenziali delle chiavi.

Questi passaggi sono illustrati nelle guide introduttive seguenti:These steps are illustrated in the following quickstarts:

- [Creare e crittografare una macchina virtuale Windows con l'interfaccia della riga di comando di Azure](disk-encryption-cli-quickstart.md)
- [Creare e crittografare una macchina virtuale Windows con Azure PowerShell](disk-encryption-cli-quickstart.md)

È inoltre possibile, se lo si desidera, generare o importare una chiave di crittografia a chiave (KEK).

> [!Note]
> I passaggi descritti in questo articolo sono automatizzati nello [script dell'interfaccia](https://github.com/ejarvi/ade-cli-getting-started) della riga di comando dei prerequisiti di Crittografia disco di Azure e nello [script di PowerShell dei prerequisiti di Crittografia disco](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)di Azure.The steps in this article are automated in the Azure Disk Encryption prerequisites CLI script and Azure Disk Encryption prerequisites PowerShell script .

## <a name="install-tools-and-connect-to-azure"></a>Installare gli strumenti e connettersi ad AzureInstall tools and connect to Azure

I passaggi descritti in questo articolo possono essere completati con l'interfaccia della riga di comando di [Azure,](/cli/azure/)il [modulo Azure PowerShell Az](/powershell/azure/overview)o il portale di [Azure.](https://portal.azure.com)

Mentre il portale è accessibile tramite il browser, l'interfaccia della riga di comando di Azure e Azure PowerShell richiedono l'installazione locale. Per informazioni dettagliate, vedere [Crittografia disco di Azure per Windows: Installare gli strumenti.](disk-encryption-windows.md#install-tools-and-connect-to-azure)

### <a name="connect-to-your-azure-account"></a>Connettersi all'account di Azure

Prima di usare l'interfaccia della riga di comando di Azure o Azure PowerShell, è necessario connettersi alla sottoscrizione di Azure.Before using the Azure CLI or Azure PowerShell, you must first connect to your Azure subscription. A tale [scopo, accedere con l'interfaccia della riga](/cli/azure/authenticate-azure-cli?view=azure-cli-latest)di comando di Azure, [accedere con Azure Powershell](/powershell/azure/authenticate-azureps?view=azps-2.5.0)o fornire le credenziali al portale di Azure quando richiesto.

```azurecli-interactive
az login
```

```azurepowershell-interactive
Connect-AzAccount
```

[!INCLUDE [disk-encryption-key-vault](../../../includes/disk-encryption-key-vault.md)]
 
## <a name="next-steps"></a>Passaggi successivi

- [Script dell'interfaccia della riga di comando dei prerequisiti di Crittografia disco di AzureAzure Disk Encryption prerequisites](https://github.com/ejarvi/ade-cli-getting-started)
- [Script di PowerShell per i prerequisiti di Crittografia disco di AzureAzure Disk Encryption prerequisites PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- Informazioni sugli scenari di Crittografia disco di [Azure nelle macchine virtuali di WindowsLearn Azure Disk Encryption scenarios on Windows VMs](disk-encryption-windows.md)
- Informazioni su come [risolvere i problemi di Crittografia disco di AzureLearn](disk-encryption-troubleshooting.md) how to troubleshoot Azure Disk Encryption
- Leggere gli script di esempio di Crittografia disco di [AzureRead](disk-encryption-sample-scripts.md) the Azure Disk Encryption sample scripts
