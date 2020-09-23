---
title: Integrazione continua/distribuzione continua per il cloud di Azure Spring
description: Integrazione continua/distribuzione continua per il cloud di Azure Spring
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 40e674594c80a076fc9775fd4315aee938a43593
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90888698"
---
# <a name="cicd-for-azure-spring-cloud"></a>Integrazione continua/distribuzione continua per il cloud di Azure Spring

Gli strumenti di integrazione continua e recapito continuo consentono di distribuire rapidamente gli aggiornamenti alle applicazioni esistenti con il minimo sforzo e rischio. Azure DevOps consente di organizzare e controllare questi processi chiave. Attualmente, Azure Spring cloud non offre un plug-in Azure DevOps specifico.  Tuttavia, è possibile integrare le applicazioni Spring cloud con DevOps usando un' [attività dell'interfaccia](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops&preserve-view=true)della riga di comando di Azure.

Questo articolo illustra come usare un'attività dell'interfaccia della riga di comando di Azure con Azure Spring cloud per l'integrazione con Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Creare una connessione al servizio Azure Resource Manager

Leggere [questo articolo](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops&preserve-view=true) per informazioni su come creare una connessione del servizio Azure Resource Manager al progetto DevOps di Azure. Assicurarsi di selezionare la stessa sottoscrizione usata per l'istanza del servizio cloud Spring di Azure.

## <a name="azure-cli-task-templates"></a>Modelli di attività CLI di Azure
::: zone pivot="programming-language-csharp"
### <a name="deploy-artifacts"></a>Distribuire artefatti

È possibile compilare e distribuire i progetti utilizzando una serie di `tasks` . Questo frammento di codice definisce le variabili, un'attività .NET Core per compilare l'applicazione e un'attività dell'interfaccia della riga di comando di Azure per distribuire il file *zip* .

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'
  resourceGroupName: '<your resource group name>'

steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Configure Azure CLI and install spring-cloud extension
- task: AzureCLI@1
  inputs:
    azureSubscription: '<your subscription>'
    scriptLocation: 'inlineScript'
    inlineScript: |
      az extension add --name spring-cloud --y
      az configure --defaults group=${{ variables.resourceGroupName }}
      az configure --defaults spring-cloud=${{ variables.serviceName }}
      az spring-cloud app deploy -n ${{ variables.planetAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.planetMainEntry }} --artifact-path ./${{ variables.planetAppName }}/publish-deploy-planet.zip
      az spring-cloud app deploy -n ${{ variables.solarAppName }} --runtime-version NetCore_31 --main-entry ${{ variables.solarMainEntry }} --artifact-path ./${{ variables.solarAppName }}/publish-deploy-solar.zip
      az spring-cloud app update -n ${{ variables.solarAppName }} --is-public true
      az spring-cloud app show -n ${{ variables.solarAppName }} -o table
    workingDirectory: '${{ variables.workingDirectory }}/src'
```

::: zone-end
::: zone pivot="programming-language-java"
### <a name="deploy-artifacts"></a>Distribuire artefatti

È possibile compilare e distribuire i progetti utilizzando una serie di `tasks` . Questo frammento di codice definisce innanzitutto un'attività Maven per compilare l'applicazione, seguita da una seconda attività che distribuisce il file JAR usando l'estensione dell'interfaccia della riga di comando di Azure Spring cloud.

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --jar-path ./target/your-result-jar.jar
      # deploy other app
```

### <a name="deploy-from-source"></a>Distribuisci dall'origine

È possibile eseguire la distribuzione direttamente in Azure senza un'istruzione di compilazione separata.

```yaml
- task: AzureCLI@1
  inputs:
    azureSubscription: <your service connection name>
    scriptLocation: inlineScript
    inlineScript: |
      az extension add -y --name spring-cloud
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
::: zone-end

## <a name="next-steps"></a>Passaggi successivi

* [Avvio rapido: Distribuire la prima applicazione Azure Spring Cloud](spring-cloud-quickstart.md)
