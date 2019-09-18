---
title: 'Guida introduttiva: Creare una knowledge base - QnA Maker'
titleSuffix: Azure Cognitive Services
description: Usare il portale di servizi API QnA Maker per aggiungere o creare una knowledge base con chiacchiere. in modo da rendere più accattivante la propria app. Aggiungere un set predefinito delle migliori chit chat nella knowledge base come punto di partenza per la chit chat del proprio bot e risparmiare così tempi e costi per la scrittura di nuovi contenuti.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 798bbb725d4764c5ec7a1d69770e9508af0fdf5a
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376220"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Guida introduttiva: Creare una knowledge base con il portale di servizi API QnA Maker

Il portale di servizi API QnA Maker semplifica l'aggiunta di origini dati esistenti per creare una knowledge base. È possibile creare una nuova knowledge base di QnA Maker dai tipi di documento seguenti:

<!-- added for scanability -->
* Pagine di domande frequenti
* Manuali di prodotti
* Documenti strutturati

Includere una personalità per chit chat in modo da rendere la propria knowledge base più accattivante per gli utenti.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare. 

## <a name="create-a-new-knowledge-base"></a>Creare una nuova knowledge base

1. Accedere al [portale di QnA Maker](https://qnamaker.ai) con le credenziali di Azure e selezionare **Create a knowledge base** (Crea una knowledge base).

1. Se non è già stato creato un servizio QnA Maker, selezionare **Create a QnA service** (Crea servizio QnA). 

1. Selezionare il tenant di Azure, nome sottoscrizione di Azure e nome risorsa di Azure associato al servizio QnA Maker dagli elenchi presenti nel **passaggio 2** nel portale di QnA Maker. Selezionare il servizio QnA Maker che ospiterà la knowledge base.

    ![Configurare il servizio QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

1. Immettere il nome della knowledge base e le origini dati per la nuova knowledge base.

    ![Impostare le origini dati](../media/qnamaker-how-to-create-kb/set-data-sources.png)

1. Assegnare un **nome** al servizio, ad esempio `my first kb`. Sono supportati nomi duplicati e caratteri speciali.

1. Aggiungere la pagina di risoluzione dei problemi di QnA Maker come URL: `https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/troubleshooting`, quindi selezionare `+ Add URL`. Altre informazioni sui tipi di origini supportati sono disponibili [qui](../Concepts/data-sources-supported.md). Per questa guida di avvio rapido, **non caricare file** per i dati che devono essere estratti. Per informazioni sul numero di documenti che è possibile aggiungere, vedere le [informazioni sui prezzi](https://aka.ms/qnamaker-pricing).

1. Aggiungere **chiacchiere _professionali_** alla knowledge base. 

1. Selezionare **Crea la KB**.

    ![Creare la knowledge base](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Per l'estrazione dei dati possono essere necessari alcuni minuti.

    ![Estrazione](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. Se la knowledge base è stata creata correttamente, si verrà reindirizzati alla pagina **Knowledge base**.

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver completato la knowledge base, rimuoverla dal portale di QnA Maker.

## <a name="next-steps"></a>Passaggi successivi

Per le misure di risparmio sui costi, è possibile [condividere](set-up-qnamaker-service-azure.md#share-existing-services-with-qna-maker) alcune ma non tutte le risorse di Azure create per QnA Maker.

> [!div class="nextstepaction"]
> [Aggiungere domande con i metadati](../quickstarts/add-question-metadata-portal.md)
