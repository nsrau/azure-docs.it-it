---
title: Gestire QnA Maker app-QnA Maker
description: QnA Maker consente a più utenti di collaborare a una Knowledge Base. QnA Maker offre una funzionalità per migliorare la qualità della Knowledge base con l'apprendimento attivo. Uno può esaminare, accettare o rifiutare e aggiungere senza rimuovere o modificare le domande esistenti.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 10/11/2020
ms.openlocfilehash: 77290d271709db36f9c62e165b0b4070783b3ec6
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92128478"
---
# <a name="manage-qna-maker-app"></a>Gestisci app QnA Maker

QnA Maker consente di collaborare con autori e editor di contenuto diversi offrendo una funzionalità per limitare l'accesso al collaboratore in base al ruolo del collaboratore.
Scopri di più sui [concetti di autenticazione QnA Maker collaboratore](../Concepts/role-based-access-control.md).

È inoltre possibile migliorare la qualità della Knowledge base suggerendo domande alternative tramite l' [apprendimento attivo](../Concepts/active-learning-suggestions.md). Gli invii degli utenti vengono presi in considerazione e visualizzati come suggerimenti nell'elenco domande alternative. È possibile aggiungere tali suggerimenti come domande alternative o rifiutarli.

La knowledge base non viene modificata automaticamente. Per rendere effettive le modifiche, è necessario accettare i suggerimenti. Questi suggerimenti aggiungono domande, ma non modificano o rimuovono le domande esistenti.

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Aggiungere il controllo degli accessi in base al ruolo di Azure (RBAC di Azure)

QnA Maker consente a più utenti di collaborare su tutte le Knowledge base nella stessa risorsa QnA Maker. Questa funzionalità viene fornita con il [controllo degli accessi in base al ruolo di Azure (RBAC di Azure)](../../../active-directory/role-based-access-control-configure.md).

## <a name="access-at-the-qna-maker-resource-level"></a>Accesso a livello di risorsa QnA Maker

Non è possibile condividere una particolare Knowledge base in un servizio QnA Maker. Se si desidera un controllo di accesso più granulare, valutare la possibilità di distribuire le Knowledge base su risorse QnA Maker diverse, quindi aggiungere ruoli a ogni risorsa.

## <a name="add-a-role-to-a-resource"></a>Aggiungere un ruolo a una risorsa

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Aggiungere un account utente alla risorsa QnA Maker

Nei passaggi seguenti viene utilizzato il ruolo Collaboratore, ma è possibile aggiungere uno dei [ruoli](../reference-role-based-access-control.md) utilizzando questa procedura

1. Accedere al portale di [Azure](https://portal.azure.com/) e passare alla risorsa QnA Maker.

    ![Elenco di risorse QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Passare alla scheda **Controllo di accesso (IAM)**.

    ![Controllo di accesso (IAM) di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Selezionare **Aggiungi**.

    ![Aggiunta di un ruolo IAM di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Selezionare un ruolo dall'elenco seguente:

    |Ruolo|
    |--|
    |Proprietario|
    |Collaboratore|
    |Lettore di QnA Maker di servizi cognitivi|
    |Editor QnA Maker di servizi cognitivi|
    |Utente Servizi cognitivi|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker IAM Aggiungi ruolo.":::

1. Immettere l'indirizzo di posta elettronica dell'utente e fare clic su **Salva**.

    ![Aggiunta di un indirizzo e-mail IAM di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>Visualizza QnA Maker Knowledge base

Quando la persona che ha condiviso il servizio QnA Maker con i log nel [portale di QnA Maker](https://qnamaker.ai), può visualizzare tutte le Knowledge base nel servizio in base al proprio ruolo.

Quando si seleziona una Knowledge base, il relativo ruolo corrente su tale risorsa QnA Maker è visibile accanto al nome della Knowledge base.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="QnA Maker IAM Aggiungi ruolo.":::

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