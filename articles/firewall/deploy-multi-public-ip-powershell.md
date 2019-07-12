---
title: Distribuzione di Firewall di Azure con più indirizzi IP pubblici usando Azure PowerShell
description: In questo articolo descrive come distribuire un Firewall di Azure con più indirizzi IP pubblici tramite Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 7/10/2019
ms.author: victorh
ms.openlocfilehash: ce47612f18ee64caa3a053001deb5448f7c27bfd
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703992"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Distribuire un Firewall di Azure con più indirizzi IP pubblici usando Azure PowerShell

> [!IMPORTANT]
> Firewall di Azure con più indirizzi IP pubblici è disponibile tramite Azure PowerShell, CLI di Azure, REST e modelli. Interfaccia utente del portale viene aggiunto alle aree in modo incrementale e sarà disponibile in tutte le aree quando viene completata l'implementazione.

È possibile distribuire un Firewall di Azure con fino a 100 indirizzi IP pubblici.

Questa funzionalità consente gli scenari seguenti:

- **DNAT** - È possibile convertire più istanze di porta standard per i server back-end. Se ad esempio si dispone di due indirizzi IP pubblici, è possibile convertire la porta TCP 3389 (RDP) per entrambi gli indirizzi IP.
- **SNAT** - Sono disponibili porte aggiuntive per le connessioni SNAT in uscita, riducendo il rischio di esaurimento delle porte SNAT. Al momento, Firewall di Azure seleziona in modo casuale l'indirizzo IP pubblico di origine da usare per una connessione. Se sono presenti filtri downstream nella rete, è necessario consentire tutti gli indirizzi IP pubblici associati al firewall.

Gli esempi di Azure PowerShell seguenti illustrano come è possibile configurare, aggiungere e rimuovere indirizzi IP pubblici per il Firewall di Azure.

> [!NOTE]
> È possibile rimuovere la prima configurazione IP dalla pagina configurazione indirizzi IP pubblica Firewall di Azure. Se si desidera modificare l'indirizzo IP, è possibile usare Azure PowerShell.

## <a name="create-a-firewall-with-two-or-more-public-ip-addresses"></a>Creare una regola del firewall con due o più indirizzi IP pubblici

In questo esempio crea un firewall collegato alla rete virtuale *vnet* con due indirizzi IP pubblici.

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

In questo esempio, l'indirizzo IP pubblico *azFwPublicIp1* è collegata al firewall.

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

In questo esempio, l'indirizzo IP pubblico *azFwPublicIp1* è disconnesso dal firewall.

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
