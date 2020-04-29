---
title: Piano di continuità aziendale - QnA Maker
description: L'obiettivo primario del piano di continuità aziendale è creare un endpoint di Knowledge Base resiliente che garantisca l'assenza di tempi di inattività per il bot o l'applicazione che ne fa uso.
ms.topic: conceptual
ms.date: 04/07/2020
ms.openlocfilehash: 5d1501ecc42fe948959075cec7d728f6c9df908a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80887070"
---
# <a name="create-a-business-continuity-plan-for-your-qna-maker-service"></a>Creare un piano di continuità aziendale per il servizio QnA Maker

L'obiettivo primario del piano di continuità aziendale è creare un endpoint di Knowledge Base resiliente che garantisca l'assenza di tempi di inattività per il bot o l'applicazione che ne fa uso.

## <a name="business-continuity-with-traffic-manager"></a>Continuità aziendale con gestione traffico

> [!div class="mx-imgBorder"]
> ![Piano di continuità aziendale QnA Maker](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

L'idea generale è la seguente:

1. Impostare due [servizi QnA Maker](set-up-qnamaker-service-azure.md) paralleli in [aree associate di Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

2. [Eseguire il backup](../../../app-service/manage-backup.md) del servizio app QnA Maker primario e [ripristinarlo](../../../app-service/web-sites-restore.md) nell'installazione secondaria. In questo modo, entrambe le configurazioni funzioneranno con lo stesso nome host e le stesse chiavi.

3. Mantieni sincronizzati gli indici di ricerca di Azure primaria e secondaria. Usare l'esempio GitHub [qui](https://github.com/pchoudhari/QnAMakerBackupRestore) per informazioni su come eseguire il backup e ripristinare gli indici di Azure.

4. Eseguire il backup di Application Insights usando l'[esportazione continua](../../../application-insights/app-insights-export-telemetry.md).

5. Una volta impostati gli stack primario e secondario, usare [Gestione traffico](../../../traffic-manager/traffic-manager-overview.md) per configurare i due endpoint e impostare un metodo di routing.

6. È necessario creare un Transport Layer Security (TLS), noto in precedenza come Secure Sockets Layer (SSL), certificato per l'endpoint di gestione traffico. [Associare il certificato TLS/SSL](../../../app-service/configure-ssl-bindings.md) nei servizi app.

7. Usare infine l'endpoint di Gestione traffico nel bot o nell'app.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scegliere la capacità](./improve-knowledge-base.md)