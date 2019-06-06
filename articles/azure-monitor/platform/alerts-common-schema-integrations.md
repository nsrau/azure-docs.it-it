---
title: Come integrare schema comune di avviso con App per la logica
description: Informazioni su come creare un'app per la logica che utilizza lo schema comune degli avvisi per gestire tutti gli avvisi.
author: ananthradhakrishnan
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: 13cb3880662e1665b03dd63f009645acbe97fc75
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734896"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Come integrare schema comune di avviso con App per la logica

Questo articolo illustra come creare un'app per la logica che utilizza lo schema comune degli avvisi per gestire tutti gli avvisi.

## <a name="overview"></a>Panoramica

Il [common schema avviso](https://aka.ms/commonAlertSchemaDocs) fornisce uno schema JSON standardizzato ed estensibile in tutti i diversi tipi di avviso. Lo schema comune degli avvisi è molto utile se usato a livello di codice, tramite i webhook, runbook e le App per la logica. In questo articolo viene illustrato come un'app per la logica singolo può essere creata per gestire tutti gli avvisi. Possono applicare gli stessi principi ad altri metodi a livello di codice. L'app per la logica descritta in questo articolo consente di creare variabili ben definite per il [campi 'essenziali'](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema-definitions#essentials-fields)e viene inoltre descritto come gestire [tipo di avviso](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) specifici per la logica.


## <a name="prerequisites"></a>Prerequisiti 

Questo articolo si presuppone che il lettore abbia familiarità con 
* Configurare le regole di avviso ([metric](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [log](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [log attività](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* Configurazione di [gruppi di azioni](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Abilitazione di [common schema avviso](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) all'interno di gruppi di azioni

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Creare un'app per la logica usando lo schema comune degli avvisi

1. Seguire le [descritto in questa procedura per creare app per la logica](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

1.  Selezionare il trigger: **Alla ricezione di una richiesta HTTP**.

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

1. Selezionare **+** **Nuovo passaggio** e quindi selezionare **Aggiungi un'azione**.

    ![Aggiungere un'azione](media/action-groups-logic-app/add-action.png "Aggiungere un'azione")

1. In questa fase, è possibile aggiungere un'ampia gamma di connettori (Microsoft Teams, Slack, Salesforce e così via) in base alle esigenze aziendali specifiche. È possibile usare il 'campi fondamentali' out-of-the-box. 

    ![Campi fondamentali](media/alerts-common-schema-integrations/logic-app-essential-fields.png "campi fondamentali")
    
    In alternativa, è possibile creare la logica condizionale in base al tipo di avviso tramite l'opzione 'Expression'.

    ![Espressione di app per la logica](media/alerts-common-schema-integrations/logic-app-expressions.png "espressione di app per la logica")
    
     Il [campo 'monitoringService'](/azure/azure-monitor/platform/alerts-common-schema-definitions#alert-context-fields) consente di identificare in modo univoco il tipo di avviso basate su cui è possibile creare la logica condizionale.

    
    Ad esempio, il seguente frammento di codice controlla se l'avviso è un avviso di log di Application Insights in base e caso in questo consente di stampare i risultati della ricerca. In caso contrario, viene stampato "ND".

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Altre informazioni sulle [scrittura di espressioni di app per la logica](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions).

    


## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sui gruppi di azioni](../../azure-monitor/platform/action-groups.md).
* [Altre informazioni sullo schema di avviso comune](https://aka.ms/commonAlertSchemaDocs).

