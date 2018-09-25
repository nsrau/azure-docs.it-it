---
title: Esportare o eliminare dati di sperimentazione o gestione modelli - Azure Machine Learning  | Microsoft Docs
description: In Azure Machine Learning è possibile esportare o eliminare i dati dell'account correlati alla sperimentazione o alla gestione modelli con portale di Azure, interfaccia della riga di comando, SDK e API REST autenticate. Questo articolo illustra i passaggi da eseguire.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
manager: haining
ms.reviewer: jmartens, mldocs
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.date: 05/22/2018
ROBOTS: NOINDEX
ms.openlocfilehash: 7daa3bdf9fb51fee6b0e190625f07e0d14c3d1f4
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995143"
---
# <a name="export-or-delete-your-experimentation-or-model-management-data-in-machine-learning"></a>Esportare o eliminare dati di sperimentazione o gestione modelli in Machine Learning

[!INCLUDE [workbench-deprecated](../../../includes/aml-deprecating-preview-2017.md)]

In Azure Machine Learning è possibile esportare o eliminare i dati dell'account correlati alla sperimentazione o alla gestione modelli con le API REST autenticate. Questo articolo descrive come esportare o eliminare i dati.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="control-your-account-data"></a>Controllare i dati dell'account
I dati interni al prodotto archiviati tramite la sperimentazione e la gestione modelli di Machine Learning sono disponibili per l'esportazione e l'eliminazione mediante portale di Azure, interfaccia della riga di comando, SDK e API REST autenticate. I dati di telemetria sono accessibili tramite il portale Privacy di Azure. 

In Azure Machine Learning i dati personali sono costituiti dalle informazioni utente dei documenti della cronologia di esecuzione e dai record dei dati di telemetria di alcune interazioni dell'utente con il servizio.

## <a name="delete-account-data-with-the-rest-api"></a>Eliminare i dati dell'account con l'API REST 

Per eliminare i dati, è possibile effettuare le chiamate API seguenti con il verbo HTTP DELETE. L'autorizzazione delle chiamate API deriva dalla presenza di un'intestazione`Authorization: Bearer <arm-token>` nella richiesta, dove `<arm-token>` è il token di accesso AAD per l'endpoint `https://management.core.windows.net/`.  

Per informazioni su come ottenere il token e chiamare gli endpoint di Azure, vedere la [documentazione dell'API REST di Azure](https://docs.microsoft.com/rest/api/azure/).  

Negli esempi seguenti sostituire il testo in {} con i nomi delle istanze che determinano la risorsa associata.

## <a name="delete-from-a-hosting-account"></a>Eliminare da un account di hosting

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningModelManagement/accounts/{account-name}?api-version=2017-09-01-preview      

## <a name="delete-from-the-model-management-service"></a>Eliminare dal servizio di gestione modelli

### <a name="model-document"></a>Documento dei modelli
Usare questa chiamata per ottenere un elenco dei modelli e i relativi ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

I singoli modelli possono essere eliminati con:  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview

### <a name="manifest-document"></a>Documento dei manifesti
Usare questa chiamata per ottenere un elenco di tutti i manifesti e i relativi ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

I singoli manifesti possono essere eliminati con:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="service-documents"></a>Documenti dei servizi
Usare questa chiamata per ottenere un elenco di tutti i servizi e i relativi ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

I singoli servizi possono essere eliminati con:    

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="image-document"></a>Documento delle immagini
Usare questa chiamata per ottenere un elenco di tutte le immagini e i relativi ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

Le singole immagini possono essere eliminate con:  

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview

## <a name="delete-run-history-artifact-and-notification-data"></a>Eliminare i dati della cronologia di esecuzione, degli elementi e delle notifiche
I dati della cronologia di esecuzione, degli elementi e delle notifiche archiviati per un progetto vengono eliminati dopo l'eliminazione del documento di progetto corrispondente:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview
    
## <a name="delete-from-experimentation-account-resource-provider"></a>Eliminare dal provider di risorse dell'account di sperimentazione
I documenti del progetto vengono eliminati con:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}/projects/{project-name}?api-version=2017-05-01-preview

I documenti dell'area di lavoro vengono eliminati con:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}/workspaces/{workspace-name}?api-version=2017-05-01-preview

L'intero account di sperimentazione viene eliminato con:
    
    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.MachineLearningExperimentation/accounts/{account-name}?api-version=2017-05-01-preview


## <a name="export-service-data-with-the-rest-api"></a>Esportare i dati del servizio con l'API REST
Per esportare i dati, è possibile effettuare le chiamate API seguenti con il verbo HTTP GET. L'autorizzazione delle chiamate API deriva dalla presenza di un'intestazione`Authorization: Bearer <arm-token>` nella richiesta, dove `<arm-token>` è il token di accesso AAD per l'endpoint `https://management.core.windows.net/`.  

