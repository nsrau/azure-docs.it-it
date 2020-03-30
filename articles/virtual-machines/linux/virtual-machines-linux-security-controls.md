---
title: Controlli di sicurezza per macchine virtuali Linux di Azure - LinuxSecurity controls for Azure Linux Virtual Machines - Linux
description: Elenco di controllo dei controlli di sicurezza per la valutazione delle macchine virtuali Linux di Azure
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 300acaf4a9d2a11ef107e19df99452c909257d54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190563"
---
# <a name="security-controls-for-linux-virtual-machines"></a>Controlli di sicurezza per macchine virtuali LinuxSecurity controls for Linux Virtual Machines

Questo articolo documenta i controlli di sicurezza integrati in Macchine virtuali Linux.This article documents the security controls built into Linux Virtual Machines.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

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
| Supporto per il monitoraggio di Azure (analisi dei log, informazioni dettagliate sulle app e così via)Azure monitoring support (Log analytics, App insights, etc.)| Sì | Vedere [Monitorare e aggiornare una macchina virtuale Linux in Azure.See Monitor](/azure/virtual-machines/linux/tutorial-monitoring)and update a Linux virtual machine in Azure. |
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
| Crittografia lato server inattivi: chiavi gestite da MicrosoftServer-side encryption at rest: Microsoft-managed keys | Sì | Vedere Crittografia disco di [Azure per macchine virtuali Linux.See Azure Disk Encryption for Linux VMs](disk-encryption-overview.md). |
| Crittografia in transito (ad esempio crittografia ExpressRoute, nella crittografia della rete virtuale e crittografia VNet-VNet )| Sì | Macchine virtuali di Azure supporta la crittografia ExpressRoute e VNet.Azure Virtual Machines supports [ExpressRoute](/azure/expressroute) and VNet encryption. Vedere [Crittografia in transito nelle macchine virtuali](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Crittografia lato server inattivi: chiavi gestite dal cliente (BYOK)Server-side encryption at rest: customer-managed keys (BYOK) | Sì | Le chiavi gestite dal cliente sono uno scenario di crittografia di Azure supportato. vedere [Panoramica della crittografia](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms)di Azure .|
| Crittografia a livello di colonna (Azure Data Services)Column level encryption (Azure Data Services)| N/D | |
| Chiamate API crittografate| Sì | Tramite HTTPS e TLS. |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì |  | 

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui controlli di sicurezza incorporati nei servizi di Azure.Learn more about the [built-in security controls across Azure services](../../security/fundamentals/security-controls.md).
