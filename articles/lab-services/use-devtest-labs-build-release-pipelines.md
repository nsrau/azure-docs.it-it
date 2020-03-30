---
title: Usare DevTest Labs nelle pipeline di compilazione e versione di Azure Pipelines
description: Informazioni su come usare Azure DevTest Labs nelle pipeline di compilazione e rilascio delle pipeline di Azure.Learn how to use Azure DevTest Labs in Azure Pipelines build and release pipelines.
services: devtest-lab, lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: e16f3c5a0c0b2b86d6a893f541cefb275a8e7d07
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169230"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>Usare DevTest Labs nelle pipeline di compilazione e versione di Azure Pipelines
Questo articolo fornisce informazioni su come DevTest Labs può essere usato nelle pipeline di compilazione e rilascio delle pipeline di Azure.This article provides information on how DevTest Labs can be used in Azure Pipelines build and release pipelines. 

## <a name="overall-flow"></a>Flusso complessivo
Il flusso di base consiste nel disporre di una pipeline di **compilazione** che esegue le attività seguenti:The basic flow is to have a build pipeline that does the following tasks:

1. Compilare il codice dell'applicazione.
1. Creare l'ambiente di base in DevTest Labs.Create the base environment in DevTest Labs.
1. Aggiornare l'ambiente con informazioni personalizzate.
1. Distribuire l'applicazione nell'ambiente DevTest Labs
1. Testare il codice. 

Una volta completata la compilazione, la pipeline di **rilascio** utilizzerà gli elementi di compilazione per distribuire la gestione temporanea o la produzione. 

Una delle premesse necessarie è che tutte le informazioni necessarie per ricreare l'ecosistema testato siano disponibili all'interno degli elementi di compilazione, inclusa la configurazione delle risorse di Azure.One of the necessary premises is that all the information needed to recreate the tested ecosystem is available within the build artifacts, including the configuration of the Azure resources. Poiché le risorse di Azure comportano un costo quando vengono usate, le aziende vogliono controllare o tenere traccia dell'uso di queste risorse. In alcune situazioni, i modelli di Azure Resource Manager usati per creare e configurare le risorse possono essere gestiti da un altro reparto come il reparto IT. Questi modelli potrebbero essere archiviati in un repository diverso. Porta a una situazione interessante in cui una compilazione verrà creata e testata e sia il codice che la configurazione dovranno essere archiviati all'interno degli elementi di compilazione per ricreare correttamente il sistema nell'ambiente di produzione. 

Usando DevTest Labs durante la fase di compilazione/test, è possibile aggiungere modelli di Azure Resource Manager e file di supporto alle origini di compilazione in modo che durante la fase di rilascio l'esatta configurazione usata per il test venga distribuita nell'ambiente di produzione. L'attività **Crea ambiente Azure DevTest Labs** con la configurazione corretta salverà i modelli di Resource Manager all'interno degli elementi di compilazione. Per questo esempio, si userà il codice [dell'esercitazione: Creare un'app Web .NET Core e database SQL nel servizio app di Azure](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)per distribuire e testare l'app Web in Azure.For this example, you'll be using the code from the Tutorial: Build a .NET Core and SQL Database web app in Azure App Service , to deploy and test the web app in Azure.

