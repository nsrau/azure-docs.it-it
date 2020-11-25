---
author: IEvangelist
ms.author: dapine
ms.date: 10/02/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a6f399e19cadf3d6ce9edfaecb3d904e62c498aa
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006875"
---
Le impostazioni di configurazione del contenitore sono le seguenti:

|Necessario|Impostazione|Scopo|
|--|--|--|
|Sì|[ApiKey](#apikey-configuration-setting)|Tiene traccia delle informazioni di fatturazione.|
|No|[ApplicationInsights](#applicationinsights-setting)|Consente di aggiungere al contenitore il supporto per i dati di telemetria di [Azure Application Insights](/azure/application-insights).|
|Sì|[Fatturazione](#billing-configuration-setting)|Specifica l'URI dell'endpoint della risorsa del servizio in Azure.|
|Sì|[Eula](#eula-setting)| Indica che è stata accettata la licenza per il contenitore.|
|No|[Fluentd](#fluentd-settings)|Scrive il log e, facoltativamente, i dati delle metriche in un server Fluentd.|
|No|Proxy HTTP|Configura un proxy HTTP per le richieste in uscita.|
|No|[Logging](#logging-settings)|Fornisce il supporto di registrazione ASP.NET Core per il contenitore. |
|No|[Mounts](#mount-settings)|Legge e scrive i dati dal computer host al contenitore e dal contenitore al computer host.|