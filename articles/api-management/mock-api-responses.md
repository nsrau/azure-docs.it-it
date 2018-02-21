---
title: Simulare le risposte dell'API con il portale di Azure | Microsoft Docs
description: Questa esercitazione illustra come usare Gestione API per impostare un criterio per un'API in modo che restituisca una risposta fittizia. Questo metodo consente agli sviluppatori di procedere con l'implementazione e il test dell'istanza di Gestione API qualora il back-end non sia disponibile per inviare risposte reali.
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: e485071b026c52eb23532639546ad475fc92cde3
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2017
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

## <a name="prerequisites"></a>prerequisiti

Completare la guida introduttiva seguente: [Creare un'istanza di Gestione API di Azure](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-test-api"></a>Creare un'API di test 

I passaggi descritti in questa sezione illustrano come creare un'API vuota senza back-end e come aggiungere un'operazione all'API. La chiamata all'operazione al termine dei passaggi descritti in questa sezione genera un errore. Non verranno visualizzati errori dopo avere completato i passaggi descritti nella sezione "Abilitare la simulazione della risposta".

1. Selezionare **API** in **GESTIONE API**.
2. Fare clic su **+ Aggiungi API** nel menu a sinistra.
3. Selezionare **API vuota** dall'elenco.
4. Immettere "*Test API*" in **Nome visualizzato**.
5. Immettere "*Unlimited*" in **Prodotti**.
6. Selezionare **Create**.

## <a name="add-an-operation-to-the-test-api"></a>Aggiungere un'operazione all'API di test

1. Selezionare l'API creata nel passaggio precedente.
2. Fare clic su **+ Aggiungi operazione**.

    ![Risposta di operazione fittizia](./media/mock-api-responses/mock-api-responses02.png)

    |Impostazione|Valore|DESCRIZIONE|
    |---|---|---|
    |**URL** (verbo HTTP)|GET|È possibile scegliere tra uno dei verbi HTTP predefiniti.|
    |**URL** |*/test*|Percorso URL per l'API. |
    |**Nome visualizzato**|*Test call*|Nome visualizzato nel **portale per sviluppatori**.|
    |**Descrizione**||Immettere una descrizione dell'operazione usata per fornire la documentazione necessaria agli sviluppatori che usano questa API nel **portale per sviluppatori**.|
    |Scheda **Query**||È possibile aggiungere parametri di query. Oltre al nome e alla descrizione, è possibile specificare i valori che possono essere assegnati a questo parametro. Uno dei valori può essere contrassegnato come predefinito (facoltativo).|
    |Scheda **Richiesta**||È possibile definire schemi, esempi e tipi di contenuto della richiesta. |
    |Scheda **Risposta**|Vedere i passaggi che seguono questa tabella.|Consente di definire schemi, esempi, tipi di contenuto e codici di stato della risposta.|

3. Selezionare la scheda **Risposta**, sotto i campi URL, Nome visualizzato e Descrizione.
4. Fare clic su **+ Aggiungi risposta**.
5. Selezionare **200 OK** dall'elenco.
6. Sotto l'intestazione **Rappresentazioni** a destra selezionare **+ Aggiungi rappresentazione**.
7. Immettere "*application/json*" nella casella di ricerca e selezionare il tipo di contenuto **application/json**.
8. Nella casella di testo **Esempio** immettere "*{ 'sampleField' : 'test' }*".
9. Selezionare **Salva**.

## <a name="enable-response-mocking"></a>Abilitare la simulazione della risposta

1. Selezionare l'API creata nel passaggio "Creare un'API di test".
2. Selezionare l'operazione di test aggiunta.
2. Nella finestra di destra fare clic sulla scheda **Progettazione**.
3. Nella finestra **Elaborazione in ingresso** fare clic sull'icona a forma di matita.
4. Nella scheda **Comportamento fittizio** selezionare **Risposte statiche** per **Comportamento fittizio**.
5. Nella casella di testo **API Management returns the following response:** (Gestione API restituisce la risposta seguente) digitare **200 OK, application/json**. Questa selezione indica che l'API deve restituire l'esempio di risposta definito nella sezione precedente.
6. Selezionare **Salva**.

## <a name="test-the-mocked-api"></a>Testare l'API fittizia

1. Selezionare l'API creata nel passaggio "Creare un'API di test".
2. Aprire la scheda **Test**.
3. Verificare che l'API **Test call** sia selezionata.

    > [!TIP]
    > Una barra gialla con il testo **Mocking is enabled** (Il comportamento fittizio è abilitato) indica che le risposte sono state restituite da Gestione API e invia un criterio fittizio e non una vera risposta back-end.

3. Selezionare **Invia** per eseguire una chiamata di test.
4. La **risposta HTTP** visualizza il codice JSON fornito come esempio nella prima sezione dell'esercitazione.

## <a name="video"></a>Video

> [!VIDEO https://www.youtube.com/embed/i9PjUAvw7DQ]
> 
> 

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
