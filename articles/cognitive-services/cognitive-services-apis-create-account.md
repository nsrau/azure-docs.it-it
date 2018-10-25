---
title: Creare un account delle API Servizi cognitivi nel portale di Azure
titlesuffix: Azure Cognitive Services
description: Come creare un account delle API Servizi cognitivi Microsoft nel Portale di Azure.
services: cognitive-services
author: garyericson
manager: cgronlun
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: garye
ms.openlocfilehash: 37f53303a3b0c224c1286fb488a796fd5cdee0e5
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/17/2018
ms.locfileid: "49386417"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>Guida introduttiva: Creare un account Servizi cognitivi nel portale di Azure

Seguire questa guida introduttiva per iniziare a usare Servizi cognitivi di Azure. Questi servizi sono rappresentati da [risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) di Azure che consentono di connettersi a una o più delle numerose API Servizi cognitivi disponibili.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure valida. È possibile [creare un account](https://azure.microsoft.com/free/) gratuitamente.

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Creare e sottoscrivere una risorsa di Servizi cognitivi di Azure

1. Accedere al [portale di Azure](http://portal.azure.com) e fare clic su **+Crea una risorsa**.
    
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
    | **Gruppo di risorse** | [Gruppo di risorse Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group) che conterrà la risorsa di Servizi cognitivi. È possibile creare un nuovo gruppo o aggiungerla a un gruppo già esistente. |

    ![Schermata di creazione risorsa](media/cognitive-services-apis-create-account/resource_create_screen.png)

## <a name="access-your-resource"></a>Accedere alla risorsa 

> [!NOTE]
> I proprietari delle sottoscrizioni possono disabilitare la creazione di account Servizi cognitivi per gruppi di risorse o sottoscrizioni tramite i [criteri di Azure](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assegnando una definizione di criteri "Tipi di risorse non consentiti" e specificando **Microsoft.CognitiveServices/accounts** come tipo di risorsa di destinazione.

Dopo aver creato la risorsa, è possibile accedervi dal dashboard di Azure se è stata aggiunta. In caso contrario, è possibile trovarla in **Gruppi di risorse**.

All'interno della risorsa di Servizi cognitivi è possibile usare l'URL dell'endpoint e le chiavi nella sezione **Panoramica** per iniziare a effettuare chiamate alle API nelle applicazioni.

![Schermata delle risorse](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esercitazione in C# dell'API Visione artificiale](https://docs.microsoft.com/azure/cognitive-services/computer-vision/tutorials/csharptutorial)

## <a name="see-also"></a>Vedere anche 

* [Guida introduttiva: Estrarre testo scritto a mano da un'immagine](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [Esercitazione: Creare un'app per rilevare e incorniciare i visi in un'immagine](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [Compilare una pagina Web di ricerca personalizzata](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Integrare Language Understanding (LUIS) con un bot usando Bot Framework](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
