---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 8530f4469a0c25f3c32e652e2b0752c51c28ff3f
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78191043"
---
Gli attributi di binding vengono definiti direttamente nel file function.json. A seconda del tipo di binding, potrebbero essere necessarie altre proprietà. La tabella di [configurazione dell'output della coda](../articles/azure-functions/functions-bindings-storage-queue-output.md#configuration) indica i campi necessari per un binding della coda di archiviazione di Azure. L'estensione semplifica l'aggiunta di binding nel file function.json. 

Per creare un binding, fare clic con il pulsante destro del mouse (CTRL+clic in macOS) sul file `function.json` nella cartella HttpTrigger e scegliere **Aggiungi binding**. Seguire i prompt per definire le proprietà seguenti per il nuovo binding:

| Prompt | valore | Descrizione |
| -------- | ----- | ----------- |
| **Selezionare la direzione di binding** | `out` | Il binding è un binding di output. |
| **Selezionare il binding con direzione** | `Azure Queue Storage` | Il binding è un binding della coda di archiviazione di Azure. |
| **Il nome usato per identificare questo binding nel codice** | `msg` | Nome che identifica il parametro di binding a cui viene fatto riferimento nel codice. |
| **La coda a cui verrà inviato il messaggio** | `outqueue` | Il nome della coda in cui scrive il binding. Se *queueName* non esiste, il binding lo crea al primo utilizzo. |
| **Selezionare l'impostazione da "local.setting.json"** | `AzureWebJobsStorage` | Il nome dell'impostazione dell'applicazione che contiene la stringa di connessione per l'account di archiviazione. L'impostazione `AzureWebJobsStorage` contiene la stringa di connessione per l'account di archiviazione creato con l'app per le funzioni. |

Viene aggiunto un binding alla matrice `bindings` nel file function.json, che dovrà essere come indicato di seguito:

:::code language="son" source="~/functions-docs-javascript/functions-add-output-binding-storage-queue-cli/HttpExample/function.json" range="18-24":::