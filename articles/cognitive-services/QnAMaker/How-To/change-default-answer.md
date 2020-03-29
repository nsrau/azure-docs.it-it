---
title: Ottenere la risposta predefinita - QnA MakerGet default answer - QnA Maker
description: La risposta predefinita viene restituita quando non vi è alcuna corrispondenza alla domanda. È possibile modificare la risposta predefinita rispetto alla risposta predefinita standard.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843277"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>Impostare la risposta predefinita per una Knowledge Base

La risposta predefinita viene restituita quando non vi è alcuna corrispondenza alla domanda. È possibile modificare la risposta predefinita rispetto alla risposta predefinita standard.

## <a name="change-default-answer"></a>Modificare la risposta predefinita

1. Accedere al [portale di Azure](https://portal.azure.com) e passare al gruppo di risorse che rappresenta il servizio QnA Maker creato.

2. Fare clic per aprire il **servizio app**.

    ![Nel portale di Azure, accedere al servizio app per QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Fare clic su **Impostazioni applicazione** e modificare il campo **DefaultAnswer** con la risposta predefinita desiderata. Fare clic su **Salva**.

    ![Selezionare Impostazioni applicazione e quindi modificare il valore di DefaultAnswer per QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Riavviare il servizio app

    ![Dopo aver modificato il valore di DefaultAnswer, riavviare il servizio app di QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Passaggi successivi

* [Creare un bot con QnA Maker e LUISCreate a bot with QnA Maker and LUIS](../tutorials/integrate-qnamaker-luis.md)