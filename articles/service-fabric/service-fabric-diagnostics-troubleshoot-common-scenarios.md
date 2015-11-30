<properties
   pageTitle="Risoluzione dei problemi comuni | Microsoft Azure"
   description="I problemi più comuni riscontrati durante la distribuzione dei servizi nell’infrastruttura di servizi di Microsoft Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="mattrowmsft"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/10/2015"
   ms.author="mattrow"/>


# Risoluzione dei problemi comuni
Durante l'esecuzione di servizi nel computer di sviluppo è facile usare [Strumenti di debug di Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Per i cluster remoti, [i rapporti di integrità](service-fabric-view-entities-aggregated-health.md) sono sempre un buon punto di partenza. I modi più semplici per accedere a questi report sono quelli che avvengono tramite powershell o [SFX](service-fabric-visualizing-your-cluster.md). In questo articolo si presuppone che si stia eseguendo il debug di un cluster remoto e una conoscenza di base dell'utilizzo di questi strumenti.

##Arresto anomalo dell’applicazione
Il report 'La partizione è inferiore rispetto al conteggio delle repliche di destinazione o dell’istanza ' è una valida indicazione del fatto che il servizio si sta arrestando in modo anomalo. Per scoprire dove il servizio si arresta in modo anomalo è necessaria un’ulteriore indagine. Quando si esegue su vasta scala, il migliore amico sarà un set di tracce ben studiate. È consigliabile provare [WAD](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md) per la raccolta e la visualizzazione di tali tracce.

![Integrità della partizione SFX](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

###Durante l'inizializzazione dell’Attore o del Servizio
Tutte le eccezioni prima che il tipo di servizio venga inizializzato causeranno l'arresto anomalo del processo. Per questi tipi di arresti anomali nel Log eventi dell’Applicazione verrà visualizzato l'errore dal servizio. Si tratta delle eccezioni più comuni che vengono visualizzate prima dell’inizializzazione del servizio.

| Errore | Descrizione |
| --- | --- |
| System.IO.FileNotFoundException | Sono spesso dovute a delle dipendenze mancanti dell’assembly. Controllare la proprietà CopyLocal in Visual Studio o la GAC (Global Assembly Cache) per il nodo.
| System.Runtime.InteropServices.COMException at System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory(IntPtr, IFabricStatefulServiceFactory)|Indica che il nome del tipo di servizio registrato non corrisponde con il manifesto del servizio. |

[WAD](service-fabric-diagnostics-how-to-setup-wad-operational-insights.md) può essere configurato per caricare automaticamente il Log eventi dell'applicazione per tutti i nodi.

###RunAsync() or OnActivateAsync()
Se si verifica l'arresto anomalo durante l'inizializzazione o l'esecuzione del tipo di servizio registrato o dell’attore, l'eccezione verrà rilevata dall'infrastruttura di servizi. È possibile visualizzarli dai provider EventSource descritti in modo dettagliato nei passaggi successivi.

## Passaggi successivi

Altre informazioni sulla diagnostica esistente fornita dall'infrastruttura di servizi:

* [Diagnostica degli attori](service-fabric-reliable-actors-diagnostics.md)
* [Diagnostica di Reliable Services](service-fabric-reliable-services-diagnostics.md)

<!---HONumber=Nov15_HO4-->