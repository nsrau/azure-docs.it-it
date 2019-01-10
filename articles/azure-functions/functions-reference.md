---
title: Linee guida per lo sviluppo di Funzioni di Azure | Microsoft Docs
description: Informazioni sui concetti e sulle tecniche di Funzioni di Azure necessari per sviluppare funzioni in Azure in tutti i linguaggi e i binding di programmazione.
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
keywords: manuale dello sviluppatore, funzioni di azure, funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 10/12/2017
ms.author: glenga
ms.openlocfilehash: 42635852bb5c6e7b388d4dc58b9d5bfaa6212438
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53725854"
---
# <a name="azure-functions-developers-guide"></a>Manuale dello sviluppatore di Funzioni di Azure
In Funzioni di Azure funzioni specifiche condividono alcuni concetti tecnici e componenti di base, indipendentemente dal linguaggio o dall'associazione che vengono usati. Prima di passare all'apprendimento di dettagli specifici per un linguaggio o un'associazione, assicurarsi di leggere questa panoramica generale.

Questo articolo presuppone che sia già stata letta la [Panoramica di Funzioni di Azure](functions-overview.md) e si abbia familiarità con i [concetti di WebJobs SDK, ad esempio trigger, associazione e runtime JobHost](https://github.com/Azure/azure-webjobs-sdk/wiki). Funzioni di Azure si basa su WebJobs SDK. 

## <a name="function-code"></a>Codice di funzione
La *funzione* è il concetto primario in Funzioni di Azure. Il codice per una funzione viene scritto in un linguaggio di propria scelta, quindi il codice e i file di configurazione vengono salvati nella stessa cartella. La configurazione viene denominata `function.json`, che contiene i dati di configurazione JSON. I linguaggi supportati sono numerosi e quindi è possibile scegliere quello con cui si lavora meglio, in base alla propria esperienza. 

Il file function.json definisce le associazioni di funzione e altre impostazioni di configurazione. Il runtime usa questo file per determinare gli eventi da monitorare e come passare i dati e restituirli dall'esecuzione di funzioni. Di seguito è riportato un esempio di file function.json.

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

Impostare la proprietà `disabled` su `true` per impedire l'esecuzione della funzione.

La proprietà `bindings` è quella che consente di configurare trigger e associazioni. Ogni associazione condivide alcune impostazioni comuni e altre specifiche per tipo. Ogni associazione richiede le impostazioni seguenti:

| Proprietà | Valori/tipi | Commenti |
| --- | --- | --- |
| `type` |stringa |Tipo di associazione. Ad esempio, `queueTrigger`. |
| `direction` |'in', 'out' |Indica se l'associazione consente la ricezione di dati nella funzione o l'invio di dati dalla funzione. |
| `name` |stringa |Il nome che viene usato per i dati associati nella funzione. Per C#, si tratta di un nome di argomento, per JavaScript è la chiave in un elenco di chiavi/valori. |

## <a name="function-app"></a>App per le funzioni
L'app per le funzioni offre un contesto di esecuzione per le funzioni. Un'app per le funzioni è costituita da una o più singole funzioni che vengono gestite insieme dal servizio app di Azure. Tutte le funzioni in un'app per le funzioni condividono lo stesso piano tariffario, la stessa distribuzione continua e la stessa versione runtime. Un'app per le funzioni può essere considerata un modo per organizzare e gestire collettivamente le funzioni. 

> [!NOTE]
> A partire dalla [versione 2.x](functions-versions.md) del runtime di Funzioni di Azure, tutte le funzioni in un'app per le funzioni devono essere create nella stessa lingua.

## <a name="runtime"></a>Runtime
Il runtime, o host di script, di Funzioni di Azure è l'host sottostante che rimane in ascolto degli eventi, raccoglie e invia dati e infine esegue il codice. Questo stesso host viene usato da WebJobs SDK.

È anche disponibile un host Web che gestisce le richieste del trigger HTTP per il runtime. Avere due host aiuta a isolare il runtime dal traffico front-end gestito dall'host Web.

## <a name="folder-structure"></a>Struttura di cartelle
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Quando si configura un progetto per la distribuzione di funzioni in un'app per le funzioni in Azure, è possibile considerare questa struttura di cartelle come codice del sito. Per distribuire il progetto nell'app per le funzioni in Azure, è consigliabile usare la [distribuzione del pacchetto](deployment-zip-push.md). È anche possibile usare gli strumenti esistenti, ad esempio [integrazione e distribuzione continue](functions-continuous-deployment.md) e Azure DevOps.

> [!NOTE]
> Verificare di distribuire il file `host.json` e le cartelle di funzione direttamente nella cartella `wwwroot`. Non includere la cartella `wwwroot` nelle distribuzioni. In caso contrario, verranno create le cartelle `wwwroot\wwwroot`.

## <a id="fileupdate"></a> Come aggiornare i file delle app per le funzioni
L'editor funzioni incorporato nel portale di Azure consente di aggiornare il file *function.json* e il file di codice di una funzione. Per caricare o aggiornare altri file, ad esempio *package.json* o *project.json* o le relative dipendenze, è necessario usare altri metodi di distribuzione.

Le app per le funzioni sono basate sul servizio app, quindi tutte le [opzioni di distribuzione disponibili per le app Web standard](../app-service/deploy-local-git.md) sono disponibili anche per le app per le funzioni. Ecco alcuni metodi per caricare o aggiornare file delle app per le funzioni. 

#### <a name="use-local-tools-and-publishing"></a>Usare strumenti e pubblicazione locali
È possibile creare e pubblicare app per le funzioni tramite vari strumenti, tra cui [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](functions-create-first-function-vs-code.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md) e [Strumenti di base di Funzioni di Azure](./functions-develop-local.md). Per altre informazioni, vedere [Scrivere codice per Funzioni di Azure e testarle in locale](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

#### <a name="continuous-deployment"></a>Distribuzione continua
Seguire le istruzioni illustrate nell'argomento [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Esecuzione parallela
Quando si verificano rapidamente più eventi di trigger di quanti il runtime della funzione a thread singolo riesca a elaborare, il runtime chiama la funzione più volte in parallelo.  Se un'app per le funzioni usa il [piano di hosting a consumo](functions-scale.md#how-the-consumption-plan-works), il numero di istanze dell'app può aumentare automaticamente.  Ogni istanza dell'app per le funzioni, indipendentemente dal fatto che venga eseguita in un piano di hosting a consumo o in un normale [piano di hosting del servizio app](../app-service/overview-hosting-plans.md), può elaborare chiamate di funzione simultanee in parallelo usando più thread.  Il numero massimo di chiamate di funzione simultanee in ogni istanza dell'app per le funzioni dipende dal tipo di trigger usato e dalle risorse usate dalle altre funzioni nell'app per le funzioni.

## <a name="functions-runtime-versioning"></a>Controllo delle versioni del runtime di Funzioni

È possibile configurare la versione del runtime di Funzioni usando le impostazioni dell'app `FUNCTIONS_EXTENSION_VERSION`. Ad esempio, il valore "~ 2" indica che l'app per le funzioni userà 2.x come numero di versione principale. Le app per le funzioni vengono aggiornate a ogni nuova versione secondaria appena rilasciata. Per altre informazioni, incluso come visualizzare la versione esatta dell'app per le funzioni, vedere [Come specificare le versioni del runtime per Funzioni di Azure](set-runtime-version.md).

## <a name="repositories"></a>Repository
Il codice di Funzioni di Azure è open source e archiviato in repository GitHub:

* [Runtime di Funzioni di Azure](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Portale Funzioni di Azure](https://github.com/projectkudu/AzureFunctionsPortal)
* [Modelli di Funzioni di Azure](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Estensioni Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Associazioni
La tabella riportata di seguito elenca tutte le associazioni supportate.

[!INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

In caso di problemi con errori provenienti dalle associazioni, rivedere la documentazione [Azure Functions Binding Error Codes](functions-bindings-error-pages.md) (Codici degli errori di associazione di Funzioni di Azure).

## <a name="reporting-issues"></a>Segnalazione di problemi
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le seguenti risorse:

* [Best Practices for Azure Functions](functions-best-practices.md) (Procedure consigliate per Funzioni di Azure)
* [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-reference-csharp.md)
* [Guida di riferimento per gli sviluppatori di Funzioni di Azure in F#](functions-reference-fsharp.md)
* [Guida di riferimento per gli sviluppatori NodeJS di Funzioni di Azure](functions-reference-node.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
* [Azure Functions: The Journey](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) (Evoluzione di Funzioni di Azure) nel blog del team del Servizio app di Azure. Storia dello sviluppo di Funzioni di Azure.

