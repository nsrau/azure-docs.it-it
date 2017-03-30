---
title: Creare applicazioni per dispositivi mobili con Xamarin e DocumentDB | Documentazione Microsoft
description: "Esercitazione in cui si crea un&quot;applicazione Xamarin iOS, Android o Form usando Azure DocumentDB. DocumentDB è un database su cloud per dispositivi mobili ultraveloce e dotato di copertura globale."
services: documentdb
documentationcenter: .net
author: arramac
manager: monicar
editor: 
ms.assetid: ff97881a-b41a-499d-b7ab-4f394df0e153
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 4a783397b038b7545bbbfc45813db3d9973c5e19
ms.lasthandoff: 03/22/2017


---
# <a name="build-mobile-applications-with-xamarin-and-azure-documentdb"></a>Creare applicazioni per dispositivi mobili con Xamarin e DocumentDB
Molte app per dispositivi mobili devono archiviare i dati nel cloud e Azure DocumentDB è appunto un database su cloud per le app per dispositivi mobili. Include tutto ciò di cui uno sviluppatore per dispositivi mobili ha bisogno. È un database NoSQL distribuito come servizio completamente gestito che offre scalabilità su richiesta e può portare i dati ovunque nel mondo, dove gli utenti si spostano, in modo trasparente all'applicazione. Usando [Azure DocumentDB .NET Core SDK](documentdb-sdk-dotnet-core.md) è possibile abilitare le app Xamarin per dispositivi mobili a interagire direttamente con DocumentDB, senza un livello intermedio.

In questo articolo viene fornita un'esercitazione per la creazione di app per dispositivi mobili con Xamarin e DocumentDB. È possibile trovare il codice sorgente completo per l'esercitazione in [Xamarin e DocumentDB su Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin), incluse le modalità per gestire utenti e autorizzazioni.

## <a name="documentdb-capabilities-for-mobile-apps"></a>Capacità di DocumentDB per le app per dispositivi mobili
DocumentDB offre le seguenti funzionalità principali per gli sviluppatori di app per dispositivi mobili:

![Capacità di DocumentDB per le app per dispositivi mobili](media/documentdb-mobile-apps-with-xamarin/documentdb-for-mobile.png)

