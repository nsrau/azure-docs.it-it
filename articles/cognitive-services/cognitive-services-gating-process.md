---
title: Processo di controllo dei servizi cognitivi
titleSuffix: Azure Cognitive Services
description: Informazioni su come richiedere l'accesso anticipato ai nuovi contenitori e API di servizi cognitivi.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: aahi
ms.openlocfilehash: a161cc0675eedee50608b8d6c288c57147fd6d23
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599513"
---
# <a name="gating-process-for-azure-cognitive-services"></a>Processo di controllo per servizi cognitivi di Azure

> [!NOTE]
> Dopo che un'offerta di servizio ha completato un'anteprima gestita, viene inserita in un'anteprima pubblica "non gestita" che non richiede l'accesso a un'applicazione. Al termine del processo di anteprima, viene rilasciato come disponibile a livello generale (GA).

Man mano che vengono introdotte nuove offerte di servizi cognitivi di Azure, i clienti passano attraverso un'anteprima gestita, in cui i clienti devono richiedere l'accesso tramite un'applicazione. Questo processo di controllo consente di identificare le opportunità di miglioramento delle offerte di servizio prima che siano ampiamente disponibili. 

Questo articolo illustra il processo dell'applicazione per le offerte di servizi cognitivi gestita.

## <a name="eligibility-and-approval-process"></a>Idoneità e processo di approvazione

Il processo di controllo è in atto per aiutare a valutare l'interesse e comprendere meglio le esigenze dei clienti. Il team Microsoft accetta [le applicazioni](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u) dei clienti commerciali Microsoft con una sottoscrizione di Azure valida e uno scenario aziendale valido. I clienti potranno potenzialmente negare le proprie applicazioni nei casi seguenti:

 - Non sono associati ad alcuna organizzazione
 - Non hanno una sottoscrizione di Azure valida
 - L'applicazione è stata inviata tramite posta elettronica personale ( @hotmail.com , @gmail.com , @yahoo.com )
 - Non sono state fornite giustificazioni o scenari aziendali appropriati

Data la richiesta da segmenti di clienti diversi, stiamo tentando di accelerare il processo di approvazione. Tuttavia, non è possibile eseguire il commit in una sequenza temporale. Una volta effettuata la decisione, il team Microsoft contatterà l'utente e il team di gestione degli account per i passaggi successivi. La comprensione e la pazienza sono apprezzate.

Se l'applicazione viene approvata, il team Microsoft invierà un messaggio di posta elettronica con i dettagli, la documentazione e le linee guida. Dettagli prezzi di servizi cognitivi disponibili [qui](https://azure.microsoft.com/pricing/details/cognitive-services/).


Attualmente, i servizi riportati di seguito vengono forniti tramite il processo di controllo:

## <a name="gated-web-apis"></a>API Web gestite

|Service  |
|---------|
|Rilevamento anomalie V2     | 

## <a name="gated-online-containers"></a>Contenitori online controllati

| Service                             | Contenitore/i                                                                  |
|-------------------------------------|-------------------------------------------------------------------------------|
| [Visione artificiale][cv-containers]    | Lettura                                                                          |
| [Viso][fa-containers]               | Viso                                                                          |
| [Riconoscimento modulo][fr-containers]    | Riconoscimento modulo                                                               |
| [API servizio Voce][sp-containers] | Sintesi vocale (personalizzata e standard) e sintesi vocale (personalizzata e standard) |
| [Traduzione testuale][tt-containers]    | Traduzione testuale                                                               |

> [!IMPORTANT]
> Se un servizio o un'offerta in contenitori non è presente nell'elenco, non è gestita o non è disponibile.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Iscriversi ai servizi controllati](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyQZ7B8Cg2FEjpibPziwPcZUNlQ4SEVORFVLTjlBSzNLRlo0UzRRVVNPVy4u)

[ad-containers]: ./anomaly-detector/anomaly-detector-container-howto.md
[cv-containers]: ./computer-vision/computer-vision-how-to-install-containers.md
[fa-containers]: ./face/face-how-to-install-containers.md
[fr-containers]: ./form-recognizer/form-recognizer-container-howto.md
[lu-containers]: ./luis/luis-container-howto.md
[sp-containers]: ./speech-service/speech-container-howto.md
[ta-containers]: ./text-analytics/how-tos/text-analytics-how-to-install-containers.md
[tt-containers]: ./translator/how-to-install-containers.md
