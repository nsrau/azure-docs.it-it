---
title: Creare un'app per le funzioni dal portale di AzureCreate a function app from the Azure Portal
description: Creare una nuova app per le funzioni in Azure dal portale.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 086a543e75d083094d4dfa789e71afaba5da6d6e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368758"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Creare un'app per le funzioni dal portale di Azure

Questo argomento illustra come usare Funzioni di Azure per creare un'app per le funzioni nel portale di Azure.This topic shows you how to use Azure Functions to create a function app in the Azure portal. Un'app per le funzioni è un contenitore che ospita l'esecuzione delle singole funzioni. 

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Dopo aver creato l'app per le funzioni, è possibile creare singole funzioni in una o più lingue diverse. Creare funzioni [tramite il portale](functions-create-first-azure-function.md#create-function), [la distribuzione continua](functions-continuous-deployment.md) o tramite il [caricamento con FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Piani di servizio

Funzioni di Azure ha tre piani di servizio diversi: Piano di consumo, Piano Premium e piano dedicato (servizio app). È necessario scegliere il piano di servizio quando viene creata l'app per le funzioni e non può essere successivamente modificata. Per altre informazioni, vedere [Scegliere un piano di hosting di Funzioni di Azure](functions-scale.md).

Se si prevede di eseguire funzioni JavaScript in un piano dedicato (servizio app), è consigliabile scegliere un piano con un numero inferiore di core. Per altre informazioni, vedere le [informazioni di riferimento su JavaScript per le funzioni](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Requisiti dell'account di archiviazione

Quando si crea un'app per le funzioni, è necessario creare o collegare un account di archiviazione di Azure generico che supporti l'archiviazione BLOB, coda e tabelle. Le funzioni usano internamente l'archiviazione per operazioni come la gestione dei trigger e la registrazione dell'esecuzione delle funzioni. Alcuni account di archiviazione, come gli account di archiviazione solo BLOB, Archiviazione Premium di Azure e gli account di archiviazione di uso generico con replica ZRS, non supportano code e tabelle. Questi account vengono filtrati dal pannello Account di archiviazione quando si crea una nuova app per le funzioni.

>[!NOTE]
>Quando si usa il piano di hosting a consumo, i file del codice di funzione e la configurazione di binding vengono archiviati nell'archiviazione file di Azure nell'account di archiviazione principale. Quando si elimina l'account di archiviazione principale, il contenuto viene eliminato e non può essere ripristinato.

Per altre informazioni sui tipi di account di archiviazione, vedere [Introduzione ai servizi](../storage/common/storage-introduction.md#azure-storage-services)di archiviazione di Azure.To learn more about storage account types, see Introducing the Azure Storage Services . 

## <a name="next-steps"></a>Passaggi successivi

Mentre il portale di Azure rende più semplice creare e provare le funzioni, noi raccomandiamo lo [sviluppo locale](functions-develop-local.md). Dopo aver creato un'app per le funzioni nel portale, è comunque necessario aggiungere una funzione. 

> [!div class="nextstepaction"]
> [Aggiungere una funzione attivata tramite HTTP](functions-create-first-azure-function.md#create-function)
