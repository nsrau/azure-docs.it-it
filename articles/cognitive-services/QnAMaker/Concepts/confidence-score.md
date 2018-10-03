---
title: Punteggio di attendibilità - QnA Maker
titleSuffix: Azure Cognitive Services
description: Un punteggio di attendibilità indica il grado di corrispondenza tra la domanda dell'utente e la risposta restituita.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 33da5cf5724b8314ce813f12eb077d9a15ec1b2a
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47041524"
---
# <a name="confidence-score"></a>Punteggio di attendibilità

Un punteggio di attendibilità indica il grado di corrispondenza tra la domanda dell'utente e la risposta restituita.

Quando una query dell'utente viene confrontata con il contenuto della Knowledge Base, può essere restituita più di una risposta. Le risposte vengono restituite in ordine decrescente di punteggio di attendibilità.

Il punteggio di attendibilità è compreso tra 0 e 100.

|Valore del punteggio|Attendibilità|
|--|--|
|100|Corrispondenza esatta tra query dell'utente e domanda della Knowledge Base|
|90|Attendibilità elevata, la maggior parte delle parole corrispondono|
|40-60|Buona attendibilità, le parole importanti corrispondono|
|10|Bassa attendibilità, le parole importanti non corrispondono|
|0|Nessuna parola corrisponde|


## <a name="similar-confidence-scores"></a>Punteggi di attendibilità simili
Quando più risposte hanno un punteggio di attendibilità simile, è probabile che la query fosse troppo generica e quindi associata con pari probabilità a più risposte. Provare a strutturare meglio le domande e le risposte in modo che ogni entità di domanda/risposta abbia una finalità distinta.


## <a name="improving-confidence-scores"></a>Ottimizzazione dei punteggi di attendibilità
Per ottimizzare il punteggio di attendibilità di una particolare risposta a una query dell'utente, è possibile aggiungere la query alla Knowledge Base come domanda alternativa per tale risposta.
   
## <a name="confidence-score-differences"></a>Differenze tra punteggi di attendibilità
Il punteggio di attendibilità di una risposta può variare in modo trascurabile tra la versione di test e la versione pubblicata della Knowledge Base, anche se il contenuto è lo stesso. Questo accade perché il contenuto della versione di test e della versione pubblicata della Knowledge Base si trova in indici diversi di Ricerca di Azure.

Vedere qui come funziona l'operazione di [pubblicazione](../How-To/publish-knowledge-base.md).


## <a name="no-match-found"></a>Nessuna corrispondenza trovata
Se lo strumento di classificazione non trova corrispondenze soddisfacenti, viene restituito il punteggio di attendibilità di 0.0 oppure "None" (Nessuno) e la risposta predefinita è "No good match found in the KB" (Nessuna buona corrispondenza trovata nella Knowledge Base). È possibile sovrascrivere questa risposta predefinita nel codice del bot o dell'applicazione che chiama l'endpoint. In alternativa è anche possibile impostare la risposta sostitutiva in Azure, modificando così la risposta predefinita per tutte le Knowledge Base distribuite in un particolare servizio QnA Maker.

1. Accedere al [portale di Azure](http://portal.azure.com) e passare al gruppo di risorse che rappresenta il servizio QnA Maker creato.

2. Fare clic per aprire il **servizio app**.

    ![Accedere al servizio app](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Fare clic su **Impostazioni applicazione** e modificare il campo **DefaultAnswer** con la risposta predefinita desiderata. Fare clic su **Save**.

    ![Modificare la risposta predefinita](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Riavviare il servizio app

    ![Riavvio del servizio app QnA Maker](../media/qnamaker-faq/qnamaker-appservice-restart.png)


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Origini dati supportate](./data-sources-supported.md)

## <a name="see-also"></a>Vedere anche  

[Panoramica di QnA Maker](../Overview/overview.md)
