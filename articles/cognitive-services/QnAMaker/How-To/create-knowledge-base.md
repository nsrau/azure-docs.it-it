---
title: Crea Knowledge base-QnA Maker
titleSuffix: Azure Cognitive Services
description: Per aggiungere crea una Knowledge base con Chit-Chat, usare il portale del servizio API QnA Maker. in modo da rendere più accattivante la propria app. Aggiungere un set predefinito delle migliori chit chat nella knowledge base come punto di partenza per la chit chat del proprio bot e risparmiare così tempi e costi per la scrittura di nuovi contenuti.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 322a1d25ed434d8be674288b1b13d6ecf961590b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193573"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Avvio rapido: Creare una Knowledge base tramite il portale del servizio API QnA Maker

Il portale del servizio API QnA Maker semplifica l'aggiunta di origini dati esistenti durante la creazione di una Knowledge base. È possibile creare una nuova knowledge base di QnA Maker dai tipi di documento seguenti:

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

1. Assegnare un **nome** al servizio, ad `my first kb`esempio. Sono supportati nomi duplicati e caratteri speciali.

1. Aggiungere la pagina di risoluzione dei problemi di QnA Maker come `https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/troubleshooting`URL:, `+ Add URL`quindi selezionare. Altre informazioni sui tipi di origini supportati sono disponibili [qui](../Concepts/data-sources-supported.md). Per questa Guida introduttiva, **non caricare i file** per i dati che si desidera estrarre. Per informazioni sul numero di documenti che è possibile aggiungere, vedere le [informazioni sui prezzi](https://aka.ms/qnamaker-pricing).

1. Aggiungere la **chat _professionale_**  alla KB. 

1. Selezionare **Crea la KB**.

    ![Creare la knowledge base](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Potrebbero essere necessari alcuni minuti per estrarre i dati.

    ![Estrazione](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. Una volta creata correttamente la Knowledge base, si verrà reindirizzati alla pagina della **Knowledge base** .

## <a name="clean-up-resources"></a>Pulire le risorse

Dopo aver completato la knowledge base, rimuoverla dal portale di QnA Maker.

## <a name="next-steps"></a>Passaggi successivi

Per le misure di risparmio sui costi, è possibile [condividere](set-up-qnamaker-service-azure.md#share-existing-services-with-qna-maker) alcune risorse di Azure create per QnA Maker.

> [!div class="nextstepaction"]
> [Aggiungere domande con i metadati](../quickstarts/add-question-metadata-portal.md)
