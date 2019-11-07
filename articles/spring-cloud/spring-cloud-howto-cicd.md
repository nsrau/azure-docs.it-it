---
title: Integrazione continua/distribuzione continua per il cloud di Azure Spring
description: Integrazione continua/distribuzione continua per il cloud di Azure Spring
author: jpconnock
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/04/2019
ms.author: jeconnoc
ms.openlocfilehash: c19d32f0be2eb817f9f7d73e6c6eaad8d90ce350
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607789"
---
# <a name="cicd-for-azure-spring-cloud"></a>Integrazione continua/distribuzione continua per il cloud di Azure Spring

Gli strumenti di integrazione continua e recapito continuo consentono agli sviluppatori di distribuire rapidamente gli aggiornamenti alle applicazioni esistenti con il minimo sforzo e rischio. Azure DevOps consente di organizzare e controllare questi processi chiave. Attualmente, Azure Spring cloud non offre un plug-in Azure DevOps specifico.  Tuttavia, è possibile integrare le applicazioni Spring cloud con DevOps usando un' [attività dell'interfaccia](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli?view=azure-devops)della riga di comando di Azure. Questo articolo illustra come usare un'attività dell'interfaccia della riga di comando di Azure con Azure Spring cloud per l'integrazione con Azure DevOps.

## <a name="create-an-azure-resource-manager-service-connection"></a>Creare una connessione al servizio Azure Resource Manager

Leggere [questo articolo](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) per informazioni su come creare una connessione del servizio Azure Resource Manager al progetto DevOps di Azure. Assicurarsi di selezionare la stessa sottoscrizione usata per l'istanza del servizio cloud Spring di Azure.

## <a name="azure-cli-task-templates"></a>Modelli di attività CLI di Azure

### <a name="deploy-artifacts"></a>Distribuisci artefatti

È possibile compilare e distribuire i progetti utilizzando una serie di `tasks`. Questo frammento di codice definisce innanzitutto un'attività Maven per compilare l'applicazione, seguita da una seconda attività che distribuisce il file JAR usando l'estensione dell'interfaccia della riga di comando di Azure Spring cloud.

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
      az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
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
      az extension add -y --source https://azureclitemp.blob.core.windows.net/spring-cloud/spring_cloud-0.1.0-py2.py3-none-any.whl
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name>

      # or if it is a multi-module project
      az spring-cloud app deploy --resource-group <your-resource-group> --service <your-spring-cloud-service> --name <app-name> --target-module relative/path/to/module
```
