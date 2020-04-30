---
title: Ottenere la risposta predefinita QnA Maker
description: Quando non esiste alcuna corrispondenza con la domanda, viene restituita la risposta predefinita. Potrebbe essere necessario modificare la risposta predefinita dalla risposta predefinita standard.
ms.topic: how-to
ms.date: 04/22/2020
ms.openlocfilehash: db5a79ec612a73066ac37365a1815841fafb3862
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82097099"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Modificare la risposta predefinita per una risorsa QnA Maker

Quando non esiste alcuna corrispondenza con la domanda, viene restituita la risposta predefinita. Potrebbe essere necessario modificare la risposta predefinita dalla risposta predefinita standard.

## <a name="change-default-answer-in-the-azure-portal"></a>Modificare la risposta predefinita nel portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com) e passare al gruppo di risorse che rappresenta il servizio QnA Maker creato.

2. Fare clic per aprire il **servizio app**.

    ![Nel portale di Azure, accedere al servizio app per QnA Maker](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Fare clic su **Impostazioni applicazione** e modificare il campo **DefaultAnswer** con la risposta predefinita desiderata. Fare clic su **Save**.

    ![Selezionare Impostazioni applicazione e quindi modificare il valore di DefaultAnswer per QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Riavviare il servizio app

    ![Dopo aver modificato il valore di DefaultAnswer, riavviare il servizio app di QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Passaggi successivi

* [Creare una knowledge base](../How-to/manage-knowledge-bases.md)