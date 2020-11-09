---
title: Passaggi per l'importazione dell'app
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 10/16/2020
ms.openlocfilehash: b70a22e502de5441b5c91a8f33357375834f54e1
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93130730"
---
1. Nella pagina **My apps** (App personali) del [portale LUIS](https://www.luis.ai) selezionare **+ New app for conversation** (Nuova app di conversazione) e quindi **Import as JSON** (Importa come JSON). Trovare il file JSON salvato nel passaggio precedente. Non è necessario modificare il nome dell'app. Selezionare **Operazione completata**

1. Nella scheda **Versions** (Versioni) della sezione **Manage** (Gestisci) selezionare la versione `0.1`, quindi selezionare **Clone** (Clona) per clonare la versione e assegnarle il nuovo nome `ml-entity` e infine selezionare **Done** (Fine) per completare il processo di clonazione. Poiché viene usato come parte della route dell'URL, il nome della versione non può contenere caratteri non validi per un URL.

    > [!TIP]
    > La clonazione in una nuova versione è una procedura consigliata prima di modificare l'app. Dopo aver cambiato la versione, esportarla come file con estensione json o lu e archiviare il file nel sistema di controllo del codice sorgente.

1. Selezionare **Build** (Compila) nella parte superiore dello schermo, quindi fare clic su **Intents** (Finalità) nel menu di spostamento sinistro. Verranno visualizzate e finalità dell'app, che rappresentano i principali blocchi costitutivi di un'app LUIS.
