---
title: Create a function app from the Azure Portal
description: Create a new function app in Azure from the portal.
ms.topic: conceptual
ms.date: 08/29/2019
ms.custom: mvc
ms.openlocfilehash: 74eaa6837f362c849277a761da3ae79c3a8ac353
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74230781"
---
# <a name="create-a-function-app-from-the-azure-portal"></a>Creare un'app per le funzioni dal portale di Azure

This topic shows you how to use Azure Functions to create a function app in the Azure portal. Un'app per le funzioni è un contenitore che ospita l'esecuzione delle singole funzioni. 

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Quando si crea un'app per le funzioni, inserire un **nome dell'app** valido, che contenga solo lettere, numeri e trattini. Il carattere di sottolineatura ( **_** ) non è consentito.

I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. Nome dell'account di archiviazione deve essere univoco all'interno di Azure. 

Dopo aver creato l'app per le funzioni, è possibile creare singole funzioni in una o più lingue diverse. Creare funzioni [tramite il portale](functions-create-first-azure-function.md#create-function), [la distribuzione continua](functions-continuous-deployment.md) o tramite il [caricamento con FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Piani di servizio

Azure Functions has three different service plans: Consumption plan, Premium plan, and Dedicated (App Service) plan. You must choose your service plan when your function app is created, and it cannot subsequently be changed. Per altre informazioni, vedere [Scegliere un piano di hosting di Funzioni di Azure](functions-scale.md).

If you are planning to run JavaScript functions on a Dedicated (App Service) plan, you should choose a plan with fewer cores. Per altre informazioni, vedere le [informazioni di riferimento su JavaScript per le funzioni](functions-reference-node.md#choose-single-vcpu-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Requisiti dell'account di archiviazione

When creating a function app, you must create or link to a general-purpose Azure Storage account that supports Blob, Queue, and Table storage. Le funzioni usano internamente l'archiviazione per operazioni come la gestione dei trigger e la registrazione dell'esecuzione delle funzioni. Alcuni account di archiviazione, come gli account di archiviazione solo BLOB, Archiviazione Premium di Azure e gli account di archiviazione di uso generico con replica ZRS, non supportano code e tabelle. Questi account vengono filtrati dal pannello Account di archiviazione quando si crea una nuova app per le funzioni.

>[!NOTE]
>Quando si usa il piano di hosting a consumo, i file del codice di funzione e la configurazione di binding vengono archiviati nell'archiviazione file di Azure nell'account di archiviazione principale. Quando si elimina l'account di archiviazione principale, il contenuto verrà eliminato e non potrà essere ripristinato.

Per altre informazioni sui tipi di account di archiviazione, vedere l'[introduzione ai servizi di Archiviazione di Azure](../storage/common/storage-introduction.md#azure-storage-services). 

## <a name="next-steps"></a>Passaggi successivi

Mentre il portale di Azure rende più semplice creare e provare le funzioni, noi raccomandiamo lo [sviluppo locale](functions-develop-local.md). Dopo aver creato un'app per le funzioni nel portale, è comunque necessario aggiungere una funzione. 

> [!div class="nextstepaction"]
> [Aggiungere una funzione attivata tramite HTTP](functions-create-first-azure-function.md#create-function)
