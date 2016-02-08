<properties
	pageTitle="Panoramica dei livelli di prezzo di messaggistica Standard e Premium del bus di servizio | Microsoft Azure"
	description="Messaggistica Standard e Premium del bus di servizio"
	services="service-bus"
	documentationCenter=".net"
	authors="djrosanova"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="darosa"/>

# Livelli di messaggistica Standard e Premium del bus di servizio 

La messaggistica negoziata del bus di servizio, che include entità di messaggistica come code e argomenti, combina funzionalità di messaggistica aziendali con una semantica di pubblicazione/sottoscrizione a livello cloud. La messaggistica negoziata del bus di servizio viene usata come backbone di comunicazione per molte soluzioni cloud di alto livello.

Con l'introduzione del livello di messaggistica *Premium* del bus di servizio, è possibile soddisfare le più comuni richieste del cliente in termini di scalabilità, prestazioni e disponibilità per le applicazioni mission-critical. Anche se i set di funzionalità sono quasi identici, questi due livelli di messaggistica del bus di servizio di Azure sono progettati per soddisfare diversi casi d'uso.

Nella tabella seguente sono evidenziate alcune differenze di alto livello.

| Premium | Standard |
|---------------------------------------|--------------------------------|
| Velocità effettiva elevata | Velocità effettiva variabile |
| Prestazioni prevedibili | Latenza variabile |
| Prezzi prevedibili | Prezzi variabili in base all'uso |
| Possibilità di adattare il carico di lavoro | N/D |

La **messaggistica di livello Premium del bus di servizio di Azure** fornisce l'isolamento delle risorse a livello di CPU e memoria in modo che ogni carico di lavoro del cliente venga eseguito in isolamento. Questo contenitore di risorse viene chiamato *unità di messaggistica*. Ad ogni spazio dei nomi Premium viene allocata almeno un'unità di messaggistica. È possibile acquistare 1, 2 o 4 unità di messaggistica per ogni spazio dei nomi Premium del bus di servizio. Un singolo carico di lavoro o un'entità può estendersi su più unità di messaggistica e il numero di unità di messaggistica può essere modificato in base alle esigenze, anche se la fatturazione prevede un addebito a frequenza giornaliera o a 24 ore. Ne risultano prestazioni prevedibili e ripetibili per la soluzione basata sul bus di servizio.

Non solo le prestazioni sono più prevedibili e disponibili, ma anche più veloci. La messaggistica Premium del bus di servizio di Azure crea il motore di archiviazione introdotto in [Hub eventi di Azure](https://azure.microsoft.com/services/event-hubs/). Con la messaggistica Premium, le prestazioni massime sono più rapide rispetto al livello Standard.

## Differenze tecniche della messaggistica Premium

Di seguito sono descritte alcune differenze tra i livelli di messaggistica Standard e Premium.

### Entità partizionate

Le entità partizionate sono supportate nella messaggistica Premium, ma non funzionano esattamente come nei livelli di messaggistica Standard e Basic del bus di servizio. La messaggistica Premium non usa SQL come archivio dati e non ha più la competizione possibile tra le risorse associata a una piattaforma condivisa. Di conseguenza, il partizionamento non è necessario. Inoltre, il numero delle partizioni è stato modificato da 16 partizioni nella messaggistica Standard a due partizioni in quella Premium. La presenza di due partizioni garantisce disponibilità e un numero più appropriato per l'ambiente di runtime Premium. Per altre informazioni, vedere [Partizionamento delle entità di messaggistica](service-bus-partitioning.md).

### Entità Express

Poiché è in esecuzione in un ambiente di runtime completamente isolato, le entità Express non sono più necessarie nella messaggistica Premium. Di conseguenza, le entità Express non sono supportate negli spazi dei nomi Premium. Per altre informazioni sulla funzionalità Express, vedere la proprietà [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx).

## Passaggi successivi

Per ulteriori informazioni sulla messaggistica del bus di servizio, vedere gli argomenti seguenti.

- [Introduzione alla messaggistica Premium del bus di servizio di Azure (post di blog)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introduzione alla messaggistica Premium del bus di servizio di Azure (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Panoramica della messaggistica del bus di servizio](service-bus-messaging-overview.md)
- [Panoramica dell'architettura del bus di servizio di Azure](service-bus-fundamentals-hybrid-solutions.md)
- [Come usare le code del bus di servizio](service-bus-dotnet-how-to-use-queues.md)

<!---HONumber=AcomDC_0128_2016-->