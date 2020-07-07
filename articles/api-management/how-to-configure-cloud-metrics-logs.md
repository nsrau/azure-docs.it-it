---
title: Configurare le metriche e i log cloud per il gateway self-hosted di gestione API di Azure | Microsoft Docs
description: Informazioni su come configurare le metriche e i log cloud per il gateway self-hosted di gestione API di Azure
services: api-management
documentationcenter: ''
author: miaojiang
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 04/30/2020
ms.author: apimpm
ms.openlocfilehash: d0fbdcb877e91a703306f15fdc7507fd19d534f4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82205131"
---
# <a name="configure-cloud-metrics-and-logs-for-azure-api-management-self-hosted-gateway"></a>Configurare le metriche e i log cloud per il gateway self-hosted di gestione API di Azure

Questo articolo fornisce informazioni dettagliate per la configurazione delle metriche e dei log del cloud per il [gateway self-hosted](./self-hosted-gateway-overview.md).

Il gateway self-hosted deve essere associato a un servizio gestione API e richiede la connettività TCP/IP in uscita ad Azure sulla porta 443. Il gateway sfrutta la connessione in uscita per inviare i dati di telemetria ad Azure, se configurati. 

## <a name="metrics"></a>Metriche
Per impostazione predefinita, il gateway self-hosted genera una serie di metriche attraverso [monitoraggio di Azure](https://azure.microsoft.com/services/monitor/), come il gateway gestito [nel cloud](api-management-howto-use-azure-monitor.md). 

La funzionalità può essere abilitata o disabilitata usando la `telemetry.metrics.cloud` chiave nella ConfigMap della distribuzione del gateway. Di seguito è riportata una suddivisione delle configurazioni disponibili:

| Campo  | Predefinito | Descrizione |
| ------------- | ------------- | ------------- |
| telemetria. Metrics. cloud  | `true` | Abilita la registrazione tramite monitoraggio di Azure. Il valore può `true` essere `false` . |


Ecco una configurazione di esempio:

```yaml
    apiVersion: v1
    kind: ConfigMap
    metadata:
        name: contoso-gateway-environment
    data:
        config.service.endpoint: "<contoso-gateway-management-endpoint>"
        telemetry.metrics.cloud: "true"
```

Il gateway self-hosted emette attualmente le metriche seguenti tramite monitoraggio di Azure:

| Metrica  | Descrizione |
| ------------- | ------------- |
| Requests  | Numero di richieste API nel periodo |
| Durata delle richieste del gateway | Numero di millisecondi dal momento in cui il gateway ha ricevuto la richiesta al momento dell'invio della risposta completa |
| Durata delle richieste back-end | Numero di millisecondi impiegati complessivamente per l'I/O del back-end (connessione, invio e ricezione byte)  |

## <a name="logs"></a>Log

Il gateway self-hosted attualmente non invia [log di diagnostica](https://docs.microsoft.com/azure/api-management/api-management-howto-use-azure-monitor#diagnostic-logs) al cloud. Tuttavia, è possibile [configurare e salvare](how-to-configure-local-metrics-logs.md) in modo permanente i log in locale in cui viene distribuito il gateway self-hosted. 

Se un gateway viene distribuito nel [servizio Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/), è possibile abilitare [monitoraggio di Azure per i contenitori](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) per raccogliere i log dai contenitori e visualizzarli in log Analytics. 


## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul gateway self-hosted, vedere [Panoramica del gateway self-hosted di gestione API di Azure](self-hosted-gateway-overview.md)
* Informazioni sulla [configurazione e la permanenza dei log in locale](how-to-configure-local-metrics-logs.md)


