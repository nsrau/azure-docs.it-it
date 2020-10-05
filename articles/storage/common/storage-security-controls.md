---
title: Controlli di sicurezza
titleSuffix: Azure Storage
description: Visualizzare gli elenchi di controllo di sicurezza per la valutazione di archiviazione di Azure. Le aree interessate sono la protezione dei dati, la rete, il monitoraggio e la registrazione, l'identità e la configurazione.
services: storage
author: msmbaldwin
ms.author: mbaldwin
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 3b4d74e7ba869e0438a936817d824e841823d472
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "91715707"
---
# <a name="security-controls-for-azure-storage"></a>Controlli di sicurezza per archiviazione di Azure

Questo articolo descrive i controlli di sicurezza incorporati in archiviazione di Azure.

[!INCLUDE [Security controls Header](../../../includes/security-controls-header.md)]

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia lato server dei dati inattivi: chiavi gestite da Microsoft | Sì |  |
| Crittografia lato server dei dati inattivi: chiavi gestite dal cliente (BYOK) | Sì | Vedere [crittografia del servizio di archiviazione uso delle chiavi gestite dal cliente nel Azure Key Vault](storage-service-encryption-customer-managed-keys.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).|
| Crittografia a livello di colonna (Servizi dati di Azure)| N/D |  |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Supporta meccanismi HTTPS/TLS standard.  Gli utenti possono inoltre crittografare i dati prima che vengano trasmessi al servizio. |
| Chiamate API crittografate| Sì |  |

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto endpoint di servizio| Sì |  |
| Supporto per i tag di servizio| Sì | Vedere [Cenni preliminari sui tag dei servizi di Azure](../../virtual-network/service-tags-overview.md) per altre informazioni sui tag di servizio supportati da archiviazione di Azure. |
| Supporto aggiunta rete virtuale| N/D |  |
| Isolamento rete e supporto firewall| Sì | |
| Supporto del tunneling forzato| N/D |  |

## <a name="monitoring--logging"></a>Monitoraggio e registrazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto monitoraggio di Azure (analisi dei log, analisi approfondita dell'app e così via)| Sì | Metriche di Monitoraggio di Azure|
| Piano di gestione e controllo - Registrazione e controllo | Sì | Azure Activity Log |
| Piano dati - Registrazione e controllo| Sì | Log delle risorse di monitoraggio di Azure |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Sì | Azure Active Directory, chiave condivisa e token di accesso condiviso. |
| Autorizzazione| Sì | Supporto dell'autorizzazione tramite controllo degli accessi in base al ruolo di Azure, ACL POSIX e token SAS |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto gestione della configurazione (controllo delle versioni di configurazione e così via)| Sì | Supportare il controllo delle versioni del provider di risorse tramite API Azure Resource Manager |

## <a name="next-steps"></a>Passaggi successivi

- Maggiori informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../../security/fundamentals/security-controls.md).