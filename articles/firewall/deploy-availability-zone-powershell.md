---
title: Distribuire il firewall di Azure con zone di disponibilità tramite PowerShell
description: Questo articolo illustra come distribuire un firewall di Azure con zone di disponibilità usando il Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 33dcebf14f4d534962783a30ec94f7ff6529ae0d
ms.sourcegitcommit: 8e31a82c6da2ee8dafa58ea58ca4a7dd3ceb6132
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74195916"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Distribuire un firewall di Azure con zone di disponibilità usando Azure PowerShell

Firewall di Azure può essere configurato durante la distribuzione con più zone di disponibilità per una maggiore disponibilità.

Questa funzionalità consente gli scenari seguenti:

- È possibile aumentare la disponibilità al 99,99% di tempo di ingrandimento. Per altre informazioni, vedere il [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) per Firewall di Azure. Il contratto di servizio con un tempo di attività del 99,99% è disponibile quando si selezionano due o più zone di disponibilità.
- È anche possibile associare Firewall di Azure a una zona specifica solo per motivi di prossimità, tramite il contratto di servizio standard pari al 99,95%.

Per altre informazioni sui zone di disponibilità di Azure firewall, vedere informazioni su [Firewall](overview.md) di Azure

Nell'esempio di Azure PowerShell seguente viene illustrato come è possibile distribuire un firewall di Azure con zone di disponibilità.

## <a name="create-a-firewall-with-availability-zones"></a>Creare un firewall con zone di disponibilità

Questo esempio crea un firewall nelle zone 1, 2 e 3.

Quando viene creato l'indirizzo IP pubblico standard, non viene specificata alcuna zona specifica. Per impostazione predefinita, viene creato un indirizzo IP con ridondanza della zona. Gli indirizzi IP pubblici standard possono essere configurati in tutte le zone o in una singola zona.

È importante saperlo, perché non è possibile avere un firewall nella zona 1 e un indirizzo IP nella zona 2. È tuttavia possibile avere un firewall nella zona 1 e nell'indirizzo IP in tutte le zone oppure un firewall e un indirizzo IP nella stessa zona per finalità di prossimità.

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

- [Esercitazione: Monitorare i log di Firewall di Azure](./tutorial-diagnostics.md)
