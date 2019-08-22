---
title: Collaborazione sulla Knowledge base-QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker consente a più utenti di collaborare a una Knowledge Base. Questa funzione viene fornita con il controllo degli accessi in base al ruolo di Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: d9c91d54fb357807682cd57f46b04454e4e2cfec
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876657"
---
# <a name="collaborate-on-your-knowledge-base"></a>Collaborare alla Knowledge Base

QnA Maker consente a più utenti di collaborare a una Knowledge Base. Questa funzione viene fornita con il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) di Azure. 

Eseguire questi passaggi per condividere il servizio QnA Maker:

1. Accedere al portale di Azure e passare alla risorsa QnA Maker.

    ![Elenco di risorse QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Passare alla scheda **Controllo di accesso (IAM)** .

    ![Controllo di accesso (IAM) di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Selezionare **Aggiungi**.

    ![Aggiunta di un ruolo IAM di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Selezionare il ruolo **Proprietario** o **Collaboratore**. Non è possibile concedere l'accesso di sola lettura tramite il controllo degli accessi in base al ruolo. I ruoli proprietario e collaboratore hanno le autorizzazioni di accesso in lettura/scrittura al servizio QnA Maker.

    ![Aggiunta di un ruolo IAM di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Immettere l'indirizzo di posta elettronica dell'utente e fare clic su **Salva**.

    ![Aggiunta di un indirizzo e-mail IAM di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Quando la persona con cui si è condiviso il servizio QnA Maker, accede al [portale di QnA Maker](https://qnamaker.ai) Visualizza tutte le Knowledge base presenti in tale servizio.

Non è possibile condividere una Knowledge Base specifica di un servizio QnA Maker. Per ottenere un controllo degli accessi più granulare, considerare la possibilità di distribuire le Knowledge Base tra diversi servizi QnA Maker.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Testare una Knowledge Base](./test-knowledge-base.md)
