---
title: Come integrare lo schema di avviso comune con app per la logicaHow to integrate the common alert schema with Logic Apps
description: Informazioni su come creare un'app per la logica che sfrutta lo schema degli avvisi comune per gestire tutti gli avvisi.
ms.topic: conceptual
ms.subservice: alerts
ms.date: 05/27/2019
ms.openlocfilehash: 9042ed8ddbb698192e638fa7538f74561574c262
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77668231"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Come integrare lo schema di avviso comune con app per la logicaHow to integrate the common alert schema with Logic Apps

Questo articolo illustra come creare un'app per la logica che sfrutta lo schema degli avvisi comuni per gestire tutti gli avvisi.

## <a name="overview"></a>Panoramica

Lo schema di [avviso comune](https://aka.ms/commonAlertSchemaDocs) fornisce uno schema JSON standardizzato ed estensibile in tutti i diversi tipi di avviso. Lo schema di avviso comune è particolarmente utile quando viene sfruttato a livello di codice, tramite webhook, runbook e app per la logica. In questo articolo viene illustrato come creare una singola app per la logica per gestire tutti gli avvisi. Gli stessi principi possono essere applicati ad altri metodi programmatici. L'app per la logica descritta in questo articolo crea variabili ben definite per i [campi 'essenziali'](alerts-common-schema-definitions.md#essentials)e descrive anche come gestire la logica specifica del tipo di [avviso.](alerts-common-schema-definitions.md#alert-context)


## <a name="prerequisites"></a>Prerequisiti 

In questo articolo si presuppone che il lettore abbia familiarità con 
* Impostazione delle regole di avviso ([metrica](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [log](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [registro attività](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* Impostazione dei [gruppi di azioni](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Abilitazione dello schema di avviso comune dall'interno di gruppi di azioniEnabling the [common alert schema](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) from within action groups

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Creare un'app per la logica sfruttando lo schema di avviso comuneCreate a logic app leveraging the common alert schema

1. Seguire i [passaggi descritti per creare l'app per la logica](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Selezionare il trigger **Alla ricezione di una richiesta HTTP**.

    ![Trigger dell'app per la logica](media/action-groups-logic-app/logic-app-triggers.png "Trigger dell'app per la logica")

1.  Selezionare **Modifica** per modificare il trigger di richiesta HTTP.

    ![Trigger di richiesta HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "Trigger di richiesta HTTP")


1.  Copiare e incollare lo schema seguente:

    ```json
        {
            "type": "object",
            "properties": {
                "schemaId": {
                    "type": "string"
                },
                "data": {
                    "type": "object",
                    "properties": {
                        "essentials": {
                            "type": "object",
                            "properties": {
                                "alertId": {
                                    "type": "string"
                                },
                                "alertRule": {
                                    "type": "string"
                                },
                                "severity": {
                                    "type": "string"
                                },
                                "signalType": {
                                    "type": "string"
                                },
                                "monitorCondition": {
                                    "type": "string"
                                },
                                "monitoringService": {
                                    "type": "string"
                                },
                                "alertTargetIDs": {
                                    "type": "array",
                                    "items": {
                                        "type": "string"
                                    }
                                },
                                "originAlertId": {
                                    "type": "string"
                                },
                                "firedDateTime": {
                                    "type": "string"
                                },
                                "resolvedDateTime": {
                                    "type": "string"
                                },
                                "description": {
                                    "type": "string"
                                },
                                "essentialsVersion": {
                                    "type": "string"
                                },
                                "alertContextVersion": {
                                    "type": "string"
                                }
                            }
                        },
                        "alertContext": {
                            "type": "object",
                            "properties": {}
                        }
                    }
                }
            }
        }
    ```

1. Selezionare **+** **Nuovo passaggio** e quindi scegliere Aggiungi **un'azione**.

    ![Aggiungere un'azione](media/action-groups-logic-app/add-action.png "Aggiungere un'azione")

1. In questa fase, è possibile aggiungere una varietà di connettori (Microsoft Teams, Slack, Salesforce e così via) in base ai requisiti aziendali specifici. È possibile utilizzare i "campi essenziali" out-of-the-box. 

    ![Campi essenziali](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Campi essenziali")
    
    In alternativa, è possibile creare la logica condizionale in base al tipo di avviso utilizzando l'opzione 'Espressione'.

    ![Espressione dell'app per la logica](media/alerts-common-schema-integrations/logic-app-expressions.png "Espressione dell'app per la logica")
    
     Il [campo 'monitoringService'](alerts-common-schema-definitions.md#alert-context) consente di identificare in modo univoco il tipo di avviso, in base al quale è possibile creare la logica condizionale.

    
    Ad esempio, il frammento di codice seguente controlla se l'avviso è un avviso di log basato su Application Insights e, in tal caso, stampa i risultati della ricerca. Altrimenti, stampa 'NA'.

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Ulteriori informazioni sulla scrittura di [espressioni dell'app per la logica](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Passaggi successivi

* [Ulteriori informazioni sui gruppi di azioni](../../azure-monitor/platform/action-groups.md).
* [Ulteriori informazioni sullo schema](https://aka.ms/commonAlertSchemaDocs)di avviso comune .

