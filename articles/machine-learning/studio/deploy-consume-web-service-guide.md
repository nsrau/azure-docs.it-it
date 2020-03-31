---
title: Distribuzione e utilizzo
titleSuffix: ML Studio (classic) - Azure
description: È possibile usare Azure Machine Learning Studio (classico) per distribuire flussi di lavoro e modelli di Machine Learning come servizi Web.You can use Azure Machine Learning Studio (classic) to deploy machine learning workflows and models as web services. Questi servizi Web possono quindi essere usati per chiamare i modelli di Machine Learning dalle applicazioni tramite Internet allo scopo di eseguire stime in tempo reale o in modalità batch.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 04/19/2017
ms.openlocfilehash: ff6ae0de0bbd8c47b81fa5066a97eb0b3e0cf6bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204393"
---
# <a name="azure-machine-learning-studio-classic-web-services-deployment-and-consumption"></a>Servizi Web di Azure Machine Learning Studio (classici): distribuzione e utilizzo

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

È possibile usare Azure Machine Learning Studio (classico) per distribuire flussi di lavoro e modelli di Machine Learning come servizi Web.You can use Azure Machine Learning Studio (classic) to deploy machine learning workflows and models as web services. Questi servizi Web possono quindi essere usati per chiamare i modelli di Machine Learning dalle applicazioni tramite Internet allo scopo di eseguire stime in tempo reale o in modalità batch. Essendo RESTFul, i servizi Web possono essere chiamati da diversi linguaggi di programmazione e piattaforme, come .NET e Java, nonché applicazioni, come Excel.

Le sezioni successive forniscono collegamenti a procedure dettagliate, codice e documentazione per aiutarvi a iniziare.

## <a name="deploy-a-web-service"></a>Distribuire un servizio Web

### <a name="with-azure-machine-learning-studio-classic"></a>Con Azure Machine Learning Studio (classico)With Azure Machine Learning Studio (classic)

Il portale di Studio (classico) e il portale dei servizi Web di Microsoft Azure Machine Learning consentono di distribuire e gestire un servizio Web senza scrivere codice.

I collegamenti seguenti offrono informazioni generali su come distribuire un nuovo servizio Web:

* Per una panoramica di come distribuire un nuovo servizio Web basato su Azure Resource Manager, vedere [Distribuire un nuovo servizio Web](deploy-a-machine-learning-web-service.md).
* Per una procedura dettagliata su come distribuire un servizio Web, vedere [Distribuire un servizio Web di Azure Machine Learning](deploy-a-machine-learning-web-service.md).
* Per una procedura dettagliata completa su come creare e distribuire un servizio Web, iniziare con [Esercitazione 1: Prevedere il rischio di credito](tutorial-part1-credit-risk.md).
* Per esempi specifici di distribuzione di un servizio Web, vedere:

  * [Esercitazione 3: Distribuire il modello di rischio di creditoTutorial 3: Deploy credit risk model](tutorial-part3-credit-risk-deploy.md)
  * [Come distribuire un servizio Web in più aree](deploy-a-machine-learning-web-service.md#multi-region)

### <a name="with-web-services-resource-provider-apis-azure-resource-manager-apis"></a>Con le API del provider di risorse di servizi Web (API di Azure Resource Manager)

Il provider di risorse di Azure Machine Learning Studio (classico) per i servizi Web consente la distribuzione e la gestione dei servizi Web tramite chiamate API REST. Per altre informazioni, vedere i riferimenti al [servizio Web di Machine Learning (REST)](/rest/api/machinelearning/index).

<!-- [Machine Learning Web Service (REST)](https://msdn.microsoft.com/library/azure/mt767538.aspx) reference. -->

### <a name="with-powershell-cmdlets"></a>Con i cmdlet di PowerShell

Il provider di risorse di Azure Machine Learning Studio (classico) per i servizi Web consente la distribuzione e la gestione dei servizi Web tramite i cmdlet di PowerShell.The Azure Machine Learning Studio (classic) resource provider for web services enables deployment and management of web services by using PowerShell cmdlets.

Per utilizzare i cmdlet, è innanzitutto necessario accedere all'account Azure dall'ambiente PowerShell utilizzando il cmdlet [Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount) Se non si ha familiarità con la chiamata di comandi di PowerShell basati su Resource Manger, vedere [Uso di Azure PowerShell con Azure Resource Manager](../../azure-resource-manager/management/manage-resources-powershell.md).

Usare questo [codice di esempio](https://github.com/ritwik20/AzureML-WebServices)per esportare l'esperimento predittivo. Dopo aver creato il file .exe dal codice è possibile digitare:

    C:\<folder>\GetWSD <experiment-url> <workspace-auth-token>

Con l'esecuzione dell'applicazione viene creato un modello JSON di servizio Web. Per usare il modello per distribuire un servizio Web è necessario aggiungere le informazioni seguenti:

* Nome e chiave dell'account di archiviazione

    È possibile ottenere il nome e la chiave dell'account di archiviazione dal [portale di Azure](https://portal.azure.com/).
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

* Informazioni di riferimento sui cmdlet di [Azure Machine Learning Studio (classico)](https://docs.microsoft.com/powershell/module/az.machinelearning) in MSDN
* [Procedura dettagliata](https://github.com/raymondlaghaeian/azureml-webservices-arm-powershell/blob/master/sample-commands.txt) di esempio su GitHub

## <a name="consume-the-web-services"></a>Utilizzare i servizi Web

### <a name="from-the-azure-machine-learning-web-services-ui-testing"></a>Dall'interfaccia utente dei servizi Web di Azure Machine Learning (test)

È possibile testare il servizio Web dal portale dei servizi Web di Azure Machine Learning. Sono inclusi i test delle interfacce del Servizio di richiesta-risposta (RRS) e del Servizio Esecuzione batch (BES).

* [Distribuire un nuovo servizio Web](deploy-a-machine-learning-web-service.md)
* [Distribuire un servizio Web di Azure Machine Learning](deploy-a-machine-learning-web-service.md)
* [Esercitazione 3: Distribuire il modello di rischio di creditoTutorial 3: Deploy credit risk model](tutorial-part3-credit-risk-deploy.md)

### <a name="from-excel"></a>Da Excel

È possibile scaricare un modello di Excel che usa il servizio Web:

* [Utilizzo di un servizio Web di Azure Machine Learning da Excel](consuming-from-excel.md)
* [Componente aggiuntivo di Excel per i servizi Web di Azure Machine Learning](excel-add-in-for-web-services.md)

### <a name="from-a-rest-based-client"></a>Da un client basato su REST

I servizi Web di Azure Machine Learning sono API RESTful. È possibile utilizzare queste API da varie piattaforme, ad esempio .NET, Python, R, Java e così via. La pagina **Utilizzo** per il servizio Web nel portale dei servizi Web di [Microsoft Azure Machine Learning](https://services.azureml.net) include codice di esempio che consente di iniziare. Per altre informazioni, vedere [Come usare un servizio Web di Azure Machine Learning pubblicato da un esperimento di Machine Learning](consume-web-services.md).
