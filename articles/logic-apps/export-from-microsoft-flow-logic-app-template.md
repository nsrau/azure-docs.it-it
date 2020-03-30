---
title: Esportare flussi da Power Automate ad Azure Logic AppsExport flows from Power Automate to Azure Logic Apps
description: Eseguire la migrazione dei flussi da Power Automate ad Azure Logic Apps eseguendo l'esportazione come modelli di Azure Resource ManagerMigrate flows from Power Automate to Azure Logic Apps by exporting as Azure Resource Manager templates
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 616f10b32d0a9c1a05d759a0e27550cd2808808b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428873"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Esportare flussi da Power Automate e distribuirli in App per la logica di Azure

Per estendere ed espandere le funzionalità del flusso, è possibile eseguire la migrazione di tale flusso da [Power Automate](https://flow.microsoft.com) ad App per la logica di [Azure.](../logic-apps/logic-apps-overview.md) È possibile esportare il flusso come modello di Azure Resource Manager per un'app per la logica, distribuire tale modello di app per la logica in un gruppo di risorse di Azure e quindi aprire tale app per la logica in Progettazione app per la logica.

> [!NOTE]
> Non tutti i connettori di Power Automate sono disponibili in App per la logica di Azure.Not all Power Automate connectors are available in Azure Logic Apps. È possibile importare flussi con [connettori equivalenti](../connectors/apis-list.md) in App per la logica di Azure.You can import flows that have equivalent connectors in Azure Logic Apps. Ad esempio, il trigger pulsante, il connettore approvazione e il connettore di notifica sono specifici di Power Automate.For example, the Button trigger, the Approval connector, and Notification connector are specific to Power Automate.
>
> I flussi basati su OpenAPI esportati da Power Automate non sono attualmente supportati per la distribuzione come modelli di app per la logica. 

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Flusso che si desidera esportare da Power Automate

## <a name="export-a-flow"></a>Esportare un flusso

1. Accedere a [Power Automate](https://flow.microsoft.com)e selezionare **Flussi**personali . Trovare e selezionare il flusso. Sulla barra degli strumenti selezionare il pulsante con i puntini di sospensione (**...**). Selezionare **Esporta** > modello di app per la**logica (.json)**.

   ![Esportare il flusso](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Salvare il modello nella posizione desiderata.

Per altre informazioni, vedere [Fino a Business Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Distribuire il modello tramite il portale di AzureDeploy template by using the Azure portal

1. Accedere al portale di Azure con l'account Azure.Sign in the [Azure portal](https://portal.azure.com) with your Azure account.

1. Nel menu principale di Azure scegliere **Crea una risorsa**. Nella casella di ricerca immettere "template deployment". Selezionare **Distribuzione modelli (distribuzione tramite modelli personalizzati)** e quindi selezionare **Crea**.

   ![Selezionare "Distribuzione del modello"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. In **Distribuzione personalizzata**selezionare **Crea modello personalizzato nell'editor.**

   ![Seleziona "Crea il tuo modello nell'editor"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Dalla barra degli strumenti **Modifica modello,** selezionare **Carica file**. Individuare e selezionare il modello JSON esportato da Power Automate e selezionare **Apri**.

   ![Selezionare "Carica file"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Dopo aver visualizzato il codice JSON, i parametri e le risorse nel modello, selezionare **Salva**.
  
   ![Salvare il modello](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Specificare ora questi parametri di input per il modello:

   * Sottoscrizione di Azure da usare per la fatturazioneAzure subscription to use for billing
   * Gruppo di risorse di Azure
   * Posizione per il gruppo di risorse di AzureLocation for the Azure resource group
   * Nome per la risorsa dell'app per la logicaName for the logic app resource
   * Posizione per la risorsa dell'app per la logica, se diversa dal gruppo di risorse di AzureLocation for the logic app app resource, if different from the Azure resource group
   * Nome di tutte le connessioni create in precedenza che l'app per la logica può riutilizzare

      Se si sta creando la prima app per la logica, tutte le connessioni vengono create come nuove, in modo da poter accettare i nomi predefiniti. In caso contrario, è possibile specificare i nomi per le connessioni create in precedenza, che è possibile usare in più app per la logica.

   Dopo aver fornito queste informazioni per il modello, esaminato e accettato i termini e le condizioni di Azure Marketplace per la creazione delle risorse di Azure necessarie e la fatturazione della sottoscrizione di Azure di conseguenza, quindi selezionare **Acquista**.
  
   ![Specificare i parametri di input per il modello](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Azure distribuisce il modello come app per la logica nel gruppo di risorse specificato. Tutte le app per la logica di cui si esegue la migrazione da Power Automate vengono distribuite in uno stato disabilitato.

1. Prima di attivare l'app per la logica, autorizzare le nuove connessioni eseguendo la procedura seguente:

   1. Aprire l'app per la logica creata. Nel menu dell'app per la logica selezionare Finestra di **progettazione app per la logica**.

      Ogni connessione che richiede l'autorizzazione mostra un'icona di avviso:Each connection that requires authorization shows a warning icon:

      ![Icona avviso](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Per ogni passaggio che richiede una connessione autorizzata, espandere il passaggio e selezionare **Aggiungi nuovo**.

      ![Aggiungere una nuova connessione](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Accedere a ogni servizio o fornire le credenziali necessarie per autorizzare la connessione.

1. Salvare l'app per la logica. Quando si è pronti per attivare l'app per la logica, nel menu dell'app per la logica selezionare **Panoramica**e quindi **Selezionare Abilita**.

   ![Abilitare l'app per la logicaEnable logic app](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Per evitare l'esecuzione di flussi di lavoro duplicati, assicurarsi di disattivare o eliminare il flusso originale.

## <a name="deploy-template-by-using-visual-studio"></a>Distribuire il modello tramite Visual StudioDeploy template by using Visual Studio

Se è stato configurato Visual Studio con i [prerequisiti](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) per la creazione di app per la logica, è possibile distribuire il modello esportato da Visual Studio ad Azure Logic Apps.

1. In Visual Studio aprire il file modello esportato da Power Automate.

1. In Visual Studio creare un progetto Gruppo di risorse di Azure e selezionare il modello App per la logica seguendo i passaggi descritti in Guida introduttiva: Creare attività, processi e flussi di lavoro automatizzati con App per la logica di Azure - Visual Studio , ad esempio:In Visual Studio, create an Azure Resource Group project and select the **Logic App** template by following the steps in [Quickstart: Create automated tasks, processes, and workflows with Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md), for example:

   ![Creare un progetto Gruppo di risorse di Azure](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. In Esplora soluzioni aprire il file **LogicApp.json,** se il file non è già aperto.

1. Copiare il contenuto dal modello esportato e sovrascrivere il contenuto nel file **LogicApp.json.**

1. Prima di distribuire l'app per la logica, autorizzare le nuove connessioni eseguendo la procedura seguente:Before you deploy your logic app, authorize any new connections by following these steps:

   1. Aprire il menu di scelta rapida **LogicApp.json** , quindi selezionare **Apri con Progettazione app per la logica**.

      ![Modello aperto con Progettazione app per la logicaOpen template with Logic App Designer](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Se richiesto, selezionare la sottoscrizione di Azure e il gruppo di risorse che si vuole usare per la distribuzione dell'app per la logica.

      ![Selezionare la sottoscrizione di Azure e il gruppo di risorseSelect Azure subscription and resource group](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Dopo che l'app per la logica viene visualizzata nella finestra di progettazione, tutte le connessioni che richiedono l'autorizzazione mostrano icone di avviso:After your logic app appears in the designer, any connections that require authorization show warning icons:

      ![Connessioni con icone di avviso](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Per ogni passaggio che richiede una connessione autorizzata, espandere il passaggio e selezionare **Aggiungi nuovo**.

      ![Aggiungere una nuova connessione](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Accedere a ogni servizio o fornire le credenziali necessarie per autorizzare la connessione.

   1. Salvare la soluzione prima di distribuire l'app per la logica.

1. In Esplora soluzioni aprire il menu di scelta rapida del progetto e selezionare **Distribuisci** > **nuovo**. Se richiesto, accedere con il proprio account di Azure.

1. Quando richiesto, confermare la sottoscrizione di Azure, il gruppo di risorse di Azure e tutte le altre impostazioni che si vuole usare per la distribuzione, ad esempio un file di [parametri](../azure-resource-manager/templates/parameter-files.md) da usare per il passaggio dei valori dei parametri di modello, quindi selezionare **Distribuisci**.

   ![Verificare le impostazioni di distribuzione](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Se viene visualizzata la casella **Modifica parametri,** specificare il nome per la risorsa dell'app per la logica in Azure e selezionare **Salva**.  

   ![Modificare i parametri di distribuzione](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   All'avvio della distribuzione, lo stato di distribuzione dell'app sarà visualizzato nella finestra **Output** di Visual Studio. Se lo stato non è visualizzato, aprire l'elenco **Mostra output di** e selezionare il proprio gruppo di risorse di Azure. Ad esempio:

   ![Finestra Output](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Se nell'app per la logica è necessario immettere l'input dell'utente, in background viene aperta una finestra di PowerShell in background e vengono richieste le password o le chiavi segrete necessarie. Dopo avere immesso queste informazioni, la distribuzione continuerà.

   ![Autenticare le connessioni](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Al termine della distribuzione, l'app per la logica viene pubblicata ma non attivata nel portale di Azure.After deployment finishes, your logic app is published but't activated in the Azure portal.

1. Quando si è pronti per attivare l'app per la logica nel portale di Azure, trovare e aprire l'app per la logica in Progettazione app per la logica. Nel menu dell'app per la logica selezionare **Panoramica**e quindi **Abilita**.

1. Per evitare l'esecuzione di flussi di lavoro duplicati, assicurarsi di disattivare o eliminare il flusso originale.

Per altre informazioni su questi passaggi di distribuzione, vedere Guida introduttiva: Creare attività, processi e flussi di lavoro automatizzati con app per la logica di Azure - Visual StudioFor more information about these deployment steps, see [Quickstart: Create automated tasks, processes, and workflows with Azure Logic Apps - Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sui connettori per le app per la logica di AzureLearn more about [Connectors for Azure Logic Apps](../connectors/apis-list.md)
* Altre informazioni su App per la logica di [AzureLearn](../logic-apps/logic-apps-overview.md) more about Azure Logic Apps
