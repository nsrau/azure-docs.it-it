<properties 
   pageTitle="Aggiungere spazio di archiviazione di Azure mediante Servizi connessi in Visual Studio | Microsoft Azure"
   description="Aggiungere archiviazione di Azure all'applicazione usando la finestra di dialogo Aggiungi servizi connessi di Visual Studio"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="mobile"
   ms.date="12/16/2015"
   ms.author="tarcher" />

# Aggiunta dell’archiviazione di Azure tramite Servizi connessi di Visual Studio

## Panoramica

Con Visual Studio 2015, è possibile connettersi a qualsiasi servizio cloud in C#, servizio mobile back-end .NET, sito Web o servizio ASP.NET, servizio ASP.NET 5 o servizio di Processo Web di Azure in archiviazione di Azure tramite la finestra di dialogo **Aggiungi servizi connessi**. La funzionalità servizio connesso aggiunge tutti i riferimenti richiesti e il codice di connessione e modifica i file di configurazione in modo appropriato. La finestra di dialogo visualizza inoltre la documentazione che indica i passaggi successivi per avviare l'archiviazione BLOB, code e tabelle.

## Tipi di progetto supportati

Per connettersi ad archiviazione di Azure nei seguenti tipi di progetto, è possibile usare la finestra di dialogo Servizi connessi.

- Progetti Web ASP.NET

- Progetti ASP.NET 5

- Progetti del ruolo Web del servizio cloud .NET e del ruolo di lavoro

- Progetti di servizi mobili .NET

- Progetti di Processo Web di Azure


## Connettersi ad archiviazione di Azure usando la finestra di dialogo Servizi connessi

1. È necessario disporre di un account Azure. Se non si ha un account Azure, è possibile iscriversi per ottenere una [versione di valutazione gratuita](http://go.microsoft.com/fwlink/?LinkId=518146). Dopo aver creato un account Azure, è possibile creare account di archiviazione, creare servizi mobili e configurare Azure Active Directory.

1. Aprire il progetto in Visual Studio, aprire il menu di scelta rapida per il nodo **Riferimenti** in Esplora soluzioni, quindi scegliere **Aggiungi servizio connesso**.

    ![Aggiunta di un servizio connesso](./media/vs-azure-tools-connected-services-storage/IC796702.png)

1. Nella finestra di dialogo **Aggiungi servizio connesso** scegliere **Account di archiviazione di Azure**, quindi fare clic su **Configura**. Se non è già stato effettuato, potrebbe essere richiesto l'accesso ad Azure.

    ![Finestra di dialogo Aggiungi servizio connesso - Archiviazione](./media/vs-azure-tools-connected-services-storage/IC796703.png)

1. Nella finestra di dialogo **Archiviazione di Azure** scegliere un account di archiviazione esistente e quindi fare clic su **Aggiungi**.

    Se è necessario creare un nuovo account di archiviazione, andare al passaggio successivo. In caso contrario, andare direttamente al passaggio 6.

    ![Finestra di dialogo Archiviazione di Azure](./media/vs-azure-tools-connected-services-storage/IC796704.png)

1. Per creare un nuovo account di archiviazione:

    1. Scegliere il pulsante **Crea un nuovo Account di archiviazione** nella parte inferiore della finestra di dialogo Archiviazione di Azure.

    1. Compilare la finestra di dialogo **Crea account di archiviazione**, quindi scegliere il pulsante **Crea**.
    
        ![Finestra di dialogo Archiviazione di Azure](./media/vs-azure-tools-connected-services-storage/create-storage-account.png)

        Quando si torna nella finestra di dialogo **Archiviazione di Azure**, la nuova risorsa di archiviazione viene visualizzata nell'elenco.

    1. Selezionare la nuova risorsa di archiviazione nell'elenco e fare clic su **Aggiungi**.

1. Il servizio di archiviazione connesso viene visualizzato sotto il nodo Riferimenti servizio del progetto WebJob.

    ![Archiviazione di Azure in progetti di tipo processo Web](./media/vs-azure-tools-connected-services-storage/IC796705.png)

1. Esaminare la pagina introduttiva visualizzata e controllare come è stato modificato il progetto. Ogni volta che si aggiunge un servizio connesso, nel browser verrà visualizzata una pagina introduttiva. È possibile esaminare i passaggi successivi e gli esempi di codice suggeriti o passare alla pagina Informazioni sull'evento per vedere quali riferimenti sono stati aggiunti al progetto e in che modo i file di configurazione e il codice sono stati modificati.

## Come viene modificato il progetto

Una volta terminata la finestra di dialogo, Visual Studio aggiunge riferimenti e modifica di alcuni file di configurazione. Le modifiche specifiche dipendono dal tipo di progetto.

 - Per i progetti ASP.NET, vedere [Informazioni sull'evento – Progetti ASP.NET](http://go.microsoft.com/fwlink/p/?LinkId=513126). 
 - Per i progetti ASP.NET 5, vedere [Informazioni sull'evento – Progetti ASP.NET 5](http://go.microsoft.com/fwlink/p/?LinkId=513124). 
 - Per i progetti del servizio cloud (ruoli Web e ruoli di lavoro), vedere [Informazioni sull'evento – Progetti del servizio cloud](http://go.microsoft.com/fwlink/p/?LinkId=516965). 
 - Per i progetti di tipo processo Web, vedere [Risultati - Progetti di tipo processo Web](vs-storage-webjobs-what-happened/).

## Passaggi successivi

1. Usando gli esempi di codice introduttivi come guida, creare il tipo di risorsa di archiviazione desiderato, quindi iniziare a scrivere codice per accedere all'account di archiviazione.

1. Domande e assistenza
     - [Forum MSDN: Archiviazione di Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazuredata)

     - [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)

     - [Archiviazione in azure.microsoft.com](http://azure.microsoft.com/services/storage)

     - [Documentazione di archiviazione in azure.microsoft.com](http://azure.microsoft.com/documentation/services/storage/)

<!---HONumber=AcomDC_1223_2015-->