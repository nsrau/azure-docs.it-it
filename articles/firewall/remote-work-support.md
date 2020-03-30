---
title: Supporto del lavoro remoto di Firewall di AzureAzure Firewall remote work support
description: Questo articolo illustra come Firewall di Azure può supportare i requisiti della forza lavoro remota.
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
# <a name="azure-firewall-remote-work-support"></a>Supporto del lavoro remoto di Firewall di AzureAzure Firewall remote work support

Firewall di Azure è un servizio di sicurezza di rete gestito basato su cloud che protegge le risorse di rete virtuale di Azure.Azure Firewall is a managed, cloud-based network security service that protects your Azure virtual network resources. È un firewall con stato completo distribuito come servizio con disponibilità elevata e scalabilità cloud senza limiti. 

## <a name="firewall-rules"></a>Regole del firewall

È possibile usare Firewall di Azure per proteggere l'accesso RDP in ingresso dell'infrastruttura di desktop virtuale (VDI) alla rete virtuale di Azure usando le regole DNAT di Firewall di Azure.You can use Azure Firewall to secure your virtual desktop infrastructure (VDI) inbound RDP access to your Azure virtual network using Azure Firewall [DNAT rules.](rule-processing.md) Windows Virtual Desktop (WVD) non richiede l'apertura di alcun accesso in ingresso alla rete virtuale. Tuttavia, è necessario consentire un set di connessioni di rete in uscita per le macchine virtuali WVD in esecuzione nella rete virtuale. Per ulteriori informazioni, vedere [Che cos'è Windows Virtual Desktop?](../virtual-desktop/overview.md#requirements)

È possibile configurare questo accesso in uscita usando le regole dell'applicazione Firewall di Azure.You can configure this outbound access using Azure Firewall application rules. Per altre informazioni, vedere [Esercitazione: Distribuire e configurare Firewall](tutorial-firewall-deploy-portal.md)di Azure usando il portale di Azure.For more information, see Tutorial: Deploy and configure Azure Firewall using the Azure portal .

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni su [Windows Virtual Desktop](https://docs.microsoft.com/azure/virtual-desktop/).