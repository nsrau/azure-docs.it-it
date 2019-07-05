---
title: Crea App senza server con le App per la logica di Azure e funzioni di Azure in Visual Studio
description: Creare, distribuire e gestire la prima app senza server con le App per la logica di Azure e funzioni di Azure in Visual Studio
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.custom: vs-azure
ms.topic: article
ms.date: 06/20/2019
ms.openlocfilehash: b7af4fc731d01bb666165655baa2f1d6c64d4071
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444861"
---
# <a name="build-your-first-serverless-app-by-using-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Compilare la prima app senza server con le App per la logica di Azure e funzioni di Azure in Visual Studio

È possibile sviluppa e Distribuisci App cloud usando le funzionalità e strumenti senza server in Azure, ad esempio rapidamente [Azure Logic Apps](../logic-apps/logic-apps-overview.md) e [funzioni di Azure](../azure-functions/functions-overview.md). Questo articolo illustra come iniziare a compilare un'app senza server che usa un'app per la logica che chiama una funzione di Azure in Visual Studio. Per informazioni sulle soluzioni senza server in Azure, vedere [Azure Serverless with Functions and Logic Apps](../logic-apps/logic-apps-serverless-overview.md) (Architettura senza server di Azure con Funzioni e App per la logica).

## <a name="prerequisites"></a>Prerequisiti

Per compilare un'app senza server in Visual Studio, è necessario:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Gli strumenti seguenti. Se non sono disponibili già, scaricarli e installarli.

  * [Visual Studio 2019, 2017 o 2015 (Community o altra versione)](https://aka.ms/download-visual-studio). 
  Questa guida introduttiva usa Visual Studio Community 2017, disponibile gratuitamente.

    > [!IMPORTANT]
    > Quando si installa Visual Studio 2019 o 2017, assicurarsi di selezionare il carico di lavoro **Sviluppo di Azure**.

  * [Microsoft Azure SDK per .NET (versione 2.9.1 o versione successiva)](https://azure.microsoft.com/downloads/). 
  Altre informazioni su [Azure SDK per .NET](https://docs.microsoft.com/dotnet/azure/dotnet-tools?view=azure-dotnet).

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * Strumenti App per la logica di Azure per la versione di Visual Studio desiderata:

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)

    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)

    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)
  
    È anche possibile scaricare e installare Strumenti App per la logica di Azure direttamente da Visual Studio Marketplace o [installare questa estensione da Visual Studio](https://docs.microsoft.com/visualstudio/ide/finding-and-using-visual-studio-extensions). 
    Assicurarsi di riavviare Visual Studio al termine dell'installazione.

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) per funzioni di debug in locale.

* Accesso al web mentre si usa la finestra di progettazione di App per la logica incorporata.

  La finestra di progettazione richiede una connessione Internet per creare le risorse in Azure e leggere le proprietà e i dati dai connettori nell'app per la logica. 
  Ad esempio, se si usa il connettore per Dynamics CRM Online, la finestra di progettazione verifica la disponibilità di proprietà predefinite e personalizzate nell'istanza di CRM.

## <a name="create-a-resource-group-project"></a>Creare un progetto gruppo di risorse

Per iniziare, creare un [progetto Gruppo di risorse di Azure](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) per l'app senza server. In Azure, si creano risorse all'interno di un *gruppo di risorse*, che è una raccolta logica usi per l'organizzazione, la gestione e distribuzione delle risorse per un'intera app come un singolo asset. Per un'app senza server in Azure, il gruppo di risorse include risorse sia per App per la logica di Azure sia per Funzioni di Azure. È possibile accedere ad altre informazioni sui [gruppi di risorse e le risorse di Azure](../azure-resource-manager/resource-group-overview.md).

1. Avviare Visual Studio e accedere con l'account di Azure.

1. Scegliere **Nuovo** > **Progetto** dal menu **File**

   ![Creare un nuovo progetto in Visual Studio](./media/logic-apps-serverless-get-started-vs/create-new-project-visual-studio.png)

