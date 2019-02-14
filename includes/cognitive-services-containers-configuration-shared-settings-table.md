---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2019
ms.openlocfilehash: 5228e4986993f792dceb7324bb6ba9f836f29aea
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56213081"
---
Questo contenitore ha le impostazioni di configurazione seguenti:

|Obbligatoria|Impostazione|Scopo|
|--|--|--|
|Sì|[ApiKey](#apikey-configuration-setting)|Si usa per rilevare le informazioni di fatturazione.|
|No |[ApplicationInsights](#applicationinsights-setting)|Consente di aggiungere al contenitore il supporto per i dati di telemetria di [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Sì|[Fatturazione](#billing-configuration-setting)|Specifica l'URI dell'endpoint della risorsa del servizio in Azure.|
|Sì|[Eula](#eula-setting)| Indica che è stata accettata la licenza per il contenitore.|
|No |[Fluentd](#fluentd-settings)|Scrivere il log e, facoltativamente, i dati delle metriche in un server Fluentd.|
|No |[Proxy HTTP](#http-proxy-credentials-settings)|Configurare un proxy HTTP per le richieste in uscita.|
|No |[registrazione](#logging-settings)|Fornisce il supporto di registrazione ASP.NET Core per il contenitore. |
|No |[Mounts](#mount-settings)|Leggere e scrivere dati dal computer host al contenitore e dal contenitore al computer host.|
