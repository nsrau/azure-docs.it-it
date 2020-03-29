---
title: CI/CD per Azure Spring Cloud
description: CI/CD per Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: brendm
ms.openlocfilehash: f329fb5472c5a2eab6f22a2e81b19d90e7045330
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278524"
---
# <a name="cicd-for-azure-spring-cloud"></a>CI/CD per Azure Spring Cloud

L'integrazione continua e gli strumenti di distribuzione continua consentono agli sviluppatori di distribuire rapidamente gli aggiornamenti alle applicazioni esistenti con il minimo sforzo e rischio. DevOps di Azure consente di organizzare e controllare questi processi chiave. Attualmente, Azure Spring Cloud non offre un plug-in DevOps di Azure specifico.  Tuttavia, è possibile integrare le applicazioni Spring Cloud con DevOps usando un'attività dell'interfaccia della riga di comando di [Azure.However,](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops)you can integrate your Spring Cloud applications with DevOps using an Azure CLI task . Questo articolo illustra come usare un'attività dell'interfaccia della riga di comando di Azure con Azure Spring Cloud per l'integrazione con DevOps di Azure.This article show you how to use an Azure CLI task with Azure Spring Cloud to integrate with Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Creare una connessione al servizio Azure Resource ManagerCreate an Azure Resource Manager service connection

Leggere [questo articolo](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) per informazioni su come creare una connessione del servizio Azure Resource Manager al progetto DevOps di Azure.Read this article to learn how to create an Azure Resource Manager service connection to your Azure DevOps project. Assicurarsi di selezionare la stessa sottoscrizione usata per l'istanza del servizio Azure Spring Cloud.Sure to select the same subscription you are using for your Azure Spring Cloud service instance.

## <a name="azure-cli-task-templates"></a>Modelli di attività dell'interfaccia della riga di comando di AzureAzure CLI

### <a name="deploy-artifacts"></a>Distribuire artefatti

È possibile compilare e distribuire `tasks`i progetti utilizzando una serie di file . Questo frammento definisce innanzitutto un'attività Maven per compilare l'applicazione, seguita da una seconda attività che distribuisce il file JAR usando l'interfaccia della riga di comando di Azure Spring Cloud di Azure.This snippet first defines a Maven task to build the application, followed by a second task that deploys the JAR file using the Azure Spring Cloud Azure CLI.

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

È possibile distribuire direttamente in Azure senza un'istruzione di compilazione separata.

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
