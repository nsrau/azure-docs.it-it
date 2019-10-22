---
title: Crea la prima app senza server in Visual Studio-app per la logica di Azure
description: Creare, distribuire e gestire un'app senza server usando app per la logica di Azure e funzioni di Azure in Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.workload: vs-azure
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: 76ab76440ddc25f4b1cf51f7d540a6e21614541d
ms.sourcegitcommit: d37991ce965b3ee3c4c7f685871f8bae5b56adfa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "72680136"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Creare la prima app senza server usando app per la logica di Azure e funzioni di Azure in Visual Studio

Puoi sviluppare e distribuire rapidamente app cloud usando gli strumenti e le funzionalità senza server in Azure, ad esempio app per la [logica di Azure](../logic-apps/logic-apps-overview.md) e [funzioni di Azure](../azure-functions/functions-overview.md). Questo articolo illustra come iniziare a compilare un'app senza server che usa un'app per la logica che chiama una funzione di Azure in Visual Studio. Per informazioni sulle soluzioni senza server in Azure, vedere [Azure Serverless with Functions and Logic Apps](../logic-apps/logic-apps-serverless-overview.md) (Architettura senza server di Azure con Funzioni e App per la logica).

## <a name="prerequisites"></a>Prerequisiti

Per creare un'app senza server in Visual Studio, è necessario:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Gli strumenti seguenti. Se non sono già presenti, scaricarli e installarli.

  * [Visual Studio 2019, 2017 o 2015 (community o un'altra edizione)](https://aka.ms/download-visual-studio). 
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
    Assicurarsi di riavviare Visual Studio dopo avere completato l'installazione.

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) per le funzioni di debug locale.

* Accesso al Web con la finestra di progettazione di app per la logica incorporata.

  La finestra di progettazione richiede una connessione Internet per creare le risorse in Azure e leggere le proprietà e i dati dai connettori nell'app per la logica. 
  Ad esempio, se si usa il connettore di Dynamics CRM Online, la finestra di progettazione verifica le proprietà predefinite e personalizzate disponibili nell'istanza di CRM.

## <a name="create-a-resource-group-project"></a>Creare un progetto gruppo di risorse

Per iniziare, creare un [progetto Gruppo di risorse di Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) per l'app senza server. In Azure è possibile creare risorse all'interno di un *gruppo di risorse*, ovvero una raccolta logica da usare per organizzare, gestire e distribuire le risorse per un'intera app come singolo asset. Per un'app senza server in Azure, il gruppo di risorse include risorse sia per App per la logica di Azure sia per Funzioni di Azure. È possibile accedere ad altre informazioni sui [gruppi di risorse e le risorse di Azure](../azure-resource-manager/resource-group-overview.md).

1. Avviare Visual Studio e accedere usando l'account Azure.

1. Scegliere **Nuovo** > **Progetto** dal menu **File**

   ![Creare un nuovo progetto in Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. In **Installato** selezionare **Visual C#** o **Visual Basic**. Selezionare quindi **Cloud**  > **gruppo di risorse di Azure**.

   > [!NOTE]
   > Se la categoria **cloud** o il progetto **gruppo di risorse di Azure** non esiste, verificare di aver installato Azure SDK per Visual Studio.

   Se si usa Visual Studio 2019, procedere come segue:

   1. Nella casella **Crea un nuovo progetto** selezionare il modello di progetto **gruppo di risorse di Azure** per Visual C# o Visual Basic e quindi fare clic su **Avanti**.

   1. Fornire il nome e altre informazioni sul progetto che si vuole usare per il gruppo di risorse di Azure. Al termine, selezionare **Crea**.

1. Assegnare un nome e un percorso al progetto e quindi fare clic su **OK**.

   Visual Studio richiede di selezionare un modello dall'elenco modelli. 
   Questo esempio usa un modello di avvio rapido di Azure per poter compilare un'app senza server che include un'app per la logica e una chiamata a una funzione di Azure.

   > [!TIP]
   > Negli scenari in cui non si vuole predistribuire la soluzione in un gruppo di risorse di Azure, è possibile usare il modello di app per la **logica** vuota, che crea semplicemente un'app per la logica vuota.

1. Dall'elenco **Mostra modelli da questo percorso** selezionare **avvio rapido di Azure (github.com/Azure/Azure-QuickStart-templates)** .

1. Nella casella di ricerca immettere "Logic-app" come filtro. Nei risultati selezionare il modello **101-Logic-app-and-Function-app** .

   ![Selezionare il modello di avvio rapido di Azure](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio crea e visualizza una soluzione per il progetto Gruppo di risorse. 
   Il modello di avvio rapido di Azure selezionato consente di creare un modello di distribuzione denominato file azuredeploy. JSON nel progetto del gruppo di risorse. Questo modello di distribuzione include la definizione di una semplice app per la logica attivata da una richiesta HTTP, chiama una funzione di Azure e restituisce il risultato come risposta HTTP.

   ![Nuova soluzione senza server](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Distribuire quindi la soluzione in Azure. È necessario eseguire questa operazione prima di poter aprire il modello di distribuzione ed esaminare le risorse per l'app senza server.

## <a name="deploy-your-solution"></a>Distribuire la soluzione

Prima di poter aprire l'app per la logica in progettazione app per la logica in Visual Studio, è necessario avere un gruppo di risorse di Azure già distribuito in Azure. Il progettista può quindi creare le connessioni ai servizi e alle risorse disponibili nell'app per la logica. Per questa attività, seguire questa procedura per distribuire la soluzione da Visual Studio nel portale di Azure:

1. In Esplora soluzioni scegliere **distribuisci**  > **nuovo**dal menu di scelta rapida del progetto di risorse.

   ![Creare una nuova distribuzione per il gruppo di risorse](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Se non sono già selezionati, selezionare la sottoscrizione di Azure e il gruppo di risorse in cui si vuole eseguire la distribuzione. Selezionare quindi **Distribuisci**.

   ![Impostazioni di distribuzione](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Se viene visualizzata la casella **modifica parametri** , specificare i nomi delle risorse da usare per l'app per la logica e l'app per le funzioni di Azure in fase di distribuzione, quindi salvare le impostazioni. Assicurarsi di usare un nome univoco globale per l'app per le funzioni.

   ![Assegnare un nome all'app per la logica e all'app per le funzioni](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Quando Visual Studio avvia la distribuzione nel gruppo di risorse specificato, lo stato di distribuzione della soluzione sarà visualizzato nella finestra **Output** di Visual Studio. 
   Al termine della distribuzione, l'app per la logica sarà attiva nel portale di Azure.

## <a name="edit-your-logic-app-in-visual-studio"></a>Modificare l'app per la logica in Visual Studio

Per modificare l'app per la logica dopo la distribuzione, aprire l'app per la logica usando la finestra di progettazione dell'app per la logica in Visual Studio.

1. In Esplora soluzioni scegliere **Apri con progettazione app**per la logica dal menu di scelta rapida del file file azuredeploy. JSON.

   ![Aprire file azuredeploy. JSON in progettazione app per la logica](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Se non si dispone di questo comando in Visual Studio 2019, verificare di aver installato gli aggiornamenti più recenti per Visual Studio.

1. Quando viene visualizzata la casella **Proprietà app** per la logica, in **sottoscrizione**Selezionare la sottoscrizione di Azure, se non è già selezionata. In **gruppo di risorse**selezionare il gruppo di risorse e il percorso in cui è stata distribuita la soluzione, quindi fare clic su **OK**.

   ![Proprietà dell'app per la logica](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Dopo che viene aperta la finestra di progettazione di app per la logica, è possibile continuare ad aggiungere passaggi o modificare il flusso di lavoro e salvare gli aggiornamenti.

   ![App per la logica aperta nella finestra di progettazione di app per la logica](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Creare il progetto di funzioni di Azure

Per creare il progetto e la funzione di funzioni usando JavaScript, Python F#,, PowerShell, batch o bash, seguire la procedura descritta in [usare Azure Functions Core Tools](../azure-functions/functions-run-local.md). Per sviluppare la funzione di Azure usando C# all'interno della soluzione, usare C# una libreria di classi attenendosi alla procedura descritta in [pubblicare una libreria di classi .NET come app per le funzioni](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Distribuire le funzioni da Visual Studio

Il modello di distribuzione distribuisce tutte le funzioni di Azure presenti nella soluzione dal repository git specificato dalle variabili nel file file azuredeploy. JSON. Se si crea e si crea il progetto di funzioni nella soluzione, è possibile controllare il progetto nel controllo del codice sorgente git (ad esempio, GitHub o Azure DevOps) e quindi aggiornare la variabile `repo` in modo che il modello distribuisca la funzione di Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Gestire le app per la logica e visualizzare la cronologia di esecuzione

Per le app per la logica già distribuite in Azure, è comunque possibile modificare, gestire, visualizzare la cronologia di esecuzione per e disabilitare tali app da Visual Studio.

1. Dal menu **Visualizza** di Visual Studio aprire **Cloud Explorer**.

1. In **tutte le sottoscrizioni**selezionare la sottoscrizione di Azure associata alle app per la logica che si vuole gestire e quindi selezionare **applica**.

1. In **App per la logica** selezionare l'app per la logica. Dal menu di scelta rapida dell'app selezionare **Apri con Editor app per la logica**.

   > [!TIP]
   > Se non si dispone di questo comando in Visual Studio 2019, verificare di aver installato gli aggiornamenti più recenti per Visual Studio.

Ora è possibile scaricare l'app per la logica già pubblicata nel progetto Gruppo di risorse. Quindi, anche se è possibile che sia stata avviata un'app per la logica nella portale di Azure, è comunque possibile importare e gestire tale app in Visual Studio. Per altre informazioni, vedere [Gestire le app per la logica con Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Passaggi successivi

* [Gestire app per la logica con Visual Studio](manage-logic-apps-with-visual-studio.md)
