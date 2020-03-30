---
title: Debug your application in Visual Studio
description: Migliorare l'affidabilità e le prestazioni dei servizi sviluppandoli ed eseguendone il debug in Visual Studio all'interno di un cluster di sviluppo locale.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: fff8a19d5643f7ce866c9eb9c57486340b6f8a50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77624128"
---
# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Debug dell'applicazione di Service Fabric mediante Visual Studio
> [!div class="op_single_selector"]
> * [Visual Studio/CSharp](service-fabric-debugging-your-application.md) 
> * [Eclipse/Java](service-fabric-debugging-your-application-java.md)
>


## <a name="debug-a-local-service-fabric-application"></a>Eseguire il debug di un'applicazione di Service Fabric
Per risparmiare tempo e denaro, è possibile distribuire l'applicazione di Service Fabric ed eseguirne il debug in un cluster di sviluppo locale. Visual Studio 2019 o 2015 può distribuire l'applicazione nel cluster locale e connettere automaticamente il debugger a tutte le istanze dell'applicazione. Visual Studio deve essere eseguito come amministratore per connettere il debugger.

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
5. È possibile aprire la finestra **degli** eventi di diagnostica anche in Cloud Explorer.  In **Service Fabric** fare clic con il pulsante destro del mouse e scegliere **Visualizza tracce streaming**.
   
    ![Aprire la finestra degli eventi di diagnostica][viewdiagnosticevents]
   
    Se si desidera filtrare le tracce in base a un servizio o a un'applicazione specifica, abilitare le tracce di flusso in tale servizio o applicazione specifica.
