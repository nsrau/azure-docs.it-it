---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2019
ms.openlocfilehash: 1a3de57446c5c92afbc5dd5901e284bc3580f9db
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2019
ms.locfileid: "54479012"
---
Questo contenitore ha le impostazioni di configurazione seguenti:

|Obbligatoria|Impostazione|Scopo|
|--|--|--|
|Yes|[ApiKey](#apikey-setting)|Si usa per rilevare le informazioni di fatturazione.|
|No |[ApplicationInsights](#applicationinsights-setting)|Consente di aggiungere al contenitore il supporto per i dati di telemetria di [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Yes|[Fatturazione](#billing-setting)|Specifica l'URI dell'endpoint della risorsa del servizio in Azure.|
|Yes|[Eula](#eula-setting)| Indica che è stata accettata la licenza per il contenitore.|
|No |[Fluentd](#fluentd-settings)|Scrivere il log e, facoltativamente, i dati delle metriche in un server Fluentd.|
|No |[Proxy HTTP](#http-proxy-credentials-settings)|Configurare un proxy HTTP per eseguire le richieste in uscita.|
|No |[registrazione](#logging-settings)|Fornisce il supporto di registrazione ASP.NET Core per il contenitore. |
|No |[Mounts](#mount-settings)|Leggere e scrivere dati dal computer host al contenitore e dal contenitore al computer host.|
