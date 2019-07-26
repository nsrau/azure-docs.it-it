---
title: Attributi di sicurezza per l'inoltro del bus di servizio di Azure
description: Elenco di controllo degli attributi di sicurezza per la valutazione di inoltro del bus di servizio di Azure
services: service-bus-relay
ms.service: service-bus-relay
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: mbaldwin
ms.openlocfilehash: 2047f64914d4a286e6de38b7b2c8524d98eba562
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68443876"
---
# <a name="security-attributes-for-azure-service-bus-relay"></a>Attributi di sicurezza per l'inoltro del bus di servizio di Azure

Questo articolo descrive gli attributi di sicurezza incorporati nell'inoltro del bus di servizio di Azure.

[!INCLUDE [Security Attributes Header](../../includes/security-attributes-header.md)]

## <a name="preventative"></a>Prevenzione

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Crittografia dei servizi inattivi, ad esempio la crittografia lato server, la crittografia lato server con chiavi gestite dal cliente e altre funzionalità di crittografia|  N/D | L'inoltro è un socket Web e non rende persistenti i dati. |
| Crittografia in transito (ad esempio crittografia ExpressRoute, crittografia VNet e crittografia VNet-VNet)| Sì | Il servizio richiede TLS. |
| Gestione della chiave di crittografia (CMK, BYOK e così via)| No | USA solo certificati TLS Microsoft.  |
| Crittografia a livello di colonna (servizi dati di Azure)| N/D | |
| Chiamate API crittografate| Sì | HTTPS. |

## <a name="network-segmentation"></a>Segmentazione di rete

| Attributo di sicurezza | Sì/No | Note |
|---|---|--|
| Supporto per endpoint di servizio| No |  |
| Isolamento rete e supporto del firewall| No |  |
| Supporto del tunneling forzato| N/D | L'inoltro è il tunnel TLS  |

## <a name="detection"></a>Rilevamento

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto di monitoraggio di Azure (log Analytics, Application Insights e così via)| Sì | |

## <a name="identity-and-access-management"></a>Gestione delle identità e dell'accesso

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Authentication| Yes | Tramite SAS. |
| Authorization|  Sì | Tramite SAS. |


## <a name="audit-trail"></a>Audit trail

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Registrazione e controllo del piano di gestione e controllo| Yes | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml). |
| Registrazione e controllo del piano dati| Sì | Connessione riuscita/non riuscita ed errori e registrato.  |

## <a name="configuration-management"></a>Gestione della configurazione

| Attributo di sicurezza | Sì/No | Note|
|---|---|--|
| Supporto per la gestione della configurazione (controllo delle versioni della configurazione e così via)| Sì | Tramite [Azure Resource Manager](../azure-resource-manager/index.yml).|
