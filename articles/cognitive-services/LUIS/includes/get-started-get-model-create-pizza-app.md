---
title: includere file
description: includere file
services: cognitive-services
author: roy-har
manager: diberry
ms.service: cognitive-services
ms.date: 06/04/2020
ms.subservice: language-understanding
ms.topic: include
ms.custom: include file
ms.author: roy-har
ms.openlocfilehash: 9965e4c856fdef2af17b116264ad5344ebc97eb2
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2020
ms.locfileid: "84466880"
---
Creare l'app Pizza.

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

Se viene visualizzata una finestra di dialogo **Come creare un'app di LUIS efficace**, chiudere la finestra di dialogo.

## <a name="train-and-publish-the-pizza-app"></a>Eseguire il training dell’app Pizza e pubblicarla

Verrà visualizzata la pagina **Finalità** con un elenco delle finalità dell'app pizza.

[!INCLUDE [How to train](howto-train.md)]

[!INCLUDE [How to publish](howto-publish.md)]

## <a name="add-an-authoring-resource-to-the-pizza-app"></a>Aggiungere una risorsa di creazione all'app Pizza

1. Selezionare **GESTISCI**.
1. Selezionare **Risorse di Azure**.
1. Selezionare **Risorsa Creazione**.
1. Selezionare **Change authoring resource** (Modifica risorsa Creazione).

Se è disponibile una risorsa Creazione, immettere un valore nei campi **Nome del tenant**, **Nome sottoscrizione** e **LUIS resource name** (Nome risorsa LUIS) della risorsa Creazione.

Se non è disponibile alcuna risorsa Creazione:

1. Selezionare **Crea nuova risorsa**.
1. Immettere un valore in **Nome del tenant**, **Nome della risorsa**, **Nome sottoscrizione** e **Nome gruppo di risorse di Azure**.

L'app Pizza è ora pronta per essere usata.

## <a name="record-the-access-values-for-your-pizza-app"></a>Prendere nota dei valori di accesso per l'app Pizza

Per usare la nuova app Pizza, sono necessari l'ID app, la chiave di creazione e l'endpoint di creazione dell'app Pizza.

Per trovare questi valori:

1. Nella pagina **Finalità**, selezionare **GESTISCi**.
1. Nella pagina **Impostazioni applicazione**, registrare l’**ID app**.
1. Selezionare **Risorse di Azure**.
1. Selezionare **Risorsa Creazione**.
1. Nella scheda **Risorsa Creazione** prendere nota del valore di **Chiave primaria**. Questo valore corrisponde alla chiave di creazione.
1. Registrare l’**URL dell'endpoint**. Questo valore corrisponde all'endpoint di creazione.
