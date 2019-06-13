---
title: Usare Azure Deployment Manager con modelli di Resource Manager | Microsoft Docs
description: Usare modelli di Resource Manager con Azure Deployment Manager per distribuire risorse di Azure.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 05/31/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d43a0e7c48db9dd42c7cf3b52e5d4072a4827898
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479167"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Esercitazione: Usare il controllo integrità in Azure Deployment Manager (anteprima pubblica)

Informazioni su come integrare il controllo integrità in [Azure Deployment Manager](./deployment-manager-overview.md). Questa esercitazione si basa sull'esercitazione [Usare Azure Deployment Manager con modelli di Resource Manager](./deployment-manager-tutorial.md). Prima di procedere, è necessario completare tale esercitazione.

Nel modello di implementazione usato in [Usare Azure Deployment Manager con modelli di Resource Manager](./deployment-manager-tutorial.md), è stato chiamato un passaggio di attesa. In questa esercitazione, sostituire il passaggio di attesa con un passaggio di controllo integrità.

> [!IMPORTANT]
> Se la sottoscrizione è contrassegnata in modo che Canary testi nuove funzionalità di Azure, è possibile usare Azure Deployment Manager solo per distribuire le aree di Canary. 

Questa esercitazione illustra le attività seguenti:

> [!div class="checklist"]
> * Creare un simulatore del servizio di controllo integrità
> * Rivedere il modello di implementazione
> * Distribuire la topologia
> * Distribuire l'implementazione con stato non integro
> * Verificare la distribuzione dell'implementazione
> * Distribuire l'implementazione con stato integro
> * Verificare la distribuzione dell'implementazione
> * Pulire le risorse

Le informazioni di riferimento sull'API REST di Azure Deployment Manager sono disponibili [qui](https://docs.microsoft.com/rest/api/deploymentmanager/).

