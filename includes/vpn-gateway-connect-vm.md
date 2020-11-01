---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/23/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a8d1b27fc040e6aed0bdeeb86b2e6c4df13f87c3
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2020
ms.locfileid: "92540899"
---
È possibile connettersi a una VM distribuita nella rete virtuale creando una connessione Desktop remoto alla VM. Il modo migliore per verificare inizialmente che è possibile connettersi alla VM consiste nel connettersi usando il rispettivo indirizzo IP privato, invece del nome del computer. Ciò consente di verificare se è possibile connettersi, non se la risoluzione dei nomi è configurata correttamente.

1. Individuare l'indirizzo IP privato. È possibile trovare l'indirizzo IP privato di una VM esaminando le proprietà per la VM nel portale di Azure oppure usando PowerShell.

   * Portale di Azure: individuare la macchina virtuale nel portale di Azure. Visualizzare le proprietà per la VM. Viene elencato l'indirizzo IP.

   * PowerShell: usare l'esempio per visualizzare un elenco di macchine virtuali e di indirizzi IP privati dai gruppi di risorse. Non è necessario modificare questo esempio prima di usarlo.

     ```azurepowershell-interactive
     $VMs = Get-AzVM
     $Nics = Get-AzNetworkInterface | Where VirtualMachine -ne $null

     foreach($Nic in $Nics)
     {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
     }
     ```

1. Verificare di essere connessi alla rete virtuale usando la connessione VPN da punto a sito.
1. Aprire la **connessione Desktop remoto** digitando "RDP" o "Connessione Desktop remoto" nella casella di ricerca sulla barra delle applicazioni, quindi selezionare Connessione Desktop remoto. È anche possibile aprire una connessione Desktop remoto usando il comando "mstsc" in PowerShell. 
1. In Connessione Desktop remoto immettere l'indirizzo IP privato della VM. È possibile fare clic su "Mostra opzioni" per modificare altre impostazioni e quindi connettersi.

**Risolvere i problemi relativi a una connessione**

Se si verificano problemi di connessione a una macchina virtuale tramite la connessione VPN, controllare gli elementi seguenti:

* Verificare che la connessione VPN sia attiva.

* Verificare che venga effettuata la connessione all'indirizzo IP privato per la VM.

* Se è possibile connettersi alla VM usando l'indirizzo IP privato, ma non il nome del computer, verificare di avere configurato correttamente il valore per DNS. Per altre informazioni sul funzionamento della risoluzione dei nomi per le macchine virtuali, vedere [Risoluzione dei nomi per le macchine virtuali](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

* Per altre informazioni sulle connessioni RDP, vedere [Risolvere i problemi delle connessioni Desktop remoto a una macchina virtuale](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md).