---
title: Come integrare lo schema di avviso comune con le app per la logica
description: Informazioni su come creare un'app per la logica che sfrutta lo schema di avviso comune per gestire tutti gli avvisi.
author: ananthradhakrishnan
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/27/2019
ms.author: anantr
ms.subservice: alerts
ms.openlocfilehash: f431e5e5f4537d1a5f889457eb81b881e47ee178
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71091793"
---
# <a name="how-to-integrate-the-common-alert-schema-with-logic-apps"></a>Come integrare lo schema di avviso comune con le app per la logica

Questo articolo illustra come creare un'app per la logica che sfrutta lo schema di avviso comune per gestire tutti gli avvisi.

## <a name="overview"></a>Panoramica

Lo [schema di avviso comune](https://aka.ms/commonAlertSchemaDocs) fornisce uno schema JSON standardizzato ed estendibile in tutti i diversi tipi di avviso. Lo schema di avviso comune è particolarmente utile quando viene usato a livello di codice, tramite webhook, manuali operativi e app per la logica. Questo articolo illustra come è possibile creare una singola app per la logica per gestire tutti gli avvisi. Gli stessi principi possono essere applicati ad altri metodi a livello di codice. L'app per la logica descritta in questo articolo crea variabili ben definite per i [campi ' Essential '](alerts-common-schema-definitions.md#essentials)e descrive anche come è possibile gestire la logica specifica del [tipo di avviso](alerts-common-schema-definitions.md#alert-context) .


## <a name="prerequisites"></a>Prerequisiti 

In questo articolo si presuppone che il lettore abbia familiarità con 
* Impostazione delle regole di avviso ([metrica](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric), [log](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log), [log attività](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log))
* Configurazione di [gruppi di azioni](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups)
* Abilitazione dello [schema di avviso comune](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-common-schema#how-do-i-enable-the-common-alert-schema) dall'interno di gruppi di azioni

## <a name="create-a-logic-app-leveraging-the-common-alert-schema"></a>Creare un'app per la logica usando lo schema di avviso comune

1. Seguire i [passaggi descritti per creare l'app per la logica](https://docs.microsoft.com/azure/azure-monitor/platform/action-groups-logic-app). 

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

1. In questa fase è possibile aggiungere un'ampia gamma di connettori (Microsoft teams, Slack, Salesforce e così via) in base ai requisiti aziendali specifici. È possibile usare il campo "Essential Fields". 

    ![Campi essenziali](media/alerts-common-schema-integrations/logic-app-essential-fields.png "Campi essenziali")
    
    In alternativa, è possibile creare la logica condizionale basata sul tipo di avviso utilizzando l'opzione ' Expression '.

    ![Espressione app] per la logica (media/alerts-common-schema-integrations/logic-app-expressions.png "Espressione app") per la logica
    
     Il [campo ' monitoringService '](alerts-common-schema-definitions.md#alert-context) consente di identificare in modo univoco il tipo di avviso, in base al quale è possibile creare la logica condizionale.

    
    Il frammento di codice seguente, ad esempio, controlla se l'avviso è un avviso di log basato su Application Insights e, in caso affermativo, stampa i risultati della ricerca. In caso contrario, stampa "NA".

    ```text
      if(equals(triggerBody()?['data']?['essentials']?['monitoringService'],'Application Insights'),triggerBody()?['data']?['alertContext']?['SearchResults'],'NA')
    ```
    
     Altre informazioni sulla [scrittura di espressioni di app](https://docs.microsoft.com/azure/logic-apps/workflow-definition-language-functions-reference#logical-comparison-functions)per la logica.

    


## <a name="next-steps"></a>Passaggi successivi

* [Altre informazioni sui gruppi di azioni](../../azure-monitor/platform/action-groups.md).
* [Altre informazioni sullo schema di avviso comune](https://aka.ms/commonAlertSchemaDocs).

