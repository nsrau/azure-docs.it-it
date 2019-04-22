---
title: Configurazione di un servizio - QnA Maker
titleSuffix: Azure Cognitive Services
description: Prima di poter creare una Knowledge Base di QnA Maker, è necessario configurare un servizio QnA Maker in Azure. Qualsiasi utente autorizzato a creare nuove risorse in una sottoscrizione può configurare un servizio QnA Maker.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/02/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 90e7abf229e0ee1ca396150fee0c1f0970d257aa
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "58876402"
---
# <a name="create-a-qna-maker-service"></a>Creare un servizio QnA Maker

Prima di poter creare una Knowledge Base di QnA Maker, è necessario configurare un servizio QnA Maker in Azure. Qualsiasi utente autorizzato a creare nuove risorse in una sottoscrizione può configurare un servizio QnA Maker.

## <a name="create-a-new-service"></a>Creare un nuovo servizio

Questa procedura consente di distribuire alcune risorse di Azure. Insieme, queste risorse gestiscono il contenuto della Knowledge Base e forniscono capacità di risposta alle domande attraverso un endpoint.

1. Accedere al [portale di Azure](<https://portal.azure.com>).

1. Selezionare **Aggiungi nuova risorsa**, digitare "qna maker" nella ricerca e selezionare la risorsa di QnA Maker

    ![Creare un nuovo servizio QnA Maker - Aggiungere una nuova risorsa](../media/qnamaker-how-to-setup-service/create-new-resource.png)

1. Selezionare **Create** dopo aver letto i termini e condizioni.

    ![Creare un nuovo servizio QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. In **QnA Maker** selezionare i livelli e le aree appropriati.

    ![Creare un nuovo servizio QnA Maker - Piano tariffario e aree](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Specificare un **nome** univoco per identificare il servizio QnA Maker. Questo nome identifica anche l'endpoint QnA Maker a cui saranno associate le Knowledge Base.
    * Scegliere la **sottoscrizione** in cui verrà distribuita la risorsa QnA Maker.
    * Selezionare il **piano tariffario** per i servizi di gestione di QnA Maker (portale e API di gestione). Vedere [qui](https://aka.ms/qnamaker-pricing) per informazioni dettagliate sui prezzi degli SKU.
    * Creare un nuovo **gruppo di risorse** (scelta consigliata) o usare un gruppo di risorse esistente in cui distribuire questa risorsa QnA Maker. QnA Maker crea diverse risorse di Azure; Quando si crea un gruppo di risorse per contenere queste risorse, è possibile trovare facilmente, gestire ed eliminare queste risorse per il nome del gruppo di risorse.
    * Scegliere il **piano tariffario** per il servizio Ricerca di Azure. Se l'opzione Livello gratuito è disabilitata, nella sottoscrizione è già stato distribuito un livello gratuito per Ricerca di Azure. In tal caso è necessario iniziare con il livello Basic di Ricerca di Azure. Vedere [qui](https://azure.microsoft.com/pricing/details/search/) i dettagli dei prezzi di Ricerca di Azure.
    * Scegliere il **percorso di ricerca** per la distribuzione dei dati di Ricerca di Azure. Le limitazioni relative ai percorsi in cui devono essere archiviati i dati dei clienti determinano il percorso scelto per Ricerca di Azure.
    * Assegnare un nome al servizio app in **Nome app**.
    * Per impostazione predefinita, il servizio app è impostato sul livello Standard (S1). È possibile modificare il piano dopo la creazione. Vedere [qui](https://azure.microsoft.com/pricing/details/app-service/) per altri dettagli sui prezzi del servizio app.
    * Scegliere il percorso del **sito Web** in cui verrà distribuito il servizio app.

        > [!NOTE]
        > Il percorso di ricerca sarà diverso dal percorso del sito Web.

    * Scegliere se abilitare o meno **Application Insights**. Se **Application Insights** è abilitato, QnA Maker raccoglierà dati di telemetria su traffico, log delle chat ed errori.
    * Scegliere il **percorso** in cui verrà distribuita la risorsa Application Insights.
    * Per le misure di risparmio sui costi, è possibile [condividere](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) alcuni ma non tutte le risorse di Azure create per QnA Maker. 

1. Dopo che tutti i campi vengono convalidati, è possibile selezionare **Create** per avviare la distribuzione di questi servizi nella sottoscrizione. Questa operazione richiederà qualche minuto.

1. Dopo aver completato la distribuzione, nella sottoscrizione verranno visualizzate le seguenti risorse create.

    ![Risorsa creata - Nuovo servizio QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="region-of-management-service"></a>Area del servizio di gestione

Il servizio di gestione di QnA Maker, usato solo per il portale e per l'elaborazione dei dati iniziale, è disponibile solo negli Stati Uniti occidentali. Nessun dato cliente viene archiviato in questo servizio di Stati Uniti occidentali.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare e pubblicare una Knowledge Base](../Quickstarts/create-publish-knowledge-base.md)
