---
title: 'Avvio rapido: Librerie client di LUIS (Language Understanding) SDK'
description: Questa guida di avvio rapido illustra come creare ed eseguire query su un'app LUIS con le librerie client di LUIS SDK con C#, Python o JavaScript.
ms.topic: quickstart
ms.date: 09/01/2020
keywords: Azure, intelligenza artificiale, IA, elaborazione del linguaggio naturale, NLP, comprensione del linguaggio naturale, NLU, conversazione di intelligenza artificiale, chatbot IA, LUIS, Azure LUIS
ms.custom: devx-track-python, devx-track-javascript, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-diberry-3core
ms.openlocfilehash: 6bcdca85125d44475fadfd195c1dfda88f761f88
ms.sourcegitcommit: 5ed504a9ddfbd69d4f2d256ec431e634eb38813e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/02/2020
ms.locfileid: "89323086"
---
# <a name="quickstart-language-understanding-luis-sdk-client-libraries-to-create-and-query-your-luis-app"></a>Avvio rapido: Librerie client di LUIS (Language Understanding) SDK per creare ed eseguire query sull'app LUIS

Questa guida di avvio rapido illustra come creare ed eseguire query su un'app LUIS con le librerie client di LUIS SDK con C#, Python o JavaScript.

Language Understanding (LUIS) consente di applicare l'intelligence dell'apprendimento automatico personalizzato al testo in linguaggio naturale parlato di un utente per prevedere il significato generale ed estrarre informazioni pertinenti e dettagliate.

* La libreria client di **Authoring SDK** consente di creare, modificare, eseguire il training e pubblicare l'app LUIS. * La libreria client di **Prediction Runtime SDK** consente di eseguire una query sull'app pubblicata.

::: zone pivot="programming-language-csharp"
[!INCLUDE [LUIS development with C# SDK](./includes/sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [LUIS development with Node.js SDK](./includes/sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [LUIS development with Python SDK](./includes/sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Pulire le risorse

È possibile eliminare l'app dal [portale LUIS](https://www.luis.ai) ed eliminare le risorse di Azure dal [portale di Azure](https://portal.azure.com/).

## <a name="troubleshooting"></a>Risoluzione dei problemi

* Autenticazione alla libreria client: gli errori di autenticazione indicano in genere che sono stati usati un endpoint e una chiave non corretti. Per comodità, questa guida di avvio rapido usa la chiave di creazione e l'endpoint per il runtime di previsione, ma funziona solo se non è già stata usata la quota mensile. Se non è possibile usare la chiave di creazione e l'endpoint, è necessario usare la chiave e l'endpoint del runtime di previsione per accedere alla libreria client di Prediction Runtime SDK.
* Creazione di entità: se viene restituito un errore durante la creazione dell'entità di Machine Learning annidata usata in questa esercitazione, assicurarsi di aver copiato il codice senza averlo modificato per creare un'altra entità.
* Creazione di espressioni di esempio: se viene restituito un errore durante la creazione dell'espressione di esempio etichettata usata in questa esercitazione, assicurarsi di aver copiato il codice senza averlo modificato per creare un altro esempio etichettato.
* Training: un eventuale errore di training in genere indica un'app vuota (senza finalità con espressioni di esempio) o un'app con finalità o entità in formato non valido.
* Errori vari: poiché il codice chiama le librerie client con oggetti JSON e di testo, assicurarsi di non aver modificato il codice.

Altri errori: se viene ricevuto un errore non incluso nell'elenco precedente, segnalarlo inviando un feedback nella parte inferiore della pagina. Includere il linguaggio di programmazione e la versione delle librerie client installate. 

## <a name="next-steps"></a>Passaggi successivi

* [Che cos'è l'API Language Understanding (LUIS)?](what-is-luis.md)
* [Novità](whats-new.md)
* [Finalità](luis-concept-intent.md), [entità](luis-concept-entity-types.md), [espressioni di esempio](luis-concept-utterance.md) ed [entità predefinite](luis-reference-prebuilt-entities.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code).
