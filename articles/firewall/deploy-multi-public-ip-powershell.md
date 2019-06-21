---
title: Distribuzione di Firewall di Azure con più indirizzi IP pubblici usando Azure PowerShell
description: In questo articolo descrive come distribuire un Firewall di Azure con più indirizzi IP pubblici tramite Azure PowerShell.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 06/15/2019
ms.author: victorh
ms.openlocfilehash: a7dd35212a573fc3e94dadea4365f150122e1b5a
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67276743"
---
# <a name="deploy-an-azure-firewall-with-multiple-public-ip-addresses-using-azure-powershell"></a>Distribuire un Firewall di Azure con più indirizzi IP pubblici usando Azure PowerShell

> [!IMPORTANT]
> Firewall di Azure con più indirizzi IP pubblici è attualmente in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate.
> Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

È possibile distribuire un Firewall di Azure con indirizzi IP pubblici fino a 600.

Questa funzionalità consente gli scenari seguenti:

- **DNAT** -è possibile convertire più istanze di porta standard per i server back-end. Ad esempio, se si dispongono di due indirizzi IP pubblici, è possibile tradurre la porta TCP 3389 (RDP) per entrambi gli indirizzi IP.
- **SNAT** -porte aggiuntive sono disponibili per le connessioni SNAT in uscita, riducendo il rischio di esaurimento delle porte SNAT. A questo punto, Firewall di Azure seleziona casualmente l'indirizzo IP pubblico di origine da utilizzare per una connessione. Se si dispone di qualsiasi applicazione di filtri a valle nella rete, è necessario consentire tutti gli indirizzi IP pubblici associati con il firewall.

Gli esempi di Azure PowerShell seguenti illustrano come è possibile aggiungere, rimuovere e configurare gli indirizzi IP pubblici per il Firewall di Azure.

> [!NOTE]
> Se si aggiunge o rimuove un indirizzo IP pubblico a un firewall in esecuzione, la connettività in ingresso esistente utilizzando le regole DNAT potrebbe non funzionare per 40 120 secondi. Si tratta di una limitazione dell'anteprima pubblica per questa funzionalità.

## <a name="add-a-public-ip-address-to-an-existing-firewall"></a>Aggiungere un indirizzo IP pubblico a un firewall esistente

In questo esempio, l'indirizzo IP pubblico *azFwPublicIp1* collegata al firewall.

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

In questo esempio, l'indirizzo IP pubblico *azFwPublicIp1* come disconnesse dal firewall.

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

## <a name="next-steps"></a>Passaggi successivi

* [Esercitazione: monitorare i log del Firewall di Azure](./tutorial-diagnostics.md)
