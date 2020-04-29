---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: 7ccbc6c06419d22add7c52829069bb858cb35cf7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73484102"
---
Le impostazioni di configurazione del contenitore sono le seguenti:

|Obbligatoria|Impostazione|Scopo|
|--|--|--|
|Sì|[ApiKey](#apikey-configuration-setting)|Tiene traccia delle informazioni di fatturazione.|
|No|[ApplicationInsights](#applicationinsights-setting)|Consente di aggiungere al contenitore il supporto per i dati di telemetria di [Azure Application Insights](https://docs.microsoft.com/azure/application-insights).|
|Sì|[Fatturazione](#billing-configuration-setting)|Specifica l'URI dell'endpoint della risorsa del servizio in Azure.|
|Sì|[Eula](#eula-setting)| Indica che è stata accettata la licenza per il contenitore.|
|No|[Fluentd](#fluentd-settings)|Scrive il log e, facoltativamente, i dati delle metriche in un server Fluentd.|
|No|Proxy HTTP|Configura un proxy HTTP per le richieste in uscita.|
|No|[Registrazione](#logging-settings)|Fornisce il supporto di registrazione ASP.NET Core per il contenitore. |
|No|[Mounts](#mount-settings)|Legge e scrive i dati dal computer host al contenitore e dal contenitore al computer host.|
