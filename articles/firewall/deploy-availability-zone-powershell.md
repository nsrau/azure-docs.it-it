---
title: Distribuire Firewall di Azure con le zone di disponibilità usando Azure PowerShell
description: In questo articolo descrive come distribuire un Firewall di Azure con le zone di disponibilità tramite Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/20/2019
ms.author: victorh
ms.openlocfilehash: 2fa6a62a28a1536da83994cb07b7c5fa5d7bda9f
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276912"
---
# <a name="deploy-an-azure-firewall-with-availability-zones-using-azure-powershell"></a>Distribuire un Firewall di Azure con le zone di disponibilità tramite Azure PowerShell

> [!IMPORTANT]
> Firewall di Azure con le zone di disponibilità è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Firewall di Azure può essere configurato durante la distribuzione in più zone di disponibilità per una maggiore disponibilità di estendersi.

Questa funzionalità consente gli scenari seguenti:

- È possibile aumentare la disponibilità al 99,99% tempo di attività. Per altre informazioni, vedere il Firewall di Azure [contratto di servizio (SLA)](https://azure.microsoft.com/support/legal/sla/azure-firewall/v1_0/). Il tempo di attività pari al 99,99% contratto di servizio è disponibile quando si selezionano due o più zone di disponibilità.
- È inoltre possibile associare il Firewall di Azure a una zona specifica solo per motivi di prossimità, tramite il contratto di servizio standard del 99,95%.

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
