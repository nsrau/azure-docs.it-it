---
title: Distribuzione e utilizzo
titleSuffix: ML Studio (classic) - Azure
description: You can use Azure Machine Learning Studio (classic) to deploy machine learning workflows and models as web services. Questi servizi Web possono quindi essere usati per chiamare i modelli di Machine Learning dalle applicazioni tramite Internet allo scopo di eseguire stime in tempo reale o in modalità batch.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: 1c97fd4f4d5646b6654f5261abd99372c521c389
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228242"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Azure Machine Learning Studio (classic) Web Services: Deployment and consumption

You can use Azure Machine Learning Studio (classic) to deploy machine learning workflows and models as web services. Questi servizi Web possono quindi essere usati per chiamare i modelli di Machine Learning dalle applicazioni tramite Internet allo scopo di eseguire stime in tempo reale o in modalità batch. Essendo RESTFul, i servizi Web possono essere chiamati da diversi linguaggi di programmazione e piattaforme, come .NET e Java, nonché applicazioni, come Excel.

Le sezioni successive forniscono collegamenti a procedure dettagliate, codice e documentazione per aiutarvi a iniziare.

## <a name="deploy-a-web-service"></a>Distribuire un servizio Web

### <a name="with-azure-machine-learning-studio-classic"></a>With Azure Machine Learning Studio (classic)

The Studio (classic) portal and the Microsoft Azure Machine Learning Web Services portal help you deploy and manage a web service without writing code.

I collegamenti seguenti offrono informazioni generali su come distribuire un nuovo servizio Web:

* Per una panoramica di come distribuire un nuovo servizio Web basato su Azure Resource Manager, vedere [Distribuire un nuovo servizio Web](deploy-a-machine-learning-web-service.md).
* Per una procedura dettagliata su come distribuire un servizio Web, vedere [Distribuire un servizio Web di Azure Machine Learning](deploy-a-machine-learning-web-service.md).
* For a full walkthrough about how to create and deploy a web service, start with [Tutorial 1: Predict credit risk](tutorial-part1-credit-risk.md).
* Per esempi specifici di distribuzione di un servizio Web, vedere:

  * [Tutorial 3: Deploy credit risk model](tutorial-part3-credit-risk-deploy.md)
  * [Come distribuire un servizio Web in più aree](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Con le API del provider di risorse di servizi Web (API di Azure Resource Manager)

The classic version of Azure Machine Learning Studio resource provider for web services enables deployment and management of web services by using REST API calls. Per altre informazioni, vedere i riferimenti al [servizio Web di Machine Learning (REST)](/rest/api/machinelearning/index).

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Con i cmdlet di PowerShell

The classic version of Azure Machine Learning Studio resource provider for web services enables deployment and management of web services by using PowerShell cmdlets.

To use the cmdlets, you must first sign in to your Azure account from within the PowerShell environment by using the [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet. Se non si ha familiarità con la chiamata di comandi di PowerShell basati su Resource Manger, vedere [Uso di Azure PowerShell con Azure Resource Manager](../../azure-resource-manager/manage-resources-powershell.md).

Usare questo [codice di esempio](https://github.com/ritwik20/AzureML-WebServices)per esportare l'esperimento predittivo. Dopo aver creato il file .exe dal codice è possibile digitare:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Con l'esecuzione dell'applicazione viene creato un modello JSON di servizio Web. Per usare il modello per distribuire un servizio Web è necessario aggiungere le informazioni seguenti:

* Nome e chiave dell'account di archiviazione

    È possibile ottenere il nome e la chiave dell'account di archiviazione dal [portale di Azure](https://portal.azure.com/).
* ID del piano di impegno

    È possibile ottenere l'ID del piano dal portale dei [servizi Web di Azure Machine Learning](https://services.azureml.net) eseguendo l'accesso e facendo clic sul nome di un piano.

Aggiungere le informazioni al modello JSON come figli del nodo *Properties* allo stesso livello del nodo *MachineLearningWorkspace*.

Ecco un esempio:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Per altre informazioni, vedere gli articoli e il codice di esempio seguenti:

* [Azure Machine Learning Studio (classic) Cmdlets](https://docs.microsoft.com/powershell/module/az.machinelearning) reference on MSDN
* [Procedura dettagliata](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) di esempio su GitHub

## <a name="consume-the-web-services"></a>Utilizzare i servizi Web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Dall'interfaccia utente dei servizi Web di Azure Machine Learning (test)

È possibile testare il servizio Web dal portale dei servizi Web di Azure Machine Learning. Sono inclusi i test delle interfacce del Servizio di richiesta-risposta (RRS) e del Servizio Esecuzione batch (BES).

* [Distribuire un nuovo servizio Web](deploy-a-machine-learning-web-service.md)
* [Distribuire un servizio Web di Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* [Tutorial 3: Deploy credit risk model](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Da Excel

È possibile scaricare un modello di Excel che usa il servizio Web:

* [Utilizzo di un servizio Web di Azure Machine Learning da Excel](consuming-from-excel.md)
* [Componente aggiuntivo Excel per i servizi Web di Azure Machine Learning](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Da un client basato su REST

I servizi Web di Azure Machine Learning sono API RESTful. You can consume these APIs from various platforms, such as .NET, Python, R, Java, etc. The **Consume** page for your web service on the [Microsoft Azure Machine Learning Web Services portal](https://services.azureml.net) has sample code that can help you get started. Per altre informazioni, vedere [Come usare un servizio Web di Azure Machine Learning pubblicato da un esperimento di Machine Learning](consume-web-services.md).
