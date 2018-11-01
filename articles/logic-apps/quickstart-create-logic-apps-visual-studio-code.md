---
title: Creare e gestire flussi di lavoro automatizzati con Visual Studio Code - App per la logica di Azure | Microsoft Docs
description: Guida introduttiva per creare e gestire app per la logica con JSON in Visual Studio Code (VS Code)
services: logic-apps
ms.service: logic-apps
ms.workload: azure-vs
author: ecfan
ms.author: estfan
ms.topic: article
ms.reviewer: klam, deli, LADocs
ms.suite: integration
ms.date: 10/05/2018
ms.openlocfilehash: 0fec590523fa130af2e5670a92914c056df289d1
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50229618"
---
# <a name="quickstart-create-and-manage-automated-logic-app-workflows---visual-studio-code"></a>Guida introduttiva: Creare e gestire flussi di lavoro automatizzati delle app per la logica - Visual Studio Code

Con [App per la logica di Azure](../logic-apps/logic-apps-overview.md) e Visual Studio Code, è possibile creare e gestire app per la logica che consentono di automatizzare attività, flussi di lavoro e processi per l'integrazione di app, dati, sistemi e servizi tra aziende e organizzazioni. Questa Guida introduttiva illustra come è possibile creare e modificare definizioni del flusso di lavoro delle app per la logica usando lo schema di definizione del flusso di lavoro in JavaScript Object Notation (JSON) tramite un'esperienza basata su codice. È anche possibile usare le app per la logica esistenti già distribuite in <a href="https://docs.microsoft.com/azure/guides/developer/azure-developer-guide" target="_blank">Azure</a> nel cloud. 

Anche se è possibile eseguire queste stesse attività nel <a href="https://portal.azure.com" target="_blank">portale di Azure</a> e in Visual Studio, quando si ha già familiarità con le app per la logica e si vuole lavorare direttamente nel codice, Visual Studio Code permette di iniziare più velocemente. È ad esempio possibile disabilitare, abilitare, eliminare e aggiornare le app per la logica già create. Inoltre, è possibile lavorare con app per la logica e account di integrazione da qualsiasi piattaforma di sviluppo che supporta l'esecuzione di Visual Studio Code, ad esempio Linux, Windows e Mac.