* Query avanzate su dati senza schema. DocumentDB archivia i dati come documenti JSON senza schema in insiemi eterogenei e offre [query avanzate e veloci](documentdb-sql-query.md) senza la necessità di preoccuparsi di schema o indici.
* Veloce. Garantito. Bastano pochi millisecondi per leggere e scrivere documenti con DocumentDB. Gli sviluppatori possono specificare la velocità effettiva di cui hanno bisogno e DocumentDB la rispetta con contratti di servizio al 99,99%.
* Scalabilità senza limiti. Gli insiemi di DocumentDB [crescono al crescere dell'app](documentdb-partition-data.md). È possibile iniziare con una dimensione di dati ridotta e centinaia di richieste al secondo per raggiungere dimensioni molto maggiori, decine e centinaia di milioni di richieste al secondo di velocità effettiva e petabyte di dati.
* Distribuzione a livello globale. Gli utenti dell'app per dispositivi mobili viaggiano spesso in tutto il mondo. DocumentDB è un [database distribuito a livello globale](documentdb-distribute-data-globally.md) e con un solo clic su una mappa i dati verranno visualizzati ovunque gli utenti si trovino.
* Autorizzazioni avanzate integrate. Con DocumentDB è possibile implementare facilmente modelli diffusi come i [dati per utente](https://aka.ms/documentdb-xamarin-todouser) o i dati condivisi fra più utenti senza codice di autorizzazione complesso.
* Query geospaziali. Molte app per dispositivi mobili offrono oggi un'esperienza geografica contestuale. Grazie all'eccellente supporto per i [tipi geospaziali](documentdb-geospatial.md), con DocumentDB è facile realizzare queste esperienze.
* Allegati binari. I dati dell'app includono spesso BLOB binari. Il supporto nativo per gli allegati facilita l'uso di DocumentDB come unica risorsa per i dati dell'app.

## <a name="documentdb-and-xamarin-tutorial"></a>Esercitazione su DocumentDB e Xamarin
L'esercitazione seguente illustra come creare un'app per dispositivi mobili usando Xamarin e DocumentDB in cinque semplici passaggi. È possibile trovare il codice sorgente completo per l'esercitazione in [Xamarin e DocumentDB su Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

### <a name="get-started"></a>Introduzione
Iniziare a usare DocumentDB è facile. Passare al portale di Azure, creare un nuovo account DocumentDB, quindi passare alla scheda Guida introduttiva e scaricare un esempio di "elenco di attività" di Xamarin Forms, già connessa all'account DocumentDB. 

![Guida introduttiva a DocumentDB per le app per dispositivi mobili](media/documentdb-mobile-apps-with-xamarin/documentdb-quickstart.png)

Oppure, se si dispone di un'app Xamarin esistente, è possibile aggiungere solo questo [pacchetto DocumentDB NuGet](documentdb-sdk-dotnet-core.md). DocumentDB supporta le librerie condivise Xamarin.IOS, Xamarin.Android e Xamarin Forms.

### <a name="work-with-data"></a>Usare i dati
I record di dati vengono archiviati in DocumentDB come documenti JSON senza schema in insiemi eterogenei. È possibile archiviare documenti con strutture diverse nello stesso insieme.

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

Nei progetti Xamarin è possibile usare query LINQ (Language Integrated Query) su dati senza schema:

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>Aggiungi utenti
Come molti esempi introduttivi, l'esempio di DocumentDB che è stato scaricato si autentica al servizio usando la chiave master specificata nel codice dell'app. Questa non è una buona idea per un'app che sarà eseguita da qualsiasi posizione che non sia l'emulatore locale. Se un utente malintenzionato ottiene la chiave master, tutti i dati nell'intero account DocumentDB saranno compromessi. Si vuole invece che l'app acceda solo ai record per l'utente connesso. DocumentDB consente agli sviluppatori di concedere all'applicazione l'accesso in lettura o in lettura/scrittura a un insieme, un set di documenti raggruppati in base a una chiave di partizione o un documento specifico. 

Ecco ad esempio come modificare l'app dell'elenco di attività in un'app "elenco di attività" per più utenti: 

* Aggiungere l'accesso all'app tramite Facebook, Active Directory o qualsiasi altro provider.
* Creare un insieme DocumentDB UserItems con /userId come chiave di partizione. Specificare la chiave di partizione per l'insieme consente a DocumentDB di scalare infinitamente quando il numero degli utenti dell'app cresce, continuando a offrire query veloci.
* Aggiungere DocumentDB Resource Token Broker, una semplice API Web che autentica gli utenti ed emette token di breve durata, per gli utenti che hanno eseguito l'accesso, con il solo accesso ai documenti interni alla partizione dell'utente. In questo esempio si ospita Resource Token Broker nel servizio app.
* Modificare l'app per l'autenticazione a Resource Token Broker con Facebook e richiedere i token di risorsa per l'utente di Facebook che ha eseguito l'accesso, quindi accedere ai dati dell'utente nell'insieme UserItems.  

È possibile trovare un esempio di codice completo di questo modello in [Resource Token Broker su Github](http://aka.ms/documentdb-xamarin-todouser). Questo diagramma illustra la soluzione:

![Utenti di DocumentDB e broker delle autorizzazioni](media/documentdb-mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

Ora, se si desidera che due utenti possano ottenere l'accesso allo stesso "elenco di attività", è necessario aggiungere altre autorizzazioni al token di accesso in Resource Token Broker.

### <a name="scale-on-demand"></a>Scalabilità su richiesta
DocumentDB è un database distribuito come servizio. Anche se la base di utenti aumenta, non è necessario preoccuparsi del provisioning delle VM o della crescita delle memorie centrali. È sufficiente indicare a DocumentDB quante operazioni al secondo (velocità effettiva) sono richieste dall'app. È possibile specificare la velocità effettiva tramite la scheda Scalabilità del portale usando una misura della velocità effettiva chiamata unità richiesta al secondo (UR). Ad esempio un'operazione di lettura su un documento da 1 KB richiede 1 UR. È possibile anche aggiungere avvisi per la metrica "Velocità effettiva" per monitorare l'incremento del traffico e modificare la velocità effettiva a livello di codice quando gli avvisi vengono attivati.

![Scalabilità della velocità effettiva su richiesta di DocumentDB](media/documentdb-mobile-apps-with-xamarin/documentdb-scale.png)

### <a name="go-planet-scale"></a>Passare alla scala globale
Con la diffusione dell'app, si possono acquisire utenti in tutto il mondo. O semplicemente non si vuole essere presi alla sprovvista se un meteorite colpisce il data center di Azure in cui è stato creato l'insieme di DocumentDB. Passare al portale di Azure, all'account DocumentDB e, con un clic su una mappa, fare in modo che i dati vengano replicati in modo continuo in qualsiasi numero di aree in tutto il mondo. In questo modo i dati saranno disponibili ovunque si trovano gli utenti ed è possibile aggiungere criteri di failover per essere preparati a qualsiasi imprevisto.

![Scalabilità fra aree geografiche di DocumentDB](media/documentdb-mobile-apps-with-xamarin/documentdb-replicate-globally.png)

Congratulazioni. La soluzione è stata completata e si dispone di un'app per dispositivi mobili con Xamarin e DocumentDB. Un modello simile può essere usato nelle app Cordova usando DocumentDB JavaScript SDK e app native per iOS/Android con le API REST di DocumentDB.

## <a name="next-steps"></a>Passaggi successivi
* Visualizzare il codice sorgente per [Xamarin e DocumentDB su Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).
* Scaricare [DocumentDB .NET Core SDK](documentdb-sdk-dotnet-core.md).
* Trovare altri esempi di codice per [applicazioni .NET](documentdb-dotnet-samples.md).
* Informazioni sulle [funzionalità di query avanzate di DocumentDB](documentdb-sql-query.md).
* Informazioni sul [supporto geospaziale in DocumentDB](documentdb-geospatial.md).




