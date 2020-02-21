---
title: Controlli di sicurezza per archiviazione di Azure
description: Elenco di controllo dei controlli di sicurezza per la valutazione di archiviazione di Azure
services: storage
documentationcenter: ''
author: msmbaldwin
manager: rkarlin
ms.service: storage
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2cc54077456fce1e7e0f47843a762beee8e715f7
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/21/2020
ms.locfileid: "77526757"
---
# <a name="security-controls-for-azure-storage"></a>Controlli di sicurezza per archiviazione di Azure

Questo articolo descrive i controlli di sicurezza incorporati in archiviazione di Azure. 

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server: chiavi gestite da Microsoft | Sì |  |
| Crittografia lato server inattiva: chiavi gestite dal cliente (BYOK) | Sì | Vedere [crittografia del servizio di archiviazione uso delle chiavi gestite dal cliente nel Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Crittografia a livello di colonna (servizi dati di Azure)| N/D |  |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Supporta meccanismi HTTPS/TLS standard.  Gli utenti possono inoltre crittografare i dati prima che vengano trasmessi al servizio. |
| Chiamate API crittografate| Sì |  |

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| Sì |  |
| Supporto di VNet Injection| N/D |  |
| Isolamento rete e supporto del firewall| Sì | |
| Supporto del tunneling forzato| N/D |  |

## <a name="monitoring--logging"></a>Monitoraggio & registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | Metriche di monitoraggio di Azure|
| Registrazione e controllo del piano di gestione e controllo | Sì | Log attività Azure Resource Manager |
| Registrazione e controllo del piano dati| Sì | Log di diagnostica del servizio.|

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Azure Active Directory, chiave condivisa e token di accesso condiviso. |
| Autorizzazione| Sì | Supportare l'autorizzazione tramite RBAC, ACL POSIX e token SAS |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Supportare il controllo delle versioni del provider di risorse tramite API Azure Resource Manager |

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../../security/fundamentals/security-controls.md).