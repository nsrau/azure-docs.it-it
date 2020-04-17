---
title: File di inclusione
description: File di inclusione
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 04/15/2020
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: b5b6389491fd91b716a05e7c57c98addb6352bd9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461931"
---
1. Scaricare le guide introduttive dell'SDK client per le piattaforme seguenti:
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C)Windows (C'è)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Se si utilizza il progetto iOS è necessario scaricare\*"azuresdk-iOS- .zip" dalla [versione più recente di GitHub.](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest) Decomprimere e `MicrosoftAzureMobile.framework` aggiungere il file alla radice del progetto.
    >

2. Sarà necessario aggiungere una connessione al database o connettersi a una connessione esistente. Determinare innanzitutto se si creerà un archivio dati o se ne verrà utilizzato uno esistente.

    - **Creare un nuovo archivio dati:** se si intende creare un archivio dati, usare la guida introduttiva seguente:Create a new data store : If you're to create a data store, use the following quickstart:

        [Guida introduttiva: Introduzione a singoli database nel database SQL di AzureQuickstart: Getting started with single databases in Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Origine dati esistente**: seguire le istruzioni riportate di seguito se si desidera utilizzare una connessione di database esistente

        1. Formato stringa di connessione al database SQL -`Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **your_SQLServer dollari di lavoro.** Nome del server, questo può essere trovato nella pagina di panoramica per il database ed è di solito sotto forma di "server_name.database.windows.net".
            **di** solito 1433.
            **your_catalogue dollari di lavoro.** Nome del database.
            **your_username, your_username** Nome utente per accedere al database.
            **your_password, your_password** Password per accedere al database.

            [Altre informazioni sul formato della stringa di connessione SQLLearn more about SQL Connection String format](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Aggiungere la stringa di connessione **all'app per dispositivi mobili** nel servizio app, è possibile gestire le stringhe di connessione per l'applicazione usando l'opzione **Configurazione** nel menu.

            Per aggiungere una stringa di connessione:To add a connection string:

            1. Fare clic sulla scheda **Impostazioni applicazione.**

            2. Fare clic su **[-] Nuova stringa**di connessione .

            3. È necessario specificare **Name**, **Value** e **Type** per la stringa di connessione.

            4. **Nome** tipo come`MS_TableConnectionString`

            5. Il valore deve essere la stringa di connessione formata nel passaggio precedente.

            6. Se si aggiunge una stringa di connessione a un database SQL Azure, scegliere **SQLAzure** in **tipo**.

3. App per dispositivi mobili di Azure include SDK per i back-end .NET e Node.js.Azure Mobile Apps has SDKs for .NET and Node.js backends.

   - **Back-end Node.js**
    
     Se hai intenzione di usare Node.js quickstart app, seguire le istruzioni riportate di seguito.
     
        1. Creare una nuova API: è possibile apportare modifiche direttamente nel portale di Azure o modificare il codice in locale nell'ambiente di sviluppo e quindi pubblicare in Azure.Create a new API - You can either make changes right in the Azure portal or modify the code locally in your development environment and then publish to Azure. Fai clic `App Service Editor (Preview)` `Development Tools` sul menu in cui offre un'esperienza di modifica nel browser per il codice dell'app.
        
        2. Fare `Go` clic su e una volta che si apre l'Editor del servizio app, si ha il controllo completo sul codice sorgente. Supponendo che sia già stato installato il pacchetto express e azure-mobile-apps con il comando di installazione npm, fare clic sulla cartella api in WWWROOT per creare o modificare l'API personalizzata. Apportare le modifiche al file di codice e le modifiche vengono salvate automaticamente.
        
        3. Si dispone del controllo completo sul database SQL di Azure usato per archiviare i dati dell'applicazione. È possibile creare facilmente nuove tabelle nel database.
 
   - **Back-end .NET**
    
        Se hai intenzione di usare l'app di avvio rapido .NET, segui le istruzioni riportate di seguito.

        1. Scaricare il progetto di server .NET di App per dispositivi mobili di Azure dal [repository azure-mobile-apps-quickstarts](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Compilare il progetto server .NET in locale in Visual Studio.

        3. In Visual Studio aprire Esplora soluzioni, fare clic con il pulsante destro del mouse sul `ZUMOAPPNAMEService` progetto, **scegliere Pubblica**, verrà visualizzata una `Publish to App Service` finestra. Se stai lavorando su Mac, scopri altri modi per distribuire l'app [qui](https://docs.microsoft.com/azure/app-service/deploy-local-git).
        
           ![Pubblicazione di Visual Studio](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Selezionare **Servizio app** come destinazione di pubblicazione, quindi fare clic su **Seleziona esistente**, quindi fare clic sul pulsante **Pubblica** nella parte inferiore della finestra.

        5. È necessario accedere prima a Visual Studio con la sottoscrizione di Azure.You will need to log into Visual Studio with your Azure subscription first. Selezionare `Subscription` `Resource Group`il , , quindi selezionare il nome dell'app. Quando si è pronti, fare clic su **OK**, verrà distribuito il progetto server .NET in locale nel back-end del servizio app. Al termine della distribuzione, si verrà `http://{zumoappname}.azurewebsites.net/` reindirizzati a nel browser.                   
