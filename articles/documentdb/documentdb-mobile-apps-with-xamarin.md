---
title: Creare applicazioni per dispositivi mobili con Xamarin e DocumentDB | Documentazione Microsoft
description: "Esercitazione in cui si crea un&quot;applicazione Xamarin iOS, Android o Form usando Azure DocumentDB. DocumentDB è un database su cloud per dispositivi mobili veloce e dotato di copertura globale."
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
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 3e058505f7c17d19d2cebca053badb3505a00f13
ms.lasthandoff: 03/28/2017


---
# <a name="build-mobile-applications-with-xamarin-and-azure-documentdb"></a>Creare applicazioni per dispositivi mobili con Xamarin e DocumentDB
Molte app per dispositivi mobili devono archiviare i dati nel cloud e Azure DocumentDB è appunto un database su cloud per le app per dispositivi mobili. Include tutto ciò di cui uno sviluppatore per dispositivi mobili ha bisogno. È un database NoSQL completamente gestito come un servizio che offre scalabilità su richiesta. È capace di inserire i dati all'applicazione in modo trasparente, ovunque si trovino gli utenti, in tutto il mondo. Usando [Azure DocumentDB .NET Core SDK](documentdb-sdk-dotnet-core.md) è possibile abilitare le app Xamarin per dispositivi mobili a interagire direttamente con DocumentDB, senza un livello intermedio.

Questo articolo contiene un'esercitazione per la creazione di app per dispositivi mobili con Xamarin e DocumentDB. È possibile trovare il codice sorgente completo per l'esercitazione in [Xamarin e DocumentDB su Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin), incluse le modalità per gestire utenti e autorizzazioni.

## <a name="documentdb-capabilities-for-mobile-apps"></a>Capacità di DocumentDB per le app per dispositivi mobili
DocumentDB offre le seguenti funzionalità principali per gli sviluppatori di app per dispositivi mobili:

![Capacità di DocumentDB per le app per dispositivi mobili](media/documentdb-mobile-apps-with-xamarin/documentdb-for-mobile.png)

