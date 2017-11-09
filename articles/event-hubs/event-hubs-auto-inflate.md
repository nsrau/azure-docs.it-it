---
title: "Aumentare automaticamente le unità elaborate di Hub eventi di Azure | Microsoft Docs"
description: "Abilitare Aumento automatico in uno spazio dei nomi per aumentare le unità elaborate"
services: event-hubs
documentationcenter: na
author: ShubhaVijayasarathy
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/05/2017
ms.author: sethm
ms.openlocfilehash: 1cd31e0866ee6088483f88e8f80d01f75764c771
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="automatically-scale-up-azure-event-hubs-throughput-units"></a>Aumentare automaticamente le unità elaborate di Hub eventi di Azure

Hub eventi di Azure è una piattaforma di streaming dei dati altamente scalabile. In quanto tale, i clienti di Hub eventi aumentano spesso il loro uso dopo l'onboarding al servizio. Questi aumenti richiedono un aumento delle unità elaborate predeterminate per scalare Hub eventi e gestire velocità di trasferimento più alte. La funzionalità *Aumento automatico* di Hub eventi aumenta automaticamente il numero di unità elaborate per soddisfare le esigenze di utilizzo. L'aumento delle unità elaborate previene scenari di limitazione in cui:

* Le velocità di ingresso dei dati superano le unità elaborate impostate.
* Le velocità di richiesta dei dati in uscita superano le unità elaborate impostate.

## <a name="how-auto-inflate-works"></a>Funzionamento di Aumento automatico

Il traffico di Hub eventi è controllato tramite le unità elaborate. Una singola unità elaborata consente 1 MB al secondo in ingresso e il doppio in uscita. Gli Hub eventi standard possono essere configurati con 1-20 unità elaborate. Aumento automatico consente di iniziare gradualmente con il minimo di unità elaborate richiesto. La funzionalità aumenta quindi automaticamente le unità elaborate fino al limite massimo necessario, a seconda dell'aumento del traffico. Aumento automatico offre i seguenti vantaggi:

- Un meccanismo di scala efficiente per iniziare con poche unità elaborate e aumentarle al bisogno.
- Aumenta automaticamente le unità elaborate fino al limite superiore specificato senza problemi di limitazioni.
- Offre maggiore controllo sulle dimensioni perché consente di specificare quando e come gestire l'aumento.

## <a name="enable-auto-inflate-on-a-namespace"></a>Abilitare Aumento automatico in uno spazio dei nomi

È possibile abilitare o disabilitare Aumento automatico in uno spazio dei nomi usando uno dei seguenti metodi:

1. Il [portale di Azure](https://portal.azure.com).
2. Un modello di Azure Resource Manager.

### <a name="enable-auto-inflate-through-the-portal"></a>Abilitare Aumento automatico tramite il portale

È possibile abilitare la funzionalità Aumento automatico in uno spazio dei nomi quando si crea uno spazio dei nomi Hub eventi:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate1.png)

Dopo aver abilitato questa opzione, è possibile iniziare con un numero ridotto di unità elaborate e aumentarle in funzione delle esigenze di utilizzo. Il limite superiore per l'aumento non influisce sul prezzo che dipende dal numero di unità elaborate usate all'ora.

È possibile anche abilitare la funzionalità Aumento automatico usando l'opzione di **ridimensionamento** nel pannello Impostazioni del portale:
 
![](./media/event-hubs-auto-inflate/event-hubs-auto-inflate2.png)

### <a name="enable-auto-inflate-using-an-azure-resource-manager-template"></a>Abilitare Aumento automatico usando un modello di Azure Resource Manager

È possibile abilitare Aumento automatico durante la distribuzione di un modello di Azure Resource Manager. Ad esempio, impostare la proprietà `isAutoInflateEnabled` su **true**, quindi impostare `maximumThroughputUnits` su 10.

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
* [Create an Event Hub](event-hubs-create.md) (Creare un Hub eventi)
