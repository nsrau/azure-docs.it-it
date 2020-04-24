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
1. Scaricare le guide introduttive per l'SDK client per le piattaforme seguenti:
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (SWIFT)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Se si usa il progetto iOS, è necessario scaricare "azuresdk-iOS-\*. zip" dalla [versione più recente di GitHub](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest). Decomprimere e `MicrosoftAzureMobile.framework` aggiungere il file alla radice del progetto.
    >

2. Sarà necessario aggiungere una connessione di database o connettersi a una connessione esistente. Per prima cosa, determinare se creare un archivio dati o utilizzarne uno esistente.

    - **Creare un nuovo archivio dati**: se si intende creare un archivio dati, usare la Guida introduttiva seguente:

        [Guida introduttiva: Introduzione ai database singoli nel database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Origine dati esistente**: se si vuole usare una connessione di database esistente, seguire le istruzioni riportate di seguito.

        1. Formato della stringa di connessione del database SQL-`Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}** Nome del server, disponibile nella pagina Panoramica del database e in genere in formato "server_name. database. Windows. NET".
            **{Port}** in genere 1433.
            **{your_catalogue}** Nome del database.
            **{your_username}** Nome utente per accedere al database.
            **{your_password}** Password per accedere al database.

            [Altre informazioni sul formato della stringa di connessione SQL](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Aggiungere la stringa di connessione all' **app per dispositivi mobili** nel servizio app, è possibile gestire le stringhe di connessione per l'applicazione usando l'opzione di **configurazione** nel menu.

            Per aggiungere una stringa di connessione:

            1. Fare clic sulla scheda **Impostazioni applicazione** .

            2. Fare clic su **[+] nuova stringa di connessione**.

            3. Sarà necessario specificare il **nome**, il **valore** e il **tipo** per la stringa di connessione.

            4. Digitare il **nome**`MS_TableConnectionString`

            5. Il valore deve corrispondere alla stringa di connessione creata nel passaggio precedente.

            6. Se si aggiunge una stringa di connessione a un database SQL Azure scegliere **sqlazure** in **tipo**.

3. App per dispositivi mobili di Azure include SDK per i backend .NET e node. js.

   - **Back-end Node.js**
    
     Se si intende usare l'app di avvio rapido node. js, seguire le istruzioni riportate di seguito.
     
        1. Creare una nuova API: è possibile apportare modifiche direttamente all'portale di Azure o modificare il codice localmente nell'ambiente di sviluppo e quindi eseguire la pubblicazione in Azure. Fare clic sul `App Service Editor (Preview)` menu `Development Tools` sotto che fornisce un'esperienza di modifica nel browser per il codice dell'app.
        
        2. Fare clic `Go` su on e una volta aperto il editor del servizio app si ha il controllo completo sul codice sorgente. Supponendo che sia già stato installato il pacchetto Express e Azure-Mobile-Apps con il comando NPM install, fare clic sulla cartella API in WWWROOT per creare o modificare un'API personalizzata. Apportare le modifiche al file di codice e le modifiche vengono salvate automaticamente.
        
        3. Si ha il controllo completo sul database SQL di Azure usato per archiviare i dati dell'applicazione. È possibile creare facilmente nuove tabelle nel database.
 
   - **Back-end .NET**
    
        Se si intende usare l'app di avvio rapido .NET, seguire le istruzioni riportate di seguito.

        1. Scaricare il progetto server .NET di app per dispositivi mobili di Azure dal [repository Azure-Mobile-Apps-QuickStarts](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Compilare il progetto server .NET in locale in Visual Studio.

        3. In Visual Studio aprire Esplora soluzioni, fare clic con il pulsante `ZUMOAPPNAMEService` destro del mouse su progetto, fare clic su `Publish to App Service` **pubblica**. viene visualizzata una finestra. Se si lavora su Mac, vedere altri modi per distribuire l'app [qui](https://docs.microsoft.com/azure/app-service/deploy-local-git).
        
           ![Pubblicazione di Visual Studio](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Selezionare **servizio app** come destinazione di pubblicazione, fare clic su **Seleziona esistente**, quindi fare clic sul pulsante **pubblica** nella parte inferiore della finestra.

        5. Sarà prima di tutto necessario accedere a Visual Studio con la sottoscrizione di Azure. Selezionare `Subscription`, `Resource Group`, quindi selezionare il nome dell'app. Quando si è pronti, fare clic su **OK**. verrà distribuito il progetto server .NET disponibile localmente nel back-end del servizio app. Al termine della distribuzione, si verrà reindirizzati a `http://{zumoappname}.azurewebsites.net/` nel browser.                   
