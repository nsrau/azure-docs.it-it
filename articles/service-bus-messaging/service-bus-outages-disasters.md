---
title: Isolamento delle applicazioni del bus di servizio di Azure da interruzioni ed emergenze del servizio | Documentazione Microsoft
description: Tecniche per proteggere le applicazioni da potenziali interruzioni del bus di servizio.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: 24611e265788cf046aa0733bc423917aaf305427
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60003021"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Procedure consigliate per isolare le applicazioni del bus di servizio da interruzioni ed emergenze del servizio

Le applicazioni criciali devono funzionare in modo continuo, anche in presenza di interruzioni impreviste o situazioni di emergenza. Questo articolo descrive le tecniche che è possibile usare per proteggere le applicazioni del bus di servizio da una potenziale emergenza o interruzione del servizio.

Il termine "interruzione" indica la temporanea indisponibilità del bus di servizio di Azure. Un'interruzione può interessare alcuni componenti del bus di servizio, ad esempio un archivio di messaggistica, o anche l'intero data center. Una volta risolto il problema, il bus di servizio torna di nuovo disponibile. In genere, un'interruzione non determina la perdita di messaggi o di altri dati. Un esempio di errore di un componente è la mancata disponibilità di un particolare archivio di messaggistica. Un esempio di interruzione a livello di data center è un'interruzione dell'alimentazione o il guasto di un commutatore di rete. Un'interruzione può durare da pochi minuti ad alcuni giorni.

Il termine "emergenza" indica la perdita permanente di un'unità di scala del bus di servizio o di un data center. È possibile che il data center non torni di nuovo disponibile. In genere, un'emergenza determina la perdita di alcuni o di tutti i messaggi o di altri dati. Un'emergenza può essere causata, ad esempio, da un incendio, un'inondazione o un terremoto.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Protezione da emergenze e interruzioni - bus di servizio Premium
I concetti di disponibilità elevata e ripristino di emergenza sono integrati nel livello Premium del bus di servizio di Azure, sia all'interno della stessa area (tramite le zone di disponibilità) che tra aree diverse (tramite il ripristino di emergenza geografico).

### <a name="geo-disaster-recovery"></a>Ripristino di emergenza geografico

Il bus di servizio Premium supporta il ripristino di emergenza geografico a livello di spazio dei nomi. Per altre informazioni, vedere [Ripristino di emergenza geografico per il bus di servizio di Azure](service-bus-geo-dr.md). La funzionalità di ripristino di emergenza, disponibile solo per lo [SKU Premium](service-bus-premium-messaging.md), implementa il ripristino di emergenza dei metadati e si basa sugli spazi dei nomi primari e secondari del ripristino di emergenza.

### <a name="availability-zones"></a>Zone di disponibilità

Lo SKU Premium del bus di servizio supporta le [zone di disponibilità](../availability-zones/az-overview.md) fornendo località con isolamento di errore all'interno della stessa area di Azure.

