---
title: Creare un&quot;app per le funzioni dal portale di Azure | Microsoft Docs
description: Creare una nuova app per le funzioni in Servizio app di Azure dal portale.
services: functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/11/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: f9f1b95ade5f397fa1a3bcdc4165c81c1c9628c0
ms.contentlocale: it-it
ms.lasthandoff: 05/12/2017


---
# <a name="create-a-function-app-from-the-azure-portal"></a>Creare un'app per le funzioni dal portale di Azure

App per le funzioni di Azure usa l'infrastruttura di Servizio app di Azure. In questo argomento viene illustrato come creare un'app per le funzioni nel portale di Azure. Un'app per le funzioni è un contenitore che ospita l'esecuzione delle singole funzioni. Quando si crea un'app per le funzioni nel servizio app che ospita il piano, l'app per le funzioni può usare tutte le funzionalità del servizio app.

## <a name="create-a-function-app"></a>Creare un'app per le funzioni

[!INCLUDE [functions-create-function-app-portal](../../includes/functions-create-function-app-portal.md)]

Quando si crea un'app per le funzioni, inserire un **nome dell'app** valido, che contenga solo lettere, numeri e trattini. Il carattere di sottolineatura (**_**) non è consentito.

I nomi degli account di archiviazione devono avere una lunghezza compresa tra 3 e 24 caratteri e possono contenere solo numeri e lettere minuscole. Nome dell'account di archiviazione deve essere univoco all'interno di Azure. 

Dopo aver creato l'app per le funzioni, è possibile creare singole funzioni in una o più lingue diverse. Creare funzioni [tramite il portale](functions-create-first-azure-function.md#create-function), [la distribuzione continua](functions-continuous-deployment.md) o tramite il [caricamento con FTP](https://github.com/projectkudu/kudu/wiki/Accessing-files-via-ftp).

## <a name="service-plans"></a>Piani di servizio

Funzioni di Azure offre due piani di servizio diversi: piano a consumo e piano di servizio app. Il piano a consumo alloca automaticamente funzionalità di calcolo durante l'esecuzione del codice, aumenta il numero di istanze in base alla necessità per gestire il carico e quindi riduce il numero di istanze quando il codice non è in esecuzione. Il piano di servizio app consente all'app per le funzioni di accedere a tutte le funzionalità del servizio app. È necessario scegliere il piano di servizio quando viene creata l'app per le funzioni, che al momento non può essere modificato. Per altre informazioni, vedere [Scegliere un piano di hosting di Funzioni di Azure](functions-scale.md).

Se si prevede di eseguire funzioni JavaScript in un piano di servizio App, è necessario scegliere un piano con un minor numero di core. Per altre informazioni, vedere le [informazioni di riferimento su JavaScript per le funzioni](functions-reference-node.md#choose-single-core-app-service-plans).

<a name="storage-account-requirements"></a>

## <a name="storage-account-requirements"></a>Requisiti dell'account di archiviazione

Quando si crea un'app per le funzioni in servizio app, è necessario creare o collegare un account di Archiviazione di Azure di uso generico che supporti l'archiviazione BLOB, code e tabelle. Le funzioni usano internamente l'archiviazione per operazioni come la gestione dei trigger e la registrazione dell'esecuzione delle funzioni. Alcuni account di archiviazione, come gli account di archiviazione solo BLOB, Archiviazione Premium di Azure e gli account di archiviazione di uso generico con replica ZRS, non supportano code e tabelle. Questi account vengono filtrati dal pannello Account di archiviazione quando si crea una nuova app per le funzioni.

>[!NOTE]
>Quando si usa il piano di hosting a consumo, i file del codice di funzione e la configurazione di binding vengono archiviati nell'archiviazione file di Azure nell'account di archiviazione principale. Quando si elimina l'account di archiviazione principale, il contenuto verrà eliminato e non potrà essere ripristinato.

Per altre informazioni sui tipi di account di archiviazione, vedere [Introduzione ai servizi di archiviazione di Azure] (../storage/storage-introduction.md#introducing-the-azure-storage-services).

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Functions quickstart next steps](../../includes/functions-quickstart-next-steps.md)]




