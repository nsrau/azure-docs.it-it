---
title: Creare e crittografare un set di scalabilità di macchine virtuali con i modelli di Azure Resource Manager
description: In questa guida di avvio rapido si apprenderà come usare i modelli di Azure Resource Manager per creare e crittografare un set di scalabilità di macchine virtuali
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/10/2019
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: c2b49a7e7e14bfbefcca64133ff23fdfabe53e7b
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2020
ms.locfileid: "83198433"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Crittografare i set di scalabilità di macchine virtuali con Azure Resource Manager

È possibile crittografare e decrittografare set di scalabilità di macchine virtuali Linux usando i modelli di Azure Resource Manager.

## <a name="deploying-templates"></a>Distribuzione dei modelli

Prima di tutto selezionare il modello più adatto per lo scenario.

- [Abilitare la crittografia dei dischi in un set di scalabilità di macchine virtuali Linux in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Abilitare la crittografia dei dischi in un set di scalabilità di macchine virtuali Windows in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Distribuire un set di scalabilità di macchine virtuali Linux con una jumpbox e abilitare la crittografia nei set di scalabilità di macchine virtuali Linux](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Distribuire un set di scalabilità di macchine virtuali Windows con una jumpbox e abilitare la crittografia nei set di scalabilità di macchine virtuali Windows](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Disabilitare la crittografia dei dischi in un set di scalabilità di macchine virtuali Linux in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Disabilitare la crittografia dei dischi in un set di scalabilità di macchine virtuali Windows in esecuzione](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Seguire quindi questa procedura:

     1. Click **Deploy to Azure**.
     2. Fill in the required fields then agree to the terms and conditions.
     3. Click **Purchase** to deploy the template.

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia dischi di Azure per set di scalabilità di macchine virtuali](disk-encryption-overview.md)
- [Crittografare set di scalabilità di macchine virtuali con l'interfaccia della riga di comando di Azure](disk-encryption-cli.md)
- [Crittografare set di scalabilità di macchine virtuali con Azure PowerShell](disk-encryption-powershell.md)
- [Creare e configurare un insieme di credenziali delle chiavi per Crittografia dischi di Azure](disk-encryption-key-vault.md)
- [Usare Crittografia dischi di Azure con sequenziazione delle estensioni del set di scalabilità di macchine virtuali](disk-encryption-extension-sequencing.md)
