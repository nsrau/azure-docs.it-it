---
title: Linee guida per lo sviluppo di Funzioni di Azure | Documentazione Microsoft
description: Informazioni sui concetti e sulle tecniche di Funzioni di Azure necessari per sviluppare funzioni in Azure in tutti i linguaggi e i binding di programmazione.
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: 
tags: 
keywords: manuale dello sviluppatore, funzioni di azure, funzioni, elaborazione eventi, webhook, calcolo dinamico, architettura senza server
ms.assetid: d8efe41a-bef8-4167-ba97-f3e016fcd39e
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/30/2017
ms.author: chrande
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: 879be48150cfe13e31064475aa637f13f5f5f9d5
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---
# <a name="azure-functions-developers-guide"></a>Manuale dello sviluppatore di Funzioni di Azure
In Funzioni di Azure funzioni specifiche condividono alcuni concetti tecnici e componenti di base, indipendentemente dal linguaggio o dall'associazione che vengono usati. Prima di passare all'apprendimento di dettagli specifici per un linguaggio o un'associazione, assicurarsi di leggere questa panoramica generale.

Questo articolo presuppone che sia già stata letta la [Panoramica di Funzioni di Azure](functions-overview.md) e si abbia familiarità con i [concetti di WebJobs SDK, ad esempio trigger, associazione e runtime JobHost](../app-service-web/websites-dotnet-webjobs-sdk.md). Funzioni di Azure si basa su WebJobs SDK. 

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
| `name` |string |Il nome che viene usato per i dati associati nella funzione. Per C#, si tratta di un nome di argomento, per JavaScript è la chiave in un elenco di chiavi/valori. |

## <a name="function-app"></a>App per le funzioni
Un'app per le funzioni è costituita da una o più singole funzioni che vengono gestite insieme dal servizio app di Azure. Tutte le funzioni in un'app per le funzioni condividono lo stesso piano tariffario, la stessa distribuzione continua e la stessa versione runtime. Funzioni scritte in più linguaggi possono condividere la stessa app per le funzioni. Un'app per le funzioni può essere considerata un modo per organizzare e gestire collettivamente le funzioni. 

## <a name="runtime-script-host-and-web-host"></a>Runtime (host di script e host Web)
Il runtime, o host di script, è l'host di WebJobs SDK sottostante che rimane in ascolto degli eventi, raccoglie e invia dati e infine esegue il codice. 

Per facilitare i trigger HTTP, è disponibile anche un host Web che è progettato per precedere l'host di script negli scenari di produzione. Avere due host aiuta a isolare l'host di script dal traffico front-end gestito dall'host Web.

