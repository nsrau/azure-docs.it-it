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
ms.openlocfilehash: 3bbfd167e89ae1b5f9b7de1df5fd1cb72c720cb6
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2020
ms.locfileid: "86254530"
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

Il gateway self-hosted attualmente non invia [log di diagnostica](./api-management-howto-use-azure-monitor.md#activity-logs) al cloud. Tuttavia, è possibile [configurare e salvare](how-to-configure-local-metrics-logs.md) in modo permanente i log in locale in cui viene distribuito il gateway self-hosted. 

Se un gateway viene distribuito nel [servizio Azure Kubernetes](https://azure.microsoft.com/services/kubernetes-service/), è possibile abilitare [monitoraggio di Azure per i contenitori](../azure-monitor/insights/container-insights-overview.md) per raccogliere i log dai contenitori e visualizzarli in log Analytics. 


## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sul gateway self-hosted, vedere [Panoramica del gateway self-hosted di gestione API di Azure](self-hosted-gateway-overview.md)
* Informazioni sulla [configurazione e la permanenza dei log in locale](how-to-configure-local-metrics-logs.md)
