---
title: Compilare app per la logica di Azure in Visual Studio | Documentazione Microsoft
description: Creare un progetto in Visual Studio per creare e distribuire un&quot;app per la logica.
author: jeffhollan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: e484e5ce-77e9-4fa9-bcbe-f851b4eb42a6
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2016
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: a9f786259676a1bc9aa616f2db2935dff45475e2
ms.openlocfilehash: db818b2b76d3a8d8c3324c9556237139d319efb9


---
# <a name="build-and-deploy-logic-apps-in-visual-studio"></a>Compilare e distribuire app per la logica in Visual Studio
Nonostante il [portale di Azure](https://portal.azure.com/) offra un'ottima soluzione per progettare e gestire le app per la logica, è anche possibile progettare e distribuire un'app per la logica da Visual Studio.  Per le app per la logica è disponibile un set completo di strumenti di Visual Studio, che consente di compilare un'app per la logica con la finestra di progettazione, configurare qualsiasi modello di distribuzione e di automazione ed eseguire la distribuzione in qualsiasi ambiente.  

## <a name="installation-steps"></a>Procedura di installazione
Di seguito sono riportati i passaggi per installare e configurare gli strumenti di Visual Studio per le app per la logica.

### <a name="prerequisites"></a>Prerequisiti
* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [Versione più recente di Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 o versione successiva)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
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

Dopo l'installazione è possibile usare il progetto Gruppo di risorse di Azure con la finestra di progettazione delle app per la logica.

## <a name="create-a-project"></a>Creare un progetto
1. Scegliere **Nuovo**, **Progetto** >   dal menu **File**. In alternativa, è possibile selezionare **Aggiungi** e quindi **Nuovo progetto** per aggiungerlo a una soluzione esistente:  ![Menu File](./media/logic-apps-deploy-from-vs/filemenu.png)
2. Nella finestra di dialogo selezionare **Cloud** e quindi **Gruppo di risorse di Azure**. Digitare un **Nome** e quindi fare clic su **OK**.
    ![Aggiunta di un nuovo progetto](./media/logic-apps-deploy-from-vs/addnewproject.png)
3. Selezionare il modello **App per la logica** . Viene creato un modello di distribuzione di un'app per la logica vuoto come punto di partenza.
    ![Selezione del modello di Azure](./media/logic-apps-deploy-from-vs/selectazuretemplate1.png)
4. Dopo avere selezionato il **Modello**, fare clic su **OK**.
   
    Il progetto di app per la logica viene aggiunto alla soluzione. Il file di distribuzione verrà visualizzato in Esplora soluzioni:  
   
    ![Distribuzione](./media/logic-apps-deploy-from-vs/deployment.png)

## <a name="using-the-logic-app-designer"></a>Uso della finestra di progettazione delle app per la logica
Dopo aver creato un progetto Gruppo di risorse di Azure contenente un'app per la logica, è possibile aprire la finestra di progettazione in Visual Studio per creare il flusso di lavoro.  La finestra di progettazione richiede una connessione Internet per eseguire query sui connettori per ottenere le proprietà e i dati disponibili. Se si usa il connettore Dynamics CRM Online, ad esempio, la finestra di progettazione esegue una query sull'istanza di CRM per ottenere un elenco delle proprietà predefinite e personalizzate.

1. Fare clic con il pulsante destro del mouse sul file `<template>.json` e scegliere **Open with Logic App Designer** (Apri con finestra di progettazione app per la logica) oppure `Ctrl+L`
2. Scegliere la sottoscrizione, il gruppo di risorse e il percorso per il modello di distribuzione
   * È importante notare che progettando un'app per la logica verranno create risorse **Connessione API** per l'esecuzione di query per ottenere le proprietà durante la progettazione.  Il gruppo di risorse selezionato viene usato per creare tali connessioni durante la fase di progettazione.  È possibile visualizzare o modificare qualsiasi connessione API passando al portale di Azure e cercando **Connessioni API**.
   
     ![Selezione della sottoscrizione](./media/logic-apps-deploy-from-vs/designer_picker.png)
3. La finestra di progettazione eseguirà il rendering in base alla definizione contenuta nel file `<template>.json` .
4. È ora possibile creare e progettare l'app per la logica. Il modello di distribuzione viene aggiornato con le modifiche.
    ![Finestra di progettazione in Visual Studio](./media/logic-apps-deploy-from-vs/designer_in_vs.png)

Vengono aggiunte risorse `Microsoft.Web/connections` al file di risorse per tutte le connessioni necessarie per il funzionamento dell'app per la logica.  Queste proprietà di connessione possono essere impostate al momento della distribuzione ed essere gestite successivamente in **Connessioni API** nel portale di Azure.

### <a name="switching-to-the-json-code-view"></a>Passaggio alla visualizzazione del codice JSON
È possibile selezionare la scheda **Visualizzazione Codice** nella parte inferiore della finestra di progettazione per passare alla rappresentazione JSON dell'app per la logica.  Per tornare al file di risorse JSON completo, fare clic con il pulsante destro del mouse su `<template>.json` e scegliere **Apri**.

### <a name="saving-the-logic-app"></a>Salvataggio dell'app per la logica
È possibile salvare l'app per la logica in qualsiasi momento con il pulsante **Salva** o con `Ctrl+S`.  Se al momento del salvataggio sono presenti errori nell'app per la logica, questi vengono visualizzati nella finestra **Output** di Visual Studio.

## <a name="deploying-your-logic-app"></a>Distribuzione dell'app per la logica
Infine, dopo aver configurato l'app è possibile distribuirla direttamente da Visual Studio in pochi passaggi. 

1. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e passare a **Distribuisci** > **Nuova distribuzione**
    ![Nuova distribuzione](./media/logic-apps-deploy-from-vs/newdeployment.png)
2. Verrà richiesto di accedere alla sottoscrizione di Azure. 
3. A questo punto è necessario scegliere i dettagli del gruppo di risorse in cui si vuole distribuire l'app per la logica. 
    ![Distribuzione in un gruppo di risorse](./media/logic-apps-deploy-from-vs/deploytoresourcegroup.png)
   
   > [!NOTE]
   > Assicurarsi di selezionare il modello e i file dei parametri appropriati per il gruppo di risorse (ad esempio, se si effettua la distribuzione in un ambiente di produzione è opportuno scegliere il file dei parametri di produzione). 
   > 
   > 
4. Selezionare il pulsante Distribuisci
5. Lo stato della distribuzione viene visualizzato nella finestra **Output**. Potrebbe essere necessario scegliere **Provisioning Azure**. 
    ![Output](./media/logic-apps-deploy-from-vs/output.png)

In futuro sarà possibile modificare l'app per la logica nel controllo del codice sorgente e usare Visual Studio per distribuire nuove versioni. 

> [!NOTE]
> Se si modifica la definizione direttamente nel portale di Azure, alla successiva distribuzione da Visual Studio tali modifiche verranno sovrascritte.
> 
> 

## <a name="adding-a-logic-app-to-an-existing-resource-group-project"></a>Aggiunta di un'app per la logica a un progetto Gruppo di risorse esistente
Se esiste già un progetto Gruppo di risorse, per aggiungere un'app per la logica o un'altra app per la logica con quella creata prima, è possibile usare la finestra Struttura JSON.
1. Aprire il file `<template>.json` .
2. Aprire la finestra Struttura JSON.  La finestra Struttura JSON è disponibile in **Visualizza** > **Altre finestre** > **Struttura JSON**.
3. Per aggiungere una risorsa al modello, fare clic sul pulsante Aggiungi risorsa nella parte superiore della finestra Struttura JSON oppure fare clic con il pulsante destro del mouse su **risorse** e scegliere **Aggiungi nuova risorsa**.

    ![Struttura JSON](./media/logic-apps-deploy-from-vs/jsonoutline.png)
    
4. Nella finestra di dialogo **Aggiungi risorsa** cercare e selezionare **App per la logica**, assegnare un nome e selezionare **Aggiungi**.

    ![Aggiungere una risorsa](./media/logic-apps-deploy-from-vs/addresource.png)

## <a name="next-steps"></a>Passaggi successivi
* Per iniziare a usare le app per la logica, seguire l'esercitazione [Creare una nuova app per la logica](logic-apps-create-a-logic-app.md) .  
* [Visualizzare esempi e scenari comuni](logic-apps-examples-and-scenarios.md)
* [Le app per la logica consentono di automatizzare i processi aziendali](http://channel9.msdn.com/Events/Build/2016/T694) 
* [Informazioni su come integrare i sistemi correnti con le app per la logica](http://channel9.msdn.com/Events/Build/2016/P462)




<!--HONumber=Jan17_HO3-->


