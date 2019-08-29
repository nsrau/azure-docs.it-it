---
title: Aggiornare il servizio Qna Maker - QnA Maker
titleSuffix: Azure Cognitive Services
description: Condividere o aggiornare i servizi di QnA Maker per gestire meglio le risorse.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 08/26/2019
ms.author: diberry
ms.openlocfilehash: ba9c2cd5a85e02a7dd4b1091a050d76e94861964
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147095"
---
# <a name="share-or-upgrade-your-qna-maker-service"></a>Condividere o aggiornare il servizio QnA Maker
Condividere o aggiornare i servizi di QnA Maker per gestire meglio le risorse. 

È possibile scegliere di aggiornare i singoli componenti dello stack di QnA Maker dopo la creazione iniziale. Vedere i dettagli dei componenti dipendenti e della selezione dello SKU [qui](https://aka.ms/qnamaker-docs-capacity).

## <a name="share-existing-services-with-qna-maker"></a>Condividere i servizi esistenti con QnA Maker

QnA Maker crea diverse risorse di Azure. Per ridurre la gestione e trarre vantaggio dalla condivisione dei costi, usare la tabella seguente per comprendere cosa è possibile e non è possibile condividere:

|Service|Condividi|`Reason`|
|--|--|--|
|Servizi cognitivi|X|Non possibile dalla progettazione|
|Piano di servizio app|✔|Spazio su disco fisso allocato per un piano di servizio app. Se altre app, che condividono lo stesso piano di servizio app, usano spazio su disco significativo, il servizio app QnAMaker si risolverà in problemi.|
|Servizio app|X|Non possibile dalla progettazione|
|Application Insights|✔|Può essere condivisa|
|Servizio di ricerca|✔|1. `testkb` è un nome riservato per il servizio QnAMaker, non può essere usato da altri.<br>2. Il mapping dei sinonimi `synonym-map` con il nome è riservato per il servizio QnAMaker.<br>3. Il numero di KB pubblicati è limitato dal livello di servizio di ricerca. Se sono disponibili indici gratuiti, possono essere usati da altri servizi.|


## <a name="upgrade-qna-maker-management-sku"></a>Aggiornare lo SKU di gestione di QnA Maker

Quando è necessario disporre di altre domande e risposte nella knowledge base, oltre il livello corrente, aggiornare il piano tariffario del servizio QnA Maker. 

Per aggiornare lo SKU di gestione di QnA Maker:

1. Passare alla risorsa QnA Maker nel portale di Azure e selezionare **Piano tariffario**.

    ![Risorsa QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-resource.png)

2. Scegliere lo SKU appropriato e fare clic su **Seleziona**.

    ![Prezzi di QnA Maker](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-pricing-page.png)

## <a name="upgrade-app-service"></a>Aggiornare il servizio app

 Quando la knowledge base deve servire più richieste dall'applicazione client, aggiornare il piano tariffario del servizio app.

È possibile [aumentare](https://docs.microsoft.com/azure/app-service/manage-scale-up) o ridurre le prestazioni del servizio app.

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
