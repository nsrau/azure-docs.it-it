---
title: Creare un account di Servizi cognitivi nel portale di Azure
titlesuffix: Azure Cognitive Services
description: Come creare un account API servizi cognitivi di Azure nel portale di Azure.
services: cognitive-services
author: garyericson
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: garye
ms.openlocfilehash: b5adb5b8bddd0f379ac581f3268f0121791cf0bc
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546379"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>Avvio rapido: Creare un account di Servizi cognitivi nel portale di Azure

In questa guida di avvio rapido viene illustrato come effettuare l'iscrizione per Servizi cognitivi di Azure e creare una sottoscrizione di un singolo servizio o multiservizio. Questi servizi sono rappresentati da [risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) di Azure che consentono di connettersi a una o più API Servizi cognitivi di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure valida. [Creare un account](https://azure.microsoft.com/free/) gratuitamente.

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Creare e sottoscrivere una risorsa di Servizi cognitivi di Azure

Prima di iniziare, è importante sapere che ci sono due tipi di sottoscrizioni di Servizi cognitivi di Azure. La prima è una sottoscrizione di un singolo servizio, ad esempio Visione artificiale o i servizi di riconoscimento vocale. Una sottoscrizione di un singolo servizio è limitata alla risorsa specifica. La seconda è una sottoscrizione multiservizio per Servizi cognitivi di Azure. Questa sottoscrizione consente di usare una sottoscrizione singola per la maggior parte dei Servizi cognitivi di Azure. Questa opzione consente anche il consolidamento della fatturazione. Per altre informazioni, vedere [Prezzi di Servizi cognitivi](https://azure.microsoft.com/pricing/details/cognitive-services/).

>[!WARNING]
> Attualmente questi servizi **non** supportano le chiavi multiservizio: QnA Maker, servizi di riconoscimento vocale, visione artificiale personalizzato e rilevatore di anomalie.

Nelle sezioni successive vengono illustrati i passaggi per la creazione di una sottoscrizione di un singolo servizio o multiservizio.


### <a name="multi-service-subscription"></a>Sottoscrizione multiservizio

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **+Crea una risorsa**.

    ![Selezionare API Servizi cognitivi](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Individuare la barra di ricerca e immettere: **Servizi cognitivi**.

    ![Cercare Servizi cognitivi](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

3. Selezionare **Servizi cognitivi**.

    ![Selezionare Servizi cognitivi](media/cognitive-services-apis-create-account/azureMarketplaceMulti.png)

3. Nella pagina **Crea** specificare le informazioni seguenti:

    |    |    |
    |--|--|
    | **Nome** | Nome descrittivo per la risorsa di Servizi cognitivi. È consigliabile usare un nome descrittivo, ad esempio *MyCognitiveServicesAccount*. |
    | **Sottoscrizione** | Selezionare una delle sottoscrizioni di Azure disponibili. |
    | **Posizione** | Posizione dell'istanza di Servizi cognitivi. Posizioni diverse possono introdurre latenza, ma non hanno alcun impatto sulla disponibilità di runtime della risorsa. |
    | **Piano tariffario** | Il costo dell'account Servizi cognitivi dipende dalle opzioni scelte e dall'utilizzo. Per altre informazioni, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/) delle API.
    | **Gruppo di risorse** | [Gruppo di risorse Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) che conterrà la risorsa di Servizi cognitivi. È possibile creare un nuovo gruppo o aggiungerla a un gruppo già esistente. |

    ![Schermata di creazione risorsa](media/cognitive-services-apis-create-account/resource_create_screen_multi.png)

### <a name="single-service-subscription"></a>Sottoscrizione di un singolo servizio

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **+Crea una risorsa**.

    ![Selezionare API Servizi cognitivi](media/cognitive-services-apis-create-account/azurePortalScreen.png)

2. In Azure Marketplace selezionare **Intelligenza artificiale e Machine Learning**. Se il servizio a cui si è interessati non è presente, fare clic su **Visualizza tutto** per vedere l'intero catalogo di API Servizi cognitivi.

    ![Selezionare API Servizi cognitivi](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Nella pagina **Crea** specificare le informazioni seguenti:

    |    |    |
    |--|--|
    | **Nome** | Nome descrittivo per la risorsa di Servizi cognitivi. È consigliabile usare un nome descrittivo, ad esempio *MyNameFaceAPIAccount*. |
    | **Sottoscrizione** | Selezionare una delle sottoscrizioni di Azure disponibili. |
    | **Posizione** | Posizione dell'istanza di Servizi cognitivi. Posizioni diverse possono introdurre latenza, ma non hanno alcun impatto sulla disponibilità di runtime della risorsa. |
    | **Piano tariffario** | Il costo dell'account Servizi cognitivi dipende dalle opzioni scelte e dall'utilizzo. Per altre informazioni, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/) delle API.
    | **Gruppo di risorse** | [Gruppo di risorse Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) che conterrà la risorsa di Servizi cognitivi. È possibile creare un nuovo gruppo o aggiungerla a un gruppo già esistente. |

    ![Schermata di creazione risorsa](media/cognitive-services-apis-create-account/resource_create_screen.png)

## <a name="access-your-resource"></a>Accedere alla risorsa

> [!NOTE]
> I proprietari delle sottoscrizioni possono disabilitare la creazione di account Servizi cognitivi per gruppi di risorse o sottoscrizioni tramite i [criteri di Azure](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assegnando una definizione di criteri "Tipi di risorse non consentiti" e specificando **Microsoft.CognitiveServices/accounts** come tipo di risorsa di destinazione.

Dopo aver creato la risorsa, è possibile accedervi dal dashboard di Azure se è stata aggiunta. In caso contrario, è possibile trovarla in **Gruppi di risorse**.

All'interno della risorsa di Servizi cognitivi è possibile usare l'URL dell'endpoint e le chiavi nella sezione **Panoramica** per iniziare a effettuare chiamate alle API nelle applicazioni.

![Schermata delle risorse](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Autenticare le richieste a Servizi cognitivi di Azure](authentication.md)

## <a name="see-also"></a>Vedere anche 

* [Avvio rapido: Estrarre testo scritto a mano da un'immagine](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [Esercitazione: Creare un'app per rilevare e incorniciare i visi in un'immagine](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [Compilare una pagina Web di ricerca personalizzata](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Integrare Language Understanding (LUIS) con un bot usando Bot Framework](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
