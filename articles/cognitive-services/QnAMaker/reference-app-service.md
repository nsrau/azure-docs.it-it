---
title: Configurazione del servizio-QnA Maker
description: Informazioni su come e dove configurare le risorse.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 1d54b912d2177a3ccd0cf34d57fc0358af653199
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91776715"
---
# <a name="service-configuration"></a>Configurazione del servizio

QnA Maker usa diverse risorse (servizi) di Azure, tra cui ricerca cognitiva, servizio app, piano di servizio app e Application Insights.

Di seguito sono elencate tutte le personalizzazioni apportate a queste impostazioni supportate da QnA Maker.

## <a name="app-service"></a>Servizio app

QnA Maker usa il servizio app per fornire il runtime di query usato dall' [API generateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer).


Queste impostazioni sono disponibili nella portale di Azure per il servizio app. Le impostazioni sono disponibili selezionando **Impostazioni**, quindi **configurazione**.

È possibile impostare una singola impostazione tramite l'elenco delle impostazioni dell'applicazione o modificare diverse impostazioni selezionando **modifica avanzata**.

|Risorsa|Impostazione|
|--|--|
|AzureSearchAdminKey|Ricerca cognitiva: usato per l'archiviazione e la classificazione delle coppie QnA #1|
|AzureSearchName|Ricerca cognitiva: usato per l'archiviazione e la classificazione delle coppie QnA #1|
|DefaultAnswer|Testo di risposta quando non viene trovata alcuna corrispondenza|
|UserAppInsightsAppId|Log di chat e telemetria|
|UserAppInsightsKey|Log di chat e telemetria|
|UserAppInsightsName|Log di chat e telemetria|

Informazioni [su come aggiungere la modifica del servizio ricerca cognitiva](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) al servizio.

Dopo aver apportato le modifiche, è necessario **riavviare** il servizio dalla pagina **Panoramica** del portale di Azure.

## <a name="qna-maker-service"></a>Servizio QnA Maker

Il servizio QnA Maker fornisce la configurazione per consentire agli utenti seguenti di collaborare in un singolo servizio QnA Maker e in tutte le Knowledge base.

Informazioni [su come aggiungere collaboratori](./how-to/collaborate-knowledge-base.md) al servizio.

## <a name="application-insights"></a>Application Insights

Application Insights non dispone di impostazioni di configurazione specifiche per l'QnA Maker.

## <a name="app-service-plan"></a>Piano di servizio app

Il piano di servizio app non contiene impostazioni di configurazione specifiche per QnA Maker.

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sui [formati](reference-document-format-guidelines.md) per i documenti e gli URL che si desidera importare in una Knowledge base.