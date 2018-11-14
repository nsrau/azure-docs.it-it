---
title: Configurare gli endpoint su una VM Windows classica | Microsoft Docs
description: Informazioni su come configurare gli endpoint per una macchina virtuale Windows classica nel portale di Azure per consentire la comunicazione con una macchina virtuale Windows in Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 8afc21c2-d3fb-43a3-acce-aa06be448bb6
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/23/2018
ms.author: cynthn
ms.openlocfilehash: 373003eb8be0482ed96d7d11ecd879237f69b47a
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957166"
---
# <a name="set-up-endpoints-on-a-windows-virtual-machine-by-using-the-classic-deployment-model"></a>Impostare gli endpoint su una macchina virtuale Windows usando il modello di distribuzione classico
Le macchine virtuali (VM) Windows create in Azure con il modello di distribuzione classico possono comunicare automaticamente mediante un canale di rete privato con altre macchine virtuali dello stesso servizio cloud o nella stessa rete virtuale. Tuttavia, i computer in Internet o altre reti virtuali richiedono gli endpoint per indirizzare il traffico di rete in ingresso a una macchina virtuale. 

È anche possibile impostare gli endpoint su [macchine virtuali Linux](../../linux/classic/setup-endpoints.md).

> [!IMPORTANT]
> Azure offre due diversi modelli di distribuzione per creare e usare le risorse: [Gestione risorse e la distribuzione classica](../../../resource-manager-deployment-model.md). In questo articolo viene illustrato il modello di distribuzione classica. Microsoft consiglia di usare il modello di Gestione risorse per le distribuzioni più recenti.  
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

In the **Resource Manager** gli endpoint vengono configurati tramite **Gruppi di sicurezza di rete**. Per altre informazioni, vedere [Consentire l'accesso esterno alla VM mediante il portale di Azure](../nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Quando si crea una macchina virtuale Windows nel portale di Azure, gli endpoint comuni, ad esempio quelli di Desktop remoto e Comunicazione remota di Windows PowerShell, sono in genere creati automaticamente. È possibile configurare altri endpoint in un secondo momento in base alle esigenze.

[!INCLUDE [virtual-machines-common-classic-setup-endpoints](../../../../includes/virtual-machines-common-classic-setup-endpoints.md)]

## <a name="next-steps"></a>Passaggi successivi
* Per usare un cmdlet di Azure PowerShell per impostare un endpoint di VM, vedere [Add-AzureEndpoint](https://msdn.microsoft.com/library/azure/dn495300.aspx).
* Per usare un cmdlet di Azure PowerShell per gestire un ACL in un endpoint, vedere [Come gestire gli elenchi di controllo di accesso per gli endpoint tramite PowerShell](../../../virtual-network/virtual-networks-acl-powershell.md).
* Se una macchina virtuale è stata creata nel modello di distribuzione Resource Manager, è possibile usare Azure PowerShell per [creare gruppi di sicurezza di rete](../../../virtual-network/tutorial-filter-network-traffic.md) per controllare il traffico verso la VM.
