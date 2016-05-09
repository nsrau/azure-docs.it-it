<properties
	pageTitle="Guida di riferimento per gli sviluppatori di Funzioni di Azure | Microsoft Azure"
	description="Comprendere i concetti e i componenti comuni a tutti linguaggi e le associazioni di Funzioni di Azure."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="Funzioni di Azure, Funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="04/07/2016"
	ms.author="chrande"/>

# Guida di riferimento per gli sviluppatori di Funzioni di Azure

Funzioni di Azure condivide alcuni concetti tecnici e componenti di base, indipendentemente dal linguaggio o dall'associazione che vengono usati. Prima di passare all'apprendimento di dettagli specifici per un linguaggio o un'associazione, assicurarsi di leggere questa panoramica generale.

Questo articolo presuppone che sia già stata letta la [Panoramica di Funzioni di Azure](functions-overview.md) e si abbia familiarità con [concetti di WebJobs SDK, ad esempio trigger, associazione e runtime JobHost](../app-service-web/websites-dotnet-webjobs-sdk.md). Funzioni di Azure si basa su WebJobs SDK.

## funzione

La *funzione* è il concetto primario in Funzioni di Azure. Il codice per una funzione viene scritto in un linguaggio di propria scelta, quindi i file di codice e il file di configurazione vengono salvati nella stessa cartella. Il file di configurazione è un file JSON e viene denominato `function.json`. I linguaggi supportati sono numerosi, è quindi possibile scegliere quello con cui si lavora meglio, in base alla propria esperienza. Struttura di cartelle di esempio:

```
mynodefunction
| - function.json
| - index.js
| - node_modules
| | - ... packages ...
| - package.json
mycsharpfunction
| - function.json
| - run.csx
```

## function.json e associazioni

Il file `function.json` contiene la configurazione specifica di una funzione, comprese le associazioni relative. Il runtime legge il file per determinare quali eventi attivare, quali dati includere quando la funzione viene chiamata e dove inviare i dati specificati dalla funzione stessa.

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

È possibile impedire che il runtime esegua la funzione impostando la proprietà `disabled` su `true`.

La proprietà `bindings` è quella che consente di configurare trigger e associazioni. Ogni associazione condivide alcune impostazioni comuni e altre specifiche per tipo. Ogni associazione richiede le seguenti impostazioni:

|Proprietà|Valori/tipi|Commenti|
|---|-----|------|
|`type`|string|Tipo di associazione. ad esempio `queueTrigger`.
|`direction`|'in', 'out'| Indica se l'associazione consente la ricezione di dati nella funzione o l'invio di dati dalla funzione.
| `name` | string | Il nome che verrà usato per i dati associati nella funzione. Per C# si tratterà di un nome di argomento, per JavaScript sarà la chiave in un elenco di chiavi/valori.

## Runtime (host di script e host Web)

Il runtime, noto anche come host di script, è l'host di WebJobs SDK sottostante che sta in ascolto attendendo eventi, raccoglie e invia dati e infine esegue il codice.

Per facilitare i trigger HTTP, è disponibile anche un host Web che è progettato per precedere l'host di script negli scenari di produzione. Questo aiuta a isolare l'host di script dal traffico front-end gestito dall'host Web.

## Struttura di cartelle

L'host di script punta a una cartella che contiene un file di configurazione e una o più funzioni.

```
parentFolder (for example, wwwroot)
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

Il file *host.json* contiene alcune configurazioni specifiche dell'host di script e si trova nella cartella padre.

Ogni funzione ha una cartella che contiene i file di codice, il file *function.json*, e altre dipendenze.

Quando si configura un progetto per la distribuzione di funzioni in un'app di funzione in un servizio App di Azure, è possibile considerare questa struttura di cartelle il codice del sito. È possibile usare gli strumenti esistenti come l' integrazione e la distribuzione continue o gli script di distribuzione personalizzata per l'installazione del pacchetto di distribuzione o la transpilazione del codice.

## Esecuzione parallela

Quando si verificano rapidamente più eventi di trigger di quanti il runtime della funzione a thread singolo sia in grado di elaborare, il runtime chiama la funzione più volte in parallelo. Se un'app di funzione sta usando il [piano di servizio Dynamic](functions-scale.md#dynamic-service-plan), può aumentare automaticamente fino a 10 istanze simultanee. Ogni istanza dell'app di funzione, indipendentemente dal fatto che venga eseguita in un piano di servizio Dynamic o in un normale [piano di servizio App](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), può elaborare chiamate di funzione simultanee in parallelo usando più thread. Il numero massimo di chiamate di funzione simultanee in ogni istanza dell'app di funzione varia in base alla dimensione della memoria dell'app di funzione.

## Funzioni di Azure - Pulse  

Pulse è un flusso di eventi attivi che visualizza la frequenza con cui viene eseguita la funzione, nonché le operazioni riuscite e gli errori. È anche possibile monitorare il tempo medio di esecuzione. Questa funzionalità verrà dotata di altre funzioni e possibilità di personalizzazione più avanti. È possibile accedere alla pagina **Pulse** dalla scheda relativa al **monitoraggio**.

## Associazioni

La tabella riportata di seguito elenca tutte le associazioni supportate.

[AZURE.INCLUDE [Calcolo dinamico](../../includes/functions-bindings.md)]

## Segnalazione di problemi

[AZURE.INCLUDE [Segnalazione di problemi](../../includes/functions-reporting-issues.md)]

## Passaggi successivi

Per altre informazioni, vedere le seguenti risorse:

* [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-reference-csharp.md)
* [Guida di riferimento per gli sviluppatori NodeJS di Funzioni di Azure](functions-reference-node.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)

<!---HONumber=AcomDC_0427_2016-->