---
title: Passaggi per l'importazione dell'app
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: b0d1735df7a3ac36e20af771939aec6b8013f2df
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74840817"
---
1. Nel [portale LUIS di anteprima](https://preview.luis.ai) nella pagina **My apps** (App personali) selezionare **Import** (Importa), quindi **Import as JSON** (Importa come JSON). Trovare il file JSON salvato nel passaggio precedente. Non è necessario modificare il nome dell'app. Selezionare **Operazione completata**

1. Nella scheda **Versions** (Versioni) della sezione **Manage** (Gestisci) selezionare la versione, quindi selezionare **Clone** (Clona) per clonare la versione e assegnarle un nuovo nome di 10 caratteri, infine selezionare **Done** (Fine) per completare il processo di clonazione. Poiché viene usato come parte della route dell'URL, il nome della versione non può contenere caratteri non validi per un URL.

    > [!TIP]
    > La clonazione in una nuova versione è una procedura consigliata prima di modificare l'app. Dopo aver completato una versione, esportare la versione, come file con estensione json o lu, e archiviare il file nel sistema di controllo del codice sorgente.

1. Selezionare **Build** (Compila) e quindi **Intents** (Finalità) per visualizzare le finalità, ovvero i blocchi principali di un'app LUIS.

    ![Passare dalla pagina Versions (Versioni) alla pagina Intents (Finalità).](../media/tutorial-machine-learned-entity/new-version-imported-app.png)