1. In **Installato** selezionare **Visual C#** o **Visual Basic**. Quindi, selezionare **Cloud** > **gruppo di risorse di Azure**.

   > [!NOTE]
   > Se il **Cloud** categoria oppure **gruppo di risorse di Azure** progetto non esiste, verificare che è installato Azure SDK per Visual Studio.

   Se si usa Visual Studio 2019, procedere come segue:

   1. Nel **creare un nuovo progetto** , quindi selezionare la **gruppo di risorse di Azure** modello di progetto per l'oggetto visivo C# o Visual Basic e quindi selezionare **successivo**.

   1. Specificare il nome e altre informazioni sul progetto che si desidera utilizzare per il gruppo di risorse di Azure. Al termine, selezionare **Crea**.

1. Assegnare al progetto un nome e un percorso e quindi selezionare **OK**.

   Visual Studio chiederà di selezionare un modello dall'elenco dei modelli. 
   Questo esempio Usa un modello di avvio rapido di Azure in modo che sia possibile compilare un'app senza server che include un'app per la logica e una chiamata a una funzione di Azure.

   > [!TIP]
   > Negli scenari in cui non si vuole pre-distribuire la soluzione in un gruppo di risorse di Azure, è possibile usare lo spazio vuoto **App per la logica** modello che crea solo un'app per la logica vuota.

1. Dal **Mostra modelli da questa posizione** elenco, selezionare **avvio rapido di Azure (github.com/Azure/azure-quickstart-templates)** .

1. Nella casella di ricerca immettere "app per la logica" come filtro. Dai risultati, selezionare la **101-logic-app-and-function-app** modello.

   ![Selezionare il modello di avvio rapido di Azure](./media/logic-apps-serverless-get-started-vs/select-template.png)

   Visual Studio crea e visualizza una soluzione per il progetto Gruppo di risorse. 
   Il modello di avvio rapido di Azure che è stato selezionato consente di creare un modello di distribuzione denominato azuredeploy. JSON all'interno del progetto gruppo di risorse. Questo modello di distribuzione include la definizione di una semplice app per la logica che viene attivata da una richiesta HTTP, chiama una funzione di Azure e restituisce il risultato come una risposta HTTP.

   ![Nuova soluzione senza server](./media/logic-apps-serverless-get-started-vs/create-serverless-solution.png)

1. Successivamente, distribuire la soluzione in Azure. È necessario farlo prima di aprire il modello di distribuzione ed esaminare le risorse per l'app senza server.

## <a name="deploy-your-solution"></a>Distribuire la soluzione

Prima di poter aprire l'app per la logica in Progettazione App per la logica in Visual Studio, è necessario disporre di un gruppo di risorse di Azure che è già stato distribuito in Azure. Il progettista può quindi creare le connessioni ai servizi e alle risorse disponibili nell'app per la logica. Per questa attività, seguire questi passaggi per distribuire la soluzione da Visual Studio per il portale di Azure:

1. In Esplora soluzioni, dal menu di scelta rapida del progetto di risorse, selezionare **Deploy** > **New**.

   ![Creare una nuova distribuzione per il gruppo di risorse](./media/logic-apps-serverless-get-started-vs/deploy.png)

1. Se non si sono già selezionate, selezionare la sottoscrizione di Azure e il gruppo di risorse a cui si desidera distribuire. Quindi, selezionare **Distribuisci**.

   ![Impostazioni di distribuzione](./media/logic-apps-serverless-get-started-vs/deploy-to-resource-group.png)

1. Se il **modifica parametri** verrà visualizzata la finestra, specificare i nomi di risorsa da usare per l'app per la logica e app per le funzioni di Azure in fase di distribuzione e quindi salvare le impostazioni. Assicurarsi di usare un nome univoco globale per l'app per le funzioni.

   ![Assegnare un nome all'app per la logica e all'app per le funzioni](./media/logic-apps-serverless-get-started-vs/logic-function-app-name-parameters.png)

   Quando Visual Studio avvia la distribuzione nel gruppo di risorse specificato, lo stato di distribuzione della soluzione sarà visualizzato nella finestra **Output** di Visual Studio. 
   Al termine della distribuzione, l'app per la logica sarà attiva nel portale di Azure.

