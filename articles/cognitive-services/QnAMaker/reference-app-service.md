---
title: Configurazione del servizio - QnA MakerService configuration - QnA Maker
description: Comprendere come e dove configurare le risorse.
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 8ef6fecbfeb119d0c68ec5bc3bbc90ec449dbb7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651861"
---
# <a name="service-configuration"></a>Configurazione del servizio

QnA Maker uses several Azure resources (services) including Cognitive Search, App Service, App Service Plan, and Application Insights.

Tutte le personalizzazioni di queste impostazioni supportate da QnA Maker sono elencate di seguito.

## <a name="app-service"></a>Servizio app

QnA Maker utilizza il servizio app per fornire il runtime di query utilizzato [dall'API generateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer).


Queste impostazioni sono disponibili nel portale di Azure per il servizio app. Le impostazioni sono disponibili selezionando **Impostazioni**, quindi **Configurazione**.

È possibile impostare una singola impostazione tramite l'elenco Impostazioni applicazione o modificare diverse impostazioni selezionando **Modifica avanzata**.

|Risorsa|Impostazione|
|--|--|
|Chiave AzureSearchAdmin|Ricerca cognitiva - utilizzata per l'archiviazione dei set QnA e il #1 Ranker|
|NomeAzureSearchName|Ricerca cognitiva - utilizzata per l'archiviazione dei set QnA e il #1 Ranker|
|Risposta predefinita|Testo della risposta quando non viene trovata alcuna corrispondenza|
|UserAppInsightsAppId|Log di chat e telemetria|
|UserAppInsightsKey (Chiave UserAppInsightsKey)|Log di chat e telemetria|
|UserAppInsightsName|Log di chat e telemetria|

Informazioni su come aggiungere la modifica del [servizio di Ricerca cognitiva](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) al servizio.

Una volta che si sono apportate modifiche, è necessario **riavviare** il servizio dalla pagina **Panoramica** del portale di Azure.

## <a name="qna-maker-service"></a>Servizio QnA Maker

Il servizio QnA Maker fornisce la configurazione per gli utenti seguenti per collaborare su un singolo servizio QnA Maker e su tutte le relative knowledge base.

[Scopri come aggiungere collaboratori](./how-to/collaborate-knowledge-base.md) al tuo servizio.

## <a name="application-insights"></a>Application Insights

Application Insights has no configuration settings specific to QnA Maker.

## <a name="app-service-plan"></a>Piano di servizio app

App Service Plan has no configuration settings specific to QnA Maker.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sui [formati](reference-document-format-guidelines.md) per i documenti e gli URL che si desidera importare in una Knowledge Base.