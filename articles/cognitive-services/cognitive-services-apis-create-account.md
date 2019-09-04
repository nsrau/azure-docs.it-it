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
ms.openlocfilehash: c7db2b4d49e3b9297c32d2e11ffe7c7702c17544
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274594"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Creare una risorsa di servizi cognitivi usando il portale di Azure

Usare questa Guida introduttiva per creare una risorsa di servizi cognitivi di Azure usando il portale di Azure. Dopo aver creato una risorsa Servizi cognitivi, si otterrà un endpoint e una chiave che è possibile usare per autenticare le applicazioni.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure valida- [crearne una gratuita](https://azure.microsoft.com/free/).

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Creare una nuova risorsa Servizi cognitivi di Azure

Prima di creare una risorsa Servizi cognitivi, è necessario disporre di un gruppo di risorse di Azure per contenere la risorsa. Quando si crea una nuova risorsa, è possibile scegliere di creare un nuovo gruppo di risorse o utilizzarne uno esistente. Questo articolo illustra come creare un nuovo gruppo di risorse.

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **+Crea una risorsa**.

    ![Selezionare API Servizi cognitivi](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Come spiegato in precedenza, è possibile creare una risorsa di servizi cognitivi in due modi, usando una risorsa multiservizio o una risorsa a servizio singolo.

    #### <a name="multi-service-resourcetabmultiservice"></a>[Risorsa multiservizio](#tab/multiservice)

    Per creare una risorsa multiservizio, immettere **Servizi cognitivi** nella barra di ricerca.

    ![Cercare Servizi cognitivi](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    Nella pagina Servizi cognitivi selezionare **Crea**.

    ![Crea account servizi cognitivi](media/cognitive-services-apis-create-account/azurecogservsearchmulti-2.png)

    #### <a name="single-service-resourcetabsingleservice"></a>[Risorsa a servizio singolo](#tab/singleservice)

    Per visualizzare tutti i servizi cognitivi disponibili, selezionare **ai + Machine Learning**, in **Azure Marketplace**. Se il servizio a cui si è interessati non è visibile, fare clic su **Visualizza tutto** e scorrere fino a **Servizi cognitivi**. Fare clic su **Visualizza altro** per visualizzare l'intero catalogo di servizi cognitivi.

    Quando si è interessati al servizio, fare clic su **Crea**.
    
    ![Selezionare API Servizi cognitivi](media/cognitive-services-apis-create-account/azureMarketplace.png)

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

Dopo che la risorsa è stata creata correttamente, viene visualizzata una notifica popup nella parte superiore destra della schermata. Dalla notifica, fare clic su **Vai alla risorsa** per visualizzare la risorsa di servizi cognitivi creata. 

![Vai alla risorsa servizio cognitivo](media/cognitive-services-apis-create-account/cog-serv-go-to-resource.png)

Dal riquadro avvio rapido visualizzato è possibile accedere all'endpoint e alla chiave.

![Ottieni chiave ed endpoint](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Piani tariffari e fatturazione

I piani tariffari (e l'importo fatturato) si basano sul numero di transazioni inviate usando le informazioni di autenticazione. Ogni piano tariffario specifica:
* numero massimo di transazioni consentite al secondo (TPS).
* funzionalità del servizio abilitate all'interno del piano tariffario.
* Costo di un numero predefinito di transazioni. Il superamento di questo importo comporterà un addebito aggiuntivo come specificato nei dettagli relativi ai [prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) per il servizio.

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
