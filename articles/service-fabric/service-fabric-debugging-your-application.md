<properties
   pageTitle="Debug dell’applicazione in Visual Studio | Microsoft Azure"
   description="Migliorare l'affidabilità e le prestazioni dei servizi sviluppandoli ed eseguendone il debug in Visual Studio all'interno di un cluster di sviluppo locale."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="01/28/2016"
   ms.author="jesseb"/>

# Debug dell'applicazione di Service Fabric mediante Visual Studio

Per risparmiare tempo e denaro, è possibile distribuire l'applicazione di Service Fabric ed eseguirne il debug in un cluster di sviluppo locale. Visual Studio può distribuire l'applicazione nel cluster locale e connettere automaticamente il debugger a tutte le istanze dell'applicazione.

1. Avviare un cluster di sviluppo locale seguendo la procedura descritta nell'articolo [Configurazione dell'ambiente di sviluppo di Service Fabric](service-fabric-get-started.md).

2. Premere **F5** oppure fare clic su **Debug** > **Avvia debug**.

    ![Avviare il debug di un'applicazione][startdebugging]

3. Impostare i punti di interruzione nel codice ed eseguire l'applicazione un'istruzione alla volta scegliendo i comandi dal menu **Debug**.

    > [AZURE.NOTE] Visual Studio si connette a tutte le istanze dell'applicazione. Mentre il codice viene eseguito un'istruzione alla volta, i punti di interruzione possono essere raggiunti da più processi, dando luogo a sessioni simultanee. Provare a disabilitare i punti di interruzione dopo che sono stati raggiunti rendendoli condizionali in base all'ID del thread, oppure usando gli eventi di diagnostica.

4. La finestra degli **Eventi di diagnostica** si aprirà automaticamente in modo da poter visualizzare gli eventi diagnostici in tempo reale.

    ![Visualizzare gli eventi diagnostici in tempo reale][diagnosticevents]

5. È possibile aprire la finestra degli **Eventi di diagnostica** anche in Esplora server. In **Azure**, fare clic con il pulsante destro del mouse su **Service Fabric Cluster** > **View Diagnostic Events**.

    ![Aprire la finestra degli eventi di diagnostica][viewdiagnosticevents]

6. Gli eventi diagnostici possono essere visualizzati nel file **ServiceEventSource.cs** generato automaticamente e vengono chiamati dal codice dell'applicazione.

    ```csharp
    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, Service.ServiceTypeName);
    ```

7. Nella finestra degli eventi di diagnostica è possibile filtrare, sospendere ed esaminare gli eventi in tempo reale. Il filtro è una semplice ricerca di stringhe del messaggio dell'evento, incluso il relativo contenuto.

    ![Filtrare, sospendere e riprendere o esaminare gli eventi in tempo reale][diagnosticeventsactions]

8. Il debug dei servizi è analogo al debug di qualsiasi altra applicazione. I punti di interruzione possono essere impostati normalmente tramite Visual Studio per un semplice debug. Anche se le raccolte Reliable Collections vengono replicate tra più nodi, implementano comunque IEnumerable. Ciò significa che è possibile utilizzare la visualizzazione dei risultati in Visual Studio durante il debug per vedere cosa è stato memorizzato all'interno. È sufficiente impostare un punto di interruzione in qualsiasi posizione all'interno del codice.

    ![Avviare il debug di un'applicazione][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## Passaggi successivi

- [Testare un servizio di Service Fabric](service-fabric-testability-overview.md)
- [Gestione delle applicazioni di Service Fabric in Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png

<!---HONumber=AcomDC_0204_2016-->