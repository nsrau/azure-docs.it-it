---
title: Migliorare una knowledge base - QnA Maker
description: Migliorare la qualità della Knowledge base con l'apprendimento attivo. Verifica, accetta o rifiuta, Aggiungi senza rimuovere o modificare le domande esistenti.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 33b3c547b0aea9a1e235bf8a05d01aa16b468a71
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "80071133"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Usare l'apprendimento attivo per migliorare la knowledge base

L' [apprendimento attivo](../Concepts/active-learning-suggestions.md) consente di migliorare la qualità della Knowledge base suggerendo domande alternative. Gli invii degli utenti vengono presi in considerazione e vengono visualizzati come suggerimenti nell'elenco domande alternative. È possibile aggiungere tali suggerimenti come domande alternative o rifiutarli.

La knowledge base non viene modificata automaticamente. Per rendere effettive le modifiche, è necessario accettare i suggerimenti. Questi suggerimenti aggiungono domande, ma non modificano o rimuovono le domande esistenti.


## <a name="upgrade-runtime-version-to-use-active-learning"></a>Aggiornare la versione di runtime per usare l'apprendimento attivo

L'apprendimento attivo è supportato nella versione del runtime 4.4.0 e versioni successive. Se la knowledge base è stata creata in una versione precedente, [aggiornare il runtime](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) per usare questa funzionalità.

## <a name="turn-on-active-learning-for-alternate-questions"></a>Attivare l'apprendimento attivo per domande alternative

L'apprendimento attivo è disattivato per impostazione predefinita. Attivarlo per visualizzare le domande suggerite. Dopo aver attivato l'apprendimento attivo, è necessario inviare le informazioni dall'app client a QnA Maker. Per altre informazioni, vedere [flusso di architettura per l'uso di GenerateAnswer ed eseguire il training delle API da un bot](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Selezionare **pubblica** per pubblicare la Knowledge base. Le query di apprendimento attivo vengono raccolte solo dall'endpoint di stima dell'API GenerateAnswer. Le query nel riquadro di test nel portale di QnA Maker non influiscano sull'apprendimento attivo.

1. Per attivare l'apprendimento attivo nel portale di QnA Maker, passare all'angolo superiore destro, selezionare il **nome**, passare a [**Impostazioni servizio**](https://www.qnamaker.ai/UserSettings).

    ![Attivare le alternative di domande suggerite per l'apprendimento attivo dalla pagina delle impostazioni del servizio. Selezionare il nome utente nel menu in alto a destra, quindi selezionare impostazioni del servizio.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Trovare il servizio QnA Maker, quindi attivare **Active Learning** (Apprendimento attivo).

    > [!div class="mx-imgBorder"]
    > [![Nella pagina Impostazioni servizio selezionare la funzionalità di apprendimento attivo. Se non si è in grado di abilitare o disabilitare la funzionalità, potrebbe essere necessario aggiornare il servizio.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > La versione esatta sull'immagine precedente viene mostrata solo come esempio. La versione può essere diversa.

    Una volta abilitata l' **apprendimento attivo** , la Knowledge base suggerisce nuove domande a intervalli regolari in base alle domande inviate dall'utente. È possibile disabilitare l'**apprendimento attivo** selezionando nuovamente l'impostazione.

## <a name="review-suggested-alternate-questions"></a>Esaminare le domande alternative suggerite

[Esaminare le domande alternative suggerite](improve-knowledge-base.md) nella pagina **modifica** di ogni Knowledge base.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare una knowledge base](./manage-knowledge-bases.md)