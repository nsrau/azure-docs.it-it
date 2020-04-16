---
title: Passaggi per l'importazione dell'app
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/30/2020
ms.author: diberry
ms.openlocfilehash: b7b8befa0f5871b65b9b5621bfb99c659bf07235
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422739"
---
1. Nel [portale LUIS di anteprima](https://preview.luis.ai), nella pagina **My apps** (App personali), selezionare **+ New app for conversation** (Nuova app di conversazione), quindi **Import as JSON** (Importa come JSON). Trovare il file JSON salvato nel passaggio precedente. Non è necessario modificare il nome dell'app. Selezionare **Operazione completata**

1. Nella scheda **Versions** (Versioni) della sezione **Manage** (Gestisci) selezionare la versione `0.1`, quindi selezionare **Clone** (Clona) per clonare la versione e assegnarle un nuovo nome di 10 caratteri, `ml-entity`, infine selezionare **Done** (Fine) per completare il processo di clonazione. Poiché viene usato come parte della route dell'URL, il nome della versione non può contenere caratteri non validi per un URL.

    > [!TIP]
    > La clonazione in una nuova versione è una procedura consigliata prima di modificare l'app. Dopo aver cambiato la versione, esportarla come file con estensione json o lu e archiviare il file nel sistema di controllo del codice sorgente.

1. Selezionare **Build** (Compila) e quindi **Intents** (Finalità) per visualizzare le finalità, ovvero i blocchi principali di un'app LUIS.

    ![Passare dalla pagina Versions (Versioni) alla pagina Intents (Finalità).](../media/tutorial-machine-learned-entity/new-version-imported-app.png)