Per informazioni su come ottenere il token e chiamare gli endpoint di Azure, vedere la [documentazione dell'API REST di Azure](https://docs.microsoft.com/rest/api/azure/).   

Negli esempi seguenti sostituire il testo in {} con i nomi delle istanze che determinano la risorsa associata.

## <a name="export-hosting-account-data"></a>Esportare i dati dell'account di hosting

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningModelManagement/accounts/{accountName}?api-version=2017-09-01-preview     

## <a name="export-model-management-service-data"></a>Esportare i dati del servizio di gestione modelli
### <a name="model-document"></a>Documento dei modelli

Usare questa chiamata per ottenere un elenco dei modelli e i relativi ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models?api-version=2017-09-01-preview"

I singoli modelli possono essere ottenuti con:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/models/{modelId}?api-version=2017-09-01-preview 

### <a name="manifests"></a>Manifesti
Usare questa chiamata per ottenere un elenco di tutti i manifesti e i relativi ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests?api-version=2017-09-01-preview

I singoli manifesti possono essere ottenuti con:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/manifests/{manifestId}?api-version=2017-09-01-preview

### <a name="services"></a>Services
Usare questa chiamata per ottenere un elenco di tutti i servizi e i relativi ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services?api-version=2017-09-01-preview

I singoli servizi possono essere ottenuti con: 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/services/{serviceName}?api-version=2017-09-01-preview

### <a name="images"></a>Immagini
Usare questa chiamata per ottenere un elenco di tutte le immagini e i relativi ID:

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images?api-version=2017-09-01-preview

I singoli servizi possono essere ottenuti con: 

    https://{location}.modelmanagement.azureml.net/api/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/accounts/{accountName}/images/{imageId}?api-version=2017-09-01-preview     

## <a name="export-compute-data"></a>Esportare i dati di calcolo
### <a name="compute-clusters"></a>Cluster di elaborazione
Usare questa chiamata per ottenere un elenco di tutti i cluster di elaborazione e i relativi nomi:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes?api-version=2018-03-01-preview

I singoli cluster possono essere ottenuti con:

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningServices/workspaces/{workspaceName}/computes/{compute-name}?api-version=2018-03-01-preview

### <a name="operationalization-clusters"></a>Cluster di operazionalizzazione
Usare questa chiamata per ottenere un elenco di tutti i cluster e i relativi nomi:

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters?api-version=2017-06-01-preview

I singoli cluster possono essere ottenuti con:

    https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.MachineLearningCompute/operationalizationClusters/{clusterName}?api-version=2017-06-01-preview

## <a name="export-run-history-data"></a>Esportare i dati della cronologia di esecuzione
Usare questa chiamata per ottenere un elenco di tutte le esecuzioni e i relativi ID:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs

Usare questa chiamata per ottenere un elenco di tutte le sperimentazioni e i relativi ID:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments

Gli elementi della cronologia di esecuzione possono essere ottenuti con:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}

Gli elementi della metrica di esecuzione possono essere ottenuti con:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runmetrics

Le sperimentazioni di esecuzione possono essere ottenute con:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/experiments/{experimentId}    

Elementi della cronologia di esecuzione:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts

URI degli elementi della cronologia di esecuzione:

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacturi?name={artifactName}

## <a name="export-artifacts"></a>Esportare gli elementi
Usare questa chiamata per ottenere un elenco degli asset e i relativi nomi:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets

Usare questa chiamata per ottenere un elenco degli elementi e i relativi percorsi:

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/origins/{origin}/containers/{runId}
        
        
### <a name="artifact-contents"></a>Contenuto dell'elemento

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/artifacts/contentinfo/ExperimentRun/{runId}/{artifactPath}

### <a name="artifact-documents"></a>Documenti degli elementi

    https://{location}.experiments.azureml.net/history/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/runs/{runId}/artifacts
        
### <a name="assets"></a>Asset

    https://{location}.experiments.azureml.net/artifact/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/assets/name/{name}

## <a name="export-notifications"></a>Esportare le notifiche

1. Passare alla [sezione Utenti del portale di Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/) e quindi selezionare un utente dalla colonna **Nome**. 
2. Prendere nota dell'**ID oggetto** e usarlo nella chiamata seguente:     

        https://{location}.experiments.azureml.net/notification/v1.0/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}/users/{objectId}/jobs

## <a name="export-experimentation-account-information"></a>Esportare le informazioni sull'account di sperimentazione
### <a name="experimentation-account-information"></a>Informazioni sull'account di sperimentazione

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}?api-version=2017-05-01-preview
        
### <a name="workspace-information"></a>Informazioni sull'area di lavoro

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}?api-version=2017-05-01-preview  

### <a name="project-information"></a>Informazioni sul progetto

    https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.MachineLearningExperimentation/accounts/{accountName}/workspaces/{workspaceName}/projects/{projectName}?api-version=2017-05-01-preview
