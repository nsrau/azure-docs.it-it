---
title: Controlli di sicurezza per macchine virtuali Windows di AzureSecurity controls for Azure Windows Virtual Machines
description: Elenco di controllo dei controlli di sicurezza per la valutazione delle macchine virtuali Windows di Azure
services: virtual-machines
ms.service: virtual-machines
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: 569c989e8876bb7213d1469c7a70da99aa0b1e9c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455343"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Controlli di sicurezza per macchine virtuali WindowsSecurity controls for Windows Virtual Machines

In questo articolo vengono documentati i controlli di sicurezza incorporati in Macchine virtuali Windows.This article documents the security controls built into Windows Virtual Machines.

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
| Supporto per il monitoraggio di Azure (analisi dei log, informazioni dettagliate sulle app e così via)Azure monitoring support (Log analytics, App insights, etc.)| Sì | [Monitorare e aggiornare una macchina virtuale Windows in Azure.](tutorial-monitoring.md) |
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
| Crittografia lato server inattivi: chiavi gestite da MicrosoftServer-side encryption at rest: Microsoft-managed keys | Sì | Vedere [Crittografare i dischi virtuali in una macchina virtuale Windows](/azure/virtual-machines/windows/disk-encryption-overview). |
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
