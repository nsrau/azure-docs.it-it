<properties 
	pageTitle="Compilare app per la logica in Visual Studio | Microsoft Azure" 
	description="Creare un progetto in Visual Studio per creare e distribuire un'app per la logica." 
	authors="jeffhollan" 
	manager="erikre" 
	editor="" 
	services="logic-apps" 
	documentationCenter=""/>

<tags
	ms.service="logic-apps"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/26/2016"
	ms.author="jehollan"/>
	
# Compilare e distribuire app per la logica in Visual Studio

Nonostante il [portale di Azure](https://portal.azure.com/) offra un'ottima soluzione per progettare e gestire le app per la logica, è anche possibile progettare e distribuire un'app per la logica da Visual Studio. Per le app per la logica è disponibile un set completo di strumenti di Visual Studio che consente di compilare un'app per la logica con la finestra di progettazione, configurare qualsiasi modello di distribuzione e di automazione ed eseguire la distribuzione in qualsiasi ambiente.

## Procedura di installazione

Di seguito sono riportati i passaggi per installare e configurare gli strumenti di Visual Studio per le app per la logica.

### Prerequisiti

- [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- [Versione più recente di Azure SDK](https://azure.microsoft.com/downloads/) (2.9.1 o versione successiva)
- [Azure PowerShell](https://github.com/Azure/azure-powershell#installation)
- Accesso al Web mentre viene usata la finestra di progettazione incorporata

### Installare gli strumenti di Visual Studio per le app per la logica

Dopo aver installato i prerequisiti:

1. Aprire Visual Studio 2015 e quindi scegliere **Estensioni e aggiornamenti** nel menu **Strumenti**
1. Selezionare la categoria **Online** per eseguire la ricerca online
1. Cercare **App per la logica** per visualizzare **Azure Logic Apps Tools for Visual Studio** (Strumenti per app per la logica di Azure per Visual Studio)
1. Fare clic sul pulsante **Scarica** per scaricare e installare l'estensione
1. Al termine dell'installazione riavviare Visual Studio

> [AZURE.NOTE] È anche possibile scaricare l'estensione direttamente da [questo collegamento](https://visualstudiogallery.msdn.microsoft.com/e25ad307-46cf-412e-8ba5-5b555d53d2d9)

Dopo l'installazione sarà possibile usare il progetto Gruppo di risorse di Azure con la finestra di progettazione delle app per la logica.

## Creare un progetto

1. Scegliere **Nuovo** > **Progetto** dal menu **File**. In alternativa, è possibile selezionare **Aggiungi** e quindi **Nuovo progetto** per aggiungerlo a una soluzione esistente: ![File menu](./media/app-service-logic-deploy-from-vs/filemenu.png)

1. Nella finestra di dialogo selezionare **Cloud** e quindi **Gruppo di risorse di Azure**. Digitare un **Nome** e fare clic su **OK**. ![Add new project](./media/app-service-logic-deploy-from-vs/addnewproject.png)

1. Selezionare il modello **App per la logica**. Verrà creato un modello di distribuzione di un'app per la logica vuoto come punto di partenza. ![Select Azure template](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)

1. Dopo avere selezionato il **modello**, fare clic su **OK**.

	Il progetto di app per la logica viene aggiunto alla soluzione. Il file di distribuzione verrà visualizzato in Esplora soluzioni:

	![Distribuzione](./media/app-service-logic-deploy-from-vs/deployment.png)

## Uso della finestra di progettazione delle app per la logica

Dopo aver creato un progetto Gruppo di risorse di Azure contenente un'app per la logica, è possibile aprire la finestra di progettazione in Visual Studio per creare il flusso di lavoro. La finestra di progettazione richiede una connessione Internet per eseguire query sui connettori per ottenere le proprietà e i dati disponibili. Se si usa il connettore Dynamics CRM Online, ad esempio, la finestra di progettazione eseguirà una query sull'istanza di CRM per ottenere un elenco delle proprietà predefinite e personalizzate.

1. Fare clic con il pulsante destro del mouse sul file `<template>.json` e scegliere **Open with Logic App Designer** (Apri con finestra di progettazione app per la logica) (o `Ctrl+L`)
1. Scegliere la sottoscrizione, il gruppo di risorse e il percorso per il modello di distribuzione
	- È importante notare che progettando un'app per la logica verranno create risorse **Connessione API** per l'esecuzione di query per ottenere le proprietà durante la progettazione. Il gruppo di risorse selezionato sarà il gruppo usato per creare tali connessioni durante la fase di progettazione. È possibile visualizzare o modificare qualsiasi connessione API passando al portale di Azure e cercando **Connessioni API**. ![Selezione della sottoscrizione](./media/app-service-logic-deploy-from-vs/designer_picker.png)
1. La finestra di progettazione eseguirà il rendering in base alla definizione contenuta nel file `<template>.json`.
1. È ora possibile creare e progettare l'app per la logica. Il modello di distribuzione verrà aggiornato con le modifiche. ![Finestra di progettazione in Visual Studio](./media/app-service-logic-deploy-from-vs/designer_in_vs.png)

Verranno anche aggiunte risorse `Microsoft.Web/connections` al file di risorse per tutte le connessioni necessarie per il funzionamento dell'app per la logica. Queste proprietà di connessione possono essere impostate al momento della distribuzione ed essere gestite successivamente in **Connessioni API** nel portale di Azure.

### Passaggio alla visualizzazione del codice JSON

È possibile selezionare la scheda **Visualizzazione Codice** nella parte inferiore della finestra di progettazione per passare alla rappresentazione JSON dell'app per la logica. Per tornare al file di risorse JSON completo, fare clic con il pulsante destro del mouse su `<template>.json` e scegliere **Apri**.

### Salvataggio dell'app per la logica

È possibile salvare l'app per la logica in qualsiasi momento con il pulsante **Salva** o con `Ctrl+S`. Se al momento del salvataggio sono presenti errori nell'app per la logica, questi verranno visualizzati nella finestra **Output** di Visual Studio.

## Distribuzione dell'app per la logica

Infine, dopo aver configurato l'app è possibile distribuirla direttamente da Visual Studio in pochi passaggi.

1. Fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni e passare a **Distribuisci** > **Nuova distribuzione**. ![New deployment](./media/app-service-logic-deploy-from-vs/newdeployment.png)

2. Verrà richiesto di accedere alla sottoscrizione di Azure.

3. A questo punto è necessario scegliere i dettagli del gruppo di risorse in cui si vuole distribuire l'app per la logica. ![Deploy to resource group](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)

     > [AZURE.NOTE]    Assicurarsi di selezionare il modello e i file dei parametri appropriati per il gruppo di risorse (ad esempio, se si effettua la distribuzione in un ambiente di produzione è opportuno scegliere il file dei parametri di produzione).
4. Selezionare il pulsante Distribuisci
 
    
6. Lo stato della distribuzione viene visualizzato nella finestra **Output**. Potrebbe essere necessario scegliere **Provisioning Azure**. ![Output](./media/app-service-logic-deploy-from-vs/output.png)

In futuro sarà possibile modificare l'app per la logica nel controllo del codice sorgente e usare Visual Studio per distribuire nuove versioni.

> [AZURE.NOTE] Se si modifica la definizione direttamente nel portale di Azure, alla successiva distribuzione da Visual Studio tali modifiche verranno sovrascritte.

## Passaggi successivi

- Per iniziare a usare le app per la logica, seguire l'esercitazione [Creare una nuova app per la logica](app-service-logic-create-a-logic-app.md).
- [Visualizzare esempi e scenari comuni](app-service-logic-examples-and-scenarios.md)
- [Le app per la logica consentono di automatizzare i processi aziendali](http://channel9.msdn.com/Events/Build/2016/T694)
- [Informazioni su come integrare i sistemi correnti con le app per la logica](http://channel9.msdn.com/Events/Build/2016/P462)

<!---HONumber=AcomDC_0928_2016-->