---
title: Passaggi per l'importazione dell'app
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 05/07/2020
ms.author: diberry
ms.openlocfilehash: 4c87ea9ebb2dd7aec36025e4b45f74e186ca1cd4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83588785"
---
1. Nella pagina **My apps** (App personali) del [portale LUIS](https://www.luis.ai) selezionare **+ New app for conversation** (Nuova app di conversazione) e quindi **Import as JSON** (Importa come JSON). Trovare il file JSON salvato nel passaggio precedente. Non è necessario modificare il nome dell'app. Selezionare **Operazione completata**

1. Nella scheda **Versions** (Versioni) della sezione **Manage** (Gestisci) selezionare la versione `0.1`, quindi selezionare **Clone** (Clona) per clonare la versione e assegnarle il nuovo nome `ml-entity` e infine selezionare **Done** (Fine) per completare il processo di clonazione. Poiché viene usato come parte della route dell'URL, il nome della versione non può contenere caratteri non validi per un URL.

    > [!TIP]
    > La clonazione in una nuova versione è una procedura consigliata prima di modificare l'app. Dopo aver cambiato la versione, esportarla come file con estensione json o lu e archiviare il file nel sistema di controllo del codice sorgente.

1. Selezionare **Build** (Compila) e quindi **Intents** (Finalità) per visualizzare le finalità, ovvero i blocchi principali di un'app LUIS.

    ![Passare dalla pagina Versions (Versioni) alla pagina Intents (Finalità).](../media/tutorial-machine-learned-entity/new-version-imported-app.png)