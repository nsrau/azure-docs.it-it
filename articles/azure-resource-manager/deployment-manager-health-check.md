---
title: Introdurre implementazione di integrazione dell'integrità di gestione di distribuzione di Azure
description: Descrive come distribuire un servizio in più aree con Azure Deployment Manager. Illustra le procedure di distribuzione sicure per verificare la stabilità della distribuzione prima dell'implementazione in tutte le aree.
services: azure-resource-manager
documentationcenter: na
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/08/19
ms.author: jgao
ms.openlocfilehash: 4c2c38754b2051a4f61249f623f60f21e9690f5e
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206616"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Introdurre implementazione di integrazione dell'integrità a Azure Deployment Manager (anteprima pubblica)

[Azure Deployment Manager](./deployment-manager-overview.md) consente di eseguire ripristini in più fasi delle risorse di Azure Resource Manager. Area dall'area vengono distribuite le risorse in modo ordinato. Il controllo di integrità integrati di Azure Deployment Manager può monitorare implementazioni e automaticamente implementazioni di problematiche significative, in modo che sia possibile risolvere i problemi e ridurre la scala dell'impatto. Questa funzionalità può ridurre l'indisponibilità del servizio causati da regressioni negli aggiornamenti.

## <a name="health-monitoring-providers"></a>I provider di monitoraggio dell'integrità

Per rendere il più semplice possibile l'integrazione dell'integrità, Microsoft collabora con alcune delle principali società di monitoraggio dell'integrità dei servizi per fornire una semplice soluzione copia/incolla per integrare i controlli integrità con le distribuzioni. Se un monitoraggio di integrità non è già in uso, questi rappresentano un'ottima soluzione per iniziare:

