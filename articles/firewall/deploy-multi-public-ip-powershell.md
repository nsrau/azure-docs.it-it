---
title: Distribuire Firewall di Azure con più indirizzi IP pubblici tramite PowerShellDeploy Azure Firewall with multiple public IP addresses using PowerShell
description: In this article, you learn how to deploy an Azure Firewall with multiple public IP addresses using the Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ad54b60d8f15e36636f887015d97967740123669
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74195873"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Distribuire un Firewall di Azure con più indirizzi IP pubblici usando Azure PowerShell

Questa funzionalità consente i seguenti scenari:

- **DNAT** - È possibile convertire più istanze di porta standard per i server back-end. Se ad esempio si dispone di due indirizzi IP pubblici, è possibile convertire la porta TCP 3389 (RDP) per entrambi gli indirizzi IP.
- **SNAT** - Sono disponibili porte aggiuntive per le connessioni SNAT in uscita, riducendo il rischio di esaurimento delle porte SNAT. Al momento, Firewall di Azure seleziona in modo casuale l'indirizzo IP pubblico di origine da usare per una connessione. Se sono presenti filtri downstream nella rete, è necessario consentire tutti gli indirizzi IP pubblici associati al firewall.
 
Il servizio Firewall di Azure con più indirizzi IP pubblici è disponibile tramite il portale di Azure, Azure PowerShell, l'interfaccia della riga di comando di Azure, REST e i modelli. È possibile distribuire un firewall di Azure con un massimo di 100 indirizzi IP pubblici.

Gli esempi di Azure PowerShell seguenti illustrano come configurare, aggiungere e rimuovere indirizzi IP pubblici per Firewall di Azure.The following Azure PowerShell examples show how you can configure, add, and remove public IP addresses for Azure Firewall.

> [!NOTE]
> Non è possibile rimuovere il primo ipConfiguration dalla pagina di configurazione dell'indirizzo IP pubblico di Firewall di Azure.You can't remove the first ipConfiguration from the Azure Firewall public IP address configuration. Se si vuole modificare l'indirizzo IP, è possibile usare Azure PowerShell.If you want to modify the IP address, you can use Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Creare un firewall con due o più indirizzi IP pubblici

In questo esempio viene creato un firewall collegato alla *rete virtuale vnet* con due indirizzi IP pubblici.

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

* [Esercitazione: Monitorare i log di Firewall di Azure](./tutorial-diagnostics.md)
