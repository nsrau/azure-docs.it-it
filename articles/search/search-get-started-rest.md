---
title: 'Avvio rapido: Creare un indice di ricerca con le API REST'
titleSuffix: Azure Cognitive Search
description: Questo argomento di avvio rapido sulle API REST illustra come chiamare le API REST di Ricerca cognitiva di Azure con Postman o Visual Studio Code.
zone_pivot_groups: URL-test-interface-rest-apis
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.openlocfilehash: 971ee806d8b5f9a336b40d96d500ce47d2f5166e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/17/2020
ms.locfileid: "94711333"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-rest-apis"></a>Avvio rapido: Creare un indice di Ricerca cognitiva di Azure con le API REST

Questo articolo illustra come formulare richieste all'API REST in modo interattivo usando le [API REST di Ricerca cognitiva di Azure](/rest/api/searchservice) e un client API per l'invio e la ricezione di richieste. Con un client API e queste istruzioni è possibile inviare richieste e visualizzare le risposte prima di scrivere codice.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

::: zone pivot="url-test-tool-rest-postman"

[!INCLUDE [Send requests using Postman](includes/search-get-started-rest-postman.md)]

::: zone-end

::: zone pivot="url-test-tool-rest-vscode-ext"

[!INCLUDE [Send requests using Visual Studio Code](includes/search-get-started-rest-vscode-ext.md)]

::: zone-end

## <a name="clean-up-resources"></a>Pulire le risorse

Quando si lavora nella propria sottoscrizione, alla fine di un progetto è opportuno verificare se le risorse create sono ancora necessarie. Le risorse che rimangono in esecuzione hanno un costo. È possibile eliminare le singole risorse oppure il gruppo di risorse per eliminare l'intero set di risorse.

Per trovare e gestire le risorse nel portale, usare il collegamento **Tutte le risorse** o **Gruppi di risorse** nel riquadro di spostamento a sinistra.

Se si usa un servizio gratuito, tenere presente che il numero di indicizzatori e origini dati è limitato a tre. Per non superare il limite, è possibile eliminare i singoli elementi nel portale. 

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come eseguire le attività principali, è possibile procedere con le chiamate ad API REST aggiuntive per funzionalità più avanzate, come gli indicizzatori o la [configurazione di una pipeline di arricchimento](cognitive-search-tutorial-blob.md), che aggiunge trasformazioni del contenuto all'indicizzazione. Per il passaggio successivo, vedere il collegamento seguente:

> [!div class="nextstepaction"]
> [Esercitazione: Usare REST e intelligenza artificiale per generare contenuto ricercabile dai BLOB di Azure](cognitive-search-tutorial-blob.md)