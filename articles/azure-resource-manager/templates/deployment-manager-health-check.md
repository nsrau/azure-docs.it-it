---
title: Implementazione dell'integrazione dell'integrità - Gestione distribuzione di AzureHealth integration rollout - Azure Deployment Manager
description: Descrive come distribuire un servizio in più aree con Azure Deployment Manager. Illustra le procedure di distribuzione sicure per verificare la stabilità della distribuzione prima dell'implementazione in tutte le aree.
author: mumian
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: jgao
ms.openlocfilehash: aa99bdfcbc2f42ae81bdd55c266bcd7d87808031
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79273800"
---
# <a name="introduce-health-integration-rollout-to-azure-deployment-manager-public-preview"></a>Introduzione all'integrazione dell'integrità in Gestione distribuzione di Azure (anteprima pubblica)Introduce health integration rollout to Azure Deployment Manager (Public preview)

[Gestione distribuzione di Azure](./deployment-manager-overview.md) consente di eseguire implementazioni in fasi delle risorse di Azure Resource Manager.Azure Deployment Manager allows you to perform staged rollouts of Azure Resource Manager resources. Le risorse vengono distribuite regione per area in modo ordinato. Il controllo di integrità integrato di Gestione distribuzione di Azure consente di monitorare le implementazioni e arrestare automaticamente le implementazioni problematiche, in modo da poter risolvere i problemi e ridurre la portata dell'impatto. Questa funzionalità può ridurre l'indisponibilità del servizio causata da regressioni negli aggiornamenti.

## <a name="health-monitoring-providers"></a>Fornitori di monitoraggio dell'integrità

Per rendere il più semplice possibile l'integrazione dell'integrità, Microsoft collabora con alcune delle principali società di monitoraggio dell'integrità dei servizi per fornire una semplice soluzione copia/incolla per integrare i controlli integrità con le distribuzioni. Se non si sta già utilizzando un monitoraggio dello stato, queste sono ottime soluzioni per iniziare:

| ![azure deployment Manager health monitor provider datadog](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-datadog.svg) | ![sito del provider del monitoraggio dello stato di Azure Deployment Manager24x7](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-site24x7.svg) | ![server di monitoraggio dello stato di azure di gestione della distribuzione provider wavefront](./media/deployment-manager-health-check/azure-deployment-manager-health-monitor-provider-wavefront.svg) |
|-----|------|------|
|Datadog, la piattaforma leader nel monitoraggio e nell'analisi per i moderni ambienti cloud. Scopri [come Datadog si integra con Gestione distribuzione di Azure.](https://www.datadoghq.com/azure-deployment-manager/)|Site24x7, la soluzione di monitoraggio dei servizi cloud pubblici e privati all-in-one. Scopri [come Site24x7 si integra con Gestione distribuzione di Azure.](https://www.site24x7.com/azure/adm.html)| Wavefront, la piattaforma di monitoraggio e analisi per ambienti applicativi multi-cloud. Scopri [come Wavefront si integra con Gestione distribuzione di Azure.](https://go.wavefront.com/wavefront-adm/)|

## <a name="how-service-health-is-determined"></a>Come viene determinata l'integrità del servizioHow service health is determined

