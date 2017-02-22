---
title: Risoluzione dei problemi con la traccia di eventi | Documentazione Microsoft
description: "I problemi più comuni riscontrati durante la distribuzione dei servizi nell’infrastruttura di servizi di Microsoft Azure."
services: service-fabric
documentationcenter: .net
author: mattrowmsft
manager: timlt
editor: 
ms.assetid: 5eb8ef21-da04-4ac8-8b9a-5f7ff1e0a180
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/31/2016
ms.author: mattrow
translationtype: Human Translation
ms.sourcegitcommit: c9730b553e59d12b8720bbf3a06cc956912e27de
ms.openlocfilehash: c920a206d6f3288f6cba0d2658d556749fc4d574


---
# <a name="troubleshoot-common-issues-when-you-deploy-services-on-azure-service-fabric"></a>Risolvere i problemi comuni quando si distribuiscono servizi in Azure Service Fabric
Durante l'esecuzione di servizi nel computer di sviluppo è facile usare [Strumenti di debug di Visual Studio](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md). Per i cluster remoti, [i rapporti di integrità](service-fabric-view-entities-aggregated-health.md) sono sempre un buon punto di partenza. I modi più semplici per accedere a questi report sono quelli che avvengono tramite PowerShell o [SFX](service-fabric-visualizing-your-cluster.md). In questo articolo si presuppone che si stia eseguendo il debug di un cluster remoto e una conoscenza di base dell'utilizzo di questi strumenti.

## <a name="application-crash"></a>Arresto anomalo dell’applicazione
Il report 'La partizione è inferiore rispetto al conteggio delle repliche di destinazione o dell’istanza ' è una valida indicazione del fatto che il servizio si sta arrestando in modo anomalo. Per scoprire dove il servizio si arresta in modo anomalo è necessaria un’ulteriore indagine. Quando si esegue su vasta scala, sarà necessario un set di tracce ben studiate.  È consigliabile provare [Diagnostica di Azure](service-fabric-diagnostics-how-to-setup-wad.md) per raccogliere tali tracce e usare una soluzione come [Azure Application Insights](https://azure.microsoft.com/services/application-insights/) per visualizzare ed eseguire ricerche nelle tracce.

![Integrità della partizione SFX](./media/service-fabric-diagnostics-troubleshoot-common-scenarios/crashNewApp.png)

### <a name="during-service-or-actor-initialization"></a>Durante l'inizializzazione dell’attore o del servizio
Tutte le eccezioni prima che il tipo di servizio venga inizializzato causeranno l'arresto anomalo del processo. Per questi tipi di arresti anomali il log eventi dell’applicazione visualizzerà l'errore dal servizio.
Si tratta delle eccezioni più comuni che vengono visualizzate prima dell’inizializzazione del servizio.

***System.IO.FileNotFoundException***

Questo errore è spesso dovute a delle dipendenze mancanti dell’assembly. Controllare la proprietà CopyLocal in Visual Studio o la Global Assembly Cache per il nodo.

***System.Runtime.InteropServices.COMException***
 *at System.Fabric.Interop.NativeRuntime+IFabricRuntime.RegisterStatefulServiceFactory(IntPtr, IFabricStatefulServiceFactory)*

 Indica che il nome del tipo di servizio registrato non corrisponde con il manifesto del servizio.

[Diagnostica di Azure](service-fabric-diagnostics-how-to-setup-wad.md) può essere configurato per caricare automaticamente il log eventi dell'applicazione per tutti i nodi.

### <a name="runasync-or-onactivateasync"></a>RunAsync() or OnActivateAsync()
Se si verifica l'arresto anomalo durante l'inizializzazione o l'esecuzione del tipo di servizio registrato o dell’attore, l'eccezione verrà rilevata da Service Fabric di Azure. È possibile visualizzarli dai provider EventSource descritti in modo dettagliato nella sezione “Passaggi successivi”.

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sulla diagnostica esistente fornita dall'infrastruttura di servizi:

* [Diagnostica di Reliable actors](service-fabric-reliable-actors-diagnostics.md)
* [Diagnostica di Reliable Services](service-fabric-reliable-services-diagnostics.md)




<!--HONumber=Jan17_HO2-->