![Flusso complessivo](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Configurare le risorse di Azure
Ci sono un paio di elementi che devono essere creati in anticipo:

- Due repository. Il primo con il codice dell'esercitazione e un modello di Resource Manager con due macchine virtuali aggiuntive. Il secondo conterrà il modello di Azure Resource Manager di base (configurazione esistente).
- Gruppo di risorse per la distribuzione del codice di produzione e della configurazione.
- Un lab deve essere configurato con una connessione al repository di configurazione per la pipeline di [compilazione.](devtest-lab-create-environment-from-arm.md) Il modello di Resource Manager deve essere archiviato nel repository di configurazione come azuredeploy.json con metadata.json per consentire a DevTest Labs di riconoscere e distribuire il modello.

La pipeline di compilazione creerà un ambiente DevTest Labs e distribuirà il codice per il test.

## <a name="set-up-a-build-pipeline"></a>Impostare una pipeline di compilazioneSet up a build pipeline
In Pipeline di Azure creare una pipeline di compilazione usando il codice [dell'esercitazione: Creare un'app Web di database SQL e di .NET nel servizio app di Azure.In](../app-service/app-service-web-tutorial-dotnetcore-sqldb.md)Azure Pipelines, create a build pipeline using the code from the Tutorial: Build a .NET Core and SQL Database web app in Azure App Service . Usare il modello **ASP.NET Core,** che popolerà l'attività necessaria per compilare, testare e pubblicare il codice.

![Selezionare il modello di ASP.NET](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

È necessario aggiungere tre attività aggiuntive per creare l'ambiente in DevTest Labs e distribuirlo nell'ambiente.

![Pipeline con tre attività](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>Attività crea ambiente
Nell'attività Crea ambiente (attività Crea ambiente di**Azure DevTest Labs)** usare gli elenchi a discesa per selezionare i valori seguenti:In the create environment task ( Azure DevTest Labs Create Environment task) use the drop-down lists to select the following values:

- Sottoscrizione di Azure
- nome del laboratorio
- nome del repository
- nome del modello (che mostra la cartella in cui è memorizzato l'ambiente). 

Si consiglia di utilizzare gli elenchi a discesa nella pagina anziché immettere le informazioni manualmente. Se si immettono manualmente le informazioni, immettere gli ID risorsa di Azure completi. L'attività visualizza i nomi descrittivi anziché gli ID risorsa. 

The environment name is the displayed name shown within DevTest Labs. Deve essere un nome univoco per ogni compilazione. Ad esempio: **TestEnv (Build.BuildId)**. 

È possibile specificare un file di parametri o parametri per passare informazioni nel modello di Resource Manager.You can specify either parameters file or parameters to pass information into the Resource Manager template. 

Selezionare l'opzione Crea variabili di **output in base all'output del modello** di ambiente e immettere un nome di riferimento. Per questo esempio, immettere **BaseEnv** come nome di riferimento. Si utilizzerà questo BaseEnv durante la configurazione dell'attività successiva. 

![Creare l'attività Ambiente di Azure DevTest LabsCreate Azure DevTest Labs Environment task](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>Attività di popolamento dell'ambiente
La seconda attività (attività**Popola ambiente di Azure DevTest Labs)** consiste nell'aggiornare l'ambiente DevTest Labs esistente. L'attività Crea ambiente restituisce **BaseEnv.environmentResourceId** usato per configurare il nome dell'ambiente per questa attività. Il modello di Resource Manager per questo esempio include due parametri: **adminUserName** e **adminPassword**. 

![Attività Popola ambiente ambiente laboratori di Azure DevTest](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>Attività di distribuzione del servizio app
La terza attività è l'attività Di distribuzione del servizio app di **Azure.The** third task is the Azure App Service Deploy task. Il tipo di app è impostato su **App Web** e il nome del servizio app è impostato su **(SitoWeb).**

![Attività di distribuzione del servizio app](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>Configurare la pipeline di rilascio
Creare una pipeline di rilascio con due attività: **Distribuzione di Azure: creare o aggiornare** il gruppo di risorse e distribuire il servizio app di Azure.You create a release pipeline with two tasks: Azure Deployment: Create or Update Resource Group and **Deploy Azure App Service**. 

Per la prima attività, specificare il nome e il percorso del gruppo di risorse. Il percorso del modello è un elemento collegato. Se il modello di Resource Manager include modelli collegati, è necessario implementare una distribuzione del gruppo di risorse personalizzato. Il modello si trova nell'elemento di rilascio pubblicato. Eseguire l'override dei parametri del modello per il modello di Resource Manager.Override template parameters for the Resource Manager template. È possibile lasciare le impostazioni rimanenti con i valori predefiniti. 

Per la seconda attività **Deploy Azure App Service**, specificare la sottoscrizione di Azure, selezionare Web **App** per il tipo **di app**e il nome del **servizio app** **.** È possibile lasciare le impostazioni rimanenti con i valori predefiniti. 

## <a name="test-run"></a>Esecuzione dei test
Ora che entrambe le pipeline sono impostate, accodare manualmente una compilazione e verificarne il funzionamento. Il passaggio successivo consiste nel impostare il trigger appropriato per la compilazione e connettere la compilazione alla pipeline di rilascio.

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

- [Integrare i laboratori DevTest di Azure nella pipeline di integrazione e distribuzione continua delle pipeline di AzureIntegrate Azure DevTest Labs into your Azure Pipelines continuous integration and delivery pipeline](devtest-lab-integrate-ci-cd-vsts.md)
- [Integrare gli ambienti nelle pipeline CI/CD delle pipeline di AzureIntegrate environments into your Azure Pipelines CI/CD pipelines](integrate-environments-devops-pipeline.md)
