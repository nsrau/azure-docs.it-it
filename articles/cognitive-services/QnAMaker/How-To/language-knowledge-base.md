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
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 5e50c814fef24aa799549d055ad6496f5bdf05e0
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961492"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Supporto per le lingue dei contenuti della Knowledge Base per QnA Maker

QnA Maker supporta i contenuti della Knowledge Base in molte lingue. Ogni servizio QnA Maker deve essere tuttavia riservato a una sola lingua. La prima Knowledge Base creata per un particolare servizio QnA Maker imposta la lingua di quel servizio. Vedere [qui](../Overview/languages-supported.md) per l'elenco di lingue supportate.

La lingua viene automaticamente riconosciuta dal contenuto delle origini dati estratto. Dopo aver creato un nuovo servizio QnA Maker e una nuova Knowledge Base in tale servizio, è possibile verificare che la lingua sia stata impostata correttamente.

1. Passare al [portale di Azure](https://portal.azure.com/).

1. Selezionare **Gruppi risorse**, passare al gruppo di risorse in cui è distribuito il servizio QnA Maker e selezionare la risorsa **Ricerca di Azure**.

    ![Selezionare la risorsa Ricerca di Azure](../media/qnamaker-how-to-language-kb/select-azsearch.png)

1. Selezionare **indici**, quindi selezionare l'indice **Testkb** . Questo è il primo indice di ricerca di Azure creato e contiene il contenuto salvato di tutte le Knowledge base in tale servizio. 

1. Selezionare i **campi** per visualizzare i campi dell'indice.

1. La colonna _analizzatore_ dei `questions` campi `answer` e è impostata su una lingua specifica. Questa lingua è stata rilevata automaticamente durante il passaggio di creazione della Knowledge base dagli URL e dai file importati. La lingua non può essere cambiata dopo aver creato la risorsa.

    ![Analizzatore selezionato](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare un bot QnA con il servizio Azure Bot](../Tutorials/create-qna-bot.md)