## <a name="folder-structure"></a>Struttura di cartelle
[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Quando si configura un progetto per la distribuzione di funzioni in un'app per le funzioni di un servizio app di Azure, questa struttura di cartelle può essere considerata il codice del sito. È possibile usare gli strumenti esistenti come l' integrazione e la distribuzione continue o gli script di distribuzione personalizzata per l'installazione del pacchetto di distribuzione o la transpilazione del codice.

> [!NOTE]
> Verificare di distribuire il file `host.json` e le cartelle di funzione direttamente nella cartella `wwwroot`. Non includere la cartella `wwwroot` nelle distribuzioni. In caso contrario, verranno create le cartelle `wwwroot\wwwroot`. 
> 
> 

## <a id="fileupdate"></a> Come aggiornare i file delle app per le funzioni
L'editor funzioni incorporato nel portale di Azure consente di aggiornare il file *function.json* e il file di codice di una funzione. Per caricare o aggiornare altri file, ad esempio *package.json* o *project.json* o le relative dipendenze, è necessario usare altri metodi di distribuzione.

Le app per le funzioni sono basate sul servizio app, quindi tutte le [opzioni di distribuzione disponibili per le app Web standard](../app-service-web/web-sites-deploy.md) sono disponibili anche per le app per le funzioni. Ecco alcuni metodi per caricare o aggiornare file delle app per le funzioni. 

#### <a name="to-use-app-service-editor"></a>Per usare l'editor del servizio app
1. Nel portale Funzioni di Azure fare clic su **Impostazioni dell'app per le funzioni**.
2. Nella sezione **Impostazioni avanzate** fare clic su **Passa a Impostazioni del servizio app**.
3. Fare clic su **Editor del servizio app** nel menu di navigazione dell'applicazione in **DEVELOPMENT TOOLS** (STRUMENTI DI SVILUPPO).
4. Fare clic su **Vai**.
   
   Dopo il caricamento dell'editor del servizio app, il file *host.json* e le cartelle delle funzioni vengono visualizzati in *wwwroot*. 
5. Aprire i file per modificarli oppure selezionare e trascinare i file dal computer di sviluppo per caricarli.

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>Per usare l'endpoint SCM (Kudu) dell'app per le funzioni
1. Accedere a `https://<function_app_name>.scm.azurewebsites.net`.
2. Fare clic su **Debug Console (Console di debug) > CMD**.
3. Passare a `D:\home\site\wwwroot\` per aggiornare *host.json* o a `D:\home\site\wwwroot\<function_name>` per aggiornare i file di una funzione.
4. Selezionare e trascinare un file da caricare nella cartella appropriata della griglia di file. Nella griglia di file è possibile rilasciare un file in due aree. Per i file *ZIP* viene visualizzata una casella con l'etichetta "Drag here to upload and unzip" (Trascinare qui per caricare e decomprimere). Gli altri tipi di file devono essere rilasciati nella griglia di file ma all'esterno della casella per la decompressione.

<!--NOTE: I've removed documentation on FTP, because it does not sync triggers on the consumption plan --DonnaM -->

#### <a name="to-use-continuous-deployment"></a>Per usare la distribuzione continua
Seguire le istruzioni illustrate nell'argomento [Distribuzione continua per Funzioni di Azure](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Esecuzione parallela
Quando si verificano rapidamente più eventi di trigger di quanti il runtime della funzione a thread singolo riesca a elaborare, il runtime chiama la funzione più volte in parallelo.  Se un'app per le funzioni usa il [piano di hosting a consumo](functions-scale.md#how-the-consumption-plan-works), il numero di istanze dell'app può aumentare automaticamente.  Ogni istanza dell'app per le funzioni, indipendentemente dal fatto che venga eseguita in un piano di hosting a consumo o in un normale [piano di hosting del servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), può elaborare chiamate di funzione simultanee in parallelo usando più thread.  Il numero massimo di chiamate di funzione simultanee in ogni istanza dell'app per le funzioni dipende dal tipo di trigger usato e dalle risorse usate dalle altre funzioni nell'app per le funzioni.

## <a name="functions-runtime-versioning"></a>Controllo delle versioni del runtime di Funzioni

È possibile configurare la versione del runtime di Funzioni usando le impostazioni dell'app `FUNCTIONS_EXTENSION_VERSION`. Ad esempio, il valore "~ 1" indica che l'app per le funzioni userà 1 come numero di versione principale. Le app per le funzioni vengono aggiornate a ogni nuova versione secondaria appena rilasciata. È possibile visualizzare la versione esatta dell'app per le funzioni nella scheda **Impostazioni** nel portale di Azure.

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

## <a name="reporting-issues"></a>Segnalazione di problemi
[!INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)]

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni, vedere le seguenti risorse:

* [Best Practices for Azure Functions](functions-best-practices.md) (Procedure consigliate per Funzioni di Azure)
* [Guida di riferimento per gli sviluppatori C# di Funzioni di Azure](functions-reference-csharp.md)
* [Guida di riferimento per gli sviluppatori di Funzioni di Azure in F#](functions-reference-fsharp.md)
* [Guida di riferimento per gli sviluppatori NodeJS di Funzioni di Azure](functions-reference-node.md)
* [Trigger e associazioni di Funzioni di Azure](functions-triggers-bindings.md)
* [Post sull'evoluzione di Funzioni di Azure](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) nel blog del team del Servizio app di Azure. Storia dello sviluppo di Funzioni di Azure.


