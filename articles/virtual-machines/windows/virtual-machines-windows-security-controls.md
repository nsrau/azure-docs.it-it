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
ms.openlocfilehash: 6ab6133faef4a6c7a8eb929e5f4cd1840e971a99
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87088343"
---
# <a name="security-controls-for-windows-virtual-machines"></a>Controlli di sicurezza per macchine virtuali Windows

Questo articolo descrive i controlli di sicurezza incorporati in macchine virtuali Windows.

[!INCLUDE [Security controls header](../../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint di servizio| Sì | |
| Supporto aggiunta rete virtuale| Sì | |
| Supporto isolamento rete e protezione tramite firewall| Sì |  |
| Supporto del tunneling forzato| Sì | Vedere [Configurare il tunneling forzato tramite il modello di distribuzione Azure Resource Manager](../../vpn-gateway/vpn-gateway-forced-tunneling-rm.md). |

## <a name="monitoring--logging"></a>Monitoraggio e registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto monitoraggio di Azure (analisi dei log, analisi approfondita dell'app e così via)| Sì | [Monitorare e aggiornare una macchina virtuale Windows in Azure](./tutorial-monitor.md). |
| Piano di gestione e controllo - Registrazione e controllo| Sì |  |
| Piano dati - Registrazione e controllo | No |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì |  |
| Autorizzazione| Sì |  |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server dei dati inattivi: chiavi gestite da Microsoft | Sì | Vedere [crittografare i dischi virtuali in una macchina virtuale Windows](./disk-encryption-overview.md). |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Macchine virtuali di Azure supporta la crittografia [ExpressRoute](../../expressroute/index.yml) e VNet. Vedere [Crittografia dei dati in transito nelle VM](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms). |
| Crittografia lato server dei dati inattivi: chiavi gestite dal cliente (BYOK) | Sì | Chiavi gestite dal cliente è uno scenario di crittografia di Azure supportato. vedere [Panoramica di crittografia di Azure](../../security/fundamentals/encryption-overview.md#in-transit-encryption-in-vms).|
| Crittografia a livello di colonna (Servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | Tramite HTTPS e TLS. |



## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto gestione della configurazione (controllo delle versioni di configurazione e così via)| Sì |  | 

## <a name="next-steps"></a>Passaggi successivi

- Maggiori informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../../security/fundamentals/security-controls.md).
