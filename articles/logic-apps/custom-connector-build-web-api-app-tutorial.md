---
title: Creare connettori personalizzati dalle API Web - App per la logica di Azure | Microsoft Docs
description: Sviluppare connettori personalizzati dalle API Web
author: ecfan
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 22bf335718721d29933557142b436e75778f8c86
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-connectors-from-web-apis"></a>Creare connettori personalizzati dalle API Web

Per creare un connettore personalizzato che è possibile usare in App per la logica di Azure, Microsoft Flow o Microsoft PowerApps, creare innanzitutto un'API Web che è possibile ospitare in app Web di Azure, autenticare con Azure Active Directory e registrare come connettore in App per la logica, Flow o PowerApps. In questa esercitazione viene illustrato come eseguire queste attività creando un'app per le API Web ASP.NET. Per modelli che mostrano come è possibile strutturare il codice per i trigger e le azioni del connettore, vedere [Creare API personalizzate che è possibile chiamare da flussi di lavoro delle app per la logica](../logic-apps/logic-apps-create-api-app.md).

## <a name="prerequisites"></a>Prerequisiti

* [Visual Studio 2013 o versione successiva](https://www.visualstudio.com/vs/). Questa esercitazione usa Visual Studio 2015.

* Codice per l'API Web. Se non è disponibile, provare questa esercitazione: [Getting Started with ASP.NET Web API 2 (C#)](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) (Introduzione ad API Web ASP.NET 2 - C#).

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione, è possibile iniziare con un [account Azure gratuito](https://azure.microsoft.com/free/). In alternativa, iscriversi per ottenere una [sottoscrizione con pagamento in base al consumo](https://azure.microsoft.com/pricing/purchase-options/).

## <a name="create-and-deploy-an-aspnet-web-app-to-azure"></a>Creare e distribuire un'app Web ASP.NET in Azure

Per questa esercitazione, creare un'applicazione Web ASP.NET di Visual C#. 

1. Aprire Visual Studio e quindi scegliere **File** > **Nuovo progetto**.

   1. Espandere **Installati**, passare a **Modelli** > **Visual C#** > **Web** e selezionare **Applicazione Web ASP.NET**.

   2. Specificare un nome per il progetto, il percorso e il nome della soluzione per l'app, quindi scegliere **OK**.

   ad esempio:

   ![Creare un'applicazione Web ASP.NET Visual C#](./media/custom-connector-build-web-api-app-tutorial/visual-studio-new-project-aspnet-web-app.png)

2. Nella finestra di dialogo **Nuova applicazione Web ASP.NET** selezionare il modello **API Web**. Se l'opzione non è già selezionata, selezionare **Ospita nel cloud**. Scegliere **Modifica autenticazione**.

   ![Selezionare il modello "API Web", "Ospita cloud", "Modifica autenticazione"](./media/custom-connector-build-web-api-app-tutorial/visual-studio-web-api-template.png)

3. Selezionare **Nessuna autenticazione** e scegliere **OK**. È possibile configurare l'autenticazione in seguito.

   ![Selezionare "Nessuna autenticazione"](./media/custom-connector-build-web-api-app-tutorial/visual-studio-change-authentication.png)

4. Quando viene visualizzata di nuovo la finestra di dialogo **Nuova applicazione Web ASP.NET** scegliere **OK**. 

5. Nella finestra di dialogo **Crea servizio app** controllare le impostazioni di hosting descritte nella tabella, apportare le modifiche desiderate e scegliere **Crea**. 

   Un [piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) rappresenta una raccolta di risorse fisiche usate per l'hosting di app nella sottoscrizione di Azure. Informazioni sul [servizio app](../app-service/app-service-value-prop-what-is.md).

   ![Creare un servizio app](./media/custom-connector-build-web-api-app-tutorial/visual-studio-create-app-service.png)

   | Impostazione | Valore consigliato | Descrizione | 
   | ------- | --------------- | ----------- | 
   | Account aziendale o dell'istituto di istruzione di Azure oppure account Microsoft personale | *account-utente-personale* | Selezionare il proprio account utente. | 
   | **Nome dell'app Web** | *Nome-app-API-Web-personalizzato* o il nome predefinito | Immettere il nome per l'app per le API Web, che viene usato nell'URL dell'app, ad esempio: http://*nome-app-api-web*. | 
   | **Sottoscrizione** | *nome-sottoscrizione-Azure* | Selezionare la sottoscrizione di Azure da usare. | 
   | **Gruppo di risorse** | *nome-gruppo-risorse-Azure* | Selezionare un gruppo di risorse di Azure esistente o crearne uno nuovo. <p>**Nota**: un gruppo di risorse di Azure consente di organizzare le risorse di Azure nella sottoscrizione di Azure. | 
   | **Piano di servizio app** | *Nome-piano-servizio-app* | Selezionare un piano di servizio app esistente oppure crearne uno nuovo. | 
   |||| 

   Se si crea un piano di servizio app, è possibile specificare queste impostazioni:

   | Impostazione | Valore consigliato | Descrizione | 
   | ------- | --------------- | ----------- | 
   | **Posizione** | *area-distribuzione* | Selezionare l'area per la distribuzione dell'app. | 
   | **Dimensione** | *Dimensione-piano-servizio-app* | Selezionare le dimensioni del piano, che determinano il costo e la capacità di risorse di calcolo per il piano di servizio. | 
   |||| 

   Per configurare eventuali altre risorse necessarie per l'app, scegliere **Esplora altri servizi di Azure**.

   | Impostazione | Valore consigliato | Descrizione | 
   | ------- | --------------- | ----------- | 
   | **Tipo di risorsa** | *Tipo-risorsa-Azure* | Selezionare e configurare eventuali risorse aggiuntive necessarie per l'app. | 
   |||| 

6. Dopo la distribuzione del progetto con Visual Studio, compilare il codice per l'app.

## <a name="create-an-openapi-swagger-file-that-describes-your-web-api"></a>Creare un file OpenAPI (Swagger) che descrive l'API Web

Per connettere l'app per le API Web ad App per la logica, è necessario un [file OpenAPI (in precedenza Swagger)](http://swagger.io/) che descrive le operazioni dell'API. È possibile scrivere la propria definizione OpenAPI dell'API con l'[editor online Swagger](http://editor.swagger.io/), ma in questa esercitazione viene usato uno strumento open source denominato [Swashbuckle](https://github.com/domaindrivendev/Swashbuckle/blob/master/README.md).

1. Se non è già stato fatto, installare il pacchetto NuGet Swashbuckle nel progetto di Visual Studio.

   1. In Visual Studio scegliere **Strumenti** > **Gestione pacchetti NuGet** > 
    **Console di Gestione pacchetti**.

   2. In **Console di Gestione pacchetti** passare alla directory del progetto dell'app se non è già attiva (eseguire `Set-Location "project-path"`) ed eseguire il cmdlet di PowerShell seguente: 
   
      `Install-Package Swashbuckle`

      ad esempio:

      ![Console di Gestione pacchetti, installazione di Swashbuckle](./media/custom-connector-build-web-api-app-tutorial/visual-studio-package-manager-install-swashbuckle.png)

   > [!TIP]
   > Se si esegue l'app dopo aver installato Swashbuckle, Swashbuckle genera un file OpenAPI a questo indirizzo: 
   >
   > http://*{URL-radice-app-api-web}*/swagger/docs/v1
   > 
   > Swashbuckle genera anche un'interfaccia utente all'indirizzo seguente: 
   > 
   > http://*{URL-radice-app-api-web}*/swagger

3. Quando si è pronti, pubblicare l'app per le API Web in Azure. Per eseguire la pubblicazione da Visual Studio, fare clic con il pulsante destro del mouse sul progetto Web in Esplora soluzioni, scegliere **Pubblica** e seguire le istruzioni.

   > [!IMPORTANT]
   > La presenza di ID di operazione duplicati invalida un documento OpenAPI. Se è stato usato lo stesso modello C# di esempio, il modello *ripete due volte l'ID operazione*: `Values_Get` 
   > 
   > Per risolvere questo problema, sostituire una delle istanze con `Value_Get` e pubblicare di nuovo.

4. Per ottenere il documento OpenAPI, passare all'indirizzo seguente: 

   http://*{URL-radice-app-api-web}*/swagger/docs/v1

   È anche possibile scaricare un [documento di esempio OpenAPI](https://pwrappssamples.blob.core.windows.net/samples/webAPI.json) da questa esercitazione. 
   Assicurarsi di rimuovere i commenti, che iniziano con "//", prima di usare il documento.

5. Salvare il contenuto come file JSON. A seconda del browser, potrebbe essere necessario copiare e incollare il testo in un file di testo vuoto.

## <a name="next-steps"></a>Passaggi successivi

* [Configurare l'autenticazione per i connettori personalizzati](../logic-apps/custom-connector-azure-active-directory-authentication.md)











