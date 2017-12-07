---
title: Come eseguire la migrazione e la pubblicazione di un'applicazione Web in un servizio cloud di Azure da Visual Studio | Documentazione Microsoft
description: Informazioni su come eseguire la migrazione e la pubblicazione di un'applicazione Web in un servizio cloud di Azure da Visual Studio
services: visual-studio-online
documentationcenter: na
author: kraigb
manager: ghogen
editor: 
ms.assetid: 9394adfd-a645-4664-9354-dd5df08e8c91
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 11/10/2017
ms.author: kraigb
ms.openlocfilehash: d5d41ab47c17a024900efc88ba0a006da63ab246
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-migrate-and-publish-a-web-application-to-an-azure-cloud-service-from-visual-studio"></a>Procedura: Eseguire la migrazione e la pubblicazione di un'applicazione Web in un servizio cloud di Azure da Visual Studio

Per sfruttare i servizi di hosting e scalabilità di Azure, è possibile eseguire la migrazione e la distribuzione dell'applicazione Web in un servizio cloud di Azure. Sono necessarie solo modifiche minime al codice. Questo articolo illustra solo la procedura di distribuzione in un servizio cloud; per il Servizio app, vedere [Distribuire un'app Web nel Servizio app di Azure](app-service/app-service-deploy-local-git.md).

