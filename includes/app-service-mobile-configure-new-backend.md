---
title: File di inclusione
description: File di inclusione
services: app-service\mobile
author: conceptdev
ms.service: app-service-mobile
ms.topic: include
ms.date: 05/06/2019
ms.author: crdun
ms.custom: include file
ms.openlocfilehash: a7e543dcad9ad1b016d1244451cd87cda5ad7492
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67440204"
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

     1. Nel portale di Azure passare a **Tabelle semplici**, verrà visualizzata questa schermata.
      
        ![Tabelle facili dei nodi](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. Assicurarsi che la stringa di connessione SQL sia già stata aggiunta nella scheda **Configurazione.** Quindi selezionare la casella di **Riconosco che questo sovrascriverà tutto** il contenuto del sito e fare clic su Crea **TodoItem tabella** pulsante.
     
        ![Configurazione delle tabelle facili dei nodiNode Easy Tables Configuration](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. In **Tabelle facili**, fare clic sul pulsante **Aggiungi.**
    
        ![Pulsante Aggiungi tabelle facili da nodo](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. Creare `TodoItem` una tabella con accesso anonimo.
      
        ![Nodo Easy Tabelle Aggiungi Tabella](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **Back-end .NET**
    
        Se hai intenzione di usare l'app di avvio rapido .NET, segui le istruzioni riportate di seguito.

        1. Scaricare il progetto di server .NET di App per dispositivi mobili di Azure dal [repository azure-mobile-apps-quickstarts](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Compilare il progetto server .NET in locale in Visual Studio.

        3. In Visual Studio aprire Esplora soluzioni, fare clic con il pulsante destro del mouse sul `ZUMOAPPNAMEService` progetto, **scegliere Pubblica**, verrà visualizzata una `Publish to App Service` finestra. Se stai lavorando su Mac, scopri altri modi per distribuire l'app [qui](https://docs.microsoft.com/azure/app-service/deploy-local-git).
        
           ![Pubblicazione di Visual Studio](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Selezionare **Servizio app** come destinazione di pubblicazione, quindi fare clic su **Seleziona esistente**, quindi fare clic sul pulsante **Pubblica** nella parte inferiore della finestra.

        5. È necessario accedere prima a Visual Studio con la sottoscrizione di Azure.You will need to log into Visual Studio with your Azure subscription first. Selezionare `Subscription` `Resource Group`il , , quindi selezionare il nome dell'app. Quando si è pronti, fare clic su **OK**, verrà distribuito il progetto server .NET in locale nel back-end del servizio app. Al termine della distribuzione, si verrà `http://{zumoappname}.azurewebsites.net/` reindirizzati a nel browser.                   