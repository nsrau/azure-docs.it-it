---
title: Ridistribuire macchine virtuali Windows in Azure | Documentazione Microsoft
description: Come ridistribuire macchine virtuali Windows in Azure per ridurre i problemi di connessione RDP.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: genlin
manager: dcscontentpm
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: afbea39a080e1dd768a14d6e0eacda1bad23c5a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87074428"
---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Ridistribuire una macchina virtuale Windows in un nuovo nodo di Azure
Se si stanno riscontrando difficoltà nella risoluzione dei problemi relativi a connessione di desktop remoto (RDP) o accesso delle applicazioni a una macchina virtuale (VM) di Azure basata su Windows, potrebbe essere utile la ridistribuzione. Quando si ridistribuisce una macchina virtuale, Azure arresta la macchina virtuale, la sposta in un nuovo nodo dell'infrastruttura di Azure e quindi la riaccende conservando tutte le opzioni di configurazione e le risorse associate. In questo articolo viene illustrato come ridistribuire una VM con Azure PowerShell o il portale di Azure.

> [!NOTE]
> Dopo la ridistribuzione di una VM, il disco temporaneo viene perso e gli indirizzi IP dinamici associati all'interfaccia di rete virtuale vengono aggiornati. 


## <a name="using-azure-powershell"></a>Uso di Azure PowerShell
Assicurarsi che sia installata la versione più recente di Azure PowerShell 1.x. Per altre informazioni, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/).

L'esempio seguente distribuisce la VM denominata `myVM` nel gruppo di risorse `myResourceGroup`:

```powershell
Set-AzVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Passaggi successivi
In caso di difficoltà di connessione alla VM, è possibile trovare assistenza specifica sulla [risoluzione dei problemi delle connessioni RDP](troubleshoot-rdp-connection.md) o [passaggi dettagliati sulla risoluzione dei problemi RDP](detailed-troubleshoot-rdp.md). Se non si riesce ad accedere a un'applicazione in esecuzione sulla VM, è possibile leggere l'articolo sulle [difficoltà nella risoluzione dei problemi relativi alle applicazioni](./troubleshoot-app-connection.md).
