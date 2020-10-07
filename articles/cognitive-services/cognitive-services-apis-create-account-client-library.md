---
title: Creare una risorsa di Servizi cognitivi con la libreria client di gestione di Azure
titleSuffix: Azure Cognitive Services
description: Creare e gestire le risorse di Servizi cognitivi di Azure con la libreria client di gestione di Azure.
services: cognitive-services
keywords: servizi cognitivi, intelligenza cognitiva, soluzioni cognitive, servizi di intelligenza artificiale
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: quickstart
ms.date: 09/14/2020
ms.author: pafarley
zone_pivot_groups: programming-languages-set-ten
ms.openlocfilehash: e8628d051db7f5066a81171567f6f7e54fb0ab97
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91262451"
---
# <a name="quickstart-create-a-cognitive-services-resource-using-the-azure-management-client-library"></a>Avvio rapido: Creare una risorsa di Servizi cognitivi con la libreria client di gestione di Azure

Seguire questo argomento di avvio rapido per creare e gestire risorse di Servizi cognitivi di Azure con la libreria client di gestione di Azure.

I Servizi cognitivi di Azure sono servizi basati sul cloud con API REST ed SDK delle librerie client che consentono agli sviluppatori di integrare l'intelligenza cognitiva nelle applicazioni senza la necessità di competenze o conoscenze dirette di intelligenza artificiale e data science. I Servizi cognitivi di Azure consentono agli sviluppatori di aggiungere facilmente funzionalità cognitive alle applicazioni con soluzioni cognitive in grado di vedere, ascoltare, parlare, comprendere e persino di iniziare a ragionare.

I singoli servizi di intelligenza artificiale sono rappresentati da [risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) di Azure create nella sottoscrizione di Azure. Dopo aver creato una risorsa, è possibile usare le chiavi e gli endpoint generati per autenticare le applicazioni.

::: zone pivot="programming-language-csharp"

[!INCLUDE [C# SDK quickstart](includes/quickstarts/management-csharp.md)]

::: zone-end

::: zone pivot="programming-language-java"

[!INCLUDE [Java SDK quickstart](includes/quickstarts/management-java.md)]

::: zone-end

::: zone pivot="programming-language-javascript"

[!INCLUDE [NodeJS SDK quickstart](includes/quickstarts/management-node.md)]

::: zone-end

::: zone pivot="programming-language-python"

[!INCLUDE [Python SDK quickstart](includes/quickstarts/management-python.md)]

::: zone-end
