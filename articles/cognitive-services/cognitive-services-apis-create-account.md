---
title: Creare un account delle API Servizi cognitivi nel Portale di Azure | Microsoft Docs
description: Come creare un account delle API Servizi cognitivi Microsoft nel Portale di Azure.
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b6176bb2-3bb6-4ebf-84d1-3598ee6e01c6
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2017
ms.author: garye;gibattag
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: a19f56f0b87d992e445034989bd35a827a72f367
ms.lasthandoff: 03/03/2017


---

# <a name="create-a-cognitive-services-apis-account-in-the-azure-portal"></a>Creare un account delle API Servizi cognitivi nel Portale di Azure

Per usare le API Servizi cognitivi Microsoft, è innanzitutto necessario creare un account nel Portale di Azure.

1. Accedere al [portale di Azure](http://portal.azure.com).

2. Fare clic su **+ NUOVO**.

3. Selezionare **Intelligence e analisi**, quindi **Cognitive Services APIs (preview)** (API Servizi cognitivi (anteprima)).

    ![Selezionare API Servizi cognitivi](media/cognitive-services-apis-create-account/select-cognitive-services-apis.png)

4. Nella pagina **Crea** specificare le informazioni seguenti:

    -   **Nome account:** il nome dell'account. È consigliabile usare un nome descrittivo, ad esempio *AFaceAPIAccount*.

    -   **Sottoscrizione:** selezionare una delle sottoscrizioni di Azure disponibili in cui si dispone almeno delle autorizzazioni di collaboratore.

    -   **Tipo di API:** scegliere l'API Servizi cognitivi da usare. Per altre informazioni sulle varie API Servizi cognitivi a disposizione, consultare la pagina [Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/).

    ![Selezionare il tipo di API](media/cognitive-services-apis-create-account/list-of-apis.png)

    -   **Piano tariffario:** il costo dell'account di Servizi cognitivi dipende dall'uso effettivo e dalle opzioni che vengono selezionate. Per altre informazioni in merito ai prezzi per ogni API, fare riferimento alle [pagine sui prezzi](https://azure.microsoft.com/pricing/details/cognitive-services/).

    -   **Gruppo risorse:** un gruppo di risorse è una raccolta di risorse che condividono il ciclo di vita, le autorizzazioni e i criteri. Per altre informazioni sui gruppi di risorse, vedere [Gestire le risorse di Azure mediante il portale](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

    -   **Località del gruppo di risorse:** è obbligatorio solo se l'API selezionata è globale (non associata a una posizione). Se l'API è globale e non associata a una posizione, è necessario specificare una posizione per il gruppo di risorse in cui risiederanno i metadati associati all'account delle API Servizi cognitivi. La posizione non avrà alcun impatto sulla disponibilità di runtime dell'account. Per altre informazioni sui gruppi di risorse, vedere [Gestire le risorse di Azure mediante il portale](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).

    -   **Impostazione API:** per impostazione predefinita, la creazione di account è disabilitata fino a quando non viene abilitata esplicitamente dall'[amministratore dell'account Azure](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles).

        La modifica di questa impostazione verrà applicata solo alla posizione e al tipo di API correntemente selezionati e alla posizione del gruppo di risorse nel pannello a sinistra.

        ![Creare un account delle API Servizi cognitivi](media/cognitive-services-apis-create-account/create-account.png)

        > [!NOTE]
        > Se si riceve una notifica che segnala l'errore di impostazione dell'aggiornamento, significa che non è stato effettuato l'accesso come [amministratore dell'account](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator#types-of-azure-admin-accounts). L'amministratore dell'account deve seguire i passaggi precedenti per abilitare la creazione.
        >
        > ![Messaggio sull'esito negativo dell'impostazione dell'aggiornamento](./media/cognitive-services-apis-create-account/updatefailed.png)
        
        In alcuni casi l'amministratore dell'account potrebbe non avere accesso alla sottoscrizione. In questo caso, invitare l'amministratore del servizio a seguire i passaggi illustrati nel documento [Aggiungere o modificare i ruoli di amministratore di Azure che gestiscono la sottoscrizione o il servizio](https://docs.microsoft.com/azure/billing/billing-add-change-azure-subscription-administrator).
        
        Per trovare l'amministratore dell'account o l'amministratore del servizio per la sottoscrizione, selezionare la sottoscrizione nel [Portale di Azure](https://portal.azure.com), quindi selezionare __Proprietà__. Le informazioni __Amministratore account__ e __Amministratore dei servizi__ sono visualizzate nella parte inferiore del pannello delle proprietà.
        
        ![Proprietà della sottoscrizione](./media/cognitive-services-apis-create-account/subscription-properties.png)
    
    Microsoft può usare i dati inviati ai Servizi cognitivi per migliorare i prodotti e i servizi Microsoft. Per altre informazioni, consultare la [sezione dei Servizi cognitivi Microsoft](http://www.microsoft.com/Licensing/product-licensing/products.aspx) 
    nei termini di servizio online.

5. Per aggiungere l'account al dashboard del Portale di Azure, fare clic su **Aggiungi al dashboard**.

6. Fare clic su **Crea** per creare l'account.

Dopo che l'account di Servizi cognitivi è stato distribuito correttamente, fare clic sul riquadro nel dashboard per visualizzare le informazioni sull'account.

È possibile usare l'**URL dell'endpoint** nella sezione **Panoramica** e le chiavi nella sezione **Chiavi** per iniziare a effettuare chiamate alle API nelle applicazioni.

![Mostrare informazioni sull'account](media/cognitive-services-apis-create-account/display-account.png)

![Mostrare le chiavi dell'account](media/cognitive-services-apis-create-account/account-keys.png)

### <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni su tutti i Servizi cognitivi Microsoft a disposizione, vedere [Servizi cognitivi](https://azure.microsoft.com/services/cognitive-services/).

- Per guide introduttive all'uso di alcune API Servizi cognitivi di esempio, vedere:
    - [Introduzione alle API Text Analytics per rilevare sentimenti, frasi chiave, argomenti e lingua](cognitive-services-text-analytics-quick-start.md)
    - [Guida introduttiva per l'API Recommendations dei Servizi cognitivi](cognitive-services-recommendations-quick-start.md)
