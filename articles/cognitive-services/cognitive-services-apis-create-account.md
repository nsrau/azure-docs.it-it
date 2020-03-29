---
title: Creare una risorsa Servizi cognitivi nel portale di AzureCreate a Cognitive Services resource in the Azure portal
titleSuffix: Azure Cognitive Services
description: Iniziare a usare Servizi cognitivi di Azure creando e sottoscrivendo una risorsa nel portale di Azure.Get started with Azure Cognitive Services by creating and subscribing to a resource in the Azure portal.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: dd4444bf42bcc8dda95f8fa37b42a365538efa85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219478"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Creare una risorsa di Servizi cognitivi usando il portale di AzureCreate a Cognitive Services resource using the Azure portal

Usare questa guida introduttiva per iniziare a usare Servizi cognitivi di Azure.Use this quickstart to start using Azure Cognitive Services. Dopo aver creato una risorsa del servizio cognitivo nel portale di Azure, si otterrà un endpoint e una chiave per l'autenticazione delle applicazioni.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione valida di Azure - [Creane una gratuitamente.](https://azure.microsoft.com/free/)

## <a name="create-a-new-azure-cognitive-services-resource"></a>Creare una nuova risorsa Servizi cognitivi di AzureCreate a new Azure Cognitive Services resource

1. Crea una risorsa.

    #### <a name="multi-service-resource"></a>[Risorsa multiservizio](#tab/multiservice)
    
    La risorsa multiservizio è denominata **Servizi cognitivi** nel portale. [Creare una risorsa di Servizi cognitivi](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).
    
    A questo punto, la risorsa multiservizio consente l'accesso ai servizi cognitivi seguenti:At this time, the multi-service resource enables access to the following Cognitive Services:
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Visione artificiale  | Content Moderator                                    | Viso               | Language Understanding (LUIS) | Text Analytics   |
    | Traduzione testuale  | Ricerca Bing v7 <br>(Web, Immagine, Notizie, Video, Visuale) | Ricerca personalizzata Bing | Ricerca entità Bing            | Suggerimenti automatici Bing |
    | Controllo ortografico Bing |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resource"></a>[Risorsa a servizio singolo](#tab/singleservice)

    Usare i collegamenti seguenti per creare una risorsa per i servizi cognitivi disponibili:Use the below links to create a resource for the available Cognitive Services:

    | Visione                      | Sintesi vocale                  | Linguaggio                          | Decisione             | Ricerca                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Visione artificiale](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Servizi Voce](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Lettore immersivo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Rilevamento anomalie](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [API ricerca Bing V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Servizio di visione personalizzato](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Riconoscimento del parlante](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Moderatore dei contenuti](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Ricerca personalizzata Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Viso](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [Creatore QnA](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Ricerca entità Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Riconoscimento input penna](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Analisi del testo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Controllo ortografico Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Testo traduttore](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Suggerimenti automatici Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. Nella pagina **Crea** specificare le informazioni seguenti:

    #### <a name="multi-service-resource"></a>[Risorsa multiservizio](#tab/multiservice)

    |    |    |
    |--|--|
    | **Nome** | Nome descrittivo per la risorsa di Servizi cognitivi. Ad esempio, *MyCognitiveServicesResource*. |
    | **Sottoscrizione** | Selezionare una delle sottoscrizioni di Azure disponibili. |
    | **Percorso** | Posizione dell'istanza di Servizi cognitivi. Posizioni diverse possono introdurre latenza, ma non hanno alcun impatto sulla disponibilità di runtime della risorsa. |
    | **Piano tariffario** | Il costo dell'account Servizi cognitivi dipende dalle opzioni scelte e dall'utilizzo. Per altre informazioni, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/) delle API.
    | **Gruppo di risorse** | Gruppo di risorse Azure che conterrà la risorsa di Servizi cognitivi. È possibile creare un nuovo gruppo o aggiungerla a un gruppo già esistente. |

    ![Schermata di creazione risorsa](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Fare clic su **Crea**.

    #### <a name="single-service-resource"></a>[Risorsa a servizio singolo](#tab/singleservice)

    |    |    |
    |--|--|
    | **Nome** | Nome descrittivo per la risorsa di Servizi cognitivi. Ad esempio, *TextAnalyticsResource*. |
    | **Sottoscrizione** | Selezionare una delle sottoscrizioni di Azure disponibili. |
    | **Percorso** | Posizione dell'istanza di Servizi cognitivi. Posizioni diverse possono introdurre latenza, ma non hanno alcun impatto sulla disponibilità di runtime della risorsa. |
    | **Piano tariffario** | Il costo dell'account Servizi cognitivi dipende dalle opzioni scelte e dall'utilizzo. Per altre informazioni, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/) delle API.
    | **Gruppo di risorse** | Gruppo di risorse Azure che conterrà la risorsa di Servizi cognitivi. È possibile creare un nuovo gruppo o aggiungerla a un gruppo già esistente. |

    ![Schermata di creazione risorsa](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Fare clic su **Crea**.

    ***


## <a name="get-the-keys-for-your-resource"></a>Ottenere le chiavi per la risorsaGet the keys for your resource

1. Dopo aver distribuito correttamente la risorsa, fare clic su **Vai alla risorsa** in Passaggi **successivi**.

    ![Cercare Servizi cognitivi](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. Dal riquadro delle guide rapide che si apre, è possibile accedere alla chiave e all'endpoint.

    ![Ottenere chiave ed endpointGet key and endpoint](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. L'eliminazione del gruppo di risorse comporta anche l'eliminazione di tutte le altre risorse contenute nel gruppo.

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e scegliere **Gruppi di risorse** per visualizzare l'elenco dei gruppi di risorse.
2. Individuare il gruppo di risorse contenente la risorsa da eliminare
3. Fare clic con il pulsante destro del mouse sull'elenco dei gruppi di risorse. Scegliere **Elimina gruppo di risorse** e confermare.

## <a name="see-also"></a>Vedere anche

* [Autenticare le richieste a Servizi cognitivi di Azure](authentication.md)
* [Che cos'è Servizi cognitivi di Azure?](Welcome.md)
* [Supporto del linguaggio naturale](language-support.md)
* [Supporto per contenitori Docker](cognitive-services-container-support.md)