6. Gli eventi diagnostici possono essere visualizzati nel file **ServiceEventSource.cs** generato automaticamente e vengono chiamati dal codice dell'applicazione.
   
    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```
7. Nella finestra **degli**eventi di diagnostica è possibile filtrare, sospendere ed esaminare eventi in tempo reale.  Il filtro è una semplice ricerca di stringhe del messaggio dell'evento, incluso il relativo contenuto.
   
    ![Filtrare, sospendere e riprendere o esaminare eventi in tempo reale][diagnosticeventsactions]
8. Il debug dei servizi è come il debug di qualsiasi altra applicazione. In genere si impostaNo punti di interruzione tramite Visual Studio per un facile debug. Anche se le raccolte Reliable Collections vengono replicate in più nodi, implementano comunque IEnumerable. Questa implementazione significa che è possibile utilizzare la visualizzazione risultati in Visual Studio durante il debug per vedere ciò che è stato archiviato all'interno. A tale scopo, impostare un punto di interruzione in qualsiasi punto del codice.
   
    ![Avviare il debug di un'applicazione][breakpoint]


### <a name="running-a-script-as-part-of-debugging"></a>Esecuzione di uno script come parte del debug
In alcuni scenari potrebbe essere necessario eseguire uno script come parte dell'avvio di una sessione di debug (ad esempio quando non si utilizzano i servizi predefiniti).

In Visual Studio è possibile aggiungere un file denominato **Start-Service.ps1** nella cartella **Scripts** del progetto Service Fabric Application (con estensione sfproj). Questo script verrà richiamato dopo la creazione dell'applicazione nel cluster locale.


<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Eseguire il debug di un'applicazione remota di Service Fabric
Se le applicazioni di Service Fabric sono in esecuzione in un cluster di Service Fabric in Azure, è possibile eseguire il debug di queste applicazioni in remoto direttamente da Visual Studio.If your Service Fabric applications are running on a Service Fabric cluster in Azure, you can remotely debug these applications, directly from Visual Studio.

> [!NOTE]
> La funzionalità richiede [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [Azure SDK per .NET 2.9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [!WARNING]
> Il debug remoto è progettato per scenari di sviluppo e test e non deve essere usato in ambienti di produzione a causa dell'impatto sulle applicazioni in esecuzione.

1. Passare al cluster in **Cloud Explorer**. Fare clic con il **pulsante destro del** mouse e scegliere Abilita debug
   
    ![Abilitare il debug remoto][enableremotedebugging]
   
    Questa azione avvierà il processo di abilitazione dell'estensione per il debug remoto nei nodi del cluster e nelle configurazioni di rete necessarie.
2. Fare clic con il pulsante destro del mouse sul nodo del cluster in **Cloud Explorer** e scegliere **Associa debugger**
   
    ![Collega debugger][attachdebugger]
3. Nella finestra di dialogo **Associa a processo** scegliere il processo di cui si vuole eseguire il debug e fare clic su **Collega**
   
    ![Scegliere il processo][chooseprocess]
   
    Il nome del processo a cui connettersi corrisponde al nome dell'assembly del progetto del servizio.
   
    Il debugger si connetterà a tutti i nodi che eseguono il processo.
   
   * Nel caso in cui si esegue il debug di un servizio senza stato, tutte le istanze del servizio in tutti i nodi fanno parte della sessione di debug.
   * Se si esegue il debug di un servizio con stato, solo la replica primaria di qualsiasi partizione sarà attiva e pertanto rilevata dal debugger. Se la replica primaria viene spostata durante la sessione di debug, l'elaborazione di quella replica farà ancora parte della sessione di debug.
   * Per intercettare solo partizioni o istanze rilevanti di un determinato servizio, è possibile usare punti di interruzione condizionali per interrompere solo una partizione o un'istanza specifica.
     
     ![Punto di interruzione condizionale][conditionalbreakpoint]
     
     > [!NOTE]
     > Attualmente non è supportato il debug di un cluster di Service Fabric con più istanze dello stesso nome di eseguibile del servizio.
     > 
     > 
4. Al termine del debug dell'applicazione è possibile disabilitare l'estensione di debug remoto facendo clic con il pulsante destro del mouse sul cluster in **Cloud Explorer** e scegliendo **Disabilita debug**
   
    ![Disabilitare il debug remoto][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Streaming delle tracce da un nodo del cluster remoto
È anche possibile trasmettere le tracce direttamente da un nodo del cluster remoto a Visual Studio.You're also able to stream traces directly from a remote cluster node to Visual Studio. Questa funzionalità consente di eseguire lo streaming degli eventi di traccia ETW, generati su un nodo del cluster di Service Fabric.

> [!NOTE]
> La funzionalità richiede [Service Fabric SDK 2.0](https://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [Azure SDK per .NET 2.9](https://azure.microsoft.com/downloads/).
> Questa funzionalità supporta solo i cluster in esecuzione in Azure.
> 
> 

<!-- -->
> [!WARNING]
> Lo streaming delle tracce è progettato per scenari di sviluppo e test e non deve essere usato in ambienti di produzione a causa dell'impatto sulle applicazioni in esecuzione.
> In uno scenario di produzione è consigliabile basarsi sugli eventi di inoltro usando Diagnostica di Azure.

1. Passare al cluster in **Cloud Explorer**. Fare clic con il **pulsante destro del** mouse e scegliere Abilita tracce di streaming
   
    ![Abilitare le tracce di streaming remote][enablestreamingtraces]
   
    Questa azione avvierà il processo di abilitazione dell'estensione delle tracce di streaming nei nodi del cluster, nonché le configurazioni di rete necessarie.
2. Espandere l'elemento **Nodi** in **Cloud Explorer**, fare clic con il pulsante destro del mouse sul nodo da cui trasmettere le tracce e scegliere **Visualizza tracce streaming**
   
    ![Visualizzare le tracce di streaming remote][viewremotestreamingtraces]
   
    Ripetere il passaggio 2 per tutti i nodi da cui visualizzare le tracce. Il flusso di ogni nodo verrà visualizzato in una finestra dedicata.
   
    A questo punto è possibile vedere le tracce emesse da Service Fabrice e dai propri servizi. Per filtrare gli eventi in modo da visualizzare solo una specifica applicazione, è sufficiente immettere il nome dell'applicazione nel filtro.
   
    ![Visualizzazione delle tracce di streaming][viewingstreamingtraces]
3. Al termine dello streaming delle tracce dal cluster, è possibile disabilitare le tracce di streaming remote facendo clic con il pulsante destro del mouse sul cluster in **Cloud Explorer** e scegliere **Disabilita tracce streaming**
   
    ![Disabilitare le tracce di streaming remote][disablestreamingtraces]

## <a name="next-steps"></a>Passaggi successivi
* [Testare un servizio Service Fabric](service-fabric-testability-overview.md).
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
