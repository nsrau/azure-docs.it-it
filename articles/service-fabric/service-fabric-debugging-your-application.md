---
title: Debug dell'applicazione in Visual Studio | Documentazione Microsoft
description: "Migliorare l'affidabilità e le prestazioni dei servizi sviluppandoli ed eseguendone il debug in Visual Studio all'interno di un cluster di sviluppo locale."
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: 
ms.assetid: cb888532-bcdb-4e47-95e4-bfbb1f644da4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/29/2017
ms.author: vturecek;mikhegn
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 2459025899a7f5ffebf44fa104ed112c0eb99dfa
ms.contentlocale: it-it
ms.lasthandoff: 07/01/2017


---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Debug dell'applicazione di Service Fabric mediante Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Eseguire il debug di un'applicazione di Service Fabric
Per risparmiare tempo e denaro, è possibile distribuire l'applicazione di Service Fabric ed eseguirne il debug in un cluster di sviluppo locale. Visual Studio 2017 o Visual Studio 2015 può distribuire l'applicazione nel cluster locale e connettere automaticamente il debugger a tutte le istanze dell'applicazione.

1. Avviare un cluster di sviluppo locale seguendo la procedura descritta nell'articolo [Configurazione dell'ambiente di sviluppo di Service Fabric](service-fabric-get-started.md).
2. Premere **F5** oppure fare clic su **Debug** > **Avvia debug**.
   
    ![Avviare il debug di un'applicazione][startdebugging]
3. Impostare i punti di interruzione nel codice ed eseguire l'applicazione un'istruzione alla volta scegliendo i comandi dal menu **Debug** .
   
   > [!NOTE]
   > Visual Studio si connette a tutte le istanze dell'applicazione. Mentre il codice viene eseguito un'istruzione alla volta, i punti di interruzione possono essere raggiunti da più processi, dando luogo a sessioni simultanee. Provare a disabilitare i punti di interruzione dopo che sono stati raggiunti rendendoli condizionali in base all'ID del thread, oppure usando gli eventi di diagnostica.
   > 
   > 
4. La finestra degli **eventi di diagnostica** si aprirà automaticamente per visualizzare gli eventi diagnostici in tempo reale.
   
    ![Visualizzare gli eventi diagnostici in tempo reale][diagnosticevents]
5. È possibile aprire la finestra degli **** eventi di diagnostica anche in Cloud Explorer.  In **Service Fabric** fare clic con il pulsante destro del mouse e scegliere **Visualizza tracce streaming**.
   
    ![Aprire la finestra degli eventi di diagnostica][viewdiagnosticevents]
   
    Se si vuole filtrare le tracce in un'applicazione o in un servizio, è sufficiente abilitare le tracce di streaming in tale applicazione o servizio.
6. Gli eventi diagnostici possono essere visualizzati nel file **ServiceEventSource.cs** generato automaticamente e vengono chiamati dal codice dell'applicazione.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. Nella finestra degli ****eventi di diagnostica è possibile filtrare, sospendere ed esaminare eventi in tempo reale.  Il filtro è una semplice ricerca di stringhe del messaggio dell'evento, incluso il relativo contenuto.
   
    ![Filtrare, sospendere e riprendere o esaminare eventi in tempo reale][diagnosticeventsactions]
8. Il debug dei servizi è come il debug di qualsiasi altra applicazione. In genere i punti di interruzione vengono impostati tramite Visual Studio per semplificare il debug. Anche se le raccolte Reliable Collections vengono replicate in più nodi, implementano comunque IEnumerable. Ciò significa che è possibile utilizzare la visualizzazione dei risultati in Visual Studio durante il debug per vedere cosa è stato memorizzato all'interno. È sufficiente impostare un punto di interruzione in qualsiasi posizione all'interno del codice.
   
    ![Avviare il debug di un'applicazione][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Eseguire il debug di un'applicazione remota di Service Fabric
Se le applicazioni di Service Fabric sono in esecuzione in un cluster di Service Fabric in Azure, è possibile eseguirne il debug remoto direttamente da Visual Studio.

> [!NOTE]
> La funzionalità richiede [Service Fabric SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [Azure SDK per .NET 2.9](https://azure.microsoft.com/downloads/).    
> 
> 

<!-- -->
> [!WARNING]
> Il debug remoto è progettato per scenari di sviluppo e test e non deve essere usato in ambienti di produzione a causa dell'impatto sulle applicazioni in esecuzione.
> 
> 

1. Passare al cluster in **Cloud Explorer**, fare clic con il pulsante destro del mouse e scegliere **Abilita debug**
   
    ![Abilitare il debug remoto][enableremotedebugging]
   
    Verrà avviato il processo di abilitazione dell'estensione di debug remoto nei nodi del cluster e delle configurazioni di rete necessarie.
2. Fare clic con il pulsante destro del mouse sul nodo del cluster in **Cloud Explorer** e scegliere **Associa debugger**
   
    ![Collega debugger][attachdebugger]
3. Nella finestra di dialogo **Associa a processo** scegliere il processo di cui si vuole eseguire il debug e fare clic su **Collega**
   
    ![Scegliere il processo][chooseprocess]
   
    Il nome del processo a cui connettersi corrisponde al nome dell'assembly del progetto del servizio.
   
    Il debugger si connetterà a tutti i nodi che eseguono il processo.
   
   * Se si esegue il debug di un servizio senza stato, tutte le istanze del servizio in tutti i nodi fanno parte della sessione di debug.
   * Se si esegue il debug di un servizio con stato, solo la replica primaria di qualsiasi partizione sarà attiva e quindi rilevata dal debugger. Se la replica primaria viene spostata durante la sessione di debug, l'elaborazione di quella replica farà ancora parte della sessione di debug.
   * Per rilevare solo le partizioni o istanze pertinenti di un determinato servizio, è possibile usare punti di interruzione condizionali per interrompere solo una specifica istanza o partizione.
     
     ![Punto di interruzione condizionale][conditionalbreakpoint]
     
     > [!NOTE]
     > Attualmente non è supportato il debug di un cluster di Service Fabric con più istanze dello stesso nome di eseguibile del servizio.
     > 
     > 
4. Al termine del debug dell'applicazione è possibile disabilitare l'estensione di debug remoto facendo clic con il pulsante destro del mouse sul cluster in **Cloud Explorer** e scegliendo **Disabilita debug**
   
    ![Disabilitare il debug remoto][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Streaming delle tracce da un nodo del cluster remoto
Si può anche eseguire lo streaming delle tracce direttamente da un nodo del cluster remoto a Visual Studio. Questa funzionalità consente di eseguire lo streaming degli eventi di traccia ETW, generati su un nodo del cluster di Service Fabric.

> [!NOTE]
> La funzionalità richiede [Service Fabric SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [Azure SDK per .NET 2.9](https://azure.microsoft.com/downloads/).
> Questa funzionalità supporta solo i cluster in esecuzione in Azure.
> 
> 

<!-- -->
> [!WARNING]
> Lo streaming delle tracce è progettato per scenari di sviluppo e test e non deve essere usato in ambienti di produzione a causa dell'impatto sulle applicazioni in esecuzione.
> In uno scenario di produzione è consigliabile basarsi sugli eventi di inoltro usando Diagnostica di Azure.
> 
> 

1. Passare al cluster in **Cloud Explorer**, fare clic con il pulsante destro del mouse e scegliere **Abilita tracce streaming**
   
    ![Abilitare le tracce di streaming remote][enablestreamingtraces]
   
    Verrà avviato il processo di abilitazione dell'estensione di streaming delle tracce nei nodi del cluster e delle configurazioni di rete necessarie.
2. Espandere l'elemento **Nodi** in **Cloud Explorer**, fare clic con il pulsante destro del mouse sul nodo da cui trasmettere le tracce e scegliere **Visualizza tracce streaming**
   
    ![Visualizzare le tracce di streaming remote][viewremotestreamingtraces]
   
    Ripetere il passaggio 2 per tutti i nodi da cui visualizzare le tracce. Il flusso di ogni nodo verrà visualizzato in una finestra dedicata.
   
    A questo punto è possibile vedere le tracce emesse da Service Fabrice e dai propri servizi. Per filtrare gli eventi in modo da visualizzare solo una specifica applicazione, è sufficiente immettere il nome dell'applicazione nel filtro.
   
    ![Visualizzazione delle tracce di streaming][viewingstreamingtraces]
3. Al termine dello streaming delle tracce dal cluster, è possibile disabilitare le tracce di streaming remote facendo clic con il pulsante destro del mouse sul cluster in **Cloud Explorer** e scegliere **Disabilita tracce streaming**
   
    ![Disabilitare le tracce di streaming remote][disablestreamingtraces]

## <a name="next-steps"></a>Passaggi successivi
* [Testare un servizio di Service Fabric](service-fabric-testability-overview.md)
* [Gestione delle applicazioni di Service Fabric in Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png

