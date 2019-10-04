---
title: Creare una risorsa Servizi cognitivi nella portale di Azure
titleSuffix: Azure Cognitive Services
description: Per iniziare a usare servizi cognitivi di Azure, creare e sottoscrivere una risorsa nel portale di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: d0b2c9c7450b8a44ca93a9d9fa8770b7ab3d9282
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743860"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Creare una risorsa di servizi cognitivi usando il portale di Azure

Usare questa Guida introduttiva per iniziare a usare servizi cognitivi di Azure. Dopo aver creato una risorsa di servizio cognitivo nella portale di Azure, si otterranno un endpoint e una chiave per autenticare le applicazioni.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure valida- [crearne una gratuita](https://azure.microsoft.com/free/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Creare una nuova risorsa Servizi cognitivi di Azure

1. Crea una risorsa.

    #### <a name="multi-service-resourcetabmultiservice"></a>[Risorsa multiservizio](#tab/multiservice)
    
    La risorsa multiservizio è denominata **Servizi cognitivi** nel portale. [Creare una risorsa Servizi cognitivi](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).
    
    A questo punto, la risorsa multiservizio consente l'accesso ai servizi cognitivi seguenti:
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Visione artificiale  | Content Moderator                                    | Viso               | Language Understanding (LUIS) | Analisi del testo   |
    | Traduzione testuale  | Ricerca Bing v7 <br>(Web, immagini, notizie, video, oggetti visivi) | Ricerca personalizzata Bing | Ricerca entità Bing            | Suggerimenti automatici Bing |
    | Controllo ortografico Bing |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resourcetabsingleservice"></a>[Risorsa a servizio singolo](#tab/singleservice)

    Usare i collegamenti seguenti per creare una risorsa per i servizi cognitivi disponibili:

    | Visione                      | Riconoscimento vocale                  | Linguaggio                          | Decisione             | Cerca                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Visione artificiale](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Servizi Voce](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Lettore immersivo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Rilevamento anomalie](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [API Ricerca Bing V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Servizio visione artificiale personalizzato](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Riconoscimento del parlante](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUIS) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Ricerca personalizzata Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Viso](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Ricerca entità Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Riconoscimento input penna](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Analisi del testo](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Controllo ortografico Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Traduzione testuale](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Suggerimenti automatici Bing](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. Nella pagina **Crea** specificare le informazioni seguenti:

    #### <a name="multi-service-resourcetabmultiservice"></a>[Risorsa multiservizio](#tab/multiservice)

    |    |    |
    |--|--|
    | **Nome** | Nome descrittivo per la risorsa di Servizi cognitivi. Ad esempio, *MyCognitiveServicesResource*. |
    | **Sottoscrizione** | Selezionare una delle sottoscrizioni di Azure disponibili. |
    | **Posizione** | Posizione dell'istanza di Servizi cognitivi. Posizioni diverse possono introdurre latenza, ma non hanno alcun impatto sulla disponibilità di runtime della risorsa. Ricordare il percorso di Azure, come potrebbe essere necessario quando si chiamano i servizi cognitivi di Azure. |
    | **Piano tariffario** | Il costo dell'account Servizi cognitivi dipende dalle opzioni scelte e dall'utilizzo. Per altre informazioni, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/) delle API.
    | **Gruppo di risorse** | Il gruppo di risorse di Azure che conterrà la risorsa Servizi cognitivi. È possibile creare un nuovo gruppo o aggiungerla a un gruppo già esistente. |

    ![Schermata di creazione risorsa](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Fare clic su **Create**(Crea).

    #### <a name="single-service-resourcetabsingleservice"></a>[Risorsa a servizio singolo](#tab/singleservice)

    |    |    |
    |--|--|
    | **Nome** | Nome descrittivo per la risorsa di Servizi cognitivi. Ad esempio, *TextAnalyticsResource*. |
    | **Sottoscrizione** | Selezionare una delle sottoscrizioni di Azure disponibili. |
    | **Posizione** | Posizione dell'istanza di Servizi cognitivi. Posizioni diverse possono introdurre latenza, ma non hanno alcun impatto sulla disponibilità di runtime della risorsa. Ricordare il percorso di Azure, come potrebbe essere necessario quando si chiamano i servizi cognitivi di Azure. |
    | **Piano tariffario** | Il costo dell'account Servizi cognitivi dipende dalle opzioni scelte e dall'utilizzo. Per altre informazioni, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/) delle API.
    | **Gruppo di risorse** | Il gruppo di risorse di Azure che conterrà la risorsa Servizi cognitivi. È possibile creare un nuovo gruppo o aggiungerla a un gruppo già esistente. |

    ![Schermata di creazione risorsa](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Fare clic su **Create**(Crea).

    ***

## <a name="get-the-keys-for-your-resource"></a>Ottenere le chiavi per la risorsa

1. Dopo che la risorsa è stata distribuita correttamente, fare clic su **Vai alla risorsa** in **passaggi successivi**.

    ![Cercare Servizi cognitivi](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. Dal riquadro avvio rapido visualizzato è possibile accedere alla chiave e all'endpoint.

    ![Ottieni chiave ed endpoint](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse, vengono eliminate anche tutte le altre risorse contenute nel gruppo.

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e scegliere **Gruppi di risorse** per visualizzare l'elenco dei gruppi di risorse.
2. Individuare il gruppo di risorse che contiene la risorsa da eliminare
3. Fare clic con il pulsante destro del mouse sull'elenco dei gruppi di risorse. Scegliere **Elimina gruppo di risorse** e confermare.

## <a name="see-also"></a>Vedere anche

* [Autenticare le richieste a Servizi cognitivi di Azure](authentication.md)
* [Che cosa sono i servizi cognitivi di Azure?](Welcome.md)
* [Supporto del linguaggio naturale](language-support.md)
* [Supporto del contenitore Docker](cognitive-services-container-support.md)
