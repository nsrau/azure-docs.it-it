---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/02/2019
ms.openlocfilehash: 2f5b03dd0170da9a9869183d7a412688509525ef
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60599380"
---
L'impostazione `ApplicationInsights` consente di aggiungere al contenitore il supporto per i dati di telemetria di [Azure Application Insights](https://docs.microsoft.com/azure/application-insights). Application Insights offre funzionalità di monitoraggio avanzate del contenitore. È possibile monitorare con facilità la disponibilità, le prestazioni e l'utilizzo del contenitore. È anche possibile identificare e diagnosticare rapidamente gli errori nel contenitore.

La tabella seguente illustra le impostazioni di configurazione supportate nella sezione `ApplicationInsights`.

|Obbligatorio| NOME | Tipo di dati | DESCRIZIONE |
|--|------|-----------|-------------|
|No | `InstrumentationKey` | string | Chiave di strumentazione dell'istanza di Application Insights a cui vengono inviati i dati di telemetria per il contenitore. Per altre informazioni, vedere [Application Insights per ASP.NET Core](https://docs.microsoft.com/azure/application-insights/app-insights-asp-net-core). <br><br>Esempio:<br>`InstrumentationKey=123456789`|

