---
title: Esempi .NET
titleSuffix: Azure Cognitive Search
description: Trovare esempi di codice C# di Azure ricerca cognitiva demo che usano le librerie client .NET.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/17/2020
ms.openlocfilehash: d068365cc8197a579c0b043d3fff2da3d54eb803
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94687093"
---
# <a name="net-c-code-samples-for-azure-cognitive-search"></a>Esempi di codice .NET (C#) per Azure ricerca cognitiva

Informazioni sugli esempi di codice C# che illustrano le funzionalità e le funzionalità di Azure ricerca cognitiva. I repository primari sono i seguenti:

| Archivio | Descrizione |
|------------|-------------|
| [Azure-SDK-for-NET/SDK/Search/Azure.Search.Documents/Samples/](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/samples) | Esempi prodotti dal team di Azure SDK forniti con la libreria client di Azure.Search.Documents nell'SDK. È anche possibile esaminare gli [unit test](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/search/Azure.Search.Documents/tests) per la libreria client per vedere come vengono chiamate le varie API. |
| [Azure-Samples/Azure-Search-DotNet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) | Esempi che accompagnano gli articoli di procedure nella documentazione, incluso [come usare la libreria client .NET](search-howto-dotnet-sdk.md).|
| [Azure-Samples/Search-DotNet-Getting-Started](https://github.com/Azure-Samples/search-dotnet-getting-started) | Esempi che accompagnano guide introduttive ed esercitazioni nella documentazione.|

> [!Tip]
> Provare il [browser degli esempi](/samples/browse/?languages=csharp&products=azure-cognitive-search) per cercare esempi di codice Microsoft in GitHub, filtrati in base al prodotto, al servizio e alla lingua.

## <a name="net-sdk-samples"></a>Esempi di .NET SDK

Azure SDK per .NET include numerosi esempi e un [file Leggimi di esempio](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/README.md) che ne descrive ciascuno. Questo elenco è riportato di seguito per praticità.

| Esempi | Descrizione |
|---------|-------------|
| ["Hello World", in modo sincrono](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01a_HelloWorld.md) | Viene illustrato come creare un client, autenticare e gestire gli errori usando metodi sincroni.|
| ["Hello World", in modo asincrono](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample01b_HelloWorldAsync.md) | Viene illustrato come creare un client, autenticare e gestire gli errori usando i metodi asincroni.  |
| [Operazioni a livello di servizio](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample02_Service.md) | Viene illustrato come creare indici, indicizzatori, origini dati, skillsets e mappe sinonimo. Questo esempio illustra anche come ottenere le statistiche del servizio e come eseguire una query su un indice.  |
| [Operazioni sugli indici](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample03_Index.md) | Viene illustrato come eseguire un'azione sull'indice esistente, in questo caso ottenendo un conteggio dei documenti archiviati nell'indice.  |
| [FieldBuilderIgnore](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample04_FieldBuilderIgnore.md) | Viene illustrata una tecnica per l'utilizzo di tipi di dati non supportati.  |
| [Indicizzazione di documenti (modello push)](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample05_IndexingDocuments.md) | Indicizzazione del modello "push", in cui si invia un payload JSON a un indice in un servizio.   |
| [Esempio di chiave di crittografia](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/search/Azure.Search.Documents/samples/Sample06_EncryptedIndex.md) | Viene illustrato l'uso di una chiave di crittografia gestita dal cliente per aggiungere un livello aggiuntivo di protezione rispetto al contenuto sensibile.  |

## <a name="documentation-samples"></a>Esempi di documentazione

Gli esempi seguenti includono un articolo associato in [Azure ricerca cognitiva documentazione](https://docs.microsoft.com/azure/search/).

| Esempi | Descrizione |
|---------|-------------|
| [QuickStart](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/quickstart) | Codice sorgente per la [Guida introduttiva: creare un indice di ricerca ](search-get-started-dotnet.md).  |
| [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)  | Codice sorgente per [l'utilizzo della libreria client .NET](search-howto-dotnet-sdk.md) |
| [DotNetHowToSynonyms](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)  | Gli elenchi di sinonimi vengono utilizzati per l'espansione delle query, fornendo termini corrispondenti esterni a un indice. Questo esempio è incluso nell' [esempio: aggiungere sinonimi in C#](search-synonyms-tutorial-sdk.md). |
| [DotNetToIndexers](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) | Codice sorgente dietro i frammenti correlati all'indicizzatore in vari articoli. Questo esempio illustra come configurare un indicizzatore con una pianificazione, i mapping dei campi e i parametri.  |
| [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK)  | Codice sorgente per [la configurazione delle chiavi gestite dal cliente per la crittografia dei dati](search-security-manage-encryption-keys.md) |
| [Crea la tua prima app in C #](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/create-first-app/v11) |  Codice sorgente per [esercitazione: creare la prima app di ricerca](tutorial-csharp-create-first-app.md). Sebbene la maggior parte degli esempi siano applicazioni console, questo esempio MVC usa una pagina Web per individuare l'indice degli hotel di esempio, dimostrando la ricerca di base, l'impaginazione, il completamento automatico e le query suggerite, i facet e i filtri. |
| [più origini dati](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/multiple-data-sources)  | Codice sorgente per [esercitazione: Indice da più origini dati](tutorial-multiple-data-sources.md). |
|  [ottimizzazione-dati-indicizzazione](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing) | Codice sorgente per [esercitazione: ottimizzare l'indicizzazione con l'API push](tutorial-optimize-indexing-push-api.md).  |
| [esercitazione-intelligenza artificiale](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/tutorial-ai-enrichment)  | Codice sorgente per [esercitazione: contenuto ricercabile generato da ai BLOB di Azure con .NET SDK](cognitive-search-tutorial-blob-dotnet.md).  |

## <a name="standalone-samples-and-solutions"></a>Esempi e soluzioni autonome

| Esempi | Descrizione |
|---------|-------------|
| [Azure-Search-Power-Skills](https://github.com/Azure-Samples/azure-search-power-skills)  | Codice sorgente per competenze personalizzate utilizzabili che è possibile incorporare nelle soluzioni vinte.  |
| [Acceleratore di soluzione Knowledge Mining](https://docs.microsoft.com/samples/azure-samples/azure-search-knowledge-mining/azure-search-knowledge-mining/) | Include modelli, file di supporto e report analitici che consentono di eseguire il prototipo di una soluzione di Knowledge mining end-to-end.  |
| [Covid-19 Cerca repository app](https://github.com/liamca/covid19search) | Repository del codice sorgente per l' [app Covid-19 Search](https://covid19search.azurewebsites.net/) basata su ricerca cognitiva |
| [JFK](https://github.com/Microsoft/AzureSearch_JFK_Files) | Scopri di più sulla [soluzione JFK](https://www.microsoft.com/ai/ai-lab-jfk-files). |