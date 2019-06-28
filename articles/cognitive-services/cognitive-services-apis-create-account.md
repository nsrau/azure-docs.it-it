---
title: Creare un account di Servizi cognitivi nel portale di Azure
titlesuffix: Azure Cognitive Services
description: Come creare un account API servizi cognitivi di Azure nel portale di Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: aahi
ms.openlocfilehash: b857ee0395c447c8699b8f6a812853528812a7bd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445861"
---
# <a name="create-a-cognitive-services-account-using-the-azure-portal"></a>Creare un account servizi cognitivi nel portale di Azure

In questa Guida introduttiva verrà illustrato come iscriversi per servizi cognitivi di Azure e creare un account che disponga di una sottoscrizione di singoli servizi o multiservizio. Questi servizi sono rappresentati da [risorse](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) di Azure che consentono di connettersi a una o più API Servizi cognitivi di Azure.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure valida. [Creare un account](https://azure.microsoft.com/free/) gratuitamente.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Creare una nuova risorsa di servizi cognitivi di Azure

Prima di creare una risorsa, è necessario disporre di un gruppo di risorse di Azure. Ogni account di servizi cognitivi (e la relativa risorsa di Azure associata) devono appartenere a un gruppo di risorse di Azure. Quando si crea un account, hai la possibilità di creare un nuovo gruppo di risorse o usarne uno esistente. Questo articolo illustra come creare un nuovo gruppo di risorse.

1. Accedere al [portale di Azure](https://portal.azure.com) e fare clic su **+Crea una risorsa**.

    ![Selezionare API Servizi cognitivi](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. È possibile trovare servizi cognitivi disponibili con nei modi seguenti:
    * Usare la barra di ricerca e immettere il nome del servizio che si desidera eseguire la sottoscrizione.
        * Per creare una risorsa per una sottoscrizione multiservizio, immettere **servizi cognitivi** nella ricerca della barra e selezionare il **servizi cognitivi** risorsa.

        ![Cercare Servizi cognitivi](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * Per visualizzare tutti i servizi cognitivi disponibili, selezionare **intelligenza artificiale e Machine Learning**, in **Azure Marketplace**. Se non viene visualizzato il servizio si è interessati, fare clic su **Vedi tutto** e scorrere fino alla **servizi cognitivi**. Fare clic su **altre** per visualizzare l'intero catalogo di API servizi cognitivi.
    
        ![Selezionare API Servizi cognitivi](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Nella pagina **Crea** specificare le informazioni seguenti:

    > [!IMPORTANT]
    > Ricordare la località di Azure, come si potrebbe essere necessario quando si chiamano i servizi cognitivi di Azure.

    |    |    |
    |--|--|
    | **Nome** | Nome descrittivo per la risorsa di Servizi cognitivi. È consigliabile usare un nome descrittivo, ad esempio *MyCognitiveServicesAccount*. |
    | **Sottoscrizione** | Selezionare una delle sottoscrizioni di Azure disponibili. |
    | **Posizione** | Posizione dell'istanza di Servizi cognitivi. Posizioni diverse possono introdurre latenza, ma non hanno alcun impatto sulla disponibilità di runtime della risorsa. |
    | **Piano tariffario** | Il costo dell'account Servizi cognitivi dipende dalle opzioni scelte e dall'utilizzo. Per altre informazioni, vedere i [dettagli sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/) delle API.
    | **Gruppo di risorse** | [Gruppo di risorse Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) che conterrà la risorsa di Servizi cognitivi. È possibile creare un nuovo gruppo o aggiungerla a un gruppo già esistente. |

    ![Schermata di creazione risorsa](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-subscription"></a>Ottenere le chiavi per la sottoscrizione

Dopo aver creato la risorsa, è possibile accedervi dal dashboard di Azure se è stata aggiunta. In caso contrario, è possibile trovarla in **Gruppi di risorse**. Dopo aver selezionato la risorsa, è possibile ottenere le chiavi selezionando **tasti** sotto **gestione delle risorse**.

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Pulire le risorse

Se si desidera pulire e rimuovere una sottoscrizione a servizi cognitivi, è possibile eliminare la risorsa o gruppo di risorse. L'eliminazione del gruppo di risorse elimina anche eventuali altre risorse associate al gruppo di risorse.

Per rimuovere un gruppo di risorse usando il portale di Azure:

1. Nel portale di Azure espandere il menu a sinistra per aprire il menu dei servizi e scegliere **Gruppi di risorse** per visualizzare l'elenco dei gruppi di risorse.
2. Individuare il gruppo di risorse da eliminare e fare doppio clic sul pulsante più (...) a destra dell'elenco.
3. Scegliere **Elimina gruppo di risorse** e confermare.

## <a name="see-also"></a>Vedere anche

* [Autenticare le richieste a Servizi cognitivi di Azure](authentication.md)
* [Quali sono i servizi cognitivi di Azure?](Welcome.md)
* [Supporto del linguaggio naturale](language-support.md)
* [Supporto per i contenitori docker](cognitive-services-container-support.md)
