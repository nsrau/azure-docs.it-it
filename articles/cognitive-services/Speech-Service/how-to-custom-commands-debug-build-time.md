---
title: Errori di debug durante la creazione di un'applicazione di comandi personalizzati (anteprima)
titleSuffix: Azure Cognitive Services
description: In questo articolo si apprenderà come eseguire il debug degli errori durante la creazione dell'applicazione di comandi personalizzati.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 9c84b35318637f5b89e6c88c0ebb3fd6616533fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86023126"
---
# <a name="debug-errors-when-authoring-a-custom-commands-application"></a>Errori di debug durante la creazione di un'applicazione di comandi personalizzati

Questo articolo descrive come eseguire il debug quando si verificano errori durante la compilazione di un'applicazione di comandi personalizzata. 

## <a name="errors-when-creating-an-application"></a>Errori durante la creazione di un'applicazione
I comandi personalizzati consentono inoltre di creare un'applicazione in [Luis](https://www.luis.ai/) quando si crea un'applicazione di comandi personalizzata. 

[Luis limita le applicazioni 500 per ogni risorsa di creazione](https://docs.microsoft.com/azure/cognitive-services/luis/luis-limits). La creazione di un'applicazione LUIS potrebbe non riuscire se si usa una risorsa di creazione che ha già 500 applicazioni. 

Verificare che la risorsa di authoring LUIS selezionata disponga di meno di 500 di applicazioni. In caso contrario, è possibile creare una nuova risorsa LUIS authoring, passare a un'altra oppure provare a pulire le applicazioni LUIS.  

## <a name="errors-when-deleting-an-application"></a>Errori durante l'eliminazione di un'applicazione
### <a name="cant-delete-luis-application"></a>Non è possibile eliminare l'applicazione LUIS
Quando si elimina un'applicazione comandi personalizzata, i comandi personalizzati possono anche provare a eliminare l'applicazione LUIS associata all'applicazione comandi personalizzati.

Se l'eliminazione dell'applicazione LUIS non è riuscita, passare all'account [Luis](https://www.luis.ai/) per eliminarli manualmente.

### <a name="toomanyrequests"></a>TooManyRequests
Quando si tenta di eliminare un numero elevato di applicazioni contemporaneamente, è probabile che vengano visualizzati errori di "TooManyRequests". Questi errori indicano che le richieste di eliminazione vengono limitate da Azure. 

Aggiornare la pagina e provare a eliminare un minor numero di applicazioni.

## <a name="errors-when-modifying-an-application"></a>Errori durante la modifica di un'applicazione

### <a name="cant-delete-a-parameter-or-a-web-endpoint"></a>Non è possibile eliminare un parametro o un endpoint Web
Non è possibile eliminare un parametro quando viene usato. Rimuovere qualsiasi riferimento del parametro in qualsiasi risposta vocale, frasi di esempio, condizioni, azioni e riprovare.

### <a name="cant-delete-a-web-endpoint"></a>Non è possibile eliminare un endpoint Web
Non è possibile eliminare un endpoint Web quando viene usato. Rimuovere qualsiasi azione dell' **endpoint Web di chiamata** che usa questo endpoint Web prima di rimuovere un endpoint Web.

## <a name="errors-when-training-an-application"></a>Errori durante il training di un'applicazione
### <a name="built-in-intents"></a>Intent Built-In
LUIS ha incorporato Sì/No Intent. La presenza di frasi di esempio con solo "Yes", "No" potrebbe non riuscire a eseguire il training. 

| Parola chiave | Variazioni | 
| ------- | --------- | 
| Sì | Certo, OK |
| No | No, no | 

### <a name="common-sample-sentences"></a>Frasi di esempio comuni
I comandi personalizzati non consentono frasi di esempio comuni condivise tra comandi diversi. Il training di un'applicazione potrebbe non riuscire se alcune frasi di esempio in un comando sono già definite in un altro comando. 

Assicurarsi che non siano presenti frasi di esempio comuni condivise tra comandi diversi. 

Per la procedura consigliata per il bilanciamento delle frasi di esempio in diversi comandi, vedere la [procedura consigliata Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices).

### <a name="empty-sample-sentences"></a>Frasi di esempio vuote
È necessario avere almeno una frase di esempio per ogni comando.

### <a name="undefined-parameter-in-sample-sentences"></a>Parametro non definito nelle frasi di esempio
Uno o più parametri vengono utilizzati nelle frasi di esempio ma non definiti.

### <a name="training-takes-too-long"></a>Il training richiede troppo tempo
LUIS Training è concepito per imparare rapidamente con meno esempi. Non aggiungere troppe frasi di esempio. 

Se sono presenti molte frasi di esempio simili, definire un parametro, estrarle in un modello e aggiungerle alle frasi di esempio.

Ad esempio, è possibile definire un parametro {Vehicle} per le frasi di esempio seguenti e aggiungere "Book a {Vehicle}" alle frasi di esempio.

| Frasi di esempio | Modello | 
| ------- | ------- | 
| Prenotare un'auto | Prenota un {Vehicle} | 
| Prenota un volo | Prenota un {Vehicle} |
| Prenota un taxi | Prenota un {Vehicle} |

Per la procedura consigliata per la formazione di LUIS, vedere la [procedura consigliata Luis](https://docs.microsoft.com/azure/cognitive-services/luis/luis-concept-best-practices).

## <a name="cant-update-luis-key"></a>Non è possibile aggiornare la chiave LUIS
### <a name="reassign-to-e0-authoring-resource"></a>Riassegna alla risorsa di creazione di E0
LUIS non supporta la riassegnazione dell'applicazione LUIS alla risorsa di creazione di E0.

Se è necessario modificare la risorsa di creazione da F0 a E0 oppure passare a una risorsa E0 diversa, ricreare l'applicazione. 

Per esportare rapidamente un'applicazione esistente e importarla in una nuova applicazione, vedere [distribuzione continua con Azure DevOps](./how-to-custom-commands-deploy-cicd.md).

### <a name="save-button-is-disabled"></a>Il pulsante Salva è disabilitato
Se non si assegna mai una risorsa di stima LUIS all'applicazione, il pulsante Salva viene disabilitato quando si tenta di modificare la risorsa di creazione senza aggiungere una risorsa di stima.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Vedere gli esempi in GitHub](https://aka.ms/speech/cc-samples)
