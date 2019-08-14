---
title: Configurazione di un servizio - QnA Maker
titleSuffix: Azure Cognitive Services
description: Prima di poter creare una Knowledge Base di QnA Maker, è necessario configurare un servizio QnA Maker in Azure. Qualsiasi utente autorizzato a creare nuove risorse in una sottoscrizione può configurare un servizio QnA Maker.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/13/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: f30b55eda4a02cfb3e961c0019128e4fe686cf53
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967664"
---
# <a name="create-a-qna-maker-service"></a>Creare un servizio QnA Maker

Prima di poter creare una Knowledge Base di QnA Maker, è necessario configurare un servizio QnA Maker in Azure. Qualsiasi utente autorizzato a creare nuove risorse in una sottoscrizione può configurare un servizio QnA Maker.

## <a name="create-a-new-service"></a>Creare un nuovo servizio

Questa procedura distribuisce alcune risorse di Azure. Insieme, queste risorse gestiscono il contenuto della Knowledge Base e forniscono capacità di risposta alle domande attraverso un endpoint.

1. Accedere al portale di Azure e [creare una risorsa QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) .

1. Selezionare **Crea** dopo aver letto i termini e le condizioni.

    ![Creare un nuovo servizio QnA Maker](../media/qnamaker-how-to-setup-service/create-new-resource-button.png)

1. In **QnA Maker** selezionare i livelli e le aree appropriati.

    ![Creare un nuovo servizio QnA Maker - Piano tariffario e aree](../media/qnamaker-how-to-setup-service/enter-qnamaker-info.png)

    * Specificare un **nome** univoco per identificare il servizio QnA Maker. Questo nome identifica anche l'endpoint QnA Maker a cui saranno associate le Knowledge Base.
    * Scegliere la **sottoscrizione** in cui verrà distribuita la risorsa QnA Maker.
    * Selezionare il piano tariffario per i servizi di gestione QnA Maker (portale e API di gestione). Vedere [qui](https://aka.ms/qnamaker-pricing) per informazioni dettagliate sui prezzi degli SKU.
    * Creare un nuovo **gruppo di risorse** (scelta consigliata) o usare un gruppo di risorse esistente in cui distribuire questa risorsa QnA Maker. QnA Maker crea diverse risorse di Azure; Quando si crea un gruppo di risorse che contiene queste risorse, è possibile trovare, gestire ed eliminare facilmente queste risorse in base al nome del gruppo di risorse.
    * Selezionare una **località del gruppo di risorse**.
    * Scegliere il **piano tariffario** per il servizio Ricerca di Azure. Se l'opzione Livello gratuito è disabilitata, nella sottoscrizione è già stato distribuito un livello gratuito per Ricerca di Azure. In tal caso è necessario iniziare con il livello Basic di Ricerca di Azure. Vedere [qui](https://azure.microsoft.com/pricing/details/search/) i dettagli dei prezzi di Ricerca di Azure.
    * Scegliere il **percorso di ricerca** per la distribuzione dei dati di Ricerca di Azure. Le limitazioni relative ai percorsi in cui devono essere archiviati i dati dei clienti determinano il percorso scelto per Ricerca di Azure.
    * Assegnare un nome al servizio app in **Nome app**.
    * Per impostazione predefinita, il servizio app è impostato sul livello Standard (S1). È possibile modificare il piano dopo la creazione. Vedere [qui](https://azure.microsoft.com/pricing/details/app-service/) per altri dettagli sui prezzi del servizio app.
    * Scegliere il percorso del **sito Web** in cui verrà distribuito il servizio app.

        > [!NOTE]
        > Il percorso di ricerca sarà diverso dal percorso del sito Web.

    * Scegliere se abilitare o meno **Application Insights**. Se **Application Insights** è abilitato, QnA Maker raccoglierà dati di telemetria su traffico, log delle chat ed errori.
    * Scegliere il **percorso** in cui verrà distribuita la risorsa Application Insights.
    * Per le misure di risparmio sui costi, è possibile [condividere](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) alcune risorse di Azure create per QnA Maker. 

1. Una volta convalidati tutti i campi, è possibile selezionare **Crea** per avviare la distribuzione di questi servizi nella sottoscrizione. Questa operazione richiederà qualche minuto.

1. Dopo aver completato la distribuzione, nella sottoscrizione verranno visualizzate le seguenti risorse create.

    ![Risorsa creata - Nuovo servizio QnA Maker](../media/qnamaker-how-to-setup-service/resources-created.png)

## <a name="region-of-management-service"></a>Area del servizio di gestione

Il servizio di gestione di QnA Maker, usato solo per il portale & per l'elaborazione iniziale dei dati, è disponibile solo negli Stati Uniti occidentali. Nessun dato cliente è archiviato in questo servizio Stati Uniti occidentali.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Creare e pubblicare una Knowledge Base](../Quickstarts/create-publish-knowledge-base.md)
