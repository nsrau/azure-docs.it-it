---
title: Collaborazione sulla Knowledge base-QnA Maker
description: QnA Maker consente a più utenti di collaborare a una Knowledge Base. Questa funzione viene fornita con il controllo degli accessi in base al ruolo di Azure.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 1a2908b4b65017f427682627ce5d83b186956a58
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650777"
---
# <a name="collaboration-with-authors-and-editors"></a>Collaborazione con autori ed editor

La collaborazione viene fornita a livello di risorsa QnA Maker per consentire di limitare l'accesso al collaboratore in base al ruolo del collaboratore. Scopri di più sui [concetti](../Concepts/role-based-access-control.md)di autenticazione QnA Maker collaboratore.

## <a name="add-role-based-access-rbac-to-your-qna-maker-resource"></a>Aggiungere l'accesso in base al ruolo (RBAC) alla risorsa QnA Maker

QnA Maker consente a più utenti di collaborare su tutte le Knowledge base nella stessa risorsa QnA Maker. Questa funzione viene fornita con il [controllo degli accessi in base al ruolo](../../../active-directory/role-based-access-control-configure.md) di Azure.

## <a name="access-at-the-qna-maker-resource-level"></a>Accesso a livello di risorsa QnA Maker

Non è possibile condividere una particolare Knowledge base in un servizio QnA Maker. Se si desidera un controllo di accesso più granulare, valutare la possibilità di distribuire le Knowledge base su risorse QnA Maker diverse, quindi aggiungere ruoli a ogni risorsa.

## <a name="add-role-to-resource"></a>Aggiungi ruolo a risorsa

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Aggiungere un account utente alla risorsa QnA Maker

Nei passaggi seguenti viene utilizzato il ruolo Collaboratore, ma è possibile aggiungere uno dei [ruoli](../reference-role-based-access-control.md) utilizzando questa procedura

1. Accedere al portale di [Azure](https://portal.azure.com/) e passare alla risorsa QnA Maker.

    ![Elenco di risorse QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Passare alla scheda **Controllo di accesso (IAM)**.

    ![Controllo di accesso (IAM) di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Selezionare **Aggiungi**.

    ![Aggiunta di un ruolo IAM di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Selezionare un ruolo dall'elenco seguente:

    |Ruolo|
    |--|
    |Proprietario|
    |Autore di contributi|
    |Lettore di QnA Maker|
    |Editor QnA Maker|
    |Utente Servizi cognitivi|

    ![Aggiunta di un ruolo IAM di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Immettere l'indirizzo di posta elettronica dell'utente e fare clic su **Salva**.

    ![Aggiunta di un indirizzo e-mail IAM di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Quando la persona che ha condiviso il servizio QnA Maker con i log nel [portale di QnA Maker](https://qnamaker.ai), può visualizzare tutte le Knowledge base nel servizio in base al proprio ruolo.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Testare una Knowledge Base](./test-knowledge-base.md)

Altre informazioni sulla collaborazione:
* Controllo degli accessi in base al ruolo di [Azure](../../../active-directory/role-based-access-control-configure.md)
* [Concetti relativi](../Concepts/role-based-access-control.md) al controllo degli accessi in base al ruolo QnA Maker