Se non si ha una sottoscrizione di Azure, [creare un account gratuito](https://azure.microsoft.com/free/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione di questo articolo, sono necessari gli elementi seguenti:

* Completare l'esercitazione [Usare Azure Deployment Manager con modelli di Resource Manager](./deployment-manager-tutorial.md).
* Scaricare [i modelli e gli artefatti](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip) usati in questa esercitazione.

## <a name="create-a-health-check-service-simulator"></a>Creare un simulatore del servizio di controllo integrità

Nell'ambiente di produzione, è in genere necessario usare uno o più provider di monitoraggio. Per rendere il più semplice possibile l'integrazione dell'integrità, Microsoft collabora con alcune delle principali società di monitoraggio dell'integrità dei servizi per fornire una semplice soluzione copia/incolla per integrare i controlli integrità con le distribuzioni. Per un elenco di queste società, vedere [Health monitoring providers](./deployment-manager-health-check.md#health-monitoring-providers) (Provider di monitoraggio integrità). Ai fini di questa esercitazione, si crea una [funzione di Azure](/azure/azure-functions/) per simulare un servizio di monitoraggio dello stato. Questa funzione accetta un codice di stato e restituisce lo stesso codice. Il modello di Azure Deployment Manager usa il codice di stato per determinare come procedere con la distribuzione.

I due file seguenti vengono usati per la distribuzione della funzione di Azure. Non è necessario scaricare questi file per completare l'esercitazione.

* Un modello di Resource Manager disponibile in [https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json](https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json). Distribuire questo modello per creare una funzione di Azure.
* Un file zip del codice sorgente della funzione di Azure, [http://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip](http://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip). Questo file zip viene chiamato dal modello di Resource Manager.

Per distribuire la funzione di Azure, selezionare **Prova** per aprire Azure Cloud Shell e quindi incollare lo script seguente nella finestra della shell.  Per incollare il codice, fare clic con il pulsante destro del mouse nella finestra della shell e quindi selezionare **Incolla**.

> [!IMPORTANT]
> **projectName** nello script di PowerShell viene usato per generare nomi per i servizi di Azure che vengono distribuiti in questa esercitazione. I diversi servizi di Azure hanno diversi requisiti sui nomi. Per garantire la riuscita della distribuzione, scegliere un nome che contenga meno di 12 caratteri, con solo lettere minuscole e numeri.
> Salvare una copia del nome del progetto. Usare lo stesso projectName per tutta l'esercitazione.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json" -projectName $projectName
```

Per verificare e testare la funzione di Azure:

1. Aprire il [portale di Azure](https://portal.azure.com).
1. Aprire il gruppo di risorse.  Il nome predefinito è il nome del progetto con **rg** aggiunto.
1. Selezionare il servizio app nel gruppo di risorse.  Il nome predefinito del servizio app è il nome del progetto con **webapp** aggiunto.
1. Espandere **Funzioni**, quindi selezionare **HttpTrigger1**.

    ![Funzione di Azure Controllo integrità di Azure Deployment Manager](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Selezionare **&lt;/>Ottenere l'URL della funzione**.
1. Selezionare **Copia** per copiare l'URL negli appunti.  L'URL è simile a:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Sostituire `{healthStatus}` nell'URL con un codice di stato. In questa esercitazione, usare **non integro** per testare lo scenario di tipo non integro e usare **integro** o **avviso** per testare lo scenario integro. Creare due URL, uno con lo stato non integro e l'altro con lo stato integro. Ad esempio:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Sono necessari entrambi gli URL per completare questa esercitazione.

1. Per testare il simulatore di monitoraggio dell'integrità, aprire gli URL creati nel passaggio precedente.  I risultati dello stato non integro sarà simile al seguente:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>Rivedere il modello di implementazione

Lo scopo di questa sezione è descrivere come includere un passaggio di controllo integrità nel modello di implementazione. Non è necessario creare un proprio file CreateADMRollout.json per completare questa esercitazione. Il modello di implementazione modificato è condiviso in un account di archiviazione che viene usato nelle sezioni successive.

1. Aprire **CreateADMRollout.json**. Questo file JSON è una parte del download.  Vedere [Prerequisiti](#prerequisites).
1. Aggiungere altri due parametri:

    ```json
    "healthCheckUrl": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check URL."
        }
    },
    "healthCheckAuthAPIKey": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check Azure Function function authorization key."
        }
    }
    ```

1. Sostituire la definizione di risorsa del passaggio di attesa con una definizione di risorsa del passaggio di controllo integrità:

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

    In base alla definizione, la distribuzione continua se lo stato di integrità è *integro* oppure *avviso*.

1. Aggiornare la condizione **dependsON** della definizione di implementazione in modo da includere il passaggio di controllo integrità appena definito:

    ```json
    "dependsOn": [
        "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
        "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Aggiornare **stepGroups** in modo da includere il passaggio di controllo integrità. **healthCheckStep** viene chiamato in **postDeploymentSteps** di **stepGroup2**. **stepGroup3** e **stepGroup4** vengono distribuiti solo se lo stato di integrità è *integro* o *avviso*.

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

    Se si confronta la sezione **stepGroup3** prima e dopo la revisione, a questo punto dipende da **stepGroup2**.  È necessario quando **stepGroup3** e i gruppi di passaggi successivi dipendono dai risultati del monitoraggio dell'integrità.

    Lo screenshot seguente illustra le aree modificate e come viene usato il passaggio di controllo integrità:

    ![Modello di controllo integrità di Azure Deployment Manager](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>Distribuire la topologia

Per semplificare l'esercitazione, il modello di topologia e gli elementi sono condivisi nei percorsi seguenti in modo che non sia necessario preparare la propria copia. Se si vuole usare la propria copia, seguire le istruzioni nell'[Esercitazione: Usare Azure Deployment Manager con modelli di Resource Manager](./deployment-manager-tutorial.md).

* Modello di topologia: https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplates/CreateADMServiceTopology.json
* Archivio Artefatti: https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

Per distribuire la topologia, selezionare **Prova** per aprire Cloud Shell e quindi incollare lo script di PowerShell.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMServiceTopology.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation
```

Verificare che la creazione della topologia del servizio e delle risorse sottolineate sia stata completata usando il portale di Azure:

![Esercitazione su Azure Deployment Manager - Risorse della topologia del servizio distribuite](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

Per visualizzare le risorse deve essere selezionata l'opzione **Mostra tipi nascosti**.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>Distribuire l'implementazione con stato non integro

Per semplificare l'esercitazione, il modello di topologia revisionato viene condiviso nei percorsi seguenti in modo che non sia necessario preparare la propria copia. Se si vuole usare la propria copia, seguire le istruzioni nell'[Esercitazione: Usare Azure Deployment Manager con modelli di Resource Manager](./deployment-manager-tutorial.md).

* Modello di topologia: https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json
* Archivio Artefatti: https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore

Usare l'URL di stato non integro creato in [Creare un simulatore del servizio di controllo integrità](#create-a-health-check-service-simulator). Per **managedIdentityID**, vedere [Creare l'identità gestita assegnata dall'utente](./deployment-manager-tutorial.md#create-the-user-assigned-managed-identity).

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$managedIdentityID = Read-Host -Prompt "Enter a user-assigned managed identity"
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation `
    -managedIdentityID $managedIdentityID `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment` è una chiamata asincrona. Il messaggio di operazione riuscita indica solo che la distribuzione è stata avviata correttamente. Per verificare la distribuzione, usare `Get-AZDeploymentManagerRollout`.  Vedere la procedura successiva.

Per controllare lo stato di avanzamento dell'implementazione con lo script PowerShell seguente:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$resourceGroupName = "${projectName}rg"
$rolloutName = "${projectName}Rollout"

# Get the rollout status
Get-AzDeploymentManagerRollout `
    -ResourceGroupName $resourceGroupName `
    -Name $rolloutName `
    -Verbose
```

L'output di esempio seguente mostra la distribuzione non riuscita a causa dello stato non integro:

```output
Service: myhc0417ServiceWUSrg
    TargetLocation: WestUS
    TargetSubscriptionId: <Subscription ID>

    ServiceUnit: myhc0417ServiceWUSWeb
        TargetResourceGroup: myhc0417ServiceWUSrg

        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
Get-AzDeploymentManagerRollout :
Service: myhc0417ServiceWUSrg
    ServiceUnit: myhc0417ServiceWUSWeb
        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
At line:1 char:1
+ Get-AzDeploymentManagerRollout `
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : NotSpecified: (:) [Get-AzDeploymentManagerRollout], Exception
+ FullyQualifiedErrorId : RolloutFailed,Microsoft.Azure.Commands.DeploymentManager.Commands.GetRollout


ResourceGroupName       : myhc0417rg
BuildVersion            : 1.0.0.0
ArtifactSourceId        : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/artifactSources/myhc0417ArtifactSourceRollout
TargetServiceTopologyId : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/serviceTopologies/myhc0417ServiceTopology
Status                  : Failed
TotalRetryAttempts      : 0
Identity                : Microsoft.Azure.Commands.DeploymentManager.Models.PSIdentity
OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
Services                : {myhc0417ServiceWUS, myhc0417ServiceWUSrg}
Name                    : myhc0417Rollout
Type                    : Microsoft.DeploymentManager/rollouts
Location                : centralus
Id                      : /subscriptions/<Subscription ID>/resourcegroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/rollouts/myhc0417Rollout
Tags                    :
```

Una volta completata l'implementazione, verrà visualizzato un gruppo di risorse aggiuntivo creato per gli Stati Uniti occidentali.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Distribuire l'implementazione con stato integro

Ripetere questa sezione per distribuire l'implementazione con l'URL di stato integro.  Una volta completata l'implementazione, verrà visualizzato un gruppo di risorse aggiuntivo creato per gli Stati Uniti orientali.

## <a name="verify-the-deployment"></a>Verificare la distribuzione

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Passare alle nuove applicazioni Web nei gruppi di risorse creati dalla distribuzione dell'implementazione.
3. Aprire l'applicazione Web in un Web browser. Verificare la località e la versione nel file index.html.

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessarie, eseguire la pulizia delle risorse di Azure distribuite eliminando il gruppo di risorse.

1. Nel portale di Azure selezionare **Gruppo di risorse** nel menu a sinistra.
2. Usare il campo **Filtra per nome** per limitare l'elenco ai gruppi di risorse creati in questa esercitazione, che saranno 3-4:

    * **&lt;namePrefix>rg**, che contiene le risorse di Deployment Manager.
    * **&lt;namePrefix>ServiceWUSrg**, che contiene le risorse definite da ServiceWUS.
    * **&lt;namePrefix>ServiceEUSrg**, che contiene le risorse definite da ServiceEUS.
    * Gruppo di risorse per l'identità gestita definita dall'utente.
3. Selezionare il nome del gruppo di risorse.
4. Selezionare **Elimina gruppo di risorse** nel menu in alto.
5. Ripetere gli ultimi due passaggi per eliminare gli altri gruppi di risorse creati con questa esercitazione.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato illustrato come usare la funzionalità di controllo integrità di Azure Deployment Manager. Per altre informazioni, vedere la [documentazione di Azure Resource Manager](/azure/azure-resource-manager/).
