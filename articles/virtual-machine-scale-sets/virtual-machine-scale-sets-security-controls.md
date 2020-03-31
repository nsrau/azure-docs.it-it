---
title: Controlli di sicurezza per i set di scalabilità di macchine virtuali di AzureSecurity controls for Azure Virtual Machine Scale Sets
description: Elenco di controllo dei controlli di sicurezza per la valutazione dei set di scalabilità di macchine virtuali di Azure
ms.service: virtual-machine-scale-sets
author: msmbaldwin
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 4007f4adeee065fe32492d3bd16f3a06d24e7d96
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190598"
---
# <a name="security-controls-for-azure-virtual-machine-scale-sets"></a>Controlli di sicurezza per i set di scalabilità di macchine virtuali di AzureSecurity controls for Azure Virtual Machine Scale Sets

Questo articolo documenta i controlli di sicurezza incorporati nei set di scalabilità di macchine virtuali di Azure.This article documents the security controls built into Azure Virtual Machine Scale Sets.

[!INCLUDE [Security controls header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto dell'endpoint di servizioService endpoint support| Sì | |
| Supporto per l'inserimento di vNet| Sì | |
| Supporto per l'isolamento della rete e il firewall| Sì |  |
| Supporto per il tunneling forzato| Sì | Vedere [Configurare il tunneling forzato usando il modello](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm)di distribuzione di Azure Resource Manager. |

## <a name="monitoring--logging"></a>Monitoraggio della registrazione dei &

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per il monitoraggio di Azure (analisi dei log, informazioni dettagliate sulle app e così via)Azure monitoring support (Log analytics, App insights, etc.)| Sì | Vedere [Monitorare e aggiornare una macchina virtuale Linux in Azure](/azure/virtual-machines/linux/tutorial-monitoring) e [Monitorare e aggiornare una macchina virtuale Windows in Azure.See](/azure/virtual-machines/windows/tutorial-monitoring)Monitor and update a Linux virtual machine in Azure. |
| Controllo e gestione del controllo e dell'audit degli aerei| Sì |  |
| Registrazione e controllo dei piani dati | No |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì |  |
| Autorizzazione| Sì |  |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server inattivi: chiavi gestite da MicrosoftServer-side encryption at rest: Microsoft-managed keys | Sì | Vedere Crittografia del disco di Azure per set di [scalabilità di macchine virtuali](disk-encryption-overview.md). |
| Crittografia in transito (ad esempio crittografia ExpressRoute, nella crittografia della rete virtuale e crittografia VNet-VNet )| Sì | Macchine virtuali di Azure supporta la crittografia ExpressRoute e VNet.Azure Virtual Machines supports [ExpressRoute](/azure/expressroute) and VNet encryption. Vedere [Crittografia in transito nelle macchine virtuali](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Crittografia lato server inattivi: chiavi gestite dal cliente (BYOK)Server-side encryption at rest: customer-managed keys (BYOK) | Sì | Le chiavi gestite dal cliente sono uno scenario di crittografia di Azure supportato. vedere Crittografia disco di Azure per set di scalabilità [di macchine virtualiSee Azure Disk Encryption for Virtual Machine Scale Sets](disk-encryption-overview.md)|
| Crittografia a livello di colonna (Azure Data Services)Column level encryption (Azure Data Services)| N/D | |
| Chiamate API crittografate| Sì | Tramite HTTPS e TLS. |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì |  | 

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui controlli di sicurezza incorporati nei servizi di Azure.Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).
