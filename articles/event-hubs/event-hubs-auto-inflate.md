---
title: Aumentare automaticamente le unità elaborate di Hub eventi di Azure | Microsoft Docs
description: Abilitare Aumento automatico in uno spazio dei nomi per aumentare le unità elaborate.
services: event-hubs
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2018
ms.author: sethm
ms.openlocfilehash: 46ed6acc14356221eaf24b03dfa37dc4c76efcbc
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/03/2018
ms.locfileid: "37434641"
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Aumentare automaticamente le unità elaborate di Hub eventi di Azure

Hub eventi di Azure è una piattaforma di streaming dei dati altamente scalabile. L'uso di Hub eventi, quindi, spesso aumenta dopo che si è iniziato a usare il servizio. Questo utilizzo richiede di aumentare le prestazioni di Hub eventi aumentando le [unità elaborate](event-hubs-features.md#throughput-units) predeterminate e di gestire velocità di trasferimento più alte. La funzionalità **Aumento automatico** di Hub eventi aumenta automaticamente le prestazioni aumentando il numero di unità elaborate per soddisfare le esigenze di utilizzo. L'aumento delle unità elaborate previene scenari di limitazione in cui:

* Le velocità di ingresso dei dati superano le unità elaborate impostate.
* Le velocità di richiesta dei dati in uscita superano le unità elaborate impostate.

## <a name="how-auto-inflate-works"></a>Funzionamento di Aumento automatico

Il traffico di Hub eventi è controllato dalle [unità elaborate](event-hubs-features.md#throughput-units). Una singola unità elaborata consente 1 MB al secondo in ingresso e il doppio in uscita. Gli hub eventi standard possono essere configurati con un numero di unità elaborate compreso tra 1 e 20. Aumento automatico consente di iniziare gradualmente con il minimo di unità elaborate richiesto a scelta. La funzionalità aumenta quindi automaticamente le unità elaborate fino al limite massimo necessario, a seconda dell'aumento del traffico. Aumento automatico offre i seguenti vantaggi:

- Un meccanismo di scala efficiente per iniziare con poche unità elaborate e aumentarle al bisogno.
- Aumenta automaticamente le unità elaborate fino al limite superiore specificato senza problemi di limitazioni.
- Offre maggiore controllo sull'aumento di prestazioni perché consente di specificare quando e come gestire l'aumento.

## <a name="enable-auto-inflate-on-a-namespace"></a>Abilitare Aumento automatico in uno spazio dei nomi

Per abilitare o disabilitare Aumento automatico in uno spazio dei nomi di Hub eventi, usare uno dei metodi seguenti:

- Il [portale di Azure](https://portal.azure.com).
- Un [modello di Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate).

### <a name="enable-auto-inflate-through-the-portal"></a>Abilitare Aumento automatico tramite il portale

È possibile abilitare la funzionalità Aumento automatico quando si crea uno spazio dei nomi di Hub eventi:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Dopo avere abilitato questa opzione, è possibile iniziare con un numero ridotto di unità elaborate e aumentarle in funzione delle esigenze di utilizzo. Il limite superiore per l'aumento non influisce immediatamente sui prezzi, che dipendono dal numero di unità elaborate usate all'ora.

È anche possibile abilitare la funzionalità Aumento automatico usando l'opzione **Ridimensiona** nel riquadro Impostazioni del portale:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Abilitare Aumento automatico usando un modello di Azure Resource Manager

È possibile abilitare Aumento automatico durante la distribuzione di un modello di Azure Resource Manager. Ad esempio, impostare la proprietà `isAutoInflateEnabled` su **true**, quindi impostare `maximumThroughputUnits` su 10. Ad esempio: 

```json
"resources": [
        {
            "apiVersion": "2017-04-01",
            "name": "[parameters('namespaceName')]",
            "type": "Microsoft.EventHub/Namespaces",
            "location": "[variables('location')]",
            "sku": {
                "name": "Standard",
                "tier": "Standard"
            },
            "properties": {
                "isAutoInflateEnabled": true,
                "maximumThroughputUnits": 10
            },
            "resources": [
                {
                    "apiVersion": "2017-04-01",
                    "name": "[parameters('eventHubName')]",
                    "type": "EventHubs",
                    "dependsOn": [
                        "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
                    ],
                    "properties": {},
                    "resources": [
                        {
                            "apiVersion": "2017-04-01",
                            "name": "[parameters('consumerGroupName')]",
                            "type": "ConsumerGroups",
                            "dependsOn": [
                                "[parameters('eventHubName')]"
                            ],
                            "properties": {}
                        }
                    ]
                }
            ]
        }
    ]
```

Per il modello completo, vedere il modello [Create Event Hubs namespace and enable inflate (Creare uno spazio dei nomi Hub eventi e abilitare l'aumento)](https://github.com/Azure/azure-quickstart-templates/tree/master/201-eventhubs-create-namespace-and-enable-inflate) in GitHub.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni su Hub eventi visitare i collegamenti seguenti:

* [Panoramica di Hub eventi](event-hubs-what-is-event-hubs.md)

