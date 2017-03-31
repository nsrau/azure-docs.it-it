---
title: Uso di proxy in Funzioni di Azure | Microsoft Docs
description: Informazioni generali sull&quot;uso dei proxy in Funzioni di Azure
services: functions
documentationcenter: 
author: mattchenderson
manager: erikre
editor: 
ms.assetid: 
ms.service: functions
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/19/2017
ms.author: mahender
translationtype: Human Translation
ms.sourcegitcommit: facdd2abbb90a85535b8936ea655a824134c4c8b
ms.openlocfilehash: 3e8b8f9908cf24baf7a5d70521c79dbd470001f8
ms.lasthandoff: 02/22/2017


---
# <a name="working-with-azure-functions-proxies-preview"></a>Uso di proxy in Funzioni di Azure (anteprima)

> [!Note] 
> I prozy di Funzioni di Azure sono attualmente in anteprima. Sono gratuite in anteprima, ma si applicano le tariffe standard per le funzioni quando vengono eseguiti i proxy. Per altre informazioni, vedere [Piano tariffario di Funzioni di Azure](https://azure.microsoft.com/pricing/details/functions/).

Questo articolo illustra come configurare e usare i proxy in Funzioni di Azure. Questa funzionalità consente di specificare gli endpoint nell'app per le funzioni implementati da un'altra risorsa. È possibile usare questi proxy per suddividere un'API di grandi dimensioni in più app per le funzioni (come in un'architettura di microservizio), pur continuando a presentare una singola superficie API per i client.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]


## <a name="a-nameenableaenabling-azure-functions-proxies"></a><a name="enable"></a>Abilitazione dei proxy di Funzioni di Azure

I proxy non sono abilitati per impostazione predefinita. È possibile creare i proxy quando la funzionalità è disabilitata, ma non è possibile eseguirli. Nei passaggi successivi verrà illustrato come abilitare i proxy:

1. Accedere al [Portale di Azure] e passare all'app per le funzioni.
2. Selezionare **Impostazioni dell'app per le funzioni**.
3. Attivare **Enable Azure Functions Proxies (preview)** (Abilita i proxy di Funzioni di Azure (anteprima)).

È inoltre possibile ritornare qui per aggiornare il runtime del proxy man mano che le nuove funzioni diventano disponibili.


## <a name="creating-a-proxy"></a>Creazione di un proxy

In questa sezione verrà descritto come creare un proxy nel portale delle funzioni.

1. Accedere al [Portale di Azure] e passare all'app per le funzioni.
2. Nel riquadro di spostamento a sinistra selezionare **Nuovo proxy**.
3. Dare un nome al proxy.
4. Configurare l'endpoint esposto in questa app per le funzioni, specificando il **modello di route** e i **metodi HTTP**. Questi parametri si comportano in base alle regole dei [trigger HTTP]
5. Impostare l'**URL di back-end** su un altro endpoint. Potrebbe trattarsi di una funzione in un'altra app per le funzioni oppure di qualsiasi altra API.
6. Fare clic su Crea.

Il proxy è ora presente come un nuovo endpoint sull'app per le funzioni. Dalla prospettiva del client, è equivalente a un HttpTrigger nelle Funzioni di Azure. È possibile provare il nuovo proxy copiando l'URL del proxy ed eseguendo un test con il proprio client HTTP preferito.


## <a name="a-namemodify-requestsamodifying-backend-requests"></a><a name="modify-requests"></a>Modifica delle richieste di back-end

Il parametro URL di back-end non deve necessariamente essere statico. È possibile stabilizzarlo in base all'input dalle impostazioni dell'applicazione o della richiesta.


### <a name="using-request-parameters"></a>Uso dei parametri della richiesta

I parametri usati nel modello di route possono essere usati come input per la proprietà URL di back-end. Per fare riferimento ai valori si usa il nome, racchiuso tra parentesi graffe "{}".

Ad esempio, se un proxy ha un modello di route come `/pets/{petId}`, l'URL di back-end può includere il valore `{petId}`, come in `https://<AnotherApp>.azurewebsites.net/api/pets/{petId}`. Se il modello di route termina con un carattere jolly, ad esempio `/api/{*restOfPath}`, il valore `{restOfPath}` sarà una rappresentazione di stringa dei segmenti del percorso rimanente della richiesta in ingresso.


### <a name="using-application-settings"></a>Uso delle impostazioni dell'applicazione

È inoltre possibile fare riferimento alle [impostazioni dell'applicazione](https://docs.microsoft.com/azure/azure-functions/functions-how-to-use-azure-function-app-settings#develop) racchiudendo il nome dell'impostazione tra i segni di percentuale "%".

Ad esempio, per l'URL di back-end di `https://%ORDER_PROCESSING_HOST%/api/orders`, "%ORDER_PROCESSING_HOST%" verrà sostituito con il valore dell'impostazione ORDER_PROCESSING_HOST.

> [!TIP] 
> Usare le impostazioni dell'applicazione per gli host di back-end quando si dispone di più distribuzioni o ambienti di test. In questo modo, è possibile assicurarsi di comunicare sempre con il back-end corretto per quell'ambiente.



## <a name="deployment-methods"></a>Metodi di distribuzione

I proxy configurati vengono archiviati in un file proxies.json, situato nella radice di una directory dell'app per le funzioni. È possibile modificare manualmente questo file e distribuirlo come parte dell'app quando si usa uno dei [metodi di distribuzione](https://docs.microsoft.com/azure/azure-functions/functions-continuous-deployment) che la funzione supporta.

La funzione deve essere abilitata per poter elaborare il file. È possibile farlo seguendo le istruzioni in [Abilitazione dei proxy di Funzioni di Azure](#enable).

Il file proxies.JSON è definito da un oggetto proxy, composto da proxy denominati e dalle relative definizioni. Un esempio di file apparirà come segue:

```json
{
    "proxies": {
        "proxy1": {
            "matchCondition": {
                "methods": [ "GET" ],
                "route": "/api/{test}"
            },
            "backendUri": "https://<AnotherApp>.azurewebsites.net/api/<FunctionName>"
        }
    }
}
```

Ogni proxy ha un nome descrittivo, ad esempio "proxy1" come nell'esempio precedente. L'oggetto di definizione del proxy corrispondente viene definito dalle proprietà seguenti:

* **matchCondition**: obbligatorio, un oggetto che definisce le richieste che attiveranno l'esecuzione di questo proxy. Contiene due proprietà condivise con i [trigger HTTP]:
    * _methods_: è una matrice dei metodi HTTP a cui il proxy risponderà. Se non viene specificata, il proxy risponderà a tutti i metodi HTTP sul percorso.
    * _route_: obbligatorio, definisce il modello di route, controllando a quali URL delle richieste il proxy risponderà. A differenza dei trigger HTTP, non vi è alcun valore predefinito.
* **backendUri**: l'URL della risorsa di back-end a cui la richiesta deve essere trasmessa tramite proxy. Questo valore può basarsi su modelli, come descritto in [Modifica delle richieste di back-end](#modify-requests). Se questa proprietà non è inclusa, Funzioni di Azure risponderà con un HTTP 200 OK.

> [!Note] 
> I proxy di Funzioni di Azure della proprietà di route non rispettano la proprietà routePrefix della configurazione di host di funzioni. Se si desidera includere un prefisso, ad esempio /api, deve essere incluso nella proprietà di route.



[Portale di Azure]: https://portal.azure.com
[trigger HTTP]: https://docs.microsoft.com/azure/azure-functions/functions-bindings-http-webhook#http-trigger
