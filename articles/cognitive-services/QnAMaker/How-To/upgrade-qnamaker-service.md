---
title: Aggiornare il servizio Qna Maker - QnA Maker
titleSuffix: Azure Cognitive Services
description: È possibile scegliere di aggiornare i singoli componenti dello stack di QnA Maker dopo la creazione iniziale.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.openlocfilehash: e37a903112f30917ff5051305bb2144981de1c9d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55883271"
---
# <a name="upgrade-your-qna-maker-service"></a>Aggiornare il servizio QnA Maker
È possibile scegliere di aggiornare i singoli componenti dello stack di QnA Maker dopo la creazione iniziale. Vedere i dettagli dei componenti dipendenti e della selezione dello SKU [qui](https://aka.ms/qnamaker-docs-capacity).

## <a name="upgrade-qna-maker-management-sku"></a>Aggiornare lo SKU di gestione di QnA Maker

Quando è necessario disporre di altre domande e risposte nella knowledge base, oltre il livello corrente, aggiornare il piano tariffario del servizio QnA Maker. 

Per aggiornare lo SKU di gestione di QnA Maker:

1. Passare alla risorsa QnA Maker nel portale di Azure e selezionare **Piano tariffario**.

    ![Risorsa QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Scegliere lo SKU appropriato e fare clic su **Seleziona**.

    ![Prezzi di QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Aggiornare il servizio app

 Quando la knowledge base deve servire più richieste dall'applicazione client, aggiornare il piano tariffario del servizio app.

È possibile [aumentare](https://docs.microsoft.com/azure/app-service/web-sites-scale) o ridurre le prestazioni del servizio app.

1. Passare alla risorsa del servizio app nel portale di Azure e selezionare le opzioni per **aumentare** o **ridurre** le prestazioni secondo le esigenze.

    ![Ridimensionamento del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-scale.png)

## <a name="upgrade-azure-search-service"></a>Aggiornare il servizio Ricerca di Azure

Quando si prevede di avere molte knowledge base, aggiornare il piano tariffario del servizio di Ricerca di Azure. 

Al momento non è possibile eseguire un aggiornamento sul posto dello SKU di Ricerca di Azure. È tuttavia possibile creare una nuova risorsa di Ricerca di Azure con lo SKU desiderato, ripristinare i dati nella nuova risorsa e quindi collegarla allo stack di QnA Maker.

1. Creare una nuova risorsa di Ricerca di Azure nel portale di Azure e scegliere lo SKU desiderato.

    ![Risorsa di Ricerca di Azure QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-new.png)

2. Ripristinare gli indici dalla risorsa di Ricerca di Azure originale alla nuova risorsa. Vedere il codice di esempio di ripristino del backup [qui](https://github.com/pchoudhari/QnAMakerBackupRestore).

3. Dopo aver ripristinato i dati, passare alla nuova risorsa di Ricerca di Azure, selezionare **Chiavi**, quindi annotare **Nome** e **Chiave amministratore**.

    ![Chiavi di Ricerca di Azure QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-azuresearch-keys.png)

4. Per collegare la nuova risorsa di Ricerca di Azure allo stack di QnA Maker, passare al servizio app QnA Maker.

    ![Servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource-list-appservice.png)

5. Selezionare **Impostazioni applicazione** e sostituire i campi **AzureSearchName** e **AzureSearchAdminKey** del passaggio 3.

    ![Impostazione del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-settings.png)

6. Riavviare il servizio app.

    ![Riavvio del servizio app QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Usare l'API QnA Maker](../Quickstarts/csharp.md)
