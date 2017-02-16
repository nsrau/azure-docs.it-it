---
title: Gestire le app per la logica di Azure da Visual Studio Cloud Explorer | Documentazione Microsoft
description: Gestire le app per la logica di Azure da Visual Studio Cloud Explorer.
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
ms.date: 12/19/2016
ms.author: klam
translationtype: Human Translation
ms.sourcegitcommit: a9f786259676a1bc9aa616f2db2935dff45475e2
ms.openlocfilehash: dcb0512b4f9e6c94262a3f39ef59f82fcfbf313e


---
# <a name="manage-logic-apps-with-the-visual-studio-cloud-explorer"></a>Gestire le app per la logica con Visual Studio Cloud Explorer
Nonostante il [portale di Azure](https://portal.azure.com) sia un'ottima soluzione per progettare e gestire app per la logica, Visual Studio Cloud Explorer consente di gestire molti asset di Azure, tra cui le app per la logica, in Visual Studio.  Con Cloud Explorer è possibile esplorare le app per la logica pubblicate, eseguire azioni come l'abilitazione, la disabilitazione e la modifica e visualizzare le cronologie di esecuzione. 

## <a name="installation-steps"></a>Procedura di installazione
Di seguito sono riportati i passaggi per installare e configurare gli strumenti di Visual Studio per le app per la logica.

### <a name="prerequisites"></a>Prerequisiti
* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [Versione più recente di Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 o versione successiva)
* [Visual Studio Cloud Explorer](https://marketplace.visualstudio.com/items?itemName=MicrosoftCloudExplorer.CloudExplorerforVisualStudio2015)
* Accesso al Web mentre viene usata la finestra di progettazione incorporata

### <a name="install-visual-studio-tools-for-logic-apps"></a>Installare gli strumenti di Visual Studio per le app per la logica
Dopo aver installato i prerequisiti: 

1. Aprire Visual Studio 2015 e quindi scegliere **Estensioni e aggiornamenti** dal menu **Strumenti**
2. Selezionare la categoria **Online** per eseguire la ricerca online
3. Cercare **App per la logica** per visualizzare **Azure Logic Apps Tools for Visual Studio** (Strumenti per app per la logica di Azure per Visual Studio)
4. Fare clic sul pulsante **Scarica** per scaricare e installare l'estensione
5. Al termine dell'installazione riavviare Visual Studio

> [!NOTE]
> È anche possibile scaricare l'estensione direttamente da [questo collegamento](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)
> 
> 

## <a name="browsing-logic-apps"></a>Esplorazione delle app per la logica
Per esplorare le app per la logica in Cloud Explorer, aprire Cloud Explorer scegliendo Visualizza > Cloud Explorer. È possibile eseguire l'esplorazione per gruppo di risorse o per tipo di risorsa.  In caso di esplorazione per tipo di risorsa, selezionare una sottoscrizione, espandere la sezione App per la logica e quindi selezionare un'app per la logica.  Per eseguire l'azione desiderata, è possibile fare clic con il pulsante destro del mouse su un'app per la logica oppure usare il menu Azioni nella parte inferiore di Cloud Explorer.

![Sfoglia](./media/logic-apps-manage-from-vs/browse.png)

## <a name="edit-logic-app-with-the-designer"></a>Modificare un'app per la logica con la finestra di progettazione
Per aprire l'app per la logica attualmente distribuita nella stessa finestra di progettazione disponibile nel portale di Azure, fare clic con il pulsante destro del mouse sull'app per la logica e scegliere "Apri con Editor app per la logica".  Con la finestra di progettazione è possibile modificare l'app per la logica, salvarla nuovamente nel cloud e avviare una nuova esecuzione con il pulsante "Esegui trigger".

![Finestra di progettazione](./media/logic-apps-manage-from-vs/designer.png)

## <a name="browse-logic-app-run-history"></a>Esplorare la cronologia di esecuzione di un'app per la logica
Per visualizzare la cronologia di esecuzione per un'app per la logica, fare clic con il pulsante destro del mouse sull'app per la logica e scegliere "Apri cronologia di esecuzione".  È possibile ordinare la visualizzazione in base a qualsiasi proprietà visualizzata, selezionando l'intestazione di colonna.  

![Esecuzioni](media/logic-apps-manage-from-vs/runs.png)

Facendo doppio clic su un'istanza di esecuzione viene visualizzata la cronologia di esecuzione di tale istanza con i risultati dell'esecuzione e gli input e gli output di ogni passaggio.

![Cronologia](./media/logic-apps-manage-from-vs/history.png)

## <a name="next-steps"></a>Passaggi successivi
* Per iniziare a usare le app per la logica, seguire l'esercitazione [Creare una nuova app per la logica](logic-apps-create-a-logic-app.md) .  
* [Visualizzare esempi e scenari comuni](logic-apps-examples-and-scenarios.md)
* [Le app per la logica consentono di automatizzare i processi aziendali](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Informazioni su come integrare i sistemi correnti con le app per la logica](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Jan17_HO3-->