> [!NOTE]
> Il supporto per le zone di disponibilità per il bus di servizio di Azure Premium è disponibile solo nelle [aree di Azure](../availability-zones/az-overview.md#services-support-by-region) in cui sono presenti le zone di disponibilità.

Usando il portale di Azure, è possibile abilitare le zone di disponibilità solo negli spazi dei nomi. Il bus di servizio non supporta la migrazione degli spazi dei nomi esistenti. Non è possibile disabilitare la ridondanza della zona dopo che è stata abilitata nello spazio dei nomi.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Protezione da emergenze e interruzioni - bus di servizio standard
Per ottenere resilienza in caso di possibili interruzioni dei data center durante l'uso del piano tariffario per la messaggistica standard, il bus di servizio supporta due approcci: replica *attiva* e replica *passiva*. Per ogni approccio, se una coda o un argomento specifico deve rimanere accessibile in presenza di un'interruzione del data center, è possibile creare tale coda o argomento in entrambi gli spazi dei nomi. Entrambe le entità possono avere lo stesso nome. Ad esempio, una coda primaria può essere raggiunta in **contosoPrimary.servicebus.windows.net/myQueue**, mentre la rispettiva coda secondaria può essere raggiunta in **contosoSecondary.servicebus.windows.net/myQueue**.

>[!NOTE]
> La configurazione di **replica attiva** e **replica passiva** sono soluzioni per utilizzo generico e non funzionalità specifiche del bus di servizio. La logica di replica (con l'invio a 2 spazi dei nomi diversi) si trova nelle applicazioni mittente e il ricevente deve avere la logica personalizzata per il rilevamento dei duplicati.

Se l'applicazione non richiede una comunicazione mittente-ricevitore permanente, può implementare una coda permanente sul lato client in modo da impedire la perdita di messaggi e proteggere il mittente da eventuali errori temporanei del bus di servizio.

### <a name="active-replication"></a>Replica attiva
La replica attiva usa entità in entrambi gli spazi dei nomi per ogni operazione. Ogni client invia sempre due copie di un messaggio. La prima viene inviata all'entità primaria, ad esempio **contosoPrimary.servicebus.windows.net/sales**, la seconda all'entità secondaria, ad esempio **contosoSecondary.servicebus.windows.net/sales**.

Un client riceve messaggi da entrambe le code. Il ricevitore elabora la prima copia di un messaggio ed elimina la seconda. Per eliminare i messaggi duplicati, il mittente deve contrassegnare ogni messaggio con un identificatore univoco. Entrambe le copie del messaggio devono essere contrassegnate con lo stesso identificatore. Per contrassegnare il messaggio, è possibile usare la proprietà [BrokeredMessage.MessageId][BrokeredMessage.MessageId] o [BrokeredMessage.Label][BrokeredMessage.Label] oppure una proprietà personalizzata. Il ricevitore deve mantenere l'elenco dei messaggi già ricevuti.

L'esempio relativo alla [replica geografica con il livello standard del bus di servizio][Geo-replication with Service Bus Standard Tier] illustra la replica attiva delle entità di messaggistica.

> [!NOTE]
> Nella replica attiva il numero delle operazioni raddoppia. Di conseguenza, questo approccio può comportare costi più elevati.
> 
> 

### <a name="passive-replication"></a>Replica passiva
Quando non si verificano errori, nella replica passiva viene usata solo una delle due entità di messaggistica. Un client invia il messaggio all'entità attiva. Se l'operazione sull'entità attiva non riesce e viene restituito un codice di errore per segnalare che il data center che ospita l'entità attiva potrebbe non essere disponibile, il client invia una copia del messaggio all'entità di backup. A quel punto, l'entità attiva e quella di backup si scambiano i ruoli: il client mittente considera l'entità attiva precedente come nuova entità di backup e l'entità di backup precedente come nuova entità attiva. Se entrambe le operazioni di invio hanno esito negativo, i ruoli delle due entità rimangono invariati e viene restituito un errore.

Un client riceve messaggi da entrambe le code. Poiché potrebbe ricevere due copie dello stesso messaggio, il ricevitore deve eliminare i messaggi duplicati. È possibile eliminare i duplicati nel modo descritto per la replica attiva.

In genere, la replica passiva è più vantaggiosa di quella attiva perché nella maggior parte dei casi viene eseguita una sola operazione. La latenza, la velocità effettiva e il costo sono identici a quelli di uno scenario non replicato.

Quando si usa la replica passiva, negli scenari seguenti è possibile che i messaggi vengano persi o ricevuti due volte.

* **Ritardo o perdita del messaggio**: si supponga che il mittente abbia inviato con esito positivo un messaggio m1 alla coda primaria e che quindi la coda diventi non disponibile prima della ricezione di m1 da parte del ricevitore. Il mittente invia un secondo messaggio m2 alla coda secondaria. Se la coda primaria è temporaneamente non disponibile, m1 viene ricevuto solo quando la coda torna nuovamente disponibile. In caso di emergenza, il destinatario potrebbe non ricevere mai m1.
* **Ricezione di duplicati**: si supponga che il mittente invii un messaggio m alla coda primaria. Il bus di servizio elabora m ma non riesce a inviare una risposta. Dopo il timeout dell'operazione di invio, il mittente invia una copia identica di m alla coda secondaria. Se la prima copia di m viene ricevuta prima che la coda primaria diventi non disponibile, le due copie di m verranno ricevute quasi contemporaneamente. Se invece la prima copia di m non viene ricevuta prima che la coda primaria diventi non disponibile, il ricevitore riceverà inizialmente solo la seconda copia di m. L'altra copia di m verrà ricevuta quando la coda primaria diventerà disponibile.

L'esempio relativo alla [replica geografica con il livello standard del bus di servizio][Geo-replication with Service Bus Standard Tier] illustra la replica passiva delle entità di messaggistica.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Protezione degli endpoint di inoltro da interruzioni o emergenze dei data center
La replica geografica degli endpoint di inoltro consente a un servizio che espone un endpoint di inoltro di essere raggiungibile in presenza di interruzioni del bus di servizio. Per ottenere la replica geografica, il servizio deve creare due endpoint di inoltro in diverse istanze di spazio dei nomi servizio. Le due istanze di spazio dei nomi servizio devono trovarsi in data center diversi e i due endpoint devono avere nomi differenti. Ad esempio, un endpoint primario può essere raggiunto in **contosoPrimary.servicebus.windows.net/myPrimaryService**, mentre il corrispettivo endpoint secondario può essere raggiunto in **contosoSecondary.servicebus.windows.net/mySecondaryService**.

Il servizio rimane quindi in ascolto su entrambi gli endpoint e un client può richiamare il servizio tramite l'uno o l'altro. Un'applicazione client seleziona casualmente uno degli inoltri come endpoint primario e invia la richiesta all'endpoint attivo. Se l'operazione ha esito negativo e viene visualizzato un codice di errore, significa che l'endpoint di inoltro non è disponibile. L'applicazione apre un canale per l'endpoint di backup e invia nuovamente la richiesta. A quel punto, l'endpoint attivo e quello di backup si scambiano i ruoli: l'applicazione client considera l'endpoint attivo precedente come nuovo endpoint di backup e l'endpoint di backup precedente come nuovo endpoint attivo. Se entrambe le operazioni di invio hanno esito negativo, i ruoli delle due entità rimangono invariati e viene restituito un errore.

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sul ripristino di emergenza, vedere gli articoli seguenti:

* [Ripristino di emergenza geografico per il bus di servizio di Azure](service-bus-geo-dr.md)
* [Continuità aziendale del database SQL di Azure][Azure SQL Database Business Continuity]
* [Progettazione di applicazioni resilienti per Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png
