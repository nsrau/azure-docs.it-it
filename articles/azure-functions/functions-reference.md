---
title: Linee guida per lo sviluppo di Funzioni di Azure | Documentazione Microsoft
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
ms.openlocfilehash: 5b2b7f3cd6bfa219b794edc63d6bf8b2784b713c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62120739"
---
# <a name="azure-functions-developers-guide"></a>Manuale dello sviluppatore di Funzioni di Azure
In Funzioni di Azure funzioni specifiche condividono alcuni concetti tecnici e componenti di base, indipendentemente dal linguaggio o dall'associazione che vengono usati. Prima di passare all'apprendimento di dettagli specifici per un linguaggio o un'associazione, assicurarsi di leggere questa panoramica generale.

Questo articolo presuppone che sia già stato letto [Panoramica di Funzioni di Azure](functions-overview.md).

## <a name="function-code"></a>Codice di funzione
La *funzione* è il concetto primario in Funzioni di Azure. Una funzione contiene due elementi importanti: il codice, che può essere scritto in svariati linguaggi, e un file config, il file function.json. Per i linguaggi compilati, questo file config viene generato automaticamente dalle annotazioni nel codice. Per i linguaggi di scripting, è necessario fornire personalmente il file config.

Il file function.json definisce il trigger, le associazioni e altre impostazioni di configurazione della funzione. Ogni funzione ha un solo trigger. Il runtime usa questo file config per determinare gli eventi da monitorare e come passare i dati e restituirli da un'esecuzione della funzione. Di seguito è riportato un esempio di file function.json.

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

La proprietà `bindings` è quella che consente di configurare trigger e associazioni. Ogni associazione condivide alcune impostazioni comuni e altre specifiche per tipo. Ogni associazione richiede le impostazioni seguenti:

| Proprietà | Valori/tipi | Commenti |
| --- | --- | --- |
| `type` |string |Tipo di associazione. Ad esempio, `queueTrigger`. |
| `direction` |'in', 'out' |Indica se l'associazione consente la ricezione di dati nella funzione o l'invio di dati dalla funzione. |
| `name` |string |Il nome che viene usato per i dati associati nella funzione. Per C#, si tratta di un nome di argomento, per JavaScript è la chiave in un elenco di chiavi/valori. |

## <a name="function-app"></a>App per le funzioni
L'app per le funzioni offre un contesto di esecuzione per le funzioni. Un'app per le funzioni è costituita da una o più singole funzioni che vengono gestite, distribuite e ridimensionate insieme. Tutte le funzioni in un'app per le funzioni condividono lo stesso piano tariffario, la stessa distribuzione continua e la stessa versione runtime. Un'app per le funzioni può essere considerata un modo per organizzare e gestire collettivamente le funzioni. 

> [!NOTE]
> Tutte le funzioni in un'app per le funzioni devono essere create nello stesso linguaggio. Nelle [versioni precedenti](functions-versions.md) del runtime di Funzioni di Azure questo non è un requisito.

## <a name="folder-structure"></a>Struttura di cartelle
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

La struttura di cartelle mostrata sopra è quella predefinita (e consigliata) per un'app per le funzioni. Se si vuole modificare il percorso del file del codice di una funzione, modificare la sezione `scriptFile` del file _function.json_. È anche consigliabile usare la [distribuzione di pacchetti](deployment-zip-push.md) per distribuire il progetto nell'app per le funzioni in Azure. È anche possibile usare gli strumenti esistenti, ad esempio [integrazione e distribuzione continue](functions-continuous-deployment.md) e Azure DevOps.

> [!NOTE]
> Se si distribuisce un pacchetto manualmente, assicurarsi di distribuire il file _host.json_ e le cartelle della funzione direttamente nella cartella `wwwroot`. Non includere la cartella `wwwroot` nelle distribuzioni. In caso contrario, verranno create le cartelle `wwwroot\wwwroot`.

#### <a name="use-local-tools-and-publishing"></a>Usare strumenti e pubblicazione locali
È possibile creare e pubblicare app per le funzioni tramite vari strumenti, tra cui [Visual Studio](./functions-develop-vs.md), [Visual Studio Code](functions-create-first-function-vs-code.md), [IntelliJ](./functions-create-maven-intellij.md), [Eclipse](./functions-create-maven-eclipse.md) e [Azure Functions Core Tools](./functions-develop-local.md). Per altre informazioni, vedere [Scrivere codice per Funzioni di Azure e testarle in locale](./functions-develop-local.md).

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --glenga -->

## <a id="fileupdate"></a> Come modificare le funzioni nel portale di Azure
L'editor delle funzioni integrato nel portale di Azure permette di aggiornare il codice e il file *function.json* direttamente inline. Si tratta di un approccio consigliato solo per piccole modifiche o modelli di verifica, mentre la procedura consigliata consiste nell'usare uno strumento di sviluppo locale come Visual Studio Code.

## <a name="parallel-execution"></a>Esecuzione parallela
Quando si verificano rapidamente più eventi di trigger di quanti il runtime della funzione a thread singolo riesca a elaborare, il runtime chiama la funzione più volte in parallelo.  Se un'app per le funzioni usa il [piano di hosting a consumo](functions-scale.md#how-the-consumption-and-premium-plans-work), il numero di istanze dell'app può aumentare automaticamente.  Ogni istanza dell'app per le funzioni, indipendentemente dal fatto che venga eseguita in un piano di hosting a consumo o in un normale [piano di hosting del servizio app](../app-service/overview-hosting-plans.md), può elaborare chiamate di funzione simultanee in parallelo usando più thread.  Il numero massimo di chiamate di funzione simultanee in ogni istanza dell'app per le funzioni dipende dal tipo di trigger usato e dalle risorse usate dalle altre funzioni nell'app per le funzioni.

## <a name="functions-runtime-versioning"></a>Controllo delle versioni del runtime di Funzioni

È possibile configurare la versione del runtime di Funzioni usando le impostazioni dell'app `FUNCTIONS_EXTENSION_VERSION`. Ad esempio, il valore "~ 2" indica che l'app per le funzioni userà 2.x come numero di versione principale. Le app per le funzioni vengono aggiornate a ogni nuova versione secondaria appena rilasciata. Per altre informazioni, incluso come visualizzare la versione esatta dell'app per le funzioni, vedere [Come specificare le versioni del runtime per Funzioni di Azure](set-runtime-version.md).

## <a name="repositories"></a>Repository
Il codice di Funzioni di Azure è open source e archiviato in repository GitHub:

* [Funzioni di Azure](https://github.com/Azure/Azure-Functions)
* [Host di Funzioni di Azure](https://github.com/Azure/azure-functions-host/)
* [Portale Funzioni di Azure](https://github.com/azure/azure-functions-ux)
* [Modelli di Funzioni di Azure](https://github.com/azure/azure-functions-templates)
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

* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
* [Scrivere codici per Funzioni di Azure e testarle in locale](./functions-develop-local.md)
* [Best Practices for Azure Functions](functions-best-practices.md) (Procedure consigliate per Funzioni di Azure)
* [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-reference-csharp.md)
* [Guida di riferimento per gli sviluppatori NodeJS di Funzioni di Azure](functions-reference-node.md)
