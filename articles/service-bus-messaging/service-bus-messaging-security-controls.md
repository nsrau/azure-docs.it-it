---
title: Controlli di sicurezza per la messaggistica del bus di servizio di Azure
description: Elenco di controllo dei controlli di sicurezza per la valutazione della messaggistica del bus di servizio di Azure
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: 3130150a227076befae3f58f65e00a36578b68d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85341623"
---
# <a name="security-controls-for-azure-service-bus-messaging"></a>Controlli di sicurezza per la messaggistica del bus di servizio di Azure

Questo articolo descrive i controlli di sicurezza incorporati nella messaggistica del bus di servizio di Azure.

[!INCLUDE [Security controls Header](../../includes/security-controls-header.md)]

## <a name="network"></a>Rete

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Supporto endpoint di servizio| Sì (solo livello Premium) | Gli endpoint del servizio VNet sono supportati solo per il [livello Premium del bus di servizio](service-bus-premium-messaging.md) . |  |
| Supporto aggiunta rete virtuale| No | |  |
| Isolamento rete e supporto del firewall| Sì (solo livello Premium) |  |  |
| Supporto del tunneling forzato| No |  |  |

## <a name="monitoring--logging"></a>Monitoraggio e registrazione

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto monitoraggio di Azure (analisi dei log, analisi approfondita dell'app e così via)| Sì | Supportato tramite [monitoraggio e avvisi di Azure](service-bus-metrics-azure-monitor.md). |  |
| Piano di gestione e controllo - Registrazione e controllo| Sì | I log delle operazioni sono disponibili.  | [Log di diagnostica del bus di servizio](service-bus-diagnostic-logs.md) |
| Piano dati - Registrazione e controllo| No |  |

## <a name="identity"></a>Identità

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Authentication| Sì | Gestito tramite [Azure Active Directory identità del servizio gestita](service-bus-managed-service-identity.md).| [Autenticazione e autorizzazione del bus di servizio](service-bus-authentication-and-authorization.md). |
| Autorizzazione| Sì | Supporta l'autorizzazione tramite il controllo [RBAC](authenticate-application.md) e il token SAS. | [Autenticazione e autorizzazione del bus di servizio](service-bus-authentication-and-authorization.md). |

## <a name="data-protection"></a>Protezione dei dati

| Controllo di sicurezza | Sì/No | Note | Documentazione |
|---|---|--|--|
| Crittografia lato server dei dati inattivi: chiavi gestite da Microsoft |  Sì per la crittografia lato server per impostazione predefinita. |  |  |
| Crittografia lato server dei dati inattivi: chiavi gestite dal cliente (BYOK) | Sì. | Una chiave gestita dal cliente nell'insieme di credenziali delle chiavi di Azure può essere usata per crittografare i dati nello spazio dei nomi del bus di servizio inattivi. | [Configurare chiavi gestite dal cliente per la crittografia dei dati del bus di servizio di Azure inattivi usando il portale di Azure](configure-customer-managed-key.md)  |
| Crittografia a livello di colonna (Servizi dati di Azure)| N/D | |   |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Supporta il meccanismo HTTPS/TLS standard. |   |
| Chiamate API crittografate| Sì | Le chiamate API vengono eseguite tramite [Azure Resource Manager](../azure-resource-manager/index.yml) e HTTPS. |   |

## <a name="configuration-management"></a>Gestione della configurazione

| Controllo di sicurezza | Sì/No | Note| Documentazione |
|---|---|--|--|
| Supporto gestione della configurazione (controllo delle versioni di configurazione e così via)| Sì | Supporta il controllo delle versioni del provider di risorse tramite l' [API Azure Resource Manager](/rest/api/resources/).|   |

## <a name="next-steps"></a>Passaggi successivi

- Maggiori informazioni sui [controlli di sicurezza incorporati nei servizi di Azure](../security/fundamentals/security-controls.md).