> [!Important]
> Questa migrazione è supportata solo per specifici progetti ASP.NET, Silverlight, WCF e di flusso di lavoro WCF. Non è supportata per progetti ASP.NET Core. Vedere [Modelli di progetto supportati](#supported-project-templates).

## <a name="migrate-a-project-to-cloud-services"></a>Migrare un progetto in un servizio cloud

1. Fare clic con il pulsante destro del mouse sul progetto di applicazione Web e selezionare **Converti > Converti in progetto servizio cloud di Microsoft Azure**. Questo comando non viene visualizzato se nella soluzione è già presente un progetto di ruolo Web.
1. Visual Studio crea un progetto di servizio cloud nella soluzione contente il ruolo Web richiesto. Il nome del progetto corrisponde al nome del progetto di applicazione, con l'aggiunta del suffisso `.Azure`.
1. Visual Studio imposta anche la proprietà **Copia localmente** su true per qualsiasi assembly necessario per MVC 2, MVC 3, MVC 4 e applicazioni aziendali di Silverlight. In questo modo gli assembly vengono aggiunti al pacchetto del servizio usato per la distribuzione.

   > [!Important]
   > Se si dispone di altri assembly o file necessari per l'applicazione Web, è necessario impostare manualmente le proprietà di questi file. Per informazioni su come impostare queste proprietà, vedere [Includere file nel pacchetto del servizio](#include-files-in-the-service-package).

### <a name="errors-and-warnings"></a>Errori e avvisi

Eventuali avvisi o errori visualizzati indicano problemi da risolvere prima di eseguire la distribuzione in Azure, ad esempio assembly mancanti.

Se si compila l'applicazione, la si esegue in locale con l'emulatore di calcolo o la si pubblica in Azure, è possibile che venga visualizzato l'errore "Il percorso e/o il nome file specificato è troppo lungo". Questo errore indica che la lunghezza del nome completo del progetto di Azure è superiore a 146 caratteri. Per correggere il problema, spostare la soluzione in una cartella diversa con un percorso più breve.

Per altre informazioni su come gestire eventuali avvisi come errori, vedere [Configurare un progetto di servizio cloud di Azure con Visual Studio](vs-azure-tools-configuring-an-azure-project.md).

### <a name="test-the-migration-locally"></a>Testare la migrazione in locale

1. In **Esplora soluzioni** di Visual Studio fare clic con il pulsante destro del mouse sul progetto di servizio cloud aggiunto e selezionare **Imposta come progetto di avvio**.
1. Selezionare **Debug > Avvia debug** (F5) per avviare l'ambiente di debug di Azure. Questo ambiente consente, nello specifico, l'emulazione di vari servizi di Azure.

### <a name="use-an-azure-sql-database-for-your-application"></a>Usare un database SQL di Azure per l'applicazione

Se si ha una stringa di connessione per l'applicazione Web che usa un database SQL Server locale, è necessario eseguire la migrazione del database al database SQL di Azure e aggiornare la stringa di connessione. Per istruzioni su questo processo, vedere gli argomenti seguenti:

- [Migrazione di un database SQL Server al database SQL nel cloud](sql-database/sql-database-cloud-migrate.md)
- [Usare .NET (C#) con Visual Studio Code per connettersi a un database SQL ed eseguire query](sql-database/sql-database-connect-query-dotnet-visual-studio.md).

## <a name="publish-the-application-to-azure-cloud-service"></a>Pubblicare l'applicazione in un servizio cloud di Azure

1. Creare gli account di archiviazione e dei servizi cloud necessari nella sottoscrizione di Azure, come descritto in [Preparare la pubblicazione o la distribuzione di un'applicazione di Azure da Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md).
1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto di applicazione e selezionare **Pubblica in Microsoft Azure...** (diverso dal comando "Pubblica...").
1. Nella finestra **Pubblica applicazione Azure** visualizzata, accedere usando l'account con la sottoscrizione di Azure e scegliere **Avanti >**.
1. Nella scheda **Impostazioni > Impostazioni comuni** selezionare il servizio cloud di destinazione dall'elenco a discesa **Servizio cloud**, insieme all'ambiente e alle configurazioni selezionate. 
1. In **Impostazioni > Impostazioni avanzate** selezionare l'account di archiviazione da usare e quindi scegliere **Avanti >**.
1. In **Diagnostica** decidere se si vuole inviare informazioni ad Application Insights.
1. Selezionare **Avanti >** per visualizzare un riepilogo e quindi selezionare **Pubblica** per avviare la distribuzione.
1. Visual Studio apre una finestra Log attività in cui è possibile monitorare lo stato di avanzamento del processo:

    ![VST_AzureActivityLog](./media/vs-azure-tools-migrate-publish-web-app-to-cloud-service/IC744149.png)

1. (Facoltativo) Per annullare il processo di distribuzione, fare clic con il pulsante destro del mouse sulla voce del log attività e scegliere **Annulla e rimuovi**. Questo comando arresta il processo di distribuzione ed elimina l'ambiente di distribuzione da Azure. Nota: per rimuovere questo ambiente di distribuzione dopo che è stato distribuito, è necessario usare il [portale di Azure](https://portal.azure.com).
1. (Facoltativo) Dopo l'avvio delle istanze del ruolo, Visual Studio mostra automaticamente l'ambiente di distribuzione nel nodo **Esplora server > Servizi cloud**. Da qui è possibile visualizzare lo stato delle singole istanze del ruolo.
1. Per accedere all'applicazione dopo la distribuzione, scegliere la freccia accanto alla distribuzione quando nel **Log attività di Azure** viene visualizzato lo stato **Completato** insieme all'URL. Vedere la tabella seguente per informazioni dettagliate su come avviare un tipo specifico di applicazione Web da Azure.

## <a name="using-the-compute-emulator-and-starting-application-in-azure"></a>Utilizzo dell'emulatore di calcolo e avvio dell'applicazione in Azure

Tutti i tipi di applicazione possono essere avviati in un browser connesso al debugger di Visual Studio selezionando **Debug > Avvia debug** (F5). Con un progetto di applicazione Web ASP.NET vuota, è necessario prima aggiungere una pagina `.aspx` nell'applicazione e impostarla come pagina iniziale del progetto Web.

La tabella seguente fornisce informazioni dettagliate sull'avvio dell'applicazione in Azure:

   | Tipo di applicazione Web | Esecuzione in Azure |
   | --- | --- | --- |
   | Applicazione Web ASP.NET<br/>(inclusi MVC 2, MVC 3, MVC 4) | Nella scheda **Distribuzione** selezionare l'URL per **Log attività di Azure**. |
   | Applicazione Web ASP.NET vuota | Se si ha una pagina `.aspx` predefinita nell'applicazione, nella scheda **Distribuzione** selezionare l'URL per **Log attività di Azure**. Per passare a una pagina diversa, in un browser immettere un URL nel formato seguente: `<deployment_url>/<page_name>.aspx` |
   | Applicazione Silverlight<br/>Applicazione aziendale di Silverlight<br/>Applicazione di navigazione Silverlight | Passare alla pagina specifica dell'applicazione usando il formato di URL seguente: `<deployment_url>/<page_name>.aspx` |
    Applicazione di servizio WCF<br/>Applicazione di servizio del flusso di lavoro WCF | Impostare il file `.svc` come pagina iniziale del progetto di servizio WCF. Passare a `<deployment_url>/<service_file>.svc` |
   | Entità dinamiche ASP.NET<br/>Linq ASP.NET Dynamic Data a SQL | Aggiornare la stringa di connessione, come descritto nella sezione seguente. Passare quindi a `<deployment_url>/<page_name>.aspx`. Per Linq to SQL è necessario usare un database SQL di Azure. |

## <a name="update-a-connection-string-for-aspnet-dynamic-entities"></a>Aggiornamento di una stringa di connessione per entità dinamiche ASP.NET

1. Creare un database SQL di Azure per un'applicazione Web di entità dinamiche ASP.NET, come descritto in precedenza in (#use-an-azuresql-database-for-your-application).
1. Aggiungere le tabelle e i campi necessari per il database dal portale di Azure.
1. Specificare una stringa di connessione nel file `web.config` con il formato seguente e salvare il file:

    ```xml
    <addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;data source=<server name>\SQLEXPRESS;initial catalog=<database name>;integrated security=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

    Aggiornare il valore *connectionString* con la stringa di connessione ADO.NET per il database SQL Azure, come indicato di seguito:

    ```xml
    XMLCopy<addname="tempdbEntities"connectionString="metadata=res://*/Model1.csdl|res://*/Model1.ssdl|res://*/Model1.msl;provider=System.Data.SqlClient;provider connection string=&quot;Server=tcp:<SQL Azure server name>.database.windows.net,1433;Database=<database name>;User ID=<user name>;Password=<password>;Trusted_Connection=False;Encrypt=True;multipleactiveresultsets=True;App=EntityFramework&quot;"providerName="System.Data.EntityClient"/>
    ```

## <a name="supported-project-templates"></a>Modelli di progetto supportati

Le applicazioni che possono essere migrate e pubblicate in servizi cloud devono usare uno dei modelli presenti nella tabella seguente. ASP.NET Core non è supportato.

| Gruppo di modelli | Modello di progetto |
| --- | --- |
| Web | Applicazione Web ASP.NET (.NET Framework) |
| Web | Applicazione Web ASP.NET MVC 2 |
| Web | Applicazione Web ASP.NET MVC 3 |
| Web | Applicazione Web ASP.NET MVC4 |
| Web | Applicazione Web ASP.NET vuota (o Sito) |
| Web | Applicazione Web vuota ASP.NET MVC 2 |
| Web | Applicazione Web entità ASP.NET Dynamic Data |
| Web | Applicazione Web Linq ASP.NET Dynamic Data a SQL |
| Silverlight | Applicazione Silverlight |
| Silverlight | Applicazione aziendale di Silverlight |
| Silverlight | Applicazione di navigazione Silverlight |
| WCF | Applicazione di servizio WCF |
| WCF | Applicazione di servizio del flusso di lavoro WCF |
| Flusso di lavoro | Applicazione di servizio del flusso di lavoro WCF |

## <a name="next-steps"></a>Passaggi successivi

- [Preparare la pubblicazione o la distribuzione di un'applicazione di Azure da Visual Studio](vs-azure-tools-cloud-service-publish-set-up-required-services-in-visual-studio.md)
- [Configurazione delle credenziali per l'autenticazione denominate](vs-azure-tools-setting-up-named-authentication-credentials.md).
