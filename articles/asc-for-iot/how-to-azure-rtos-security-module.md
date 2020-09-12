---
title: Configurare e personalizzare il modulo di sicurezza per Azure RTO
description: Informazioni su come configurare e personalizzare il modulo di sicurezza per Azure RTO.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/06/2020
ms.author: mlottner
ms.openlocfilehash: eed0422f574e54ff6d7df486b4929850a26418fa
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514871"
---
# <a name="configure-and-customize-security-module-for-azure-rtos-preview"></a>Configurare e personalizzare il modulo di sicurezza per Azure RTO (anteprima)

Usare il file seguente per configurare il comportamento del dispositivo.

## <a name="azure_iot_security_moduleincasc_porth"></a>azure_iot_security_module/Inc/asc_port. h

 Il comportamento predefinito di ogni configurazione è disponibile nelle tabelle seguenti: 

### <a name="general"></a>Generale

| Nome | Type | Predefinito | Dettagli |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | string | --- | Identificatore univoco del dispositivo  |
| ASC_SECURITY_MODULE_PENDING_TIME  | Number | 300 | Tempo di attesa del modulo di sicurezza in secondi. Se il tempo supera la modifica dello stato da sospendere. |

#### <a name="collection"></a>Collection (Raccolta)

| Nome | Type | Predefinito | Dettagli |
| - | - | - | - |
| ASC_HIGH_PRIORITY_INTERVAL | Number | 10 | Intervallo di gruppi con priorità alta in secondi per gli agenti di raccolta. |
| ASC_MEDIUM_PRIORITY_INTERVAL | Number | 30 | Intervallo del gruppo di priorità media degli agenti di raccolta in secondi. |
| ASC_LOW_PRIORITY_INTERVAL | Number | 145.440  | Intervallo in secondi per il gruppo di agenti di raccolta con priorità bassa. |

#### <a name="collector-network-activity"></a>Attività di rete dell'agente di raccolta

Per personalizzare la configurazione dell'attività di rete dell'agente di raccolta, usare quanto segue:

| Nome | Type | Predefinito | Dettagli |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | Boolean | false | Filtrare l' `TCP` attività di rete |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | Boolean | false | Filtrare `UDP` gli eventi di attività di rete |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | Boolean | false | Filtrare `ICMP` gli eventi di attività di rete |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | Boolean | true | Acquisisci solo i pacchetti in ingresso unicast, quando è impostato su false Capture anche broadcast e multicast |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | Number | 64 | Numero massimo di eventi di rete IPv4 da archiviare in memoria |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | Number | 64  | Numero massimo di eventi di rete IPv6 da archiviare in memoria |


## <a name="compile-flags"></a>Flag di compilazione
I flag di compilazione consentono di eseguire l'override delle configurazioni predefinite.

### <a name="collectors"></a>Raccolta
| Nome | Type | Predefinito | Dettagli |
| - | - | - | - |
| collector_heartbeat_enabled | Boolean | ON | Abilitare l'agente di raccolta heartbeat |
| collector_network_activity_enabled | Boolean | ON | Abilitare l'agente di raccolta attività di rete |
| collector_system_information_enabled | Boolean | ON | Abilitare l'agente di raccolta informazioni di sistema |

## <a name="supported-security-alerts-and-recommendations"></a>Avvisi e raccomandazioni di sicurezza supportati

Il modulo Security per Azure RTO supporta avvisi e raccomandazioni di sicurezza specifici. Assicurarsi di [rivedere e personalizzare i valori di avviso e raccomandazione pertinenti](concept-rtos-security-alerts-recommendations.md) per il servizio.

## <a name="log-analytics-optional"></a>Log Analytics (facoltativo)

Sebbene sia facoltativo che non necessario, l'abilitazione e la configurazione di Log Analytics possono essere utili quando si desidera esaminare ulteriormente gli eventi e le attività del dispositivo. Per altre informazioni, vedere la pagina relativa alla configurazione e all'uso di [log Analytics con il Centro sicurezza di Azure per il servizio](how-to-security-data-access.md#log-analytics) Internet delle cose. 

## <a name="next-steps"></a>Passaggi successivi

- Esaminare e personalizzare il modulo di sicurezza per gli [avvisi di sicurezza e le raccomandazioni](concept-rtos-security-alerts-recommendations.md) di Azure RTO
- Vedere il [modulo di sicurezza per l'API RTO di Azure](azure-rtos-security-module-api.md) in base alle esigenze.

