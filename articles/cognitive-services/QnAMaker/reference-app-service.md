---
title: Configurazione del servizio-QnA Maker
description: Informazioni su come e dove configurare le risorse.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: eac930971cab041fbf398da1ac5f8a055412832d
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376860"
---
# <a name="service-configuration"></a>Configurazione del servizio

Ogni versione di QnA Maker usa un insieme diverso di risorse di Azure (servizi). Questo articolo descrive le personalizzazioni supportate per questi servizi. 

## <a name="app-service"></a>Servizio app

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versione stabile)](#tab/v1)

QnA Maker usa il servizio app per fornire il runtime di query usato dall' [API generateAnswer](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer).

Queste impostazioni sono disponibili nella portale di Azure per il servizio app. Le impostazioni sono disponibili selezionando **Impostazioni** , quindi **configurazione**.

È possibile impostare una singola impostazione tramite l'elenco delle impostazioni dell'applicazione o modificare diverse impostazioni selezionando **modifica avanzata**.

|Risorsa|Impostazione|
|--|--|
|AzureSearchAdminKey|Ricerca cognitiva: usato per l'archiviazione e la classificazione delle coppie QnA #1|
|AzureSearchName|Ricerca cognitiva: usato per l'archiviazione e la classificazione delle coppie QnA #1|
|DefaultAnswer|Testo di risposta quando non viene trovata alcuna corrispondenza|
|UserAppInsightsAppId|Log di chat e telemetria|
|UserAppInsightsKey|Log di chat e telemetria|
|UserAppInsightsName|Log di chat e telemetria|

Dopo aver apportato le modifiche, è necessario **riavviare** il servizio dalla pagina **Panoramica** del portale di Azure.

# <a name="qna-maker-managed-preview-release"></a>[Gestione QnA Maker (versione di anteprima)](#tab/v2)

Le personalizzazioni del servizio app non si applicano ai QnA Maker gestiti (anteprima).

---

## <a name="qna-maker-service"></a>Servizio QnA Maker

Il servizio QnA Maker fornisce la configurazione per consentire agli utenti seguenti di collaborare in un singolo servizio QnA Maker e in tutte le Knowledge base.

Informazioni [su come aggiungere collaboratori](./how-to/collaborate-knowledge-base.md) al servizio.

## <a name="change-azure-cognitive-search"></a>Modificare ricerca cognitiva di Azure

Informazioni [su come modificare il servizio di ricerca cognitiva](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) collegato al servizio QnA Maker.

## <a name="change-default-answer"></a>Cambiare la risposta predefinita

Informazioni [su come modificare il testo delle risposte predefinite](How-To/change-default-answer.md). 

## <a name="telemetry"></a>Telemetria

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (versione stabile)](#tab/v1)

Application Insights viene usato per il monitoraggio dei dati di telemetria con QnA Maker GA. Non sono disponibili impostazioni di configurazione specifiche per QnA Maker.

# <a name="qna-maker-managed-preview-release"></a>[Gestione QnA Maker (versione di anteprima)](#tab/v2)

Informazioni [su come aggiungere dati di telemetria al servizio QnA Maker gestito (anteprima)](How-To/get-analytics-knowledge-base.md). 

---

## <a name="app-service-plan"></a>Piano di servizio app

# <a name="qnamaker-ga-stable-release"></a>[QnAMaker GA (versione stabile)](#tab/v1)

Il piano di servizio app non contiene impostazioni di configurazione specifiche per QnA Maker.

# <a name="qnamaker-managed-preview-release"></a>[Gestito da QnAMaker (versione di anteprima)](#tab/v2)

Il piano di servizio app non viene usato con QnA Maker gestito (anteprima).

---

## <a name="next-steps"></a>Passaggi successivi

Ulteriori informazioni sui [formati](reference-document-format-guidelines.md) per i documenti e gli URL che si desidera importare in una Knowledge base.