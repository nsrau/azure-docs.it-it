---
title: Knowledge base non in lingua inglese - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker supporta i contenuti della Knowledge Base in molte lingue. Ogni servizio QnA Maker deve essere tuttavia riservato a una sola lingua. La prima Knowledge Base creata per un particolare servizio QnA Maker imposta la lingua di quel servizio.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 63eb13dd131fcc1c424c02fdac10f531cc9f0282
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876632"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Supporto per le lingue dei contenuti della Knowledge Base per QnA Maker

QnA Maker supporta i contenuti della Knowledge Base in molte lingue. Ogni servizio QnA Maker deve essere tuttavia riservato a una sola lingua. La prima Knowledge Base creata per un particolare servizio QnA Maker imposta la lingua di quel servizio. Vedere [qui](../Overview/languages-supported.md) per l'elenco di lingue supportate.

La lingua viene automaticamente riconosciuta dal contenuto delle origini dati estratto. Dopo aver creato un nuovo servizio QnA Maker e una nuova Knowledge Base in tale servizio, è possibile verificare che la lingua sia stata impostata correttamente.

1. Passare al [portale di Azure](https://portal.azure.com/).

1. Selezionare **Gruppi risorse**, passare al gruppo di risorse in cui è distribuito il servizio QnA Maker e selezionare la risorsa **Ricerca di Azure**.

    ![Selezionare la risorsa Ricerca di Azure](../media/qnamaker-how-to-language-kb/select-azsearch.png)

1. Selezionare l'indice **testkb**. Questo indice di Ricerca di Azure è sempre il primo creato e contiene il contenuto salvato di tutte le Knowledge Base di quel servizio. 

    ![Selezionare la Knowledge Base di test](../media/qnamaker-how-to-language-kb/select-testkb.png)

1. Selezionare la sezione dei **campi** che mostra i dettagli _Testkb_ .

    ![Selezionare i campi](../media/qnamaker-how-to-language-kb/selectfields.png)

1. Selezionare la casella **Analizzatore** per visualizzare i dettagli della lingua.

    ![Selezionare Analizzatore](../media/qnamaker-how-to-language-kb/select-analyzer.png)

1. Si noterà che l' _analizzatore_ è impostato su una lingua specifica. Questa lingua è stata rilevata automaticamente durante il passaggio di creazione della Knowledge base dagli URL e dai file importati. La lingua non può essere cambiata dopo aver creato la risorsa.

    ![Analizzatore selezionato](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un bot QnA con il servizio Azure Bot](../Tutorials/create-qna-bot.md)