| ![distribuzione di Azure manager health monitor provider datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![distribuzione di Azure manager health monitor provider site24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![distribuzione di Azure manager health monitor provider wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog, il monitoraggio iniziale e piattaforma analitica per gli ambienti cloud moderne. Visualizzare [modo Datadog si integra con Azure Deployment Manager](https://www.datadoghq.com/azure-deployment-manager/).|Servizi di cloud privati e pubblici all-in-one Site24x7, soluzione di monitoraggio. Visualizzare [modo Site24x7 si integra con Azure Deployment Manager](https://www.site24x7.com/azure/adm.html).| Wavefront, la piattaforma di monitoraggio e analitica per ambienti di applicazioni multi-cloud. Visualizzare [come Wavefront si integra con Azure Deployment Manager](https://go.wavefront.com/wavefront-adm/).|

## <a name="how-service-health-is-determined"></a>La modalità di determinazione dell'integrità del servizio

[I provider di monitoraggio dell'integrità](#health-monitoring-providers) offre diversi meccanismi per servizi di monitoraggio e avviso eventuali problemi di integrità del servizio. [Monitoraggio di Azure](../azure-monitor/overview.md) è riportato un esempio di un'offerta di questo tipo. Monitoraggio di Azure è utilizzabile per creare avvisi quando vengono superate determinate soglie. Ad esempio, l'utilizzo di CPU e memoria picchi oltre ai livelli previsto quando si distribuisce un nuovo aggiornamento del servizio. Quando si riceve una notifica, si possono intraprendere azioni correttive.

Questi provider di integrità offrono in genere le API REST in modo che lo stato dei monitoraggi del servizio può essere esaminato a livello di codice. Le API REST possono provenire nuovamente con un semplice segnale integri o non integri (determinata dal codice di risposta HTTP) e/o le informazioni dettagliate sui segnali di ricezione.

Il nuovo *healthCheck* passaggio nella gestione di distribuzione di Azure consente di dichiarare i codici HTTP che indicano che un servizio integro oppure, per ottenere risultati più complessi REST, è anche possibile specificare espressioni regolari che, se corrispondono, indicano un integro risposta.

Il flusso per ottenere il programma di installazione con controlli di integrità di gestione di distribuzione Azure:

1. Creare i monitoraggi di integrità tramite un provider di servizi di integrità di propria scelta.
1. Creare uno o più passaggi healthCheck come parte dell'implementazione Azure Deployment Manager. Compilare la procedura healthCheck con le informazioni seguenti:

    1. L'URI per l'API REST per i monitoraggi di integrità (come definito dal provider del servizio integrità).
    1. Informazioni di autenticazione. Attualmente è supportata solo l'autenticazione di stile chiave API.
    1. [Codici di stato HTTP](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) o espressioni regolari che definiscono una risposta integra. Si noti che è possibile fornire le espressioni regolari, che devono tutte le corrispondenze per la risposta per essere considerato integro o si possono fornire espressioni di cui uno deve corrispondere per la risposta essere considerato integro. Entrambi i metodi sono supportati.

    Il codice Json seguente è riportato un esempio:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

1. Richiamare la procedura healthCheck al momento opportuno l'implementazione Azure Deployment Manager. Nell'esempio seguente, un passaggio di verifica dell'integrità viene richiamato in **postDeploymentSteps** dei **stepGroup2**.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

Per illustrare un esempio, vedere [esercitazione: Usare il controllo integrità in Azure Deployment Manager](./deployment-manager-health-check.md).

## <a name="phases-of-a-health-check"></a>Fasi di un controllo di integrità

A questo punto, Azure Deployment Manager sa come eseguire una query per l'integrità del servizio e in quali fasi l'implementazione di farlo. Tuttavia, Azure Deployment Manager consente inoltre di configurazione avanzato dei tempi di questi controlli. Viene eseguito un passaggio di healthCheck in 3 fasi sequenziale, ognuno dei quali hanno durate configurabile: 

1. Attesa

    1. Al termine di un'operazione di distribuzione, potrebbero essere il riavvio di macchine virtuali, la riconfigurazione in base a nuovi dati o anche in corso l'avvio per la prima volta. Può anche tempo per i servizi avviare la creazione di segnali di integrità per l'aggregazione da provider di monitoraggio in qualcosa di utile dell'integrità. Durante questo tumultuoso processo, può non avere senso per controllare l'integrità del servizio poiché l'aggiornamento non ha ancora raggiunto uno stato stabile. In effetti, il servizio potrebbe essere oscilla avanti tra gli stati integri e non integri come pagare le risorse. 
    1. Durante la fase di attesa, dell'integrità del servizio non è monitorato. Viene utilizzato per consentire alle risorse distribuite il tempo necessario per prima di iniziare il processo di controllo di integrità, vengono inseriti. 
1. Elastic

    1. Poiché non è possibile sapere sempre quanto tempo le risorse richiederà a, vengono inseriti prima che diventino stabili, la fase elastica consente un periodo di tempo flessibile tra quando le risorse sono potenzialmente instabili e quando sono necessari per mantenere una stabile integro stato.
    1. Quando viene avviata la fase elastica, Azure Deployment Manager inizia periodicamente il polling dell'endpoint REST fornita per l'integrità del servizio. L'intervallo di polling è configurabile. 
    1. Se il monitoraggio dell'integrità torna disponibile con i segnali che indica che il servizio non è integro, questi segnali vengono ignorati, la fase di Elastic continua e continua il polling. 
    1. Non appena il monitoraggio dell'integrità viene restituita con segnali che indicano che il servizio è integro, la fase di Elastic end e la fase HealthyState inizia. 
    1. Di conseguenza, la durata specificata per la fase elastica è la quantità massima di tempo che può essere impiegato per il polling per l'integrità del servizio prima di una risposta integra considerata obbligatoria. 
1. HealthyState

    1. Durante la fase HealthyState, dell'integrità del servizio continuamente di polling è nello stesso intervallo come fase elastica. 
    1. Il servizio dovrà mantenere integro segnali provenienti dal provider di monitoraggio dell'integrità per l'intera durata specificata. 
    1. Se in qualsiasi momento viene rilevata una risposta di tipo non integro, Azure Deployment Manager interrompe l'intera implementazione e restituire la risposta REST che trasportano i segnali di servizio non integro.
    1. Una volta terminata la durata HealthyState, il healthCheck è stata completata e la distribuzione continua con il passaggio successivo.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come integrare il monitoraggio dello stato in Azure Deployment Manager. Passare all'articolo successivo per informazioni su come eseguire la distribuzione con Deployment Manager.

> [!div class="nextstepaction"]
> [Esercitazione: integrazione di controllo di integrità in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)