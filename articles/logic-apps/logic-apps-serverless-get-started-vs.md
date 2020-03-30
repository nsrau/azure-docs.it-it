---
title: Crea la prima app senza server in Visual Studio
description: Creare, distribuire e gestire un'app senza server usando app per la logica di Azure e Funzioni di Azure in Visual StudioBuild, deploy, and manage a serverless app by using Azure Logic Apps and Azure Functions in Visual Studio
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 2e27958dd9379a26ca7e7f4d7e427e5afa216e29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75981156"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Crea la tua prima app senza server usando le app per la logica di Azure e le funzioni di Azure in Visual Studio

È possibile sviluppare e distribuire rapidamente app cloud usando gli strumenti e le funzionalità senza server in Azure, ad esempio [App per la logica](../logic-apps/logic-apps-overview.md) di Azure e Funzioni di [Azure.](../azure-functions/functions-overview.md) Questo articolo illustra come iniziare a compilare un'app senza server che usa un'app per la logica che chiama una funzione di Azure in Visual Studio. Per informazioni sulle soluzioni senza server in Azure, vedere [Azure Serverless with Functions and Logic Apps](../logic-apps/logic-apps-serverless-overview.md) (Architettura senza server di Azure con Funzioni e App per la logica).

## <a name="prerequisites"></a>Prerequisiti

