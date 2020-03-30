---
title: Distribuire Firewall di Azure con zone di disponibilità tramite PowerShellDeploy Azure Firewall with Availability zones using PowerShell
description: In this article, you learn how to deploy an Azure Firewall with Availability Zones using the Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 33dcebf14f4d534962783a30ec94f7ff6529ae0d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74195916"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Distribuire un Firewall di Azure con zone di disponibilità tramite Azure PowerShell

Firewall di Azure può essere configurato durante la distribuzione con più zone di disponibilità per una maggiore disponibilità.

Questa funzionalità consente i seguenti scenari:

- È possibile aumentare la disponibilità al 99,99% di tempo di attività. Per altre informazioni, vedere il [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/) per Firewall di Azure. Il contratto di servizio con un tempo di attività del 99,99% è disponibile quando si selezionano due o più zone di disponibilità.
- È anche possibile associare Firewall di Azure a una zona specifica solo per motivi di prossimità, tramite il contratto di servizio standard pari al 99,95%.

Per altre informazioni sulle zone di disponibilità del firewall di Azure, vedere [Che cos'è Firewall](overview.md) di Azure?

L'esempio di Azure PowerShell seguente mostra come distribuire un firewall di Azure con zone di disponibilità.

## <a name="create-a-firewall-with-availability-zones"></a>Creare un firewall con zone di disponibilità

In questo esempio viene creato un firewall nelle zone 1, 2 e 3.

Quando viene creato l'indirizzo IP pubblico standard, non viene specificata alcuna zona specifica. In questo modo viene creato un indirizzo IP con ridondanza di zona per impostazione predefinita. Gli indirizzi IP pubblici standard possono essere configurati in tutte le zone o in una singola zona.

È importante sapere, perché non è possibile avere un firewall nella zona 1 e un indirizzo IP nella zona 2. Tuttavia, è possibile avere un firewall nella zona 1 e un indirizzo IP in tutte le aree oppure un firewall e un indirizzo IP nella stessa area singola per scopi di prossimità.

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