I provider di [monitoraggio dell'integrità](#health-monitoring-providers) offrono diversi meccanismi per il monitoraggio dei servizi e l'avviso di eventuali problemi di integrità del servizio. [Monitoraggio di Azure](../../azure-monitor/overview.md) è un esempio di un'offerta di questo tipo. Monitoraggio di Azure può essere usato per creare avvisi quando vengono superate determinate soglie. Ad esempio, l'utilizzo della memoria e della CPU supera i livelli previsti quando si distribuisce un nuovo aggiornamento al servizio. Quando viene notificato, è possibile intraprendere azioni correttive.

Questi provider di integrità offrono in genere API REST in modo che lo stato dei monitoraggi del servizio possa essere esaminato a livello di codice. Le API REST possono essere ritornate con un semplice segnale integro/non integro (determinato dal codice di risposta HTTP) e/o con informazioni dettagliate sui segnali che riceve.

Il nuovo passaggio *healthCheck* in Gestione distribuzione di Azure consente di dichiarare codici HTTP che indicano un servizio integro o, per risultati REST più complessi, è anche possibile specificare espressioni regolari che, se corrispondono, indicano una risposta integra.

Flusso per l'installazione con i controlli di integrità di Azure Deployment Manager:The flow to getting setup with Azure Deployment Manager health checks:

1. Crea i tuoi monitoraggi dello stato tramite un fornitore di servizi sanitari di tua scelta.
1. Creare uno o più passaggi di healthCheck come parte dell'implementazione di Gestione distribuzione di Azure.Create one or more healthCheck steps as part of your Azure Deployment Manager rollout. Compilare i passaggi di healthCheck con le seguenti informazioni:

    1. URI per l'API REST per i monitoraggi dell'integrità (come definito dal provider di servizi di integrità).
    1. Informazioni di autenticazione. Attualmente è supportata solo l'autenticazione in stile chiave API.
    1. [Codici di stato HTTP](https://www.wikipedia.org/wiki/List_of_HTTP_status_codes) o espressioni regolari che definiscono una risposta integra. Si noti che è possibile fornire espressioni regolari, che ALL devono corrispondere affinché la risposta sia considerata integra, oppure è possibile fornire espressioni di cui ANY deve corrispondere affinché la risposta sia considerata integra. Sono supportati entrambi i metodi.

    Il codice Json seguente è un esempio:The following Json is an example:

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

1. Richiamare i passaggi healthCheck al momento appropriato nell'implementazione di Gestione distribuzione di Azure.Invoke the healthCheck steps at the appropriate time in your Azure Deployment Manager rollout. Nell'esempio seguente viene richiamato un passaggio di controllo dell'integrità in **postDeploymentSteps** di **stepGroup2**.

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

Per illustrare un esempio, vedere [Esercitazione: Usare il controllo dell'integrità in Gestione distribuzione di Azure.To](./deployment-manager-health-check.md)walk through an example, see Tutorial: Use health check in Azure Deployment Manager .

## <a name="phases-of-a-health-check"></a>Fasi di un controllo di integrità

A questo punto Gestione distribuzione di Azure sa come eseguire una query per l'integrità del servizio e in quali fasi dell'implementazione eseguire questa operazione. Tuttavia, Gestione distribuzione di Azure consente anche la configurazione completa della temporizzazione di questi controlli. Un passaggio healthCheck viene eseguito in 3 fasi sequenziali, tutte con durate configurabili:A healthCheck step is executed in 3 sequential phases, which which which all have able durations: 

1. Attesa

    1. Al termine di un'operazione di distribuzione, è possibile che le macchine virtuali vengano riavviate, riconfigurate in base a nuovi dati o addirittura avviate per la prima volta. Ci vuole anche tempo per i servizi per iniziare a emettere segnali di integrità per essere aggregato dal provider di monitoraggio dello stato in qualcosa di utile. Durante questo processo tumultuoso, potrebbe non avere senso controllare l'integrità del servizio poiché l'aggiornamento non ha ancora raggiunto uno stato stabile. Infatti, il servizio può oscillare tra stati integri e malsani come le risorse si stabiliscono. 
    1. Durante la fase di attesa, l'integrità del servizio non viene monitorata. Viene utilizzato per consentire alle risorse distribuite il tempo di cottura prima di iniziare il processo di controllo dell'integrità. 
1. Elastic

    1. Poiché è impossibile sapere in tutti i casi quanto tempo ci vorrà per cuocere le risorse prima che diventino stabili, la fase elastica consente un periodo di tempo flessibile tra il momento in cui le risorse sono potenzialmente instabili e quando sono necessarie per mantenere una temperatura stabile e integro Stato.
    1. All'inizio della fase Elastic, Gestione distribuzione di Azure inizia a eseguire periodicamente il polling dell'endpoint REST fornito per l'integrità del servizio. L'intervallo di polling è configurabile. 
    1. Se il monitoraggio dello stato viene riunito con segnali che indicano che il servizio non è integro, questi segnali vengono ignorati, la fase elastica continua e il polling continua. 
    1. Non appena il monitoraggio dello stato torna con segnali che indicano che il servizio è integro, la fase elastica termina e inizia la fase HealthyState. 
    1. Pertanto, la durata specificata per la fase elastica è la quantità massima di tempo che può essere impiegata per il polling per l'integrità del servizio prima che una risposta integra venga considerata obbligatoria. 
1. HealthyState

    1. Durante la fase HealthyState, l'integrità del servizio viene continuamente sottoposta a polling nello stesso intervallo della fase elastica. 
    1. Si prevede che il servizio manterrà segnali integri dal provider di monitoraggio dello stato per l'intera durata specificata. 
    1. Se in qualsiasi momento viene rilevata una risposta non integra, Gestione distribuzione di Azure arresterà l'intera implementazione e restituirà la risposta REST che trasporta i segnali del servizio non integro.
    1. Al termine della durata di HealthyState, healthCheck è completo e la distribuzione continua al passaggio successivo.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo sono stati appresi come integrare il monitoraggio dell'integrità in Gestione distribuzione di Azure.In this article, you learn about how to integrate health monitoring in Azure Deployment Manager. Passare all'articolo successivo per informazioni su come eseguire la distribuzione con Deployment Manager.

> [!div class="nextstepaction"]
> [Esercitazione: integrare il controllo di integrità in Gestione distribuzione di AzureTutorial: integrate health check in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md)