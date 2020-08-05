---
title: 'Avvio rapido: Libreria client di Analisi del testo v3 | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Usare questo argomento di avvio rapido per connettere le applicazioni all'API Analisi del testo di Servizi cognitivi di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: quickstart
ms.date: 07/27/2020
ms.author: aahi
ms.custom: tracking-python, devx-track-javascript
zone_pivot_groups: programming-languages-text-analytics
ms.openlocfilehash: 4aba103bcb3593d9baef256ce04a9ab6754ed900
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87407669"
---
# <a name="quickstart-use-the-text-analytics-client-library"></a>Avvio rapido: Usare la libreria client di Analisi del testo

Introduzione alla libreria client di Analisi del testo. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

Usare la libreria client di Analisi del testo per eseguire queste operazioni:

* Analisi del sentiment
* Rilevamento della lingua
* Riconoscimento delle entità
* Estrazione di frasi chiave

::: zone pivot="programming-language-csharp"

> [!IMPORTANT]
> * L'ultima versione stabile dell'API Analisi del testo è `3.0`.
>    * Assicurarsi di seguire solo le istruzioni per la versione in uso.
> * Il codice di questo articolo usa metodi sincroni e archiviazione con credenziali non protette per motivi di semplicità. Per gli scenari di produzione, è consigliabile usare metodi asincroni in batch per favorire prestazioni e scalabilità. Vedere la documentazione di riferimento di seguito.

[!INCLUDE [C# quickstart](../includes/quickstarts/csharp-sdk.md)]

::: zone-end

::: zone pivot="programming-language-java"

> [!IMPORTANT]
> * L'ultima versione stabile dell'API Analisi del testo è `3.0`.
> * Il codice di questo articolo usa metodi sincroni e archiviazione con credenziali non protette per motivi di semplicità. Per gli scenari di produzione, è consigliabile usare metodi asincroni in batch per favorire prestazioni e scalabilità. Vedere la documentazione di riferimento di seguito.

[!INCLUDE [Java quickstart](../includes/quickstarts/java-sdk.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

> [!IMPORTANT]
> * L'ultima versione stabile dell'API Analisi del testo è `3.0`.
>    * Assicurarsi di seguire solo le istruzioni per la versione in uso.
> * Il codice di questo articolo usa metodi sincroni e archiviazione con credenziali non protette per motivi di semplicità. Per gli scenari di produzione, è consigliabile usare metodi asincroni in batch per favorire prestazioni e scalabilità. Vedere la documentazione di riferimento di seguito.
> * È anche possibile eseguire questa versione della libreria client di Analisi del testo [nel browser](https://github.com/Azure/azure-sdk-for-js/blob/master/documentation/Bundling.md).

[!INCLUDE [NodeJS quickstart](../includes/quickstarts/nodejs-sdk.md)]

::: zone-end

::: zone pivot="programming-language-python"

> [!IMPORTANT]
> * L'ultima versione stabile dell'API Analisi del testo è `3.0`.
>    * Assicurarsi di seguire solo le istruzioni per la versione in uso.
> * Il codice di questo articolo usa metodi sincroni e archiviazione con credenziali non protette per motivi di semplicità. Per gli scenari di produzione, è consigliabile usare metodi asincroni in batch per favorire prestazioni e scalabilità. Vedere la documentazione di riferimento di seguito. 

[!INCLUDE [Python quickstart](../includes/quickstarts/python-sdk.md)]

::: zone-end

::: zone pivot="programming-language-other"

## <a name="additional-language-support"></a>Elementi del linguaggio aggiuntivi supportati

Se si è fatto clic su questa scheda, è probabile che non sia stato visualizzato un argomento di avvio rapido nel linguaggio di programmazione preferito. Sono però disponibili altri argomenti di avvio rapido. Usare la tabella per trovare l'esempio appropriato per il proprio linguaggio di programmazione.

| Linguaggio | Versione disponibile | 
|----------|------------------------|
| Ruby     | [Versione 2.1](ruby-sdk.md) | 
| Go       | [Versione 2.1](go-sdk.md) | 

::: zone-end

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esplorare una soluzione](../text-analytics-user-scenarios.md#analyze-recorded-inbound-customer-calls)

* [Panoramica di Analisi del testo](../overview.md)
* [Analisi del sentiment](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [Riconoscimento delle entità](../how-tos/text-analytics-how-to-entity-linking.md)
* [Rilevare la lingua](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [Riconoscimento della lingua](../how-tos/text-analytics-how-to-language-detection.md)
