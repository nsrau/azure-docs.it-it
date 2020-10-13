---
title: Esportare i flussi da Power automatici ad app per la logica di Azure
description: Migrare i flussi da Power automatizzate a app per la logica di Azure esportando come modelli Azure Resource Manager
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: conceptual
ms.date: 06/03/2020
ms.openlocfilehash: 1c2f0a2c54be7adbc7b8babd596f18e08c67a024
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91269557"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Esportare flussi da Power Automate e distribuirli in App per la logica di Azure

Per estendere ed espandere le funzionalità del flusso, è possibile eseguire la migrazione di tale flusso da [Power automatici](https://flow.microsoft.com) ad app per la [logica di Azure](../logic-apps/logic-apps-overview.md). È possibile esportare il flusso come modello di Azure Resource Manager per un'app per la logica, distribuire il modello di app per la logica in un gruppo di risorse di Azure e quindi aprire l'app per la logica nella finestra di progettazione dell'app per la logica.

> [!NOTE]
> Non tutti i connettori di Power automatizzate sono disponibili nelle app per la logica di Azure. È possibile eseguire la migrazione solo dei flussi di Power automatizzati che dispongono di connettori equivalenti nelle app per la logica di Azure. Ad esempio, il trigger del pulsante, il connettore di approvazione e il connettore di notifica sono specifici per l'automazione dell'energia elettrica. Attualmente i flussi basati su OpenAPI in Power automatizzate non sono supportati per l'esportazione e la distribuzione come modelli di app per la logica.
>
> * Per individuare i connettori di Power automatizzate che non dispongono di equivalenti di app per la logica, vedere [connettori di Power automatizzate](/connectors/connector-reference/connector-reference-powerautomate-connectors).
>
> * Per individuare i connettori delle app per la logica che non dispongono di energia automatica equivalenti, vedere [connettori per app](/connectors/connector-reference/connector-reference-powerautomate-connectors)per la logica.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Il flusso che si vuole esportare dall'energia automatica

## <a name="export-your-flow"></a>Esportare il flusso

1. Accedere a [Power automatizzate](https://flow.microsoft.com)e selezionare **flussi personali**. Trovare e selezionare il flusso. Sulla barra degli strumenti, selezionare il pulsante con i puntini di sospensione (**...**) > **esportare**il modello di app per la  >  **logica (. Json)**.

   ![Esporta flusso da Power automatizza](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Salvare il file con estensione JSON del modello nel percorso desiderato.

Per altre informazioni, vedere [crescita fino ad app per la logica di Azure](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Distribuire il modello usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) con l'account Azure.

1. Nella casella di ricerca del home page di Azure immettere `custom template` . Nei risultati selezionare **Distribuisci un modello personalizzato**  >  **Crea**.

   ![Trovare e selezionare "Distribuzione modelli"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. In **distribuzione personalizzata**selezionare **Compila un modello personalizzato nell'editor**.

   ![Selezionare "Compila un modello personalizzato nell'editor"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Nella barra degli strumenti **modifica modello** selezionare **Carica file**.

   ![Selezionare "carica file"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Passare al percorso in cui è stato salvato il file modello JSON esportato da Power automatizzate. Selezionare il file di modello > **Apri**.

1. Quando l'editor Mostra il codice JSON, i parametri e le risorse nel modello, selezionare **Salva**.

   ![Salvare il modello](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. A questo punto, fornire altre informazioni sull'app per la logica.

   1. Selezionare o specificare i valori dei parametri di input per il modello.

      | Proprietà | Descrizione |
      |----------|-------------|
      | **Sottoscrizione** | Sottoscrizione di Azure da usare per la fatturazione |
      | **Gruppo di risorse** | Gruppo di risorse di Azure da usare per l'app per la logica. È possibile utilizzare un gruppo esistente o creare un nuovo gruppo. |
      | **Posizione** | Area di Azure da usare se si crea un nuovo gruppo di risorse |
      | **Nome dell'app per la logica** | Nome da usare per la risorsa dell'app per la logica |
      | **Percorso dell'app per la logica** | Area di Azure in cui si vuole creare la risorsa dell'app per la logica, se diversa da quella del gruppo di risorse di Azure |
      | <*Nome connessione*> | Uno o più nomi per tutte le connessioni create in precedenza che possono essere riutilizzate dall'app per la logica <p><p>**Nota**: se questa app per la logica è la prima, tutte le connessioni vengono create come nuove, quindi è possibile accettare i nomi predefiniti. In caso contrario, è possibile specificare i nomi per le connessioni create in precedenza, che possono essere usate in più app per la logica. |
      |||

      Ad esempio:

      ![Specificare i parametri di input per il modello](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   1. Al termine, esaminare i **termini e le condizioni** per la creazione delle risorse di Azure necessarie e la fatturazione della sottoscrizione di Azure di conseguenza.

   1. Quando si è pronti, selezionare Accetto **i termini e le condizioni indicati in precedenza**  >  **purchase**.

      Azure distribuisce il modello come app per la logica nel gruppo di risorse specificato.

1. Tutte le app per la logica di cui si esegue la migrazione da Power automatizzate vengono distribuite in uno stato disabilitato. Prima di abilitare l'app per la logica, autorizzare le nuove connessioni attenendosi alla procedura seguente:

   1. Nella portale di Azure aprire l'app per la logica creata. Nel menu dell'app per la logica selezionare **progettazione app**per la logica.

      Ogni connessione che richiede l'autorizzazione Visualizza un'icona di avviso:

      ![Icona avviso](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Per ogni passaggio che richiede una connessione autorizzata, espandere il passaggio e selezionare **Aggiungi nuovo**.

      ![Screenshot che mostra il pulsante "Aggiungi nuovo" selezionato nella finestra "connessioni" di Outlook.](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Accedere a ogni servizio o fornire le credenziali necessarie per autorizzare la connessione.

   1. Dopo aver aggiornato le connessioni, sulla barra degli strumenti della finestra di progettazione selezionare **Salva**.

1. Quando si è pronti per attivare l'app per la logica, scegliere **Panoramica**dal menu dell'app per la logica e quindi fare clic su **Abilita**.

   ![Abilitare l'app per la logica](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Per evitare l'esecuzione di flussi di lavoro duplicati, assicurarsi di disattivare o eliminare il flusso originale.

## <a name="deploy-template-by-using-visual-studio"></a>Distribuire un modello con Visual Studio

Se Visual Studio è stato configurato con i [prerequisiti](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) per la creazione di app per la logica, è possibile distribuire il modello esportato in app per la logica di Azure usando Visual Studio.

1. In Visual Studio passare a e aprire il file con estensione JSON per il modello di app per la logica esportato da Power automatizzate.

1. In Visual Studio creare un progetto di **gruppo di risorse di Azure** che usa il modello di app per la **logica** seguendo la procedura descritta in [Guida introduttiva: creare attività, processi e flussi di lavoro automatizzati con app per la logica di Azure-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

   Questo esempio Mostra come creare una soluzione di Visual Studio denominata "ImportedLogicApp".

   ![Creare un progetto Gruppo di risorse di Azure](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. Dopo aver creato la soluzione, in Esplora soluzioni aprire il **LogicApp.jsnel** file, se il file non è già aperto.

1. Copiare il contenuto del modello esportato e sovrascrivere il contenuto nella **LogicApp.jssu** file.

1. Prima di distribuire l'app per la logica, autorizzare le nuove connessioni attenendosi alla procedura seguente:

   1. Aprire il **LogicApp.js** dal menu di scelta rapida e quindi selezionare **Apri con progettazione app per la logica**.

      ![Aprire un modello con progettazione app per la logica](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Se richiesto, selezionare la sottoscrizione di Azure e il gruppo di risorse che si vuole usare per la distribuzione dell'app per la logica.

      ![Selezionare la sottoscrizione di Azure e il gruppo di risorse](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Quando l'app per la logica viene visualizzata nella finestra di progettazione, tutte le connessioni che richiedono l'autorizzazione visualizzano le icone di avviso:

      ![Connessioni con icone di avviso](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Per ogni passaggio che richiede una connessione autorizzata, espandere il passaggio e selezionare **Aggiungi nuovo**.

      ![Aggiungere una nuova connessione](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Accedere a ogni servizio o fornire le credenziali necessarie per autorizzare la connessione.

   1. Salvare la soluzione prima di distribuire l'app per la logica.

1. In Esplora soluzioni aprire il menu di scelta rapida del progetto e scegliere **Distribuisci**  >  **nuovo**. Se richiesto, accedere con il proprio account di Azure.

1. Quando richiesto, confermare la sottoscrizione di Azure, il gruppo di risorse di Azure e tutte le altre impostazioni che si vuole usare per la distribuzione, ad esempio un [file di parametri](../azure-resource-manager/templates/parameter-files.md) da usare per passare i valori dei parametri di modello, quindi selezionare **Distribuisci**.

   ![Confermare le impostazioni di distribuzione](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Se viene visualizzata la casella **modifica parametri** , specificare il nome della risorsa dell'app per la logica in Azure e selezionare **Salva**.  

   ![Modifica parametri di distribuzione](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   All'avvio della distribuzione, lo stato di distribuzione dell'app sarà visualizzato nella finestra **Output** di Visual Studio. Se lo stato non è visualizzato, aprire l'elenco **Mostra output di** e selezionare il proprio gruppo di risorse di Azure. Ad esempio:

   ![Output (finestra)](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Se per le connessioni nell'app per la logica è necessario un input, viene visualizzata una finestra di PowerShell in background e viene richiesta la password o le chiavi segrete necessarie. Dopo avere immesso queste informazioni, la distribuzione continuerà.

   ![Autenticare le connessioni](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Al termine della distribuzione, l'app per la logica viene pubblicata ma non è attivata nel portale di Azure.

1. Quando si è pronti per attivare l'app per la logica nella portale di Azure, trovare e aprire l'app per la logica nella finestra di progettazione dell'app per la logica. Scegliere **Panoramica**dal menu dell'app per la logica e quindi selezionare **Abilita**.

1. Per evitare l'esecuzione di flussi di lavoro duplicati, assicurarsi di disattivare o eliminare il flusso originale.

Per altre informazioni su questi passaggi di distribuzione, vedere [Guida introduttiva: creare attività, processi e flussi di lavoro automatizzati con app per la logica di Azure-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sui [connettori per le app per la logica di Azure](../connectors/apis-list.md)
* Altre informazioni sulle [app](../logic-apps/logic-apps-overview.md) per la logica di Azure
