---
title: Connettersi a un'istanza di SQL Server locale da un servizio mobile back-end di .NET mediante Connessioni ibride | Microsoft Docs
description: Informazioni su come connettersi a un'istanza di SQL Server locale da un servizio mobile back-end di .NET mediante Connessioni ibride di Azure
services: mobile-services
documentationcenter: ''
author: ggailey777
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# Connettersi a un'istanza di SQL Server locale da Servizi mobili di Azure mediante Connessioni ibride
[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Per la versione equivalente di questo argomento per le app per dispositivi mobili, vedere [Accedere alle risorse locali usando connessioni ibride nel servizio app di Azure](../app-service-web/web-sites-hybrid-connection-get-started.md).
> 
> 

Quando un'azienda passa al cloud, potrebbe non essere possibile eseguire subito la migrazione di tutte le risorse in Azure. La funzionalità Connessioni ibride consente a Servizi mobili di Azure di connettersi in modo sicuro alle risorse locali. In questo modo, è possibile rendere accessibili i dati locali ai client mobili mediante Azure. Sono supportate tutte le risorse che usano una porta TCP statica, compresi Microsoft SQL Server, MySQL, le API Web HTTP e la maggior parte dei servizi Web personalizzati. La funzionalità Connessioni ibride usa l'autorizzazione con firma di accesso condiviso per proteggere le connessioni tra il servizio mobile e Gestione connessione ibrida locale. Per altre informazioni, vedere [Panoramica delle connessioni ibride](../biztalk-services/integration-hybrid-connection-overview.md).

In questa esercitazione si apprenderà come modificare un servizio mobile back-end .NET in modo da usare un database SQL Server locale anziché il database SQL di Azure di cui viene effettuato il provisioning con il servizio. La funzionalità Connessioni ibride è supportata anche per un servizio mobile bak-end JavaScript, come illustrato in [questo articolo](http://blogs.msdn.com/b/azuremobile/archive/2014/05/12/connecting-to-an-external-database-with-node-js-backend-in-azure-mobile-services.aspx).

## Prerequisiti
Per completare questa esercitazione, è necessario disporre di:

* **Un servizio mobile back-end .NET esistente** <br/>Eseguire l'esercitazione [Introduzione a Servizi mobili] per creare e scaricare un nuovo servizio mobile back-end .NET dal [portale di Azure classico].

[!INCLUDE [hybrid-connections-prerequisites](../../includes/hybrid-connections-prerequisites.md)]

## Installare SQL Server Express, abilitare TCP/IP e creare un database SQL Server locale
[!INCLUDE [hybrid-connections-create-on-premises-database](../../includes/hybrid-connections-create-on-premises-database.md)]

## Creare una connessione ibrida
[!INCLUDE [hybrid-connections-create-new](../../includes/hybrid-connections-create-new.md)]

## Installare l'istanza locale di Hybrid Connection Manager per completare la connessione
[!INCLUDE [hybrid-connections-install-connection-manager](../../includes/hybrid-connections-install-connection-manager.md)]

## Configurare il progetto di servizio mobile per la connessione al database di SQL Server
In questo passaggio viene definita una stringa di connessione per il database locale e il servizio mobile viene modificato in modo da usare la connessione.

1. In Visual Studio 2013 aprire il progetto che definisce il servizio mobile back-end .NET.
   
    Per informazioni su come scaricare il progetto di back-end .NET, vedere [Introduzione a Servizi mobili](mobile-services-dotnet-backend-windows-store-dotnet-get-started.md).
2. In Esplora soluzioni aprire il file Web.config, individuare la sezione **connectionStrings**, aggiungere una nuova voce SqlClient simile alla seguente, che punta al database di SQL Server locale:
   
        <add name="OnPremisesDBConnection"
         connectionString="Data Source=OnPremisesServer,1433;
         Initial Catalog=OnPremisesDB;
         User ID=HybridConnectionLogin;
         Password=<**secure_password**>;
         MultipleActiveResultSets=True"
         providerName="System.Data.SqlClient" />
   
    Ricordare di sostituire `<**secure_password**>` in questa stringa con la password creata per *HbyridConnectionLogin*.
3. Fare clic su **Salva** in Visual Studio per salvare il file Web.config.
   
   > [!NOTE]
   > Questa impostazione di connessione viene usata per l'esecuzione nel computer locale. Per l'esecuzione in Azure viene eseguito l'override di questa impostazione con l'impostazione di connessione definita nel portale.
   > 
   > 
4. Espandere la cartella **Modelli** e aprire il file di modello dati, che termina con *Context.cs*.
5. Modificare il costruttore di istanza **DbContext** per passare il valore `OnPremisesDBConnection` al costruttore **DbContext** di base, simile al frammento di codice seguente:
   
        public class hybridService1Context : DbContext
        {
            public hybridService1Context()
                : base("OnPremisesDBConnection")
            {
            }
        }
   
    Il servizio userà ora la nuova connessione al database di SQL Server.

## Testare la connessione al database in locale
Prima di eseguire la pubblicazione in Azure e di usare la connessione ibrida, è consigliabile verificare che la connessione al database funzioni durante l'esecuzione in locale. In questo modo è più semplice potere diagnosticare e correggere eventuali problemi di connessione prima di eseguire una nuova pubblicazione e di iniziare a usare la connessione ibrida.

[!INCLUDE [mobile-services-dotnet-backend-test-local-service-api-documentation](../../includes/mobile-services-dotnet-backend-test-local-service-api-documentation.md)]

## Aggiornare Azure in modo da usare la stringa di connessione locale
Dopo aver verificato la connessione al database, è necessario aggiungere un'impostazione dell'app per la nuova stringa di connessione in modo da poter essere usata da Azure e quindi pubblicare il servizio mobile in Azure.

1. Passare al servizio mobile nel [portale di Azure classico].
2. Fare clic sulla scheda **Configura** e individuare la sezione **Stringhe di connessione**.
   
    ![Stringa di connessione del database locale](./media/mobile-services-dotnet-backend-hybrid-connections-get-started/11.png)
3. Aggiungere una nuova stringa di connessione di **SQL Server** denominata `OnPremisesDBConnection`, con un valore analogo al seguente:
   
        Server=OnPremisesServer,1433;Database=OnPremisesDB;User ID=HybridConnectionsLogin;Password=<**secure_password**>

    Sostituire `<**secure_password**>` con la password di protezione per *HybridConnectionLogin*.

1. Premere **Salva** per salvare la connessione ibrida e la stringa di connessione creata.
2. Mediante Visual Studio, pubblicare il progetto di servizio mobile aggiornato in Azure.
   
    Verrà visualizzata la pagina di avvio del servizio.
3. Mediante il pulsante **Prova adesso** nella pagina iniziale, come illustrato in precedenza, o mediante un'app client connessa al servizio mobile, richiamare alcune operazioni che generano modifiche nel database.
   
   > [!NOTE]
   > Quando si usa il pulsante**Prova adesso** per avviare le pagine delle API della Guida, specificare la chiave dell'applicazione come password (con un nome utente vuoto).
   > 
   > 
4. In SQL Server Management Studio connettersi all'istanza di SQL Server, aprire Esplora oggetti, espandere il database **OnPremisesDB** ed espandere **Tabelle**.
5. Fare clic con il pulsante destro del mouse sulla tabella **hybridService1.TodoItems** e scegliere **Seleziona prime 1000 righe** per visualizzare i risultati.
   
    Si noti che le modifiche generate nell'app sono state salvate dal servizio mobile nel database locale mediante la connessione ibrida.

## Vedere anche
* [Sito Web delle connessioni ibride](https://azure.microsoft.com/services/biztalk-services/)
* [Panoramica delle connessioni ibride](../biztalk-services/integration-hybrid-connection-overview.md)
* [Servizi BizTalk: schede Dashboard, Monitor, Scala, Configura e Connessione ibrida](../biztalk-services/biztalk-dashboard-monitor-scale-tabs.md)
* [Come modificare un modello di dati in un servizio mobile back-end .NET](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md)

<!-- IMAGES -->


<!-- Links -->
[portale di Azure classico]: http://manage.windowsazure.com
[Introduzione a Servizi mobili]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md

<!---HONumber=AcomDC_0727_2016-->