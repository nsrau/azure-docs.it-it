---
title: Schema degli eventi del log di scalabilità automatica di Azure
description: Formato dei log per il monitoraggio e la risoluzione dei problemi delle azioni di scalabilità automatica
author: rboucher
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: robb
ms.subservice: autoscale
ms.openlocfilehash: 8b99cec69b05fc0f78dcf813e67e6b65ba50d819
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152931"
---
# <a name="azure-monitor-autoscale-actions-resource-log-schema"></a>Schema del log delle risorse azioni di scalabilità automatica di monitoraggio di Azure

Di seguito sono riportati i formati generali per i log delle risorse di scalabilità automatica con dati di esempio inclusi. Non tutti gli esempi riportati di seguito sono JSON in formato corretto perché possono includere più valori che potrebbero essere validi per un determinato campo. 

Usare eventi di questo tipo per risolvere i problemi che potrebbero verificarsi con la scalabilità automatica. Per ulteriori informazioni, vedere [risoluzione dei problemi di scalabilità](autoscale-troubleshoot.md)automatica.


## <a name="profile-evaluation"></a>Valutazione del profilo

Registrato quando il ridimensionamento automatico esamina prima di tutto un profilo di scalabilità automatica

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": ["FixedDateProfileEvaluation", "RecurrentProfileEvaluation", "DefaultProfileEvaluation"],
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "profile": "defaultProfile",
    "profileSelected": [true, false]
  }
}
```

## <a name="profile-cooldown-evaluation"></a>Valutazione del cooldown del profilo

Registrato quando la scalabilità automatica valuta se non deve eseguire una scala a causa di un periodo di raffreddamento. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "ScaleRuleCooldownEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "selectedProfile": "defaultProfile",
    "scaleDirection": ["Increase", "Decrease"]
    "lastScaleActionTime": "2018-09-10 18:08:00.6132593",
    "cooldown": "00:30:00",
    "evaluationTime": "2018-09-10 18:11:00.6132593",
    "skipRuleEvaluationForCooldown": true
  }
}
```

## <a name="rule-evaluation"></a>Valutazione della regola

Registrato quando la scalabilità automatica inizia a valutare una determinata regola di scalabilità. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "ScaleRuleEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "timeGrain": "00:01:00",
    "timeGrainStatistic": "Average",
    "timeWindow": "00:10:00",
    "timeAggregationType": "Average",
    "operator": "GreaterThan",
    "threshold": 70,
    "observedValue": 25,
    "estimateScaleResult": ["Triggered", "NotTriggered", "Unknown"]
  }
}
```

## <a name="metric-evaluation"></a>Valutazione metrica

Registrato quando il ridimensionamento automatico valuta la metrica usata per attivare un'azione di ridimensionamento. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "MetricEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "metricName": "Percentage CPU",
    "metricNamespace": "",
    "timeGrain": "00:01:00",
    "timeGrainStatistic": "Average",
    "startTime": "2018-09-10 18:00:00.43833793",
    "endTime": "2018-09-10 18:10:00.43833793",
    "data": [0.33333333333333331,0.16666666666666666,1.0,0.33333333333333331,2.0,0.16666666666666666,9.5]
  }
}
```

## <a name="instance-count-evaluation"></a>Valutazione del conteggio delle istanze

Registrato quando il ridimensionamento automatico valuta il numero di istanze già in esecuzione in preparazione per decidere se deve iniziare più, arrestare alcune o non eseguire alcuna operazione. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "InstanceCountEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "currentInstanceCount": 20,
    "minimumInstanceCount": 15,
    "maximumInstanceCount": 30,
    "defaultInstanceCount": 20
  }
}
```

## <a name="scale-action-evaluation"></a>Valutazione dell'azione di ridimensionamento

Registrato quando la scalabilità automatica avvia la valutazione se deve essere eseguita un'azione di ridimensionamento. 

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "ScaleActionOperationEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "lastScaleActionOperationId": "378ejr-7yye-892d-17dd-92ndijfe1738",
    "lastScaleActionOperationStatus": ["InProgress", "Timeout"]
    "skipCurrentAutoscaleEvaluation": [true, false]
  }
}
```

## <a name="instance-update-evaluation"></a>Valutazione dell'aggiornamento dell'istanza

Registrato quando il ridimensionamento automatico aggiorna il numero di istanze di calcolo in esecuzione, sia verso l'alto che verso il basso.

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "InstanceUpdateEvaluation",
  "category": "AutoscaleEvaluations",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "currentInstanceCount": 20,
    "newInstanceCount": 21,
    "shouldUpdateInstance": [true, false],
    "reason": ["Scale down action triggered", "Scale up to default instance count", ...]
  }
}
```

## <a name="scale-action"></a>Azione di ridimensionamento

Registrato quando la scalabilità automatica avvia un'azione di ridimensionamento, sia verso l'alto o verso il basso. 
```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "InstanceScaleAction",
  "category": "AutoscaleScaleActions",
  "resultType": ["Succeeded", "InProgress", "Failed"],
  "resultDescription": ["Create async operation job failed", ...]
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "currentInstanceCount": 20,
    "newInstanceCount": 21,
    "scaleDirection": ["Increase", "Decrease"],
    ["createdAsyncScaleActionJob": [true, false],]
    ["createdAsyncScaleActionJobId": "378ejr-7yye-892d-17dd-92ndijfe1738",]
  }
}
```

## <a name="scale-action-tracking"></a>Ridimensionamento del rilevamento delle azioni

Registrato a intervalli diversi di un'azione di ridimensionamento dell'istanza.

```json
{
  "time": "2018-09-10 18:12:00.6132593",
  "resourceId": "/SUBSCRIPTIONS/BA13C41D-C957-4774-8A37-092D62ACFC85/RESOURCEGROUPS/AUTOSCALETRACKING12042017/PROVIDERS/MICROSOFT.INSIGHTS/AUTOSCALESETTINGS/DEFAULTSETTING",
  "operationName": "InstanceScaleAction",
  "category": "AutoscaleScaleActions",
  "correlationId": "e8f67045-f381-445d-bc2d-eeff81ec0d77",
  "property": {
    "targetResourceId": "/subscriptions/d45c994a-809b-4cb3-a952-e75f8c488d23/resourceGroups/RingAhoy/providers/Microsoft.Web/serverfarms/ringahoy",
    "scaleActionOperationId": "378ejr-7yye-892d-17dd-92ndijfe1738",
    "scaleActionOperationStatus": ["InProgress", "Timeout", "Canceled", ...],
    "scaleActionMessage": ["Scale action is inprogress", ...]
  }
}
```

## <a name="next-steps"></a>Passaggi successivi
Informazioni sulla [scalabilità](autoscale-overview.md) automatica