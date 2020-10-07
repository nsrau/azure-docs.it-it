---
title: includere file
description: includere file
services: cognitive-services
author: roy-har
manager: diberry
ms.service: cognitive-services
ms.date: 06/03/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: roy-har
ms.openlocfilehash: 8e67a6d0c98a3839922a79e9b452465087da1b69
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "84418039"
---
1. Selezionare [pizza-app-for-luis-v6.json](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-app-for-luis-v6.json) per aprire la pagina di GitHub per il file `pizza-app-for-luis.json`.
1. Toccare e tenere premuto a lungo o fare clic con il pulsante destro del mouse sul pulsante **Raw** e selezionare **Salva collegamento come** per salvare `pizza-app-for-luis.json` nel computer.
1. Accedere al [portale LUIS](https://www.luis.ai).
1. Selezionare [App personali](https://www.luis.ai/applications).
1. Nella pagina **App personali**, selezionare **+ Nuova app di conversazione**.
1. Selezionare **Importa come JSON**.
1. Nella finestra di dialogo **Importa nuova app** selezionare il pulsante **Scegli file**.
1. Selezionare il file `pizza-app-for-luis.json` scaricato e quindi selezionare **Apri**.
1. Nel campo **Nome** della finestra di dialogo **Importa nuova app** immettere un nome per l'app Pizza, quindi selezionare il pulsante **Fatto**.

L'app verrà importata.

Se viene visualizzata una finestra di dialogo **Come creare un'app LUIS efficace**, chiudere la finestra di dialogo.

## <a name="train-and-publish-the-pizza-app"></a>Eseguire il training dell’app Pizza e pubblicarla

Verrà visualizzata la pagina **Finalità** con un elenco delle finalità dell'app pizza.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

L'app Pizza è ora pronta per essere usata.

## <a name="record-the-app-id-prediction-key-and-prediction-endpoint-of-your-pizza-app"></a>Registrare l'ID app, la chiave di previsione e l'endpoint di previsione dell'app Pizza

Per usare la nuova app Pizza, sono necessari l'ID app, la chiave di previsione e l'endpoint previsione dell'app Pizza.

Per trovare questi valori:

1. Nella pagina **Finalità**, selezionare **GESTISCi**.
1. Nella pagina **Impostazioni applicazione**, registrare l’**ID app**.
1. Selezionare **Risorse di Azure**.
1. Dalla pagina **Risorse di Azure**, registrare la **chiave primaria**. Questo valore è la chiave di previsione.
1. Registrare l’**URL dell'endpoint**. Questo valore è l’endpoint di previsione.
