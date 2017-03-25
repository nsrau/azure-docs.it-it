---
title: Sviluppare le Funzioni di Azure con Servizi multimediali
description: In questo argomento viene illustrato come avviare lo sviluppo di Funzioni di Azure con Servizi multimediali tramite il Portale di Azure.
services: media-services
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 51bdcb01-1846-4e1f-bd90-70020ab471b0
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/13/2017
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b077504e7a289fd4976aa22bab0ad5784ffb491b
ms.lasthandoff: 03/15/2017


---
#<a name="develop-azure-functions-with-media-services"></a>Sviluppare le Funzioni di Azure con Servizi multimediali
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

In questo argomento viene illustrato come avviare lo sviluppo di Funzioni di Azure con Servizi multimediali tramite il Portale di Azure. 

È inoltre possibile distribuire Funzioni di Azure dei Servizi multimediali esistenti da [qui](https://github.com/Azure-Samples/media-services-dotnet-functions-integration) premendo il pulsante **Distribuisci in Azure**. Questo repository contiene esempi di Funzioni di Azure che usano Servizi multimediali di Azure per visualizzare i flussi di lavoro correlati all'inserimento di contenuto direttamente dall'archiviazione BLOB, alla codifica e alla scrittura del contenuto nell'archiviazione BLOB. Include inoltre esempi su come monitorare le notifiche dei processi tramite i webhook e le code di Azure.

È possibile sviluppare le funzioni in base agli esempi di [questo](https://github.com/Azure-Samples/media-services-dotnet-Functions-integration) repository. In questo argomento viene illustrato come iniziare a creare le Funzioni di Azure che usano i Servizi multimediali. 

## <a name="prerequisites"></a>Prerequisiti

Per poter creare la prima funzione, è necessario avere un account Azure attivo. Se non si possiede già un account Azure, [sono disponibili account gratuiti](https://azure.microsoft.com/free/).

Se si intende creare le Funzioni di Azure per eseguire azioni sull'account dei Servizi multimediali di Azure o ascoltare gli eventi inviati dai Servizi multimediali, è necessario creare un account AMS, come descritto [qui](media-services-portal-create-account.md).

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

Creare un'app per le funzioni come descritto [qui](../azure-functions/functions-create-first-azure-function-azure-portal.md#create-a-function-app).

## <a name="create-a-function"></a>Creare una funzione

In seguito alla distribuzione dell'app per le funzioni, questa verrà visualizzata tra le Funzioni di Azure dei **Servizi app**. 

1. Selezionare l'app per le funzioni e fare clic su **Nuova funzione**.
3. Scegliere il linguaggio **C#** e lo scenario **Webhook + API**.
3. Selezionare **GenericWebHook-CSharp** (verrà eseguito ogni volta che riceve una richiesta webhook) o **HttpTrigger-CSharp** (verrà eseguito ogni volta che si riceve una richiesta HTTP) e assegnare il nome della funzione.
4. Fare clic su **Crea**. 

## <a name="get-function-url"></a>Ottenere l'URL della funzione

Per attivare l'esecuzione delle funzioni da uno strumento di test HTTP o da un'altra finestra del browser, sarà necessario il valore dell'URL della funzione. 

![Impostazioni](./media/media-services-azure-functions/media-services-azure-functions002.png)

## <a name="files"></a>File

La funzione di Azure è associata al file del codice e ad altri file descritti in questa sezione. Per impostazione predefinita, una funzione è associata ai file **function.json** e **run.csx**. Sarà necessario aggiungere un file **project.json**. La parte successiva di questa sezione illustra le definizioni per questi file.

![input](./media/media-services-azure-functions/media-services-azure-functions003.png)

### <a name="functionjson"></a>function.json

Il file function.json definisce le associazioni di funzione e altre impostazioni di configurazione. Il runtime usa questo file per determinare gli eventi da monitorare e come passare i dati e restituirli dall'esecuzione di funzioni. 

Di seguito è riportato un esempio di file **function.json**.

    {
      "bindings": [
        {
          "type": "httpTrigger",
          "name": "req",
          "direction": "in",
          "methods": [
        "post",
        "get",
        "put",
        "update",
        "patch"
          ]
        },
        {
          "type": "http",
          "name": "res",
          "direction": "out"
        }
      ]
    }
    
### <a name="projectjson"></a>project.json

Il file project.json contiene dipendenze. Di seguito è riportato un esempio del file **function.json** che include le librerie AMS.

    {
      "frameworks": {
        "net46":{
          "dependencies": {
        "windowsazure.mediaservices": "3.8.0.5",
        "windowsazure.mediaservices.extensions": "3.8.0.3"
          }
        }
       }
    }
    
### <a name="runcsx"></a>run.csx

Questo è il codice C# per la funzione. Per vedere un esempio di una funzione webhook, vedere [questo](media-services-dotnet-check-job-progress-with-webhooks.md) argomento. 

Al termine dell'operazione di definizione della funzione fare clic su **Esegui**.
    
    ///////////////////////////////////////////////////
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.WindowsAzure.MediaServices.Client;
    using System.Collections.Generic;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using System.IO;
    using System.Globalization;
    using Newtonsoft.Json;
    using Microsoft.Azure;
    using System.Net;
    using System.Security.Cryptography;
    

    static string _mediaServicesAccountName = Environment.GetEnvironmentVariable("AMSAccount");
    static string _mediaServicesAccountKey = Environment.GetEnvironmentVariable("AMSKey");
    
    static CloudMediaContext _context = null;
    
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
    {
        log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
    
        Task<byte[]> taskForRequestBody = req.Content.ReadAsByteArrayAsync();
        byte[] requestBody = await taskForRequestBody;
    
        string jsonContent = await req.Content.ReadAsStringAsync();
        log.Info($"Request Body = {jsonContent}");
    
        // some valication code ...

        _context = new CloudMediaContext(new MediaServicesCredentials(
        _mediaServicesAccountName,
        _mediaServicesAccountKey));

        // some AMS operations ...
      
        return req.CreateResponse(HttpStatusCode.BadRequest, "Generic Error.");
    }



## <a name="configure-function-app-settings"></a>Configurare le impostazioni dell'app per le funzioni

Quando si sviluppano le funzioni di Servizi multimediali, è utile aggiungere i parametri che verranno usati nelle funzioni per la sezione **Impostazioni app**. 

ad esempio:

![Impostazioni](./media/media-services-azure-functions/media-services-azure-functions001.png)


## <a name="next-step"></a>Passaggio successivo

A questo punto, si è pronti per iniziare a sviluppare un'applicazione di Servizi multimediali. Per altre informazioni, vedere [Usare i webhook di Azure per monitorare le notifiche dei processi di Servizi multimediali con .NET](media-services-dotnet-check-job-progress-with-webhooks.md);   

## <a name="media-services-learning-paths"></a>Percorsi di apprendimento di Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornire commenti e suggerimenti
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]


