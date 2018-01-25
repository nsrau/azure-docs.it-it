---
title: Gestire le app per la logica in Visual Studio - App per la logica di Azure | Microsoft Docs
description: Gestire le app per la logica e altri asset di Azure con Visual Studio Cloud Explorer
author: klam
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 12/19/2016
ms.author: LADocs; klam
ms.openlocfilehash: 34ce65ec272c9fe5be8f95371179de092c5df2f3
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>Gestire le app per la logica con Visual Studio Cloud Explorer

Sebbene il [portale di Azure](https://portal.azure.com/) rappresenti un ottimo modo per progettare e gestire le app per la logica di Azure, esiste anche Visual Studio Cloud Explorer per gestire le risorse di Azure, incluse le app per la logica. Visual Studio Cloud Explorer consente di esplorare, gestire, modificare e scaricare app per la logica pubblicate. Le attività di gestione includono l'abilitazione, la disabilitazione e la visualizzazione della cronologia delle esecuzioni. 

Prima di poter accedere alle app per la logica e gestirle in Visual Studio, installare e configurare questi strumenti di Visual Studio per App per la logica di Azure. 

## <a name="prerequisites"></a>prerequisiti

* [Visual Studio 2015 o Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [Versione più recente di Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 o versione successiva)
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* Accesso al Web mentre viene usata la finestra di progettazione incorporata

## <a name="install-visual-studio-tools-for-logic-apps"></a>Installare gli strumenti di Visual Studio per le app per la logica

Dopo avere installato i prerequisiti, scaricare e installare Strumenti App per la logica di Azure per Visual Studio.

1. Aprire Visual Studio. Scegliere **Estensioni e aggiornamenti** dal menu **Strumenti**.
2. Espandere la categoria **Online** per poter eseguire le ricerche in Visual Studio Gallery.
3. Sfogliare o cercare **App per la logica** per visualizzare **Azure Logic Apps Tools for Visual Studio** (Strumenti per app per la logica di Azure per Visual Studio).
4. Fare clic sul pulsante **Scarica** per scaricare e installare l'estensione.
5. Al termine dell'installazione riavviare Visual Studio.

> [!NOTE]
> Per scaricare Strumenti App per la logica di Azure per Visual Studio direttamente, accedere al [Visual Studio Marketplace](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9).

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>Cercare App per la logica in Cloud Explorer

1.  Per aprire Cloud Explorer, scegliere **Cloud Explorer** nel menu **Visualizza**.
2.  Cercare l'app per la logica, in base al gruppo di risorse o al tipo di risorsa. 

    * In caso di esplorazione per tipo di risorsa, selezionare la sottoscrizione di Azure, espandere la sezione **App per la logica** e selezionare un'app per la logica. 
    * Se si esplora in base al gruppo di risorse, espandere il gruppo di risorse che contiene l'app per la logica desiderata e selezionarla.

    Per visualizzare i comandi per l'app per la logica, fare clic con il pulsante destro del mouse sull'app per la logica oppure, nella parte inferiore di Cloud Explorer, scegliere un comando dal menu **Azioni**.

    ![Cercare l'app per la logica](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-apps-designer"></a>Modificare l'app per la logica con Progettazione app per la logica

In Cloud Explorer è possibile aprire un'app per la logica attualmente distribuita nella stessa finestra di progettazione che si usa nel portale di Azure. 

* Per modificare l'app per la logica, in Cloud Explorer fare clic con il pulsante destro del mouse sull'app per la logica e selezionare **Apri con Editor app per la logica**. 

* Per pubblicare gli aggiornamenti al cloud, scegliere **Pubblica**. 

* Per avviare una nuova esecuzione, scegliere **Esegui trigger**.

![Progettazione app per la logica](./media/logic-apps-manage-from-vs/designer.png)

Dalla finestra di progettazione è anche possibile **scaricare** un'app per la logica. Questa azione parametrizza automaticamente la definizione dell'app per la logica e salva la definizione come modello di distribuzione di Azure Resource Manager. Questo modello può essere aggiunto al progetto Gruppo di risorse di Azure.

## <a name="browse-your-logic-app-run-history"></a>Esplorare la cronologia di esecuzione dell'app per la logica

Per visualizzare la cronologia di esecuzione per l'app per la logica, fare clic con il pulsante destro del mouse sull'app per la logica e selezionare **Apri cronologia di esecuzione**. Per riordinare la cronologia di esecuzione in base a una delle proprietà visualizzate, selezionare l'intestazione di colonna.

![Cronologia di esecuzione](media/logic-apps-manage-from-vs/runs.png)

Per visualizzare la cronologia di esecuzione di un'istanza ed esaminare i risultati dell'esecuzione, inclusi gli input e gli output di ogni passaggio, fare doppio clic su una delle istanze di esecuzione.

![Risultati della cronologia di esecuzione, input e output dei passaggi](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>Passaggi successivi

* [Creare la prima app per la logica](quickstart-create-first-logic-app-workflow.md)
* [Progettare, compilare e distribuire app per la logica in Visual Studio](logic-apps-deploy-from-vs.md)
* [Visualizzare esempi e scenari comuni](logic-apps-examples-and-scenarios.md)
* [Video: Automate business processes with Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/T694) (Automatizzare i processi aziendali con App per la logica di Azure)
* [Video: Integrate your systems with Azure Logic Apps](http://channel9.msdn.com/Events/Build/2016/P462) (Integrare i sistemi con App per la logica di Azure)