Per creare un'app senza server in Visual Studio, è necessario:To build a serverless app in Visual Studio, you need:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* I seguenti strumenti. Se non li hai già, scaricali e installali.

  * [Visual Studio 2019, 2017 o 2015 (Community o un'altra edizione)](https://aka.ms/download-visual-studio). 
  Questa guida introduttiva usa Visual Studio Community 2017, disponibile gratuitamente.

    > [!IMPORTANT]
    > Quando si installa Visual Studio 2019 o 2017, assicurarsi di selezionare il carico di lavoro **Sviluppo di Azure**.

  * [Microsoft Azure SDK per .NET (versione 2.9.1 o successiva)](https://azure.microsoft.com/downloads/). 
  Altre informazioni su [Azure SDK per .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Strumenti App per la logica di Azure per la versione di Visual Studio desiderata:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    È anche possibile scaricare e installare Strumenti App per la logica di Azure direttamente da Visual Studio Marketplace o [installare questa estensione da Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Assicurarsi di riavviare Visual Studio al termine dell'installazione.

  * [Strumenti di base](https://www.npmjs.com/package/azure-functions-core-tools) di Funzioni di Azure per il debug locale delle funzioni.

* Accesso al Web durante l'utilizzo di Progettazione app per la logica incorporata.

  La finestra di progettazione richiede una connessione Internet per creare le risorse in Azure e leggere le proprietà e i dati dai connettori nell'app per la logica. 
  Ad esempio, se si usa il connettore per Dynamics CRM Online, la finestra di progettazione verifica la disponibilità di proprietà predefinite e personalizzate nell'istanza di CRM.

## <a name="create-a-resource-group-project"></a>Creare un progetto di gruppo di risorseCreate a resource group project

Per iniziare, creare un [progetto Gruppo di risorse di Azure](../azure-resource-manager/templates/create-visual-studio-deployment-project.md) per l'app senza server. In Azure si creano risorse all'interno di un gruppo di *risorse,* che è una raccolta logica usata per organizzare, gestire e distribuire le risorse per un'intera app come singolo asset. Per un'app senza server in Azure, il gruppo di risorse include risorse sia per App per la logica di Azure sia per Funzioni di Azure. È possibile accedere ad altre informazioni sui [gruppi di risorse e le risorse di Azure](../azure-resource-manager/management/overview.md).

1. Avviare Visual Studio e accedere usando l'account Azure.Start Visual Studio and sign in by using your Azure account.

1. Scegliere **Nuovo** > **Progetto** dal menu **File**

   ![Creare un nuovo progetto in Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. In **Installato** selezionare **Visual C#** o **Visual Basic**. Selezionare quindi**Gruppo di risorse di Azure** **cloud.** > 

   > [!NOTE]
   > Se la categoria Cloud o il progetto gruppo di risorse di Azure non esiste, assicurarsi di aver installato Azure SDK per Visual Studio.If the **Cloud** category or **Azure Resource Group** project doesn't exist, make sure that you installed the Azure SDK for Visual Studio.

   Se si usa Visual Studio 2019, procedere come segue:

   1. Nella casella **Crea un nuovo progetto** selezionare il modello di progetto Gruppo di risorse di **Azure** per Visual Cè o Visual Basic e quindi scegliere **Avanti**.

   1. Specificare il nome e altre informazioni sul progetto che si vuole usare per il gruppo di risorse di Azure.Provide the name and other project information that you want to use for the Azure resource group. Al termine, selezionare **Crea**.

1. Assegnare un nome e un percorso al progetto, quindi scegliere **OK**.

   Visual Studio richiede di selezionare un modello dall'elenco dei modelli. 
   Questo esempio usa un modello di Guida introduttiva di Azure per creare un'app senza server che include un'app per la logica e una chiamata a una funzione di Azure.This example uses an Azure QuickStart template so that you can build a serverless app that includes a logic app and a call to an Azure function.

   > [!TIP]
   > Negli scenari in cui non si vuole predistribuire la soluzione in un gruppo di risorse di Azure, è possibile usare il modello di **app per** la logica vuoto, che crea solo un'app per la logica vuota.

1. Nell'elenco **Mostra modelli da questa posizione** selezionare Guida rapida di Azure **(github.com/Azure/azure-quickstart-templates)**.

1. Nella casella di ricerca immettere "logic-app" come filtro. Dai risultati, seleziona il modello **101-logic-app-and-function-app.**

   ![Selezionare il modello Di Azure QuickStart](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio crea e visualizza una soluzione per il progetto Gruppo di risorse. 
   Il modello di Guida rapida di Azure selezionato crea un modello di distribuzione denominato azuredeploy.json all'interno del progetto del gruppo di risorse. Questo modello di distribuzione include la definizione per un'app per la logica semplice attivata da una richiesta HTTP, chiama una funzione di Azure e restituisce il risultato come risposta HTTP.

   ![Nuova soluzione senza server](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Distribuire quindi la soluzione in Azure.Next, deploy your solution to Azure. È necessario eseguire questa operazione prima di poter aprire il modello di distribuzione ed esaminare le risorse per l'app senza server.

## <a name="deploy-your-solution"></a>Distribuire la soluzione

Prima di poter aprire l'app per la logica in Progettazione app per la logica in Visual Studio, è necessario disporre di un gruppo di risorse di Azure già distribuito in Azure.Before you can open your logic app in the Logic App Designer in Visual Studio, you must have an Azure resource group that's already deployed in Azure. Il progettista può quindi creare le connessioni ai servizi e alle risorse disponibili nell'app per la logica. Per questa attività, seguire questi passaggi per distribuire la soluzione da Visual Studio al portale di Azure:For this task, follow these steps to deploy your solution from Visual Studio to the Azure portal:

1. In Esplora soluzioni scegliere **Distribuisci** > **nuovo**dal menu di scelta rapida del progetto di risorsa.

   ![Creare una nuova distribuzione per il gruppo di risorse](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Se non sono già selezionati, selezionare la sottoscrizione di Azure e il gruppo di risorse in cui si vuole distribuire. Selezionare **quindi Distribuisci**.

   ![Impostazioni di distribuzione](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Se viene visualizzata la casella **Modifica parametri,** specificare i nomi delle risorse da usare per l'app per la logica e l'app per le funzioni di Azure durante la distribuzione, quindi salvare le impostazioni. Assicurarsi di usare un nome univoco globale per l'app per le funzioni.

   ![Assegnare un nome all'app per la logica e all'app per le funzioni](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Quando Visual Studio avvia la distribuzione nel gruppo di risorse specificato, lo stato di distribuzione della soluzione sarà visualizzato nella finestra **Output** di Visual Studio. 
   Al termine della distribuzione, l'app per la logica sarà attiva nel portale di Azure.

## <a name="edit-your-logic-app-in-visual-studio"></a>Modificare l'app per la logica in Visual Studio

Per modificare l'app per la logica dopo la distribuzione, aprire l'app per la logica usando La finestra di progettazione app per la logica in Visual Studio.To edit your logic app after deployment, open your logic app by using the Logic App Designer in Visual Studio.

1. In Esplora soluzioni scegliere **Apri con Progettazione app per**la logica dal menu di scelta rapida del file azuredeploy.json .

   ![Aprire azuredeploy.json in Progettazione app per la logicaOpen azuredeploy.json in Logic App Designer](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Se non si dispone di questo comando in Visual Studio 2019, verificare di aver installato gli aggiornamenti più recenti per Visual Studio.

1. Dopo aver visualizzato la finestra **Proprietà dell'app** per la logica, in **Sottoscrizione**selezionare la sottoscrizione di Azure, se non è già selezionata. In **Gruppo di risorse**selezionare il gruppo di risorse e il percorso in cui è stata distribuita la soluzione, quindi scegliere **OK.**

   ![Proprietà dell'app per la logica](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Dopo che viene aperta la finestra di progettazione di app per la logica, è possibile continuare ad aggiungere passaggi o modificare il flusso di lavoro e salvare gli aggiornamenti.

   ![App per la logica aperta nella finestra di progettazione di app per la logica](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Creare il progetto Funzioni di AzureCreate your Azure Functions project

Per creare il progetto e la funzione di Funzioni usando JavaScript, Python, F , PowerShell, Batch o Bash, seguire i passaggi descritti in [Work with Azure Functions Core Tools](../azure-functions/functions-run-local.md). Per sviluppare la funzione di Azure usando C'è all'interno della soluzione, usare una libreria di classi C ,seguire i passaggi descritti in Pubblicare una libreria di classi [.NET come app](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/)per le funzioni .

## <a name="deploy-functions-from-visual-studio"></a>Distribuire le funzioni da Visual Studio

Il modello di distribuzione distribuisce tutte le funzioni di Azure presenti nella soluzione dal repository Git specificato dalle variabili nel file azuredeploy.json.Your deployment template deploys any Azure functions that you have in your solution from the Git repo that's specified by variables in the azuredeploy.json file. Se si crea e si crea il progetto Functions nella soluzione, è possibile archiviare il progetto nel `repo` controllo del codice sorgente Git (ad esempio, GitHub o Azure DevOps) e quindi aggiornare la variabile in modo che il modello distribuisca la funzione di Azure.If you create and author your Functions project in your solution, you can check that project into Git source control (for example, GitHub or Azure DevOps) and then update the variable so that the template deploys your Azure function.

## <a name="manage-logic-apps-and-view-run-history"></a>Gestire le app per la logica e visualizzare la cronologia di esecuzione

Per le app per la logica già distribuite in Azure, è comunque possibile modificare, gestire, visualizzare la cronologia di esecuzione e disabilitarle da Visual Studio.For logic apps already deployed in Azure, you can still edit, manage, view run history for, and disable those apps from Visual Studio.

1. Dal menu **Visualizza** di Visual Studio aprire **Cloud Explorer**.

1. In **Tutte le sottoscrizioni**selezionare la sottoscrizione di Azure associata alle app per la logica che si vuole gestire e quindi selezionare **Applica**.

1. In **App per la logica** selezionare l'app per la logica. Dal menu di scelta rapida dell'app selezionare **Apri con Editor app per la logica**.

   > [!TIP]
   > Se non si dispone di questo comando in Visual Studio 2019, verificare di aver installato gli aggiornamenti più recenti per Visual Studio.

Ora è possibile scaricare l'app per la logica già pubblicata nel progetto Gruppo di risorse. Pertanto, anche se potrebbe essere stata avviata un'app per la logica nel portale di Azure, è comunque possibile importare e gestire tale app in Visual Studio.So, although you might have started a logic app in the Azure portal, you can still import and manage that app in Visual Studio. Per altre informazioni, vedere [Gestire le app per la logica con Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Passaggi successivi

* [Gestire app per la logica con Visual Studio](manage-logic-apps-with-visual-studio.md)