È possibile creare la stessa app per la logica creata in questo articolo seguendo la [guida introduttiva per la creazione di un'app per la logica nel portale di Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), che approfondisce meglio i concetti di base. In Visual Studio Code l'app per la logica è simile a questo esempio:

![App per la logica completata](./media/create-logic-apps-visual-studio-code/overview.png)

Prima di iniziare, verificare di avere gli elementi seguenti:

* Se non si ha una sottoscrizione di Azure, <a href="https://azure.microsoft.com/free/" target="_blank">iscriversi per creare un account Azure gratuito</a>.

* Conoscenza di base delle [definizioni dei flussi di lavoro delle app per la logica](../logic-apps/logic-apps-workflow-definition-language.md) e relativa struttura, che usa JavaScript Object Notation (JSON) 

  Se si ha familiarità con le app per la logica, provare a eseguire la Guida introduttiva che illustra [come creare la prima app per la logica nel portale di Azure](../logic-apps/quickstart-create-first-logic-app-workflow.md), maggiormente incentrata sui concetti di base. 

* Accesso al Web per accedere ad Azure e alla sottoscrizione di Azure

* Scaricare e installare questi strumenti, se non sono già disponibili: 

  * <a href="https://code.visualstudio.com/" target="_blank">Visual Studio Code versione 1.25.1 o successiva</a>, disponibile gratuitamente

  * Estensione Visual Studio Code per App per la logica di Azure

    È possibile scaricare e installare questa estensione da [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) o direttamente dall'interno di Visual Studio Code. 
    Assicurarsi di ricaricare Visual Studio Code dopo l'installazione. 

    ![Trovare "Visual Studio Code extension for Azure Logic Apps" (Estensione Visual Studio Code per App per la logica di Azure)](./media/create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Verificare che l'icona di Azure sia visualizzata nella barra degli strumenti di Visual Studio Code per essere certi che l'estensione sia stata installata correttamente. 

    ![Estensione installata](./media/create-logic-apps-visual-studio-code/installed-extension.png)

    Per altre informazioni, vedere <a href="https://code.visualstudio.com/docs/editor/extension-gallery" target="_blank">Extension Marketplace</a> (Marketplace delle estensioni). È anche possibile visualizzare e inviare contributi per questa versione open source dell'estensione, visitando la pagina dell'[estensione di App per la logica di Azure per Visual Studio Code su GitHub](https://github.com/Microsoft/vscode-azurelogicapps). 

<a name="sign-in-azure"></a>

## <a name="sign-in-to-azure"></a>Accedere ad Azure

1. Aprire Visual Studio Code. Sulla barra degli strumenti di Visual Studio Code selezionare l'icona di Azure. 

   ![Selezionare l'icona di Azure](./media/create-logic-apps-visual-studio-code/open-extension.png)

1. Nella finestra di Azure, in **App per la logica** selezionare **Accedi ad Azure**. 

   ![Selezionare "Accedi ad Azure"](./media/create-logic-apps-visual-studio-code/sign-in-azure.png)

   A questo punto viene chiesto di accedere usando il codice di autenticazione fornito. 

1. Copiare il codice di autenticazione e quindi scegliere **Copia e apri** per aprire una nuova finestra del browser.

   ![Richiesta di accesso](./media/create-logic-apps-visual-studio-code/sign-in-prompt.png)

1. Immettere il codice di autenticazione. Quando richiesto, scegliere **Continua**.

   ![Immettere il codice](./media/create-logic-apps-visual-studio-code/authentication-code.png)

1. Selezionare l'account Azure. Dopo aver effettuato l'accesso, è possibile chiudere il browser e tornare a Visual Studio Code.

   Nella finestra di Azure i riquadri App per la logica e Account di integrazione mostrano ora le sottoscrizioni di Azure nell'account. 

   ![Selezionare la sottoscrizione](./media/create-logic-apps-visual-studio-code/select-azure-subscription.png)

   Se non vengono visualizzate le sottoscrizioni previste, accanto all'etichetta **App per la logica** scegliere **Selezionare le sottoscrizioni** (icona del filtro). Trovare e selezionare le sottoscrizioni desiderate.

1. Per visualizzare tutte le app per la logica o gli account di integrazione esistenti nella sottoscrizione di Azure, espandere la sottoscrizione.

   ![Visualizzare le app per la logica e gli account di integrazione](./media/create-logic-apps-visual-studio-code/existing-logic-apps.png)

<a name="create-logic-app"></a>

## <a name="create-logic-app"></a>Creare l'app per la logica

1. Se non è stato effettuato l'accesso alla sottoscrizione di Azure da Visual Studio Code, seguire la procedura descritta in questo articolo per [accedere ora](#sign-in-azure).

1. Dal menu di scelta rapida della sottoscrizione scegliere **Crea**.

   ![Scegliere "Crea"](./media/create-logic-apps-visual-studio-code/create-logic-app.png)

1. Nell'elenco contenente i gruppi di risorse di Azure della sottoscrizione selezionare un gruppo di risorse esistente o **creare un nuovo gruppo di risorse**. 

   Questo esempio crea un nuovo gruppo di risorse:

   ![Creare un nuovo gruppo di risorse](./media/create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Specificare un nome per il gruppo di risorse di Azure e quindi premere INVIO.

   ![Assegnare un nome al gruppo di risorse](./media/create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Selezionare la località del data center in cui salvare i metadati dell'app per la logica.

   ![Selezionare la località](./media/create-logic-apps-visual-studio-code/select-location.png)

1. Assegnare un nome all'app per la logica e quindi premere INVIO.

   ![Assegnare un nome all'app per la logica](./media/create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   La nuova app per la logica viene ora visualizzata nella finestra di Azure, nella sottoscrizione di Azure. Ora è possibile avviare la creazione della definizione del flusso di lavoro dell'app per la logica.

1. Dal menu di scelta rapida dell'app per la logica scegliere **Apri nell'editor**. 

   ![Aprire l'app per la logica nell'editor](./media/create-logic-apps-visual-studio-code/open-new-logic-app.png)

   Visual Studio Code apre un modello di definizione di flusso di lavoro di app per la logica (file con estensione logicapp.json) per poter avviare la creazione del flusso di lavoro dell'app per la logica.

   ![Nuova definizione del flusso di lavoro dell'app per la logica](./media/create-logic-apps-visual-studio-code/blank-logic-app-workflow-definition.png)

1. Nel file modello di definizione del flusso di lavoro dell'app per la logica iniziare a compilare la definizione del flusso di lavoro dell'app per la logica. Per informazioni di riferimento tecniche, vedere lo [schema del linguaggio di definizione del flusso di lavoro per App per la logica di Azure](../logic-apps/logic-apps-workflow-definition-language.md).

   Di seguito è riportato un esempio di definizione logica. Gli elementi JSON vengono in genere visualizzati in ordine alfabetico all'interno di ogni sezione, ma questo esempio mostra questi elementi più o meno nell'ordine in cui vengono visualizzati i passaggi dell'app per la logica nella finestra di progettazione.

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "Title: @{triggerBody()?['title']}\n\nDate published: @{triggerBody()?['publishDate']}\n\nLink: @{triggerBody()?['primaryLink']}",
                  "Subject": "New RSS item: @{triggerBody()?['title']}",
                  "To": "Sophie.Owen@contoso.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['outlook']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/Mail"
            }
         }
      },
      "outputs": {}
   }   
   ```

1. Al termine, salvare il file della definizione dell'app per la logica. Quando Visual Studio Code chiede di confermare il caricamento della definizione dell'app per la logica nella sottoscrizione di Azure, scegliere **Carica**.

   ![Caricare la nuova app per la logica](./media/create-logic-apps-visual-studio-code/upload-new-logic-app.png)

   Dopo che Visual Studio Code ha pubblicato l'app per la logica in Azure, è possibile trovare l'app in esecuzione nel portale di Azure. 

   ![App per la logica pubblicata nel portale di Azure](./media/create-logic-apps-visual-studio-code/published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-logic-app"></a>Modificare l'app per la logica

Per modificare un'app per la logica esistente già distribuita in Azure, è possibile aprire il file della definizione del flusso di lavoro dell'app in Visual Studio Code.

1. Se non è stato effettuato l'accesso alla sottoscrizione di Azure da Visual Studio Code, seguire la procedura descritta in questo articolo per [accedere ora](#sign-in-azure).

1. Nella finestra di Azure, in **App per la logica** espandere la sottoscrizione di Azure e selezionare l'app per la logica desiderata. 

1. Dal menu dell'app per la logica scegliere **Apri nell'editor** oppure scegliere l'icona di modifica accanto al nome dell'app per la logica.

   ![Aprire l'editor per l'app per la logica esistente](./media/create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code apre il file con estensione logicapp.json della definizione del flusso di lavoro dell'app per la logica.

   ![Definizione del flusso di lavoro dell'app per la logica aperta](./media/create-logic-apps-visual-studio-code/edit-logic-app-workflow-definition-file.png)

1. Apportare le modifiche nella definizione dell'app per la logica.

1. Al termine, salvare le modifiche.

1. Quando Visual Studio Code chiede di aggiornare la definizione dell'app per la logica nella sottoscrizione di Azure, scegliere **Carica**. 

   ![Caricare le modifiche](./media/create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="get-support"></a>Supporto

* In caso di domande, visitare il <a href="https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps" target="_blank">forum di App per la logica di Azure</a>.
* Per votare o inviare idee relative alle funzionalità, visitare il <a href="https://aka.ms/logicapps-wish" target="_blank">sito dei commenti e suggerimenti degli utenti di App per la logica</a>.

