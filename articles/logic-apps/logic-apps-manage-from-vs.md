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
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: 2fafb3c127183c4903ed7ee0da5811988db9116c
ms.openlocfilehash: 9a542f83eb35b159de04f0395cd850376da498de
ms.lasthandoff: 03/02/2017

---

# <a name="manage-your-logic-apps-with-visual-studio-cloud-explorer"></a>Gestire le app per la logica con Visual Studio Cloud Explorer

Nonostante il [Portale di Azure](https://portal.azure.com/) offra un ottimo modo per progettare e gestire app per la logica di Azure, quando si usa Visual Studio Cloud Explorer è possibile gestire molti asset di Azure, tra cui le app per la logica, in Visual Studio. È possibile esplorare le app per la logica pubblicate ed eseguire operazioni come l'abilitazione e la disabilitazione delle app per la logica o la modifica e la visualizzazione delle cronologie di esecuzione. 

## <a name="installation-steps"></a>Procedura di installazione

Per installare e configurare gli strumenti di Visual Studio per le app per la logica di Azure, seguire questi passaggi.

### <a name="prerequisites"></a>Prerequisiti

* [Visual Studio 2015 o Visual Studio 2017](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [Versione più recente di Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 o versione successiva)
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* Accesso al Web mentre viene usata la finestra di progettazione incorporata

### <a name="install-visual-studio-tools-for-logic-apps"></a>Installare gli strumenti di Visual Studio per le app per la logica

Dopo aver installato i prerequisiti:

1. Aprire Visual Studio. Scegliere **Estensioni e aggiornamenti** dal menu **Strumenti**.
2. Espandere la categoria **In rete** per poter eseguire le ricerche in rete.
3. Sfogliare o cercare **App per la logica** per visualizzare **Azure Logic Apps Tools for Visual Studio** (Strumenti per app per la logica di Azure per Visual Studio).
4. Fare clic sul pulsante **Scarica** per scaricare e installare l'estensione.
5. Al termine dell'installazione riavviare Visual Studio.

> [!NOTE]
> È inoltre possibile scaricare gli strumenti App per la logica di Azure per Visual Studio direttamente il [Visual Studio Marketplace](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9).

## <a name="browse-for-logic-apps-in-cloud-explorer"></a>Cercare App per la logica in Cloud Explorer

1.    Per aprire Cloud Explorer, scegliere **Cloud Explorer** nel menu **Visualizza**.
2.    Cercare l'app per la logica, in base al gruppo di risorse o al tipo di risorsa. 

    In caso di esplorazione per tipo di risorsa, selezionare la sottoscrizione di Azure, espandere la sezione App per la logica e selezionare un'app per la logica. 
    È possibile fare clic con il pulsante destro del mouse su un'app per la logica o scegliere dal menu **Azioni** nella parte inferiore di Cloud Explorer.

    ![Cercare l'app per la logica](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-your-logic-app-with-logic-app-designer"></a>Modificare l'app per la logica nella finestra di progettazione di app per la logica

Per aprire l'app per la logica attualmente distribuita nella stessa finestra di progettazione che viene usata nel Portale di Azure, fare clic con il pulsante destro del mouse sull'app per la logica e selezionare **Apri con Editor app per la logica**. 

Nella finestra di progettazione è possibile modificare l'app per la logica, salvare gli aggiornamenti al cloud e avviare una nuova esecuzione scegliendo **Esegui trigger**.

![Finestra di progettazione di app per la logica](./media/logic-apps-manage-from-vs/designer.png)

## <a name="browse-your-logic-app-run-history"></a>Esplorare la cronologia di esecuzione dell'app per la logica

Per visualizzare la cronologia di esecuzione per l'app per la logica, fare clic con il pulsante destro del mouse sull'app per la logica e selezionare **Apri cronologia di esecuzione**. Per riordinare la cronologia di esecuzione in base a una delle proprietà visualizzate, selezionare l'intestazione di colonna.

![Cronologia di esecuzione](media/logic-apps-manage-from-vs/runs.png)

Per visualizzare la cronologia di esecuzione di un'istanza con i risultati dell'esecuzione, inclusi gli input e gli output di ogni passaggio, fare doppio clic su una delle istanze di esecuzione.

![Risultati della cronologia di esecuzione, input e output dei passaggi](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>Passaggi successivi

*    Per un'introduzione alle app per la logica di Azure, imparare a [creare la prima app per la logica nel Portale di Azure](logic-apps-create-a-logic-app.md)
* [Progettare, compilare e distribuire app per la logica in Visual Studio](logic-apps-deploy-from-vs.md)
* [Visualizzare esempi e scenari comuni](logic-apps-examples-and-scenarios.md)
* [Introduzione all'automazione dei processi aziendali con le app per la logica di Azure](http://channel9.msdn.com/Events/Build/2016/T694)
* [Informazioni su come integrare i sistemi con le app per la logica di Azure](http://channel9.msdn.com/Events/Build/2016/P462)

