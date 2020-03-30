---
title: Applicare la licenza di Windows alle macchine virtuali host della sessione - AzureApply Windows license to session host virtual machines - Azure
description: Viene descritto come applicare la licenza di Windows per le macchine virtuali Windows Virtual Desktop.
services: virtual-desktop
author: ChristianMontoya
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 2543dd12e8a75a038a1fc04371b8c562ef696e25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254235"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>Applicare la licenza di Windows alle macchine virtuali host della sessioneApply Windows license to session host virtual machines

I clienti con licenza appropriata per l'esecuzione dei carichi di lavoro di Windows Virtual Desktop sono idonei ad applicare una licenza di Windows alle macchine virtuali dell'host di sessione ed eseguirle senza pagare per un'altra licenza. Per ulteriori informazioni, vedere Prezzi di [Windows Virtual Desktop](https://azure.microsoft.com/pricing/details/virtual-desktop/).

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Modalità di utilizzo della licenza di Windows Virtual Desktop
Gestione licenze Desktop virtuale di Windows consente di applicare una licenza a qualsiasi macchina virtuale Windows o Windows Server registrata come host di sessione in un pool host e riceve connessioni utente. Questa licenza non si applica alle macchine virtuali in esecuzione come server di condivisione file, controller di dominio e così via.

Esistono diversi modi per utilizzare la licenza desktop virtuale di Windows:
- È possibile creare un pool host e le relative macchine virtuali host di sessione usando [l'offerta Azure Marketplace.](./create-host-pools-azure-marketplace.md) Alle macchine virtuali create in questo modo viene applicata automaticamente la licenza.
- È possibile creare un pool host e le relative macchine virtuali host di sessione usando il [modello GitHub Azure Resource Manager](./create-host-pools-arm-template.md). Alle macchine virtuali create in questo modo viene applicata automaticamente la licenza.
- È possibile applicare una licenza a una macchina virtuale host di sessione esistente. A tale scopo, seguire innanzitutto le istruzioni in [Creare un pool host con PowerShell](./create-host-pools-powershell.md) per creare un pool host e macchine virtuali associate, quindi tornare a questo articolo per informazioni su come applicare la licenza.

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>Applicare una licenza di Windows a una macchina virtuale host sessioneApply a Windows license to a session host VM
Verificare di aver prima [installato e configurato l'ultima versione di Azure PowerShell](/powershell/azure/overview). Eseguire il cmdlet PowerShell seguente per applicare la licenza di Windows:

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>Verificare che la macchina virtuale dell'host della sessione stia usando il vantaggio della licenzaVerify your session host VM is using the licensing benefit
Dopo aver distribuito la macchina virtuale, eseguire questo cmdlet ot verificare il tipo di licenza:
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Una macchina virtuale host della sessione con la licenza di Windows applicata ti mostrerà qualcosa di simile a questo:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Le macchine virtuali senza la licenza di Windows applicata ti mostreranno qualcosa di simile a questo:

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Eseguire il cmdlet seguente per visualizzare un elenco di tutte le macchine virtuali host della sessione a cui è applicata la licenza di Windows nella sottoscrizione di Azure:

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