## <a name="edit-your-logic-app-in-visual-studio"></a>Modificare l'app per la logica in Visual Studio

Per modificare l'app per la logica dopo la distribuzione, aprire l'app per la logica con progettazione App per la logica in Visual Studio.

1. In Esplora soluzioni, dal menu di scelta rapida del file azuredeploy. JSON, selezionare **Apri con progettazione App per logica**.

   ![Aprire azuredeploy. JSON in Progettazione App per la logica](./media/logic-apps-serverless-get-started-vs/open-logic-app-designer.png)

   > [!TIP]
   > Se non si dispone di questo comando in Visual Studio 2019, verificare di avere gli ultimi aggiornamenti per Visual Studio.

1. Dopo il **delle proprietà dell'App per la logica** verrà visualizzata la finestra, sotto **sottoscrizione**, selezionare la sottoscrizione di Azure se non è già selezionata. Sotto **gruppo di risorse**, selezionare il gruppo di risorse e il percorso in cui è distribuita la soluzione e quindi selezionare **OK**.

   ![Proprietà dell'app per la logica](./media/logic-apps-serverless-get-started-vs/logic-app-properties.png)

   Dopo che viene aperta la finestra di progettazione di app per la logica, è possibile continuare ad aggiungere passaggi o modificare il flusso di lavoro e salvare gli aggiornamenti.

   ![App per la logica aperta nella finestra di progettazione di app per la logica](./media/logic-apps-serverless-get-started-vs/opened-logic-app.png)

## <a name="create-your-azure-functions-project"></a>Creare il progetto di funzioni di Azure

Per creare il progetto di funzioni e una funzione con JavaScript, Python, F#, PowerShell, Batch o Bash, seguire i passaggi [lavorare con Azure Functions Core Tools](../azure-functions/functions-run-local.md). Per sviluppare le funzioni di Azure tramite C# all'interno della soluzione, usare un C# libreria di classi seguendo i passaggi descritti in [pubblicare una libreria di classi .NET come un'App per le funzioni](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/16/publishing-a-net-class-library-as-a-function-app/).

## <a name="deploy-functions-from-visual-studio"></a>Distribuire le funzioni da Visual Studio

Modello di distribuzione consente di distribuire eventuali funzioni di Azure che si dispone della soluzione dal repository Git specificato da variabili nel file azuredeploy. JSON. Se si creano e si crea il progetto di funzioni nella soluzione, è possibile archiviare tale progetto nel controllo del codice sorgente Git (ad esempio, GitHub o DevOps di Azure) e quindi aggiornare il `repo` variabili in modo che il modello distribuisce la funzione di Azure.

## <a name="manage-logic-apps-and-view-run-history"></a>Gestire le app per la logica e visualizzare la cronologia di esecuzione

Per le App per la logica già distribuite in Azure, si possono ancora modificare, gestire, visualizzare la cronologia di esecuzione per e disabilitare tali app da Visual Studio.

1. Dal menu **Visualizza** di Visual Studio aprire **Cloud Explorer**.

1. Sotto **tutte le sottoscrizioni**, selezionare la sottoscrizione di Azure associata alle App per la logica che si desidera gestire e quindi selezionare **applica**.

1. In **App per la logica** selezionare l'app per la logica. Dal menu di scelta rapida dell'app selezionare **Apri con Editor app per la logica**.

   > [!TIP]
   > Se non si dispone di questo comando in Visual Studio 2019, verificare di avere gli ultimi aggiornamenti per Visual Studio.

Ora è possibile scaricare l'app per la logica già pubblicata nel progetto Gruppo di risorse. Pertanto, anche se potrebbe aver avviato un'app per la logica nel portale di Azure, è possibile comunque importare e gestire app in Visual Studio. Per altre informazioni, vedere [Gestire le app per la logica con Visual Studio](../logic-apps/manage-logic-apps-with-visual-studio.md).

## <a name="next-steps"></a>Passaggi successivi

* [Gestire app per la logica con Visual Studio](manage-logic-apps-with-visual-studio.md)
