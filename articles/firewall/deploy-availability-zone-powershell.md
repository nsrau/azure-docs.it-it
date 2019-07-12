---
title: Distribuire Firewall di Azure con le zone di disponibilità usando Azure PowerShell
description: In questo articolo descrive come distribuire un Firewall di Azure con le zone di disponibilità tramite Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: 56958eedceeb4602589d65d5e0eb7b10e8a9ff2d
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704006"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Distribuire un Firewall di Azure con le zone di disponibilità tramite Azure PowerShell

Firewall di Azure può essere configurato durante la distribuzione con più zone di disponibilità per una maggiore disponibilità.

Questa funzionalità consente gli scenari seguenti:

- È possibile aumentare la disponibilità al 99,99% tempo di attività. Per altre informazioni, vedere il [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) per Firewall di Azure. Il contratto di servizio con un tempo di attività del 99,99% è disponibile quando si selezionano due o più zone di disponibilità.
- È anche possibile associare Firewall di Azure a una zona specifica solo per motivi di prossimità, tramite il contratto di servizio standard pari al 99,95%.

Per altre informazioni sulle zone di disponibilità Firewall di Azure, vedere [What ' s Firewall di Azure?](overview.md)

L'esempio di Azure PowerShell seguente illustra come distribuire un Firewall di Azure con le zone di disponibilità.

## <a name="create-a-firewall-with-availability-zones"></a>Creare una regola del firewall con le zone di disponibilità

In questo esempio crea un firewall nelle zone 1, 2 e 3.

Quando viene creato l'indirizzo IP pubblico standard, è specificato alcun fuso specifico. Ciò consente di creare un indirizzo IP con ridondanza della zona per impostazione predefinita. Gli indirizzi IP pubblici standard possono essere configurati in tutte le zone, o una singola zona.

È importante conoscere, perché non è possibile avere un firewall nella zona 1 e un indirizzo IP nella zona 2. È tuttavia possibile impostare un firewall nella zona 1 e l'indirizzo IP in tutte le zone, o un firewall e un indirizzo IP nella stessa zona singola per scopi di prossimità.

```azurepowershell
$rgName = "resourceGroupName"

$vnet = Get-AzVirtualNetwork `
  -Name "vnet" `
  -ResourceGroupName $rgName

$pip1 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1)
  -Zone 1,2,3
```

## <a name="next-steps"></a>Passaggi successivi

- [Esercitazione: monitorare i log del Firewall di Azure](./tutorial-diagnostics.md)
