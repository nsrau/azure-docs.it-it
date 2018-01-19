---
title: Creare un'applicazione .NET Service Fabric in Azure | Microsoft Docs
description: Creare un'applicazione .NET per Azure usando l'esempio disponibile nella guida introduttiva a Service Fabric.
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: quickstart
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/02/2018
ms.author: mikhegn
ms.custom: mvc, devcenter
ms.openlocfilehash: 70167322f1576b4a9cbd5f499edfc934b8a9a799
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2018
---
# <a name="create-a-net-service-fabric-application-in-azure"></a>Creare un'applicazione .NET Service Fabric in Azure
Azure Service Fabric è una piattaforma di sistemi distribuiti per la distribuzione e la gestione di microservizi e contenitori scalabili e affidabili. 

In questa guida introduttiva viene illustrato come distribuire la prima applicazione .NET in Service Fabric. Al termine, sarà disponibile un'applicazione di voto con un front-end Web ASP.NET Core che salva i risultati delle votazioni in un servizio back-end con stato nel cluster.

![Screenshot dell'applicazione](./media/service-fabric-quickstart-dotnet/application-screenshot.png)

Usando questa applicazione, si apprenderà come:
> [!div class="checklist"]
> * Creare un'applicazione mediante .NET e Service Fabric
> * Usare ASP.NET Core come front-end Web
> * Archiviare i dati dell'applicazione in un servizio con stato
> * Eseguire il debug dell'applicazione in locale
> * Distribuire l'applicazione in un cluster in Azure
> * Scalare orizzontalmente l'applicazione in più nodi
> * Eseguire un aggiornamento in sequenza delle applicazioni

## <a name="prerequisites"></a>prerequisiti
Per completare questa guida introduttiva:
1. [Installare Visual Studio 2017](https://www.visualstudio.com/) con i carichi di lavoro **Sviluppo di Azure** e **Sviluppo ASP.NET e Web**.
2. [Installare Git](https://git-scm.com/)
3. [Installare Microsoft Azure Service Fabric SDK](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-CoreSDK)
4. Eseguire il comando seguente per consentire a Visual Studio di eseguire la distribuzione nel cluster Service Fabric locale:
    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Force -Scope CurrentUser
    ```

>[!NOTE]
> L'applicazione di esempio in questa guida introduttiva usa funzionalità non disponibili in Windows 7.
>

## <a name="download-the-sample"></a>Scaricare l'esempio
In una finestra di comando eseguire il comando seguente per clonare il repository dell'app di esempio nel computer locale.
```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="run-the-application-locally"></a>Eseguire l'applicazione in locale
Fare clic con il pulsante destro del mouse sull'icona di Visual Studio nel menu Start e scegliere **Esegui come amministratore**. Per connettere il debugger ai servizi, è necessario eseguire Visual Studio come amministratore.

Aprire la soluzione di Visual Studio **Voting.sln** dal repository che è stato clonato.  

Per impostazione predefinita, l'applicazione Voting è impostata per l'ascolto sulla porta 8080.  La porta dell'applicazione è impostata nel file */VotingWeb/PackageRoot/ServiceManifest.xml*.  È possibile cambiare la porta dell'applicazione aggiornando l'attributo **Port** dell'elemento **Endpoint**.  Per distribuire ed eseguire l'applicazione in locale, la porta dell'applicazione deve essere aperta e disponibile nel computer.  Se si cambia la porta dell'applicazione, sostituire "8080" con il nuovo valore della porta dell'applicazione in tutto l'articolo.

Per distribuire l'applicazione, premere **F5**.

> [!NOTE]
> La prima volta che si esegue e si distribuisce l'applicazione, Visual Studio crea un cluster locale per il debug. Questa operazione può richiedere del tempo. Lo stato della creazione del cluster verrà visualizzato nella finestra di output di Visual Studio.  Nell'output verrà visualizzato il messaggio "L'URL dell'applicazione non è impostato oppure non è di tipo HTTP/HTTPS, di conseguenza l'applicazione non verrà aperta nel browser."  Questo messaggio non indica un errore, ma indica che un browser non verrà avviato automaticamente.

Al termine della distribuzione, avviare un browser e aprire la pagina `http://localhost:8080`, ovvero il front-end Web dell'applicazione.

![Front-end dell'applicazione](./media/service-fabric-quickstart-dotnet/application-screenshot-new.png)

È ora possibile aggiungere un set di opzioni per le votazioni e iniziare a raccogliere i voti. L'applicazione viene eseguita e archivia tutti i dati nel cluster di Service Fabric, senza che sia necessario un database separato.

## <a name="walk-through-the-voting-sample-application"></a>Descrizione dettagliata dell'applicazione di voto di esempio
L'applicazione di voto è costituita da due servizi:
- Il servizio front-end Web (VotingWeb) - Un servizio front-end Web ASP.NET Core che gestisce la pagina Web e che espone le API Web per la comunicazione con il servizio back-end.
- Il servizio back-end (VotingData) - Un servizio Web ASP.NET Core che espone un'API per l'archiviazione dei risultati delle votazioni in un oggetto Reliable Dictionary reso persistente su disco.

![Diagramma dell'applicazione](./media/service-fabric-quickstart-dotnet/application-diagram.png)

Quando l'utente vota nell'applicazione, si verificano gli eventi seguenti:
1. JavaScript invia la richiesta di voto all'API Web nel servizio front-end Web come una richiesta HTTP PUT.

2. Il servizio front-end Web usa un proxy per individuare e inoltrare una richiesta PUT HTTP al servizio back-end.

3. Il servizio back-end accetta la richiesta in ingresso e archivia il risultato aggiornato in un oggetto Reliable Dictionary, che viene replicato in più nodi all'interno del cluster e reso persistente su disco. Tutti i dati dell'applicazione sono archiviati nel cluster, quindi non è necessario alcun database.

## <a name="debug-in-visual-studio"></a>Eseguire il debug in Visual Studio
Durante il debug dell'applicazione in Visual Studio, viene usato un cluster di sviluppo locale di Service Fabric. È possibile modificare l'esperienza di debug in base allo specifico scenario. In questa applicazione i dati vengono archiviati nel servizio back-end tramite un dizionario Reliable. Visual Studio rimuove l'applicazione per impostazione predefinita quando si arresta il debugger. La rimozione dell'applicazione determina la rimozione anche dei dati nel servizio back-end. Per rendere persistenti i dati tra le sessioni di debug, è possibile modificare la proprietà **Modalità di debug applicazione** del progetto **Voting** in Visual Studio.

Per osservare che cosa avviene nel codice, completare la procedura seguente:
1. Aprire il file **/VotingWeb/Controllers/VotesController.cs** e impostare un punto di interruzione nel metodo **Put** dell'API Web alla riga 47. È possibile eseguire una ricerca nel file usando Esplora soluzioni in Visual Studio.

2. Aprire il file **/VotingData/Controllers/VoteDataController.cs** e impostare un punto di interruzione nel metodo **Put** dell'API Web alla riga 50.

3. Tornare al browser e fare clic su un'opzione di voto oppure aggiungere una nuova opzione di voto. È stato raggiunto il primo punto di interruzione nel controller API del front-end Web.
    - In questa posizione, il codice JavaScript nel browser invia una richiesta al controller API Web nel servizio front-end.
    
    ![Aggiungere il servizio front-end di voto](./media/service-fabric-quickstart-dotnet/addvote-frontend.png)

    - Prima di tutto, costruire l'URL di ReverseProxy per il servizio back-end **(1)**.
    - Inviare quindi la richiesta PUT HTTP a ReverseProxy **(2)**.
    - Infine, restituire la risposta dal servizio back-end al client **(3)**.

4. Premere **F5** per continuare.
    - Ora ci troviamo al punto di interruzione nel servizio back-end.
    
    ![Aggiungere il servizio back-end di voto](./media/service-fabric-quickstart-dotnet/addvote-backend.png)

    - Nella prima riga del metodo **(1)** `StateManager` viene usato per ottenere o aggiungere un dizionario Reliable denominato `counts`.
    - Tutte le interazioni con i valori in un oggetto Reliable Dictionary richiedono una transazione, che viene creata dall'istruzione using **(2)**.
    - Nella transazione aggiornare il valore della chiave pertinente per l'opzione di voto ed eseguire il commit dell'operazione **(3)**. Dopo la restituzione del metodo Commit, i dati vengono aggiornati nel dizionario e replicati negli altri nodi del cluster. A questo punto, i dati sono archiviati in modo sicuro nel cluster e il servizio back-end può eseguire il failover in altri nodi, rendendo comunque disponibili i dati.
5. Premere **F5** per continuare.

Per interrompere la sessione di debug, premere **MAIUSC+F5**.

## <a name="deploy-the-application-to-azure"></a>Distribuzione dell'applicazione in Azure
Per distribuire l'applicazione in Azure, è necessario un cluster di Service Fabric che esegua l'applicazione. 

### <a name="join-a-party-cluster"></a>Aggiungere un party cluster
I cluster di entità sono cluster Service Fabric gratuiti e disponibili per un periodo di tempo limitato ospitati in Azure e gestiti dal team di Service Fabric, in cui chiunque può distribuire applicazioni e ottenere informazioni sulla piattaforma. 

Eseguire l'accesso e [aggiungere un cluster Windows](http://aka.ms/tryservicefabric). Ricordare il valore di **Endpoint connessione**, che viene usato nei passaggi seguenti.

> [!Note]
> Per impostazione predefinita, il servizio front-end Web è configurato per l'ascolto del traffico in ingresso sulla porta 8080. La porta 8080 è aperta nel party cluster.  Se è necessario cambiare la porta dell'applicazione, sostituirla con una delle porte aperte nel party cluster.
>

### <a name="deploy-the-application-using-visual-studio"></a>Distribuire l'applicazione tramite Visual Studio
Ora che l'applicazione è pronta, è possibile distribuirla in un cluster direttamente da Visual Studio.

1. Fare clic con il pulsante destro del mouse su **Voting** in Esplora soluzioni e scegliere **Pubblica**. Verrà visualizzata la finestra di dialogo Pubblica.

    ![Finestra di dialogo Pubblica](./media/service-fabric-quickstart-dotnet/publish-app.png)

2. Copiare l'**endpoint della connessione** dalla pagina del party cluster nel campo **Endpoint connessione** e fare clic su **Pubblica**. Ad esempio, `winh1x87d1d.westus.cloudapp.azure.com:19000`.

    Ogni applicazione nel cluster deve avere un nome univoco.  Un party cluster è tuttavia un ambiente pubblico condiviso e potrebbe verificarsi un conflitto con un'applicazione esistente.  Se è presente un conflitto di nomi, ridenominare il progetto di Visual Studio e distribuirlo di nuovo.

3. Aprire un browser e digitare l'indirizzo del cluster seguito da ":8080" per passare all'applicazione nel cluster, ad esempio `http://winh1x87d1d.westus.cloudapp.azure.com:8080`. A questo punto, sarà visualizzata l'applicazione in esecuzione nel cluster in Azure.

![Front-end dell'applicazione](./media/service-fabric-quickstart-dotnet/application-screenshot-new-azure.png)

## <a name="scale-applications-and-services-in-a-cluster"></a>Ridimensionare applicazioni e servizi in un cluster
I servizi di Service Fabric possono essere facilmente ridimensionati in un cluster per supportare le modifiche del carico sui servizi. È possibile ridimensionare un servizio modificando il numero di istanze in esecuzione nel cluster. Sono disponibili diversi sistemi per garantire la scalabilità dei servizi: è possibile usare gli script o i comandi di PowerShell oppure l'interfaccia della riga di comando di Service Fabric (sfctl). In questo esempio usare Service Fabric Explorer.

Service Fabric Explorer è in esecuzione in tutti i cluster di Service Fabric ed è accessibile da un browser, passando alla porta di gestione HTTP (19080) del cluster, ad esempio `http://winh1x87d1d.westus.cloudapp.azure.com:19080`.

Per scalare il servizio front-end Web, seguire questa procedura:

1. Aprire Service Fabric Explorer nel cluster, ad esempio `http://winh1x87d1d.westus.cloudapp.azure.com:19080`.
2. Fare clic sui puntini di sospensione accanto al nodo **fabric:/Voting/VotingWeb** nella visualizzazione ad albero e scegliere **Scale Service** (Ridimensiona servizio).

    ![Service Fabric Explorer](./media/service-fabric-quickstart-dotnet/service-fabric-explorer-scale.png)

    Ora è possibile scegliere di modificare il numero di istanze del servizio front-end Web.

3. Impostare il numero su **2** e fare clic su **Scale Service** (Ridimensiona servizio).
4. Fare clic sul nodo **fabric:/Voting/VotingWeb** nella visualizzazione ad albero ed espandere il nodo della partizione (rappresentato da un GUID).

    ![Ridimensionamento dei servizi in Service Fabric Explorer](./media/service-fabric-quickstart-dotnet/service-fabric-explorer-scaled-service.png)

    Dopo un ritardo saranno presenti due istanze del servizio.  Nella visualizzazione ad albero si può verificare in quali nodi vengono eseguite le istanze.

Con questa semplice attività di gestione le risorse disponibili si sono raddoppiate per il servizio front-end per l'elaborazione del carico utente. È importante comprendere che non sono necessarie più istanze di un servizio perché questo venga eseguito in modo affidabile. In caso di problemi di un servizio, Service Fabric assicura l'esecuzione di una nuova istanza del servizio nel cluster.

## <a name="perform-a-rolling-application-upgrade"></a>Eseguire un aggiornamento in sequenza delle applicazioni
Durante la distribuzione di nuovi aggiornamenti per l'applicazione, Service Fabric distribuisce gli aggiornamenti in modo sicuro. Gli aggiornamenti in sequenza non comportano tempi di inattività durante l'aggiornamento e consentono il rollback automatico in caso di errori.

Per aggiornare l'applicazione, eseguire le operazioni seguenti:

1. Aprire il file **/VotingWeb/Views/Home/Index.cshtml** in Visual Studio.
2. Modificare l'intestazione della pagina aggiungendo o aggiornando il testo. Ad esempio, modificare il titolo in "Esempio di voto di Service Fabric v2".
3. Salvare il file.
4. Fare clic con il pulsante destro del mouse su **Voting** in Esplora soluzioni e scegliere **Pubblica**. Verrà visualizzata la finestra di dialogo Pubblica.
5. Fare clic sul pulsante **Versione manifesto** per modificare la versione del servizio e dell'applicazione.
6. Modificare la versione dell'elemento **Codice** in **VotingWebPkg**, ad esempio in "2.0.0", e fare clic su **Salva**.

    ![Finestra di dialogo Modifica versione](./media/service-fabric-quickstart-dotnet/change-version.png)
7. Nella finestra di dialogo **Pubblica applicazione di Service Fabric** selezionare la casella di controllo Aggiorna l'applicazione e fare clic su **Pubblica**.

    ![Impostazione di aggiornamento nella finestra di dialogo Pubblica](./media/service-fabric-quickstart-dotnet/upgrade-app.png)
8. Aprire il browser e passare all'indirizzo del cluster sulla porta 19080, ad esempio `http://winh1x87d1d.westus.cloudapp.azure.com:19080`.
9. Fare clic sul nodo **Applicazioni** nella visualizzazione ad albero, quindi su **Upgrades in Progress** (Aggiornamenti in corso) nel riquadro destro. È possibile osservare che l'aggiornamento viene distribuito attraverso i domini di aggiornamento del cluster, verificando l'integrità di ogni dominio prima di procedere a quello successivo. Nell'indicatore di stato viene visualizzato in verde un dominio di aggiornamento dopo aver verificato l'integrità di dominio.
    ![Visualizzazione dell'aggiornamento in Service Fabric Explorer](./media/service-fabric-quickstart-dotnet/upgrading.png)

    Service Fabric garantisce la sicurezza degli aggiornamenti attendendo due minuti dopo l'aggiornamento del servizio in ogni nodo del cluster. L'intero aggiornamento richiederà circa 8 minuti.

10. Durante l'esecuzione dell'aggiornamento, è comunque possibile usare l'applicazione. Poiché nel cluster sono in esecuzione due istanze del servizio, alcune delle richieste potrebbero ottenere la versione aggiornata dell'applicazione, mentre altre potrebbero ancora ottenere la versione precedente.

## <a name="next-steps"></a>Passaggi successivi
In questa guida introduttiva si è appreso come:

> [!div class="checklist"]
> * Creare un'applicazione mediante .NET e Service Fabric
> * Usare ASP.NET Core come front-end Web
> * Archiviare i dati dell'applicazione in un servizio con stato
> * Eseguire il debug dell'applicazione in locale
> * Distribuire l'applicazione in un cluster in Azure
> * Scalare orizzontalmente l'applicazione in più nodi
> * Eseguire un aggiornamento in sequenza delle applicazioni

Per altre informazioni su Service Fabric e .NET, fare riferimento a questa esercitazione:
> [!div class="nextstepaction"]
> [Applicazione .NET in Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
