---
title: Compilare e distribuire app per la logica di Azure in Visual Studio | Microsoft Docs
description: Creare progetti di Visual Studio per progettare, compilare e distribuire app per la logica in Azure.
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
ms.date: 2/14/2017
ms.author: jehollan
translationtype: Human Translation
ms.sourcegitcommit: bbee0bae5a20375f0de8adaedadd682dc051fb64
ms.openlocfilehash: d1b0ca953e5ee40129f00b78ad1a46514270d448


---
# <a name="build-and-deploy-azure-logic-apps-in-visual-studio"></a>Compilare e distribuire app per la logica di Azure in Visual Studio

Sebbene il [Portale di Azure](https://portal.azure.com/) sia uno strumento eccellente per la progettazione e la gestione delle app per la logica, è consigliabile usare Visual Studio per la compilazione e la distribuzione della app per la logica. Visual Studio offre strumenti avanzati per compilare le app per la logica con Progettazione app per la logica, configurare i modelli di distribuzione e automazione e distribuirli in qualsiasi ambiente.

## <a name="installation-steps"></a>Procedura di installazione

Per installare e configurare gli strumenti di Visual Studio per le app per la logica di Azure, seguire questi passaggi.

### <a name="prerequisites"></a>Prerequisiti

* [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* [Versione più recente di Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 o versione successiva)
* [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
* Accesso al Web mentre viene usata la finestra di progettazione incorporata

### <a name="install-visual-studio-tools-for-azure-logic-apps"></a>Installare gli strumenti di Visual Studio per le app per la logica di Azure

Dopo aver installato i prerequisiti:

1. Aprire Visual Studio 2015. Scegliere **Estensioni e aggiornamenti** dal menu **Strumenti**.
2. Espandere la categoria **In rete** per poter eseguire le ricerche in rete.
3. Sfogliare o cercare **App per la logica** per visualizzare **Azure Logic Apps Tools for Visual Studio** (Strumenti per app per la logica di Azure per Visual Studio).
4. Fare clic sul pulsante **Scarica** per scaricare e installare l'estensione.
5. Al termine dell'installazione riavviare Visual Studio.

> [!NOTE]
> È inoltre possibile scaricare gli strumenti App per la logica di Azure per Visual Studio direttamente il [Visual Studio Marketplace](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9).

Al completamento dell'installazione è possibile usare il progetto Gruppo di risorse di Azure con la Progettazione delle app per la logica.

## <a name="create-your-project"></a>Creare il progetto

1. Nel menu **File**, andare in **Nuovo**e selezionare **Progetto**. Oppure per aggiungere il progetto a una soluzione esistente, andare in **Aggiungi**e selezionare **Nuovo progetto**.

    ![File menu](./media/logic-apps-deploy-from-vs/filemenu.png)

2. Nella finestra **Nuovo progetto** cercare **Cloud** e selezionare **Gruppo di risorse di Azure**. Assegnare un nome al progetto e fare clic su **OK**.

    ![Add new project](./media/logic-apps-deploy-from-vs/addnewproject.png)

3. Selezionare il modello **App per la logica** che crea un modello di distribuzione delle app per la logica vuoto che è possibile usare. Dopo aver selezionato il modello, fare clic su **OK**.

    ![Selezionare il modello App per la logica](./media/logic-apps-deploy-from-vs/selectazuretemplate1.png)

    Il progetto di app per la logica viene aggiunto alla soluzione. 
    Il file di distribuzione verrà visualizzato in Esplora soluzioni.

    ![File di distribuzione](./media/logic-apps-deploy-from-vs/deployment.png)

## <a name="create-your-logic-app-in-logic-app-designer"></a>Creare app per la logica in Progettazione app per la logica

Dopo aver creato un progetto Gruppo di risorse di Azure contenente un'app per la logica, è possibile aprire Progettazione app per la logica in Visual Studio per creare il flusso di lavoro. 

> [!NOTE]
> La finestra di progettazione richiede una connessione internet ai connettori di query per i dati e le proprietà disponibili. Ad esempio, se si utilizza il connettore di Dynamics CRM Online, la finestra di progettazione richiede l'istanza CRM per visualizzare le proprietà predefinite e personalizzate disponibili.

1. Fare clic con il pulsante destro del mouse sul file `<template>.json` e scegliere **Open with Logic App Designer** (Apri con Progettazione app per la logica). (`Ctrl+L`)

2. Scegliere la sottoscrizione di Azure, il gruppo di risorse e la posizione per il modello di distribuzione.

    > [!NOTE]
    > Progettando un'app per la logica verranno create risorse di connessione API per l'esecuzione di query per ottenere le proprietà durante la progettazione. Il gruppo di risorse selezionato viene usato da Visual Studio per creare tali connessioni durante la fase di progettazione. Per visualizzare o modificare qualsiasi connessione API, andare nel Portale di Azure e cercare **Connessioni API**.

    ![Selezione della sottoscrizione](./media/logic-apps-deploy-from-vs/designer_picker.png)

    La finestra di progettazione usa la definizione nel file `<template>.json` per il rendering.

4. Creare e progettare l'app per la logica. Il modello di distribuzione viene aggiornato con le modifiche.

    ![Progettazione app per la logica in Visual Studio](./media/logic-apps-deploy-from-vs/designer_in_vs.png)

Visual Studio aggiunge le risorse `Microsoft.Web/connections` al file di risorse per tutte le connessioni necessarie all'app per la logica per funzionare. Queste proprietà di connessione possono essere impostate al momento della distribuzione ed essere gestite successivamente in **Connessioni API** nel portale di Azure.

### <a name="switch-to-json-code-view"></a>Passare alla visualizzazione del codice JSON

È possibile selezionare la scheda **Visualizzazione Codice** nella parte inferiore della finestra di progettazione per visualizzare la rappresentazione JSON dell'app per la logica.

Per tornare al file di risorse JSON completo, fare clic con il pulsante destro del mouse su `<template>.json` e scegliere **Apri**.

### <a name="add-references-for-dependent-resources-to-visual-studio-deployment-templates"></a>Aggiungere riferimenti per le risorse dipendenti ai modelli di distribuzione di Visual Studio

Quando si desidera che l'app per la logica faccia riferimento alle risorse dipendenti, è possibile usare le [funzioni del modello di Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions), come i parametri, nel modello di distribuzione di app per la logica. Ad esempio, l'utente può fare in modo che l'app per la logica faccia riferimento a una funzione di Azure o a un account di integrazione che si desidera distribuire insieme all'app per la logica. Seguire queste linee guida sull'uso dei parametri nel modello di distribuzione in modo che la Progettazione app per la logica esegua il rendering correttamente. 

È possibile usare i parametri dell'app per la logica in questi tipi di trigger e azioni:

*   Flusso di lavoro figlio
*   App per le funzioni
*   Chiamata APIM
*   URL di runtime della connessione API

Ed è possibile usare queste funzioni di modello: l'elenco sottostante include parametri, variabili, resourceId, CONCAT e così via. Ad esempio, ecco come è possibile sostituire l'ID di risorsa della funzione di Azure:

```
"parameters":{
    "functionName": {
    "type":"string",
    "minLength":1,
    "defaultValue":"<FunctionName>"
    }
},
```

E dove è possibile usare i parametri:

```
"MyFunction": {
        "type": "Function",
        "inputs": {
        "body":{},
        "function":{
        "id":"[resourceid('Microsoft.Web/sites/functions','functionApp',parameters('functionName'))]"
        }
    },
    "runAfter":{}
}
```

> [!NOTE] 
> Affinché la Progettazione dell'app per la logica funzioni quando si usano i parametri, è necessario fornire valori predefiniti, ad esempio:
> 
> ```
> "parameters": {
>     "IntegrationAccount": {
>     "type":"string",
>     "minLength":1,
>     "defaultValue":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Logic/integrationAccounts/<integrationAccountName>"
>     }
> },
> ```

### <a name="save-your-logic-app"></a>Salvare l'app per la logica

Per salvare l'app per la logica in qualsiasi momento, andare in **File** > **Salva**. (`Ctrl+S`) 

Se quando si salva l'applicazione l'app per la logica presenta errori, questi vengono visualizzati in Visual Studio nella finestra **Output**.

## <a name="deploy-your-logic-app"></a>Distribuire l'app per la logica

Dopo aver configurato l'app, è possibile distribuirla direttamente da Visual Studio in pochi passaggi. 

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto e scegliere **Distribuisci** > **Nuova distribuzione...**

    ![New deployment](./media/logic-apps-deploy-from-vs/newdeployment.png)

2. Quando verrà richiesto, accedere alla sottoscrizione di Azure. 

3. A questo punto è necessario selezionare i dettagli del gruppo di risorse in cui si desidera distribuire l'app per la logica. Al termine, fare clic su **Distribuisci**.

    > [!NOTE]
    > Assicurarsi di selezionare il modello e i parametri corretti per il gruppo di risorse. Ad esempio, se si desidera distribuire in un ambiente di produzione, scegliere il file dei parametri di produzione.

    ![Deploy to resource group](./media/logic-apps-deploy-from-vs/deploytoresourcegroup.png)

    Lo stato di distribuzione viene visualizzato nella finestra **Output**. 
    Potrebbe essere necessario selezionare **Provisioning Azure** nell'elenco **Mostra output di**.

    ![Output dello stato di distribuzione](./media/logic-apps-deploy-from-vs/output.png)

In futuro sarà possibile modificare l'app per la logica nel controllo del codice sorgente e usare Visual Studio per distribuire nuove versioni.

> [!NOTE]
> Se si modifica la definizione direttamente nel Portale di Azure, tali modifiche saranno sovrascritte alla successiva distribuzione da Visual Studio. 

## <a name="add-your-logic-app-to-an-existing-resource-group-project"></a>Aggiungere un'app per la logica a un progetto Gruppo di risorse esistente

Se si dispone di un progetto Gruppo di risorse esistente, è possibile aggiungere l'app per la logica al progetto nella finestra Struttura JSON. È inoltre possibile aggiungere un'altra app per la logica con l'applicazione creata in precedenza.

1. Aprire il file `<template>.json` .

2. Per aprire la finestra Struttura JSON andare in **Visualizza** > **Altre finestre** > **Struttura JSON**.

3. Per aggiungere una risorsa al file del modello, fare clic su **Aggiungi risorsa** nella parte superiore della finestra Struttura JSON. Oppure nella finestra Struttura JSON fare clic con il tasto destro del mouse su **risorse**e selezionare **Aggiungi nuova risorsa**.

    ![Finestra Struttura JSON](./media/logic-apps-deploy-from-vs/jsonoutline.png)
    
4. Nella finestra di dialogo **Aggiungi risorsa**, individuare e selezionare **App per la logica**. Dare un nome all'app per la logica e scegliere **Aggiungi**.

    ![Aggiungere una risorsa](./media/logic-apps-deploy-from-vs/addresource.png)

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle app per la logica di Azure, vedere l'esercitazione: [Creare la prima app per la logica](logic-apps-create-a-logic-app.md)
* [Visualizzare esempi e scenari comuni](logic-apps-examples-and-scenarios.md)
* [Introduzione all'automazione dei processi aziendali con le app per la logica di Azure](http://channel9.msdn.com/Events/Build/2016/T694)
* [Informazioni su come integrare i sistemi con le app per la logica di Azure](http://channel9.msdn.com/Events/Build/2016/P462)



<!--HONumber=Feb17_HO3-->


