---
title: Controlli di sicurezza per Azure macchine virtuali Windows
description: Elenco di controllo dei controlli di sicurezza per la valutazione di Azure macchine virtuali Windows
ms.service: virtual-machines
ms.subservice: security
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 09/05/2019
ms.author: mbaldwin
ms.openlocfilehash: ac1ed9ac25d65d0391175fc6d43b48048da74926
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82101587"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Controlli di sicurezza per macchine virtuali Windows

Questo articolo descrive i controlli di sicurezza incorporati in macchine virtuali Windows.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì | |
| Supporto di VNet Injection| Sì | |
| Isolamento rete e supporto del firewall| Sì |  |
| Supporto del tunneling forzato| Sì | Vedere [configurare il tunneling forzato usando il modello di distribuzione Azure Resource Manager](/azure/vpn-gateway/vpn-gateway-forced-tunneling-rm). |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | [Monitorare e aggiornare una macchina virtuale Windows in Azure](tutorial-monitoring.md). |
| Registrazione e controllo del piano di gestione e controllo| Sì |  |
| Registrazione e controllo del piano dati | No |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì |  |
| Autorizzazione| Sì |  |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server: chiavi gestite da Microsoft | Sì | Vedere [crittografare i dischi virtuali in una macchina virtuale Windows](/azure/virtual-machines/windows/disk-encryption-overview). |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Macchine virtuali di Azure supporta la crittografia [ExpressRoute](/azure/expressroute) e VNet. Vedere [crittografia in transito nelle VM](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms). |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | Sì | Chiavi gestite dal cliente è uno scenario di crittografia di Azure supportato. vedere [Panoramica di crittografia di Azure](/azure/security/security-azure-encryption-overview#in-transit-encryption-in-vms).|
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Tramite HTTPS e TLS. |



## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì |  | 

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../../security/fundamentals/security-controls.md).
