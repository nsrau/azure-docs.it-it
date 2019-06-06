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
ms.openlocfilehash: a7c994f85d90e94d514bb4e4f91a6644ed45432c
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66455155"
---
1. Scaricare il client SDK guide introduttive per le piattaforme seguenti:
    
    [iOS (Objective-C)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS)  
    [iOS (Swift)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/iOS-Swift)  
    [Android (Java)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/android)  
    [Xamarin.iOS](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.iOS)  
    [Xamarin.Android](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.android)  
    [Xamarin.Forms](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/xamarin.forms)  
    [Cordova](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/cordova)  
    [Windows (C#)](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/client/windows-uwp-cs)  

    > [!NOTE]
    > Se si usa il progetto iOS è necessario scaricare "azuresdk-iOS -\*zip" dalla [ultima versione di GitHub](https://github.com/Azure/azure-mobile-apps-ios-client/releases/latest). Decomprimere e aggiungere il `MicrosoftAzureMobile.framework` file alla radice del progetto.
    >

2. È necessario aggiungere una connessione al database o connettersi a una connessione esistente. In primo luogo, determinare se verranno creare un archivio dati o usarne uno esistente.

    - **Creare un nuovo archivio dati**: Se sta creando un archivio dati, usare la Guida introduttiva seguente:

        [Avvio rapido: Introduzione a database singoli nel Database SQL di Azure](https://docs.microsoft.com/azure/sql-database/sql-database-single-database-quickstart-guide)

    - **Origine dati esistente**: Seguire le istruzioni seguenti, se si desidera utilizzare una connessione di database esistente

        1. Formato di stringa di connessione del Database SQL: `Data Source=tcp:{your_SQLServer},{port};Initial Catalog={your_catalogue};User ID={your_username};Password={your_password}`

           **{your_SQLServer}**  Nome del server, questo è reperibile nella pagina di panoramica per il database e viene in genere sotto forma di "server_name.database.windows.net".
            **{port}**  in genere 1433.
            **{your_catalogue}**  Nome del database.
            **{your_username}**  Nome utente per accedere al database.
            **{your_password}**  Password per accedere al database.

            [Altre informazioni sul formato di stringa di connessione SQL](https://docs.microsoft.com/dotnet/framework/data/adonet/connection-string-syntax#sqlclient-connection-strings)

        2. Aggiungere la stringa di connessione per il **app per dispositivi mobili** nel servizio App, è possibile gestire le stringhe di connessione per l'applicazione con il **configurazione** opzione del menu.

            Per aggiungere una stringa di connessione:

            1. Fare clic sui **le impostazioni dell'applicazione** scheda.

            2. Fare clic su **[+] nuova stringa di connessione**.

            3. Si dovrà fornire **Name**, **valore** e **tipo** della stringa di connessione.

            4. Tipo di **nome** come `MS_TableConnectionString`

            5. Valore deve essere la stringa di connessione che è formata nel passaggio prima.

            6. Se si aggiunge una stringa di connessione a un database di SQL Azure, scegliere **SQLAzure** sotto **tipo**.

3. Le App per dispositivi mobili di Azure con gli SDK per back-end .NET e Node. js.

   - **Back-end Node.js**
    
     Se si intende usare l'app di Guida introduttiva di Node. js, seguire le istruzioni seguenti.

     1. Nel portale di Azure, passare a **Easy Tables**, verrà visualizzata questa schermata.
      
        ![Nodo tabelle semplici](./media/app-service-mobile-configure-new-backend/node-easy-tables.png)

     2. Assicurarsi che la stringa di connessione SQL è già stato aggiunto nel **configurazione** scheda. Quindi selezionare la casella di **sono consapevole che questa operazione sovrascrive tutto il contenuto del sito** e fare clic sui **Crea tabella TodoItem** pulsante.
     
        ![Configurazione del nodo tabelle semplici](./media/app-service-mobile-configure-new-backend/node-easy-tables-configuration.png)

     3. Nelle **Easy Tables**, fare clic sui **+ Aggiungi** pulsante.
    
        ![Pulsante Aggiungi nodo tabelle semplici](./media/app-service-mobile-configure-new-backend/node-easy-tables-add.png)

     4. Creare un `TodoItem` tabella con l'accesso anonimo.
      
        ![Nodo Easy Tables Aggiungi tabella](./media/app-service-mobile-configure-new-backend/node-easy-tables-table-add.png)

   - **Back-end .NET**
    
        Se si intende usare l'app di Guida introduttiva di .NET, seguire le istruzioni seguenti.

        1. Scaricare il progetto server .NET di App per dispositivi mobili di Azure i [repository azure-mobile-apps-guide introduttive](https://github.com/Azure/azure-mobile-apps-quickstarts/tree/master/backend/dotnet/Quickstart).

        2. Compilare il progetto server .NET localmente in Visual Studio.

        3. In Visual Studio, aprire Esplora soluzioni, fare clic su `ZUMOAPPNAMEService` del progetto, fare clic su **Publish**, verrà visualizzato un `Publish to App Service` finestra. Se si lavora su Mac, consultare altri modi per distribuire l'app [qui](https://docs.microsoft.com/azure/app-service/deploy-local-git).
        
           ![Pubblicazione di Visual studio](./media/app-service-mobile-configure-new-backend/visual-studio-publish.png)

        4. Selezionare **servizio App** come destinazione di pubblicazione, quindi fare clic su **seleziona esistente**, quindi fare clic sui **Publish** nella parte inferiore della finestra.

        5. È necessario prima ad accedere a Visual Studio con la sottoscrizione di Azure. Selezionare il `Subscription`, `Resource Group`e quindi selezionare il nome dell'app. Quando si è pronti, fare clic su **OK**, questo verrà distribuito il progetto server .NET che hai in locale nel back-end di servizio App. Al termine della distribuzione, si verrà reindirizzati a `http://{zumoappname}.azurewebsites.net/` nel browser.
        
           ![Back-end sia attivo](./media/app-service-mobile-configure-new-backend/backend-is-up.png)
