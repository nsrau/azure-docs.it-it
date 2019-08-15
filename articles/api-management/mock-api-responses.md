---
title: Simulare le risposte dell'API con il portale di Azure | Microsoft Docs
description: Questa esercitazione illustra come usare Gestione API per impostare un criterio per un'API in modo che restituisca una risposta fittizia. Questo metodo consente agli sviluppatori di procedere con l'implementazione e il test dell'istanza di Gestione API qualora il back-end non sia disponibile per inviare risposte reali.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 8840c85e35a371b8c7a0a76dbee6353a20b212be
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881122"
---
# <a name="mock-api-responses"></a>Simulare le risposte di un'API

Le API back-end possono essere importate in un'API di Gestione API o create e gestite manualmente. I passaggi di questa esercitazione illustrano come usare Gestione API per creare un'API vuota e gestirla manualmente. L'esercitazione illustra come impostare un criterio per un'API in modo che restituisca una risposta fittizia. Questo metodo consente agli sviluppatori di procedere con l'implementazione e il test dell'istanza di Gestione API anche se il back-end non è disponibile per inviare risposte reali. La possibilità di simulare le risposte può essere utile in diversi scenari:

+ Quando la facciata dell'API viene progettata prima e l'implementazione del back-end viene eseguita in seguito o quando il back-end viene sviluppato in parallelo.
+ Quando il back-end è temporaneamente non operativo o non è possibile ridimensionarlo.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un'API di test 
> * Aggiungere un'operazione all'API di test
> * Abilitare la simulazione della risposta
> * Testare l'API fittizia

![Risposta di operazione fittizia](./media/mock-api-responses/mock-api-responses01.png)

## <a name="prerequisites"></a>Prerequisiti

+ Acquisire familiarità con la [terminologia di Gestione API di Azure](api-management-terminology.md).
+ Comprendere il [concetto di criteri in Gestione API di Azure](api-management-howto-policies.md).
+ Completare l'avvio rapido seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Creare un'API di test 

I passaggi descritti in questa sezione illustrano come creare un'API vuota senza back-end e come aggiungere un'operazione all'API. La chiamata all'operazione al termine dei passaggi descritti in questa sezione genera un errore. Non verranno visualizzati errori dopo avere completato i passaggi descritti nella sezione "Abilitare la simulazione della risposta".

![Creare un'API vuota](./media/mock-api-responses/03-MockAPIResponses-01-CreateTestAPI.png)

1. Nel servizio **Gestione API** selezionare **API**.
2. Fare clic su **+ Aggiungi API** nel menu a sinistra.
3. Selezionare **API vuota** dall'elenco.
4. Immettere "*Test API*" in **Nome visualizzato**.
5. Immettere "*Unlimited*" in **Prodotti**.
6. Selezionare **Create** (Crea).

## <a name="add-an-operation-to-the-test-api"></a>Aggiungere un'operazione all'API di test

![Aggiungere un'operazione all'API](./media/mock-api-responses/03-MockAPIResponses-02-AddOperation.png)

1. Selezionare l'API creata nel passaggio precedente.
2. Fare clic su **+ Aggiungi operazione**.

    | Impostazione             | Valore                             | DESCRIZIONE                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Nome visualizzato**    | *Test call*                       | Nome visualizzato nel **portale per sviluppatori**.                                                                                                                                       |
    | **URL** (verbo HTTP) | GET                               | È possibile scegliere tra uno dei verbi HTTP predefiniti.                                                                                                                                         |
    | **URL**             | */test*                           | Percorso URL per l'API.                                                                                                                                                                       |
    | **Descrizione**     |                                   | Immettere una descrizione dell'operazione usata per fornire la documentazione necessaria agli sviluppatori che usano questa API nel **portale per sviluppatori**.                                                    |
    | Scheda **Query**       |                                   | È possibile aggiungere parametri di query. Oltre al nome e alla descrizione, è possibile specificare i valori che possono essere assegnati a questo parametro. Uno dei valori può essere contrassegnato come predefinito (facoltativo). |
    | Scheda **Richiesta**     |                                   | È possibile definire schemi, esempi e tipi di contenuto della richiesta.                                                                                                                                  |
    | Scheda **Risposta**    | Vedere i passaggi che seguono questa tabella. | Consente di definire schemi, esempi, tipi di contenuto e codici di stato della risposta.                                                                                                                           |

3. Selezionare la scheda **Risposta**, sotto i campi URL, Nome visualizzato e Descrizione.
4. Fare clic su **+ Aggiungi risposta**.
5. Selezionare **200 OK** dall'elenco.
6. Sotto l'intestazione **Rappresentazioni** a destra selezionare **+ Aggiungi rappresentazione**.
7. Immettere "*application/json*" nella casella di ricerca e selezionare il tipo di contenuto **application/json**.
8. Nella casella di testo **Sample** (Esempio) immettere `{ "sampleField" : "test" }`.
9. Selezionare **Create** (Crea).

## <a name="enable-response-mocking"></a>Abilitare la simulazione della risposta

![Abilitare la simulazione della risposta](./media/mock-api-responses/03-MockAPIResponses-03-EnableMocking.png)

1. Selezionare l'API creata nel passaggio "Creare un'API di test".
2. Selezionare l'operazione di test aggiunta.
3. Nella finestra di destra fare clic sulla scheda **Progettazione**.
4. Nella finestra **Elaborazione in ingresso** fare clic su **+ Aggiungi criteri**.
5. Selezionare il riquadro **Mock responses** (Simula risposte) dalla raccolta.

    ![Riquadro del criterio Mock responses (Simula risposte)](./media/mock-api-responses/mock-responses-policy-tile.png)

6. Nella casella di testo **API Management response** (Risposta di Gestione API) digitare **200 OK, application/json**. Questa selezione indica che l'API deve restituire l'esempio di risposta definito nella sezione precedente.

    ![Abilitare la simulazione della risposta](./media/mock-api-responses/mock-api-responses-set-mocking.png)

7. Fare clic su **Save**.

## <a name="test-the-mocked-api"></a>Testare l'API fittizia

![Testare l'API fittizia](./media/mock-api-responses/03-MockAPIResponses-04-TestMocking.png)

1. Selezionare l'API creata nel passaggio "Creare un'API di test".
2. Aprire la scheda **Test**.
3. Verificare che l'API **Test call** sia selezionata.

    > [!TIP]
    > Una barra gialla con il testo **Mocking is enabled** (Il comportamento fittizio è abilitato) indica che le risposte sono state restituite da Gestione API e invia un criterio fittizio e non una vera risposta back-end.

4. Selezionare **Invia** per eseguire una chiamata di test.
5. La **risposta HTTP** visualizza il codice JSON fornito come esempio nella prima sezione dell'esercitazione.

    ![Abilitare la simulazione della risposta](./media/mock-api-responses/mock-api-responses-test-response.png)

## <a name="video"></a>Video

> [!VIDEO https://www.youtube.com/embed/i9PjUAvw7DQ]

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Creare un'API di test
> * Aggiungere un'operazione all'API di test
> * Abilitare la simulazione della risposta
> * Testare l'API fittizia

Passare all'esercitazione successiva:

> [!div class="nextstepaction"]
> [Trasformare e proteggere un'API pubblicata](transform-api.md)