* Query avanzate su dati senza schema. DocumentDB archivia i dati come documenti JSON senza schema in insiemi eterogenei. Offre [query avanzate e veloci](documentdb-sql-query.md) senza la necessità di occuparsi di indici o schemi.
* Alta velocità effettiva. Bastano pochi millisecondi per leggere e scrivere documenti con DocumentDB. Gli sviluppatori possono specificare la velocità effettiva di cui hanno bisogno e DocumentDB la rispetta con contratti di servizio al 99,99%.
* Scalabilità senza limiti. Gli insiemi di DocumentDB [crescono al crescere dell'app](documentdb-partition-data.md). È possibile iniziare con dati di piccole dimensioni e velocità effettiva di centinaia di richieste al secondo. Le raccolte possono aumentare fino a petabyte di dati e velocità effettiva di grandi dimensioni con centinaia di milioni di richieste al secondo.
* Distribuzione a livello globale. Gli utenti dell'app per dispositivi mobili viaggiano spesso in tutto il mondo. DocumentDB è un [servizio di database distribuito a livello globale](documentdb-distribute-data-globally.md). Fare clic sulla mappa per rendere i dati accessibili agli utenti.
* Autorizzazioni avanzate integrate. Con DocumentDB è possibile implementare facilmente modelli diffusi come i [dati per utente](https://aka.ms/documentdb-xamarin-todouser) o i dati condivisi fra più utenti senza codice di autorizzazione complesso.
* Query geospaziali. Molte app per dispositivi mobili offrono oggi un'esperienza geografica contestuale. Grazie all'eccellente supporto per i [tipi geospaziali](documentdb-geospatial.md), con DocumentDB è facile creare queste esperienze.
* Allegati binari. I dati dell'app includono spesso BLOB binari. Il supporto nativo per gli allegati facilita l'uso di DocumentDB come unica risorsa per i dati dell'app.

## <a name="documentdb-and-xamarin-tutorial"></a>Esercitazione su DocumentDB e Xamarin
L'esercitazione seguente illustra come creare un'app per dispositivi mobili usando Xamarin e DocumentDB. È possibile trovare il codice sorgente completo per l'esercitazione in [Xamarin e DocumentDB su Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).

### <a name="get-started"></a>Introduzione
Iniziare a usare DocumentDB è facile. Aprire il portale di Azure per creare un account DocumentDB. Fare clic sulla scheda **Avvio rapido**. Scaricare l'esempio di elenco di cose da fare di Xamarin Forms che è già connesso all'account DocumentDB. 

![Avvio rapido di DocumentDB per le app per dispositivi mobili](media/documentdb-mobile-apps-with-xamarin/documentdb-quickstart.png)

Oppure, se si dispone di un'app Xamarin esistente, è possibile aggiungere il [pacchetto DocumentDB NuGet](documentdb-sdk-dotnet-core.md). DocumentDB supporta le librerie condivise Xamarin.IOS, Xamarin.Android e Xamarin Forms.

### <a name="work-with-data"></a>Usare i dati
I record di dati vengono archiviati in DocumentDB come documenti JSON senza schema in insiemi eterogenei. È possibile archiviare documenti con strutture diverse nello stesso insieme:

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

Nei progetti Xamarin è possibile usare Language Integrated Query su dati senza schema:

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
Come molti esempi introduttivi, l'esempio di DocumentDB che è stato scaricato si autentica al servizio usando la chiave master specificata nel codice dell'app. Questa operazione predefinita non è una buona pratica per un'app che si vuole eseguire da qualsiasi posizione che non sia l'emulatore locale. Se un utente non autorizzato ottiene la chiave master, tutti i dati nell'intero account DocumentDB potrebbero essere compromessi. Si desidera invece che l'app abbia accesso solo ai record per l'utente connesso. DocumentDB consente agli sviluppatori di concedere all'applicazione l'autorizzazione in lettura o in lettura/scrittura a un insieme, un set di documenti raggruppati in base a una chiave di partizione o a un documento specifico. 

Seguire questa procedura per modificare l'app dell'elenco attività e trasformarla in un'app di elenco attività multiutente: 

  1. Aggiungere l'accesso all'app tramite Facebook, Active Directory o qualsiasi altro provider.

  2. Creare un insieme DocumentDB UserItems con **/userId** come chiave di partizione. Specificare la chiave di partizione per l'insieme consente a DocumentDB di scalare infinitamente quando il numero degli utenti dell'app cresce, continuando a offrire query veloci.

  3. Aggiungere DocumentDB Resource Token Broker, una semplice API Web che autentica gli utenti ed emette token di breve durata, per gli utenti che hanno eseguito l'accesso, con il solo accesso ai documenti interni alla partizione dell'utente. In questo esempio si ospita Resource Token Broker nel servizio app.

  4. Modificare l'app per l'autenticazione in Resource Token Broker con Facebook e richiedere i token di risorsa per l'utente di Facebook. È quindi possibile accedere ai propri dati nella raccolta UserItems.  

È possibile trovare un esempio di codice completo di questo modello in [Resource Token Broker su Github](http://aka.ms/documentdb-xamarin-todouser). Questo diagramma illustra la soluzione:

![Utenti di DocumentDB e broker delle autorizzazioni](media/documentdb-mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

Se si desidera che due utenti abbiano accesso allo stesso elenco di attività, è necessario aggiungere altre autorizzazioni al token di accesso in Resource Token Broker.

### <a name="scale-on-demand"></a>Scalabilità su richiesta
DocumentDB è un database distribuito come servizio. Anche se la base di utenti aumenta, non è necessario preoccuparsi del provisioning delle VM o della crescita delle memorie centrali. È sufficiente indicare a DocumentDB quante operazioni al secondo (velocità effettiva) sono richieste dall'app. È possibile specificare la velocità effettiva tramite la scheda **Scalabilità** usando una misura della velocità effettiva chiamata unità richiesta (UR) al secondo. Ad esempio un'operazione di lettura su un documento da 1 KB richiede 1 UR. È possibile anche aggiungere avvisi per la metrica **Velocità effettiva** per monitorare l'incremento del traffico e modificare la velocità effettiva a livello di codice quando gli avvisi vengono attivati.

![Scalabilità della velocità effettiva su richiesta di DocumentDB](media/documentdb-mobile-apps-with-xamarin/documentdb-scale.png)

### <a name="go-planet-scale"></a>Passare alla scala globale
Con la diffusione dell'app, si possono acquisire utenti in tutto il mondo. O forse si desidera essere preparati per gli eventi imprevisti. Aprire il portale di Azure per aprire un account DocumentDB. Fare clic sulla mappa per fare in modo che i dati vengano replicati continuamente in un determinato numero di aree geografiche in tutto il mondo. Questa funzionalità rende disponibili i dati, indipendentemente dalla posizione dell'utente. È inoltre possibile aggiungere criteri di failover per prevedere le contingenze.

![Scalabilità fra aree geografiche di DocumentDB](media/documentdb-mobile-apps-with-xamarin/documentdb-replicate-globally.png)

A questo punto La soluzione è stata completata e si dispone di un'app per dispositivi mobili con Xamarin e DocumentDB. Seguire una procedura simile per creare app Cordova usando DocumentDB JavaScript SDK e app native per iOS/Android con le API REST di DocumentDB.

## <a name="next-steps"></a>Passaggi successivi
* Visualizzare il codice sorgente per [Xamarin e DocumentDB su Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin).
* Scaricare [DocumentDB .NET Core SDK](documentdb-sdk-dotnet-core.md).
* Trovare altri esempi di codice per [applicazioni .NET](documentdb-dotnet-samples.md).
* Informazioni sulle [funzionalità di query avanzate di DocumentDB](documentdb-sql-query.md).
* Informazioni sul [supporto geospaziale in DocumentDB](documentdb-geospatial.md).




