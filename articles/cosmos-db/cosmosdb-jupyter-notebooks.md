---
title: Introduzione al supporto di notebook di Jupyter predefiniti in Azure Cosmos DB (anteprima)
description: Informazioni su come usare il supporto di notebook di Jupyter predefiniti in Azure Cosmos DB per eseguire query in modo interattivo.
ms.service: cosmos-db
ms.topic: overview
ms.date: 05/19/2020
author: deborahc
ms.author: dech
ms.openlocfilehash: 0299ab1e76e742adb8d2653b8b5d6923aff2f11f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "88588156"
---
# <a name="built-in-jupyter-notebooks-support-in-azure-cosmos-db-preview"></a>Supporto di notebook di Jupyter predefiniti in Azure Cosmos DB (anteprima)

Jupyter Notebook è un'applicazione Web open source che consente di creare e condividere documenti contenenti codice in tempo reale, equazioni, visualizzazioni e testo narrativo. 

I notebook di Jupyter predefiniti in Azure Cosmos DB sono integrati direttamente nel portale di Azure e negli account di Azure Cosmos DB, per cui sono pratici e facili da usare. Sviluppatori, data scientist, tecnici e analisti possono usare la familiare esperienza dei notebook di Jupyter per eseguire attività di esplorazione, pulizia e trasformazione dei dati, simulazioni numeriche, modellazione statistica, visualizzazione dei dati e Machine Learning.

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/cosmos-notebooks-overview.png" alt-text="Visualizzazioni dei notebook di Jupyter in Azure Cosmos DB":::

Azure Cosmos DB supporta notebook C# e Python per tutte le API, tra cui core (SQL), Cassandra, Gremlin, Table e API per MongoDB. All'interno del notebook è possibile sfruttare i comandi e le funzionalità predefiniti che semplificano la creazione delle risorse di Azure Cosmos DB, il caricamento dei dati, l'esecuzione di query e la visualizzazione dei dati in Azure Cosmos DB. 

:::image type="content" source="./media/cosmosdb-jupyter-notebooks/jupyter-notebooks-portal.png" alt-text="Visualizzazioni dei notebook di Jupyter in Azure Cosmos DB":::

## <a name="benefits-of-jupyter-notebooks"></a>Vantaggi dei notebook di Jupyter

I notebook di Jupyter sono stati sviluppati in origine per le applicazioni di data science scritte in Python e R. Possono tuttavia essere usati in vari modi per diversi tipi di progetto, tra cui:

**Visualizzazione dei dati:** i notebook di Jupyter consentono di visualizzare i dati sotto forma di notebook condiviso che esegue il rendering grafico di un set di dati. È possibile creare visualizzazioni, apportare modifiche interattive al codice condiviso e al set di dati e condividere i risultati.

**Condivisione del codice:** servizi come GitHub offrono la possibilità di condividere codice, ma sono prevalentemente non interattivi. Con un notebook di Jupyter, è possibile visualizzare codice, eseguirlo e mostrare i risultati direttamente nel portale di Azure.

**Interazioni in tempo reale con il codice:** il codice di un notebook di Jupyter è dinamico ed è possibile modificarlo ed eseguire gli aggiornamenti in modo incrementale in tempo reale. È possibile anche incorporare controlli utente, ad esempio dispositivi di scorrimento o campi di input del testo, usati come origini di input per codice, demo o modelli di verifica.

**Documentazione di esempi di codice e risultati dell'esplorazione dei dati:** se si dispone di un frammento di codice e si intende illustrare il funzionamento riga per riga, è possibile incorporarlo in un notebook di Jupyter. È possibile interattività insieme alla documentazione contemporaneamente.

**Comandi predefiniti per Azure Cosmos DB:** i comandi magic incorporati di Azure Cosmos DB facilitano l'interazione con l'account. È possibile usare comandi come %%upload e %%sql per caricare i dati in un contenitore ed eseguirvi query tramite la [sintassi API SQL](sql-query-getting-started.md). Non è necessario scrivere codice personalizzato aggiuntivo.

**Ambiente completo:** i notebook di Jupyter combinano codice, testo formattato, immagini, video, animazioni, equazioni matematiche, tracciati, mappe, figure interattive, widget e interfacce utente grafiche in un singolo documento.

## <a name="components-of-a-jupyter-notebook"></a>Componenti di un notebook di Jupyter

I notebook di Jupyter possono includere diversi tipi di componenti, ognuno organizzato in celle o blocchi discreti:

**Testo e HTML:** è possibile inserire in qualsiasi punto del documento testo normale o annotato nella sintassi markdown per generare HTML. È anche possibile includere stili CSS in linea o aggiungerli a un modello usato per generare il notebook.

**Codice e output:** i notebook di Jupyter supportano codice Python e C#. I risultati del codice eseguito vengono visualizzati immediatamente dopo i blocchi di codice e questi ultimi possono essere eseguiti più volte in qualsiasi ordine.

**Visualizzazioni:** è possibile generare grafici dal codice usando moduli come Matplotlib, Plotly, Bokeh e altri. In modo simile all'output, queste visualizzazioni compaiono in linea accanto al testo che le ha generate. In modo simile all'output, queste visualizzazioni compaiono in linea accanto al testo che le ha generate.

**Contenuti multimediali:** poiché i notebook di Jupyter sono basati sulla tecnologia Web, possono visualizzare tutti i tipi di contenuti multimediali supportati da una pagina Web. È possibile includerli in un notebook come elementi HTML oppure generarli a livello di codice tramite il modulo `IPython.display`.

**Dati:** in un notebook di Jupyter è possibile importare i dati dai contenitori di Azure Cosmos oppure i risultati delle query a livello di codice. Usare i comandi magic predefiniti per caricare o eseguire query sui dati in Azure Cosmos DB. 

## <a name="next-steps"></a>Passaggi successivi

Per iniziare a usare i notebook di Jupyter predefiniti in Azure Cosmos DB, vedere gli articoli seguenti:

* [Abilitare i notebook in un account Azure Cosmos](enable-notebooks.md)
* [Usare funzionalità e comandi del notebook Python](use-python-notebook-features-and-commands.md)
* [Usare funzionalità e comandi dei notebook C#](use-csharp-notebook-features-and-commands.md)
