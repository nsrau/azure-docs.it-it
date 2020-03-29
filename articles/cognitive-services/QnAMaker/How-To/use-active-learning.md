---
title: Migliorare una knowledge base - QnA Maker
description: Migliora la qualità della tua base di conoscenze con l'apprendimento attivo. Rivedere, accettare o rifiutare, aggiungere senza rimuovere o modificare le domande esistenti.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 33b3c547b0aea9a1e235bf8a05d01aa16b468a71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80071133"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>Usare l'apprendimento attivo per migliorare la knowledge base

[L'apprendimento attivo](../Concepts/active-learning-suggestions.md) ti consente di migliorare la qualità della tua base di conoscenze suggerendo domande alternative. Gli invii degli utenti vengono presi in considerazione e vengono visualizzati come suggerimenti nell'elenco delle domande alternative. Hai la flessibilità di aggiungere questi suggerimenti come domande alternative o di rifiutarli.

La knowledge base non viene modificata automaticamente. Per rendere effettive le modifiche, è necessario accettare i suggerimenti. Questi suggerimenti aggiungono domande, ma non modificano o rimuovono le domande esistenti.


## <a name="upgrade-runtime-version-to-use-active-learning"></a>Aggiornare la versione di runtime per usare l'apprendimento attivoUpgrade runtime version to use active learning

L'apprendimento attivo è supportato nella versione del runtime 4.4.0 e versioni successive. Se la knowledge base è stata creata in una versione precedente, [aggiornare il runtime](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) per usare questa funzionalità.

## <a name="turn-on-active-learning-for-alternate-questions"></a>Attivare l'apprendimento attivo per domande alternative

L'apprendimento attivo è disattivato per impostazione predefinita. Attivarlo per visualizzare le domande suggerite. Dopo aver attivato l'apprendimento attivo, è necessario inviare informazioni dall'app client a QnA Maker. Per altre informazioni, vedere [Flusso dell'architettura per l'uso di GenerateAnswer e eseguire il training delle API da un bot.](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)

1. Selezionare **Pubblica** per pubblicare la Knowledge Base. Le query di apprendimento attivo vengono raccolte solo dall'endpoint di stima dell'API GenerateAnswer.Active learning queries are collected from the GenerateAnswer API prediction endpoint only. Le query al riquadro Test nel portale QnA Maker non influiscono sull'apprendimento attivo.

1. Per attivare l'apprendimento attivo nel portale QnA Maker, andare nell'angolo in alto a destra, selezionare **Il proprio nome**, andare a Impostazioni [**servizio**](https://www.qnamaker.ai/UserSettings).

    ![Attiva le alternative di domanda suggerite per l'apprendimento attivo dalla pagina Impostazioni servizio. Seleziona il tuo nome utente nel menu in alto a destra, quindi seleziona Impostazioni servizio.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Trovare il servizio QnA Maker, quindi attivare **Active Learning** (Apprendimento attivo).

    > [!div class="mx-imgBorder"]
    > [![Nella pagina Impostazioni servizio, attiva la funzionalità Apprendimento attivo. Se non è possibile attivare o disattivare la funzionalità, potrebbe essere necessario aggiornare il servizio.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > La versione esatta dell'immagine precedente viene mostrata solo come esempio. La versione potrebbe essere diversa.

    Una volta abilitato **l'apprendimento attivo,** la Knowledge Base suggerisce nuove domande a intervalli regolari in base alle domande inviate dagli utenti. È possibile disabilitare l'**apprendimento attivo** selezionando nuovamente l'impostazione.

## <a name="review-suggested-alternate-questions"></a>Rivedere le domande alternative suggerite

[Esaminare le domande suggerite alternative](improve-knowledge-base.md) nella pagina **Modifica** di ogni Knowledge Base.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare una knowledge base](./manage-knowledge-bases.md)