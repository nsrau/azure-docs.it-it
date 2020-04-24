---
title: Supporto per lavoro remoto del firewall di Azure
description: Questo articolo illustra come il firewall di Azure può supportare i requisiti di forza lavoro remoto.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289641"
---
# <a name="azure-firewall-remote-work-support"></a>Supporto per lavoro remoto del firewall di Azure

Il firewall di Azure è un servizio di sicurezza di rete gestito e basato sul cloud che protegge le risorse della rete virtuale di Azure. È un firewall con stato completo distribuito come servizio con disponibilità elevata e scalabilità cloud senza limiti. 

## <a name="firewall-rules"></a>Regole del firewall

È possibile usare il firewall di Azure per proteggere l'accesso RDP in ingresso di Virtual Desktop Infrastructure (VDI) alla rete virtuale di Azure usando [le regole DNAT](rule-processing.md)del firewall di Azure. Per desktop virtuale Windows (WVD) non è necessario aprire alcun accesso in ingresso alla rete virtuale. È tuttavia necessario consentire un set di connessioni di rete in uscita per le macchine virtuali WVD in esecuzione nella rete virtuale. Per ulteriori informazioni, vedere informazioni sul [desktop virtuale Windows](../virtual-desktop/overview.md#requirements)

È possibile configurare questo accesso in uscita usando le regole dell'applicazione firewall di Azure. Per altre informazioni, vedere [esercitazione: distribuire e configurare il firewall di Azure usando il portale di Azure](tutorial-firewall-deploy-portal.md).

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni su [desktop virtuale di Windows](https://docs.microsoft.com/azure/virtual-desktop/).