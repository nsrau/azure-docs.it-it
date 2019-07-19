---
title: Distribuire il firewall di Azure con più indirizzi IP pubblici usando Azure PowerShell
description: Questo articolo illustra come distribuire un firewall di Azure con più indirizzi IP pubblici usando il Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/19/2019
ms.author: victorh
ms.openlocfilehash: ba2736ae69d0bf7feff5f852da2446bfa7a722a6
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325240"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Distribuire un firewall di Azure con più indirizzi IP pubblici usando Azure PowerShell

Questa funzionalità consente gli scenari seguenti:

- **DNAT** - È possibile convertire più istanze di porta standard per i server back-end. Se ad esempio si dispone di due indirizzi IP pubblici, è possibile convertire la porta TCP 3389 (RDP) per entrambi gli indirizzi IP.
- **SNAT** - Sono disponibili porte aggiuntive per le connessioni SNAT in uscita, riducendo il rischio di esaurimento delle porte SNAT. Al momento, Firewall di Azure seleziona in modo casuale l'indirizzo IP pubblico di origine da usare per una connessione. Se sono presenti filtri downstream nella rete, è necessario consentire tutti gli indirizzi IP pubblici associati al firewall.
 
Il firewall di Azure con più indirizzi IP pubblici è disponibile tramite il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure, REST e i modelli. È possibile distribuire un firewall di Azure con un massimo di 100 indirizzi IP pubblici.

Gli esempi di Azure PowerShell seguenti illustrano come è possibile configurare, aggiungere e rimuovere indirizzi IP pubblici per il firewall di Azure.

> [!NOTE]
> Non è possibile rimuovere il primo configurazione IP dalla pagina di configurazione dell'indirizzo IP pubblico del firewall di Azure. Se si desidera modificare l'indirizzo IP, è possibile utilizzare Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Creare un firewall con due o più indirizzi IP pubblici

Questo esempio crea un firewall collegato alla rete virtuale *VNET* con due indirizzi IP pubblici.

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

$pip2 = New-AzPublicIpAddress `
  -Name "AzFwPublicIp2" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

New-AzFirewall `
  -Name "azFw" `
  -ResourceGroupName $rgName `
  -Location centralus `
  -VirtualNetwork $vnet `
  -PublicIpAddress @($pip1, $pip2)
```

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Aggiungere un indirizzo IP pubblico a un firewall esistente

In questo esempio, l'indirizzo IP pubblico *azFwPublicIp1* è collegato al firewall.

```azurepowershell
$pip = New-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg" `
  -Sku "Standard" `
  -Location "centralus" `
  -AllocationMethod Static

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.AddPublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="remove-a-public-ip-address-from-an-existing-firewall"></a>Rimuovere un indirizzo IP pubblico da un firewall esistente

In questo esempio, l'indirizzo IP pubblico *azFwPublicIp1* viene scollegato dal firewall.

```azurepowershell
$pip = Get-AzPublicIpAddress `
  -Name "azFwPublicIp1" `
  -ResourceGroupName "rg"

$azFw = Get-AzFirewall `
  -Name "AzureFirewall" `
  -ResourceGroupName "rg"

$azFw.RemovePublicIpAddress($pip)

$azFw | Set-AzFirewall
```

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: monitorare i log del Firewall di Azure](./tutorial-diagnostics.md)
