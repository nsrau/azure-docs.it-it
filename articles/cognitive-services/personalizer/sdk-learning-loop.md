---
title: 'Guida introduttiva: Creare e usare un ciclo di apprendimento con SDK - Personalizza esperienze'
description: Questo argomento di avvio rapido illustra come creare e gestire una knowledge base usando l'SDK client.
ms.topic: quickstart
ms.date: 07/30/2020
ms.custom: devx-track-python, devx-track-javascript
zone_pivot_groups: programming-languages-set-six
ms.openlocfilehash: 480e860952c2659d053f8a5659e312feacff2d2b
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2020
ms.locfileid: "87874487"
---
# <a name="quickstart-personalizer-client-library"></a>Guida introduttiva: Libreria client di Personalizza esperienze

Visualizzare il contenuto personalizzato in questa guida di avvio rapido con il servizio Personalizza esperienze.

Introduzione alla libreria client di Personalizza esperienze. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

 * API Classificazione: seleziona l'elemento migliore, dalle azioni, in base alle informazioni in tempo reale fornite in merito al contenuto e al contesto.
 * API Premio: è possibile determinare il punteggio di ricompensa in base alle esigenze aziendali e quindi inviarlo a Personalizza esperienze con questa API. Questo punteggio può essere un singolo valore, ad esempio 1 per positivo e 0 per negativo, oppure un algoritmo creato in base alle esigenze aziendali.

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# SDK](./includes/quickstart-sdk-csharp.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js SDK](./includes/quickstart-sdk-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python SDK](./includes/quickstart-sdk-python.md)]
::: zone-end

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
>[Funzionamento di Personalizza esperienze](how-personalizer-works.md)

* [Informazioni su Personalizza esperienze](what-is-personalizer.md)
* [In quali situazioni è possibile usare Personalizza esperienze?](where-can-you-use-personalizer.md)
* [Risoluzione dei problemi](troubleshooting.md)
* Il codice sorgente per questo esempio è disponibile su [GitHub](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/blob/master/quickstarts/python/sample.py).
