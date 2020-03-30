---
title: Controlli di sicurezza per il gateway VPN di AzureSecurity controls for Azure VPN Gateway
description: Elenco di controllo dei controlli di sicurezza per la valutazione del gateway VPN di Azure
services: sql-database
author: msmbaldwin
manager: rkarlin
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/06/2019
ms.author: mbaldwin
ms.openlocfilehash: cdf616b29a93e786ef26af83b5d3b3541f94d67c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972286"
---
# <a name="security-controls-for-azure-vpn-gateway"></a>Controlli di sicurezza per il gateway VPN di AzureSecurity controls for Azure VPN Gateway

Questo articolo documenta i controlli di sicurezza integrati nel gateway VPN di Azure.This article documents the security controls built into Azure VPN Gateway.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto dell'endpoint di servizioService endpoint support| N/D | |
| Supporto per l'inserimento di vNet| N/D | |
| Supporto per l'isolamento della rete e il firewall| Sì | I gateway VPN sono istanze di macchine virtuali dedicate per ogni rete virtuale del cliente  |
| Supporto per il tunneling forzato| Sì |  |

## <a name="monitoring--logging"></a>Monitoraggio della registrazione dei &

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per il monitoraggio di Azure (analisi dei log, informazioni dettagliate sulle app e così via)Azure monitoring support (Log analytics, App insights, etc.)| Sì | Vedere Registri di diagnostica di [Monitoraggio di Azure/avviso](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md) & [Metriche/avviso di Monitoraggio di Azure.](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)  |
| Controllo e gestione del controllo e dell'audit degli aerei| Sì | Azure Resource Manager Activity Log. |
| Registrazione e controllo dei piani dati | Sì | [Log di diagnostica](../azure-resource-manager/management/view-activity-logs.md) di monitoraggio di Azure per la registrazione e il controllo della connettività VPN. |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) per la gestione del servizio e la configurazione del gateway VPN di Azure.Azure Active Directory for managing the service and configuring the Azure VPN gateway. |
| Autorizzazione| Sì | Supportare l'autorizzazione tramite [RBAC](../role-based-access-control/overview.md). |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server inattivi: chiavi gestite da MicrosoftServer-side encryption at rest: Microsoft-managed keys | N/D | Dati dei clienti di transito del gateway VPN, NON archivia i dati dei clienti |
| Crittografia in transito (ad esempio crittografia ExpressRoute, nella crittografia della rete virtuale e crittografia VNet-VNet )| Sì | Il gateway VPN crittografa i pacchetti dei clienti tra i gateway VPN di Azure e i dispositivi VPN locali (S2S) o i client VPN (P2S) del cliente. I gateway VPN supportano anche la crittografia da VNet a VNet. |
| Crittografia lato server inattivi: chiavi gestite dal cliente (BYOK)Server-side encryption at rest: customer-managed keys (BYOK) | No | Le chiavi precondivise specificate dal cliente vengono crittografate inattivi; ma non ancora integrato con CMK. |
| Crittografia a livello di colonna (Azure Data Services)Column level encryption (Azure Data Services)| N/D | |
| Chiamate API crittografate| Sì | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS  |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Per le operazioni di gestione, lo stato della configurazione di un gateway VPN di Azure può essere esportato come modello di Azure Resource Manager e sottoposto a controllo delle versioni nel tempo. |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui controlli di sicurezza incorporati nei servizi di Azure.Learn more about the [built-in security controls across Azure services](../security/fundamentals/security-controls.md).
