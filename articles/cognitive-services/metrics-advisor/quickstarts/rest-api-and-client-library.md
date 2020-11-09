---
title: API REST delle librerie client di Advisor metriche
titleSuffix: Azure Cognitive Services
description: Seguire questo argomento di avvio rapido per connettere le applicazioni all'API Advisor metriche da Servizi cognitivi di Azure.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: quickstart
ms.date: 10/14/2020
ms.author: mbullwin
zone_pivot_groups: programming-languages-metrics-monitor
ms.openlocfilehash: 5b3df5f4b41b2beeec68b667863f6ca7715df47b
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186938"
---
# <a name="quickstart-use-the-client-libraries-or-rest-apis-to-customize-your-solution"></a>Avvio rapido: Usare le librerie client o le API REST per personalizzare la soluzione

Introduzione all'API REST o alle librerie client di Advisor metriche. Seguire questi passaggi per installare il pacchetto e provare il codice di esempio per le attività di base.

Usare Advisor metriche per eseguire le operazioni seguenti:

* Aggiungere un feed di dati da un'origine dati
* Controllare lo stato dell'inserimento
* Configurare il rilevamento e gli avvisi 
* Eseguire query sui risultati del rilevamento anomalie
* Diagnosticare le anomalie


::: zone pivot="programming-language-csharp"

[!INCLUDE [REST API quickstart](../includes/quickstarts/csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [REST API quickstart](../includes/quickstarts/java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [REST API quickstart](../includes/quickstarts/javascript.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [REST API quickstart](../includes/quickstarts/python.md)]

::: zone-end

::: zone pivot="programming-language-rest-api"

[!INCLUDE [REST API quickstart](../includes/quickstarts/rest-api.md)]

::: zone-end

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse vengono eliminate anche tutte le altre risorse associate.

* [Portale](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Interfaccia della riga di comando di Azure](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Passaggi successivi

- [Usare il portale Web](web-portal.md)
- [Eseguire l'onboarding dei feed di dati](../how-tos/onboard-your-data.md)
    - [Gestire feed di dati](../how-tos/manage-data-feeds.md)
    - [Configurazioni per origini dati diverse](../data-feeds-from-different-sources.md)
- [Configurare le metriche e ottimizzare la configurazione del rilevamento](../how-tos/configure-metrics.md)
- [Regolare il rilevamento anomalie usando il feedback](../how-tos/anomaly-feedback.md)