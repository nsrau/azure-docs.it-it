<properties 
   pageTitle="Aggiunta di servizi mobili tramite servizi connessi in Visual Studio"
   description="Aggiungere servizi mobili tramite la finestra di dialogo Aggiungi servizi connessi di Visual Studio "
   services="visual-studio-online"
   documentationCenter="na"
   authors="patshea123"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="mobile"
   ms.date="08/12/2015"
   ms.author="patshea" />

# Aggiunta di servizi mobili tramite Servizi connessi di Visual Studio

Con Visual Studio 2015, è possibile connettersi a Servizi mobili di Azure utilizzando la finestra di dialogo **Aggiungi servizio connesso**. È possibile connettersi da qualsiasi app client C#, qualsiasi app JavaScript o app Cordova multipiattaforma. Una volta connessi, è possibile creare e accedere ai dati, creare API personalizzate e i processi pianificati o aggiungere il supporto per le notifiche push. L'operazione di servizi connessi aggiunge tutti i riferimenti appropriati e il codice di connessione. È possibile anche sfruttare il supporto integrato per l'autenticazione con un'ampia gamma di sistemi di identità comuni, quali Azure AD, Facebook, Twitter e Account di Microsoft.

## Tipi di progetto supportati

>[AZURE.NOTE]In Visual Studio 2015, l'aggiunta di Servizi mobili di Azure a progetti di Windows Universal (Windows 10) utilizzando la finestra di dialogo Aggiungi servizi connessi non è supportata. È possibile aggiungere servizi mobili di Azure installando i pacchetti appropriati utilizzando Gestione pacchetti NuGet per il progetto.

Per connettersi a Windows Azure nei seguenti tipi di progetto, è possibile utilizzare la finestra di dialogo dei Servizi mobili di Azure.

- Progetti .NET Windows 8.1 Store, Phone e Universal App

- Progetti JavaScript Windows 8.1 Store, Phone e Universal App

- Progetti creati utilizzando Strumenti di Visual Studio per Apache Cordova


## Connettersi a Servizi mobili di Azure utilizzando la finestra di dialogo Aggiungi servizi connessi

1. È necessario disporre di un account Azure. Se non si dispone di un account Azure, è possibile iscriversi per un [account gratuito](http://go.microsoft.com/fwlink/?LinkId=518146).

1. Aprire la finestra di dialogo **Aggiungi servizi connessi**.
 - Per le applicazioni .NET, aprire il progetto in Visual Studio, aprire il menu di scelta rapida del nodo **Riferimenti** in Esplora soluzioni, quindi scegliere **Aggiungi servizio connesso**
 
        ![Connecting to Azure Mobile Service](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)

 - Per i progetti di app Apache Cordova, aprire il progetto in Visual Studio, aprire il menu di scelta rapida del nodo del progetto in Esplora soluzioni e quindi scegliere **Aggiungi servizio connesso**.

1. Nella finestra di dialogo **Aggiungi servizio connesso** scegliere **Servizi mobili di Azure**, quindi scegliere il pulsante **Configura**. Potrebbe essere richiesto l'accesso ad Azure, se non è ancora stato effettuato.

    ![Aggiunta di un servizio Mobile di Azure](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)

1. Nella finestra di dialogo Servizi mobili di Azure, scegliere un servizio mobile esistente, se disponibile. Se è necessario creare un nuovo servizio mobile di Azure, attenersi alla procedura seguente per eseguire questa operazione. In caso contrario, continuare con il passaggio successivo.

    Per creare un nuovo account di servizio mobile: 1. scegliere il collegamento **Crea servizio** nella parte inferiore della finestra di dialogo. ![Aggiungere un nuovo servizio connesso mobile](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)




    2. Nella finestra di dialogo **Crea servizio Mobile** è possibile scegliere un servizio mobile back-end di JavaScript o un servizio mobile back-end .NET dall’elenco a discesa **Runtime**. 
  
        ![Creazione di un servizio mobile](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)

        Un servizio back-end di JavaScript è semplice e potente. Se si crea un servizio mobile back-end di JavaScript, il codice JavaScript lato server viene archiviato nel cloud, ma è possibile modificare gli script del server tramite Esplora server o il portale di gestione di Azure.

        Un servizio mobile back-end di .NET offre la potenza e flessibilità dell'API Web e di Entity Framework. Se si crea un servizio mobile back-end di .NET, un progetto viene creato e aggiunto alla soluzione.

    1. Scegliere l’**Area** in cui si desidera il servizio mobile e quindi immettere un nome utente e una password per il server.
 
    1. Dopo aver immesso tutte le informazioni necessarie, scegliere il pulsante **Crea** per creare il servizio mobile.
    2. Il nuovo servizio mobile verrà visualizzato nell'elenco dei servizi nella finestra di dialogo **Servizi mobili di Azure**. Scegliere il nuovo servizio mobile nell'elenco, quindi scegliere il pulsante **Aggiungi** per aggiungere il servizio al progetto.
    

1. Esaminare la pagina introduttiva visualizzata e controllare come è stato modificato il progetto. Ogni volta che si aggiunge un servizio connesso, nel browser verrà visualizzata una pagina introduttiva. È possibile esaminare i passaggi successivi e gli esempi di codice suggeriti o passare alla pagina Informazioni sull'evento per vedere quali riferimenti sono stati aggiunti al progetto e in che modo i file di configurazione e il codice sono stati modificati.

1. Utilizzando gli esempi di codice come guida, iniziare a scrivere il codice per accedere al servizio mobile.

## Come viene modificato il progetto

La modalità di modifica del progetto di Visual Studio dipende dal tipo di progetto. Per le app client C#, vedere [Informazioni sull’evento – Progetti C#](http://go.microsoft.com/fwlink/p/?LinkId=513119). Per le applicazioni client JavaScript, vedere [Informazioni sull’evento – Progetti JavaScript](http://go.microsoft.com/fwlink/p/?LinkId=513120). Per le applicazioni Cordova, vedere [Informazioni sull’evento – Progetti Cordova](http://go.microsoft.com/fwlink/p/?LinkId=513116).


##Passaggi successivi

Domande e assistenza:

 - [Forum MSDN: Servizi mobili di Azure Mobile Services](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)

 - [Servizi mobili di Azure nel blog del Team di Microsoft Azure](http://azure.microsoft.com/blog/topics/mobile/)

 - [Servizi mobili di Azure in azure.microsoft.com](http://azure.microsoft.com/services/mobile-services/)

 - [Documentazione dei servizi mobili di Azure sul sito azure.microsoft.com](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=August15_HO8-->