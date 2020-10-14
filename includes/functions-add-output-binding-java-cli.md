---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 2b2c043e70aac14c7fc6f0b58aae257624b05d13
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "80673270"
---
In un progetto Java i binding vengono definiti come annotazioni di binding nel metodo della funzione. Il file *function.json* viene quindi generato automaticamente in base a queste annotazioni.

Passare al percorso del codice della funzione, _src/main/java_, aprire il file di progetto *Function.java* e aggiungere il parametro seguente alla definizione del metodo `run`:

```java
@QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") OutputBinding<String> msg
```

Il parametro `msg` è un tipo [`OutputBinding<T>`](/java/api/com.microsoft.azure.functions.outputbinding), che rappresenta una raccolta di stringhe scritte come messaggi in un binding di output al completamento della funzione. In questo caso, l'output è una coda di archiviazione denominata `outqueue`. La stringa di connessione per l'account di archiviazione è impostata dal metodo `connection`. Invece della stringa di connessione stessa, passare l'impostazione applicazione che contiene la stringa di connessione dell'account di archiviazione.

La definizione del metodo `run` dovrà ora essere come indicato nell'esempio seguente:  

```java
@FunctionName("HttpTrigger-Java")
public HttpResponseMessage run(
        @HttpTrigger(name = "req", methods = {HttpMethod.GET, HttpMethod.POST}, authLevel = AuthorizationLevel.FUNCTION)  
        HttpRequestMessage<Optional<String>> request, 
        @QueueOutput(name = "msg", queueName = "outqueue", connection = "AzureWebJobsStorage") 
        OutputBinding<String> msg, final ExecutionContext context) {
    ...
}
```