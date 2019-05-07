---
title: Crea App senza server con le App per la logica di Azure e funzioni di Azure in Visual Studio
description: Compilare, distribuire e gestire la prima app senza server con App per la logica di Azure e Funzioni di Azure in Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 04/25/2019
ms.openlocfilehash: 265ed220b023a9d81fc5af48920152d95d9cac08
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65136697"
---
# <a name="build-your-first-serverless-app-with-azure-logic-apps-and-azure-functions---visual-studio"></a>Compilare la prima app senza server con App per la logica di Azure e Funzioni di Azure - Visual Studio

È possibile sviluppare e distribuire rapidamente app cloud usando gli strumenti e le funzionalità senza server disponibili in Azure, ad esempio [App per la logica di Azure](../logic-apps/logic-apps-overview.md) e [Funzioni di Azure](../azure-functions/functions-overview.md). Questo articolo illustra come iniziare a compilare un'app senza server che usa un'app per la logica che chiama una funzione di Azure in Visual Studio. Per informazioni sulle soluzioni senza server in Azure, vedere [Azure Serverless with Functions and Logic Apps](../logic-apps/logic-apps-serverless-overview.md) (Architettura senza server di Azure con Funzioni e App per la logica).

## <a name="prerequisites"></a>Prerequisiti

Per compilare un'app senza server in Visual Studio, sono necessari questi elementi:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Scaricare e installare questi strumenti, se non sono già disponibili:

  * [Visual Studio 2019, 2017 o 2015 - Community Edition o superiore](https://aka.ms/download-visual-studio). 
  Questa guida introduttiva usa Visual Studio Community 2017, disponibile gratuitamente.

    > [!IMPORTANT]
    > Quando si installa Visual Studio 2019 o 2017, assicurarsi di selezionare il carico di lavoro **Sviluppo di Azure**.

  * [Microsoft Azure SDK per .NET (2.9.1 o versione successiva)](https://azure.microsoft.com/downloads/). 
  Altre informazioni su [Azure SDK per .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)

  * Strumenti App per la logica di Azure per la versione di Visual Studio desiderata:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    È anche possibile scaricare e installare Strumenti App per la logica di Azure direttamente da Visual Studio Marketplace o [installare questa estensione da Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Assicurarsi di riavviare Visual Studio al termine dell'installazione.

  * [Strumenti di base di Funzioni di Azure](https://www.npmjs.com/package/azure-functions-core-tools) per eseguire il debug di Funzioni in locale

* Accesso al Web mentre si usa la finestra integrata Progettazione app per la logica

  La finestra di progettazione richiede una connessione Internet per creare le risorse in Azure e leggere le proprietà e i dati dai connettori nell'app per la logica. 
  Ad esempio, se si usa il connettore per Dynamics CRM Online, la finestra di progettazione verifica la disponibilità di proprietà predefinite e personalizzate nell'istanza di CRM.

## <a name="create-resource-group-project"></a>Creare un progetto Gruppo di risorse

Per iniziare, creare un [progetto Gruppo di risorse di Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) per l'app senza server. In Azure le risorse vengono create all'interno di un gruppo di risorse, che è una raccolta logica usata per organizzare, gestire e distribuire risorse per un'intera app come un singolo asset. Per un'app senza server in Azure, il gruppo di risorse include risorse sia per App per la logica di Azure sia per Funzioni di Azure. È possibile accedere ad altre informazioni sui [gruppi di risorse e le risorse di Azure](../azure-resource-manager/resource-group-overview.md).

1. Avviare Visual Studio e accedere con il proprio account di Azure.

1. Scegliere **Nuovo** > **Progetto** dal menu **File**

   ![Creare un nuovo progetto in Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. In **Installato** selezionare **Visual C#** o **Visual Basic**. Selezionare **Cloud** > **Gruppo di risorse di Azure**.

   > [!NOTE]
   > Se la categoria **Cloud** o il progetto **Gruppo di risorse di Azure** non esistono, verificare che sia installato Azure SDK per Visual Studio.

   Se si usa Visual Studio 2019, procedere come segue:

   1. Nella finestra di dialogo **Crea un nuovo progetto** selezionare il modello di progetto **Gruppo di risorse di Azure** per Visual C# o per Visual Basic, quindi scegliere **Avanti**.

   1. Specificare il nome del gruppo di risorse di Azure da usare e altre informazioni sul progetto. Al termine dell'operazione, scegliere **Crea**.

1. Assegnare un nome e un percorso al progetto e quindi fare clic su **OK**.

   Visual Studio chiederà di selezionare un modello dall'elenco dei modelli. 
   Questo esempio Usa un modello di avvio rapido di Azure in modo che è possibile compilare un'app senza server che include un'app per la logica e una chiamata a una funzione di Azure.

   > [!TIP]
   > Negli scenari in cui non si vuole pre-distribuire la soluzione in un gruppo di risorse di Azure, è possibile usare lo spazio vuoto **App per la logica** modello che crea solo un'app per la logica vuota.

1. Dal **Mostra modelli da questa posizione** elenco, selezionare **avvio rapido di Azure (github.com/Azure/azure-quickstart-templates)**.

1. Nella casella di ricerca immettere "app per la logica" come filtro. Dai risultati, selezionare questo modello: **101-logic-app-and-function-app**

   ![Selezionare il modello di avvio rapido di Azure](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio crea e visualizza una soluzione per il progetto Gruppo di risorse. 
   È stato selezionato il modello di avvio rapido di Azure crea un modello di distribuzione denominato `azuredeploy.json` all'interno del progetto gruppo di risorse. Questo modello di distribuzione include la definizione per un'app per la logica semplice che si attiva con una richiesta HTTP, chiama una funzione di Azure e restituisce il risultato come risposta HTTP.

   ![Nuova soluzione senza server](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. A questo punto è necessario distribuire la soluzione in Azure per poter aprire il modello di distribuzione ed esaminare le risorse per l'app senza server.

## <a name="deploy-your-solution"></a>Distribuire la soluzione

Prima di poter visualizzare l'app per la logica nella finestra di progettazione delle app per la logica in Visual Studio, è necessario che un gruppo di risorse di Azure sia già stato distribuito in Azure. Il progettista può quindi creare le connessioni ai servizi e alle risorse disponibili nell'app per la logica. Per questa attività è necessario distribuire la soluzione da Visual Studio al portale di Azure.

1. In Esplora soluzioni, dal menu di scelta rapida del progetto di risorse, selezionare **Deploy** > **New**.

   ![Creare una nuova distribuzione per il gruppo di risorse](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Se non è già selezionata, selezionare la sottoscrizione di Azure e il gruppo di risorse in cui si vuole eseguire la distribuzione. Scegliere **Distribuisci**.

   ![Impostazioni di distribuzione](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Se viene visualizzata la finestra **Modifica parametri**, specificare il nome della risorsa che l'app per la logica e l'app per le funzioni di Azure dovranno usare durante la distribuzione e quindi salvare le impostazioni. Assicurarsi di usare un nome univoco globale per l'app per le funzioni.

   ![Assegnare un nome all'app per la logica e all'app per le funzioni](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Quando Visual Studio avvia la distribuzione nel gruppo di risorse specificato, lo stato di distribuzione della soluzione sarà visualizzato nella finestra **Output** di Visual Studio. 
   Al termine della distribuzione, l'app per la logica sarà attiva nel portale di Azure.

## <a name="edit-logic-app-in-visual-studio"></a>Modificare l'app per la logica in Visual Studio

Ora che la soluzione viene distribuita al gruppo di risorse, aprire l'app per la logica con progettazione App per la logica in modo che è possibile modificare e modificare l'app per la logica.

1. In Esplora soluzioni, dal `azuredeploy.json` menu di scelta rapida del file, seleziona **Apri con progettazione App per logica**.

   ![Aprire "azuredeploy.json" in Progettazione app per la logica](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

1. Quando viene visualizzata la finestra delle **proprietà dell'app per la logica**, se non è già selezionata, selezionare la propria sottoscrizione di Azure in **Sottoscrizione**. In **Gruppo di risorse** selezionare il gruppo di risorse e il percorso in cui è stata distribuita la soluzione, quindi fare clic su **OK**.

   ![Proprietà dell'app per la logica](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Dopo che viene aperta la finestra di progettazione di app per la logica, è possibile continuare ad aggiungere passaggi o modificare il flusso di lavoro e salvare gli aggiornamenti.

   ![App per la logica aperta nella finestra di progettazione di app per la logica](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-azure-functions-project"></a>Creare un progetto di Funzioni di Azure

Per creare un progetto o una funzione di Funzioni con JavaScript, Python, F#, PowerShell, Batch o Bash, seguire la procedura descritta nell'articolo [Usare Strumenti di base di Funzioni di Azure](../azure-functions/functions-run-local.md). Per sviluppare la funzione di Azure con C# all'interno della soluzione, è possibile usare una libreria di classi C# seguendo la procedura descritta nell'articolo [Publish a .NET class library as a Function App](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/) (Pubblicare una libreria di classi .NET come un'app per le funzioni).

## <a name="deploy-functions-from-visual-studio"></a>Distribuire le funzioni da Visual Studio

Il modello di distribuzione consente di distribuire qualsiasi funzione di Azure contenuta nella soluzione dal repository Git specificato dalle variabili nel file `azuredeploy.json`. Se si crea un progetto di Funzioni all'interno della soluzione, è possibile archiviarlo nel controllo del codice sorgente Git, ad esempio GitHub o Azure DevOps, e aggiornare la variabile `repo` in modo che il modello distribuisca la funzione di Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Gestire le app per la logica e visualizzare la cronologia di esecuzione

Per le app per la logica già distribuite in Azure è possibile ancora modificare, gestire e visualizzare la cronologia di esecuzione e disabilitare le app da Visual Studio.

1. Dal menu **Visualizza** di Visual Studio aprire **Cloud Explorer**.

1. In **Tutte le sottoscrizioni** selezionare la sottoscrizione di Azure associata alle app per la logica che si vuole gestire e quindi scegliere **Applica**.

1. In **App per la logica** selezionare l'app per la logica. Dal menu di scelta rapida dell'app selezionare **Apri con Editor app per la logica**.

Ora è possibile scaricare l'app per la logica già pubblicata nel progetto Gruppo di risorse. In questo modo, anche se la creazione dell'app per la logica è già stata iniziata nel portale di Azure, è comunque possibile importarla e gestirla in Visual Studio. Per altre informazioni, vedere [Gestire le app per la logica con Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Passaggi successivi

* [Gestire app per la logica con Visual Studio](manage-logic-apps-with-visual-studio.md)
