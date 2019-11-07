---
title: Esportare i flussi da Power automatici ad app per la logica di Azure
description: Migrare i flussi da Power automatizzate a app per la logica di Azure esportando come modelli Azure Resource Manager
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: e0dda5c2097243143d18851c47e7006c81769c87
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73583262"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Esportare flussi da Power automatizzare e distribuire in app per la logica di Azure

Per estendere ed espandere le funzionalità del flusso, è possibile eseguire la migrazione di tale flusso da [Power automatici](https://flow.microsoft.com) ad app per la [logica di Azure](../logic-apps/logic-apps-overview.md). È possibile esportare il flusso come modello di Azure Resource Manager per un'app per la logica, distribuire il modello di app per la logica in un gruppo di risorse di Azure e quindi aprire l'app per la logica nella finestra di progettazione dell'app per la logica.

> [!NOTE]
> Non tutti i connettori di Power automatizzate sono disponibili nelle app per la logica di Azure. È possibile importare flussi con [connettori equivalenti](../connectors/apis-list.md) in app per la logica di Azure. Ad esempio, il trigger del pulsante, il connettore di approvazione e il connettore di notifica sono specifici per l'automazione dell'energia elettrica.
>
> I flussi basati su OpenAPI esportati da Power automatici non sono attualmente supportati per la distribuzione come modelli di app per la logica. 

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, [iscriversi per creare un account Azure gratuito](https://azure.microsoft.com/free/).

* Il flusso che si vuole esportare dall'energia automatica

## <a name="export-a-flow"></a>Esportare un flusso

1. Accedere a [Power automatizzate](https://flow.microsoft.com)e selezionare **flussi personali**. Trovare e selezionare il flusso. Sulla barra degli strumenti, selezionare il pulsante con i puntini di sospensione ( **...** ). Selezionare **esporta** > **modello di app per la logica (. Json)** .

   ![Esporta flusso](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Salvare il modello nel percorso desiderato.

Per altre informazioni, vedere [crescita fino ad app per la logica di Azure](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Distribuire il modello usando il portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) con l'account Azure.

1. Nel menu principale di Azure scegliere **Crea una risorsa**. Nella casella di ricerca immettere "distribuzione modello". Selezionare **distribuzione modelli (Distribuisci usando i modelli personalizzati)** , quindi selezionare **Crea**.

   ![Selezionare "Distribuzione modelli"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. In **distribuzione personalizzata**selezionare **Compila un modello personalizzato nell'editor**.

   ![Selezionare "Compila un modello personalizzato nell'editor"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Dalla barra degli strumenti **modifica modello** selezionare **Carica file**. Trovare e selezionare il modello JSON esportato da Power automatizzate e selezionare **Apri**.

   ![Selezionare "carica file"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Quando l'editor Mostra il codice JSON, i parametri e le risorse nel modello, selezionare **Salva**.
  
   ![Salvare il modello](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Specificare ora i parametri di input per il modello:

   * Sottoscrizione di Azure da usare per la fatturazione
   * Gruppo di risorse di Azure
   * Percorso del gruppo di risorse di Azure
   * Nome della risorsa dell'app per la logica
   * Percorso della risorsa dell'app per la logica, se diverso dal gruppo di risorse di Azure
   * Nome di tutte le connessioni create in precedenza che possono essere riutilizzate dall'app per la logica

      Se si sta creando la prima app per la logica, tutte le connessioni vengono create come nuove, quindi è possibile accettare i nomi predefiniti. In caso contrario, è possibile specificare i nomi per le connessioni create in precedenza, che possono essere usate in più app per la logica.

   Dopo aver fornito queste informazioni per il modello, rivedere e accettare i termini e le condizioni di Azure Marketplace per la creazione delle risorse di Azure necessarie e la fatturazione della sottoscrizione di Azure di conseguenza, quindi selezionare **Acquista**.
  
   ![Specificare i parametri di input per il modello](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Azure distribuisce il modello come app per la logica nel gruppo di risorse specificato. Tutte le app per la logica di cui si esegue la migrazione da Power automatizzate vengono distribuite in uno stato disabilitato.

1. Prima di attivare l'app per la logica, autorizzare le nuove connessioni attenendosi alla procedura seguente:

   1. Aprire l'app per la logica creata. Nel menu dell'app per la logica selezionare **progettazione app**per la logica.

      Ogni connessione che richiede l'autorizzazione Visualizza un'icona di avviso:

      ![Icona di avviso](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Per ogni passaggio che richiede una connessione autorizzata, espandere il passaggio e selezionare **Aggiungi nuovo**.

      ![Aggiungi nuova connessione](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Accedere a ogni servizio o fornire le credenziali necessarie per autorizzare la connessione.

1. Salvare l'app per la logica. Quando si è pronti per attivare l'app per la logica, scegliere **Panoramica**dal menu dell'app per la logica e quindi fare clic su **Abilita**.

   ![Abilitare l'app per la logica](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Per evitare l'esecuzione di flussi di lavoro duplicati, assicurarsi di disattivare o eliminare il flusso originale.

## <a name="deploy-template-by-using-visual-studio"></a>Distribuire un modello con Visual Studio

Se Visual Studio è stato configurato con i [prerequisiti](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) per la creazione di app per la logica, è possibile distribuire il modello esportato da Visual Studio in app per la logica di Azure.

1. In Visual Studio aprire il file modello esportato da Power Automate.

1. In Visual Studio creare un progetto gruppo di risorse di Azure e selezionare il modello app per la **logica** seguendo i passaggi descritti in [Guida introduttiva: creare attività, processi e flussi di lavoro automatizzati con app per la logica di Azure-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md), ad esempio:

   ![Creare un progetto Gruppo di risorse di Azure](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. Da Esplora soluzioni aprire il file **LogicApp. JSON** , se il file non è già aperto.

1. Copiare il contenuto del modello esportato e sovrascrivere il contenuto nel file **LogicApp. JSON** .

1. Prima di distribuire l'app per la logica, autorizzare le nuove connessioni attenendosi alla procedura seguente:

   1. Aprire il menu di scelta rapida **LogicApp. JSON** e quindi selezionare **Apri con progettazione app per la logica**.

      ![Aprire un modello con progettazione app per la logica](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Se richiesto, selezionare la sottoscrizione di Azure e il gruppo di risorse che si vuole usare per la distribuzione dell'app per la logica.

      ![Selezionare la sottoscrizione di Azure e il gruppo di risorse](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Quando l'app per la logica viene visualizzata nella finestra di progettazione, tutte le connessioni che richiedono l'autorizzazione visualizzano le icone di avviso:

      ![Connessioni con icone di avviso](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Per ogni passaggio che richiede una connessione autorizzata, espandere il passaggio e selezionare **Aggiungi nuovo**.

      ![Aggiungi nuova connessione](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Accedere a ogni servizio o fornire le credenziali necessarie per autorizzare la connessione.

   1. Salvare la soluzione prima di distribuire l'app per la logica.

1. In Esplora soluzioni aprire il menu di scelta rapida del progetto e selezionare **distribuisci** > **nuovo**. Se richiesto, accedere con il proprio account di Azure.

1. Quando richiesto, confermare la sottoscrizione di Azure, il gruppo di risorse di Azure e tutte le altre impostazioni che si vuole usare per la distribuzione, ad esempio un [file di parametri](../azure-resource-manager/resource-group-template-deploy.md#parameter-files) da usare per passare i valori dei parametri di modello, quindi selezionare **Distribuisci**.

   ![Confermare le impostazioni di distribuzione](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Se viene visualizzata la casella **modifica parametri** , specificare il nome della risorsa dell'app per la logica in Azure e selezionare **Salva**.  

   ![Modifica parametri di distribuzione](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   All'avvio della distribuzione, lo stato di distribuzione dell'app sarà visualizzato nella finestra **Output** di Visual Studio. Se lo stato non è visualizzato, aprire l'elenco **Mostra output di** e selezionare il proprio gruppo di risorse di Azure. Ad esempio:

   ![Finestra Output](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Se per le connessioni nell'app per la logica è necessario un input, viene visualizzata una finestra di PowerShell in background e viene richiesta la password o le chiavi segrete necessarie. Dopo avere immesso queste informazioni, la distribuzione continuerà.

   ![Autenticare le connessioni](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Al termine della distribuzione, l'app per la logica viene pubblicata ma non è attivata nel portale di Azure.

1. Quando si è pronti per attivare l'app per la logica nella portale di Azure, trovare e aprire l'app per la logica nella finestra di progettazione dell'app per la logica. Scegliere **Panoramica**dal menu dell'app per la logica e quindi selezionare **Abilita**.

1. Per evitare l'esecuzione di flussi di lavoro duplicati, assicurarsi di disattivare o eliminare il flusso originale.

Per altre informazioni su questi passaggi di distribuzione, vedere [Guida introduttiva: creare attività, processi e flussi di lavoro automatizzati con app per la logica di Azure-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sui [connettori per le app per la logica di Azure](../connectors/apis-list.md)
* Altre informazioni sulle [app](../logic-apps/logic-apps-overview.md) per la logica di Azure
