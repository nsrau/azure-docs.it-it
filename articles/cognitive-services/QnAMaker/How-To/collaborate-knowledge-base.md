---
title: Collaborazione sulla knowledge base - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker consente a più utenti di collaborare a una Knowledge Base. Questa funzione viene fornita con il controllo degli accessi in base al ruolo di Azure.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: diberry
ms.openlocfilehash: b5adc7ebacde056a141ca3b361b9eb2ea7900a39
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75660750"
---
# <a name="collaborate-on-your-knowledge-base"></a>Collaborare alla Knowledge Base

QnA Maker consente a più persone di collaborare su tutte le knowledge base nella stessa risorsa QnA Maker. Questa funzione viene fornita con il [controllo degli accessi in base al ruolo](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure) di Azure.

Eseguire questi passaggi per condividere il servizio QnA Maker:

1. Accedere al portale di Azure e passare alla risorsa QnA Maker.Sign in to the Azure portal, and go to your QnA Maker resource.

    ![Elenco di risorse QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.PNG)

1. Passare alla scheda **Controllo di accesso (IAM)**.

    ![Controllo di accesso (IAM) di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.PNG)

1. Selezionare **Aggiungi**.

    ![Aggiunta di un ruolo IAM di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.PNG)

1. Selezionare il ruolo **Proprietario** o **Collaboratore**. Non è possibile concedere l'accesso di sola lettura tramite il controllo degli accessi in base al ruolo. I ruoli Proprietario e Collaboratore dispongono delle autorizzazioni di accesso in lettura e scrittura al servizio QnA Maker.

    ![Aggiunta di un ruolo IAM di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-role.PNG)

1. Immettere l'indirizzo di posta elettronica dell'utente e premere **Salva**.

    ![Aggiunta di un indirizzo e-mail IAM di QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.PNG)

Quando la persona, con cui hai condiviso il servizio QnA Maker, accede al [portale QnA Maker,](https://qnamaker.ai) può visualizzare tutte le knowledge base in tale servizio.

Non è possibile condividere una Knowledge Base specifica di un servizio QnA Maker. Per ottenere un controllo degli accessi più granulare, considerare la possibilità di distribuire le Knowledge Base tra diversi servizi QnA Maker.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Testare una Knowledge Base](./test-knowledge-base.md)
