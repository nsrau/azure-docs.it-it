---
title: 'Servizi Web di Azure Machine Learning: distribuzione e utilizzo | Documentazione Microsoft'
description: Risorse per la distribuzione e l'uso dei servizi Web.
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: raymondl
editor: 
ms.assetid: 47635376-d1f4-4ea4-a6af-bd1f99f69a69
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2017
ms.author: v-donglo
ms.openlocfilehash: 9841b61c21586976ee06ffc1fe4d01e3fc3428b6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-machine-learning-web-services-deployment-and-consumption"></a>Servizi Web di Azure Machine Learning: distribuzione e uso
È possibile usare Azure Machine Learning (Azure ML) per distribuire i flussi di lavoro e i modelli di Machine Learning come servizi Web. Questi servizi Web possono quindi essere usati per chiamare i modelli di Machine Learning dalle applicazioni tramite Internet per eseguire stime in tempo reale o in modalità batch. Essendo RESTFul, i servizi Web possono essere chiamati da diversi linguaggi di programmazione e piattaforme, come .NET e Java, nonché applicazioni, come Excel.

Le sezioni successive forniscono collegamenti a procedure dettagliate, codice e documentazione per aiutarvi a iniziare.

## <a name="deploy-a-web-service"></a>Distribuire un servizio Web
### <a name="with-azure-machine-learning-studio"></a>Con Azure Machine Learning Studio
Machine Learning Studio e il portale dei servizi Web di Microsoft Azure Machine Learning aiutano a distribuire e gestire un servizio Web senza dover scrivere codice.

I collegamenti seguenti offrono informazioni generali su come distribuire un nuovo servizio Web:

* Per una panoramica di come distribuire un nuovo servizio Web basato su Azure Resource Manager, vedere [Distribuire un nuovo servizio Web](publish-a-machine-learning-web-service.md).
* Per una procedura dettagliata su come distribuire un servizio Web, vedere [Distribuire un servizio Web di Azure Machine Learning](publish-a-machine-learning-web-service.md).
* Per una procedura dettagliata su come creare e distribuire un servizio Web, vedere [Passaggio 1 della procedura dettagliata: Creare un'area di lavoro di Machine Learning](walkthrough-1-create-ml-workspace.md).
* Per esempi specifici di distribuzione di un servizio Web, vedere:

  * [Passaggio 5 della procedura dettagliata: Distribuzione del servizio Web di Azure Machine Learning](walkthrough-5-publish-web-service.md)
  * [Come distribuire un servizio Web in più aree](how-to-deploy-to-multiple-regions.md)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Con le API del provider di risorse di servizi Web (API di Azure Resource Manager)
Il provider di risorse di Azure Machine Learning per i servizi Web consente di distribuire e gestire servizi Web tramite chiamate all'API REST. Per altre informazioni, vedere i riferimenti al [servizio Web di Machine Learning (REST)](/rest/api/machinelearning/index).

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->


### <a name="with-powershell-cmdlets"></a>Con i cmdlet di PowerShell
Il provider di risorse di Azure Machine Learning per i servizi Web consente di distribuire e gestire i servizi Web tramite cmdlet di PowerShell.

Per usare i cmdlet è prima necessario accedere al proprio account Azure dall'interno dell'ambiente di PowerShell tramite il cmdlet [Add-AzureRmAccount](https://msdn.microsoft.com/library/mt619267.aspx) . Se non si ha familiarità con la chiamata di comandi di PowerShell basati su Resource Manger, vedere [Uso di Azure PowerShell con Azure Resource Manager](../../azure-resource-manager/powershell-azure-resource-manager.md#log-in-to-your-azure-account).

Usare questo [codice di esempio](https://github.com/ritwik20/AzureML-WebServices)per esportare l'esperimento predittivo. Dopo aver creato il file .exe dal codice è possibile digitare:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Con l'esecuzione dell'applicazione viene creato un modello JSON di servizio Web. Per usare il modello per distribuire un servizio Web è necessario aggiungere le informazioni seguenti:

* Nome e chiave dell'account di archiviazione

    È possibile ottenere il nome e la chiave dell'account di archiviazione dal [portale di Azure](https://portal.azure.com/) o dal [portale di Azure classico](http://manage.windowsazure.com/).
* ID del piano di impegno

    È possibile ottenere l'ID del piano dal portale dei [servizi Web di Azure Machine Learning](https://services.azureml.net) eseguendo l'accesso e facendo clic sul nome di un piano.

Aggiungere le informazioni al modello JSON come figli del nodo *Properties* allo stesso livello del nodo *MachineLearningWorkspace*.

Ad esempio:

    "StorageAccount": {
            "name": "YourStorageAccountName",
            "key": "YourStorageAccountKey"
    },
    "CommitmentPlan": {
        "id": "subscriptions/YouSubscriptionID/resourceGroups/YourResourceGroupID/providers/Microsoft.MachineLearning/commitmentPlans/YourPlanName"
    }

Per altre informazioni, vedere gli articoli e il codice di esempio seguenti:

* [cmdlet di Azure Machine Learning](https://msdn.microsoft.com/library/azure/mt767952.aspx) in MSDN
* [Procedura dettagliata](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) di esempio su GitHub

## <a name="consume-the-web-services"></a>Utilizzare i servizi Web
### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Dall'interfaccia utente dei servizi Web di Azure Machine Learning (test)
È possibile testare il servizio Web dal portale dei servizi Web di Azure Machine Learning. Sono inclusi i test delle interfacce del Servizio di richiesta-risposta (RRS) e del Servizio Esecuzione batch (BES).

* [Distribuire un nuovo servizio Web](publish-a-machine-learning-web-service.md)
* [Distribuire un servizio Web di Azure Machine Learning](publish-a-machine-learning-web-service.md)
* [Passaggio 5 della procedura dettagliata: Distribuzione del servizio Web di Azure Machine Learning](walkthrough-5-publish-web-service.md)

### <a name="from-excel"></a>Da Excel
È possibile scaricare un modello di Excel che usa il servizio Web:

* [Utilizzo di un servizio Web di Azure Machine Learning da Excel](consuming-from-excel.md)
* [Componente aggiuntivo Excel per i servizi Web di Azure Machine Learning](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Da un client basato su REST
I servizi Web di Azure Machine Learning sono API RESTful. È possibile usare queste API da diverse piattaforme quali .NET, Python, R, Java e così via. La pagina **di utilizzo** del servizio Web nel [portale dei servizi Web di Microsoft Azure Machine Learning](https://services.azureml.net) offre codice di esempio che può essere utile per iniziare. Per altre informazioni, vedere [Come usare un servizio Web di Azure Machine Learning pubblicato da un esperimento di Machine Learning](consume-web-services.md).
