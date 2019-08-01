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
ms.openlocfilehash: af01c0c2586ce7df1902a0bcc502c6fd06a5215d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697917"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Creare una risorsa di servizi cognitivi usando il portale di Azure

Usare questa Guida introduttiva per iniziare a usare servizi cognitivi di Azure usando il portale di Azure. I servizi cognitivi sono rappresentati da [risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) di Azure create nella sottoscrizione di Azure. Dopo aver creato la risorsa, usare le chiavi e l'endpoint generati per autenticare le applicazioni. 

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure valida- [crearne una gratuitamente](https://azure.microsoft.com/free/)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Creare una nuova risorsa Servizi cognitivi di Azure

Prima di creare una risorsa Servizi cognitivi, è necessario disporre di un gruppo di risorse di Azure per contenere la risorsa. Quando si crea una nuova risorsa, è possibile scegliere di creare un nuovo gruppo di risorse o utilizzarne uno esistente. Questo articolo illustra come creare un nuovo gruppo di risorse.

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **+Crea una risorsa**.

    ![Selezionare API Servizi cognitivi](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. È possibile trovare servizi cognitivi disponibili con nei modi seguenti:
    * Usare la barra di ricerca e immettere il nome del servizio a cui si vuole effettuare la sottoscrizione.
        * Per creare una risorsa multiservizio, immettere **Servizi cognitivi** nella barra di ricerca e selezionare la risorsa **Servizi cognitivi** .

        ![Cercare Servizi cognitivi](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * Per visualizzare tutti i servizi cognitivi disponibili, selezionare **ai + Machine Learning**, in **Azure Marketplace**. Se il servizio a cui si è interessati non è visibile, fare clic su **Visualizza tutto** e scorrere fino a **Servizi cognitivi**. Fare clic su **altro** per visualizzare l'intero catalogo di API servizi cognitivi.
    
        ![Selezionare API Servizi cognitivi](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Nella pagina **Crea** specificare le informazioni seguenti:

    > [!IMPORTANT]
    > Ricordare il percorso di Azure, come potrebbe essere necessario quando si chiamano i servizi cognitivi di Azure.

    |    |    |
    |--|--|
    | **Nome** | Nome descrittivo per la risorsa di Servizi cognitivi. Ad esempio *MyCognitiveServicesAccount*. |
    | **Sottoscrizione** | Selezionare una delle sottoscrizioni di Azure disponibili. |
    | **Posizione** | Posizione dell'istanza di Servizi cognitivi. Posizioni diverse possono introdurre latenza, ma non hanno alcun impatto sulla disponibilità di runtime della risorsa. |
    | **Piano tariffario** | Il costo dell'account Servizi cognitivi dipende dalle opzioni scelte e dall'utilizzo. Per altre informazioni, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/) delle API.
    | **Gruppo di risorse** | [Gruppo di risorse Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) che conterrà la risorsa di Servizi cognitivi. È possibile creare un nuovo gruppo o aggiungerla a un gruppo già esistente. |

    ![Schermata di creazione risorsa](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-resource"></a>Ottenere le chiavi per la risorsa

Dopo aver creato la risorsa, è possibile accedervi dal dashboard di Azure se è stata aggiunta. In caso contrario, è possibile trovarla in **Gruppi di risorse**. Dopo aver selezionato la risorsa, è possibile ottenere le chiavi selezionando **chiavi** in **Gestione risorse**.

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Piani tariffari e fatturazione

I piani tariffari (e l'importo fatturato) si basano sul numero di transazioni inviate usando le informazioni di autenticazione. Ogni piano tariffario specifica:
* numero massimo di transazioni consentite al secondo (TPS).
* funzionalità del servizio abilitate all'interno del piano tariffario.
* Costo di una quantità predefinita di transazioni. Il superamento di questo importo comporterà un addebito aggiuntivo come specificato nei dettagli relativi ai [prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) per il servizio.

## <a name="clean-up-resources"></a>Pulire le risorse

Se si vuole pulire e rimuovere una sottoscrizione a Servizi cognitivi, è possibile eliminare la risorsa o il gruppo di risorse. Eliminando il gruppo di risorse, vengono eliminate anche tutte le altre risorse contenute nel gruppo.

Per rimuovere un gruppo di risorse usando il portale di Azure:

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e scegliere **Gruppi di risorse** per visualizzare l'elenco dei gruppi di risorse.
2. Individuare il gruppo di risorse da eliminare e fare clic con il pulsante destro del mouse sul pulsante altro (...) sul lato destro dell'elenco.
3. Scegliere **Elimina gruppo di risorse** e confermare.

## <a name="see-also"></a>Vedere anche

* [Autenticare le richieste a Servizi cognitivi di Azure](authentication.md)
* [Che cosa sono i servizi cognitivi di Azure?](Welcome.md)
* [Supporto del linguaggio naturale](language-support.md)
* [Supporto del contenitore Docker](cognitive-services-container-support.md)
